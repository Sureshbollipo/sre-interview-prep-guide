---

☕ AWS Data Engineer Associate – Rapid Recall Sheet (15-Minute Pre-Exam)


---

🧩 GLUE & CATALOG

Bookmark skips partitions → disable to reprocess.

Crawlers = schema discovery → Glue Catalog → Athena/Redshift Spectrum.

DynamicFrames + ResolveChoice → handle schema drift (new columns).

Workflows = built-in job orchestration + retries.

Repartition/coalesce → merge small Parquet files → faster Athena.

Best output: Parquet + Snappy.

Glue connections: JDBC to RDS/Redshift; must allow VPC access.



---

🌊 KINESIS FAMILY

Streams = sub-second latency, manual scaling (shards).

Firehose = 1–5 min buffer (5 MB or 300 s) → near real-time.

Data Analytics (KDA) = SQL/Flink → real-time aggregations.

Hot shard? → Randomize or hash-suffix partition key (id#00–#09).

Enhanced fan-out → 1 MB/s per consumer.

Firehose Lambda → must finish in ≤ 60 s or it retries.

On-demand capacity = auto-scale shards during spikes.



---

🔄 DMS (Database Migration Service)

Replication types: Full | CDC | Full + CDC.

CDC lag? → Scale replication instance size.

Duplicate rows? → Restarted task w/o checkpoint resume.

Best S3 format: Parquet + Snappy (for Athena/Glue).

Validation = compares row counts & checksums.

Multi-AZ = automatic failover; prevents restart loss.

Wrong prefix in S3? → Check table mapping JSON.



---

🧱 REDSHIFT & SPECTRUM

DISTKEY = join/filter col → minimizes shuffle.

SORTKEY = time/range col → improves pruning.

COPY = fast load from S3; needs S3:GetObject.

UNLOAD = export to S3.

Spectrum = query S3 external data (via Glue Catalog).

VACUUM/ANALYZE = maintain stats (AUTO mode now default).

Concurrency Scaling = burst compute for parallel queries.



---

🔐 SECURITY & GOVERNANCE

Lake Formation = column/table-level governance.

Resource Link = cross-account dataset sharing.

Disable direct S3 ACLs → enforce LF only.

KMS Decrypt error? → Missing kms:Decrypt permission.

CloudTrail data events → audit S3 object-level access.

S3 versioning = data recovery, not access control.



---

💰 COST & PERFORMANCE

Athena cost = bytes scanned.
→ Use Parquet + partitions + compression.

Limit 1000 ≠ reduce cost (still scans full data).

Redshift Spectrum → no data load; cheap for joins.

Firehose → S3 (Parquet) cheapest streaming lake pattern.

EMRFS Consistent View → solves read-after-write errors.

Glue DPUs / worker type (G.2X) → more memory for large joins.



---

⚙️ TROUBLESHOOTING TRIGGERS

Symptom	Root Cause

New data skipped	Glue Bookmark
Firehose late delivery	Buffer 5 MB / 300 s
DMS lag	Small replication instance
Duplicate CDC rows	Restarted Full Load
“Access Denied” Athena	LF permission missing
“Access Denied” KMS	Missing decrypt permission
Kinesis reprocesses old data	No checkpoint
Schema mismatch	Crawler not updated
“File not found” in EMR	S3 eventual consistency
Glue job stuck stopping	Cancel via CLI



---

🧠 MINDSET REMINDER

Read question → find AWS keyword:

“Cost-effective” → choose serverless.

“Least ops” → choose managed service.

“Near real-time” → Kinesis (Streams + KDA).

“Schema drift” → Glue crawler / DynamicFrame.

“Column access” → Lake Formation.


Don’t overthink: if two are right, choose the one AWS manages for you.



---

☀️ QUICK TIMING PLAN (Exam 130 min)

Q1–33 → 65 min

Q34–65 → 55 min

Review flagged → 10 min

Always flag if 60% sure — first intuition is often correct.

