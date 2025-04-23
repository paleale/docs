#### Quotas {#quotas}

**Type of limit** | **Value**
----- | -----
Maximum message size per [push notification](../notifications/concepts/push.md) | 4 KB

#### Limits {#limits}

**Type of limit** | **Value**
----- | -----
Maximum number of [mobile push notification channels](../notifications/concepts/push.md#mobile-channel) per cloud | 20
Maximum number of [in-browser push notification channels](../notifications/concepts/browser.md) per cloud | 10
Maximum number of [SMS notification](../notifications/concepts/sms.md) channels per cloud | 10
Maximum number of SMS notification channels with a [shared sender](../notifications/concepts/sms.md) per cloud | 1
Maximum number of SMS notification channels with an [individual sender](../notifications/concepts/sms.md#individual-sender) per cloud | 10
Maximum number of [test phone numbers](../notifications/concepts/sms.md#sandbox) per SMS notification channel | 10
Maximum number of SMS messages per cloud^1^ | 100 per month
Maximum message size per push notification | 200 KB
Maximum SMS message length | 1,600 characters (10 segments)
Maximum name length for a push notification channel | 40 characters
Maximum length for user data (`CustomUserData`) per mobile endpoint | 256 characters
Maximum length for a device ID per mobile endpoint | 256 characters
Maximum rate of requests to create or update notification channel attributes per cloud | 30 requests per second
Maximum rate of requests to send an SMS message per cloud | 20 requests per second
Maximum number of SMS messages to verify one test number | 5 per day for one number
Maximum number of SMS messages to verify test numbers per cloud | 20 per day

^1^ Moving forward, the limit on the maximum number of SMS messages per cloud will become a [quota](#quotas). You will be able to increase it by contacting support.