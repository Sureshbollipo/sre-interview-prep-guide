# AWS Data Engineer Associate (DEA-C01) — Practice Set (Q01–Q25)

**Mixed order** • **Real exam difficulty** • **Full-length scenarios** • **Detailed explanations**

Aligned to DEA-C01 domains: ingestion & integration, transformation & preparation, storage & modeling, security & governance, monitoring & operations.

---

## Question1

A retail company lands daily CSV files from stores into s3://retail-lake/raw/sales/date=YYYY-MM-DD/. A Glue crawler updates a Glue Catalog table. A Glue Spark ETL job converts to Parquet and writes to s3://retail-lake/curated/sales/ and then COPYs into Redshift. Yesterday’s partition appeared in the Catalog, but the ETL job skipped it. You must ingest it without duplicating prior partitions.

- A. Disable the crawler and re-create the table
- B. Disable Glue Job Bookmarks and re-run the job once
- C. Increase DPU and worker type for the job
- D. Run VACUUM on Redshift then re-run the job

✅ Answer: B
Explanation: Glue Job Bookmarks track processed dat- A. When a new partition is added, the job may skip it if bookmarks consider it processe- D. Temporarily disabling bookmarks (or resetting) allows one-time reprocessing of the missing partition without duplicating earlier ones. VACUUM affects table maintenance, not ingest control. Increasing DPUs does not change bookmark logi- C. Recreating the table is unnecessary and risky for lineage.


---

## Question2

A startup streams mobile telemetry to Kinesis Data Streams with a hot partition key because many events use the same device I- D. Consumers lag and throttling occurs. They need a change that preserves ability to group by device later while preventing shard hotspots.

- A. Increase stream retention period to 7 days
- B. Randomize the partition key completely
- C. Add a hash suffix (e.g., deviceId#modN) to the partition key
- D. Use Kinesis Data Firehose instead of Streams

✅ Answer: C
Explanation: Hot shards occur when too many records share a partition key. Key sharding (hash-suffix bucketing like deviceId#00…#NN) spreads writes across shards while allowing downstream regrouping by stripping the suffix. Full randomization loses grouping semantics. Retention doesn’t fix write throttling. Firehose can still buffer and won’t solve upstream hot key distribution.


---

## Question3

A bank is migrating an on-prem Oracle OLTP database to an S3 data lake for analytics. DMS is configured for Full Load + CDC with S3 target in Parquet. The team reports growing CDC lag during peak hours. They want the most effective action to reduce replication latency.

- A. Switch S3 output to JSON for faster writes
- B. Increase the DMS replication instance size/class
- C. Disable table and column mappings
- D. Reduce S3 multipart upload threshold

✅ Answer: B
Explanation: CDC throughput is primarily constrained by the replication instance CPU, memory, and I/O. Scaling it up usually reduces lag. JSON is larger than Parquet and increases scan/storage costs. Table mappings are needed for control; removing them is counterproductive. Multipart thresholds have marginal impact compared to compute limits.


---

## Question4

Analysts query S3 JSON logs with Athen- A. Queries are slow and expensive because they scan hundreds of GBs. You must improve performance and cost with minimal operational overhead.

- A. Convert data to Parquet and partition by date
- B. Increase Athena workgroup bytes scanned quota
- C. Use Glue Schema Registry with JSON
- D. Store JSON.gz files but keep the same table definition

✅ Answer: A
Explanation: Columnar formats like Parquet plus partitioning (e.g., date=) drastically reduce bytes scanned via column and partition pruning, lowering cost and improving spee- D. Quotas don’t speed queries. Schema Registry helps streaming schemas, not scan volume. Gzipping JSON reduces storage, not scan bytes inside a columnar-aware engine.


---

## Question5

You orchestrate a nightly pipeline: Crawler → Glue ETL Jobs (multiple steps) → Data Quality step → Redshift COPY. You need built-in dependency chaining, retries, and centralized run history without building custom Step Functions.

- A. Glue Triggers only
- B. Glue Workflows
- C. CloudWatch Events + Lambda
- D. Step Functions with custom SDK integrations

✅ Answer: B
Explanation: Glue Workflows provide native orchestration for multiple Glue jobs, crawlers, and triggers, with dependency graphs, retries, and run lineage/history. Triggers alone are limite- D. Step Functions would work but adds extra plumbing; the ## Question prefers Glue-native solution.


---

## Question6

A large Redshift fact table is frequently joined to a customer dimension using customer_id and filtered by the same column. You observe high data redistribution during joins and poor performance.

- A. DISTSTYLE EVEN; SORTKEY(customer_id)
- B. DISTKEY(customer_id); SORTKEY(customer_id)
- C. DISTSTYLE ALL; SORTKEY(date)
- D. DISTSTYLE AUTO; SORTKEY AUTO

✅ Answer: B
Explanation: Choosing DISTKEY(customer_id) colocates rows on the same slices for joins on that key, minimizing shuffles. Pairing with a SORTKEY on the same column improves locality for filter and join. EVEN ignores the key. ALL is for small dimensions, not large facts. AUTO may help, but explicit, known-access-pattern tuning is superior here.


---

## Question7

A compliance team mandates column-level access control for PII columns in shared tables across multiple analytics accounts. Data is stored in S3, queried via Athena and Redshift Spectrum. You need minimal custom code.

- A. Use S3 bucket policies with object-level permissions per column
- B. Use Lake Formation with column-level grants on Catalog tables
- C. Create separate S3 buckets for masked and unmasked copies
- D. Enforce column filters in application SQL

✅ Answer: B
Explanation: Lake Formation provides fine-grained table/column permissions integrated with Glue Catalog and enforced by Athena/Redshift Spectrum. S3 permissions don’t understand column semantics. Multiple buckets multiply management overhea- D. Application-level filters are brittle and not centrally governed.


---

## Question8

A Glue Spark job writes thousands of tiny Parquet files (5–50 KB) per partition, causing slow Athena queries and high planning overhea- D. You need to reduce small files efficiently.

- A. Use coalesce() or repartition() in the job before writing
- B. Increase number of workers to parallelize more
- C. Switch output format to JSON
- D. Trigger a crawler after every write

✅ Answer: A
Explanation: File compaction through repartition()/coalesce() reduces the number of output files per partition, improving query performance and reducing metadata pressure. More workers create even more small files. JSON worsens scan efficiency. Crawlers don’t merge files.


---

## Question9

You built a Kinesis Data Analytics (Flink) app to aggregate clickstream events and write results to S3 via Firehose. During spikes, the app backpressures and lags. You want to reduce end-to-end latency with minimal ops.

- A. Increase KDA parallelism and enable auto-scaling
- B. Switch to Streams consumers with enhanced fan-out
- C. Increase Firehose buffer size to 128 MB
- D. Reduce Kinesis retention from 24h to 12h

✅ Answer: A
Explanation: KDA Flink supports parallelism and auto-scaling to handle spikes without manual shard management. Enhanced fan-out helps reader throughput, not Flink operator capacity. Larger Firehose buffers increase latency. Retention doesn’t address processing lag.


---

## Question10

Redshift external schema (Spectrum) points to a Glue table over s3://datalake/curated/events/. New partitions arrive hourly, but queries don’t see them automatically.

- A. Run MSCK REPAIR TABLE or add partitions via ALTER TABLE
- B. Run Redshift VACUUM REINDEX
- C. Enable COPY with MANIFEST
- D. Increase Redshift WLM queue slots

✅ Answer: A
Explanation: Spectrum relies on Glue Catalog metadat- A. New partitions must be added to the Catalog (MSCK or ALTER TABLE ADD PARTITION) for queries to see them. VACUUM and WLM are internal Redshift maintenance, not external metadata syn- C. COPY is for internal tables.


---

## Question11

A financial firm needs near real-time (< 60 seconds) fraud signals from card swipes, persisted to S3 and queryable in Athena for ops dashboards. The solution should be serverless and low-ops.

- A. Firehose → S3 → Athena scheduled queries every 5 min
- B. Streams → Lambda transforms → Firehose → S3 → Athena
- C. MSK → custom consumer → EMR → S3 → Athena hourly
- D. Streams → KDA SQL → Firehose → S3 → Athena

✅ Answer: D
Explanation: Streams + Kinesis Data Analytics (SQL) provides near real-time processing; Firehose delivers to S3; Athena queries data with minimal ops. Lambda alone can struggle with stateful aggregations and ordering. EMR hourly misses the SL- A. Firehose alone is usually 1–5 minutes latency.


---

## Question12

A DMS task writes to S3 with the structure s3://landing/db/table/year=YYYY/month=MM/day=DD/. Analysts see duplicates after a task restart. You must prevent duplicates without manual de-dup SQL.

- A. Use S3 object overwrite mode
- B. Enable task resume with cached checkpoint/LSN and ensure Full Load + CDC separation
- C. Switch to JSON to identify duplicates
- D. Use lifecycle rules to expire older files

✅ Answer: B
Explanation: Proper checkpointing and resume ensure CDC continues from the last committed LSN/SCN and avoids re-emitting full-load dat- A. DMS best practice separates full load output from CDC paths. Overwrite mode risks data loss. JSON doesn’t solve duplication. Lifecycle rules don’t prevent dupes.


---

## Question13

You query a year of logs with Athen- A. Only November is neede- D. The table is partitioned by year and month. The query still scans most data.

- A. Use WHERE month='11' and year='2025' on partition columns
- B. Use LIMIT 1000
- C. Use CTAS to materialize a subset
- D. Change SerDe to lazy simple

✅ Answer: A
Explanation: Partition pruning only works when filter predicates reference the partition columns exactly (and with correct types). LIMIT doesn’t reduce scan bytes. CTAS helps reuse but still scans once. SerDe choice doesn’t perform pruning.


---

## Question14

An EMR Spark job reading from S3 sometimes fails with “file not found” immediately after another job writes results to the same prefix. You need stronger read-after-write consistency.

- A. Use HDFS instead of S3
- B. Enable EMRFS consistent view or add a short retry/backoff
- C. Increase Spark executor memory
- D. Use Redshift Spectrum instead

✅ Answer: B
Explanation: S3 metadata is eventually consistent in some cases. EMRFS consistent view (with DynamoDB) or retry/backoff patterns mitigate timing issues after writes. HDFS loses durability and elasticity. Memory won’t fix consistency. Spectrum doesn’t address the job’s own output-read race.


---

## Question15

A Glue job connects to a private Redshift cluster in a VP- C. The job fails to connect; the subnet has no internet, and there’s no NAT. You must keep traffic private.

- A. Add a NAT Gateway for outbound internet
- B. Configure VPC endpoints (interface endpoint for Redshift) and place the job in the same VPC/subnets with proper SGs
- C. Open Redshift to 0.0.0.0/0
- D. Run the job outside the VPC

✅ Answer: B
Explanation: For private connectivity, run Glue in the same VPC with appropriate security groups and use VPC endpoints. NAT introduces internet egress. Public access is insecure. Running outside VPC can break private routing.


---

## Question 16

Your organization wants to minimize Athena cost while keeping flexibility. Data is an evolving semi-structured JSON stream. What storage strategy best balances cost and schema evolution?

- A. Store raw JSON only
- B. Convert to Parquet with schema evolution support and retain raw JSON in a separate “archive” prefix
- C. Convert to CSV and delete raw
- D. Use Avro only

✅ Answer: B
Explanation: Parquet drastically reduces scan cost; keeping a raw JSON archive preserves replay/evolution. CSV lacks types. Avro is row-oriented; Parquet is generally better for analytics scans.


---

## Question 17

Security requires auditability of who accessed which S3 objects through Athena/Redshift Spectrum. You must provide object-level access logs and query lineage with minimal custom code.

- A. Enable S3 server access logs only
- B. Enable CloudTrail data events on the S3 buckets and use Lake Formation permissions
- C. Use only IAM Access Analyzer
- D. Build a custom proxy to log accesses

✅ Answer: B
Explanation: CloudTrail data events capture object-level access logs. With Lake Formation, you control fine-grained access via the Catalog, providing both governance and auditable trails. S3 access logs lack detailed API context. Proxies add complexity.


---

## Question 18

You are evaluating ingestion for RDBMS → Redshift with minimal downtime and ability to keep warehouse updated.

- A. Use Glue JDBC job to read tables daily into Redshift
- B. Use DMS Full Load + CDC into Redshift with optimized commit settings
- C. Export CSV nightly to S3 and COPY
- D. Use Athena Federated Query

✅ Answer: B
Explanation: DMS Full Load + CDC is designed for minimal downtime migrations and near-real-time updates to Redshift. JDBC jobs and nightly exports are batch and miss low latency. Federated Query is ad-hoc, not sustained ingestion.


---

## Question 19

A team uses Firehose to S3 with Lambda transformation. During peak, delivery latency increases to ~6 minutes. They want < 1 minute without turning off the transform.

- A. Reduce Firehose buffer interval to ~60 seconds and optimize Lambda to process batches quickly
- B. Increase Firehose retry duration
- C. Switch to Streams on-demand capacity but keep Firehose
- D. Increase S3 multipart threshold

✅ Answer: A
Explanation: Firehose buffers by time or size; reducing buffer interval lowers latency. Ensure Lambda is performant on batch payloads. Retries don’t help steady-state latency. Streams on-demand is a different pattern and doesn’t lower Firehose buffering.


---

## Question 20

A Redshift table is sorted by order_date. Most queries are last-30-days with range filters on that column. Performance is degrading over time.

- A. Change sort key to customer_id
- B. Keep order_date as SORTKEY and run VACUUM/ANALYZE or use AUTO table tuning
- C. Use DISTSTYLE ALL
- D. Switch to EVEN distribution and remove sort key

✅ Answer: B
Explanation: Time-range filters benefit from a date sort key. Over time, table fragmentation grows; VACUUM/ANALYZE or AUTO tuning helps maintain performance. Changing keys arbitrarily harms typical filters. DISTSTYLE is unrelated to sort locality.


---

## Question 21

An Athena table over Parquet is returning NULL for a new column introduced last week. The crawler ran.

- A. Recreate the table
- B. Ensure crawler has “Update existing schema” and partition projection or repair partitions
- C. Change compression codec
- D. Use CTAS to copy data

✅ Answer: B
Explanation: Schema evolution requires the crawler to update the table schem- A. If partitions are added manually, use MSCK REPAIR or partition projection. Recreating the table is heavy-hande- D. Compression isn’t the cause.


---

## Question 22

A regulated enterprise needs cross-account sharing of curated datasets with column masking for PII, queryable via Athena and Redshift Spectrum. The solution should be centrally governed.

- A. Share S3 bucket with bucket policies
- B. Use Lake Formation Resource Links + column-level grants to external accounts
- C. Copy data to each account and apply local IAM policies
- D. Share Glue Catalog with no LF

✅ Answer: B
Explanation: LF Resource Links support cross-account sharing of Catalog metadata with column-level governance. S3-only sharing lacks column semantics. Copying multiplies storage and governance work. Catalog-only sharing misses enforcement.


---

## Question 23

A Glue job performing a large join spills to disk and fails with executor memory errors. You must stabilize it quickly with minimal code change.

- A. Increase worker type (e.g., G.2X) and DPUs; enable job bookmarking for incremental runs
- B. Switch to JSON output
- C. Remove partitioning
- D. Use Python shell job

✅ Answer: A
Explanation: Increasing memory/compute and enabling incremental processing reduces join pressure per run. The other options don’t address memory and scale. Python shell doesn’t run Spark.


---

## Question 24

Analysts want to query S3 data from Redshift without loading, but they also want to join with internal Redshift fact tables and apply LF permissions.

- A. Use Redshift Spectrum with external schemas tied to Glue Catalog managed by Lake Formation
- B. Build external tables directly in Redshift with no Catalog
- C. Export Redshift tables to S3 and use Athena only
- D. Use COPY to load all data first

✅ Answer: A
Explanation: Spectrum enables external tables over S3 and joins with internal tables, while LF can govern access on the Catalog. Direct external tables with no Catalog lack governance integration. COPY defeats “no load” requirement.


---

## Question 25

A pipeline: DMS → S3 (Parquet) → Glue ETL → Redshift. After a schema change in source, Glue job fails due to field mismatch. You need a resilient approach with minimal downtime.

- A. Force Glue to ignore schema drift
- B. Use Glue DynamicFrames with ResolveChoice and catalog updates via crawler before ETL
- C. Switch to JSON in DMS
- D. Pause CDC until manual schema migration completes

✅ Answer: B
Explanation: DynamicFrames handle semi-structured and evolving schemas; ResolveChoice can coerce/rename fields. Running the crawler first updates the Catalog so the ETL sees new schem- A. JSON increases size and cost. Pausing CDC adds downtime.

---

## Question 26

A logistics firm uses Kinesis Data Streams to ingest GPS data from delivery trucks. The data is processed by a Lambda consumer that enriches and stores it in S3. During peak hours, Lambda lags because multiple trucks send updates using the same truck_id partition key.

How can you reduce consumer lag and avoid hot partitions?

- A. Add more shards to the stream
- B. Randomize partition key by appending a hash or suffix to truck_id
- C. Use Kinesis Firehose instead of Streams
- D. Increase Lambda memory size

✅ Answer: B
Explanation: Hot shards occur when a few keys dominate. Hash-suffix partitioning (e.g., truck_id#00–#09) evenly distributes records across shards while preserving grouping capability.
Adding shards alone doesn’t help if partition key remains skewe- D. Firehose is for delivery, not custom per-record logi- C. Memory doesn’t fix ingestion imbalance.


---

## Question 27

You manage a data lake on S3 governed by Lake Formation. An analyst in another AWS account gets “Access Denied” while querying shared tables in Athena.

What must you do to fix this cross-account sharing issue?

- A. Attach an IAM policy granting S3 read access to the analyst’s role
- B. Create a Lake Formation Resource Link and grant LF table-level permissions to the external account
- C. Share the Glue Data Catalog directly via Resource Access Manager (RAM)
- D. Enable S3 Object Ownership = Bucket Owner Preferred

✅ Answer: B
Explanation: Resource Links in Lake Formation allow cross-account metadata sharing and permission management. LF grants are required for Athena queries, since LF enforces access beyond IAM/S3 policies.
RAM sharing of the Catalog alone does not propagate fine-grained permissions.


---

## Question 28

A Glue ETL job joins two massive datasets from S3 (each several hundred GBs). The job runs out of memory and fails. You must fix this without rewriting the logic.

- A. Increase worker type to G.2X and use bookmark-based incremental load
- B. Enable job retries and smaller partitions
- C. Convert data to CSV
- D. Split job into multiple Spark stages manually

✅ Answer: A
Explanation: For memory-heavy joins, increase DPU capacity (G.2X) for higher memory and CPU. Enabling incremental bookmarks can reduce workload size per run. Converting formats or splitting manually adds complexity.


---

## Question 29

A DMS task replicating from RDS PostgreSQL to S3 Parquet shows verification failures on certain tables. Review shows schema changes occurred on source mid-task.

What should you do to avoid this in future migrations?

- A. Enable DMS table mapping with column filtering and enable validation after full load
- B. Pause CDC before schema changes, then resume after DMS refreshes metadata
- C. Enable Glue crawler auto-updates
- D. Restart replication instance after schema changes

✅ Answer: B
Explanation: DMS doesn’t auto-refresh source schema changes. Pausing CDC ensures metadata alignment. After DMS refresh, resume with updated mappings. Schema drift during CDC causes validation mismatches.


---

## Question 30

Your Redshift queries show poor performance during daily ETL loads. Most queries run concurrently. You want to maintain consistent performance without changing SQL.

- A. Add more nodes to the cluster
- B. Enable Concurrency Scaling
- C. Use Spectrum for external queries
- D. Increase WLM queue slots to 20

✅ Answer: B
Explanation: Concurrency Scaling adds transient capacity for high workloads automatically, preserving query performance without resizing cluster or SQL rewrites. Increasing slots may cause resource contention.


---

## Question 31

A company uses Athena for ad-hoc analysis. Users complain queries take minutes even for small date ranges. The data is Parquet but not partitioned.

- A. Partition data by date in S3 and update Glue Catalog
- B. Switch to JSON format
- C. Increase Athena memory
- D. Use CTAS to pre-materialize subsets daily

✅ Answer: A
Explanation: Lack of partitioning causes full-scan behavior even on Parquet. Partitioning enables predicate pushdown, reducing scan bytes and improving spee- D. JSON worsens performance.


---

## Question 32

A Glue crawler takes over 4 hours to complete scanning s3://data-landing/, which has thousands of nested folders and small files.

- A. Limit crawler depth and sampling percentage
- B. Convert to Avro
- C. Increase DPUs for crawler
- D. Use an external Hive metastore

✅ Answer: A
Explanation: Reducing depth and sample size optimizes crawler time. Crawlers traverse folder hierarchies recursively, so limiting scope shortens runtime. Increasing DPUs doesn’t parallelize crawling.


---

## Question 33

A DMS Full Load + CDC task to Redshift experiences continuous lag during CDC.
You must fix it with minimal downtime.

- A. Increase replication instance class and split large tables into multiple tasks
- B. Disable multi-AZ
- C. Switch to S3 target
- D. Use Batch Apply mode

✅ Answer: A
Explanation: Splitting table groups and scaling up the instance parallelizes load and reduces latency. Disabling multi-AZ risks H- A. S3 target is indirect. Batch Apply adds delay.


---

## Question 34

An Athena query over Parquet fails with: “HIVE_CANNOT_OPEN_SPLIT: file too large”.

- A. Split Parquet files into smaller chunks (<128 MB)
- B. Increase worker concurrency in Athena
- C. Recreate table
- D. Change file compression

✅ Answer: A
Explanation: Athena parallelizes per-file splits. Large monolithic files exceed parallel read thresholds, causing open-split errors. Optimal Parquet size ≈ 64–128 MB.


---

## Question 35

An EMR job writing to S3 occasionally re-reads incomplete data from a previous step.

- A. Enable EMRFS Consistent View backed by DynamoDB
- B. Switch to HDFS
- C. Add retry in code
- D. Enable bucket versioning

✅ Answer: A
Explanation: S3 eventual consistency can cause stale reads. EMRFS Consistent View adds metadata tracking for read-after-write consistency using DynamoDB.


---

## Question 36

A company uses DMS to replicate from RDS MySQL to S3. After failover, replication stops automatically and requires manual restart.

- A. Enable Multi-AZ for the DMS replication instance
- B. Use enhanced logging
- C. Increase retention
- D. Create another DMS task

✅ Answer: A
Explanation: Multi-AZ provides high availability; failover happens automatically, continuing replication without manual intervention.


---

## Question 37

Your Glue job triggers automatically when new data lands in S3. However, sometimes multiple invocations overlap.

- A. Enable Job concurrency = 1 in Glue job settings
- B. Use EventBridge schedule
- C. Add delay to trigger
- D. Switch to on-demand mode only

✅ Answer: A
Explanation: Setting concurrency=1 ensures a new job run won’t start while a prior one is still executing.


---

## Question 38

A Kinesis Firehose delivering to Redshift fails intermittently with COPY errors.

- A. Check IAM role for COPY and S3 read permissions
- B. Increase retry duration
- C. Add more shards
- D. Switch to Streams

✅ Answer: A
Explanation: Firehose performs COPY from its S3 buffer to Redshift. Missing S3 or Redshift permissions cause delivery failures.


---

## Question 39

A Glue job processes a 500 GB dataset and repeatedly fails with ExecutorLostFailure.

- A. Increase worker type and DPUs
- B. Use Python shell job
- C. Disable job bookmark
- D. Change compression codec

✅ Answer: A
Explanation: ExecutorLostFailure occurs due to resource exhaustion. Scaling workers (G.2X, G.4X) provides more executors and memory.


---

## Question 40

Athena queries return incorrect data because analysts reused old tables after data schema changes.

- A. Use Glue crawler with “update schema” enabled and enable partition projection
- B. Manually edit schema
- C. Drop and recreate table each time
- D. Convert to CSV

✅ Answer: A
Explanation: Glue crawler auto-detects schema drift when update flag is enable- D. Partition projection prevents over-reliance on static metadata.


---

## Question 41

An EMR Spark job with Spot instances frequently fails mid-job due to interruptions.

- A. Use On-Demand for core nodes, Spot only for task nodes
- B. Use Spot for master node too
- C. Add retries
- D. Lower Spot bid

✅ Answer: A
Explanation: Core nodes store HDFS metadata—must be stable. Spot tasks can be preemptible.


---

## Question 42

Lake Formation permissions not taking effect; users still access S3 data directly.

- A. Disable direct S3 bucket access and enforce access via LF
- B. Add S3 ACLs for users
- C. Enable IAM passthrough
- D. Share dataset via RAM

✅ Answer: A
Explanation: To enforce Lake Formation governance, S3 direct access must be blocked; LF must be the single authorization layer.


---

## Question 43

Redshift COPY fails due to missing IAM permissions.

- A. Grant S3:GetObject and S3:ListBucket to Redshift IAM role
- B. Add Glue:GetTable
- C. Enable Enhanced VPC Routing
- D. Use Manifest files

✅ Answer: A
Explanation: COPY reads from S3; requires read permissions on source bucket.


---

## Question 44

Kinesis Streams consumers process old records again after restart.

- A. Implement checkpointing (sequence numbers)
- B. Reduce retention
- C. Increase shards
- D. Enable enhanced fan-out

✅ Answer: A
Explanation: Without checkpointing, consumers reprocess from trim horizon. Checkpointing ensures exactly-once semantics.


---

## Question 45

Athena query cost is high even with Parquet.

- A. Filter partitions and compress Parquet files further
- B. Use LIMIT
- C. Change to CSV
- D. Increase concurrency

✅ Answer: A
Explanation: Even with Parquet, if partition filters aren’t used, Athena scans entire dataset. Compression further reduces scan size.


---
## Question 46

A retail company wants to query 5 TB of S3 Parquet data from Redshift without loading it. They also need to join it with an existing Redshift fact table.
What is the most cost-efficient option?

- A. Use COPY to load the data into Redshift tables
- B. Use Redshift Spectrum with an external schema linked to the Glue Catalog
- C. Export Redshift data to S3 and use Athena only
- D. Use DMS to migrate S3 data into Redshift

✅ Answer: B
Explanation: Spectrum lets Redshift query S3 data directly using external tables registered in the Glue Catalog, avoiding load and storage cost. COPY loads duplicate dat- A. Athena cannot join internal Redshift tables.


---

## Question 47

A DMS task writes raw CDC data into S3 as uncompressed CSV, producing high storage cost and slow Athena queries.
How can you reduce both cost and latency?

- A. Enable GZIP on CSV files
- B. Use Parquet with Snappy compression as the DMS target format
- C. Use JSON output for flexibility
- D. Write to S3 Glacier

✅ Answer: B
Explanation: Columnar Parquet with Snappy drastically reduces size and improves Athena/Glue scan efficiency via predicate pushdown. CSV.GZIP compresses but remains row-oriented.


---

## Question 48

A Glue crawler fails to detect files stored in nested directories like s3://data/year=2025/month=11/day=22/.
What change fixes this?

- A. Increase crawler depth to 3
- B. Change table to dynamic partitions
- C. Enable schema inference
- D. Use partition projection

✅ Answer: A
Explanation: The crawler depth defines folder traversal levels. Default = 1; increase it to read deeper hierarchical partitions.


---

## Question 49

A Firehose stream delivering to Redshift fails with “Access Denied” errors.
What is the first thing to verify?

- A. Redshift WLM queue size
- B. IAM role permissions for COPY and S3:GetObject
- C. Enhanced VPC routing
- D. Number of shards in stream

✅ Answer: B
Explanation: Firehose performs a Redshift COPY from its S3 buffer. The attached role must allow COPY, S3:GetObject, and S3:ListBucket. WLM or routing are unrelated to authorization.


---

## Question 50

You must automate a daily Athena query that summarizes yesterday’s data and stores results back in S3.
What is the simplest, serverless solution?

- A. Run it manually in Console
- B. Schedule Athena query via EventBridge triggering a Lambda that runs the query API
- C. Use an EMR cluster
- D. Create an EC2 cron job

✅ Answer: B
Explanation: EventBridge + Lambda triggers Athena’s StartQueryExecution API on a schedule, producing results to S3—completely serverless. EMR/EC2 add cost and ops.


---

## Question 51

A Glue Spark job repeatedly fails with ExecutorLostFailure during heavy shuffles.
Which fix is most appropriate?

- A. Increase DPUs and worker type to G.2X
- B. Use Python shell job
- C. Disable job bookmarks
- D. Switch output to CSV

✅ Answer: A
Explanation: ExecutorLostFailure means executors ran out of memory or die- D. Scaling DPUs and memory addresses resource limits. Other options don’t address compute shortages.


---

## Question 52

Lake Formation permissions appear ignored; users still access S3 data directly.
How do you enforce LF governance?

- A. Disable direct S3 access and enforce via LF permission model
- B. Add bucket ACLs for all users
- C. Use IAM only
- D. Enable CloudTrail

✅ Answer: A
Explanation: Lake Formation must be the single authorization layer. Block direct bucket access so all reads/writes go through LF-authorized paths (Athena/Redshift Spectrum).


---

## Question 53

A DMS CDC task stops replicating after the source RDS instance restarts.
How can you ensure automatic continuation?

- A. Enable task resume with cached checkpoint LSN
- B. Change target type
- C. Enable logging
- D. Increase replication instance size

✅ Answer: A
Explanation: DMS can resume CDC from last committed checkpoint LSN; enabling resume avoids manual restarts after source interruptions.


---

## Question 54

Redshift query combining multiple UNION ALL statements runs slowly.
What’s the best optimization?

- A. Replace UNION ALL with CTAS (Create Table As Select) to pre-combine data
- B. Enable sort keys
- C. Increase queue slots
- D. Use DISTINCT

✅ Answer: A
Explanation: CTAS pre-computes results and allows later optimized scans. Multiple UNION ALL cause sequential scans and high CPU. DISTINCT changes semantics.


---

## Question 55

Athena returns duplicate rows across partitions.
What’s the most likely cause?

- A. Overlapping partitions in Glue Catalog
- B. S3 versioning
- C. Parallel readers
- D. Parquet block corruption

✅ Answer: A
Explanation: When multiple partitions reference the same S3 path or overlap, Athena reads duplicates. Drop stale partitions or rebuild Catalog.


---

## Question 56

A Glue job outputs Parquet files that fail downstream reads due to corrupt schema.
What should you check?

- A. DynamicFrame → DataFrame conversion consistency and schema resolution (ResolveChoice)
- B. Job timeout
- C. File size
- D. Worker concurrency

✅ Answer: A
Explanation: Mismatch during conversion between DynamicFrame and DataFrame can create incompatible Parquet schemas. Use ResolveChoice to enforce types.


---

## Question 57

A Kinesis stream experiences sudden throughput spikes and throttling. You need an auto-scaling solution without manual shard splits.

- A. Enable on-demand capacity mode
- B. Use Firehose
- C. Increase retention period
- D. Use Enhanced fan-out

✅ Answer: A
Explanation: Kinesis on-demand automatically adjusts shard capacity to traffic without manual scaling, ideal for variable workloads.


---

## Question 58

Redshift COPY command fails reading from S3.
What IAM policy is required?

- A. s3:GetObject, s3:ListBucket on source bucket
- B. glue:GetTable
- C. lakeformation:GetResource
- D. athena:StartQueryExecution

✅ Answer: A
Explanation: COPY reads objects and lists bucket contents. Glue/LF/Athena permissions are unrelated.


---

## Question 59

Despite using Parquet, Athena query cost remains high.
What can you improve?

- A. Compress Parquet files and ensure partition filters in WHERE clause
- B. Use LIMIT 1000
- C. Increase memory
- D. Re-run crawler

✅ Answer: A
Explanation: Compression + partition pruning reduces data scanne- D. LIMIT doesn’t change scanned bytes.


---

## Question 60

A Glue ETL job performing large joins is slow.
How to optimize join execution?

- A. Use broadcast join hints or dynamic partition pruning
- B. Add more crawlers
- C. Increase bookmarks
- D. Split output into many small files

✅ Answer: A
Explanation: Broadcast joins or dynamic partition pruning reduce shuffle size and improve Spark performance. Other options irrelevant.


---

## Question 61

DMS writes tables to wrong S3 prefixes, e.g., s3://target/public.customers/ instead of s3://target/db/customers/.
What’s wrong?

- A. Table mapping’s S3Prefix template misconfigured
- B. Task logging disabled
- C. Wrong replication instance size
- D. Compression missing

✅ Answer: A
Explanation: DMS uses table mapping JSON to define S3 key prefixes. A mis-templated mapping leads to incorrect folder paths.


---

## Question 62

You must share a Lake Formation table with another AWS account. Column-level control is required.
How do you configure this?

- A. Create a Resource Link table in the consumer account and grant LF permissions from the producer account
- B. Share bucket via ACLs
- C. Export Parquet files manually
- D. Use RAM to share Glue Catalog only

✅ Answer: A
Explanation: Resource Links + LF grants enable cross-account, column-level governed sharing. S3/ACL sharing lacks enforcement.


---

## Question 63

Athena query on an encrypted S3 bucket fails with “Access Denied – KMS key”.

- A. Grant kms:Decrypt permission to Athena execution role
- B. Change encryption type to SSE-S3
- C. Disable encryption
- D. Use bucket ACLs

✅ Answer: A
Explanation: Athena must have KMS Decrypt rights on the CMK used for S3 objects. SSE-S3 removes key control but isn’t mandatory.


---

## Question 64

A Kinesis consumer reprocesses the same data after restart.
How to ensure at-least-once to exactly-once semantics?

- A. Implement checkpointing using sequence numbers in DynamoDB
- B. Reduce retention
- C. Increase shards
- D. Enable enhanced fan-out

✅ Answer: A
Explanation: Consumers must persist checkpoint offsets (e.g., via KCL/DynamoDB) to avoid reprocessing from trim horizon.


---

## Question 65

A Glue job is stuck in “Stopping” state and blocks future runs.

- A. Cancel job run via CLI (stop-job-run) then restart
- B. Wait for auto-cleanup
- C. Delete the Glue database
- D. Increase DPUs

✅ Answer: A
Explanation: “Stopping” often means resources hung; manual cancellation clears state. Once stopped, a new run can start.


---