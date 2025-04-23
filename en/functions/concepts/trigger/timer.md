# Timer that invokes a {{ sf-name }} function

_Timer_ is a [trigger](../trigger/) that calls a {{ sf-name }} [function](../function.md) on a schedule. The schedule is set as a [cron expression](#cron-expression). The cron expression uses [UTC+0](https://en.wikipedia.org/wiki/Coordinated_Universal_Time).

A timer needs a [service account](../../../iam/concepts/users/service-accounts.md) to invoke a function.

For more information about creating a timer, see [{#T}](../../operations/trigger/timer-create.md).

{% include [cron-expression](../../../_includes/functions/cron-expression.md) %}

## Roles required for timers to run correctly {#roles}

* To create a timer, you need a permission for the service account under which the timer runs the operation. This permission comes with the [iam.serviceAccounts.user](../../../iam/security/index.md#iam-serviceAccounts-user) and [editor](../../../iam/roles-reference.md#editor) roles or higher.
* To run a timer, the service account needs the `{{ roles-functions-invoker }}` role for the folder containing the function called by the timer.

Read more about [access management](../../security/index.md).

## Timer message format {#format}

After the trigger is activated, it sends the following message to the function:

{% include [timer-format](../../../_includes/functions/timer-format.md) %}

## Use cases {#examples}

* [{#T}](../../tutorials/data-from-tracker.md)
* [{#T}](../../tutorials/datalens.md)
* [{#T}](../../tutorials/vm-scale-scheduled/console.md)
* [{#T}](../../tutorials/monitoring.md)
* [{#T}](../../tutorials/nodejs-cron-restart-vm.md)
* [{#T}](../../tutorials/regular-launch-datasphere.md)

## See also {#see-also}

* [Timer to run a {{ serverless-containers-name }} container](../../../serverless-containers/concepts/trigger/timer.md)
* [{#T}](../../../api-gateway/concepts/trigger/timer.md)
