# Working with PySpark jobs

[Apache Spark](https://spark.apache.org/) is a distributed processing framework for unstructured and semi-structured data and a part of the Hadoop project ecosystem.

In this section, we provide a simple example that demonstrates how to use [PySpark](https://spark.apache.org/docs/latest/api/python/), the Spark interface for Python, in {{ dataproc-name }}. In the example, we use PySpark to count the number of times each word is seen in a short text.

## Getting started {#before-you-begin}

1. [Create a service account](../../../iam/operations/sa/create.md) with the `dataproc.agent` and `dataproc.provisioner` roles.

1. {% include [basic-before-buckets](../../../_includes/data-processing/tutorials/basic-before-buckets.md) %}

1. [Create a {{ dataproc-name }}](../../../data-proc/operations/cluster-create.md) cluster with the following settings:

    * **{{ ui-key.yacloud.mdb.forms.base_field_environment }}**: `PRODUCTION`
    * **{{ ui-key.yacloud.mdb.forms.config_field_services }}**:
        * `HDFS`
        * `SPARK`
        * `YARN`
    * **{{ ui-key.yacloud.mdb.forms.base_field_service-account }}**: Select the service account you previously created.
    * **{{ ui-key.yacloud.mdb.forms.config_field_bucket }}**: Select a bucket to hold the processing results.

## Create a PySpark job {#create-job}

1. {% include [sample-txt](../../../_includes/data-processing/tutorials/sample-txt.md) %}

1. Upload a file with the Python code of the analysis program:

    1. Copy and save to the `word_count.py` file:


        {% cut "word_count.py" %}

        ```python
        import sys
        from pyspark import SparkConf, SparkContext


        def main():

            if len(sys.argv) != 3:
                print('Usage job.py <input_directory> <output_directory>')
                sys.exit(1)

            in_dir = sys.argv[1]
            out_dir = sys.argv[2]

            conf = SparkConf().setAppName("Word count - PySpark")
            sc = SparkContext(conf=conf)

            text_file = sc.textFile(in_dir)
            counts = text_file.flatMap(lambda line: line.split(" ")) \
                .map(lambda word: (word, 1)) \
                .reduceByKey(lambda a, b: a + b)

            if out_dir.startswith('s3a://'):
                counts.saveAsTextFile(out_dir) 
            else:
                default_fs = sc._jsc.hadoopConfiguration().get('fs.defaultFS')
                counts.saveAsTextFile(default_fs + out_dir)


        if __name__ == "__main__":
            main()
        ```

        {% endcut %}

    1. [Upload](../../../storage/operations/objects/upload) the `word_count.py` file to the source data bucket.

1. [Create a PySpark job](../../../data-proc/operations/jobs-pyspark#create) with the following parameters:

    * **{{ ui-key.yacloud.dataproc.jobs.field_main-python-file }}**: `s3a://<input_data_bucket_name>/word_count.py`
    * **{{ ui-key.yacloud.dataproc.jobs.field_args }}**:

        * `s3a://<input_data_bucket_name>/text.txt`
        * `s3a://<output_bucket_name>/<output_directory>`

1. Wait for the [job status](../../../data-proc/operations/jobs-pyspark.md#get-info) to change to `Done`.

1. [Download from the bucket](../../../storage/operations/objects/download.md) and review the files with the results from the bucket:

    {% cut "part-00000" %}

    ```text
    ('sea', 6)
    ('are', 2)
    ('am', 2)
    ('sure', 2)
    ```

    {% endcut %}

    {% cut "part-00001" %}

    ```text
    ('she', 3)
    ('sells', 3)
    ('shells', 6)
    ('on', 2)
    ('the', 4)
    ('shore', 3)
    ('that', 2)
    ('I', 2)
    ('so', 1)
    ('if', 1)
    ```

    {% endcut %}

{% include [get-logs-info](../../../_includes/data-processing/note-info-get-logs.md) %}

## Delete the resources you created {#clear-out}

{% include [basic-clear-out](../../../_includes/data-processing/tutorials/basic-clear-out.md) %}
