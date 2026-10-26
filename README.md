# Release Information

- **Version**: 1.0.1

- **Certified**: Yes

- **Publisher**: Fortinet

- **Scope**: Analysis and Investigation

- **Verified with Models**: Fortinet FortiAI (AI model Medium)

- [Release notes](./release_notes.md)

# Alert Investigation Agent

Analyzes incoming security alerts using alert details, enrichment results from other agents, and investigation evidence to determine alert validity, severity, and priority. Correlates available context and recommends investigative next actions.

## Installation

This agent installs along with the FortiAI solution pack.

## Configuration

The Alert Investigation agent ships with a configuration that has access to all shipped MCP servers. For custom configuration, refer to the *Configuration* section of the [FortiAI](https://github.com/fortinet-fortisoar/solution-pack-fortinet-advisor) solution pack documentation.

> [!Note]
> Refer to [Configuring MCP Servers](https://docs.fortinet.com/document/fortisoar/8.0.0/administration-guide/823139/mcp-servers#Configure_MCP_Servers) on FortiSOAR platform documentation for information on configuring a custom MCP server.

### Prerequisites

- The FortiAI solution pack must be installed and configured with the Fortinet FortiAI connector.

  - To configure the FortiAI solution pack, refer to the [FortiAI](https://github.com/fortinet-fortisoar/solution-pack-fortinet-advisor/) solution pack documentation.
  - To configure the Fortinet FortiAI connector, refer to the [Fortinet FortiAI](https://docs.fortinet.com/fortisoar/connectors/fortinet-fortiai) connector documentation.

> [!Note]
>
> FortiAI solution pack and Fortinet FortiAI connector are preconfigured out-of-the-box with FortiSOAR `v8.0.1`.
> 

## Input Parameters

The input must be provided as a JSON object.

| Parameter   | Description                                                                                                                                          |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| `data`      | The complete raw alert payload containing all available alert metadata, indicators, timestamps, entities, and contextual fields required for triage. |

The following is an example input:

```json
{
  "data": {
    "uuid": "8f3c2a1e-5b7d-4e9a-a1c6-2d4f6b8e0a13",
    "name": "Suspicious certutil download from file share",
    "source": "Fortinet FortiSIEM",
    "sourceId": "48213",
    "sourcedata": {
      "incident_data": {
        "incidentId": 48213,
        "incidentTitle": "Suspicious certutil download from file share",
        "eventSeverity": 9,
        "eventSeverityCat": "HIGH",
        "incidentFirstSeen": 1760004120000,
        "incidentLastSeen": 1760004185000,
        "incidentStatusStr": "Active",
        "attackTactic": "Command and Control",
        "attackTechnique": [
          {
            "name": "Ingress Tool Transfer",
            "techniqueid": "T1105"
          }
        ],
        "phSubIncidentCategory": "Suspicious Download",
        "incidentSrc": "procName:certutil.exe,srcIpAddr:198.51.100.24,user:jdoe",
        "incidentTarget": "destName:files.example.com,destIpAddr:203.0.113.45"
      }
    }
  }
}
```

## Response

The output is returned as a JSON object.

| Parameter    | Description                                                                                                                         |
|--------------|-------------------------------------------------------------------------------------------------------------------------------------|
| `data`       | Normalized alert, including the alert type, source, extracted IOCs, event timeline, severity, timestamps, and source identifiers. |
| `summary`    | Investigation summary with verdict, key findings, next steps, and highlights.                                                       |
| `hypotheses` | All generated investigation hypotheses.                                                                                             |
| `logs`       | Detailed evaluation of investigation questions and results.                                                                         |
| `playbook`   | Recommended next steps generated with the verdict.                                                                                  |

The following is an example response:

```json
{
  "data": {
    "name": "Suspicious certutil download from file share",
    "uuid": "8f3c2a1e-5b7d-4e9a-a1c6-2d4f6b8e0a13",
    "source": "Fortinet FortiSIEM",
    "alert_type": "Suspicious Download",
    "incident_id": "48213",
    "summary": "certutil.exe on host 198.51.100.24, run by user jdoe, downloaded a file from files.example.com.",
    "iocs": [
      {
        "type": "IP Address",
        "field": ["incident_data.incidentSrc"],
        "value": "198.51.100.24"
      },
      {
        "type": "Domain",
        "field": ["incident_data.incidentTarget"],
        "value": "files.example.com"
      },
      {
        "type": "User",
        "field": ["incident_data.incidentSrc"],
        "value": "jdoe"
      }
    ],
    "event_time_lines": [
      {
        "timestamp": 1760004120000,
        "event": "certutil.exe started a download from files.example.com"
      }
    ],
    "severity": {
      "label": "HIGH",
      "score": 9
    },
    "timestamps": {
      "alert_create": 1760004185000,
      "event_start": 1760004120000,
      "event_end": 1760004185000
    },
    "event_count": 1,
    "status": "Active",
    "source_identifiers": {
      "case_id": "48213",
      "scope_id": null,
      "event_id": null,
      "rule_id": "PH_RULE_CERTUTIL_FILESHARE_DOWNLOAD"
    }
  },
  "summary": {
    "classification": "True Positive",
    "severity": "High",
    "narrative_summary": "certutil.exe was used to download a file from an external domain, which matches a known ingress tool transfer technique.",
    "highlighted_summary": "**certutil.exe** downloaded a file from **files.example.com** on **198.51.100.24**.",
    "key_findings": [
      "certutil.exe was run with download arguments by user jdoe.",
      "files.example.com is linked to two other alerts in the last 7 days."
    ],
    "next_action_steps": [
      "Isolate host 198.51.100.24.",
      "Block the domain files.example.com."
    ]
  },
  "hypotheses": [
    {
      "id": "H1",
      "name": "Malicious tool download using a living-off-the-land binary"
    },
    {
      "id": "H2",
      "name": "Legitimate administrative file transfer"
    }
  ],
  "logs": [
    {
      "uuid": "c41d7e02-9a3b-4f15-8e6d-0b2a7c9f1e54",
      "tier": "T1",
      "question": "Is files.example.com linked to other alerts?",
      "agent_hint": "LLM",
      "params": {},
      "status": "success",
      "result": "Yes. The domain appears in two other alerts.",
      "evidence": "Related alerts share the indicator files.example.com.",
      "supports": ["H1"],
      "weakens": ["H2"]
    }
  ],
  "playbook": {
    "immediate_next_actions": [
      "Isolate host 198.51.100.24.",
      "Block the domain files.example.com."
    ]
  }
}
```