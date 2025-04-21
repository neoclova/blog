---
title: "[Release Note] Percona Server for MySQL 8.4.3-3"
date: 2025-04-08 19:31:00 +0900
categories: [2. DBMS Release Note, Percona Server for MySQL 8.4]
tags: [DBMS, MySQL, Percona, Release Notes, NeoClova, neoclova, 네오클로바]
image:
  path: /assets/img/posts_2025/Percona_Server_for_MySQL.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAAQABADASIAAhEBAxEB/8QAGgAAAgMBAQAAAAAAAAAAAAAABAUCAwYBB//EADsQAAIBAgQDBgMFBwUBAAAAAAABAgMRBAUSITEGQVFhEyJxgZEjMoGRobHR8EJicuHwFSMzQ2LC8WKi/8QAGQEAAwEBAQAAAAAAAAAAAAAAAAECAwQF/8QAJhEAAgIBBAICAgMAAAAAAAAAAAECEQMSITEEE0FRYQUTIjNhsf/aAAwDAQACEAMQAAAB8rNZa8T4PUK0KuocUt7ctZosYkxhIUExixpyNnNuSPArxOdEk1Ak+F4sh1F3rlYrXvFJKR0NfMVltMTIyIlaAaKVgAc8LQx22NPIgZT4pI7uWYZAGJWDkW2vEVTo6Ejr6qW8X1vK4zrREdm6WiOjIAqvP3qsyeFWTNdW8+M34ffco50aBprV42JWAdUly5EMuvNUK2N8lKqIlA6R5nh1lFxEViNDKFkxyEEiYAKZPYHg2Er4h2MJFRHiQGp9oNBSR3F0myuwOYvN2F4vdoOc+hglSiQoY+9Pdi7HcKl5ztF8JJd+ykeZHP3YkSak8A20swq1XZfI15chYoCKuI7AUQWGeD6kV+DaI9LLr9mnxcffJKu5SGHozm2uKoQSB4Khf8AeTPhMNnq6WjlgZqksUbSTkfTo5pUsBFg0G2QHq9YqjvFo2mssE4atGvTD7R7WwVkKtEZMQzLIwRkQPEe+Db4UpPheOPTL8Y6XsoOJTDtyzD40FtD0BSYyBT3YkfOBu9OJNSvmEFAH2yH9UPQU5j2XGmdHwTDNXdMSIPqW1DKQqQN2Pi9+l9AoKQAH/2Q==
  alt: Release Note Percona Server for MySQL 8.4.3-3
---

> <a href="https://docs.percona.com/percona-server/8.4/release-notes/8.4.3-3.html" target="_blank">Percona Server for MySQL 8.4.3-3</a> (2024-12-18)
{: .prompt-info }

## Release highlights
Improvements and bug fixes introduced by Oracle for MySQL 8.4.3 and included in Percona Server for MySQL are the following:

- The query SELECT * FROM sys.innodb_lock_waits; now fetches only two locks per wait, instead of scanning all locks twice, improving performance under heavy load. Additionally, primary keys have been added to DATA_LOCKS and DATA_LOCK_WAITS. (Bug #100537, Bug #31763497)
> `SELECT * FROM sys.innodb_lock_waits; 쿼리는 이제 모든 잠금을 두 번 스캔하는 대신 두 개의 잠금만 가져오기 때문에 높은 부하 환경에서 성능이 향상되었습니다. 추가로, DATA_LOCKS 와 DATA_LOCK_WAITS 에 Primary Key 가 추가되었습니다. (Bug #100537, Bug #31763497)`

- Changes in MySQL 8.0.33 caused performance degradation for queries using joins on InnoDB tables due to refactoring of functions that were previously inline.
> `MySQL 8.0.33 의 변경 사항으로 인해(이전에 inline 으로 구현되었던 함수들의 리팩토링) InnoDB 테이블에서 조인을 사용하는 쿼리의 성능이 저하되었습니다.`

- The server crashed when it tried to update columns altered with NULL as the default value using the INSTANT algorithm.
> `서버가 INSTANT 알고리즘을 사용하여 기본값으로 NULL이 설정된 컬럼을 업데이트하려고 할 때 충돌이 발생했습니다.`

- The server could crash during DELETE or UPDATE operations if a column was dropped using the INSTANT algorithm.
> `INSTONT 알고리즘을 사용하여 컬럼을 삭제하면 DELETE 또는 UPDATE 작업 중에 서버가 충돌할 수 있습니다.`

- Importing a table created under a different sql_mode sometimes led to schema mismatches, risking data corruption in secondary indexes. The fix now includes integrity checks on the imported tablespace.
> `다른 sql_mode 에서 생성된 테이블을 가져올 때 종종 스키마 불일치가 발생하여 secondary index 에서 데이터 손상의 위험이 있었습니다. 이제 가져온 테이블스페이스에 대해 무결성 검사가 포함되어 문제가 해결되었습니다.`

- Rebuilding tables with secondary indexes required more file I/O operations compared to MySQL 8.0.26, which slowed down query performance.
> `secondary indexes 가 있는 테이블을 재구성할 때 MySQL 8.0.26 에 비해 더 많은 파일 I/O 작업이 필요하여 쿼리 성능이 저하되었습니다.`

Find the complete list of bug fixes and changes in the MySQL 8.4.3 release notes.

## Bug fixes
- <a href="https://perconadev.atlassian.net/browse/PS-9382" target="_blank">PS-9382</a> : After an upgrade, the telemetry daemon ran continuously. The telemetry daemon was manually stopped and the service was disabled. Adding percona_telemetry_disable=1 to the configuration file and restarting MySQL led to the server becoming unresponsive and required a forced termination.
> `업그레이드 후 telemetry 데몬이 계속 실행되었습니다. telemetry 데몬을 수동으로 중지하고 서비스를 비활성화했습니다. 설정 파일에 percona_telemetry_disable=1을 추가한 후 MySQL을 재시작하자 서버가 응답하지 않게 되었고 강제 종료가 필요했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9453" target="_blank">PS-9453</a> : The percona_telemetry tool caused a long wait on COND_thd_list if the root user is absent.
> `percona_telemetry 도구는 root 사용자가 없을 경우 COND_thd_list에서 긴 대기 시간을 유발했습니다.`