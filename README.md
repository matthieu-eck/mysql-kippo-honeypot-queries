# mysql-kippo-honeypot-queries

Just some mysql queries I created to extract data from my kippo honeypot database.

More information here : https://bruteforce.gr/logging-kippo-events-using-mysql-db.html


**Which IP successfully with a password and how many times.**

```bash
mysql> SELECT sessions.ip,auth.password, COUNT(sessions.ip)   FROM sessions INNER JOIN auth ON sessions.id = auth.session   where auth.password="raspberry"   GROUP BY sessions.ip   ORDER BY COUNT(sessions.ip) DESC LIMIT 10;
+----------------+-----------+--------------------+
| ip             | password  | COUNT(sessions.ip) |
+----------------+-----------+--------------------+
| 193.104.41.137 | raspberry |                 25 |
| 182.100.67.114 | raspberry |                 23 |
| 43.255.189.84  | raspberry |                 17 |
| 218.65.30.73   | raspberry |                 17 |
| 218.87.111.116 | raspberry |                 14 |
| 218.65.30.92   | raspberry |                 14 |
| 218.87.111.110 | raspberry |                 14 |
| 218.65.30.61   | raspberry |                 14 |
| 218.87.111.109 | raspberry |                 13 |
| 218.87.111.117 | raspberry |                 13 |
+----------------+-----------+--------------------+
10 rows in set (5.39 sec)
```



**List usernames (except root), password and occurence (limit by 10) :**

```bash
mysql> SELECT username,password,COUNT(password) FROM auth where username not like 'root' group by password order by COUNT(password) desc limit 10;
+----------+----------+-----------------+
| username | password | COUNT(password) |
+----------+----------+-----------------+
| admin    | admin    |             820 |
| admin    | 123456   |             731 |
| admin    | password |             638 |
| ubnt     | ubnt     |             633 |
| admin    |          |             553 |
| admin    | test     |             324 |
| admin    | 1234     |             310 |
| guest    | guest    |             277 |
| admin    | default  |             253 |
| admin    | 12345    |             248 |
+----------+----------+-----------------+

```
---
---
**Look for a password containing cisco1234 and count how many times attackers tried :**

```bash
mysql> SELECT username,password,COUNT(password) FROM auth where password like '%cisco1234%' group by password order by COUNT(password) desc limit 10;
+----------+-------------+-----------------+
| username | password    | COUNT(password) |
+----------+-------------+-----------------+
| root     | cisco123456 |              37 |
| root     | cisco1234   |              36 |
| root     | cisco12345  |               1 |
+----------+-------------+-----------------+
3 rows in set (3.84 sec)

```
---
---
**Top 10 of attacker's IP :**

```bash
mysql> SELECT ip,COUNT(ip) from sessions GROUP by ip order by COUNT(ip) desc limit 10;
+----------------+-----------+
| ip             | COUNT(ip) |
+----------------+-----------+
| 43.229.52.38   |     28628 |
| 43.255.189.84  |     17851 |
| 103.41.124.20  |     13360 |
| 103.41.124.46  |     12247 |
| 43.255.189.86  |     12058 |
| 43.229.52.60   |     11255 |
| 43.229.53.41   |     11124 |
| 103.41.124.111 |     10921 |
| 103.41.124.103 |     10776 |
| 103.41.124.19  |     10349 |
+----------------+-----------+
10 rows in set (1.22 sec)
```
---
---
**Get top 10 Passwords :**

```bash
mysql> SELECT username,password,COUNT(password) FROM auth group by password order by COUNT(password) desc limit 10;
+--------------------------+----------+-----------------+
| username                 | password | COUNT(password) |
+--------------------------+----------+-----------------+
| root                     | wubao    |            2899 |
| root                     | jiamima  |            2761 |
| root                     | admin    |            2745 |
| root                     | password |            2343 |
| root                     | 123456   |            2255 |
| root/sshd/contrib/cygwin |          |            2037 |
| root                     | rOOt     |            1934 |
| root                     | 1234     |            1737 |
| root                     | 12345    |            1685 |
| root                     | p@ssw0rd |            1369 |
+--------------------------+----------+-----------------+
10 rows in set (9.85 sec)

```
---
---




**Get number of uniq passwords in the databse:**

```bash

mysql> SELECT count(distinct password) FROM auth;
+--------------------------+
| count(distinct password) |
+--------------------------+
|                   447993 |
+--------------------------+
1 row in set (0.00 sec)
```

A better and more complete list can be found here : https://github.com/ikoniaris/kippo-graph/blob/40cf2cc54ce9fcbc424d91efd82809830789335d/include/sql.php

