---
title: "[Release Note] Percona Server for MySQL 8.0.40-31"
date: 2025-04-08 14:34:00 +0900
categories: [2. DBMS Release Note, Percona Server for MySQL 8.0]
tags: [DBMS, MySQL, Percona, Release Notes]
image:
  path: /assets/img/posts_2025/Percona_Server_for_MySQL.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/2wBDAQkJCQwLDBgNDRgyIRwhMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjL/wAARCAAQABADASIAAhEBAxEB/8QAGgAAAgMBAQAAAAAAAAAAAAAABAUCAwYBB//EADsQAAIBAgQDBgMFBwUBAAAAAAABAgMRBAUSITEGQVFhEyJxgZEjMoGRobHR8EJicuHwFSMzQ2LC8WKi/8QAGQEAAwEBAQAAAAAAAAAAAAAAAAECAwQF/8QAJhEAAgIBBAICAgMAAAAAAAAAAAECEQMSITEEE0FRYQUTIjNhsf/aAAwDAQACEAMQAAAB8rNZa8T4PUK0KuocUt7ctZosYkxhIUExixpyNnNuSPArxOdEk1Ak+F4sh1F3rlYrXvFJKR0NfMVltMTIyIlaAaKVgAc8LQx22NPIgZT4pI7uWYZAGJWDkW2vEVTo6Ejr6qW8X1vK4zrREdm6WiOjIAqvP3qsyeFWTNdW8+M34ffco50aBprV42JWAdUly5EMuvNUK2N8lKqIlA6R5nh1lFxEViNDKFkxyEEiYAKZPYHg2Er4h2MJFRHiQGp9oNBSR3F0myuwOYvN2F4vdoOc+hglSiQoY+9Pdi7HcKl5ztF8JJd+ykeZHP3YkSak8A20swq1XZfI15chYoCKuI7AUQWGeD6kV+DaI9LLr9mnxcffJKu5SGHozm2uKoQSB4Khf8AeTPhMNnq6WjlgZqksUbSTkfTo5pUsBFg0G2QHq9YqjvFo2mssE4atGvTD7R7WwVkKtEZMQzLIwRkQPEe+Db4UpPheOPTL8Y6XsoOJTDtyzD40FtD0BSYyBT3YkfOBu9OJNSvmEFAH2yH9UPQU5j2XGmdHwTDNXdMSIPqW1DKQqQN2Pi9+l9AoKQAH/2Q==
  alt: Release Note Percona Server for MySQL 8.0.40-31
---

> <a href="https://docs.percona.com/percona-server/8.0/release-notes/8.0.40-31.html" target="_blank">Percona Server for MySQL 8.0.40-31</a> (2024-12-30)
{: .prompt-info }

## Release highlights
This release merges the MySQL 8.0.40 code base. Percona has implemented the Agile Release Train Strategy. This strategy allows us to deliver software updates more frequently, ensuring you receive the latest features and improvements as soon as they’re ready. This approach helps us respond quickly to your needs and provide a better overall experience with our product.

Improvements and bug fixes provided by Oracle for MySQL 8.0.40 and included in Percona Server for MySQL are the following:

- Changes in MySQL 8.0.33 caused queries using joins on InnoDB tables to perform worse because refactoring affected functions that were previously inline.
> `MySQL 8.0.33 변경으로 인해 InnoDB 테이블에서 조인을 사용하는 쿼리의 성능이 저하되었습니다. 이는 inline 함수들을 refactoring 하면서 영향을 받았기 때문입니다.`

- The server crashed when it tried to update columns altered with NULL as the default value using the INSTANT algorithm.
> `INSTANT 알고리즘으로 기본값을 NULL 로 변경한 컬럼을 업데이트하려고 할 때 서버가 crash 되었습니다.`

- The server could crash during DELETE or UPDATE operations if a column was dropped using the INSTANT algorithm.
> `INSTANT 알고리즘으로 컬럼을 삭제한 경우, DELETE 나 UPDATE 작업 중에 서버가 crash 될 수 있었습니다.`

- Importing a table created under a different sql_mode sometimes led to schema mismatches, risking data corruption in secondary indexes. The fix now includes integrity checks on the imported tablespace.
> `다른 sql_mode 에서 생성된 테이블을 import 할 경우 스키마 불일치로 인해 secondary indexe 의 데이터 손상이 발생할 수 있었습니다. 이제는 import 된 테이블스페이스에 대해 무결성 검사가 포함되어 있습니다.`

- Rebuilding tables with secondary indexes required more file I/O operations compared to MySQL 8.0.26, which slowed down query performance.
> `secondary indexe 를 가진 테이블을 rebuild 할 때 MySQL 8.0.26 보다 더 많은 파일 I/O 작업이 필요했으며, 이로 인해 쿼리 성능이 저하되었습니다.`

Find the complete list of bug fixes and changes in the MySQL 8.0.40 Release Notes.

## Bug Fixes
- <a href="https://perconadev.atlassian.net/browse/PS-9323" target="_blank">PS-9323</a> : A file name change in the documentation broke a link to Percona Toolkit UDFs in package install output.
> `문서의 파일 이름 변경으로 인해 패키지 설치 출력에서 Percona Toolkit UDFs 링크가 끊겼습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9369" target="_blank">PS-9369</a> : The Audit plugin caused a memory leak. This occurred when threads remained connected to the database for extended periods.
> `Audit plugin 으로 인해 메모리 누수가 발생했습니다. 이는 스레드가 오랜 시간 동안 데이터베이스에 연결된 상태로 남아 있을 때 발생했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9382" target="_blank">PS-9382</a> : After an upgrade, the telemetry daemon ran continuously. The telemetry daemon was manually stopped and the service was disabled. Adding percona_telemetry_disable=1 to the configuration file and restarting MySQL led to the server becoming unresponsive and required a forced termination.
> `업그레이드 후 telemetry 데몬이 계속 실행되었습니다. telemetry 데몬을 수동으로 중지하고 서비스를 비활성화했습니다. 설정 파일에 percona_telemetry_disable=1 을 추가한 후 MySQL 을 재시작하자 서버가 응답하지 않게 되었고 강제 종료가 필요했습니다.`

- <a href="https://perconadev.atlassian.net/browse/PS-9453" target="_blank">PS-9453</a> : The percona_telemetry tool caused a long wait on COND_thd_list if the root user is absent.
> `percona_telemetry 도구는 root 사용자가 없을 경우 COND_thd_list 에서 긴 대기 시간을 유발했습니다.`