- Deploying with Helm Charts
  - https://fluss.apache.org/docs/install-deploy/deploying-with-helm/
  - helm repo
    https://downloads.apache.org/incubator/fluss/helm-chart/0.8.0-incubating/

```
root@tablet-server-0:/opt/fluss/plugins# ls hdfs/
fluss-fs-hdfs-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls jmx/
fluss-metrics-jmx-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls lance/
fluss-lake-lance-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls s3/
fluss-fs-s3-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls prometheus/
fluss-metrics-prometheus-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls iceberg/
fluss-lake-iceberg-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls paimon/
flink-shaded-hadoop-2-uber-2.8.3-10.0.jar  fluss-lake-paimon-0.8.0-incubating.jar

root@tablet-server-0:/opt/fluss/plugins# ls
hdfs  iceberg  jmx  lance  oss  paimon  prometheus  s3
```

"Fluss uses file systems as remote storage to store snapshots for Primary-Key Table and store tiered log segments for Log Table."
local file systems 에 log와 primary key table을 저장하고
tiered 구조로 remote storage를 구성해 snapshot등을 저장한다.
hot -> cold 이동은 어떻게 할까?

- custom image build
```
docker build . -t registry.tde.sktelecom.com/emergingdp/tlake/fluss:0.8.0-incubating-hive-iceberg-s3 --plat
form linux/amd64 -f docker/Dockerfile.iceberg-s3
docker push registry.tde.sktelecom.com/emergingdp/tlake/fluss:0.8.0-incubating-hive-iceberg-s3
```

- tablet server 정보 조회 
```
root@tablet-server-2:/opt/fluss/log/rocksdb# ls
tmp_fluss_data_my_db_user_table-0_kv-0_db_LOG
root@tablet-server-2:/opt/fluss/log/rocksdb# cat tmp_fluss_data_my_db_user_table-0_kv-0_db_LOG
2025/12/24-11:02:36.516894 7f0844dff640 RocksDB version: 6.20.3
2025/12/24-11:02:36.516946 7f0844dff640 Compile date 2023-01-14 07:57:59
2025/12/24-11:02:36.517002 7f0844dff640 DB SUMMARY
2025/12/24-11:02:36.517004 7f0844dff640 DB Session ID:  EW6GDT4A2ISGTLKXIV82
2025/12/24-11:02:36.517015 7f0844dff640 SST files in /tmp/fluss/data/my_db/user_table-0/kv-0/db dir, Total Num: 0, files:
2025/12/24-11:02:36.517016 7f0844dff640 Write Ahead Log file in /tmp/fluss/data/my_db/user_table-0/kv-0/db:
2025/12/24-11:02:36.517018 7f0844dff640                         Options.error_if_exists: 0
2025/12/24-11:02:36.517019 7f0844dff640                       Options.create_if_missing: 1
2025/12/24-11:02:36.517020 7f0844dff640                         Options.paranoid_checks: 1
2025/12/24-11:02:36.517021 7f0844dff640                               Options.track_and_verify_wals_in_manifest: 0
2025/12/24-11:02:36.517022 7f0844dff640                                     Options.env: 0x7f076a4e1120
2025/12/24-11:02:36.517023 7f0844dff640                                      Options.fs: Posix File System
2025/12/24-11:02:36.517024 7f0844dff640                                Options.info_log: 0x7f075c05a550
2025/12/24-11:02:36.517025 7f0844dff640                Options.max_file_opening_threads: 16
2025/12/24-11:02:36.517026 7f0844dff640                              Options.statistics: (nil)
2025/12/24-11:02:36.517027 7f0844dff640                               Options.use_fsync: 0
2025/12/24-11:02:36.517028 7f0844dff640                       Options.max_log_file_size: 26214400
2025/12/24-11:02:36.517029 7f0844dff640                  Options.max_manifest_file_size: 1073741824
2025/12/24-11:02:36.517030 7f0844dff640                   Options.log_file_time_to_roll: 0
2025/12/24-11:02:36.517031 7f0844dff640                       Options.keep_log_file_num: 4
2025/12/24-11:02:36.517032 7f0844dff640                    Options.recycle_log_file_num: 0
2025/12/24-11:02:36.517032 7f0844dff640                         Options.allow_fallocate: 1
2025/12/24-11:02:36.517033 7f0844dff640                        Options.allow_mmap_reads: 0
2025/12/24-11:02:36.517034 7f0844dff640                       Options.allow_mmap_writes: 0
2025/12/24-11:02:36.517035 7f0844dff640                        Options.use_direct_reads: 0
2025/12/24-11:02:36.517036 7f0844dff640                        Options.use_direct_io_for_flush_and_compaction: 0
2025/12/24-11:02:36.517037 7f0844dff640          Options.create_missing_column_families: 0
2025/12/24-11:02:36.517037 7f0844dff640                              Options.db_log_dir: /opt/fluss/log/rocksdb
2025/12/24-11:02:36.517038 7f0844dff640                                 Options.wal_dir: /tmp/fluss/data/my_db/user_table-0/kv-0/db
2025/12/24-11:02:36.517039 7f0844dff640                Options.table_cache_numshardbits: 6
2025/12/24-11:02:36.517040 7f0844dff640                         Options.WAL_ttl_seconds: 0
2025/12/24-11:02:36.517042 7f0844dff640                       Options.WAL_size_limit_MB: 0
2025/12/24-11:02:36.517043 7f0844dff640                        Options.max_write_batch_group_size_bytes: 1048576
2025/12/24-11:02:36.517044 7f0844dff640             Options.manifest_preallocation_size: 4194304
2025/12/24-11:02:36.517045 7f0844dff640                     Options.is_fd_close_on_exec: 1
2025/12/24-11:02:36.517046 7f0844dff640                   Options.advise_random_on_open: 1
2025/12/24-11:02:36.517047 7f0844dff640                    Options.db_write_buffer_size: 0
2025/12/24-11:02:36.517048 7f0844dff640                    Options.write_buffer_manager: 0x7f077c2a4310
2025/12/24-11:02:36.517048 7f0844dff640         Options.access_hint_on_compaction_start: 1
2025/12/24-11:02:36.517049 7f0844dff640  Options.new_table_reader_for_compaction_inputs: 0
2025/12/24-11:02:36.517050 7f0844dff640           Options.random_access_max_buffer_size: 1048576
2025/12/24-11:02:36.517051 7f0844dff640                      Options.use_adaptive_mutex: 0
2025/12/24-11:02:36.517052 7f0844dff640                            Options.rate_limiter: (nil)
2025/12/24-11:02:36.517053 7f0844dff640     Options.sst_file_manager.rate_bytes_per_sec: 0
2025/12/24-11:02:36.517059 7f0844dff640                       Options.wal_recovery_mode: 2
2025/12/24-11:02:36.517060 7f0844dff640                  Options.enable_thread_tracking: 0
2025/12/24-11:02:36.517060 7f0844dff640                  Options.enable_pipelined_write: 0
2025/12/24-11:02:36.517061 7f0844dff640                  Options.unordered_write: 0
2025/12/24-11:02:36.517062 7f0844dff640         Options.allow_concurrent_memtable_write: 1
2025/12/24-11:02:36.517063 7f0844dff640      Options.enable_write_thread_adaptive_yield: 1
2025/12/24-11:02:36.517064 7f0844dff640             Options.write_thread_max_yield_usec: 100
2025/12/24-11:02:36.517065 7f0844dff640            Options.write_thread_slow_yield_usec: 3
2025/12/24-11:02:36.517065 7f0844dff640                               Options.row_cache: None
2025/12/24-11:02:36.517066 7f0844dff640                              Options.wal_filter: None
2025/12/24-11:02:36.517067 7f0844dff640             Options.avoid_flush_during_recovery: 0
2025/12/24-11:02:36.517068 7f0844dff640             Options.allow_ingest_behind: 0
2025/12/24-11:02:36.517069 7f0844dff640             Options.preserve_deletes: 0
2025/12/24-11:02:36.517070 7f0844dff640             Options.two_write_queues: 0
2025/12/24-11:02:36.517071 7f0844dff640             Options.manual_wal_flush: 0
2025/12/24-11:02:36.517071 7f0844dff640             Options.atomic_flush: 0
2025/12/24-11:02:36.517072 7f0844dff640             Options.avoid_unnecessary_blocking_io: 0
2025/12/24-11:02:36.517073 7f0844dff640                 Options.persist_stats_to_disk: 0
2025/12/24-11:02:36.517074 7f0844dff640                 Options.write_dbid_to_manifest: 0
2025/12/24-11:02:36.517075 7f0844dff640                 Options.log_readahead_size: 0
2025/12/24-11:02:36.517076 7f0844dff640                 Options.file_checksum_gen_factory: Unknown
2025/12/24-11:02:36.517076 7f0844dff640                 Options.best_efforts_recovery: 0
2025/12/24-11:02:36.517077 7f0844dff640                Options.max_bgerror_resume_count: 2147483647
2025/12/24-11:02:36.517078 7f0844dff640            Options.bgerror_resume_retry_interval: 1000000
2025/12/24-11:02:36.517079 7f0844dff640             Options.allow_data_in_errors: 0
2025/12/24-11:02:36.517080 7f0844dff640             Options.db_host_id: __hostname__
2025/12/24-11:02:36.517082 7f0844dff640             Options.max_background_jobs: 2
2025/12/24-11:02:36.517083 7f0844dff640             Options.max_background_compactions: -1
2025/12/24-11:02:36.517092 7f0844dff640             Options.max_subcompactions: 1
2025/12/24-11:02:36.517093 7f0844dff640             Options.avoid_flush_during_shutdown: 0
2025/12/24-11:02:36.517094 7f0844dff640           Options.writable_file_max_buffer_size: 1048576
2025/12/24-11:02:36.517095 7f0844dff640             Options.delayed_write_rate : 16777216
2025/12/24-11:02:36.517096 7f0844dff640             Options.max_total_wal_size: 0
2025/12/24-11:02:36.517097 7f0844dff640             Options.delete_obsolete_files_period_micros: 21600000000
2025/12/24-11:02:36.517098 7f0844dff640                   Options.stats_dump_period_sec: 0
2025/12/24-11:02:36.517099 7f0844dff640                 Options.stats_persist_period_sec: 600
2025/12/24-11:02:36.517099 7f0844dff640                 Options.stats_history_buffer_size: 1048576
2025/12/24-11:02:36.517100 7f0844dff640                          Options.max_open_files: -1
2025/12/24-11:02:36.517101 7f0844dff640                          Options.bytes_per_sync: 0
2025/12/24-11:02:36.517102 7f0844dff640                      Options.wal_bytes_per_sync: 0
2025/12/24-11:02:36.517103 7f0844dff640                   Options.strict_bytes_per_sync: 0
2025/12/24-11:02:36.517104 7f0844dff640       Options.compaction_readahead_size: 0
2025/12/24-11:02:36.517105 7f0844dff640                  Options.max_background_flushes: -1
2025/12/24-11:02:36.517105 7f0844dff640 Compression algorithms supported:
2025/12/24-11:02:36.517106 7f0844dff640 	kZSTDNotFinalCompression supported: 1
2025/12/24-11:02:36.517108 7f0844dff640 	kZSTD supported: 1
2025/12/24-11:02:36.517108 7f0844dff640 	kXpressCompression supported: 0
2025/12/24-11:02:36.517114 7f0844dff640 	kLZ4HCCompression supported: 1
2025/12/24-11:02:36.517115 7f0844dff640 	kLZ4Compression supported: 1
2025/12/24-11:02:36.517116 7f0844dff640 	kBZip2Compression supported: 1
2025/12/24-11:02:36.517117 7f0844dff640 	kZlibCompression supported: 1
2025/12/24-11:02:36.517118 7f0844dff640 	kSnappyCompression supported: 1
2025/12/24-11:02:36.517120 7f0844dff640 Fast CRC32 supported: Not supported on x86
2025/12/24-11:02:36.553408 7f0844dff640 [/db_impl/db_impl_open.cc:285] Creating manifest 1
2025/12/24-11:02:36.570130 7f0844dff640 [/version_set.cc:4627] Recovering from manifest file: /tmp/fluss/data/my_db/user_table-0/kv-0/db/MANIFEST-000001
2025/12/24-11:02:36.570292 7f0844dff640 [/column_family.cc:598] --------------- Options for column family [default]:
2025/12/24-11:02:36.570295 7f0844dff640               Options.comparator: leveldb.BytewiseComparator
2025/12/24-11:02:36.570296 7f0844dff640           Options.merge_operator: None
2025/12/24-11:02:36.570297 7f0844dff640        Options.compaction_filter: None
2025/12/24-11:02:36.570298 7f0844dff640        Options.compaction_filter_factory: None
2025/12/24-11:02:36.570299 7f0844dff640  Options.sst_partitioner_factory: None
2025/12/24-11:02:36.570300 7f0844dff640         Options.memtable_factory: SkipListFactory
2025/12/24-11:02:36.570301 7f0844dff640            Options.table_factory: BlockBasedTable
2025/12/24-11:02:36.570329 7f0844dff640            table_factory options:   flush_block_policy_factory: FlushBlockBySizePolicyFactory (0x7f077c26e960)
  cache_index_and_filter_blocks: 0
  cache_index_and_filter_blocks_with_high_priority: 1
  pin_l0_filter_and_index_blocks_in_cache: 0
  pin_top_level_index_and_filter: 1
  index_type: 0
  data_block_index_type: 0
  index_shortening: 1
  data_block_hash_table_util_ratio: 0.750000
  hash_index_allow_collision: 1
  checksum: 1
  no_block_cache: 0
  block_cache: 0x7f077c254d40
  block_cache_name: LRUCache
  block_cache_options:
    capacity : 8388608
    num_shard_bits : 4
    strict_capacity_limit : 0
    memory_allocator : None
    high_pri_pool_ratio: 0.500
  block_cache_compressed: (nil)
  persistent_cache: (nil)
  block_size: 4096
  block_size_deviation: 10
  block_restart_interval: 16
  index_block_restart_interval: 1
  metadata_block_size: 4096
  partition_filters: 0
  use_delta_encoding: 1
  filter_policy: rocksdb.BuiltinBloomFilter
  whole_key_filtering: 1
  verify_compression: 0
  read_amp_bytes_per_bit: 0
  format_version: 5
  enable_index_compression: 1
  block_align: 0
2025/12/24-11:02:36.570331 7f0844dff640        Options.write_buffer_size: 67108864
2025/12/24-11:02:36.570332 7f0844dff640  Options.max_write_buffer_number: 2
2025/12/24-11:02:36.570333 7f0844dff640        Options.compression[0]: LZ4
2025/12/24-11:02:36.570334 7f0844dff640        Options.compression[1]: LZ4
2025/12/24-11:02:36.570335 7f0844dff640        Options.compression[2]: LZ4
2025/12/24-11:02:36.570336 7f0844dff640        Options.compression[3]: LZ4
2025/12/24-11:02:36.570337 7f0844dff640        Options.compression[4]: LZ4
2025/12/24-11:02:36.570338 7f0844dff640        Options.compression[5]: ZSTD
2025/12/24-11:02:36.570339 7f0844dff640        Options.compression[6]: ZSTD
2025/12/24-11:02:36.570340 7f0844dff640                  Options.bottommost_compression: Disabled
2025/12/24-11:02:36.570341 7f0844dff640       Options.prefix_extractor: nullptr
2025/12/24-11:02:36.570342 7f0844dff640   Options.memtable_insert_with_hint_prefix_extractor: nullptr
2025/12/24-11:02:36.570343 7f0844dff640             Options.num_levels: 7
2025/12/24-11:02:36.570344 7f0844dff640        Options.min_write_buffer_number_to_merge: 1
2025/12/24-11:02:36.570345 7f0844dff640     Options.max_write_buffer_number_to_maintain: 0
2025/12/24-11:02:36.570345 7f0844dff640     Options.max_write_buffer_size_to_maintain: 0
2025/12/24-11:02:36.570346 7f0844dff640            Options.bottommost_compression_opts.window_bits: -14
2025/12/24-11:02:36.570347 7f0844dff640                  Options.bottommost_compression_opts.level: 32767
2025/12/24-11:02:36.570358 7f0844dff640               Options.bottommost_compression_opts.strategy: 0
2025/12/24-11:02:36.570359 7f0844dff640         Options.bottommost_compression_opts.max_dict_bytes: 0
2025/12/24-11:02:36.570360 7f0844dff640         Options.bottommost_compression_opts.zstd_max_train_bytes: 0
2025/12/24-11:02:36.570360 7f0844dff640         Options.bottommost_compression_opts.parallel_threads: 1
2025/12/24-11:02:36.570361 7f0844dff640                  Options.bottommost_compression_opts.enabled: false
2025/12/24-11:02:36.570362 7f0844dff640         Options.bottommost_compression_opts.max_dict_buffer_bytes: 0
2025/12/24-11:02:36.570363 7f0844dff640            Options.compression_opts.window_bits: -14
2025/12/24-11:02:36.570367 7f0844dff640                  Options.compression_opts.level: 32767
2025/12/24-11:02:36.570368 7f0844dff640               Options.compression_opts.strategy: 0
2025/12/24-11:02:36.570369 7f0844dff640         Options.compression_opts.max_dict_bytes: 0
2025/12/24-11:02:36.570369 7f0844dff640         Options.compression_opts.zstd_max_train_bytes: 0
2025/12/24-11:02:36.570370 7f0844dff640         Options.compression_opts.parallel_threads: 1
2025/12/24-11:02:36.570371 7f0844dff640                  Options.compression_opts.enabled: false
2025/12/24-11:02:36.570372 7f0844dff640         Options.compression_opts.max_dict_buffer_bytes: 0
2025/12/24-11:02:36.570373 7f0844dff640      Options.level0_file_num_compaction_trigger: 4
2025/12/24-11:02:36.570374 7f0844dff640          Options.level0_slowdown_writes_trigger: 20
2025/12/24-11:02:36.570375 7f0844dff640              Options.level0_stop_writes_trigger: 36
2025/12/24-11:02:36.570376 7f0844dff640                   Options.target_file_size_base: 67108864
2025/12/24-11:02:36.570376 7f0844dff640             Options.target_file_size_multiplier: 1
2025/12/24-11:02:36.570377 7f0844dff640                Options.max_bytes_for_level_base: 268435456
2025/12/24-11:02:36.570378 7f0844dff640 Options.level_compaction_dynamic_level_bytes: 0
2025/12/24-11:02:36.570380 7f0844dff640          Options.max_bytes_for_level_multiplier: 10.000000
2025/12/24-11:02:36.570382 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[0]: 1
2025/12/24-11:02:36.570383 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[1]: 1
2025/12/24-11:02:36.570383 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[2]: 1
2025/12/24-11:02:36.570384 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[3]: 1
2025/12/24-11:02:36.570385 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[4]: 1
2025/12/24-11:02:36.570386 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[5]: 1
2025/12/24-11:02:36.570387 7f0844dff640 Options.max_bytes_for_level_multiplier_addtl[6]: 1
2025/12/24-11:02:36.570388 7f0844dff640       Options.max_sequential_skip_in_iterations: 8
2025/12/24-11:02:36.570389 7f0844dff640                    Options.max_compaction_bytes: 1677721600
2025/12/24-11:02:36.570389 7f0844dff640                        Options.arena_block_size: 8388608
2025/12/24-11:02:36.570390 7f0844dff640   Options.soft_pending_compaction_bytes_limit: 68719476736
2025/12/24-11:02:36.570391 7f0844dff640   Options.hard_pending_compaction_bytes_limit: 274877906944
2025/12/24-11:02:36.570392 7f0844dff640       Options.rate_limit_delay_max_milliseconds: 100
2025/12/24-11:02:36.570393 7f0844dff640                Options.disable_auto_compactions: 0
2025/12/24-11:02:36.570394 7f0844dff640                        Options.compaction_style: kCompactionStyleLevel
2025/12/24-11:02:36.570395 7f0844dff640                          Options.compaction_pri: kMinOverlappingRatio
2025/12/24-11:02:36.570396 7f0844dff640 Options.compaction_options_universal.size_ratio: 1
2025/12/24-11:02:36.570397 7f0844dff640 Options.compaction_options_universal.min_merge_width: 2
2025/12/24-11:02:36.570398 7f0844dff640 Options.compaction_options_universal.max_merge_width: 4294967295
2025/12/24-11:02:36.570399 7f0844dff640 Options.compaction_options_universal.max_size_amplification_percent: 200
2025/12/24-11:02:36.570403 7f0844dff640 Options.compaction_options_universal.compression_size_percent: -1
2025/12/24-11:02:36.570404 7f0844dff640 Options.compaction_options_universal.stop_style: kCompactionStopStyleTotalSize
2025/12/24-11:02:36.570405 7f0844dff640 Options.compaction_options_fifo.max_table_files_size: 1073741824
2025/12/24-11:02:36.570405 7f0844dff640 Options.compaction_options_fifo.allow_compaction: 0
2025/12/24-11:02:36.570408 7f0844dff640                   Options.table_properties_collectors:
2025/12/24-11:02:36.570409 7f0844dff640                   Options.inplace_update_support: 0
2025/12/24-11:02:36.570410 7f0844dff640                 Options.inplace_update_num_locks: 10000
2025/12/24-11:02:36.570411 7f0844dff640               Options.memtable_prefix_bloom_size_ratio: 0.000000
2025/12/24-11:02:36.570412 7f0844dff640               Options.memtable_whole_key_filtering: 0
2025/12/24-11:02:36.570413 7f0844dff640   Options.memtable_huge_page_size: 0
2025/12/24-11:02:36.570414 7f0844dff640                           Options.bloom_locality: 0
2025/12/24-11:02:36.570414 7f0844dff640                    Options.max_successive_merges: 0
2025/12/24-11:02:36.570415 7f0844dff640                Options.optimize_filters_for_hits: 0
2025/12/24-11:02:36.570416 7f0844dff640                Options.paranoid_file_checks: 0
2025/12/24-11:02:36.570417 7f0844dff640                Options.force_consistency_checks: 1
2025/12/24-11:02:36.570418 7f0844dff640                Options.report_bg_io_stats: 0
2025/12/24-11:02:36.570418 7f0844dff640                               Options.ttl: 2592000
2025/12/24-11:02:36.570419 7f0844dff640          Options.periodic_compaction_seconds: 0
2025/12/24-11:02:36.570420 7f0844dff640                    Options.enable_blob_files: false
2025/12/24-11:02:36.570421 7f0844dff640                        Options.min_blob_size: 0
2025/12/24-11:02:36.570422 7f0844dff640                       Options.blob_file_size: 268435456
2025/12/24-11:02:36.570423 7f0844dff640                Options.blob_compression_type: NoCompression
2025/12/24-11:02:36.570424 7f0844dff640       Options.enable_blob_garbage_collection: false
2025/12/24-11:02:36.570425 7f0844dff640   Options.blob_garbage_collection_age_cutoff: 0.250000
2025/12/24-11:02:36.571357 7f0844dff640 [/version_set.cc:4675] Recovered from manifest file:/tmp/fluss/data/my_db/user_table-0/kv-0/db/MANIFEST-000001 succeeded,manifest_file_number is 1, next_file_number is 3, last_sequence is 0, log_number is 0,prev_log_number is 0,max_column_family is 0,min_log_number_to_keep is 0
2025/12/24-11:02:36.571361 7f0844dff640 [/version_set.cc:4684] Column family [default] (ID 0), log number is 0
2025/12/24-11:02:36.571424 7f0844dff640 [/version_set.cc:4119] Creating manifest 4
2025/12/24-11:02:36.585022 7f0844dff640 [/db_impl/db_impl_open.cc:1757] SstFileManager instance 0x7f077c07ce20
2025/12/24-11:02:36.585039 7f0844dff640 DB pointer 0x7f077c2a7eb0
root@tablet-server-2:/opt/fluss/log/rocksdb# cd /tmp/
root@tablet-server-2:/tmp# ls
fluss  fluss--tablet-server.pid  hsperfdata_root  librocksdbjni5757486990182115217.so  rocksdb-lib-5f89e23f-a78a-47d4-99d7-6311591281d0
root@tablet-server-2:/tmp# cd fluss
root@tablet-server-2:/tmp/fluss# ls
data
root@tablet-server-2:/tmp/fluss# cd data/
root@tablet-server-2:/tmp/fluss/data# ls
high-watermark-checkpoint  lost+found  my_db  recovery-point-offset-checkpoint  remote-log-index-cache
root@tablet-server-2:/tmp/fluss/data# cd my_db/
root@tablet-server-2:/tmp/fluss/data/my_db# ls
user_table-0
root@tablet-server-2:/tmp/fluss/data/my_db# cd user_table-0/
root@tablet-server-2:/tmp/fluss/data/my_db/user_table-0# ls
kv-0  log-0
root@tablet-server-2:/tmp/fluss/data/my_db/user_table-0# ls kv-0/
db
root@tablet-server-2:/tmp/fluss/data/my_db/user_table-0# ls kv-0/db/
000005.log  CURRENT  IDENTITY  LOCK  MANIFEST-000004  OPTIONS-000007
root@tablet-server-2:/tmp/fluss/data/my_db/user_table-0# ls log-0/
00000000000000000000.index  00000000000000000000.log  00000000000000000000.timeindex
```

- coordinator-server log
```
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:zookeeper.version=3.8.3-6ad6d364c7c0bcf0de452d54ebefa3058098ab56, built on 2023-10-05 10:34 UTC
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:host.name=coordinator-server-0.coordinator-server-hs.fluss.svc.cluster.local
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.version=17.0.17
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.vendor=Eclipse Adoptium
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.home=/opt/java/openjdk
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.class.path=/opt/fluss/lib/log4j-1.2-api-2.17.1.jar:/opt/fluss/lib/log4j-api-2.17.1.jar:/opt/fluss/lib/log4j-core-2.17.1.jar:/opt/fluss/lib/log4j-slf4j-impl-2.17.1.jar:/opt/fluss/lib/fluss-server-0.8.0-incubating.jar
2025-12-24 10:53:43,950 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.library.path=/usr/java/packages/lib:/usr/lib64:/lib64:/lib:/usr/lib
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.io.tmpdir=/tmp
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:java.compiler=<NA>
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:os.name=Linux
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:os.arch=amd64
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:os.version=4.18.0-372.9.1.el8.x86_64
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:user.name=root
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:user.home=/root
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:user.dir=/opt/fluss
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:os.memory.free=365MB
2025-12-24 10:53:43,951 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:os.memory.max=30688MB
2025-12-24 10:53:43,952 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Client environment:os.memory.total=480MB
2025-12-24 10:53:43,953 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ZooKeeper [] - Initiating client connection, connectString=zookeeper-client.fluss.svc.cluster.local:2181 sessionTimeout=60000 watcher=org.apache.fluss.shaded.curator5.org.apache.curator.ConnectionState@646c0a67
2025-12-24 10:53:43,956 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.common.X509Util [] - Setting -D jdk.tls.rejectClientInitiatedRenegotiation=true to disable client-initiated TLS renegotiation
2025-12-24 10:53:43,959 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxnSocket [] - jute.maxbuffer value is 1048575 Bytes
2025-12-24 10:53:43,965 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - zookeeper.request.timeout value is 0. feature enabled=false
2025-12-24 10:53:43,970 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.imps.CuratorFrameworkImpl [] - Default schema
2025-12-24 10:53:43,972 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - Opening socket connection to server zookeeper-client.fluss.svc.cluster.local/10.233.55.173:2181.
2025-12-24 10:53:43,973 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - SASL config status: Will not attempt to authenticate using SASL (unknown error)
2025-12-24 10:53:43,973 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: hdfs
2025-12-24 10:53:43,973 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: iceberg
2025-12-24 10:53:43,974 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: jmx
2025-12-24 10:53:43,974 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: lance
2025-12-24 10:53:43,974 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: oss
2025-12-24 10:53:43,974 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: paimon
2025-12-24 10:53:43,974 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: prometheus
2025-12-24 10:53:43,974 INFO  org.apache.fluss.plugin.DefaultPluginManager                 [] - Plugin loader with ID found, reusing it: s3
2025-12-24 10:53:43,978 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - Socket connection established, initiating session, client: /10.233.67.125:41986, server: zookeeper-client.fluss.svc.cluster.local/10.233.55.173:2181
2025-12-24 10:53:43,984 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - Session establishment complete on server zookeeper-client.fluss.svc.cluster.local/10.233.55.173:2181, session id = 0x30cda06e8470074, negotiated timeout = 40000
2025-12-24 10:53:43,988 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.state.ConnectionStateManager [] - State change: CONNECTED
2025-12-24 10:53:43,999 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.imps.EnsembleTracker [] - New config event received: {server.2=zookeeper-1.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.1=zookeeper-0.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.3=zookeeper-2.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, version=100000010}
2025-12-24 10:53:44,005 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.utils.Compatibility [] - Using org.apache.zookeeper.server.quorum.MultipleAddresses
2025-12-24 10:53:44,006 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.imps.EnsembleTracker [] - New config event received: {server.2=zookeeper-1.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.1=zookeeper-0.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.3=zookeeper-2.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, version=100000010}
2025-12-24 10:53:44,014 WARN  org.apache.fluss.lake.iceberg.conf.HadoopUtils               [] - Could not find Hadoop configuration via any of the supported methods (Fluss configuration, environment variables).
2025-12-24 10:53:44,145 INFO  org.apache.iceberg.CatalogUtil                               [] - Loading custom FileIO implementation: org.apache.iceberg.aws.s3.S3FileIO
2025-12-24 10:53:44,230 INFO  org.apache.fluss.server.coordinator.LakeTableTieringManager$LakeTieringExpiredOperationReaper [] - Starting
2025-12-24 10:53:44,268 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Starting Netty server on endpoints [INTERNAL://0.0.0.0:9123, CLIENT://0.0.0.0:9124] with 3 network threads and 8 worker threads.
2025-12-24 10:53:44,375 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Listening on address 0:0:0:0:0:0:0:0%0 and port 9123 for FLUSS protocol
2025-12-24 10:53:44,377 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Listening on address 0:0:0:0:0:0:0:0%0 and port 9124 for FLUSS protocol
2025-12-24 10:53:44,377 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Successfully start Netty server (took 108 ms). Listening on endpoints [INTERNAL://0:0:0:0:0:0:0:0%0:9123, CLIENT://0:0:0:0:0:0:0:0%0:9124].
2025-12-24 10:53:44,467 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:47,472 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:50,478 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:53,483 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:56,487 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:59,491 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:02,496 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:05,499 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:08,503 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:11,507 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:14,511 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:17,515 WARN  org.apache.fluss.server.coordinator.CoordinatorServer        [] - Coordinator server already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:20,520 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Registered leader CoordinatorAddress{id='bd51c79c-cc56-4722-a2cf-bac0d831279d', endpoints=[INTERNAL://0:0:0:0:0:0:0:0%0:9123, CLIENT://coordinator-server-0.coordinator-server-hs.fluss.svc.cluster.local:9124]} at path /coordinators/active.
2025-12-24 10:54:20,527 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Auto partitioning task is scheduled at fixed interval 300000ms.
2025-12-24 10:54:20,553 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Initializing coordinator context.
2025-12-24 10:54:20,558 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_ADDED event for server 0.
2025-12-24 10:54:20,560 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_ADDED event for server 1.
2025-12-24 10:54:20,560 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Load tablet servers success in 5ms when initializing coordinator context.
2025-12-24 10:54:20,560 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_ADDED event for server 2.
2025-12-24 10:54:20,571 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Load tables success in 6ms when initializing coordinator context.
2025-12-24 10:54:20,574 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Load table and partition assignment success in 2ms when initializing coordinator context.
2025-12-24 10:54:20,575 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Current total 0 tables in the cluster.
2025-12-24 10:54:20,575 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Detect tables ] to be deleted after initializing coordinator context. 
2025-12-24 10:54:20,575 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Detect partition ] to be deleted after initializing coordinator context. 
2025-12-24 10:54:20,575 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - End initializing coordinator context, cost 22ms
2025-12-24 10:54:20,585 INFO  org.apache.fluss.server.coordinator.TableManager             [] - Start up table manager.
2025-12-24 10:54:20,585 INFO  org.apache.fluss.server.coordinator.statemachine.ReplicaStateMachine [] - Initializing replica state machine.
2025-12-24 10:54:20,586 INFO  org.apache.fluss.server.coordinator.statemachine.ReplicaStateMachine [] - Triggering online replica state changes
2025-12-24 10:54:20,588 INFO  org.apache.fluss.server.coordinator.statemachine.ReplicaStateMachine [] - Triggering offline replica state changes
2025-12-24 10:54:20,588 INFO  org.apache.fluss.server.coordinator.statemachine.TableBucketStateMachine [] - Initializing bucket state machine.
2025-12-24 10:54:20,589 INFO  org.apache.fluss.server.coordinator.statemachine.TableBucketStateMachine [] - Triggering online table bucket changes
2025-12-24 10:54:20,592 INFO  org.apache.fluss.server.coordinator.event.CoordinatorEventManager$CoordinatorEventThread [] - Starting
2025-12-24 10:54:59,941 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_REMOVED event for server 1.
2025-12-24 10:54:59,942 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Tablet server failure callback for 1.
2025-12-24 10:55:01,242 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_ADDED event for server 1.
2025-12-24 10:55:01,242 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - New tablet server callback for tablet server 1
2025-12-24 10:55:45,939 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_REMOVED event for server 0.
2025-12-24 10:55:45,940 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - Tablet server failure callback for 0.
2025-12-24 10:55:48,280 INFO  org.apache.fluss.server.coordinator.event.watcher.TabletServerChangeWatcher [] - Received CHILD_ADDED event for server 0.
2025-12-24 10:55:48,280 INFO  org.apache.fluss.server.coordinator.CoordinatorEventProcessor [] - New tablet server callback for tablet server 0
2025-12-24 10:59:20,529 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T10:59:20.527Z.
2025-12-24 11:01:51,065 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Registered database my_db
2025-12-24 11:02:36,281 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Registered new schema version 1 for table my_db.user_table.
2025-12-24 11:02:36,296 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Registered table assignment {0=[2, 1, 0]} for table id 0.
2025-12-24 11:02:36,307 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Registered table user_table for database my_db
2025-12-24 11:02:36,313 INFO  org.apache.fluss.server.coordinator.TableManager             [] - New table: my_db.user_table with id 0, new table bucket assignment {0=[2, 1, 0]}.
2025-12-24 11:02:36,314 INFO  org.apache.fluss.server.coordinator.TableManager             [] - New table buckets: [TableBucket{tableId=0, bucket=0}] for table my_db.user_table.
2025-12-24 11:02:36,323 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Batch Register LeaderAndIsr{leader=2, leaderEpoch=0, isr=[2, 1, 0], coordinatorEpoch=0, bucketEpoch=0} for bucket TableBucket{tableId=0, bucket=0} in Zookeeper.
2025-12-24 11:02:36,336 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Batch registered leadAndIsr for tableId: 0, partitionId: null, partitionName: null  in Zookeeper.
2025-12-24 11:04:20,529 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T11:04:20.529Z.
2025-12-24 11:09:20,530 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T11:09:20.530Z.
2025-12-24 11:14:20,530 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T11:14:20.530Z.
2025-12-24 11:19:20,531 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T11:19:20.531Z.
2025-12-24 11:24:20,532 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T11:24:20.532Z.
2025-12-24 11:29:20,532 INFO  org.apache.fluss.server.coordinator.AutoPartitionManager     [] - Start auto partitioning for 0 tables at 2025-12-24T11:29:20.532Z.
```

- tablet-server log
```
2025-12-24 10:53:45,350 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - SASL config status: Will not attempt to authenticate using SASL (unknown error)
2025-12-24 10:53:45,357 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - Socket connection established, initiating session, client: /10.233.64.163:54334, server: zookeeper-client.fluss.svc.cluster.local/10.233.55.173:2181
2025-12-24 10:53:45,363 INFO  org.apache.fluss.shaded.zookeeper3.org.apache.zookeeper.ClientCnxn [] - Session establishment complete on server zookeeper-client.fluss.svc.cluster.local/10.233.55.173:2181, session id = 0x30cda06e8470075, negotiated timeout = 40000
2025-12-24 10:53:45,367 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.state.ConnectionStateManager [] - State change: CONNECTED
2025-12-24 10:53:45,375 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.imps.EnsembleTracker [] - New config event received: {server.2=zookeeper-1.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.1=zookeeper-0.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.3=zookeeper-2.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, version=100000010}
2025-12-24 10:53:45,380 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.utils.Compatibility [] - Using org.apache.zookeeper.server.quorum.MultipleAddresses
2025-12-24 10:53:45,381 INFO  org.apache.fluss.shaded.curator5.org.apache.curator.framework.imps.EnsembleTracker [] - New config event received: {server.2=zookeeper-1.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.1=zookeeper-0.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, server.3=zookeeper-2.zookeeper-headless.fluss.svc.cluster.local:2888:3888:participant;0.0.0.0:2181, version=100000010}
2025-12-24 10:53:45,394 INFO  org.apache.fluss.server.log.LogManager                       [] - Loading logs from dir /tmp/fluss/data
2025-12-24 10:53:45,404 INFO  org.apache.fluss.server.log.LogManager                       [] - No logs found to be loaded in /tmp/fluss/data
2025-12-24 10:53:45,404 INFO  org.apache.fluss.server.log.LogManager                       [] - log loader complete. Total success loaded log count is 0, Take 0 ms
2025-12-24 10:53:45,407 INFO  org.apache.fluss.shaded.arrow.org.apache.arrow.memory.BaseAllocator [] - Debug mode disabled. Enable with the VM option -Darrow.memory.debug.allocator=true.
2025-12-24 10:53:45,409 INFO  org.apache.fluss.shaded.arrow.org.apache.arrow.memory.DefaultAllocationManagerOption [] - allocation manager type not specified, using netty as the default type
2025-12-24 10:53:45,409 INFO  org.apache.fluss.shaded.arrow.org.apache.arrow.memory.CheckAllocator [] - Using DefaultAllocationManager at memory/DefaultAllocationManagerFactory.class
2025-12-24 10:53:45,544 INFO  org.apache.fluss.server.log.remote.RemoteLogIndexCache       [] - RemoteIndexCache directory /tmp/fluss/data/remote-log-index-cache already exists. Re-using the same directory.
2025-12-24 10:53:45,544 INFO  org.apache.fluss.server.log.remote.RemoteLogIndexCache       [] - RemoteIndexCache starts up in 0 ms.
2025-12-24 10:53:45,545 INFO  org.apache.fluss.server.log.remote.RemoteLogIndexCache$1     [] - Starting
2025-12-24 10:53:45,548 INFO  org.apache.fluss.server.replica.delay.DelayedOperationManager$ExpiredOperationReaper [] - Starting
2025-12-24 10:53:45,548 INFO  org.apache.fluss.server.replica.delay.DelayedOperationManager$ExpiredOperationReaper [] - Starting
2025-12-24 10:53:45,586 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Starting Netty server on endpoints [INTERNAL://10.233.64.163:9123, CLIENT://0.0.0.0:9124] with 3 network threads and 8 worker threads.
2025-12-24 10:53:45,613 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Listening on address 10.233.64.163 and port 9123 for FLUSS protocol
2025-12-24 10:53:45,615 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Listening on address 0:0:0:0:0:0:0:0%0 and port 9124 for FLUSS protocol
2025-12-24 10:53:45,615 INFO  org.apache.fluss.rpc.netty.server.NettyServer                [] - Successfully start Netty server (took 29 ms). Listening on endpoints [INTERNAL://10.233.64.163:9123, CLIENT://0:0:0:0:0:0:0:0%0:9124].
2025-12-24 10:53:45,721 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:48,725 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:51,728 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:54,733 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:53:57,737 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:00,741 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:03,745 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:06,750 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:09,754 WARN  org.apache.fluss.server.tablet.TabletServer                  [] - Tablet server id 2 already registered in Zookeeper. retrying register after 3000 ms....
2025-12-24 10:54:12,763 INFO  org.apache.fluss.server.zk.ZooKeeperClient                   [] - Registered tablet server 2 at path /tabletservers/ids/2 with registration TabletServerRegistration{endpoints=[INTERNAL://10.233.64.163:9123, CLIENT://tablet-server-2.tablet-server-hs.fluss.svc.cluster.local:9124], registerTimestamp=1766573625615, rack='null}.
2025-12-24 11:02:36,410 INFO  org.apache.fluss.server.log.LogTablet                        [] - Loading writer state for bucket TableBucket{tableId=0, bucket=0} till offset 0
2025-12-24 11:02:36,414 INFO  org.apache.fluss.server.log.LogManager                       [] - Loaded log for bucket TableBucket{tableId=0, bucket=0} in dir /tmp/fluss/data/my_db/user_table-0/log-0
2025-12-24 11:02:36,414 INFO  org.apache.fluss.server.replica.Replica                      [] - No local checkpoint high watermark found for table bucket TableBucket{tableId=0, bucket=0}
2025-12-24 11:02:36,415 INFO  org.apache.fluss.server.replica.Replica                      [] - Log loaded for bucket TableBucket{tableId=0, bucket=0} with initial high watermark 0
2025-12-24 11:02:36,417 INFO  org.apache.fluss.server.replica.fetcher.ReplicaFetcherManager [] - Remove fetcher for buckets: [TableBucket{tableId=0, bucket=0}]
2025-12-24 11:02:36,419 INFO  org.apache.fluss.server.log.LogTablet                        [] - Update leaderEndOffsetSnapshot to 0 for tb TableBucket{tableId=0, bucket=0} while become leader
2025-12-24 11:02:36,419 INFO  org.apache.fluss.server.replica.Replica                      [] - Start to init kv tablet for TableBucket{tableId=0, bucket=0} of table my_db.user_table.
2025-12-24 11:02:36,423 INFO  org.apache.fluss.server.replica.Replica                      [] - No snapshot found for TableBucket{tableId=0, bucket=0} of my_db.user_table, restore from log.
2025-12-24 11:02:36,512 INFO  org.apache.fluss.server.kv.rocksdb.RocksDBKvBuilder          [] - Attempting to load RocksDB native library and store it under '/tmp'
2025-12-24 11:02:36,513 INFO  org.apache.fluss.server.kv.rocksdb.RocksDBKvBuilder          [] - Successfully loaded RocksDB native library
2025-12-24 11:02:36,585 INFO  org.apache.fluss.server.kv.rocksdb.RocksDBKvBuilder          [] - Finished building RocksDB kv at /tmp/fluss/data/my_db/user_table-0/kv-0.
2025-12-24 11:02:36,588 INFO  org.apache.fluss.server.kv.KvManager                         [] - Created kv tablet for bucket TableBucket{tableId=0, bucket=0} in dir /tmp/fluss/data/my_db/user_table-0/kv-0.
2025-12-24 11:02:36,590 INFO  org.apache.fluss.server.replica.Replica                      [] - Recover kv tablet for TableBucket{tableId=0, bucket=0} of table my_db.user_table from log offset 0 finish, cost 2 ms.
2025-12-24 11:02:36,590 INFO  org.apache.fluss.server.replica.Replica                      [] - Init kv tablet for my_db.user_table of TableBucket{tableId=0, bucket=0} finish, cost 171 ms.
2025-12-24 11:02:36,595 INFO  org.apache.fluss.server.kv.snapshot.PeriodicSnapshotManager  [] - TableBucket TableBucket{tableId=0, bucket=0} starts periodic snapshot
2025-12-24 11:02:36,595 INFO  org.apache.fluss.server.replica.Replica                      [] - TabletServer 2 becomes leader for bucket TableBucket{tableId=0, bucket=0}
2025-12-24 11:02:36,602 INFO  org.apache.fluss.server.log.remote.RemoteLogManager          [] - Created a new remote log task for table-bucketTableBucket{tableId=0, bucket=0}: class org.apache.fluss.server.log.remote.LogTieringTask[TableBucket{tableId=0, bucket=0}] and getting scheduled
2025-12-24 11:02:39,470 INFO  org.apache.fluss.server.log.remote.LogTieringTask            [] - Found the copied remote log end offset: -1 for bucket TableBucket{tableId=0, bucket=0} after becoming leader
2025-12-24 11:12:36,621 WARN  org.apache.fluss.rpc.netty.server.NettyServerHandler         [] - Connection /10.233.67.125:56404 is idle, closing...
2025-12-24 11:55:12,161 WARN  com.amazonaws.services.s3.internal.Mimetypes                 [] - Unable to find 'mime.types' file in classpath
2025-12-24 11:55:12,289 ERROR org.apache.fluss.rpc.netty.client.ServerConnection           [] - Failed to establish connection to server 0:0:0:0:0:0:0:0%0:9123 (id: cs-0, rack: null).
org.apache.fluss.shaded.netty4.io.netty.channel.AbstractChannel$AnnotatedConnectException: finishConnect(..) failed: Connection refused: /[0:0:0:0:0:0:0:0%0]:9123
Caused by: java.net.ConnectException: finishConnect(..) failed: Connection refused
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Errors.newConnectException0(Errors.java:166) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Errors.handleConnectErrno(Errors.java:131) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Socket.finishConnect(Socket.java:359) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.doFinishConnect(AbstractEpollChannel.java:711) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.finishConnect(AbstractEpollChannel.java:688) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.epollOutReady(AbstractEpollChannel.java:567) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.EpollEventLoop.processReady(EpollEventLoop.java:499) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.EpollEventLoop.run(EpollEventLoop.java:407) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.concurrent.SingleThreadEventExecutor$4.run(SingleThreadEventExecutor.java:997) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.internal.ThreadExecutorMap$2.run(ThreadExecutorMap.java:74) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30) [fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at java.lang.Thread.run(Unknown Source) [?:?]
2025-12-24 11:55:12,295 WARN  org.apache.fluss.server.kv.snapshot.KvTabletSnapshotTarget   [] - Snapshot 0 for TableBucket TableBucket{tableId=0, bucket=0} does not exist in ZK. The commit truly failed, proceeding with cleanup.
2025-12-24 11:55:12,298 WARN  org.apache.fluss.server.kv.snapshot.KvTabletSnapshotTarget   [] - Snapshot 0 failure or cancellation for TableBucket TableBucket{tableId=0, bucket=0}.
org.apache.fluss.exception.NetworkException: Disconnected from node 0:0:0:0:0:0:0:0%0:9123 (id: cs-0, rack: null)
Caused by: org.apache.fluss.shaded.netty4.io.netty.channel.AbstractChannel$AnnotatedConnectException: finishConnect(..) failed: Connection refused: /[0:0:0:0:0:0:0:0%0]:9123
Caused by: java.net.ConnectException: finishConnect(..) failed: Connection refused
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Errors.newConnectException0(Errors.java:166) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Errors.handleConnectErrno(Errors.java:131) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Socket.finishConnect(Socket.java:359) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.doFinishConnect(AbstractEpollChannel.java:711) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.finishConnect(AbstractEpollChannel.java:688) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.epollOutReady(AbstractEpollChannel.java:567) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.EpollEventLoop.processReady(EpollEventLoop.java:499) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.EpollEventLoop.run(EpollEventLoop.java:407) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.concurrent.SingleThreadEventExecutor$4.run(SingleThreadEventExecutor.java:997) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.internal.ThreadExecutorMap$2.run(ThreadExecutorMap.java:74) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at java.lang.Thread.run(Unknown Source) [?:?]
2025-12-24 11:55:12,340 WARN  org.apache.fluss.server.kv.snapshot.PeriodicSnapshotManager  [] - Fail to handle snapshot result during snapshot of TableBucket TableBucket{tableId=0, bucket=0}
org.apache.fluss.exception.NetworkException: Disconnected from node 0:0:0:0:0:0:0:0%0:9123 (id: cs-0, rack: null)
Caused by: org.apache.fluss.shaded.netty4.io.netty.channel.AbstractChannel$AnnotatedConnectException: finishConnect(..) failed: Connection refused: /[0:0:0:0:0:0:0:0%0]:9123
Caused by: java.net.ConnectException: finishConnect(..) failed: Connection refused
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Errors.newConnectException0(Errors.java:166) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Errors.handleConnectErrno(Errors.java:131) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.unix.Socket.finishConnect(Socket.java:359) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.doFinishConnect(AbstractEpollChannel.java:711) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.finishConnect(AbstractEpollChannel.java:688) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.AbstractEpollChannel$AbstractEpollUnsafe.epollOutReady(AbstractEpollChannel.java:567) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.EpollEventLoop.processReady(EpollEventLoop.java:499) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.channel.epoll.EpollEventLoop.run(EpollEventLoop.java:407) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.concurrent.SingleThreadEventExecutor$4.run(SingleThreadEventExecutor.java:997) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.internal.ThreadExecutorMap$2.run(ThreadExecutorMap.java:74) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at org.apache.fluss.shaded.netty4.io.netty.util.concurrent.FastThreadLocalRunnable.run(FastThreadLocalRunnable.java:30) ~[fluss-server-0.8.0-incubating.jar:0.8.0-incubating]
    at java.lang.Thread.run(Unknown Source) [?:?]
```

- fluss-client 실행
```
root@coordinator-server-0:/tmp/test# ls
fluss-example-1.0-SNAPSHOT.jar

root@coordinator-server-0:/tmp/test# java -cp fluss-example-1.0-SNAPSHOT.jar io.songgane.fluss.DatabaseGet
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
my_db
fluss

root@coordinator-server-0:/tmp/test# java -cp fluss-example-1.0-SNAPSHOT.jar io.songgane.fluss.TableGet
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
TableInfo{tablePath=my_db.user_table, tableId=0, schemaId=1, schema=(id STRING NOT NULL,age INT,created_at TIMESTAMP(6),is_active BOOLEAN,CONSTRAINT PK_id PRIMARY KEY (id)), physicalPrimaryKeys=[id], bucketKeys=[id], partitionKeys=[], numBuckets=1, properties={table.replication.factor=3, table.datalake.format=iceberg}, customProperties={}, comment='null', createdTime=1766574156296, modifiedTime=1766574156296}

root@coordinator-server-0:/tmp/test# java -cp fluss-example-1.0-SNAPSHOT.jar io.songgane.fluss.TableWrite
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
Upserting rows to the table

root@coordinator-server-0:/tmp/test# java --add-opens=java.base/java.nio=org.apache.arrow.memory.core,ALL-UNNAMED --add-opens=java.base/java.nio=ALL-UNNAMED -cp fluss-example-1.0-SNAPSHOT.jar io.songgane.fluss.TableGet
WARNING: Unknown module: org.apache.arrow.memory.core specified to --add-opens
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
TableInfo{tablePath=my_db.user_table, tableId=0, schemaId=1, schema=(id STRING NOT NULL,age INT,created_at TIMESTAMP(6),is_active BOOLEAN,CONSTRAINT PK_id PRIMARY KEY (id)), physicalPrimaryKeys=[id], bucketKeys=[id], partitionKeys=[], numBuckets=1, properties={table.replication.factor=3, table.datalake.format=iceberg}, customProperties={}, comment='null', createdTime=1766574156296, modifiedTime=1766574156296}
Number of buckets: 1
Subscribing to bucket 0
Polling for records...
(1,20,2025-12-24T11:50:01.806731,true)
(2,22,2025-12-24T11:50:01.806780,true)
(3,23,2025-12-24T11:50:01.806788,true)
(4,24,2025-12-24T11:50:01.806794,true)
(5,25,2025-12-24T11:50:01.806800,true)
Polling for records...
Polling for records...
Polling for records...
Polling for records...
Polling for records...
```

- remote storage 조회
```
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data
[2025-12-24 20:58:46 KST]     0B kv/
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv
[2025-12-24 20:58:50 KST]     0B my_db/
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv/my_db
[2025-12-24 20:58:56 KST]     0B user_table-0/
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv/my_db/user_table-0
[2025-12-24 20:59:03 KST]     0B 0/
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv/my_db/user_table-0/0
[2025-12-24 20:59:05 KST]     0B shared/
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv/my_db/user_table-0/0/shared
[2025-12-24 20:55:12 KST]     0B STANDARD /
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv/my_db/user_table-0/0/shared
[2025-12-24 20:55:12 KST]     0B STANDARD /
➜  Downloads mc ls tlake-s3/tlake-ns2/fluss/remote-data/kv/my_db/user_table-0/0/shared
[2025-12-24 20:55:12 KST]     0B STANDARD /
```