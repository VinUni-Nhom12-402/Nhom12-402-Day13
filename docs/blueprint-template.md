# Day 13 Observability Lab Report

> **Instruction**: Fill in all sections below. This report is designed to be parsed by an automated grading assistant. Ensure all tags (e.g., `[GROUP_NAME]`) are preserved.

## 1. Team Metadata
- [GROUP_NAME]: Nhom12-402
- [REPO_URL]: https://github.com/VinUni-Nhom12-402/Nhom12-402-Day13
- [MEMBERS]:
  - Member A: [Bùi Cao Chinh - 2A202600001] | Role: Logging & PII
  - Member B: [Trần Thị Kim Ngân - 2A202600432] | Role: Tracing & Tags
  - Member C: [Dương Chí Thành - 2A202600047] | Role: SLO & Alerts
  - Member D: [Phan Xuân Quang Linh - 2A202600492] | Role: Load Test & Incident Injection
  - Member E: [Nguyễn Đức Tiến - 2A202600393] | Role: Dashboard & Evidence
  - Member F: [Nguyễn Trọng Thiên Khôi - 2A202600227] | Role: Blueprint & Demo Lead

---

## 2. Group Performance (Auto-Verified)
- [VALIDATE_LOGS_FINAL_SCORE]: 100/100
- [TOTAL_TRACES_COUNT]: 13
- [PII_LEAKS_FOUND]: 0
-  docs/screenshots/validate_logs.png

---

## 3. Technical Evidence (Group)

### 3.1 Logging & Tracing
- [EVIDENCE_CORRELATION_ID_SCREENSHOT]: docs/screenshots/pii_redaction.jpg
- [EVIDENCE_PII_REDACTION_SCREENSHOT]: docs/screenshots/pii_redaction.jpg
- [EVIDENCE_TRACE_WATERFALL_SCREENSHOT]: docs/screenshots/trace_list.jpg
- [TRACE_WATERFALL_EXPLANATION]: Trace gồm 3 spans: chat_agent (root ~160ms), RAG span (retrieve() ~20ms lấy docs từ mock store), LLM span (generate() ~130ms sinh câu trả lời). LLM span chiếm phần lớn latency — đây là điểm cần tối ưu nếu P95 vượt SLO 3000ms.

### 3.2 Dashboard & SLOs
- [DASHBOARD_6_PANELS_SCREENSHOT]: docs/screenshots/dashboard/
- [SLO_TABLE]:
| SLI | Target | Window | Current Value |
|---|---:|---|---:|
| Latency P95 | < 3000ms | 28d | ~160ms ✅ |
| Error Rate | < 2% | 28d | ~0% (normal), 100% (tool_fail incident) ✅ |
| Cost Budget | < $2.5/day | 1d | ~$0.03 ✅ |
| Quality Score | > 0.75 | 28d | ~0.80 ✅ |

### 3.3 Alerts & Runbook
- [ALERT_RULES_SCREENSHOT]: docs/screenshots/alert_rules.jpg
- [SAMPLE_RUNBOOK_LINK]: docs/alerts.md#1-high-latency-p95

---

## 4. Incident Response (Group)
- [SCENARIO_NAME]: tool_fail
- [SYMPTOMS_OBSERVED]: Toàn bộ request trả về HTTP 500, correlation_id = None trong load test output, error_type xuất hiện trong logs
- [ROOT_CAUSE_PROVED_BY]: Trace ID: trace-tool-fail-2026-04-20 — span "tool_call" có status ERROR, kết hợp log line: {"event": "request_failed", "error_type": "ToolExecutionError", "correlation_id": "req-cb286f78"}
- [FIX_ACTION]: Disable incident toggle: `python scripts/inject_incident.py --scenario tool_fail --disable`
- [PREVENTIVE_MEASURE]: Thêm fallback handler cho tool errors; alert high_error_rate (P1) trigger khi error_rate > 5% trong 5 phút

---

## 5. Individual Contributions & Evidence

### [Bùi Cao Chinh]
- [TASKS_COMPLETED]: Implemented structured JSON logging, correlation ID propagation via middleware, log enrichment with user/session/feature context, PII scrubbing (email, phone, name redaction). validate_logs.py score: 100/100.
- [EVIDENCE_LINK]: https://github.com/VinUni-Nhom12-402/Nhom12-402-Day13/pull/1

### [Trần Thị Kim Ngân]
- [TASKS_COMPLETED]: Implemented Langfuse tracing using @observe decorator on agent pipeline. Enriched traces with tags (feature, model, user_id_hash). Verified ≥10 traces visible in Langfuse with correct span structure.
- [EVIDENCE_LINK]: [https://github.com/VinUni-AI20k/Lab13-Observability/commit/f14f0a9d2716817202b4a01ed4a26961032e4033]

### [Dương Chí Thành]
- [TASKS_COMPLETED]: Defined service SLOs for latency, error rate, cost, and quality. Implemented and documented alert rules with severities, thresholds, owners, and runbook links. Supported dashboard thresholds and incident-debug workflow.
- [EVIDENCE_LINK]: https://github.com/VinUni-Nhom12-402/Nhom12-402-Day13/pull/2

### [Phan Xuân Quang Linh]
- [TASKS_COMPLETED]: Ran load tests using scripts/load_test.py with concurrency 5 to generate realistic traffic. Injected incident scenarios (tool_fail) via scripts/inject_incident.py to simulate failures. Verified system behavior under normal and incident conditions.
- [EVIDENCE_LINK]: [[(https://github.com/VinUni-AI20k/Lab13-Observability/commit/87c4360e11cac11ac6e37b21862a0b793b9b3065)]

### [Nguyễn Đức Tiến]
- [TASKS_COMPLETED]: Built 6-panel dashboard in Langfuse covering Traffic, Latency P50/P95, Error Rate, Cost over time, Tokens in/out, and Quality Score. Collected and organized evidence screenshots for grading submission.
- [EVIDENCE_LINK]: [https://github.com/VinUni-AI20k/Lab13-Observability/commit/6991b5015f0109fed6bb031e997874a4d55c9afd]

### [Nguyễn Trọng Thiên Khôi]
- [TASKS_COMPLETED]: Compiled team blueprint report, coordinated evidence collection, ran load tests and incident injection to verify system observability, fixed tracing configuration (load_dotenv), led demo preparation.
- [EVIDENCE_LINK]: [https://github.com/VinUni-AI20k/Lab13-Observability/commit/c90ce3d6d41a3860d0d283e5bc7160b8a6cddc1b]

---

## 6. Bonus Items (Optional)
- [BONUS_COST_OPTIMIZATION]: (Description + Evidence)
- [BONUS_AUDIT_LOGS]: (Description + Evidence)
- [BONUS_CUSTOM_METRIC]: (Description + Evidence)
