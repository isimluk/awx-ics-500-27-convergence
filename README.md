# Auditable Events or Activities

## Authentication Events - Logons - Success

```
Sep 25 15:46:09 mnau dockerd-current[10023]: 2019-09-25 13:46:09,571 INFO     awx.api.generics User admin logged in.
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 POST /api/login/ - HTTP/1.1 302
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/ - HTTP/1.1 200
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/me/ - HTTP/1.1 200
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/config/ - HTTP/1.1 200
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/workflow_approvals/?status=pending&page_size=1 - HTTP/1.1 200
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/teams/?page_size=20&order_by=name - HTTP/1.1 200
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/me/ - HTTP/1.1 200
Sep 25 15:46:09 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/teams/1/ - HTTP/1.1 200
Sep 25 15:46:10 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/me/ - HTTP/1.1 200
Sep 25 15:46:10 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/users/1/admin_of_organizations/ - HTTP/1.1 200
Sep 25 15:46:10 mnau dockerd-current[10023]: 172.18.0.1 OPTIONS /api/v2/teams/ - HTTP/1.1 200
Sep 25 15:46:10 mnau dockerd-current[10023]: 172.18.0.1 OPTIONS /api/v2/teams/ - HTTP/1.1 200
Sep 25 15:46:10 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/organizations/?name__iexact=Default&role_level=admin_role - HTTP/1.1 200
```

## Authentication Events - Logons - Failure

```
Sep 25 15:44:58 mnau dockerd-current[10023]: 2019-09-25 13:44:58,065 WARNING  awx.api.generics Login failed for user admin from 172.18.0.1
Sep 25 15:44:58 mnau dockerd-current[10023]: 2019-09-25 13:44:58,086 WARNING  django.request Unauthorized: /api/login/
Sep 25 15:44:58 mnau dockerd-current[10023]: 2019-09-25 13:44:58,086 WARNING  django.request Unauthorized: /api/login/
Sep 25 15:44:58 mnau dockerd-current[10023]: 172.18.0.1 POST /api/login/ - HTTP/1.1 401
```

## Authentication Events - Logoffs - Success

```
Sep 25 15:58:51 mnau dockerd-current[10023]: 2019-09-25 13:58:51,050 INFO     awx.api.generics User admin logged out.
Sep 25 15:58:51 mnau dockerd-current[10023]: 172.18.0.1 GET /api/logout/ - HTTP/1.1 302
Sep 25 15:58:51 mnau dockerd-current[10023]: 172.18.0.1 GET /api/ - HTTP/1.1 200
Sep 25 15:58:51 mnau dockerd-current[10023]: 172.18.0.1 GET /api/v2/auth/ - HTTP/1.1 200
```

## :construction: User & Group Management events - User add

```
Sep 26 11:18:15 mnau dockerd-current[18199]: 2019-09-26 09:18:15,958 INFO     awx.api.generics User NewUser1 created by admin.
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 POST /api/v2/organizations/1/users/ - HTTP/1.1 201
Sep 26 11:18:16 mnau dockerd-current[18199]: /venv/awx/lib64/python3.6/site-packages/rest_framework/pagination.py:198: UnorderedObjectListWarning: Pagination may yield inconsistent results with an unordered object_list: <class 'django.contrib.auth.models.User'> QuerySet.
Sep 26 11:18:16 mnau dockerd-current[18199]:   paginator = self.django_paginator_class(queryset, page_size)
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 GET /api/v2/me/ - HTTP/1.1 200
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 GET /api/v2/users/?page_size=20&order_by=username - HTTP/1.1 200
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 GET /api/v2/users/11/ - HTTP/1.1 200
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 GET /api/v2/me/ - HTTP/1.1 200
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 GET /api/v2/users/1/admin_of_organizations/ - HTTP/1.1 200
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 OPTIONS /api/v2/users/ - HTTP/1.1 200
Sep 26 11:18:16 mnau dockerd-current[18199]: 172.18.0.1 OPTIONS /api/v2/users/ - HTTP/1.1 200
```

## :construction: User & Group Management events - User delete

```
Sep 26 13:23:25 mnau dockerd-current[22437]: 2019-09-26 11:23:25,739 INFO     awx.api.views User TestUser deleted by admin.
Sep 26 13:23:25 mnau dockerd-current[22437]: 172.18.0.1 DELETE /api/v2/users/13/ - HTTP/1.1 204
Sep 26 13:23:25 mnau dockerd-current[22437]: 172.18.0.1 GET /api/v2/users/?page_size=20&order_by=username - HTTP/1.1 200
Sep 26 13:23:25 mnau dockerd-current[22437]: 172.18.0.1 GET /api/v2/me/ - HTTP/1.1 200
Sep 26 13:23:26 mnau dockerd-current[22437]: 172.18.0.1 OPTIONS /api/v2/users/ - HTTP/1.1 200
Sep 26 13:23:26 mnau dockerd-current[22437]: 172.18.0.1 OPTIONS /api/v2/users/ - HTTP/1.1 200
```

## :construction: User & Group Management events - User modify

```
```

## User & Group Management events - User suspend

N/A - User suspend action not available.

# Assessed Systems
  * AWX 7.0.0-228-g640e5391f
  * Ansible 2.10.0.dev0
