AWS Data Engineer Associate (DEA-C01) — Practice Set (Q01–Q25)

Mixed order • Real exam difficulty • Full-length scenarios • Detailed explanations
Aligned to DEA-C01 domains: ingestion & integration, transformation & preparation, storage & modeling, security & governance, monitoring & operations.


---

Question 1

A retail company lands daily CSV files from stores into s3://retail-lake/raw/sales/date=YYYY-MM-DD/. A Glue crawler updates a Glue Catalog table. A Glue Spark ETL job converts to Parquet and writes to s3://retail-lake/curated/sales/ and then COPYs into Redshift. Yesterday’s partition appeared in the Catalog, but the ETL job skipped it. You must ingest it without duplicating prior partitions.

A. Disable the crawler and re-create the table
B. Disable Glue Job Bookmarks and re-run the job once
C. Increase DPU and worker type for the job
D. Run VACUUM on Redshift then re-run the job

✅ Answer: B
Explanation: Glue Job Bookmarks track processed data. When a new partition is added, the job may skip it if bookmarks consider it processed. Temporarily disabling bookmarks (or resetting) allows one-time reprocessing of the missing partition without duplicating earlier ones. VACUUM affects table maintenance, not ingest control. Increasing DPUs does not change bookmark logic. Recreating the table is unnecessary and risky for lineage.


---

Question 2

A startup streams mobile telemetry to Kinesis Data Streams with a hot partition key because many events use the same device ID. Consumers lag and throttling occurs. They need a change that preserves ability to group by device later while preventing shard hotspots.

A. Increase stream retention period to 7 days
B. Randomize the partition key completely
C. Add a hash suffix (e.g., deviceId#modN) to the partition key
D. Use Kinesis Data Firehose instead of Streams

✅ Answer: C
Explanation: Hot shards occur when too many records share a partition key. Key sharding (hash-suffix bucketing like deviceId#00…#NN) spreads writes across shards while allowing downstream regrouping by stripping the suffix. Full randomization loses grouping semantics. Retention doesn’t fix write throttling. Firehose can still buffer and won’t solve upstream hot key distribution.


---

Question 3

A bank is migrating an on-prem Oracle OLTP database to an S3 data lake for analytics. DMS is configured for Full Load + CDC with S3 target in Parquet. The team reports growing CDC lag during peak hours. They want the most effective action to reduce replication latency.

A. Switch S3 output to JSON for faster writes
B. Increase the DMS replication instance size/class
C. Disable table and column mappings
D. Reduce S3 multipart upload threshold

✅ Answer: B
Explanation: CDC throughput is primarily constrained by the replication instance CPU, memory, and I/O. Scaling it up usually reduces lag. JSON is larger than Parquet and increases scan/storage costs. Table mappings are needed for control; removing them is counterproductive. Multipart thresholds have marginal impact compared to compute limits.


---

Question 4

Analysts query S3 JSON logs with Athena. Queries are slow and expensive because they scan hundreds of GBs. You must improve performance and cost with minimal operational overhead.

A. Convert data to Parquet and partition by date
B. Increase Athena workgroup bytes scanned quota
C. Use Glue Schema Registry with JSON
D. Store JSON.gz files but keep the same table definition

✅ Answer: A
Explanation: Columnar formats like Parquet plus partitioning (e.g., date=) drastically reduce bytes scanned via column and partition pruning, lowering cost and improving speed. Quotas don’t speed queries. Schema Registry helps streaming schemas, not scan volume. Gzipping JSON reduces storage, not scan bytes inside a columnar-aware engine.


---

Question 5

You orchestrate a nightly pipeline: Crawler → Glue ETL Jobs (multiple steps) → Data Quality step → Redshift COPY. You need built-in dependency chaining, retries, and centralized run history without building custom Step Functions.

A. Glue Triggers only
B. Glue Workflows
C. CloudWatch Events + Lambda
D. Step Functions with custom SDK integrations

✅ Answer: B
Explanation: Glue Workflows provide native orchestration for multiple Glue jobs, crawlers, and triggers, with dependency graphs, retries, and run lineage/history. Triggers alone are limited. Step Functions would work but adds extra plumbing; the question prefers Glue-native solution.


---

Question 6

A large Redshift fact table is frequently joined to a customer dimension using customer_id and filtered by the same column. You observe high data redistribution during joins and poor performance.

A. DISTSTYLE EVEN; SORTKEY(customer_id)
B. DISTKEY(customer_id); SORTKEY(customer_id)
C. DISTSTYLE ALL; SORTKEY(date)
D. DISTSTYLE AUTO; SORTKEY AUTO

✅ Answer: B
Explanation: Choosing DISTKEY(customer_id) colocates rows on the same slices for joins on that key, minimizing shuffles. Pairing with a SORTKEY on the same column improves locality for filter and join. EVEN ignores the key. ALL is for small dimensions, not large facts. AUTO may help, but explicit, known-access-pattern tuning is superior here.


---

Question 7

A compliance team mandates column-level access control for PII columns in shared tables across multiple analytics accounts. Data is stored in S3, queried via Athena and Redshift Spectrum. You need minimal custom code.

A. Use S3 bucket policies with object-level permissions per column
B. Use Lake Formation with column-level grants on Catalog tables
C. Create separate S3 buckets for masked and unmasked copies
D. Enforce column filters in application SQL

✅ Answer: B
Explanation: Lake Formation provides fine-grained table/column permissions integrated with Glue Catalog and enforced by Athena/Redshift Spectrum. S3 permissions don’t understand column semantics. Multiple buckets multiply management overhead. Application-level filters are brittle and not centrally governed.


---

Question 8

A Glue Spark job writes thousands of tiny Parquet files (5–50 KB) per partition, causing slow Athena queries and high planning overhead. You need to reduce small files efficiently.

A. Use coalesce() or repartition() in the job before writing
B. Increase number of workers to parallelize more
C. Switch output format to JSON
D. Trigger a crawler after every write

✅ Answer: A
Explanation: File compaction through repartition()/coalesce() reduces the number of output files per partition, improving query performance and reducing metadata pressure. More workers create even more small files. JSON worsens scan efficiency. Crawlers don’t merge files.


---

Question 9

You built a Kinesis Data Analytics (Flink) app to aggregate clickstream events and write results to S3 via Firehose. During spikes, the app backpressures and lags. You want to reduce end-to-end latency with minimal ops.

A. Increase KDA parallelism and enable auto-scaling
B. Switch to Streams consumers with enhanced fan-out
C. Increase Firehose buffer size to 128 MB
D. Reduce Kinesis retention from 24h to 12h

✅ Answer: A
Explanation: KDA Flink supports parallelism and auto-scaling to handle spikes without manual shard management. Enhanced fan-out helps reader throughput, not Flink operator capacity. Larger Firehose buffers increase latency. Retention doesn’t address processing lag.


---

Question 10

Redshift external schema (Spectrum) points to a Glue table over s3://datalake/curated/events/. New partitions arrive hourly, but queries don’t see them automatically.

A. Run MSCK REPAIR TABLE or add partitions via ALTER TABLE
B. Run Redshift VACUUM REINDEX
C. Enable COPY with MANIFEST
D. Increase Redshift WLM queue slots

✅ Answer: A
Explanation: Spectrum relies on Glue Catalog metadata. New partitions must be added to the Catalog (MSCK or ALTER TABLE ADD PARTITION) for queries to see them. VACUUM and WLM are internal Redshift maintenance, not external metadata sync. COPY is for internal tables.


---

Question 11

A financial firm needs near real-time (< 60 seconds) fraud signals from card swipes, persisted to S3 and queryable in Athena for ops dashboards. The solution should be serverless and low-ops.

A. Firehose → S3 → Athena scheduled queries every 5 min
B. Streams → Lambda transforms → Firehose → S3 → Athena
C. MSK → custom consumer → EMR → S3 → Athena hourly
D. Streams → KDA SQL → Firehose → S3 → Athena

✅ Answer: D
Explanation: Streams + Kinesis Data Analytics (SQL) provides near real-time processing; Firehose delivers to S3; Athena queries data with minimal ops. Lambda alone can struggle with stateful aggregations and ordering. EMR hourly misses the SLA. Firehose alone is usually 1–5 minutes latency.


---

Question 12

A DMS task writes to S3 with the structure s3://landing/db/table/year=YYYY/month=MM/day=DD/. Analysts see duplicates after a task restart. You must prevent duplicates without manual de-dup SQL.

A. Use S3 object overwrite mode
B. Enable task resume with cached checkpoint/LSN and ensure Full Load + CDC separation
C. Switch to JSON to identify duplicates
D. Use lifecycle rules to expire older files

✅ Answer: B
Explanation: Proper checkpointing and resume ensure CDC continues from the last committed LSN/SCN and avoids re-emitting full-load data. DMS best practice separates full load output from CDC paths. Overwrite mode risks data loss. JSON doesn’t solve duplication. Lifecycle rules don’t prevent dupes.


---

Question 13

You query a year of logs with Athena. Only November is needed. The table is partitioned by year and month. The query still scans most data.

A. Use WHERE month='11' and year='2025' on partition columns
B. Use LIMIT 1000
C. Use CTAS to materialize a subset
D. Change SerDe to lazy simple

✅ Answer: A
Explanation: Partition pruning only works when filter predicates reference the partition columns exactly (and with correct types). LIMIT doesn’t reduce scan bytes. CTAS helps reuse but still scans once. SerDe choice doesn’t perform pruning.


---

Question 14

An EMR Spark job reading from S3 sometimes fails with “file not found” immediately after another job writes results to the same prefix. You need stronger read-after-write consistency.

A. Use HDFS instead of S3
B. Enable EMRFS consistent view or add a short retry/backoff
C. Increase Spark executor memory
D. Use Redshift Spectrum instead

✅ Answer: B
Explanation: S3 metadata is eventually consistent in some cases. EMRFS consistent view (with DynamoDB) or retry/backoff patterns mitigate timing issues after writes. HDFS loses durability and elasticity. Memory won’t fix consistency. Spectrum doesn’t address the job’s own output-read race.


---

Question 15

A Glue job connects to a private Redshift cluster in a VPC. The job fails to connect; the subnet has no internet, and there’s no NAT. You must keep traffic private.

A. Add a NAT Gateway for outbound internet
B. Configure VPC endpoints (interface endpoint for Redshift) and place the job in the same VPC/subnets with proper SGs
C. Open Redshift to 0.0.0.0/0
D. Run the job outside the VPC

✅ Answer: B
Explanation: For private connectivity, run Glue in the same VPC with appropriate security groups and use VPC endpoints. NAT introduces internet egress. Public access is insecure. Running outside VPC can break private routing.


---

Question 16

Your organization wants to minimize Athena cost while keeping flexibility. Data is an evolving semi-structured JSON stream. What storage strategy best balances cost and schema evolution?

A. Store raw JSON only
B. Convert to Parquet with schema evolution support and retain raw JSON in a separate “archive” prefix
C. Convert to CSV and delete raw
D. Use Avro only

✅ Answer: B
Explanation: Parquet drastically reduces scan cost; keeping a raw JSON archive preserves replay/evolution. CSV lacks types. Avro is row-oriented; Parquet is generally better for analytics scans.


---

Question 17

Security requires auditability of who accessed which S3 objects through Athena/Redshift Spectrum. You must provide object-level access logs and query lineage with minimal custom code.

A. Enable S3 server access logs only
B. Enable CloudTrail data events on the S3 buckets and use Lake Formation permissions
C. Use only IAM Access Analyzer
D. Build a custom proxy to log accesses

✅ Answer: B
Explanation: CloudTrail data events capture object-level access logs. With Lake Formation, you control fine-grained access via the Catalog, providing both governance and auditable trails. S3 access logs lack detailed API context. Proxies add complexity.


---

Question 18

You are evaluating ingestion for RDBMS → Redshift with minimal downtime and ability to keep warehouse updated.

A. Use Glue JDBC job to read tables daily into Redshift
B. Use DMS Full Load + CDC into Redshift with optimized commit settings
C. Export CSV nightly to S3 and COPY
D. Use Athena Federated Query

✅ Answer: B
Explanation: DMS Full Load + CDC is designed for minimal downtime migrations and near-real-time updates to Redshift. JDBC jobs and nightly exports are batch and miss low latency. Federated Query is ad-hoc, not sustained ingestion.


---

Question 19

A team uses Firehose to S3 with Lambda transformation. During peak, delivery latency increases to ~6 minutes. They want < 1 minute without turning off the transform.

A. Reduce Firehose buffer interval to ~60 seconds and optimize Lambda to process batches quickly
B. Increase Firehose retry duration
C. Switch to Streams on-demand capacity but keep Firehose
D. Increase S3 multipart threshold

✅ Answer: A
Explanation: Firehose buffers by time or size; reducing buffer interval lowers latency. Ensure Lambda is performant on batch payloads. Retries don’t help steady-state latency. Streams on-demand is a different pattern and doesn’t lower Firehose buffering.


---

Question 20

A Redshift table is sorted by order_date. Most queries are last-30-days with range filters on that column. Performance is degrading over time.

A. Change sort key to customer_id
B. Keep order_date as SORTKEY and run VACUUM/ANALYZE or use AUTO table tuning
C. Use DISTSTYLE ALL
D. Switch to EVEN distribution and remove sort key

✅ Answer: B
Explanation: Time-range filters benefit from a date sort key. Over time, table fragmentation grows; VACUUM/ANALYZE or AUTO tuning helps maintain performance. Changing keys arbitrarily harms typical filters. DISTSTYLE is unrelated to sort locality.


---

Question 21

An Athena table over Parquet is returning NULL for a new column introduced last week. The crawler ran.

A. Recreate the table
B. Ensure crawler has “Update existing schema” and partition projection or repair partitions
C. Change compression codec
D. Use CTAS to copy data

✅ Answer: B
Explanation: Schema evolution requires the crawler to update the table schema. If partitions are added manually, use MSCK REPAIR or partition projection. Recreating the table is heavy-handed. Compression isn’t the cause.


---

Question 22

A regulated enterprise needs cross-account sharing of curated datasets with column masking for PII, queryable via Athena and Redshift Spectrum. The solution should be centrally governed.

A. Share S3 bucket with bucket policies
B. Use Lake Formation Resource Links + column-level grants to external accounts
C. Copy data to each account and apply local IAM policies
D. Share Glue Catalog with no LF

✅ Answer: B
Explanation: LF Resource Links support cross-account sharing of Catalog metadata with column-level governance. S3-only sharing lacks column semantics. Copying multiplies storage and governance work. Catalog-only sharing misses enforcement.


---

Question 23

A Glue job performing a large join spills to disk and fails with executor memory errors. You must stabilize it quickly with minimal code change.

A. Increase worker type (e.g., G.2X) and DPUs; enable job bookmarking for incremental runs
B. Switch to JSON output
C. Remove partitioning
D. Use Python shell job

✅ Answer: A
Explanation: Increasing memory/compute and enabling incremental processing reduces join pressure per run. The other options don’t address memory and scale. Python shell doesn’t run Spark.


---

Question 24

Analysts want to query S3 data from Redshift without loading, but they also want to join with internal Redshift fact tables and apply LF permissions.

A. Use Redshift Spectrum with external schemas tied to Glue Catalog managed by Lake Formation
B. Build external tables directly in Redshift with no Catalog
C. Export Redshift tables to S3 and use Athena only
D. Use COPY to load all data first

✅ Answer: A
Explanation: Spectrum enables external tables over S3 and joins with internal tables, while LF can govern access on the Catalog. Direct external tables with no Catalog lack governance integration. COPY defeats “no load” requirement.


---

Question 25

A pipeline: DMS → S3 (Parquet) → Glue ETL → Redshift. After a schema change in source, Glue job fails due to field mismatch. You need a resilient approach with minimal downtime.

A. Force Glue to ignore schema drift
B. Use Glue DynamicFrames with ResolveChoice and catalog updates via crawler before ETL
C. Switch to JSON in DMS
D. Pause CDC until manual schema migration completes

✅ Answer: B
Explanation: DynamicFrames handle semi-structured and evolving schemas; ResolveChoice can coerce/rename fields. Running the crawler first updates the Catalog so the ETL sees new schema. JSON increases size and cost. Pausing CDC adds downtime.
