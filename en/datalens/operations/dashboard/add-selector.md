---
title: Adding a selector to a dashboard
description: Follow this guide to add a selector to a dashboard.
---

# Adding a selector to a dashboard


Before adding a selector, make sure you have the `{{ permission-write }}` or `{{ permission-admin }}` permission for the dashboard. For more information, see [{#T}](../../security/manage-access.md).


To add a [selector](../../dashboard/selector.md) to a dashboard:


{% include [datalens-workbooks-collections-select-note](../../../_includes/datalens/operations/datalens-workbooks-collections-select-note.md) %}


1. In the left-hand panel, click ![image](../../../_assets/console-icons/layout-cells-large.svg) **Dashboards** and select the dashboard you need.
1. At the top of the page, click **Edit**.
1. In the panel at the bottom of the page, hold down ![image](../../../_assets/console-icons/sliders.svg) **Selector** and drag it to the required area.

   
   ![image](../../../_assets/datalens/operations/widget-select.png =500x118)


1. Under **Selector settings** choose the source and specify the selector parameters:

   {% list tabs %}

   - Based on dataset

     * **Dataset**: Dataset with data for the selector. You can choose a dataset from the list of objects or provide a link. This is a required field.
     * **Field**: Dataset field with selector values. It can be either a dimension or a measure (for more information, see [{#T}](../../dataset/data-model.md#field)). This is a required field.
     * **Selector type**: Drop-down list, input field, calendar, or checkbox.

       {% note info %}

       * **Calendar** is only available for fields of the `Date` or `Date and time` type.
       * **Checkbox** is only available for fields of the `Boolean` type.
       * Measure selectors can only have the **Input field** type.

       {% endnote %}

     * **Operation**: Comparison operation by which the selector filters the chart values (e.g., **Equal to**, **Greater than**, or **Less than**). If the field is left blank, the selector will filter by the **Equal to** operation by default. The list of available operations depends on the field type. Do not specify an operation if the selector filters the QL chart.
     * **Multiple choice**: Allows choosing several values in the selector. Only available for the **List** selector type.
     * **Range**: Allows setting a time interval in the selector. Only available for the **Calendar** selector type.
     * **Required field**: Sets the selector value as a required parameter. When enabled, `*` is added to the end of the selector name. Not available for the **Checkbox** selector type.
     * **Default value**: Displayed when you first open the dashboard. This is a required field if the **Required field** option is enabled.

     * **Header**: Used to pick a selector when establishing a link with other widgets. By default, when you add a selector, the header text is substituted from the field title in the [dataset](../../dataset/index.md). This is a required field.
       
       Select the header location below: `Left` (default), `Top`, or `Hidden` to hide it from the dashboard. It is not available for the **Checkbox** selector type.

     * **Inner title**: Allows specifying text inside a selector. For example, you can use it to display an operation inside a selector. Not available for the **Checkbox** selector type.
     * **Coloring**: Allows you to highlight important selectors. It is not available for the **Checkbox** selector type.
     * **Tooltip**: This option allows adding a text comment to the selector. By default, with this option enabled, the tooltip text is taken from the field description in the dataset. You can edit the tooltip text using the visual editor or [Markdown](../../dashboard/markdown.md). When you change the dataset field description, the text is not automatically updated.

       When the option is enabled, the ![image](../../../_assets/console-icons/circle-question.svg) icon appears on the dashboard next to the selector. Hover over the icon to bring up the tooltip.

   - Manual input

     * **Field or parameter**: Field name you can use to link the selector with other widgets in the [alias](../../dashboard/link.md#alias) configuration window. This is a required field.

       ![image](../../../_assets/datalens/selector-settings/field-name.png)

     * **Selector type**: Drop-down list, input field, calendar, or checkbox.

       {% note info %}

       * **Calendar** is only available for fields of the `Date` or `Date and time` type.
       * **Checkbox** is only available for fields of the `Boolean` type.
       * Measure selectors can only have the **Input field** type.

       {% endnote %}

     * **Operation**: Comparison operation by which the selector filters the chart values (e.g., **Equal to**, **Greater than**, or **Less than**). If the field is left blank, the selector will filter by the **Equal to** operation by default. The list of available operations depends on the field type. Do not specify an operation if the selector filters the QL chart.
     * **Required field**: Sets the selector value as a required parameter. When enabled, `*` is added to the end of the selector name. Not available for the **Checkbox** selector type.
     * **Multiple choice**: Allows choosing several values in the selector. Only available for the **List** selector type.
     * **Possible values**: List of values to select. Only available for the **List** selector type.
     * **Range**: Allows setting a time interval in the selector. Only available for the **Calendar** selector type.
     * **Time**: Allows specifying time. Only available for the **Calendar** selector type.
     * **Default value**: Displayed when you first open the dashboard. This field must be set for the **List** type; otherwise, no value will be available in the selector. This is a required field if the **Required field** option is enabled.

     * **Header**: Used to pick a selector when establishing a link with other widgets. This is a required field.
       
       Select the header location below: `Left` (default), `Top`, or `Hidden` to hide it from the dashboard. It is not available for the **Checkbox** selector type.

     * **Inner title**: Allows specifying text inside a selector. For example, you can use it to display an operation inside a selector. Not available for the **Checkbox** selector type.
     * **Coloring**: Allows you to highlight important selectors. It is not available for the **Checkbox** selector type.
     * **Tooltip**: This option allows adding a text comment to the selector. By default, with this option enabled, the tooltip text is taken from the field description in the [dataset](../../dataset/index.md). You can change the tooltip text. When you change the dataset field description, the text is not automatically updated.

       When the option is enabled, the ![image](../../../_assets/console-icons/circle-question.svg) icon appears on the dashboard next to the selector. Hover over the icon to bring up the tooltip.

   {% endlist %}

   In [QL charts](../../concepts/chart/ql-charts.md), you can control [selector parameters](../chart/create-sql-chart.md#selector-parameters) from the **Parameters** tab in the chart editing area and use the **Query** tab to specify a variable in the query itself in `not_var{{variable}}` format.

1. (Optional) Add another selector to the widget. To do this, on the left side under **Selectors**, click ![image](../../../_assets/console-icons/plus.svg) **Add selector** and repeat the actions from the previous step. You can also duplicate the new selector or copy it to the clipboard. To do this, under **Selectors**, click ![image](../../../_assets/console-icons/ellipsis.svg) next to the selector and select:

   * **Duplicate**: This will add a new selector with the same parameters to the widget.
   * **Copy to clipboard**: You can paste the selector you copied into a different widget using the **Paste** button in the **Selectors** section or to a dashboard using the **Insert** button on the bottom panel of the dashboard page.

1. (Optional) Click ![image](../../../_assets/console-icons/gear.svg) **Advanced settings** and specify widget settings:

   * **Apply button**: Adds a widget button which applies the values of all selectors in the widget. The selector values are not applied until you click the button.
   * **Reset button**: Adds a widget button which resets the values of all the widget selectors to their defaults.
   * **Auto height**: Sets the automatic height for a widget on the dashboard. Available for widgets with multiple selectors.
   * **Dependent selector autoupdate**: Enables dependent selectors to influence one another before you click the **Apply** button. Available for widgets with multiple selectors and the **Apply button** option enabled.
   * (Optional) For a widget with multiple selectors, customize the selector arrangement. To do this, arrange the selectors as needed by dragging them and adjust the width of each one:

      * `Auto`: Automatic selector width.
      * `%`: Selector width as a percentage of the widget's total width.
      * `px`: Selector width in pixels.

   Click **Save**.

   ![image](../../../_assets/datalens/selector-settings/selector-extending-settings.png)

1. Click **Save**. The widget will be displayed on the dashboard.

{% note warning %}

When adding a selector by a certain field to your dashboard, filters added at the chart level are no longer applied to the dashboard's chart.

{% endnote %}

## Limitations {#restrictions}

* For measure selectors, only one type is available: **Input field**.
* We recommend that you always make your measure selectors independent from other selectors. To do this, set your dashboard's [link](../../dashboard/link.md) type to **Not linked** with other selectors in the **Links** section when editing.
