#coursenotes #security 

Threat modeling is the process of identifying and prioritizing potential threats to a system and determining the value that possible security controls would have in reducing or neutralizing those threats. OWASP describes threat modeling as a way to "identify, communicate, and understand threats and mitigations" to software applications. It provides a systematic and structured way to find threats early and minimize the possible damage they may cause if realized.

Threat modeling needs you to "think like a hacker"; viewing the technological stack through this lens can allow us to identify where our application is at risk and then implement measures to mitigate or eliminate such risk accordingly.

Integrating threat modeling into software from the beginning of the software development lifecycle (SDLC) is crucial. Early identification and mitigation of application security risks are essential to make the application more secure.

When modeling systems in real life, consider adopting a widely accepted threat modeling methodology, like STRIDE, PASTA, TRIKE, or OCTAVE.

1. Define the trust boundaries in the target system by analyzing its constituent parts to understand how they interact with each other and external entities.
2. Identify the threats that could attack the application.
3. Identify the security controls to mitigate the threat level.

### Trust Boundaries

A trust boundary is a conceptual line that separates areas with different security policies or levels of trust.

It is applicable in multiple contexts, such as defining the privileges in logical functionalities, where different roles like "User" and "Administrator" possess distinct access rights. They can also divide a network based on their trustworthiness, e.g., "Local Server," "Intranet," "Internet-facing," and "Internet." This concept extends to organizational environments too, where environments such as "Cloud," "Staging," and "Production" require distinct levels of trust.

Defining trust boundaries helps to identify components susceptible to threats and where to implement security controls.

### Threats

A threat refers to a potential negative action or event that could compromise a system's security, integrity, or availability. Threats can come from various sources, including external attackers, insider threats, or even inadvertent errors.

Enumerating all possible threats is a key step in understanding what vulnerabilities exist and how they might be exploited, allowing for the development of mitigation strategies to protect the system.

Extensive threat descriptions are cataloged in projects such as the MITRE [CAPEC](https://capec.mitre.org/) project.

### Controls

A security control is a safeguard or countermeasure implemented to mitigate identified threats. They can be technological measures like installing anti-virus systems, but they can also be procedural, like implementing access control. The goal is to reduce the risk associated with various threats to an acceptable level.

## Testing

During the Definition and Design, defining and testing security requirements is essential, i.e., determining how an application works from a security perspective.

Applications should have a documented design and architecture. Use Unified Modeling Language (UML) models to describe how the application works and undertake a Threat Modeling exercise. Use the results to revisit the design and architecture with the Systems Architect to modify the design.

- **OWASP ASVS**: [1](https://github.com/OWASP/ASVS/releases/download/v4.0.2_release/OWASP.Application.Security.Verification.Standard.4.0.2-en.pdf)
- **OWASP Testing Guide**: [Definition and Design](https://owasp.org/www-project-web-security-testing-guide/stable/3-The_OWASP_Testing_Framework/0-The_Web_Security_Testing_Framework#phase-2-during-definition-and-design)

## References

[OWASP - Insecure Design](https://owasp.org/Top10/A04_2021-Insecure_Design/)

[OWASP SAMM - Design: Threat Assessment](https://owaspsamm.org/model/design/threat-assessment/)