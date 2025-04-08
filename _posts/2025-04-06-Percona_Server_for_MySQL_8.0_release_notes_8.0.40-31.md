---
title: "Percona Server for MySQL 8.0.40-31"
date: 2025-04-08 14:34:00 +0900
categories: [2. DBMS Release Note, Percona Server for MySQL 8.0]
tags: [DBMS, MySQL, Percona, Release Notes]
image:
  path: /assets/img/posts_2025/Percona_Server_for_MySQL.jpg
  lqip: data:image/webp;base64,UklGRhgBAABXRUJQVlA4IAwBAADwBQCdASogACAAPm0qkUYkIiGhMBgMAIANiUAWI3JzUBM1EetKtOpv62bapqVOiP9xek71uFiocAD++g+Bbg7nDaAbZO/dwsyF8NgAs+2QcaWV9mx11MqNK6eF8kwAfxdffojwIH+LfFvi28emn3JH+nye9f1M39h+Oe+qSCH1rWZsL3QgMQVXpLiw+EIQSy8ENzwkdqoWthmM5rSrXeMgSnbgi797H0Dw96NCPHQT4POhbVOrWr05dcFYG
  alt: Percona Server for MySQL 8.0.40-31
---

> <a href="https://docs.percona.com/percona-server/8.0/release-notes/8.0.40-31.html" target="_blank">Percona Server for MySQL 8.0.40-31</a> (2024-12-30)
{: .prompt-info }

## Release highlights
This release merges the MySQL 8.0.40 code base. Percona has implemented the Agile Release Train Strategy. This strategy allows us to deliver software updates more frequently, ensuring you receive the latest features and improvements as soon as they’re ready. This approach helps us respond quickly to your needs and provide a better overall experience with our product.

Improvements and bug fixes provided by Oracle for MySQL 8.0.40 and included in Percona Server for MySQL are the following:

- Changes in MySQL 8.0.33 caused queries using joins on InnoDB tables to perform worse because refactoring affected functions that were previously inline.

- The server crashed when it tried to update columns altered with NULL as the default value using the INSTANT algorithm.

- The server could crash during DELETE or UPDATE operations if a column was dropped using the INSTANT algorithm.

- Importing a table created under a different sql_mode sometimes led to schema mismatches, risking data corruption in secondary indexes. The fix now includes integrity checks on the imported tablespace.

- Rebuilding tables with secondary indexes required more file I/O operations compared to MySQL 8.0.26, which slowed down query performance.

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