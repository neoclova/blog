---
title: "MySQL crash-safe 분석"
date: 2025-04-10 11:14:00 +0900
categories: [3. DBMS, MySQL]
tags: [MySQL, MariaDB, Percona, crash-safe]
image:
  path: /assets/img/posts_2025/mysql_crash-safe.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAAEABQDASIAAhEBAxEB/8QAGwAAAgMBAQEAAAAAAAAAAAAABgcDBAUBAgj/xAAyEAACAQIEAwYDBwQDAAAAAAAAAQIDBBEFEiExBhMiQVFhcYEUIzKRoSNSsdHwJCOh0f/EABgBAQEBAQEAAAAAAAAAAAAAAAABAgME/8QAHBEBAQEBAQEBAQEAAAAAAAAAAAECESExEkFR/9oADAMBAAIRAxEAPwD6xAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABxyMumK7q9N8+3vOEVVTz7Of4sYZ+jl7vCjXH8uFb8lfyLeK2fRJklolFtRpRdsZOdUVfI0bYt5KSlJ2ctGdtZWc1Q2LTr6UmpXkyrGUqQlGbnUavhzUqLKV5zcjTWvl6zH2upUoUo1alWlZSvqxqzjKU5yr9PViZmZ6lHUUqUoUuVpylGRfCrqUoU5SnGcoy0opUpSlJRSlKUpSlKUoSlKUpSlKUpSlKUpSlKUpSlKUpSlKUpSlKUpSlKUpSlKUpSlKU/9k=
  alt: MySQL crash-safe 분석
---

가장 인기있는 오픈소스 관계형 데이터베이스 MySQL 은 데이터가 손실되지 않게 하기위해 매우 중요하고도 기본적인 기능을 갖추고 있습니다. 그렇다면 MySQL 은 충돌(crash)이 발생하더라도 데이터가 손실되지 않게 하기위해 어떻게 설계되었을까요? 이 기능을 지원하는 핵심 기술은 무엇일까요? 이 글에서 내용을 하나씩 정리했습니다.

## 소개

MySQL 은 데이터가 손실되지 않도록 하기위해 크게 다음 두가지 기능으로 구현됩니다.

- 원하는 시점으로든 복구할수 있는 기능
- MySQL 이 갑자기 다운되더라도 재시작후 이전에 커밋한 레코드가 손실되지 않도록 보장하는 기능

첫번째, binlog 을 충분히 보관하고 있다면 binlog 를 다시 실행해서 MySQL 을 원하는 시점으로 복구하는 방법은 많은사람이 알고있습니다.

두번째, 이글의 제목에서 언급한 충돌방지(crash-safe) 기능입니다. InnoDB 스토리지 엔진에서는 진행중인 트랜잭션 commit 프로세스 어느단계에서 충돌하더라도 MySQL을 재시작(재기동)하면 트랜잭션 무결성을 보장합니다. 이미 commit 된 데이터는 손실되지 않으며 commit 되지 않은 데이터는 자동으로 rollback 됩니다. 이 기능은 redo log 와 undo log 에 의존합니다. 

​![MySQL DML 처리과정](/assets/img/posts_2025/mysql_crash-safe_1.jpg)

crash-safe 는 주로 트랜잭션 실행 프로세스가 갑자기 충돌했을때 MySQL 을 재시작하면 트랜잭션의 무결성을 보장하기 위해 반영됩니다. 이러한 crash-safe 의 구체적인 원리를 설명하기 전에 MySQL 트랜잭션 실행의 주요단계 이해해야지만 이를 기반으로 설명할수 있습니다. update 문의 실행과정을 예로 들어보겠습니다.

위 그림을 보면 MySQL 에서 update 문이 어떻게 실행되는지 알수있습니다.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;① 메모리에서 데이터 레코드를 찾아 update 합니다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;② redo log 의 데이터 페이지에 변경사항을 기록합니다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;③ 논리적 작업은 binlog 에 기록합니다.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;④ 메모리에 있는 데이터와 로그는 일정조건을 만족하면 background thread 가 비동기적으로 flush 합니다.

위 그림의 update 실행 과정으로 질문과 답변을 통해 crash-safe 의 원리를 분석해 보겠습니다.

## WAL 메커니즘

> <b>Question</b>❓<br> 
> MySQL 은 디스크에 데이터를 직접 변경하지 않고 먼저 메모리에 데이터를 변경한다음 로그를 작성하고 background thread 에 의해 비동기로 디스크에 flush 하는 이유는 무엇입니까? 너무 복잡하지 않습니까?
{: .prompt-warning }
​
MySQL 에서 데이터를 변경할때 디스크 파일에 직접 데이터를 쓰지 않는 가장 큰 이유는 성능상의 문제 때문이다. 디스크 파일을 직접 쓰는 것은 random access 를 발생하기 때문에 높은 overhead 가 필요하며 MySQL 의 요구사항을 충족할 수 없습니다. 이것이 메모리에 데이터를 먼저 변경하고 background thread 가 비동기 방식으로 디스크에 flush 하도록 설계된 이유입니다. 하지만 메모리는 불안정하기 때문에 만약 정전이 발생한다면 아직 디스크와 동기화되지 않았던 메모리의 데이터가 손실되므로 로그를 작성하는 단계를 추가해야 합니다. 이렇게하면 MySQL 을 재시작 하더라도 로그 기록을 통해 복구가 가능합니다.

로그를 작성하는것도 디스크 쓰기 작업이지만 sequential 쓰기이기 때문에 random 쓰기보다 overhead 가 적고 명령문 실행성능을 향상시킬 수 있습니다. sequential 쓰기가 random 쓰기보다 빠른 이유는 한 페이지에 쓰는 것이 해당 페이지를 일일이 찾아서 쓰는 것보다 훨씬 빠르기 때문입니다. 이 기술은 대부분의 스토리지 시스템에 기본적으로 사용되는 WAL(Write Ahead Log) 기술로, 로그 선행 기술이라고도 하며 데이터 파일을 수정하기 전에 먼저 로그를 기록해야 합니다. 데이터 일관성과 지속성을 보장하고 성능을 향상시킵니다.

## 핵심 로그 모듈

> <b>Question</b>❓<br> 
> update SQL 문 실행 과정에서 총 3개의 로그를 작성해야 하는데, 3개의 로그가 모두 필요합니까? 단순화 할수있습니까?
{: .prompt-warning }

SQL update 실행 프로세스에는 MySQL 로그모듈의 세가지 핵심로그(redo log, undo log, binlog)가 포함됩니다. crash-safe 기능은 주로 이 세 가지 로그에 의존합니다. 다음은 각 로그의 기능을 별도로 소개하고 단순화할 수 있는지 확인해 보겠습니다.

### redo log
트랜잭션 로그라고도 하는 redo log 는 InnoDB 스토리지 엔진 계층에 의해 생성됩니다. 기록되는 내용은 특정 레코드가 수정된 방식이 아닌 데이터베이스 각 페이지가 수정된 내용으로 commit 후 실제 데이터 페이지를 복원하는데 사용할수 있습니다. 앞서 언급한 WAL 기술인 redo log 는 WAL 의 대표적인 응용프로그램으로 데이터를 변경하기 위해 트랜잭션이 commit 되면 MySQL 은 메모리의 해당 데이터 페이지만 수정하고 redo log 에 기록합니다. 완료되면 트랜잭션 commit 이 성공합니다. 디스크 데이터 파일의 update 는 background thread 에 의해 비동기적으로 처리됩니다.

redo log 로 인해 MySQL 데이터의 일관성과 내구성이 보장됩니다. 데이터가 flush 되기 전에 MySQL 이 충돌하더라도 재시작 이후 redo log 로 변경기록을 복구하고 다시 flush 할수 있습니다. redo log 를 작성하는 것은 sequential 쓰기입니다. 데이터 파일을 업데이트하기 위해 무작위로 쓰는 것에 비해 로그 쓰기 overhead 가 작아서 명령문 실행성능을 크게 향상시키고 동시성을 높일수 있습니다. redo log 는 크기가 고정되어 있으므로 처음부터 시작하여 끝에 도달하면 다시 처음으로 돌아가는 루프 방식으로만 작성할수 있습니다.redo log 가 가득차면 오래된 레코드를 지워야 하지만 지우기 전에 해당하는 레코드의 메모리 데이터 페이지가 디스크에 flush 되었는지 확인해야 합니다. redo log 가 가득차서 새로운 공간을 만들기 위해 오래된 레코드가 지워지는 동안에는 새로운 업데이트 요청을 더이상 수신할수 없으며 이로 인해 MySQL 이 지연될수 있습니다. 그래서 동시성이 큰 시스템에서는 redo log 크기를 적절하게 설정하는 것이 매우 중요합니다.

### undo log
undo log 는 주로 rollback 기능을 제공하지만 트랜잭션의 원자성을 보장하기 위해 다중행 버전제어(MVCC)라는 또 다른 주요기능도 가지고 있습니다. 데이터 수정 과정에서 현재 작업과 반대되는 논리적 로그가 undo log 에 기록됩니다. 어떤한 이유로 트랜잭션이 비정상적으로 실패하는 경우 undo log 를 사용해서 트랜잭션 무결성을 보장하기 위해 rollback 할수 있습니다. undo log 도 필수입니다

### binlog
binlog 는 MySQL 의 서버계층에서 생성되며 어떤엔진에도 속하지 않으며 주로 사용자가 데이터베이스를 운영하기 위해 사용하는 SQL 문을 기록합니다. binlog 를 archive log 라고 부르는 이유는 binlog 가 redo log 처럼 오래된 기록을 지우지 않고 주기적으로 계속해서 기록하기 때문이다. (만료될 때까지 지워지지 않음) 단일 로그(defautl 1G, max_binlog_size 시스템변수로 설정) 최대값을 초과하는 경우에는 기록을 계속하기 위해 새 파일을 생성합니다. 그러나 binlog 는 트랜잭션(ex:InnoDB 테이블 유형)을 기반으로 작성할수 있으며 트랜잭션이 파일전체에 기록되는 것은 절대 불가능하며 기록되어서는 안됩니다. 그래서 binlog 파일이 최대값에 도달했지만 트랜잭션이 commit 되지 않았다면 새로운 파일로 전환하여 기록하는 것이 아니라 로그사이즈를 계속 늘려서 작성합니다. 이러한 이유로 max_binlog_size 에 지정된 값과 실제 binlog 로그 크기가 반드시 같을 수는 없습니다. binlog 는 보관기능을 가지고 있기 때문에 Source-Replica 동기화 및 데이터베이스의 특정 시점복원에 사용됩니다.

그럼 이전 질문으로 돌아가서 binlog 를 단순화할수 있습니까? 여기서 우리는 시나리오를 살펴봐야 합니다.

- Source-Replica 모드인 경우 Replica 의 데이터 동기화가 binlog 에 의존하므로 binlog 가 필요합니다.<br>
- Stand-alone 모드이고 특정시점에 따라 데이터베이스를 복원하지 않는 경우 redo log 를 통해 crash-safe 기능을 보장할 수 있습니다. 그러나 특정시점의 상태로 rollback 해야 된다면 binlog 를 켜두는 것이 좋습니다.

위의 세가지 로그에 대한 자세한 설명을 바탕으로 질문에 답할수 있습니다. Source-Replica 모드에서는 세가지 로그가 모두 필요합니다. 그리고 독립된 Stand-alone 모드에서는 상황에 따라 binlog 유무를 결정할수 있지만 데이터 안전을 생각한다며 켜두는 것이 좋습니다.

## Two phase commit

> <b>Question</b>❓<br> 
> redo log 를 두단계로 작성하고 그 사이에 binlog 를 작성해야 하는 이유는 무엇입니까?
{: .prompt-warning }

위에서 알 수 있듯이 redo log 는 Source 데이터에 영향을 주고 binlog 는 Replica 데이터에 영향을 주기 때문에 Source-Replica 데이터 일관성을 보장하기 위해서는 redo log 와 binlog 가 일치해야 합니다. redo log 와 binlog 는 전형적인 분산 트랜잭션 시나리오입니다. 왜냐하면 redo log 와 binlog 는 독립적인 별개의 개체이기 때문에 일관성을 유지하려면 분산 트랜잭션 솔루션을 사용하여 처리해야 합니다. redo log 는 두단계로 나누어지며 Two-phase Commit 프로토콜(Two-phase Commit, 2PC)을 사용합니다.

update 문의 실행 프로세스를 단순화하고 MySQL 의 2단계 commit 이 어떻게 구현되는지 살펴보겠습니다.

​![Two-phase Commit](/assets/img/posts_2025/mysql_crash-safe_2.jpg)

그림에서 볼 수 있듯이 트랜잭션 commit 프로세스는 두단계로 구성됩니다. redo log 작성을 prepare 와 commit 의 두단계로 나눠서 중간에 binlog 를 작성하는 것입니다. 왜 Two phase commit 사용해야 합니까? Two phase commit 을 사용하지 않으면 어떻게 될까요? redo log 를 먼저 쓰고 binlog 를 쓰거나 binlog 를 먼저 쓰고 redo log 를 쓰면 안 됩니까?

id=2 인 레코드의 c 변수 초기값이 0 이라고 가정하고 id=2 를 c=c+1 로 설정해서 T 테이블을 update 진행하겠습니다. 그런 다음 redo log 에는 기록되었지만 binlog 에는 아직 작성되지 않은 상태에서 MySQL 프로세스가 비정상적으로 재시작 됩니다. redo log 에는 기록되어 있기 때문에 재시작후 redo log 를 통해 복구한 다음에는 c 값이 1 입니다. 그러나 binlog 에는 작성하기 전에 충돌이 발생했기 때문에 binlog 에는 기록되지 않았습니다. 따라서 binlog 의 c 값은 여전히 0 이므로 redo log 값과 다릅니다. 이러한 방식으로 binlog 를 통해 replica 에 복제가 된다면 데이터 일관성이 깨집니다.

마찬가지로 먼저 binlog 를 작성하고 redo log 를 작성하고 중간에 crash 가 발생해도 데이터가 일치하지 않습니다. 따라서 redo log 를 Two phase commit 으로 나누면 redo log 와 binlog 내용을 일치시켜서 Source-Replica 데이터 일관성을 보장할수 있습니다. Two phase commit 은 단일 트랜잭션에서 redo log 와 binlog 의 일관성을 보장할 수 있지만, 여러 트랜잭션의 경우 두로그의 commit 순서 일치를 보장할수 없습니다.

동시에 세 개의 트랜잭션이 커밋되었습니다.
```
T1 (--prepare--binlog---------------------commit)
T2 (-----prepare-----binlog----commit)
T3 (--------prepare-------binlog------commit)

redo log prepare：T1 --》 T2 --》 T3
binlog：T1 --》 T2 --》 T3
redo log commit：T2 --》 T3 --》 T1
```

여러 트랜잭션의 경우 Two phase commit 프로세스를 기반으로 commit 의 원자성을 보장하기 위해 잠금을 추가하여 redo log 와 binlog 의 commit 순서가 일관되도록 해야합니다. 따라서 초기 MySQL 버전에서는 트랜잭션 제출순서를 보장하기 위해 prepare_commit_mutex 잠금을 사용했습니다. 트랜잭션이 잠금을 획득한 경우에만 prepare 가 가능했으며 commit 이 끝날때까지 잠금을 해제할 수 없었습니다. 잠금은 순차일관성 문제를 완벽하게 해결하지만 동시성이 크면 잠금경합이 발생하고 성능이 저하됩니다. 잠금경합 이외에 성능에 더큰 영향을 미치는 또다른 점은 각 트랜잭션에 대해 두 번의 fsync(디스크 쓰기)가 수행 된다는점입니다. 우리 모두 알고있듯이 디스크에 쓰는 작업은 비용이 많이 드는 작업입니다. 일반 하드 디스크의 경우 초당 QPS 는 몇백에 불과합니다.

## Group Commit

> <b>Question</b>❓<br> 
> Two phase commit 에서 트랜잭션 commit 순서를 제어하기위해 잠금을 구현함으로서 발생하는 성능 병목현상 문제에 대해 더나은 해결방법이 있습니까?
{: .prompt-warning }

대답은 물론 그렇습니다. MySQL 5.6 에서는 binlog group commit 이 도입되었습니다. binlog group commit 아이디어는 InnoDB commit 순서가 binlog 디스크 배치 순서와 일치하도록 대기열(queue) 메커니즘을 도입했습니다. group commit 의 목적을 달성하기 위해 binlog flush 작업을 하나의 트랜잭션으로 그룹화하는 것입니다. 자세한 내용은 다음과 같습니다.

​![Group Commit](/assets/img/posts_2025/mysql_crash-safe_3.jpg)

- 첫번째 단계 (prepare)<br>
prepare_commit_mutex 잠금을 획득해서 prepare 상태로 설정하고 디스크에 write/fsync redo log 작업을 완료후에 prepare_commit_mutex 잠금을 해제하면 binlog 는 어떤한 작업도 수행하지 않습니다.

- 두번째 단계 (commit)<br>
세단계로 나뉘며 각단계마다 전용 스레드에 할당되어 처리됩니다.
1. Flush Stage（binlog cache 쓰기）<br>
① Lock_log mutex 획득<br>
② 대기열(Queue)에서 binlog 그룹을 가져옵니다. (대기열의 모든 트랜잭션)<br>
③ binlog 캐시 쓰기
2. Sync Stage（binlog 를 디스크에 쓰기）<br>
① Lock_log mutex 잠금을 해제하고 Lock_sync mutex 잠금을 획득합니다. Sync Stage 대기열에 리더가 비어있지 않은 상태에서 Flush Stage 스레드가 넘어올 경우 Flush Stage 의 leader 는 follower 가 되어 대기할 수도 있지만, follower 는 결코 leader 가 될수 없습니다. 이는 모든단계에서 적용됩니다.<br>
② binlog 그룹을 디스크에 flush 합니다. (fsync 작업은 sync_binlog=1 설정일때 가장 오래 걸립니다.)
3. Commit Stage（InnoDB 커밋, undo log 데이터 지우기<br>
① Lock_sync mutex 잠금을 해제하고 Lock_commit mutex 잠금을 획득합니다.<br>
② 대기열(Queue)에 있는 트랜잭션을 순회하며 InnoDB commit 을 진행합니다.<br>
③ Lock_commit mutex 잠금을 해제합니다.

각 단계별로 고유한 대기열이 있습니다. 대기열의 첫번째 트랜잭션을 leader 라고 호칭하고 다른 트랜잭션을 follower 라고 합니다. leader 는 follower 동작을 제어합니다. 단계별 대기열에는 고유한 mutex(잠금)로 보호되며 대기열의 트랜잭션은 순차적으로 처리됩니다. Flush Stage 가 완료 되어야 Sync Stage 대기열에 들어갈 수 있고, Sync Stage 가 완료되여야 Commit Stage 대기열에 들어갈수 있습니다. 이 세단계의 작업은 동시에 실행될수 있습니다. 하나의 트랜잭션 그룹이 Commit Stage 에 있는 상황에서 또다른 새로운 트랜잭션 그룹을 Flush Stage 에서 수행할수 있습니다. 세단계의 작업을 동시에 실행함으로서 진정한 의미의 Group Commit 을 실현하고 디스크 IOPS 사용을 크게 줄일 수 있습니다.

Group Commit 이 Two phase commit 보다 잠금성능이 더 나은 이유에 대해 간단히 요약해 보겠습니다. Group Commit 은 여전히 ​​단계별 대기열에서 prepare_commit_mutex 잠금을 획득하지만 잠금의 세분성이 원래 Two phase commit 보다 1/4로 작아져 잠금 경합이 크게 줄어듭니다. 또한 Group Commit 은 일괄 flush 이므로 이전 단일 레코드 flush 에 비해 디스크 IO 소비를 크게 줄일 수 있습니다.

## 데이터 복구 프로세스

> <b>Question</b>❓<br> 
> 트랜잭션 commit 중에 MySQL 프로세스가 갑자기 충돌했다고 가정했을때, 재시작 이후 데이터가 손실되지 않도록 하려면 어떻게 해야 합니까?
{: .prompt-warning }

다음 그림은 재시작 이후 서비스를 제공하기 전에 MySQL 이 가장 먼저 수행하는 작업, 즉 데이터 복원 프로세스를 보여줍니다.

​![데이터 복원 프로세스](/assets/img/posts_2025/mysql_crash-safe_4.jpg)

위 그림에 대한 간략한 설명은 다음과 같습니다. crash 이후 재시작하면 redo log 에서 prepare 상태의 트랜잭션이 있는지 확인하고 xid(트랜잭션 id)로 binlog 에서 해당 트랜잭션을 찾습니다. 트랜잭션을 찾을수 없다면 rollback 하고 찾게되면 트랜잭션을 완료하고 redo log 를 다시 commit 하여 트랜잭션 commit 을 완료합니다.​

다음은 트랜잭션 commit 프로세스에 따라 갑작스러운 충돌이 발생한했을때 위그림의 프로세스에 따라 각 단계별로 데이터를 어떻게 복구하는지 살펴보겠습니다.

- Case-1 (메모리에서 데이터 페이지를 변경한 직후 redo log 쓰기를 시작하기 전에 충돌발생)<br>
메모리의 더티 페이지가 flush 되지 않았고 redo log 와 binlog 가 기록되지 않았기 때문에(트랜잭션이 아직 commit 을 시작하지 않았기 때문에) crash-safe 는 트랜잭션과 아무 관련이 없습니다.

- Case-2 (redo log 쓰기중이거나 redo log 쓰기를 완료한 경우, prepare 상태로 아직 binlog 를 작성하지 않은 상태)<br>
복구후 redo log 는 트랜잭션 완료 여부를 판단합니다. 완료되지 않은 경우 undo log 로 rollback 합니다. 완료되어 prepare 상태이면 해당 트랜잭션 binlog 완료여부를 추가로 판단해서 완료되지 않았다면 undo log 로 rollback 합니다.

- Case-3 (binlog 를 쓰기중이거나 binlog 가 쓰기가 완료되었지만 redo log 를 commit 하기전에 충돌발생)<br>
Case-2 와 동일하게 복구후 먼저 redo log 가 완료되어 prepare 상태인지 확인합니다. 해당 트랜잭션 binlog 가 완료되었는지 확인하고 완료되지 않은 경우에는 undo log 로 rollback 완료되었다면 redo log 를 다시 commit 합니다.

- Case-4 (redo log 를 commit 할때 또는 트랜잭션이 commit 을 완료했지만 아직 클라이언트에 성공적으로 반환되지 않은경우 충돌발생)<br>
복구후에는 기본적으로 Case-4 와 동일합니다. redo log 와 binlog 의 트랜잭션 무결성을 비교해서 rollback 또는 commit 을 확인합니다.

## 요약

MySQL crash-safe 원칙에 대한 자세한 설명은 여기까지이고 요약하면 다음과 같습니다. 

먼저 WAL 로그 기술에 대한 정의, 프로세스, 역할에 대해 간략하게 소개합니다. WAL 은 데이터베이스 시스템에서 일관성과 내구성을 달성하기 위해 사용되는 일반적인 디자인 패턴입니다. 그런 다음 MySQL 로깅모듈인 redo log, undo log, binlog, Two phase commit 및 Group Commit 을 자세히 설명합니다. 마지막으로 데이터 복구 프로세스를 다양한 상황을 예시로 들어 설명하고 검증합니다.