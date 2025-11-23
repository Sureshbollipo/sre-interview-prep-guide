# AWS Data Engineer Associate (DEA-C01)
### 65 High-Probability Practice Questions (Mixed Order | Real Exam Difficulty)
---

## 🟦 Section 1: Questions 1–25

---

### **Question 1**
A Glue ETL job processes daily CSV files from S3 and writes to Redshift.  
However, it skips the latest partitions even after the crawler adds them.

**A.** Glue worker limit exceeded  
**B.** Glue bookmark enabled  
**C.** Crawler misconfiguration  
**D.** Redshift connection invalid  

✅ **Answer:** B  
**Explanation:** Glue bookmarks track processed data. When enabled, new partitions are skipped unless reprocessed. Disable bookmarks to reload data.

---

### **Question 2**
A Kinesis Firehose delivery stream to S3 delivers data 6–7 minutes late.  
You need data in under 1 minute.

**A.** Reduce Firehose buffer interval to 60 sec  
**B.** Increase retry duration  
**C.** Enable enhanced fan-out  
**D.** Use smaller shards  

✅ **Answer:** A  
**Explanation:** Firehose buffers either 5 MB or 300 sec (default). Reducing buffer interval ensures faster delivery.

---

### **Question 3**
A DMS task replicating RDS PostgreSQL to S3 Parquet is slow.  
Which change improves performance?

**A.** Use JSON format  
**B.** Increase replication instance size  
**C.** Disable full load  
**D.** Reduce target parallelism  

✅ **Answer:** B  
**Explanation:** Replication instance class directly affects throughput. Scaling up reduces latency.

---

### **Question 4**
Athena query on JSON files scans 100 GB but only needs 5 GB.  
How can cost be minimized?

**A.** Use ORC/Parquet format  
**B.** Increase partition depth  
**C.** Use CSV compression  
**D.** Use Glue catalog  

✅ **Answer:** A  
**Explanation:** Athena charges per data scanned. Columnar compressed formats reduce scan size drastically.

---

### **Question 5**
To orchestrate Glue jobs with dependencies and retries:

**A.** Glue Triggers  
**B.** Glue Workflows  
**C.** Step Functions  
**D.** Lambda  

✅ **Answer:** B  
**Explanation:** Glue Workflows manage job orchestration with dependency handling.

---

### **Question 6**
A Redshift query on a large table is slow.  
The table is frequently joined on `customer_id`.

**A.** DISTSTYLE EVEN  
**B.** DISTSTYLE ALL  
**C.** DISTKEY(customer_id)  
**D.** SORTKEY(date)  

✅ **Answer:** C  
**Explanation:** Setting DISTKEY to join/filter column reduces data redistribution.

---

### **Question 7**
A Kinesis Stream has hot shards. Which action helps?

**A.** Add more shards  
**B.** Randomize partition key  
**C.** Increase retention  
**D.** Enable enhanced fan-out  

✅ **Answer:** B  
**Explanation:** Uneven partition keys cause hot shards. Randomizing improves load balance.

---

### **Question 8**
Glue ETL produces many small Parquet files → Athena queries slow.

**A.** Increase worker type  
**B.** Use coalesce() or repartition()  
**C.** Change to CSV  
**D.** Add more DPUs  

✅ **Answer:** B  
**Explanation:** Combining small files reduces I/O overhead and improves Athena performance.

---

### **Question 9**
DMS CDC task shows lag. What to do first?

**A.** Increase replication instance size  
**B.** Use CSV output  
**C.** Disable logs  
**D.** Restart task  

✅ **Answer:** A  
**Explanation:** CPU and I/O on replication instance determine CDC latency.

---

### **Question 10**
Redshift Spectrum query doesn’t show new data in S3.

**A.** Re-run COPY command  
**B.** VACUUM table  
**C.** Refresh Glue Catalog  
**D.** Change IAM permissions  

✅ **Answer:** C  
**Explanation:** Spectrum uses Glue Catalog metadata; refresh catalog after new data.

---

### **Question 11**
Athena returns “HIVE_PARTITION_SCHEMA_MISMATCH” error after Glue crawler update.

**A.** Drop table  
**B.** Set `projection.enabled`  
**C.** Enable “Update existing schema” in crawler  
**D.** Run MSCK REPAIR TABLE  

✅ **Answer:** C  
**Explanation:** Crawler must be configured to update existing schemas when new columns appear.

---

### **Question 12**
Glue job connecting to Redshift fails intermittently.

**A.** Wrong JDBC URL  
**B.** IAM role missing S3 access  
**C.** Network VPC endpoint missing  
**D.** Redshift WLM queue full  

✅ **Answer:** C  
**Explanation:** Glue jobs require VPC endpoints to reach Redshift clusters in private subnets.

---

### **Question 13**
A data pipeline uses DMS → S3 → Glue → Athena.  
Athena shows duplicate rows.

**A.** DMS full load task restarted  
**B.** Glue job bookmark disabled  
**C.** Firehose buffering  
**D.** Athena caching  

✅ **Answer:** A  
**Explanation:** Restarting a DMS full load task re-inserts existing data, causing duplicates.

---

### **Question 14**
You need near real-time analytics (< 1 min) for IoT stream data.

**A.** Firehose → S3 → Athena  
**B.** Streams → Lambda → Redshift  
**C.** DMS → S3 → Glue  
**D.** SQS → Lambda  

✅ **Answer:** B  
**Explanation:** Firehose has 1–5 min delay; Streams + Lambda < 1 min latency.

---

### **Question 15**
Which Glue connection type is needed to connect to on-prem Oracle DB?

**A.** JDBC  
**B.** Network connection  
**C.** Catalog connection  
**D.** Spark connector  

✅ **Answer:** A  
**Explanation:** JDBC connections are required for external RDBMS integration.

---

### **Question 16**
Athena table partitions not detected automatically.

**A.** MSCK REPAIR TABLE  
**B.** Add partitions manually  
**C.** Run Crawler  
**D.** Restart query  

✅ **Answer:** A  
**Explanation:** MSCK REPAIR TABLE syncs Glue metadata with S3 folders.

---

### **Question 17**
Lake Formation-managed table gives “Access Denied” for user.

**A.** IAM role missing permission  
**B.** S3 bucket not encrypted  
**C.** LF permission not granted  
**D.** Glue schema mismatch  

✅ **Answer:** C  
**Explanation:** LF overrides IAM/S3. You must grant table-level permissions explicitly.

---

### **Question 18**
Glue crawler detects wrong data type for integer columns.

**A.** Change crawler depth  
**B.** Increase sample size  
**C.** Recreate table  
**D.** Run Glue Job  

✅ **Answer:** B  
**Explanation:** Increasing sampling percentage improves schema inference accuracy.

---

### **Question 19**
Redshift performance drops; queries queue up.

**A.** Enable concurrency scaling  
**B.** Add DISTKEY  
**C.** Change WLM configuration  
**D.** Rebuild indexes  

✅ **Answer:** A  
**Explanation:** Concurrency scaling automatically adds compute capacity for bursts.

---

### **Question 20**
Athena query returns NULL for new columns in JSON files.

**A.** Refresh crawler  
**B.** Recreate table  
**C.** Set `projection.enabled`  
**D.** Convert to Parquet  

✅ **Answer:** A  
**Explanation:** Glue crawler must update schema after JSON schema change.

---

### **Question 21**
DMS task fails with “target write throughput exceeded” to S3.

**A.** Use CSV instead of Parquet  
**B.** Reduce parallel load threads  
**C.** Use bigger replication instance  
**D.** Enable auto-scaling on target  

✅ **Answer:** B  
**Explanation:** Too many threads can overload target S3 writes. Reduce parallelism.

---

### **Question 22**
Glue job writing to S3 fails with AccessDenied.

**A.** Add Glue to IAM role’s trust policy  
**B.** Add S3:GetObject permission  
**C.** Add S3:PutObject to IAM role  
**D.** Enable encryption  

✅ **Answer:** C  
**Explanation:** Glue writer needs S3:PutObject for destination bucket.

---

### **Question 23**
Which AWS service manages schema and metadata for Athena & Redshift Spectrum?

**A.** AWS Glue Data Catalog  
**B.** Lake Formation  
**C.** CloudFormation  
**D.** DMS  

✅ **Answer:** A  
**Explanation:** Glue Catalog is central metadata store for querying data lakes.

---

### **Question 24**
An EMR cluster using spot nodes frequently fails.  
What’s the simplest mitigation?

**A.** Use On-Demand for core nodes, Spot for task nodes  
**B.** Switch to all On-Demand  
**C.** Add retry logic  
**D.** Increase timeout  

✅ **Answer:** A  
**Explanation:** Core nodes store HDFS metadata—keep them On-Demand for stability.

---

### **Question 25**
Athena charges too high. Queries scan large datasets.

**A.** Convert data to Parquet  
**B.** Increase partitions  
**C.** Use smaller instance  
**D.** Reduce concurrent queries  

✅ **Answer:** A  
**Explanation:** Parquet reduces scan size and thus cost.

---

## 🟩 Section 2: Questions 26–45

---

### **Question 26**
Lake Formation user can’t query a shared dataset even with S3 access.

✅ **Answer:** Missing Lake Formation grant  
**Explanation:** LF enforces fine-grained control, overriding IAM/S3.

---

### **Question 27**
Glue job fails with out-of-memory on large dataset.

✅ **Answer:** Increase worker type to G.1X or G.2X  
**Explanation:** Higher workers provide more memory and parallelism.

---

### **Question 28**
DMS validation failed for certain tables.

✅ **Answer:** Source table schema changed  
**Explanation:** DMS validation fails when schema mismatches occur mid-replication.

---

### **Question 29**
Redshift query joins two large tables with skewed distribution.

✅ **Answer:** Use EVEN distribution  
**Explanation:** EVEN prevents skew and reduces single-node load.

---

### **Question 30**
Glue job runtime extremely long, writing millions of rows to S3.

✅ **Answer:** Enable job bookmarking and incremental load  
**Explanation:** Bookmarking avoids full reprocessing, reducing runtime.

---

### **Question 31**
Athena query returning duplicates after Glue re-crawl.

✅ **Answer:** Old partitions not deleted in Catalog  
**Explanation:** Remove stale partitions or use `ALTER TABLE DROP PARTITION`.

---

### **Question 32**
Kinesis Firehose Lambda transform timing out.

✅ **Answer:** Reduce transformation complexity or use batch transform  
**Explanation:** Firehose invokes Lambda synchronously; long processing leads to retries.

---

### **Question 33**
DMS full load + CDC migration. CDC lag growing.

✅ **Answer:** Split task into smaller table groups  
**Explanation:** Parallelizing tasks reduces replication lag.

---

### **Question 34**
Redshift COPY command fails on gzipped CSV files.

✅ **Answer:** Use `gzip` keyword in COPY command  
**Explanation:** Redshift needs explicit format declaration.

---

### **Question 35**
Glue crawler takes hours to complete.

✅ **Answer:** Reduce depth of folder scan or limit sample size  
**Explanation:** Crawlers scanning deep paths take longer; scope it properly.

---

### **Question 36**
Athena query fails with “HIVE_CANNOT_OPEN_SPLIT” on large files.

✅ **Answer:** Split files smaller (<128 MB)  
**Explanation:** Large files cause query splits to fail on parallel nodes.

---

### **Question 37**
Lake Formation registered dataset not visible in Athena.

✅ **Answer:** Grant LF permissions to Athena service role  
**Explanation:** Both IAM and LF grants are required.

---

### **Question 38**
EMR used for Spark ETL from S3. Frequent retries observed.

✅ **Answer:** Use EMRFS consistent view or S3DistCp  
**Explanation:** Avoid eventual consistency in S3 read/writes.

---

### **Question 39**
Glue job reading from JDBC connection fails with SSL error.

✅ **Answer:** Add connection options: `ssl=true`  
**Explanation:** Many databases require SSL connection parameters.

---

### **Question 40**
Athena query timeout at 30 min.

✅ **Answer:** Partition data and use filters  
**Explanation:** Athena reads full dataset; reduce size by partition filtering.

---

### **Question 41**
DMS replication stopped after failover.

✅ **Answer:** Enable Multi-AZ on replication instance  
**Explanation:** Multi-AZ ensures automatic failover for high availability.

---

### **Question 42**
Redshift table has frequent vacuum operations.

✅ **Answer:** Use AUTO VACUUM and ANALYZE  
**Explanation:** Redshift auto-tunes VACUUM; manual runs can be reduced.

---

### **Question 43**
Kinesis consumers lagging behind.

✅ **Answer:** Increase shard count or add enhanced fan-out  
**Explanation:** Parallelism improves consumer read throughput.

---

### **Question 44**
Glue job needs to trigger when a file lands in S3.

✅ **Answer:** S3 event + Lambda trigger -> StartJobRun API  
**Explanation:** Event-driven ETL trigger pattern.

---

### **Question 45**
Athena external table uses wrong delimiter.

✅ **Answer:** ALTER TABLE SET SERDEPROPERTIES  
**Explanation:** Fix delimiter and escape characters using SerDe config.

---

## 🟥 Section 3: Questions 46–65

---

### **Question 46**
You need to query S3 data from Redshift without loading.

✅ **Answer:** Redshift Spectrum  
**Explanation:** Spectrum queries S3 data directly.

---

### **Question 47**
DMS task storage cost too high; query speed poor.

✅ **Answer:** Use Parquet + Snappy  
**Explanation:** Columnar compressed formats reduce both cost and latency.

---

### **Question 48**
Glue crawler doesn’t detect files in nested directories.

✅ **Answer:** Increase crawler depth  
**Explanation:** Default depth = 1; increase to include subfolders.

---

### **Question 49**
Kinesis Firehose delivery to Redshift failing.

✅ **Answer:** Check COPY permissions and IAM role  
**Explanation:** Firehose uses COPY under the hood and needs proper IAM trust.

---

### **Question 50**
Athena query runs daily; you want to automate refresh.

✅ **Answer:** Use EventBridge schedule to trigger query execution via Lambda  
**Explanation:** EventBridge supports scheduled automation.

---

### **Question 51**
Glue job fails with `ExecutorLostFailure`.

✅ **Answer:** Increase job DPUs or optimize partitions  
**Explanation:** Insufficient memory leads to executor loss.

---

### **Question 52**
Lake Formation permissions not taking effect.

✅ **Answer:** Disable direct S3 access; enforce via LF  
**Explanation:** LF policies override S3 and IAM; enable LF enforcement.

---

### **Question 53**
DMS CDC task drops data after source restart.

✅ **Answer:** Enable task resume and cached LSNs  
**Explanation:** DMS uses cached checkpoint LSN to continue CDC.

---

### **Question 54**
Redshift query using UNION ALL very slow.

✅ **Answer:** Use Spectrum or CTAS to pre-combine data  
**Explanation:** Large UNIONs better handled as precomputed tables.

---

### **Question 55**
Athena returning duplicate rows across partitions.

✅ **Answer:** Check for overlapping partitions in Glue Catalog  
**Explanation:** Duplicate partitions cause redundant data scans.

---

### **Question 56**
Glue job writes corrupt Parquet files.

✅ **Answer:** Ensure DynamicFrame to DataFrame conversion properly  
**Explanation:** Schema mismatch during conversion can corrupt output.

---

### **Question 57**
Kinesis throughput spikes cause throttling.

✅ **Answer:** Use on-demand capacity mode  
**Explanation:** On-demand Kinesis auto-scales shards dynamically.

---

### **Question 58**
Redshift COPY fails due to missing IAM permissions.

✅ **Answer:** Grant S3:GetObject and ListBucket  
**Explanation:** COPY reads from S3; needs read access.

---

### **Question 59**
Athena query costs too high despite Parquet.

✅ **Answer:** Compress Parquet and filter partitions  
**Explanation:** Compression + partition pruning reduces scan bytes.

---

### **Question 60**
Glue job executes slowly due to large joins.

✅ **Answer:** Enable dynamic partition pruning or broadcast join hints  
**Explanation:** Optimizes Spark join performance.

---

### **Question 61**
DMS task writes data to wrong prefix.

✅ **Answer:** Adjust table mapping with correct S3Prefix  
**Explanation:** DMS table mapping defines S3 key prefix.

---

### **Question 62**
Lake Formation cross-account sharing not working.

✅ **Answer:** Use Resource Link and grant LF permission to external account  
**Explanation:** Cross-account sharing via resource link tables.

---

### **Question 63**
Athena query on S3 encrypted data fails.

✅ **Answer:** Use correct KMS key and IAM permissions  
**Explanation:** Need decrypt permission for KMS-encrypted buckets.

---

### **Question 64**
Kinesis consumer retries same data multiple times.

✅ **Answer:** Implement checkpointing in consumer  
**Explanation:** Prevents duplicate processing by tracking sequence numbers.

---

### **Question 65**
Glue job stuck in “Stopping” state.

✅ **Answer:** Cancel job run via AWS CLI or Console and restart  
**Explanation:** Resource lock; manual termination clears the job state.

---

✅ **END OF FILE**

Each question reflects real AWS DEA-C01 difficulty and decision reasoning, not trivia.
Use this markdown as your final revision question bank.