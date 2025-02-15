---
title: "Options of grouping-by parsers"
weight:  300
---
<!-- DISCLAIMER: This file is based on the syslog-ng Open Source Edition documentation https://github.com/balabit/syslog-ng-ose-guides/commit/2f4a52ee61d1ea9ad27cb4f3168b95408fddfdf2 and is used under the terms of The syslog-ng Open Source Edition Documentation License. The file has been modified by Axoflow. -->

The `grouping-by` has the following options.


## aggregate() {#grouping-by-parser-aggregate}

|           |             |
| --------- | ----------- |
| Synopsis: | aggregate() |

*Description:* Specifies the message that {{% param "product.abbrev" %}} generates when the context is closed. This option is mandatory.

Note that the `aggregate()` option has access to every message of the context, and has the following options:

  - {{% include-headless "chunk/option-inherit-mode.md" %}}
    
    The default value of `inherit-mode()` is `context`.
    
    For details on the message context, see {{% xref "/chapter-correlating-log-messages/grouping-by-parser/_index.md" %}}.

  - *tags*: Adds the specified tag to the list of tags.

  - *value*: Adds a name-value pair to the generated message. You can include text, macros, template functions, and you can also reference every message of the context. For details on accessing other messages of the context, see {{% xref "/chapter-correlating-log-messages/grouping-by-parser/referencing-previous-messages-grouping-by/_index.md" %}}.



## having() {#grouping-by-parser-having}

|           |          |
| --------- | -------- |
| Synopsis: | having() |

*Description:* Specifies a filter: {{% param "product.abbrev" %}} generates the aggregate message only if the result of the filter expression is true. Note that the `having()` filter has access to every message of the context. For details on accessing other messages of the context, see {{% xref "/chapter-correlating-log-messages/grouping-by-parser/referencing-previous-messages-grouping-by/_index.md" %}}.



## inject-mode() {#grouping-by-parser-inject-mode}

|           |               |
| --------- | ------------- |
| Synopsis: | inject-mode() |

*Description:* By default, the aggregated message that {{% param "product.abbrev" %}} generates is injected into the same place where the `grouping-by()` statement is referenced in the log path. To post the generated message into the `internal()` source instead, use the `inject-mode()` option in the definition of the parser.


### Example: Sending triggered messages to the internal() source

To send the generated messages to the `internal` source, use the `inject-mode("internal")` option:

```shell
   parser p_grouping-by {grouping-by(
        ...
        inject-mode("internal")
    );};
```

To inject the generated messages where the parser is referenced, use the `inject-mode("pass-through")` option:

```shell
   parser p_grouping-by {grouping-by(
        ...
        inject-mode("pass-through")
    );};
```


You can configure the generated message in the `aggregate()` option (see [aggregate()](#grouping-by-parser-aggregate)). You can create an entire message, use macros and values extracted from the original message, and so on.



## key() {#grouping-by-parser-key}

|           |       |
| --------- | ----- |
| Synopsis: | key() |

*Description:* Specifies the key as a template (that is, the name of a name-value pair) that every message must have to be added to the context. The value of the key must be the same for every message of the context. For example, this can be a session-id parsed from firewall messages, and so on.

This is a mandatory option.

{{% alert title="Note" color="info" %}}

Messages that do not have a key will all belong to the same context.

{{% /alert %}} {{% alert title="Note" color="info" %}}

If the value of the key is static (for example, key("PROGRAM") instead of key("$PROGRAM")), all messages will belong to the same context.

{{% /alert %}}

<span id="grouping-by-parser-scope"></span>
{{< include-headless "chunk/option-parser-scope.md" >}}

## sort-key() {#grouping-by-parser-sort-key}

|           |            |
| --------- | ---------- |
| Synopsis: | sort-key() |

*Description:* Allows sorting of the elements before they are aggregated into a context. Use this when entries are not received in order. This works similarly to the SQL ORDER BY keyword.

{{% alert title="Note" color="info" %}}

  - Sorting is done by {{% param "product.abbrev" %}} when the context is about to be closed by trigger() or timeout(), but before {{% param "product.abbrev" %}} evaluates the [having()](#grouping-by-parser-having) option.
  - {{% param "product.abbrev" %}} can slow down if you specify several sort-key macro or template options, for example, `sort-key("${3}${4}")`.

{{% /alert %}}



## timeout() {#grouping-by-parser-timeout}

|           |                      |
| --------- | -------------------- |
| Synopsis: | timeout([seconds]) |

*Description:* Specifies the maximum time to wait for all messages of the context to arrive. If no new message is added to the context during this period, the context is assumed to be complete and {{% param "product.abbrev" %}} generates and sends the triggered message (specified in the [aggregate()](#grouping-by-parser-aggregate) option), and clears the context. If a new message is added to the context, the timeout period is restarted.

This option is mandatory, and its value must be equal to or greater than `1`.



## trigger() {#grouping-by-parser-trigger}

|           |           |
| --------- | --------- |
| Synopsis: | trigger() |

*Description:* A filter that specifies the final message of the context. If the filter matches the incoming message, {{% param "product.abbrev" %}} generates and sends the triggered message (specified in the [aggregate()](#grouping-by-parser-aggregate) option), and clears the context.



## where() {#grouping-by-parser-where}

|           |         |
| --------- | ------- |
| Synopsis: | where() |

*Description:* Specifies a filter condition. Messages not matching the filter will not be added to the context. Note that the `where()` filter has access only to the current message.

