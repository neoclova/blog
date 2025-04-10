---
title: "[Release Note] Percona Server for MySQL 8.4.4-4"
date: 2025-04-08 19:37:00 +0900
categories: [2. DBMS Release Note, Percona Server for MySQL 8.4]
tags: [DBMS, MySQL, Percona, Release Notes]
image:
  path: /assets/img/posts_2025/Percona_Server_for_MySQL.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAAQABADASIAAhEBAxEB/8QAGgAAAgMBAQAAAAAAAAAAAAAABAUCAwYBB//EADsQAAIBAgQDBgMFBwUBAAAAAAABAgMRBAUSITEGQVFhEyJxgZEjMoGRobHR8EJicuHwFSMzQ2LC8WKi/8QAGQEAAwEBAQAAAAAAAAAAAAAAAAECAwQF/8QAJhEAAgIBBAICAgMAAAAAAAAAAAECEQMSITEEE0FRYQUTIjNhsf/aAAwDAQACEAMQAAAB8rNZa8T4PUK0KuocUt7ctZosYkxhIUExixpyNnNuSPArxOdEk1Ak+F4sh1F3rlYrXvFJKR0NfMVltMTIyIlaAaKVgAc8LQx22NPIgZT4pI7uWYZAGJWDkW2vEVTo6Ejr6qW8X1vK4zrREdm6WiOjIAqvP3qsyeFWTNdW8+M34ffco50aBprV42JWAdUly5EMuvNUK2N8lKqIlA6R5nh1lFxEViNDKFkxyEEiYAKZPYHg2Er4h2MJFRHiQGp9oNBSR3F0myuwOYvN2F4vdoOc+hglSiQoY+9Pdi7HcKl5ztF8JJd+ykeZHP3YkSak8A20swq1XZfI15chYoCKuI7AUQWGeD6kV+DaI9LLr9mnxcffJKu5SGHozm2uKoQSB4Khf8AeTPhMNnq6WjlgZqksUbSTkfTo5pUsBFg0G2QHq9YqjvFo2mssE4atGvTD7R7WwVkKtEZMQzLIwRkQPEe+Db4UpPheOPTL8Y6XsoOJTDtyzD40FtD0BSYyBT3YkfOBu9OJNSvmEFAH2yH9UPQU5j2XGmdHwTDNXdMSIPqW1DKQqQN2Pi9+l9AoKQAH/2Q==
  alt: Release Note Percona Server for MySQL 8.4.4-4
---

> <a href="https://docs.percona.com/percona-server/8.4/release-notes/8.4.4-4.html" target="_blank">Percona Server for MySQL 8.4.4-4</a> (2025-03-18)
{: .prompt-info }

## Release highlights
### Percona Server for MySQL 8.4.4-4

- Improves the <a href="https://docs.percona.com/percona-server/8.4/data-masking-overview.html" target="_blank">Data masking</a> performance by introducing an internal term cache. The new cache speeds up lookups for gen_blocklist() and gen_dictionary() functions by storing dictionary data in memory. However, if the dictionary table is modified directly (outside of the proper functions), the cache may become out of sync. To fix this, use the new masking_dictionaries_flush() function.<br>
Changes also affect row-based replication: dictionary changes on the source server are replicated, but the term cache on the replica doesn’t update immediately. To address this, a new system variable, component_masking_functions.dictionaries_flush_interval_seconds, can be set to automatically refresh the cache at specified intervals, helping replicas stay in sync.<br>
Find more detailed information in the <a href="https://docs.percona.com/percona-server/8.4/data-masking-overview.html" target="_blank">Data masking overview</a> and in the <a href="https://docs.percona.com/percona-server/8.4/data-masking-function-list.html" target="_blank">Data masking component functions</a>.
> `내부 용어 캐시를 도입하여 data masking 성능을 향상시켰습니다. 이 캐시는 gen_blocklist() 및 gen_dictionary() 함수의 조회 속도를 높이기 위해 dictionary 데이터를 메모리에 저장합니다. 하지만 dictionary 테이블을 함수 외부에서 직접 수정하면 캐시가 동기화되지 않을 수 있습니다. 이를 해결하려면 새로운 masking_dictionaries_flush() 함수를 사용하십시오. 이 변경은 row-based replication 에도 영향을 미칩니다. Source 에서 dictionary 변경은 복제되지만, Replica 의 캐시는 즉시 업데이트되지 않습니다. 이를 해결하기 위해 새로운 component_masking_functions.dictionaries_flush_interval_seconds 시스템 변수를 설정하면 지정된 간격으로 캐시를 자동으로 갱신하여 Replica 가 동기화를 유지할 수 있습니다. 자세한 내용은 data masking 개요 및 data masking component 함수 문서를 참조하십시오.`

- Improves the behavior of audit_log_filter_set_user to support wildcards in the hostname.
> `audit_log_filter_set_user 의 동작이 개선되어 hostname 에 wildcards 를 지원합니다.`

### MySQL 8.4.4
Improvements and bug fixes introduced by Oracle for MySQL 8.4.4 and included in Percona Server for MySQL are the following:

- Fixed an assertion in debug builds where certain IO buffer serializations caused system hangs. (Bug #37139618)
> `debug builds 에서 특정 IO buffer serializations 로 인해 시스템이 멈추는 assert 오류를 수정했습니다. (Bug #37139618)`

- Resolved a failure when dropping the primary key and adding a new AUTO_INCREMENT column as the primary key in descending order using the INPLACE algorithm resulted in failure. (Bug #36658450)
> `INPLACE 알고리즘을 사용하여 PK 를 삭제한 후 내림차순으로 새로운 AUTO_INCREMENT 컬럼을 PK 로 추가할 때 실패하던 문제를 해결했습니다. (Bug #36658450)`

- Fixed incorrect results, including missing rows, in queries that used a descending primary key with the index_merge optimization. (Bug #106207, Bug #33767814)
> `내림차순 PK 와 index_merge 최적화를 사용하는 쿼리에서 누락된 row 등 잘못된 결과가 발생하던 문제를 수정했습니다. (Bug #106207, Bug #33767814)`

- Addressed a replication channel issue where MySQL failed to stop the channel properly when large transactions were being processed, and STOP REPLICA was requested. This issue also prevented graceful server shutdown, requiring process termination or system restart. (Bug #115966, Bug #37008345)
> `대용량 트랜잭션 처리 중 STOP REPLICA 명령이 실행될 때 replication channel 이 정상적으로 중지되지 않는 문제를 해결했습니다. 이 문제는 서버의 정상적인 종료를 방해하며, 프로세스 강제로 종료하거나 시스템 재시작이 필요했습니다. (Bug #115966, Bug #37008345)`

Find the complete list of bug fixes and changes in the MySQL 8.4.4 release notes.

## Improvements
- <a href="https://perconadev.atlassian.net/browse/PS-9148" target="_blank">PS-9148</a> : Extends the Data masking with new additions from MySQL 8.3.0 Enterprise Data Masking and De-Identification Component Variables.
- `데이터 마스킹 기능을 확장하여 MySQL 8.3.0 Enterprise 데이터 마스킹 및 비식별화 컴포넌트 변수에서 추가된 새로운 기능을 통합합니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9024" target="_blank">PS-9024</a> : Improves the behavior of audit_log_filter_set_user to support wildcards in the hostname.
- `audit_log_filter_set_user 동작이 개선되어 hostname 에서 와일드카드를 지원합니다.`

## Bug fixes
- <a href="https://perconadev.atlassian.net/browse/PS-9391" target="_blank">PS-9391</a> : The replication broke with the error HA_ERR_KEY_NOT_FOUND when the slave_rows_search_algorithms were set to INDEX_SCAN,HASH_SCAN.
- `slave_rows_search_algorithms 값을 INDEX_SCAN, HASH_SCAN 으로 설정했을 때 복제가 HA_ERR_KEY_NOT_FOUND 오류와 함께 중단되었습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9416" target="_blank">PS-9416</a> : The error messages from the Key Management Interoperability Protocol (KMIP) component were not descriptive.
- `키 관리 상호 운용성 프로토콜(KMIP) 컴포넌트의 오류 메시지가 충분히 설명적이지 않았습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9509" target="_blank">PS-9509</a> : Percona Server stopped tracking the global_connection_memory when using thread_handling='pool-of-threads'.
- `Percona Server 에서 thread_handling='pool-of-threads' 옵션을 설정할 때 global_connection_memory 를 추적하지 않았습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9537" target="_blank">PS-9537</a> : When building a new component that used mysql_command_xxx services (such as mysql_command_factory, mysql_command_query, etc.), it was impossible to reuse the same connection to run multiple queries. This issue was observed with SELECT queries, but it may also apply to INSERT, UPDATE, and DELETE operations.
- `mysql_command_xxx 서비스(mysql_command_factory, mysql_command_query 등)를 사용하는 새로운 컴포넌트를 빌드할 때, 동일한 연결을 재사용하여 여러 개의 쿼리를 실행하는 것이 불가능했습니다. 이 문제는 SELECT 쿼리에서 확인되었지만, INSERT, UPDATE, DELETE 작업에도 영향을 미칠 가능성이 있습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9542" target="_blank">PS-9542</a> : Added Clang-19 to Azure pipelines, and fixed the clang-19 compilation issues.
- `Azure 파이프라인에 Clang-19 를 추가하고, Clang-19 컴파일 문제를 해결했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9551" target="_blank">PS-9551</a> : When building a new component that used mysql_command_xxx services (such as mysql_command_factory, mysql_command_query, etc.), a server exit was encountered when setting the MYSQL_COMMAND_LOCAL_THD_HANDLE option.
- `MySQL 명령 서비스(mysql_command_factory, mysql_command_query 등)를 사용하는 컴포넌트를 빌드할 때, 단일 연결을 재사용하여 여러 개의 쿼리를 실행하는 것이 이전에는 불가능했습니다. 이 제한 사항은 SELECT 쿼리에서 확인되었지만, INSERT, UPDATE, DELETE 작업에도 영향을 미쳤을 가능성이 있습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9611" target="_blank">PS-9611</a> : An assertion failure occurred during server shutdown: !is_set() || m_can_overwrite_status.
- `서버 종료 중에 어설션 오류가 발생했습니다: !is_set() || m_can_overwrite_status.`

- <a href="https://perconadev.atlassian.net/browse/PS-9612" target="_blank">PS-9612</a> : Percona Server build failed if more than 128 threads were available. Percona merged the fix from MariaDB.
- `Percona Server 빌드 시 128개 이상의 스레드가 사용 가능한 경우 실패하는 문제가 발생했습니다. Percona 는 이 문제를 해결하기 위해 MariaDB 의 패치를 병합했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9654" target="_blank">PS-9654</a> : There was an incorrect usage of setup_component_customized.inc in the MySQL Test Runner (MTR) tests.
- `MySQL Test Runner(MTR) 테스트에서 setup_component_customized.inc가 잘못 사용되었습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9033" target="_blank">PS-9033</a> : The audit_log_filter plugin did not register remote accesses.
- `The audit_log_filter 플러그인이 원격 액세스를 등록하지 않았습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9464" target="_blank">PS-9464</a> : Some queries that used hash antijoins returned incorrect results when the hash table did not fit in the join buffer and spilled to the disk. (The query triggering the issue specified LEFT JOIN, which was transformed internally from a left outer join to an antijoin.) Percona merged the fix from MySQL (Bug #116334, Bug #37161583).
- `hash antijoins 을 사용하는 일부 쿼리가 join buffer 에 hash table 을 저장할 수 없어 디스크로 스필(spill)될 때 잘못된 결과를 반환하는 문제가 발생했습니다. (문제가 발생한 쿼리는 LEFT JOIN 을 사용했으며, 내부적으로 left outer join 에서 antijoin 으로 변환되었습니다.) Percona 는 MySQL 의 수정 사항(Bug #116334, Bug #37161583)을 병합하여 해당 문제를 해결했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9614" target="_blank">PS-9614</a> : The Pool-of-Threads timer thread failed to start if mysqld was started with --daemonize.
- `pool-of-threads가 활성화된 상태에서 --daemonize 옵션과 함께 mysqld를 시작하면 timer_thread 누락되는 문제가 발생했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9668" target="_blank">PS-9668</a> : The server exited when executing LOCK TABLES FOR BACKUP after audit logs were enabled.
- `audit 로그가 활성화된 후 LOCK TABLES FOR BACKUP 을 실행할 때 서버가 종료되었습니다.`