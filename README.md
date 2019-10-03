# Ansible Tower -- ICS 500-27 Implementation Guide

This guide will help us understand various logging mechanisms available in Ansible Tower. We will learn how to
configure Ansible Tower to generate audit trail compliant with [Intelligence Community Standard 500-27 - Collection and Sharing of Audit Data](https://www.dni.gov/files/documents/FOIA/DF-2016-00213.pdf).

This guide was last tested with Ansible Tower 3.5.x.

## Two Logging Mechanisms Available

Ansible Tower provides comprehensive logging sub-system with wide range of customization options. On top of the
standard logging sub-system Ansible Tower offers high-level interface called **Activity Stream**. Activity Streams
provide audit information that is easy to use and easy to comprehend by human operators.

In the next chapter, we will take look at how to leverage Activity Stream interface, in the third chapter we will dive
into settings of the standard logging mechanism.

## Activity Streams :: High level logs

Most screens in Tower have an Activity Stream (![activitystream](pics/activitystream.png)) button at top right corner.
Clicking this brings up the Activity Stream for this particular object. We won't go through whole variety of objects
that Tower provides, instead we will demonstrate Activity Stream capability on User model.

List of log events for User model.

![Screenshot of Ansible Tower 2.5 - User creation](pics/tower-user-activity-stream-1-creation.jpg)

Each log event contains details that can be reviewed.

![Screenshot of Ansible Tower 2.5 - User creation Detail](pics/tower-user-activity-stream-2-creation-detail.jpg)

User deletion action is logged with all the properties of user model at the time of deletion.

![Screenshot of Ansible Tower 2.5 - User creation Detail](pics/tower-user-activity-stream-4-deletion.jpg)

User modifications are logged while sensitive information (like passwords) will be hidden from the audit log.

![Screenshot of Ansible Tower 2.5 - User modify action](pics/tower-user-activity-stream-3-update.jpg)


## Standard logging :: Low level logs

Ansible Tower provides comprehensive logging sub-system with wide range of customization options. By default Ansible
Tower will log to syslog, `/var/log/tower/*`, `/var/log/supervisor/*`, `/var/log/nginx/*`and/or external logging
aggregation system.

External logging aggregation system can be configured using Ansible Tower web as described in
[Ansible Tower documentation](https://docs.ansible.com/ansible-tower/latest/html/administration/logging.html),
other settings have to be supplied to `/etc/tower/conf.d/`.

### Proceeding without logging aggregation

It assumed that most installations will forward logs from Ansible Tower to log aggregation system (Logstash, Splunk,
Loggly, Sumologic, or other) as described in previous section. For quick testing purposes, however, we can hijack
the external logging stream and log its content to a local file system.

```
cat > /etc/tower/conf.d/log_locally.py <__END__
# This overrides external_logger subsystem, remove this before configuring proper log aggregation
LOGGING['handlers']['external_logger'] = {
    'level': 'DEBUG',
    'class': 'logging.FileHandler',
    'filename': '/var/log/awx.log',
    'formatter': 'simple',
}
```

Note: Using proper logging aggregation will result in much more detailed log messages including changesets required by ICS 500-27.

### Verify configuration

At this point, we will be able to observe audit trail for user log-out/log-in either in your logging aggregation system
or local files. In case of using aggregation system user log-out will look like:

```
{
        "logger_name" => "awx.api.generics",
              "level" => "INFO",
         "stack_info" => nil,
               "type" => "logstash",
               "tags" => [
        [0] "_jsonparsefailure"
    ],
         "@timestamp" => 2019-10-02T19:16:09.957Z,
            "message" => "User admin logged out.",
         "tower_uuid" => "647f0aed-6ce0-4875-9fae-7cd4d92594f5",
    "cluster_host_id" => "localhost",
               "host" => "ansible-tower",
           "@version" => "1"
}
```

In case of not using logging aggregation system. User log-out event will not include all the details.
```
2019-10-02 13:53:28,972 INFO     awx.api.generics User admin logged out.
```

### Optionally, turn on logging for configuration changes
Subset of Ansible Tower settings is stored in database ans this subset can be modified by admin users through web and API,
we can make sure such modifications are logged.
```
cat > /etc/tower/conf.d/log_conf_changes.py <<__END__
# Turn on logging of configuration changes
LOGGING['loggers']['awx.conf.settings']['handlers'] = ['console', 'external_logger']
LOGGING['loggers']['awx.conf.settings']['level'] = 'DEBUG'
__END__
```

As a result, you can see log events like the following when you amend your settings on Tower web or API.

```
2019-10-02 17:52:16,275 DEBUG    awx.conf.settings cache set_many({'ACTIVITY_STREAM_ENABLED': True, 'ACTIVITY_STREAM_ENABLED_FOR_INVENTORY_SYNC': False, 'ORG_ADMINS_CAN_SEE_ALL_USERS': True, 'MANAGE_ORGANIZATION_AUTH': True, 'TOWER_ADMIN_ALERTS': True, 'TOWER_URL_BASE': (.....)
```

### Optionally, turn on LDAP authentication logging
In case of using LDAP authentication, we can turn on detailed logging by issuing following command.

```
cat > /etc/tower/conf.d/log_ldap.py <<__END__
# Turn on LDAP auth logging
LOGGING['loggers']['django_auth_ldap']['handlers'] = ['console', 'external_logger']
LOGGING['loggers']['django_auth_ldap']['level'] = 'DEBUG'
__END__
```

### Optionally, turn on detail logging for permission checks

```
cat > /etc/tower/conf.d/log_permissions.py <<__END__
# Turn on lots of permissions-related logging.
LOGGING['loggers']['awx.main.access']['level'] = 'DEBUG'
LOGGING['loggers']['awx.main.signals']['level'] = 'DEBUG'
LOGGING['loggers']['awx.api.permissions']['level'] = 'DEBUG'
__END__
```

### Optionally, review access.log and error.log

The `access.log` and `error.log` of nginx server may be useful for getting additional details about users accessing
information and also errors/denials they may be getting. These files are stored in `/var/log/nginx`.

## Appendix A - examples of log messages emitted by Ansible Tower

### Example - log message emitted when user logged-in

```
{
        "logger_name" => "awx.api.generics",
              "level" => "INFO",
         "stack_info" => nil,
               "type" => "logstash",
               "tags" => [
        [0] "_jsonparsefailure"
    ],
         "@timestamp" => 2019-10-02T19:21:01.202Z,
            "message" => "User admin logged in.",
         "tower_uuid" => "647f0aed-6ce0-4875-9fae-7cd4d92594f5",
    "cluster_host_id" => "localhost",
               "host" => "ansible-tower",
           "@version" => "1"
}
```

### Example - log messages emitted when admin creates a new user

```
{
        "logger_name" => "awx.analytics.activity_stream",
              "level" => "INFO",
         "stack_info" => nil,
          "operation" => "create",
               "type" => "logstash",
               "tags" => [
        [0] "_jsonparsefailure"
    ],
         "@timestamp" => 2019-10-02T19:11:05.723Z,
            "message" => "Activity Stream update entry for user",
       "relationship" => "",
            "object1" => "user",
            "object2" => "",
         "tower_uuid" => "647f0aed-6ce0-4875-9fae-7cd4d92594f5",
            "changes" => {
            "password" => "hidden",
                  "id" => 4,
        "is_superuser" => false,
               "email" => "john@acme.com",
            "username" => "john",
          "first_name" => "John",
           "last_name" => "Doe"
    },
               "host" => "ansible-tower",
              "actor" => "admin",
    "cluster_host_id" => "localhost",
           "@version" => "1"
}
{
        "logger_name" => "awx.analytics.activity_stream",
              "level" => "INFO",
         "stack_info" => nil,
          "operation" => "associate",
               "type" => "logstash",
               "tags" => [
        [0] "_jsonparsefailure"
    ],
         "@timestamp" => 2019-10-02T19:11:05.761Z,
            "message" => "Activity Stream update entry for organization",
       "relationship" => "awx.main.models.organization.Organization.member_role",
            "object1" => "organization",
            "object2" => "user",
         "tower_uuid" => "647f0aed-6ce0-4875-9fae-7cd4d92594f5",
            "changes" => {
              "action" => "associate",
          "object2_pk" => 4,
             "object1" => "organization",
             "object2" => "user",
          "object1_pk" => 1,
        "relationship" => "awx.main.models.rbac.Role_members"
    },
               "host" => "ansible-tower",
              "actor" => "admin",
    "cluster_host_id" => "localhost",
           "@version" => "1"
}

```

### Example - log message emitted when admin changes password for a user

```
{
        "logger_name" => "awx.analytics.activity_stream",
              "level" => "INFO",
         "stack_info" => nil,
          "operation" => "update",
               "type" => "logstash",
               "tags" => [
        [0] "_jsonparsefailure"
    ],
         "@timestamp" => 2019-10-02T19:04:43.343Z,
            "message" => "Activity Stream update entry for user",
       "relationship" => "",
            "object1" => "user",
            "object2" => "",
         "tower_uuid" => "647f0aed-6ce0-4875-9fae-7cd4d92594f5",
            "changes" => {
        "password" => [
            [0] "hidden",
            [1] "hidden"
        ]
    },
               "host" => "ansible-tower",
              "actor" => "admin",
    "cluster_host_id" => "localhost",
           "@version" => "1"
}
```
