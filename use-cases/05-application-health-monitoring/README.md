# Use Case 05: Application Health Monitoring Agent

## Overview

**Participant**: ศุภษา พสุกวรรณ (Suphasa Pasukawan)
**Position**: นค.8 (Computer Technician Level 8)
**ID**: 571121
**Department**: กพอ-พ. (IT Operations Division)

**Original Use Case**: ผู้ช่วยกำกับดูแลข้อมูลเพื่อการ Implement ระบบ Web Application

**English Description**: Automated monitoring agent to discover all web applications' health checks and alert IT staff via Microsoft Teams when issues are detected, enabling proactive incident management.

## Business Problem

EGAT's IT Operations team manages multiple web applications with monitoring challenges:
- **Manual monitoring**: Staff manually check application status multiple times daily
- **Reactive approach**: Issues discovered by users, not monitoring
- **Scattered health checks**: Each app has different monitoring endpoints
- **Alert fatigue**: Too many false positives from basic monitoring
- **No centralized view**: Health status spread across multiple systems
- **Delayed response**: Issues not detected until users complain
- **Knowledge silos**: Only specific staff know which apps to monitor

**Current Process Pain Points**:
- Manually visiting application URLs to verify status
- Checking logs across different servers
- No proactive alerting before users affected
- Difficulty correlating issues across systems
- Time-consuming daily health checks
- After-hours issues go undetected

## Solution

A proactive AI monitoring agent powered by Microsoft Copilot Studio that:
- **Auto-discovers applications** from configuration management database (CMDB)
- **Performs health checks** automatically (HTTP endpoints, API status, database connectivity)
- **Monitors continuously** at configurable intervals (every 5-15 minutes)
- **Sends intelligent alerts** to Teams when anomalies detected
- **Provides context**: Historical trends, related incidents, suggested actions
- **Self-healing triggers**: Initiates automated remediation for known issues
- **Reports dashboards**: Real-time and historical health status
- **On-demand queries**: "Is application X healthy?" via Teams chat

## Business Value

### Expected Benefits
- **Proactive Detection**: Find issues before users report them (30-50% faster)
- **Reduced Downtime**: Faster mean time to detection (MTTD)
- **Staff Efficiency**: Eliminate manual health checks (2-3 hours/day saved)
- **Better SLAs**: Meet uptime commitments more consistently

### Qualitative Benefits
- Improved application availability
- Better user experience (fewer outages)
- Reduced after-hours escalations
- Staff focus on improvements vs firefighting
- Data-driven capacity planning
- Compliance with IT governance standards

## Success Metrics

| Metric | Baseline | Target (3 months) | Target (6 months) |
|--------|----------|-------------------|-------------------|
| Mean Time to Detect (MTTD) | 30-60 min | < 10 min | < 5 min |
| Proactive detection rate | 0% | ≥ 60% | ≥ 80% |
| Manual check time/day | 2-3 hours | < 30 min | < 15 min |
| Application uptime | Variable | ≥ 99.5% | ≥ 99.9% |
| False positive rate | N/A | Measure | < 5% |

## Technical Architecture

```
┌─────────────────────────────────────────┐
│         Application Landscape           │
│  Web App 1 | Web App 2 | ... | Web App N│
└──────────────┬──────────────────────────┘
               │ Health Check Endpoints
               ↓
┌─────────────────────────────────────────┐
│       Copilot Studio Agent              │
│   + Power Automate Flows                │
└──────────────┬──────────────────────────┘
               │
               ├──→ HTTP/HTTPS Checks
               ├──→ API Health Endpoints
               ├──→ Database Connectivity
               ├──→ Azure Monitor Integration
               │
               ↓
┌─────────────────────────────────────────┐
│         AI Analysis Engine              │
│  - Detect anomalies                     │
│  - Correlate patterns                   │
│  - Determine severity                   │
│  - Suggest remediation                  │
└──────────────┬──────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────┐
│           Alert Actions                 │
│  - Teams notification (with context)    │
│  - Create incident ticket               │
│  - Trigger auto-remediation             │
│  - Update status dashboard              │
└─────────────────────────────────────────┘
```

## Prerequisites

### Access & Licenses
- [ ] Microsoft Copilot Studio license
- [ ] Power Platform environment access
- [ ] Power Automate Premium (for HTTP connectors)
- [ ] Azure Monitor (recommended)
- [ ] Microsoft Teams (for alerts)
- [ ] ServiceNow/Jira (optional, for ticketing)

### Technical Requirements
- [ ] Application inventory (list of apps to monitor)
- [ ] Health check endpoints for each application
- [ ] Network access from Power Platform to applications
- [ ] Teams channel for alerts
- [ ] Permissions to access application logs/metrics

### Knowledge Requirements
- [ ] Completion of Lab 01 and Lab 02
- [ ] Understanding of application architecture
- [ ] Basic knowledge of HTTP status codes
- [ ] Familiarity with Power Automate
- [ ] IT Operations/DevOps experience

## Implementation Complexity

**Overall**: ⭐⭐⭐⭐ (Medium-High)

**Breakdown**:
- Agent creation: ⭐⭐ (Medium)
- Health check automation: ⭐⭐⭐⭐ (High)
- Alert logic configuration: ⭐⭐⭐ (Medium-High)
- Integration with monitoring tools: ⭐⭐⭐⭐ (High)
- Testing and tuning: ⭐⭐⭐⭐ (High - requires production testing)
- Deployment: ⭐⭐ (Medium)

**Estimated Time**: 10-14 hours for initial implementation

## File Structure

```
05-application-health-monitoring/
├── README.md                           # This file - overview
├── implementation-guide.md             # Step-by-step instructions
├── connections-required.md             # Technical setup details
├── testing-guide.md                   # Testing procedures
├── monitoring-best-practices.md       # DevOps/SRE standards
└── assets/
    ├── sample-queries.md              # Example monitoring queries
    ├── health-check-templates.md      # Standard health check formats
    ├── alert-templates.md             # Teams notification templates
    └── remediation-playbooks.md       # Auto-fix procedures
```

## Quick Start

1. **Review Prerequisites** (above)
2. **Inventory applications** to monitor
3. **Document health check endpoints** for each app
4. **Follow** [implementation-guide.md](./implementation-guide.md)
5. **Configure connections** per [connections-required.md](./connections-required.md)
6. **Test** using [testing-guide.md](./testing-guide.md)

## Related Use Cases

This implementation can work with:
- **Incident Management Bot**: Create tickets automatically
- **Deployment Assistant**: Monitor post-deployment health
- **Capacity Planning**: Analyze resource utilization trends

## Industry Context

Automated application monitoring provides measurable value:
- Faster incident detection (50-70% improvement)
- Reduced downtime through proactive alerts
- Better SLA compliance
- Staff productivity gains (eliminate manual checks)

**Note**: Results depend on application architecture and monitoring strategy.

## Key Differentiators

### vs Manual Monitoring
- **Continuous** (24/7) vs intermittent checks
- **Proactive alerts** vs reactive discovery
- **Consistent** checks vs human error prone
- **Intelligent context** vs raw data

### vs Traditional Monitoring Tools (Nagios, Zabbix)
- **Conversational interface** (ask questions in Teams)
- **AI-powered insights** (pattern recognition, anomaly detection)
- **Natural language alerts** (context-rich notifications)
- **Lower learning curve** (no complex configuration)
- **Integrated with M365** (Teams, SharePoint)

### vs Enterprise APM (Datadog, New Relic)
- **Much lower cost** ($2-3K vs $50-100K/year)
- **Faster setup** (hours vs weeks)
- **Good enough** for many EGAT applications
- **Can complement** enterprise APM for non-critical apps

## Example Usage

### Proactive Alert in Teams

**Alert Message**:
```
🔴 CRITICAL ALERT - Application Issue Detected

**Application**: EGAT Employee Portal
**Status**: DOWN (HTTP 503 - Service Unavailable)
**Detected**: 2025-11-12 14:35:22 ICT
**Duration**: 3 minutes

**Impact Assessment**:
- Affects: All employees (3,000+ users)
- Business Impact: HIGH - Cannot access HR services
- Related Systems: Also affecting Timesheet application

**Historical Context**:
- Last similar incident: 2 weeks ago
- Previous cause: Database connection pool exhausted
- Resolution time: 15 minutes

**Suggested Actions**:
1. Check database connection pool (likely cause)
2. Review application logs at: [link]
3. Restart application pool if needed
4. Notify users via email

**Auto-Remediation Status**:
⏳ Attempting automatic restart of app pool...
✅ Restart successful - monitoring recovery...

**Additional Context**:
- CPU: 45% (normal)
- Memory: 78% (elevated)
- Database connections: 95/100 (critical)

[View Full Incident] [Acknowledge] [Escalate]
```

### Conversational Queries

**IT Staff asks in Teams**:
```
"What's the health status of all applications?"
```

**Agent responds**:
```
**Application Health Summary** (as of 14:40 ICT)

✅ Healthy (18 applications):
- EGAT Intranet Portal
- Document Management System
- HR Self-Service
- [15 more...]

⚠️ Warning (2 applications):
- Procurement System (slow response: 3.2s, threshold: 3s)
- Project Tracking (high memory: 85%)

🔴 Critical (1 application):
- Employee Portal (DOWN - under investigation)

**Overall Health**: 90% (18/20 apps healthy)
**Trend**: Declining (was 95% yesterday)

Would you like:
- Details on any specific application?
- Historical trend analysis?
- Recommendations for capacity upgrades?
```

## Next Steps

1. ✅ Review this README
2. ⏳ Create application inventory
3. ⏳ Document health check endpoints
4. ⏳ Follow [implementation-guide.md](./implementation-guide.md)
5. ⏳ Configure [connections](./connections-required.md)
6. ⏳ Test with [testing-guide.md](./testing-guide.md)
7. ⏳ Deploy monitoring for critical apps first
8. ⏳ Expand to all applications gradually

---

**Status**: ✅ Ready for Implementation
**Priority**: 🔥 High (Critical IT Operations Need)
**Last Updated**: 2025-11-12
