---
title: "PostgreSQL Internals for Newbies: A Guide to Data Storage, Part One (PostgreSQL 내부 구조 초보자 가이드: 데이터 저장 방식, 1편)"
date: 2025-04-11 19:41:00 +0900
categories: [3. DBMS, PostgreSQL]
tags: [Open source, DBMS, Percona, PostgreSQL, NeoClova, neoclova, 네오클로바]
image:
  path: /assets/img/posts_2025/postgresql_internals_for_newbies_a_guide_to_data_storage_part_one.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDABsSFBcUERsXFhceHBsgKEIrKCUlKFE6PTBCYFVlZF9VXVtqeJmBanGQc1tdhbWGkJ6jq62rZ4C8ybqmx5moq6T/2wBDARweHigjKE4rK06kbl1upKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKSkpKT/wAARCAAUABQDASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwDLsrfzpVXOB1zVqeyaJj/Ep6EUyyGI2wSGAqxaM6RSwHGwSgbm9zQBmyxfOaKv3AtxMw8xePSigCtY3Dxk7QpyMcjNTTXUohYfLhm3EY70UUAZryMWzxRRRQB//9k=
  alt: PostgreSQL Internals for Newbies
---

> 원문 : <a href="https://www.percona.com/blog/postgresql-internals-for-newbies-a-guide-to-data-storage-part-one/" target="_blank">PostgreSQL Internals for Newbies: A Guide to Data Storage, Part One</a> (September 6, 2024)
{: .prompt-info }

데이터베이스 초보자들은 PostgreSQL 을 처음 접할 때 '뒤에서 어떤 일이 벌어지는지' 궁금해하곤 합니다. 테이블을 생성하고 데이터를 추가할 때 눈에 보이지 않는 많은 일들이 일어납니다. 아마 이렇게 질문할 수 있겠죠. ‘데이터는 어디로 가는 걸까?’ 다행히도, 이러한 세부사항을 알아내는 것은 어렵지 않습니다.

## Creating a table

테이블을 생성하면 그에 대한 많은 metadata 가 함께 생성됩니다. 아래 예시에서는 'demo1' 이라는 이름의 테이블이 생성됩니다. PostgreSQL 은 우리가 지정한 이름으로 테이블을 참조하지 않고, attrelid 라 불리는 정수를 사용해 참조합니다.

```
demo=# create table demo1 (columna int);
CREATE TABLE
demo=# select attrelid, attname, atttypid from pg_attribute where attrelid = 'demo1'::regclass;
attrelid | attname | atttypid 
----------+----------+----------
16935 | tableoid | 26
16935 | cmax | 29
16935 | xmax | 28
16935 | cmin | 29
16935 | xmin | 28
16935 | ctid | 27
16935 | columna | 23
(7 rows)
 
demo=#
```

이 경우, attrelid 는 16935 입니다. 시스템에서 직접 시도해 보되, attrelid 는 다를 수 있다는 점에 유의하세요.

## Where is my data?

아직 데이터를 추가하지 않았기 때문에 현재는 데이터가 없습니다. 하지만 데이터가 어디에 저장될지는 알 수 있습니다. 테이블을 생성하면 attrelid 로 이름이 지정된 파일이 생성됩니다. 해당 디렉터리는 데이터베이스의 데이터 딕셔너리 아래에 위치하게 됩니다. 다음 명령어를 통해 data_dictionary 를 확인할 수 있습니다.

```
demo=# show data_directory;
data_directory 
-----------------------------
/var/lib/postgresql/16/main
(1 row)
```

데이터 딕셔너리 아래에는 여러 디렉터리들이 있습니다. 데이터 딕셔너리를 하나의 오피스 빌딩이라고 생각하고, 16935 는 그 안에 있는 하나의 사무실이라고 보면 됩니다.

```
demo=# select pg_relation_filepath('demo1');
pg_relation_filepath 
----------------------
base/16934/16935
(1 row)
 
demo=#
```

새로 생성된 비어있는 테이블은 base/16934/16935 에 위치합니다. 전체 경로는 데이터 디렉터리에 이 정보를 더한 것이며, 즉 /var/lib/postgresql/16/main/base/16934/16935 입니다.

```
root@test1:/var/lib/postgresql/16/main# ls -la base/16934/16935
-rw------- 1 postgres postgres 0 Aug 29 13:35 base/16934/16935
root@test1:/var/lib/postgresql/16/main#
```

현재 16935 는 비어 있기 때문에 파일 길이가 0 입니다. 이제 데이터를 추가할 시간입니다.

## The data

 ```
demo=# insert into demo1 values (1),(3),(5),(7),(9),(11);
INSERT 0 6
demo=# select * from demo1;
columna 
---------
1
3
5
7
9
11
(6 rows)
 
demo=#
```

그렇다면 우리의 데이터는 어디로 갔을까요? 바로 16935 라는 이름의 파일 안으로 들어갔습니다! 파일 시스템에서 그것을 확인해보세요:

```
root@test1:/var/lib/postgresql/16/main/base/16934# !!
ls -l 16935
-rw------- 1 postgres postgres 8192 Aug 29 13:49 16935
root@test1:/var/lib/postgresql/16/main/base/16934#
```

기본적으로 PostgreSQL 은 8K 블록을 작성하며, 이제 16935 파일에 디스크 상 8K의 데이터가 있는 것을 확인할 수 있습니다. 8K 이상의 데이터를 가지고 있지 않기 때문에, 단 하나의 데이터 블록만 기록된 것입니다.

## Can we see our data?

heap_page_items() 호출을 통해 16935 파일에 무엇이 들어 있는지 확인할 수 있습니다(자세한 내용은 다음 편에서 다룹니다). 입력했던 데이터가 t_data 필드에 표시됩니다. 앞서 입력했던 1, 3, 5, 7, 9, 11이 보이지만, 이 숫자들은 8진수로 표현되어 있다는 점에 유의해야 합니다.

```
demo=# select lp, lp_off, lp_len, t_ctid, t_data from heap_page_items(get_raw_page('demo1',0));
lp | lp_off | lp_len | t_ctid | t_data 
----+--------+--------+--------+------------
1 | 8160 | 28 | (0,1) | x01000000
2 | 8128 | 28 | (0,2) | x03000000
3 | 8096 | 28 | (0,3) | x05000000
4 | 8064 | 28 | (0,4) | x07000000
5 | 8032 | 28 | (0,5) | x09000000
6 | 8000 | 28 | (0,6) | x0b000000
(6 rows)
 
demo=#
```

다음 시간에는 t_cid 와 lp 필드에 대해 다룰 예정입니다.