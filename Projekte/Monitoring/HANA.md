# HANA Monitoring

System: **J25/02** (HDB02) · Host: `vhxsoj24db01` · Service: `indexserver`
Timestamp: 2026-10-28 14:59:21

---

## 1. Database Metrics — Delta Storage (Metric ID 29)

```bash
/usr/sap/hostctrl/exe/saphostctrl -function ListDatabaseMetrics \
  -dbname J25 -dbtype hdb -dbinstance HDB02 -format table -id 29
```

| Resource (Table) | Value (%) | Rating |
|---|---:|:---:|
| `_SYS_STATISTICS.HOST_SQL_PLAN_CACHE_BASE` | 0.003900 | 0 |
| `/PRA/OW_DIDO_BPC` | 0.002900 | 0 |
| `PA0245` | 0.001900 | 0 |
| `MMIV_SI_D_ITEM` | 0.001900 | 0 |
| `GLE_FI_ITEM_ORIG` | 0.001900 | 0 |
| `GLE_FI_ITEM_MODF` | 0.001900 | 0 |
| `CUST_EXP_KOMPDRF` | 0.001900 | 0 |
| `CTE_D_FIN_T_ITEM` | 0.001900 | 0 |
| `CFIN_ACCIT` | 0.001900 | 0 |
| `MARC` | 0.000900 | 0 |
| `MARC_PRC` | 0.000900 | 0 |
| `MARC_SRC` | 0.000900 | 0 |
| `MARC_TMP` | 0.000900 | 0 |
| `NSDM_D_MTDCSA` | 0.000900 | 0 |
| `NSDM_MM_CHK_LOGR` | 0.000900 | 0 |
| `OIB_PHYINV` | 0.000900 | 0 |
| `OIURV_DOC_RDSUM` | 0.000900 | 0 |
| `P2RN_FB` | 0.000900 | 0 |
| `PA0146` | 0.000900 | 0 |
| `PA0201` | 0.000900 | 0 |

> All ratings `0` (OK). Unit: `%`. Schema: `SAPHANADB` unless noted.

<details>
<summary>Raw output</summary>

```
Name|MetricID|Resource                                                                                   |SystemHost  |SystemID|SystemType|Timestamp              |Unit|Value   |Rating|
----|--------|-------------------------------------------------------------------------------------------|------------|--------|----------|-----------------------|----|--------|------|
29  |29      |ALERT_TEXT=_SYS_STATISTICS.HOST_SQL_PLAN_CACHE_BASE.0|HOST=vhxsoj24db01|SERVICE=indexserver|vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.003900|0     |
29  |29      |ALERT_TEXT=SAPHANADB./PRA/OW_DIDO_BPC.0|HOST=vhxsoj24db01|SERVICE=indexserver              |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.002900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.PA0245.0|HOST=vhxsoj24db01|SERVICE=indexserver                        |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.MMIV_SI_D_ITEM.0|HOST=vhxsoj24db01|SERVICE=indexserver                |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.GLE_FI_ITEM_ORIG.0|HOST=vhxsoj24db01|SERVICE=indexserver              |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.GLE_FI_ITEM_MODF.0|HOST=vhxsoj24db01|SERVICE=indexserver              |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.CUST_EXP_KOMPDRF.0|HOST=vhxsoj24db01|SERVICE=indexserver              |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.CTE_D_FIN_T_ITEM.0|HOST=vhxsoj24db01|SERVICE=indexserver              |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.CFIN_ACCIT.0|HOST=vhxsoj24db01|SERVICE=indexserver                    |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.001900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.MARC.0|HOST=vhxsoj24db01|SERVICE=indexserver                          |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.MARC_PRC.0|HOST=vhxsoj24db01|SERVICE=indexserver                      |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.MARC_SRC.0|HOST=vhxsoj24db01|SERVICE=indexserver                      |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.MARC_TMP.0|HOST=vhxsoj24db01|SERVICE=indexserver                      |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.NSDM_D_MTDCSA.0|HOST=vhxsoj24db01|SERVICE=indexserver                 |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.NSDM_MM_CHK_LOGR.0|HOST=vhxsoj24db01|SERVICE=indexserver              |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.OIB_PHYINV.0|HOST=vhxsoj24db01|SERVICE=indexserver                    |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.OIURV_DOC_RDSUM.0|HOST=vhxsoj24db01|SERVICE=indexserver               |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.P2RN_FB.0|HOST=vhxsoj24db01|SERVICE=indexserver                       |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.PA0146.0|HOST=vhxsoj24db01|SERVICE=indexserver                        |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
29  |29      |ALERT_TEXT=SAPHANADB.PA0201.0|HOST=vhxsoj24db01|SERVICE=indexserver                        |vhxsoj24db01|J25/02  |Database  |2026.10.28 14:59:21.920|%   |0.000900|0     |
```

</details>

---

## 2. Table Size — `PA0146`

```bash
hdbsql -U NAGIOSJ25 -jax \
  "SELECT SCHEMA_NAME, TABLE_NAME, (TABLE_SIZE)/1024/1024/1024 FROM M_TABLES WHERE TABLE_NAME = 'PA0146';"
```

| Schema | Table | Size (GB) |
|---|---|---:|
| SAPHANADB | PA0146 | 0.0016831532115917 |

<details>
<summary>Raw output</summary>

```
"SAPHANADB","PA0146",0.0016831532115917
```

</details>

---

## 3. Mergedog Configuration — `indexserver.ini`

```bash
hdbsql -U NAGIOSJ25 -jax \
  "SELECT FILE_NAME, LAYER_NAME, SECTION, KEY, VALUE FROM M_INIFILE_CONTENTS WHERE SECTION = 'mergedog' AND FILE_NAME = 'indexserver.ini';"
```

File: `indexserver.ini` · Layer: `DEFAULT` · Section: `mergedog`

| Key | Value |
|---|---|
| `active` | `yes` |
| `smart_merge_enabled` | `yes` |
| `check_interval` | `60000` |
| `unload_check_interval` | `1200000` |
| `max_lock_wait_time` | `1000` |
| `max_lock_retry_period` | `3600000` |
| `max_cpuload_for_parallel_merge` | `80` |
| `max_delta_memsize` | `1000` |
| `delta_merge_statistics_record_limit` | `100000` |
| `token_per_table` | `2` |
| `hard_merge_priority_func` | `QDW` |
| `auto_merge_priority_func` | `1 / (7 + MMS)` |
| `smart_merge_priority_func` | `1 / (7 + MMS)` |
| `load_balancing_func` | `1 + LCC * (AHM/GAL) * MAX(100-CLA, 0)/100` |
| `smart_merge_decision_func` | `(DMS>1000 or DRC>0.1*MRC or DMR>0.1*MRC)` |
| `auto_merge_decision_func` | `((DRC*TMD > 3600*(MRC+0.0001)) or ((DMS>PAL/2000 or DMS > 1000 or DRCC>100) and DRRC > MRC/100) or (DMR>0.2*MRC and DMR > 0.001) or (DRC=0 and DMR=0 and MPU=1) or (TMD > 3600 and MRC > 0.1 and DMR <= 0.2*MRC and MDTSBCR > 0.6))` |
| `critical_merge_decision_func` | `UPT > 43200 and LOADED and CURR and ((MMS<10000 and DMS>1000 and TMD>86400) or TMD>604800)` |
| `unload_decision_func` | `TPL = 0 and TLT > 86400 and if(TURP > 0, TLD > TURP, TLD > 259200 and TLD > MMS * 100)` |

> [!note]- Raw output
> ```
> "indexserver.ini","DEFAULT","mergedog","max_lock_wait_time","1000"
> "indexserver.ini","DEFAULT","mergedog","smart_merge_decision_func","(DMS>1000 or DRC>0.1*MRC or DMR>0.1*MRC)"
> "indexserver.ini","DEFAULT","mergedog","max_lock_retry_period","3600000"
> "indexserver.ini","DEFAULT","mergedog","smart_merge_enabled","yes"
> "indexserver.ini","DEFAULT","mergedog","max_cpuload_for_parallel_merge","80"
> "indexserver.ini","DEFAULT","mergedog","max_delta_memsize","1000"
> "indexserver.ini","DEFAULT","mergedog","critical_merge_decision_func","UPT > 43200 and LOADED and CURR and ((MMS<10000 and DMS>1000 and TMD>86400) or TMD>604800)"
> "indexserver.ini","DEFAULT","mergedog","delta_merge_statistics_record_limit","100000"
> "indexserver.ini","DEFAULT","mergedog","load_balancing_func","1 + LCC * (AHM/GAL) * MAX(100-CLA, 0)/100"
> "indexserver.ini","DEFAULT","mergedog","unload_decision_func","TPL = 0 and TLT > 86400 and if(TURP > 0, TLD > TURP, TLD > 259200 and TLD > MMS * 100)"
> "indexserver.ini","DEFAULT","mergedog","auto_merge_decision_func","((DRC*TMD > 3600*(MRC+0.0001)) or ((DMS>PAL/2000 or DMS > 1000 or DRCC>100) and DRRC > MRC/100) or (DMR>0.2*MRC and DMR > 0.001) or (DRC=0 and DMR=0 and MPU=1) or (TMD > 3600 and MRC > 0.1 and DMR <= 0.2*MRC and MDTSBCR > 0.6))"
> "indexserver.ini","DEFAULT","mergedog","check_interval","60000"
> "indexserver.ini","DEFAULT","mergedog","hard_merge_priority_func","QDW"
> "indexserver.ini","DEFAULT","mergedog","unload_check_interval","1200000"
> "indexserver.ini","DEFAULT","mergedog","token_per_table","2"
> "indexserver.ini","DEFAULT","mergedog","auto_merge_priority_func","1 / (7 + MMS)"
> "indexserver.ini","DEFAULT","mergedog","active","yes"
> "indexserver.ini","DEFAULT","mergedog","smart_merge_priority_func","1 / (7 + MMS)"
> ```

---

## 4. Auto-Merge Status — `PA0146`

```bash
hdbsql -U NAGIOSJ25 -jax \
  "SELECT SCHEMA_NAME, TABLE_NAME, AUTO_MERGE_ON FROM \"SYS\".\"TABLES\" WHERE TABLE_NAME = 'PA0146';"
```

| Schema | Table | Auto Merge On |
|---|---|:---:|
| SAPHANADB | PA0146 | `TRUE` |

<details>
<summary>Raw output</summary>

```
"SAPHANADB","PA0146","TRUE"
```

</details>
