## Part 1 - Code Review:

Cardea is an open-sourced public health app serving as the group's system-of-interest. The group executed automated code review over repositories named Cardea Mobile Agent, Cardea Primary Verifier Controller, Cardea Mobile Secondary Verifier Agent, Cardea Secondary Verifier Controller, Cardea Health Issuer Controller, and the Cardea Health Issuer UI portions of the code. Whilst doing so, they were able to identify a handful of Mtire Common Weakness Enumerations (CWE) within the open sources Github repository. 

Several challenges were encountered during code review. The initial hurdle involved a lack of familiarity with the JavaScript programming language. To overcome this challenge, the automated code-scanning tool SonarCloud (SonarCloud Online Code Review as a Service Tool) was chosen to quickly identify vulnerabilities in Cardea. The strategy was to use the tool, find the detected vulnerabilities, and then investigate them with manual review if necessary. SonarCloud reviewed the repositories that aligned with assurance and misuse cases. The tool was chosen because 1) It convienently operates within GitHub 2) It is compatible with Javascript and 3) Its findings can be referenced with hyperlinks for this assignment. After watching a demo of how the tool functioned, they decided to implement it themselves and have it analyze the code. SonarClouds revealed security vulnerabilities, bugs, and security hotspots needing attention. Scans revealed some weaknesses that were known through previous diagrams, and some unknown. 

The second difficulty arose from uncertainty about what specific aspects to scrutinize, given the well-maintained nature of Cardea. Most of scrutinization was in the shortcomings of a decentralized infrastructure, as well as poor security coding practices that have failed to be fixed over the years. 

The final challenge they foresaw was identifying issues and dealing with uncertainty in associating them with the matching Mitre Common Weakness Enumeration.

### Ryan & Perry Code Review:
Ryan and Perry anticipated several weakness after manual review of the code. Concerning Cardea Mobile Agent, CWE-306 was known since the misuse case. CWE-20 was also known in the misuse case, but was left out since it was believed to be too small of a vulnerability at the time. CWE-922 was known since the assurance case, where questions of how Cardea stores data revealed the lack of data integrity. CWE-778 was anticipated in the threat model, when the Microsoft Threat Modeling tool revealed the poor quality of logs on the app. 

The CWE's in Primary Verifier Agent were not expected like in Cardea Mobile Agent. CWE-327 was unexpected since Cardea's documentation described its cyptography as secure. CWE-200 and CWE-319 were also unexpected. CWE-1004 was unsurprising since Cole encountered the same weakness in his assigned repository. 


### Cole Code Review:
Cole had little experience Javascript, so he chose to rely primarily on an automated tool to do code analysis and identify any potential problems. He then narrowed down the focus of what the tool found by referencing the misuse cases and assurance cases identified in previous assignments to filter out potential flaws that were less concerning for our use scenarios. He then used manual analysis to guage if the flaw was applicable and how severe it is given the scenarios we had identified. The main difficulty he foresaw was the lack of understanding of the technology (JavaScript and block chain).

He forked his assigned repositories and used SonarCloud to run an automated analysis. He then examined each of the flaws that it produced and filtered them down to the relevant non-test security hotspots. He then utilized ChatGPT to match the flaw with a corresponding CWE. He then examined child CWEs and sought more specific CWEs that were a better fit. These were then examined and compared to the group's previous work.


### Daniel & Logan Code Review:
Regarding the manual code review of Cardea, neither Daniel nor Logan have a strong familiarity with JavaScript. So, they opted to fork both of the repositories that were undergoing analysis to their own GitHub accounts. From there, they then employed the native tools GitHub offers to solve errors and issues within written code and went through the folders of both repos’. By doing this, they hoped to find security flaws and syntax errors that would require action to resolve. 

For their manual review, the two were able to discover argument errors in the code which the automated tool was also able to discover at line 42 of the src/UI/CanUser.js and line 52 of the src/UI/Contact.js. Both instances are only errors in the code, rather than security issues, and were also anticipated.


## Part 2:


### Cardea Mobile Agent (Ryan):

The first weakness of Cardea Mobile Agent is CWE-20 Bad Input Validation, found in components/Registration/ContactInfo/index.js lines [123](https://sonarcloud.io/project/security_hotspots?id=redge9987_cardea-mobile-agent&hotspots=AYwTj7HNrAR3_H4WBxcK),[180](https://sonarcloud.io/project/security_hotspots?id=redge9987_cardea-mobile-agent&hotspots=AYwTj7HNrAR3_H4WBxcS) and [utils/schemas.js line 5](https://sonarcloud.io/project/security_hotspots?id=redge9987_cardea-mobile-agent&hotspots=AYwTj7K4rAR3_H4WBxhu). This weakness was hypothesized by the group and confirmed by SonarCloud. What bad input could entail on Cardea Mobile Agent is unknown, and needs further investigation. However, Cardea Mobile Agent has root-level permissions for reading/writing to local files. So a worse-case scenario could result in a rooted/jailbroken phone. 

The second weakness is CWE-922 Insecure Storage of Sensitive Information, found in [android/app/src/main/AndroidManifest.xml line 8](https://sonarcloud.io/project/security_hotspots?id=redge9987_cardea-mobile-agent&hotspots=AYwTj7LArAR3_H4WBxhz). This weakness was discovered after reading the Cardea documentation and reviewing the code manually. Tampering of Cardea User Mobile files can result in changing lab test results. This could allow ill travelers to trick Cardea-restricted restaurants, bars, and clubs. This weakness is Cardea Mobile Agent's biggest, so having SonarCloud find it too raises confidence in the tool. 

The third weakness is CWE-778 Insufficient Logging. The Microsoft Threat Modeling Tool first brought this issue to light. The tool suggested the possible weakness of repudiation on Cardea Mobile App, where logs or audits are not stored in a secure way. A manual analysis of the code revealed that no logs are kept for Cardea Mobile Agent at all. This means that key activity on the app, like receiving lab results or scanning QR codes, go undocumented. Even if logs were kept, Cardea's de-centralized infrastructure demands that logs concerning Cardea Mobile Agent would be dangerously stored on the local file system of the traveler's smartphone. This weakness is not severe, but a lack of logs makes forensic investigation and detecting malicious behavior on the mobile agent impossible. Activity on Cardea software outside of the mobile agent is still logged through the Hyperledger Indy Network. 

The last weakness of Cardea Mobile Agent is CWE-306 Missing Authentication for Critical Function. Without needing to review code, simply launching Cardea Mobile Agent will reveal that it has no user authentication. AKA no login. That means that whoever has access to the smartphone can open Cardea Mobile Agent and see the owner's lab results and whatever is in their digital wallet, like passport information. Thus, access to Cardea Mobile Agent is confined to the PIN/password/fingerprint/face scan of the smartphone, which may or may not be enabled. 


### Cardea Primary Verifier Controller (Perry):
CWE-327: Use of a Broken or Risky Cryptographic Algorithm

Located: util.js on line 57

Description: The first common weakness enumeration identified about the Cardea Primary Verifier agent is a broken or insecure cryptographic algorithm. Insecure cryptographic algorithms act as a vulnerability for this product feature, and the vulnerability can be exposed to produce unauthorized, sensitive data to an attacker. Some common attacks used to bypass weak or broken cryptographic algorithms include brute force and man-in-the-middle. This CWE was identified during the Sonarcloud automated code review. The Cardea documentation (page 14) mentions the use of cryptography for identification and encrypting/decrypting sensitive medical communications. With the code review identifying this weakness, a more robust cryptographic algorithm must be utilized to mitigate this security concern. After discussing this security vulnerability with the team, this CWE is considered a high priority. 

CWE-200: Exposure of Sensitive Information to an Unauthorized Actor / CWE-319: Cleartext Transmission of Sensitive Information

Located: orm/connections.test.js lines 17,37 & orm/contactsCompiled.test.js line 18

Description: The following two common weakness enumerations identified by the Sonarcloud automated code review were the exposure of sensitive information to an unauthorized subject and clear-text data transmissions. The primary reason these two security concerns were discovered in the code review was the code utilizing the HTTP web protocol to transmit and receive data via the web. As a result, this data transmission is processed in plain text and can ultimately be exploited by an attacker to expose confidential medical information. However, upon further review with the team, this CWE is contained within a .test file for testing purposes only. Hence, this CWE is considered a low priority.


CWE-1004 Sensitive Cookie Without 'HttpOnly' Flag

Located: /index.js line 119

Description: The fourth CWE identified for the Cardea Primary Verifier agent during the Sonarcloud automated code review was sensitive cookies not using the HttpOnly security flag. This Cardea agent feature uses a cookie to store sensitive session information such as user activity, client-side input, and authentication purposes. Furthermore, the HttpOnly flag is used with compatible web browsers to prevent client-side scripts from accessing session cookies. Ultimately, this helps mitigate Cross-Site Scripting (XSS) attacks that can allow an attacker to steal session cookies and the sensitive data stored within it. When discussing this CWE with the team, this was deemed a medium priority.


### Cardea Mobile Secondary Verifier Agent (Cole)

The first CWE associated with the Cardea Mobile Secondary Verifier Agent is CWE-250, Execution with Unnecessary Privileges, in [line 5 of the AndroidManifest.xml](https://sonarcloud.io/project/security_hotspots?id=hydra1114_cardea-mobile-secondary-verifier-agent&file=android%2Fapp%2Fsrc%2Fmain%2FAndroidManifest.xml&fileUuid=AYwTawN1GsAc-SEPvuSg&tab=code) file. This is specifically about the repository's requirements of permissions to access the camera. The verifier app does not use the camera as part of its verification process so the access is unnecessary and may be taken advantage of. This could be considered a low-level risk as it is a privacy concern, but doesn't give direct access to personal information or higher permissions. We were not originally looking into unexpected permissions as a security risk in our scenarios, so in this case the automated tool helped identify issues we hadn't thought of.

The second CWE detected was CWE-1333, Inefficient Regular Expression Complexity, in [line 159 of components/Registration/BusinessInfo/index.js](https://sonarcloud.io/project/security_hotspots?id=hydra1114_cardea-mobile-secondary-verifier-agent&file=components%2FRegistration%2FBusinessInfo%2Findex.js&fileUuid=AYwTawN1GsAc-SEPvuRq). This CWE would result in a Denial of Servce attack if taken advantage of. However, the context of this flaw is validating the user's phone number. Because of this, the only user being denied service would be the user typing in the phone number, so no security related risk is relavent. Potential inefficiencies leading to DoS attacks is something that we hadn't thought of when evaluating risks in our usage scenarios.

The third CWE found was CWE-319, Cleartext Transmission of Sensitive Information, in [line 9 of AndroidManifest.xml](https://sonarcloud.io/project/security_hotspots?id=hydra1114_cardea-mobile-secondary-verifier-agent&file=android%2Fapp%2Fsrc%2Fmain%2FAndroidManifest.xml&fileUuid=AYwTawN1GsAc-SEPvuSg&tab=code). While fact that cleartext communication is being used in an authentication and permissions application is disconcerting, the context of this flaw in this application according to Cardea's documentation is the transmission of public credentials with a limited lifespan directly with the app requesting verification. Because of the kind of information being transmitted and the nature of the communication, this flaw would be considered medium rather than high risk. Throughout our misuse and assurance cases, secure communication was a requirement of this application. The presence of this flaw, if wide-spread, could be considered a deal breaker.

### Cardea Secondary Verifier Controller (Cole)

There was only one potential security flaw detected in the Secondary Verifier Controller: CWE-1004, Sensitive Cookie Without 'HttpOnly' Flag. This is found in [line 120 of index.js](https://sonarcloud.io/project/security_hotspots?id=hydra1114_cardea-secondary-verifier-controller&tab=code) when creating a new session. It's not clear what the application is using the cookie for, but not setting the HttpOnly flag opens the application up to cross-site scripting attacks where clients can access the information stored in the cookie. Due to the ability of an attacker to access that information, this flaw could be considered medium risk. Further investigation would need to be done to asses how this cookie is used and if sensitive information would be at risk. Safe transmission and data storage is integral for the cardea suite of applications to be used for the purpose it was developed and is something that has been brought up in each of the scenarios we have explored.

### Cardea Health Issuer Controller (Logan & Daniel)

Upon employing the automated code review tool SonarCloud, the pair discovered the results they were looking for. Starting with the Cardea Health Issuer Controller, the initial CWE they identified through SonarCloud was CWE 327: Use of a broken or risky cryptographic algorithm. 
Found within the "util.js" file at the [57th line](https://sonarcloud.io/project/issues?resolved=false&types=VULNERABILITY&id=loganstranglen_cardea-health-issuer-controller&open=AYwTqSo2s7XGyIfbgB6x) of code, this CWE poses a high-security impact on the software. This is attributed to the code employing one of the most vulnerable modes of Advanced Encryption Standard (AES), namely, Cipher Block Chaining (CBC), during encryption. This mode lacks authentication mechanisms, exposing the software to potential threats, such as the unauthorized access and theft of sensitive data, including a patient's personal information. 
To address this vulnerability, the automated tool recommended a more robust mode like Galois/Counter Mode (GCM). GCM combines encryption with authentication and integrity checks using a cryptographic hash function, ensuring data confidentiality and mitigating potential security risks.

The Cardea Health Issuer Controller is also linked to CWE-319, which pertains to the Cleartext Transmission of Sensitive Information. The vulnerability lies in the code's utilization of an insecure protocol embedded in the invitation URL, pinpointed on [line 17](https://sonarcloud.io/project/security_hotspots?id=loganstranglen_cardea-health-issuer-controller ) of the connections.test.js file. The flagged issue arises from the use of HTTP instead of HTTPS in this particular line. This choice poses a risk of sensitive data interception, encompassing information like the patient's alias, status, or even the Decentralized Identifier (DID).
To address this concern, the automated tool recommended adopting a secure protocol, namely HTTPS, as a mitigation measure against potential security breaches.
