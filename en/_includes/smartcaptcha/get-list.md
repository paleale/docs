Get a list of captchas:

```bash
yc smartcaptcha captcha list
```

Result:

```text
+----------------------+---------+---------------------+------------+----------------+----------------+-------------+
|          ID          |  NAME   |       CREATED       | COMPLEXITY | PRE-CHECK TYPE | CHALLENGE TYPE | RULES COUNT |
+----------------------+---------+---------------------+------------+----------------+----------------+-------------+
| bpne29ifsca8******** | test    | 2025-02-24 17:16:13 | MEDIUM     | CHECKBOX       | IMAGE_TEXT     |           0 |
| bpnm7lhokq2t******** | prod    | 2025-02-26 09:12:02 | MEDIUM     | CHECKBOX       | IMAGE_TEXT     |           0 |
| bpn43btuo4g9******** | website | 2025-02-26 09:12:42 | MEDIUM     | CHECKBOX       | IMAGE_TEXT     |           0 |
+----------------------+---------+---------------------+------------+----------------+----------------+-------------+
```