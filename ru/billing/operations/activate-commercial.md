# Активировать платную версию

Активировать платную версию необходимо в течение шестидесяти дней с момента окончания срока действия пробного периода. В противном случае все ресурсы будут автоматически удалены.

{% include [trial-period](../../_includes/trial-period.md) %}

Если за время пробного периода вы израсходовали не весь [стартовый грант](../concepts/bonus-account.md), то оставшуюся сумму можно будет использовать в будущем для оплаты потребленных ресурсов.

Чтобы перейти к использованию платной версии:

{% list tabs group=instructions %}

- {{ billing-interface }} {#billing}
  
  1. {% include [move-to-billing-step](../_includes/move-to-billing-step.md) %}
  1. Выберите платежный аккаунт.
  1. На странице ![image](../../_assets/console-icons/flag.svg) **{{ ui-key.yacloud_billing.billing.account.switch_overview }}** нажмите кнопку **{{ ui-key.yacloud_billing.billing.account.button_billing-payment-action }}**.
  1. Подтвердите переход, для этого еще раз нажмите кнопку **{{ ui-key.yacloud_billing.billing.account.button_billing-payment-action }}**.

{% endlist %}

После активации платной версии [баланс лицевого счета](../concepts/personal-account.md#balance) по умолчанию равен нулю. Мы рекомендуем следить за балансом и [пополнять](../operations/pay-the-bill.md) его до положительного значения.
<br/>Если вы вовремя не пополните баланс и у вас появится задолженность, то использование сервисов {{ yandex-cloud }} может быть приостановлено. Дополнительную информацию см. в разделе [Цикл оплаты для организаций и ИП](../payment/billing-cycle-business.md)