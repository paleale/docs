---
title: Allocating instances across zones
description: In this article, you will learn about allocating instances across availability zones.
---

# Allocating instances across zones


Allocation of instances across zones depends on the [group scaling type](../scale.md). 

## In manually scaled groups {#fixed-scale}

In manually scaled groups, instances are distributed evenly between zones. 

In accordance with the [deployment policy](../policies/allocation-policy.md), instances are added to each zone sequentially, one at a time. If the list of zones is exhausted, but the desired number of instances is not created, zones are reused.
 
{% note tip %}

We recommend creating an instance group so that its size is a multiple of the number of zones.

In this case, every zone will host the same number of instances, and a [network load balancer](../../../../network-load-balancer/concepts/index.md) (if any) will evenly allocate the load between instances.

{% endnote %}

If the group size is not a multiple of the number of zones, some zones will host less instances than others, but no more than one instance less.

### Examples {#ex-fixed-scale}

If you listed zones in the `[{{ region-id }}-d, {{ region-id }}-a]` order in the YAML specification, and the size of the group is 4, then after allocation, you will have two instances in each zone:


| Step | {{ region-id }}-d | {{ region-id }}-a | Left |
| :-: | :-----------: | :-----------: | :------: |
|  1  |       1       |       0       |     3    |
|  2  |       1       |       1       |     2    |
|  3  |       2       |       1       |     1    |
|  4  |       2       |       2       |     0    |


If you listed zones in the `[{{ region-id }}-d, {{ region-id }}-a]` order in the YAML specification, and the size of the group is 5, then after allocation, you will have three instances in `{{ region-id }}-d` and two in `{{ region-id }}-a`:


| Step | {{ region-id }}-d | {{ region-id }}-a | Left |
| :-: | :-----------: | :-----------: | :------: |
|  1  |       1       |       0       |     4    |
|  2  |       1       |       1       |     3    |
|  3  |       2       |       1       |     2    |
|  4  |       2       |       2       |     1    |
|  5  |       3       |       2       |     0    |


Let's assume you listed three zones in the `[{{ region-id }}-d, {{ region-id }}-a, {{ region-id }}-b]` order in the specification, and the fixed size of the group is 2, which is lower than the number of zones. In this case, one instance will be created in `{{ region-id }}-d`, one in `{{ region-id }}-a`, and none in `{{ region-id }}-b`. **Although we do not recommend such configuration, it is not prohibited**.

## In automatically scaled groups {#auto-scale}

Unlike fixed-size groups, the order of zones in automatically scaled instance groups makes no difference. 

The number of instances in zones is determined by the [automatic scaling](../scale.md#auto-scale) algorithm based on the average utilization and average load per zone. It can change depending on the restrictions set by the user:

* `min_zone_size` (minimum zone size): Zone cannot have fewer instances than specified in `min_zone_size`.
  This restriction allows you to maintain a reserve of instances in the zone in case of a sudden load increase.
  You may specify `min_zone_size=0`. In this case, the zone may be empty.

* `max_size` (maximum size of the whole group): Group cannot have more instances than specified in `max_size` (in total for all zones). 
  By limiting the number of instances in an automatically scaled group you can control your finance costs. 

### Examples {#ex-auto-scale}

**When allocating instances, the `min_zone_size` restriction applies**

Let's assume `min_zone_size` is 2. The automatic scaling algorithm calculated that you need 0 instances in the zone. In this case, you will have 2 instances in the zone, because that is the minimum allowed number of instances in the zone.

**When allocating instances, the `min_zone_size` and `max_size` restrictions apply**

Let's assume `min_zone_size` is 5 and `max_size` is 20. The autoscaling algorithm allocates instances across zones as follows: 15 + 10 + 5.

1. You have 30 instances in total. This is 10 instances more than `max_size`. The algorithm removes instances from each zone until 10 are removed in total. 
  The more instances there are in a zone, the more instances you can remove from it. However, at least 5 instances must remain in every zone.

1. One possible allocation option is 9 + 6 + 5. 
  Six instances are removed from the largest zone (15 before, 9 after), four are removed from the medium zone (10 before, 6 after), and nothing is removed from the smallest group, because it already has the minimum allowable number of instances (5).

## Use cases {#examples}

* [{#T}](../../../tutorials/vm-autoscale/index.md)
* [{#T}](../../../tutorials/vm-scale-scheduled/index.md)
