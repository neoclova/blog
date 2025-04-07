---
title: "Percona Server for MySQL 8.0.39-30"
date: 2025-04-06 20:00:00 +0900
categories: [2. DBMS Release Note, Percona Server for MySQL 8.0]
tags: [DBMS, MySQL, Percona, Release Notes]
image:
  path: /assets/img/posts_2025/Percona_Server_for_MySQL.jpg
  lqip: data:image/webp;base64,UklGRhgBAABXRUJQVlA4IAwBAADwBQCdASogACAAPm0qkUYkIiGhMBgMAIANiUAWI3JzUBM1EetKtOpv62bapqVOiP9xek71uFiocAD++g+Bbg7nDaAbZO/dwsyF8NgAs+2QcaWV9mx11MqNK6eF8kwAfxdffojwIH+LfFvi28emn3JH+nye9f1M39h+Oe+qSCH1rWZsL3QgMQVXpLiw+EIQSy8ENzwkdqoWthmM5rSrXeMgSnbgi797H0Dw96NCPHQT4POhbVOrWr05dcFYG
  alt: Percona Server for MySQL 8.0.39-30
---

> <a href="https://docs.percona.com/percona-server/8.0/release-notes/8.0.39-30.html" target="_blank">Percona Server for MySQL 8.0.39-30</a> (2024-10-08)
{: .prompt-info }

## Release highlights
Improvements and bug fixes provided by Oracle for MySQL 8.0.38, MySQL 8.0.39 and included in Percona Server for MySQL are the following:

- The server could not restart successfully after creating a large number of tables (8001 or more). This issue, Bug #36808732, is a regression of Bug #33398681.
> `8001개 이상의 테이블을 생성한 후 서버가 정상적으로 재시작되지 않는 문제가 발생했습니다. 이 문제(Bug #36808732)는 이전 버그(Bug #33398681)의 회귀(regression)입니다.`

- Enhanced performance of tablespace file scanning during startup. (Bug #110402, Bug #35200385)
> `시작 시 테이블스페이스 파일 스캔 성능이 향상되었습니다. (Bug #110402, Bug #35200385)`

- InnoDB file system operations now consistently perform an fsync on the parent directory when carrying out directory-altering tasks. (Bug #36174938)
> `InnoDB 의 파일 시스템 작업은 이제 directory 변경 작업을 수행할 때 상위 directory 에 일관되게 fsync 를 수행합니다. (Bug #36174938)`

- Worker jobs now include details about the relay log file that initiated the transaction, rather than relying on the default specified by relay_log.
> `Worker 작업은 이제 트랜잭션을 시작한 relay log 파일에 대한 정보를 포함하며, 더 이상 relay_log 에 지정된 기본값에 의존하지 않습니다.`

Find the complete list of bug fixes and changes in the MySQL 8.0.38 Release Notes and MySQL 8.0.39 Release Notes.

## Improvements

- <a href="https://perconadev.atlassian.net/browse/PS-9233" target="_blank">PS-9233</a> : Adds the UUID_VX component which provides a set of functions for generating and working with various versions of the Universally Unique Identifier (UUID).
> `UUID_VX 구성 요소를 추가했습니다. 이는 다양한 버전의 Universally Unique Identifier (UUID)를 생성하고 처리하기 위한 함수 세트를 제공합니다.`

## Bug fixes

- <a href="https://perconadev.atlassian.net/browse/PS-8057" target="_blank">PS-8057</a> : The variable slow_query_log_file at runtime did not match the value defined in the my.cnf file.
> `런타임에서 slow_query_log_file 변수 값이 my.cnf 파일에 정의된 값과 일치하지 않았습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9214" target="_blank">PS-9214</a> : An ALTER TABLE operation that rebuilt an InnoDB table using the INPLACE algorithm could occasionally fail with an unwarranted duplicate primary key error. This could happen if there were concurrent insertions, even though theose insertions did not cause any primary key conflict.
> `INPLACE 알고리즘을 사용하여 InnoDB 테이블을 재구성하는 ALTER TABLE 작업이 간혹 불필요한 PK 중복 오류로 실패할 수 있었습니다. 이러한 오류는 실제로 PK 충돌이 발생하지 않았음에도 불구하고 동시 삽입이 있는 경우에 발생할 수 있었습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9314" target="_blank">PS-9314</a> : There was no connection to the server because mysqld received signal 11 when using JSON_TABLE.
> `mysqld 가 JSON_TABLE 을 사용할 때 시그널 11가 발생하여 서버와 Connection 이 실패하였습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9286" target="_blank">PS-9286</a> : The Key Management Interoperability Protocol (KMIP) component left keys in a pre-active state.
> `Key Management Interoperability Protocol (KMIP) 구성 요소는 키를 pre-active 상태로 남겼습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9306" target="_blank">PS-9306</a> : MySQL versions 8.0.38, 8.4.1, and 9.0.0 exited upon restarting if the database contained 10,000 or more tables.
> `데이터베이스에 10,000개 이상의 테이블이 포함되어 있을 때 MySQL 버전 8.0.38, 8.4.1, 및 9.0.0 은 재시작 시 종료되었습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9322" target="_blank">PS-9322</a> : When super_read_only=1 is enabled, undo truncation couldn’t update the data dictionary (DD), resulting in orphaned truncate log files.
> `super_read_only=1 이 활성화되었을 때 undo truncation 은 데이터 사전(DD)을 업데이트할 수 없게 되어 truncate 로그 파일이 일부분만 남아있었습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9379" target="_blank">PS-9379</a> : On some platforms, using the -l atomic flag could cause a compilation error because the compiler already has built-in support for atomic operations and does not require linking to the atomic library.
> `일부 플랫폼에서는 컴파일러에 이미 원자 연산에 대한 지원이 내장되어 있고 atomic 라이브러리에 연결할 필요가 없기 때문에 -l 원자 플래그를 사용하면 컴파일 오류가 발생할 수 있습니다.`


- <a href="https://perconadev.atlassian.net/browse/PS-9144" target="_blank">PS-9144</a> : In-place ALTER TABLE operations that internally rebuilt tables sometimes resulted in lost rows if a concurrent purge happened.
> `내부적으로 테이블을 재구성하는 In-place ALTER TABLE 작업이 수행되는 동안에 동시에 purge 작업이 발생하면 rows 가 손실되는 경우가 있었습니다.`