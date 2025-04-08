---
title: "[Release Note] Percona Server for MySQL 8.4.2-2"
date: 2025-04-08 19:12:00 +0900
categories: [2. DBMS Release Note, Percona Server for MySQL 8.4]
tags: [DBMS, MySQL, Percona, Release Notes]
image:
  path: /assets/img/posts_2025/Percona_Server_for_MySQL.jpg
  lqip: data:image/webp;base64,UklGRhgBAABXRUJQVlA4IAwBAADwBQCdASogACAAPm0qkUYkIiGhMBgMAIANiUAWI3JzUBM1EetKtOpv62bapqVOiP9xek71uFiocAD++g+Bbg7nDaAbZO/dwsyF8NgAs+2QcaWV9mx11MqNK6eF8kwAfxdffojwIH+LfFvi28emn3JH+nye9f1M39h+Oe+qSCH1rWZsL3QgMQVXpLiw+EIQSy8ENzwkdqoWthmM5rSrXeMgSnbgi797H0Dw96NCPHQT4POhbVOrWr05dcFYG
  alt: Release Note Percona Server for MySQL 8.4.2-2
---

> <a href="https://docs.percona.com/percona-server/8.4/release-notes/8.4.2-2.html" target="_blank">Percona Server for MySQL 8.4.2-2</a> (2024-11-04)
{: .prompt-info }

## Release highlights
Improvements and bug fixes introduced by Oracle for MySQL 8.4.1 and 8.4.2 and included in Percona Server for MySQL are the following:

- MySQL stopped unexpectedly during an UPDATE after an ALTER TABLE operation.
> `ALTER TABLE 작업 후 UPDATE 중 MySQL 이 예기치 않게 중지되었습니다.`

- Shutting down the server after an XA START with an empty XA transaction caused it to stop unexpectedly.
> `빈 XA 트랜잭션으로 XA START 후 서버를 종료하면 예기치 않게 중지되었습니다.`

- Shutting down the replication applier or binlog applier during an empty XA transaction caused the system to stop unexpectedly.
> `복제 applier 나 binlog applier 를 빈 XA 트랜잭션 중에 종료하면 시스템이 예기치 않게 중지되었습니다.`

- The result from a spatial index with a column containing a spatial reference identifier (SRID) was empty. Using FORCE INDEX to scan this index caused an assertion error.
> `공간 참조 식별자(SRID)를 포함한 컬럼의 spatial index 에서 결과가 비어 있었고, 해당 index 를 FORCE INDEX 로 스캔하면 assert 오류가 발생했습니다.`

- In some cases, after creating more than 8000 tables, the server failed to restart.
> `일부 경우 8000개 이상의 테이블을 생성한 후 서버가 재시작에 실패했습니다.`

- Startup tablespace file scanning performance was improved.
> `서버 시작 시 테이블스페이스 파일 스캔 성능이 향상되었습니다.`

Find the complete list of bug fixes and changes in the MySQL 8.4.1 Release Notes and MySQL 8.4.2 Release Notes.

## Bug fixes
- <a href="https://perconadev.atlassian.net/browse/PS-8057" target="_blank">PS-8057</a> : slow_query_log_file does not match the filename defined in my.cnf.
> `slow_query_log_file 이 my.cnf 에 설정된 파일 이름과 일치하지 않습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9144" target="_blank">PS-9144</a> : Missing rows after running a null ALTER with ALGORTITHM=INPLACE.
> `ALGORTITHM=INPLACE 로 ALTER 로 null 을 실행한 후 row 가 누락되었습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9214" target="_blank">PS-9214</a> : An ALTER table online results in a “duplicate key” error on the primary key (only index).
> `Online 으로 ALTER TABLE 을 실행하면 primary key(only index) 에서 "duplicate key" 오류가 발생합니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9306" target="_blank">PS-9306</a> : The following MySQL versions unexpectedly exit if the database has more than 10K tables:
> `다음 MySQL 버전에서는 데이터베이스에 10,000개 이상의 테이블이 있는 경우 예상치 못하게 종료됩니다: 8.0.38 / 8.4.1 / 9.0.0`

- <a href="https://perconadev.atlassian.net/browse/PS-9314" target="_blank">PS-9314</a> : Using a JSON_TABLE in Percona Server for MySQL 8.0.36 causes a signal 11 error.
> `Percona Server for MySQL 8.0.36 에서 JSON_TABLE 을 사용하면 signal 11 오류가 발생합니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9286" target="_blank">PS-9286</a> : The KMIP component left keys in a pre-active state.
> `KMIP 구성 요소가 키를 pre-active 상태에 두었습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9384" target="_blank">PS-9384</a> : A race condition between dict_stats_thread and the cost model initialization cause sporadic exits in Jenkins on start up.
> `dict_stats_thread 와 비용모델 초기화 간의 경쟁 조건으로 인해 Jenkins 시작 시 간헐적으로 종료되는 문제가 발생합니다.`