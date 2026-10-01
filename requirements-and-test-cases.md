# GridWise IoT Energy Management System

## 1. Purpose

GridWise helps facility operators reduce energy cost and peak demand by combining IoT telemetry with specialised AI agents. This prototype demonstrates the operations dashboard and the decision workflow; production integrations can replace the sample data.

## 2. Stakeholders

| Role | Primary need |
|---|---|
| Energy manager | See live energy performance and approve recommendations |
| Facility technician | Investigate device and equipment alerts |
| Finance team | Track cost and savings against forecast |
| System administrator | Configure devices, tariffs, permissions and policies |

## 3. Functional requirements

| ID | Requirement | Priority | Acceptance criterion |
|---|---|---|---|
| FR-01 | Ingest timestamped power, energy, voltage, battery and solar telemetry from IoT devices. | Must | Valid readings appear in the operational data stream with device ID and timestamp. |
| FR-02 | Show current demand, daily consumption, daily cost and solar generation. | Must | Dashboard values refresh from the selected site dataset. |
| FR-03 | Display demand trend by configurable time range. | Must | Operator can choose day, week or month aggregation. |
| FR-04 | Forecast near-term site demand. | Must | Forecast agent returns a demand estimate and confidence for the next peak window. |
| FR-05 | Optimise load against tariff windows and demand limits. | Must | Tariff agent supplies an estimated saving and an action window. |
| FR-06 | Detect abnormal device or equipment consumption. | Must | Anomaly agent creates an alert with severity, evidence and source device. |
| FR-07 | Route agent outputs through an orchestration policy. | Must | Conflicting actions are ranked and only permitted actions are sent for approval or execution. |
| FR-08 | Allow an operator to apply or dismiss an actionable recommendation. | Must | The system records the decision, actor and timestamp. |
| FR-09 | Show energy-source mix for grid, solar and battery. | Should | Source values reconcile with current site demand within defined tolerance. |
| FR-10 | Maintain an audit trail for agent decisions and device commands. | Must | Audit record contains agent, input summary, recommendation, action and result. |

## 4. Non-functional requirements

| ID | Requirement | Target |
|---|---|---|
| NFR-01 | Dashboard availability | 99.5% monthly availability excluding planned maintenance |
| NFR-02 | Telemetry freshness | Display readings no older than 60 seconds under normal connectivity |
| NFR-03 | Dashboard responsiveness | Primary views render within 2 seconds on a standard broadband connection |
| NFR-04 | Security | TLS in transit, encrypted credentials, role-based access and auditable commands |
| NFR-05 | Scalability | Support at least 10,000 devices and 1 reading per device per minute |
| NFR-06 | Safety | Autonomous commands require policy checks and a reversible command path |

## 5. Multi-agent design

1. **Telemetry monitor** validates data quality, late readings and device health.
2. **Load forecaster** predicts demand using historical load, weather and occupancy signals.
3. **Tariff optimiser** compares forecast load with tariff and contracted demand constraints.
4. **Anomaly detector** identifies deviations from equipment baselines.
5. **Orchestrator** scores, deduplicates and applies policy to agent outputs before surfacing recommendations or commands.

## 6. Test cases

| ID | Scenario | Steps | Expected result |
|---|---|---|---|
| TC-01 | Dashboard loads | Open the dashboard. | Four headline KPI cards, charts, agent panel and alerts are visible. |
| TC-02 | Change time range | Select Week then Month. | Selected range is highlighted and feedback confirms the new view. |
| TC-03 | Run agent scan | Select Run agent scan. | Button enters scanning state, then completes and confirms scan outcome. |
| TC-04 | Apply recommendation | Select Apply recommendation. | Success message appears and button shows recommendation applied. |
| TC-05 | Dismiss recommendation | Refresh page and select Dismiss. | Recommendation section disappears and dismissal feedback appears. |
| TC-06 | Telemetry validation | Send a reading without device ID or with invalid numeric value. | Monitor rejects or quarantines reading and records validation error. |
| TC-07 | Peak forecast | Supply historical rising demand before a tariff peak. | Forecaster produces peak estimate; tariff optimiser proposes a cost-saving action. |
| TC-08 | Abnormal equipment load | Send AHU power 18% above its baseline for 47 minutes. | Anomaly alert is created with device, deviation and severity. |
| TC-09 | Policy enforcement | Submit an agent command outside allowed equipment hours. | Orchestrator blocks execution and records policy reason. |
| TC-10 | Audit record | Apply an approved command. | Audit trail includes operator, recommendation, command status and timestamp. |
| TC-11 | Responsive layout | View at 375 px and 1024 px viewport widths. | Content remains legible; navigation and cards reflow without horizontal overflow. |
| TC-12 | Access control | Attempt device-command action as read-only user. | System prevents command and presents an authorisation message. |

## 7. Suggested production stack

- Device integration: MQTT or HTTPS gateway with schema validation
- Data platform: time-series database plus event queue
- Agent services: containerised services with model registry and policy engine
- API: authenticated REST or GraphQL service
- Dashboard: responsive web application with WebSocket updates
- Observability: central logs, metric alerts and immutable audit storage
