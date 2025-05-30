---
title: _Comparison_ report in {{ speechsense-name }}
---

# _Comparison_ report in {{ speechsense-name }}

The **{{ ui-key.yc-ui-talkanalytics.reports.comparison-key-value }}** report allows you to display several parameters in a single chart and see how they correlate. For example, you can create a report that shows how the flaws in the agent’s speech, e.g., hesitation, poor articulation, or silence, affect the decision not to buy a service or product. You can use system tags as parameters for such a report: `Refused to buy`, `Agent’s hesitation`, `Stop words`, `Filler words`, etc.

## How to build a report {#form}

To generate a **{{ ui-key.yc-ui-talkanalytics.reports.comparison-key-value }}** report, you need to specify these settings:

* [Evaluation parameters](#parameter): The report will reflect the changes in these parameters.
* [Filters](#filters): Filters applied to dialogs in the report.

With the settings configured, you can now [build a report](../../operations/data/manage-reports.md#build-a-comparison-report). It will show the values of several evaluation parameters in a [chart and table](#display).

### Evaluation parameters {#parameter}

_Evaluation parameters_ are those parameters whose value changes you can view in the report. Only numerical parameters are considered. If you select a [tag](../tags.md), its value will be the number of times the tag was assigned to dialogs.

The report considers the total, average, minimum, or maximum value of the evaluation parameter for the selected period. For example, such values may be useful in the following cases:

* Total: How many times the customer asked for a supervisor during the conversation.
* Average: Average dialog duration in seconds.
* Minimum or maximum: Agents with the smallest and largest number of violations.

In the report, you can select one of these evaluation parameter types:

{% include [description-of-parameters](../../../_includes/speechsense/reports/parameters.md) %}

### Filtering in the report {#filters}

{% include notitle [filters](../../../_includes/speechsense/reports/filters.md) %}

## Visualizing and using the report data {#display}

The report provides quantitative agent performance characteristics. You can view the report in the {{ speechsense-name }} web interface in chart and table form or download it in CSV format.

The available **{{ ui-key.yc-ui-talkanalytics.reports.comparison-key-value }}** report formats include:

* **{{ ui-key.yc-ui-talkanalytics.reports.barchart }}**: Allows you to visualize the evaluation parameter values at different points in time and their correlation. Each parameter has its own scale of measurement. To get detailed information about single parameter values, you can click the parameter on the legend, and the chart will display only this parameter. Clicking the legend again will bring all the parameters back to the chart.

    On the chart, you can set the report generation period as well as the data detail level. As a result, you can get values for different time intervals: from an hour to a quarter.

* **{{ ui-key.yc-ui-talkanalytics.reports.table-key-value }}**: Shows the numerical value of the evaluation parameter for the specified period. The values are broken down by the customized data grouping.
* **CSV file**: Contains the same table as in the {{ speechsense-name }} web interface. Use the CSV format to save the report locally.
