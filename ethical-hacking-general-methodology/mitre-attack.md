# MITRE ATT&CK

The MITRE ATT&CK framework is a global and continuously updated tool designed to provide detailed descriptions of the tactics, techniques, and procedures (TTPs) used by attackers against computer systems.  
Developed by MITRE, a U.S.-based nonprofit organization, this reference framework is used by defenders to better understand threats and improve their security strategies, as well as by offensive security professionals to enhance their attack strategies.

The MITRE ATT&CK framework consists of different elements:

**Structure**: The framework is structured around matrices for different environments, such as Windows, macOS, Linux, mobile networks, and cloud infrastructures. Each matrix is divided into tactics that represent the attackers' objectives (e.g., Initial Access, Execution, Persistence).

**Techniques and Sub-techniques**: Each tactic is subdivided into techniques and sub-techniques that detail how the objectives can be achieved. These techniques are accompanied by concrete examples of security incidents where they have been used, as well as advice on how to detect and mitigate them.

**Usage**: Cybersecurity professionals use ATT&CK for training, developing defense strategies, configuring their security tools, and simulating attacks to assess the robustness of their defenses.

**Community and Updates**: The framework is supported by a large community of security professionals and is regularly updated to reflect the changing threat landscape, including new techniques and tactics used by attackers. For example, it is currently at version 15. 

> It's also worth noting that recently MITRE created MITRE Atlas, which is the equivalent of MITRE ATT&CK but for artificial intelligence.  
> You can [find my page about MITRE ATLAS here](../AI/mitre-atlas.md)

## COMING SOON (more details on Mitre Attack)

## Methodology for Using MITRE ATT&CK in a Pentest

### 1. Preparation and Planning

- **Understand the Scope**: Determine the scope of the pentest, including the target environment and any specific rules of engagement.
- **Identify Objectives**: Clarify the primary objectives of the pentest, such as finding vulnerabilities, assessing security controls, or mimicking a specific threat actor.

### 2. Mapping Threats and Techniques

- **Select Relevant Tactics and Techniques**: Use the MITRE ATT&CK framework to identify tactics and techniques relevant to the target environment and the objectives of the pentest. Focus on techniques used by threat actors that would realistically target the organization.
- **Threat Intelligence Integration**: Incorporate threat intelligence to understand which adversaries are most likely to target the organization and their preferred tactics, techniques, and procedures (TTPs).

### 3. Creating a Testing Plan

- **Build Attack Scenarios**: Create attack scenarios that mimic real-world adversary behavior using the selected techniques from the ATT&CK framework.
- **Develop Test Cases**: For each technique, develop specific test cases that describe how the technique will be executed during the pentest.

### 4. Execution

- **Initial Access**: Use ATT&CK techniques to gain initial access to the target environment, such as phishing or exploiting vulnerabilities.
- **Persistence and Escalation**: Apply techniques for maintaining access and escalating privileges within the environment.
- **Lateral Movement**: Test techniques for moving laterally within the network to simulate the spread of an attack.
- **Collection and Exfiltration**: Execute techniques to gather and exfiltrate data, mimicking the actions of an adversary attempting to steal sensitive information.

### 5. Detection and Response

> It could be interesting to do during a pentest, it is not mandatory and it depends of the scope that you were assigned and the requirements.

- **Log and Monitor**: Monitor the environment for detection and response activities. Document whether security controls and monitoring systems successfully detect and respond to the simulated attacks.
- **Evasion Techniques**: Use ATT&CK techniques to test the ability of security controls to detect and prevent evasion tactics.

### 6. Analysis and Reporting

> This is nice to add in your reporting phase methodology

- **Analyze Findings**: Analyze the results of the pentest to identify strengths and weaknesses in the target environment's defenses.
- **Map Findings to ATT&CK**: Map discovered vulnerabilities and exploited techniques back to the ATT&CK framework to provide context and actionable insights.
- **Recommendations**: Provide recommendations for mitigating identified weaknesses and improving overall security posture based on the techniques used and observed deficiencies.

### 7. Review and Lessons Learned

> Usually when you will provide the report you will do the debriefing.  
> Then take some time to do the Update methodologies part as it is really worth it to keep your skills up to date or upgrade them.

- **Debriefing**: Conduct a debriefing session with relevant stakeholders to review the pentest findings and discuss the implications.
- **Update Methodologies**: Update pentesting methodologies and threat models based on the lessons learned and any new insights gained from the engagement.

## RESOURCES

- [MITRE ATT&CK](https://attack.mitre.org/)