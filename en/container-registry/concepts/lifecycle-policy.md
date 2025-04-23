# Docker image lifecycle policy

A [Docker image](docker-image.md) lifecycle policy lets you set [rules](#lifecycle-rules) for deleting Docker images automatically.

You can [configure a lifecycle policy](../operations/lifecycle-policy/lifecycle-policy-create.md) using the [{{ yandex-cloud }} CLI](../../cli/).

{% note warning %}

You can only set a lifecycle policy for a [repository](repository.md). The policy applies to Docker images whose names match the repository name exactly. There is no support for prefix matching. You cannot set a policy for a repository group, [registry](registry.md), [folder](../../resource-manager/concepts/resources-hierarchy.md#folder), or [cloud](../../resource-manager/concepts/resources-hierarchy.md#cloud).

{% endnote %}

## Lifecycle policy statuses {#status}

A lifecycle policy can have the following statuses:
* `ACTIVE`: The policy is active and regularly deletes Docker images according to the rules you set.
* `DISABLED`: The policy is disabled and does not delete Docker images from the repository. You can use policies in this status to create and test rules.

{% note info %}

The default policy is created with the `DISABLED` status. For information on activating a policy, see [Updating a lifecycle policy status](../operations/lifecycle-policy/lifecycle-policy-update.md#update-status).

{% endnote %}

A repository can only have one active policy and several disabled ones. You can disable the active policy at any time.

You can perform [dry runs](../operations/lifecycle-policy/lifecycle-policy-dry-run.md) to check which Docker images will be deleted according to the rules of the current policy.

## Lifecycle policy rules {#lifecycle-rules}

Prior to deletion, Docker images are first filtered by tag and then checked against lifecycle policy rules. If an image matches several rules at the same time, the [rule conflict](#resolve) is automatically resolved.

{% note warning %}

To create a rule, specify at least one tag-based filter and set at least one delete condition.

{% endnote %}

When [creating a lifecycle policy](../operations/lifecycle-policy/lifecycle-policy-create.md), you can place rules in a separate JSON file. Use the parameters below to configure lifecycle policy rules:
1. Filtering Docker images by tag:
   * `tag_regexp`: Tag to specify a filter as a regular expression.

     Use cases of `tag_regexp`:
     * `.*`: All images with tags.
     * `prefix.*`: Images with tags that start with `prefix`.
   * `untagged`: Tag to apply the rule to untagged Docker images.
1. Conditions for deleting Docker images:
   * `expire_period`: Period of time that must pass after creating a Docker image for it to satisfy the automatic deletion criteria. It must be a multiple of 24 hours.
   * `retained_top`: Number of Docker images (meeting the specified tag-based filter conditions) to be retained even if the period set in `expire_period` has already expired.

#### Example of a JSON file with rules {#example}

The `Test` rule deletes all images that meet the following conditions:
* Tag starts with `test`.
* Older than 80 days.

This rule will keep 20 images.

The `Untagged` rule deletes all images that meet the following conditions:
* No tags.
* Older than 48 hours.

```json
[
  {
    "description": "Test",
    "tag_regexp": "test.*",
    "expire_period": "80d",
    "retained_top": 20
  },
  {
    "description": "Untagged",
    "untagged": true,
    "expire_period": "48h"
  }
]
```

Where:
* `description`: Description of the policy rule.
* `tag_regexp`: Docker image tag for filtering. The `test.*` regular expression for `tag_regexp` retrieves all images with tags starting with `test`.
* `untagged`: Flag indicating that the rule applies to Docker images without tags.
* `expire_period`: Time after which the lifecycle policy may apply to the Docker image. This parameter comes as a number followed by a unit of measurement: `s`, `m`, `h`, or `d` (seconds, minutes, hours, or days). `expire_period` must be a multiple of 24 hours.
* `retained_top`: Number of Docker images that are not deleted even if they match the rule.

## Resolving rule conflicts {#resolve}

* If a Docker image filtered by tag only falls under a single delete rule, it is deleted according to this rule's settings.
* If a Docker image filtered by tag falls under several conflicting rules, it is deleted only if all the rules require it. If there is at least one rule saying that a Docker image shouldn't be deleted, the image is retained.

## Use cases {#examples}

* [{#T}](../tutorials/image-storage.md)
