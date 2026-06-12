# Release Information

- **Version**: 1.0.0

- **Certified**: Yes

- **Publisher**: Fortinet

- **Scope**: Analysis and Investigation

- **Verified with Models**: Fortinet FortiAI (AI model Medium)

# Alert Investigation Agent

Analyzes incoming security alerts using alert details, enrichment results from other agents, and investigation evidence to determine alert validity, severity, and priority. Correlates available context and recommends investigative next actions.

## Installation

This agent installs along with the FortiAI solution pack.

## Configuration

**Required MCP Servers**: SOC Framework, Utility Tools, FortiSOAR Playbook Management, FortiSOAR Module Management

> [!Note]
>
> Refer to [Configuring MCP Servers](https://docs.fortinet.com/document/fortisoar/8.0.0/administration-guide/823139/mcp-servers#Configure_MCP_Servers) on FortiSOAR platform documentation for information on configuring a custom MCP server.
> 

### Prerequisites

- The FortiAI solution pack must be installed and configured with the Fortinet FortiAI connector.

  - To configure the FortiAI solution pack, refer to the [FortiAI](https://github.com/fortinet-fortisoar/solution-pack-fortinet-advisor/) solution pack documentation.
  - To configure the Fortinet FortiAI connector, refer to the [Fortinet FortiAI](https://docs.fortinet.com/fortisoar/connectors/fortinet-fortiai) connector documentation.

> [!Note]
>
> FortiAI solution pack and Fortinet FortiAI connector are preconfigured out-of-the-box with FortiSOAR `v8.0.0`.
> 

## Input Parameters

The input must be provided as a JSON object.

| Parameter   | Description                                                                                                                                          |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| `data`      | The complete raw alert payload containing all available alert metadata, indicators, timestamps, entities, and contextual fields required for triage. |

## Response

The output is returned as a JSON object.

| Parameter    | Description                                                                     |
|--------------|---------------------------------------------------------------------------------|
| `Summary`    | Investigation summary with verdict, key findings, next steps, and highlights.   |
| `Hypotheses` | All generated investigation hypotheses.                                         |
| `Logs`       | Detailed evaluation of investigation questions and results.                     |

