# MITRE ATLAS

> This documentation is made from my notes of the free course by the attackiq Academy.  
> If you are interested in AI Pentest and AI Security I really recommend that you take this course.  
> Check it out [here](https://www.academy.attackiq.com/courses/foundations-of-ai-security)
>
> Like MITRE ATT&CK, MITRE ATLAS is a valuable resources for our pentests. We can use it to find techniques for our pentest engagements.  
> 
> This document aims at being use as a cheatsheet for your pentest engagements.

## What is MITRE ATLAS?

MITRE ATLAS (Adversarial Threat Landscape for Artificial-Intelligence Systems) and the AI Risk Management Framework (AI RMF) are both initiatives aimed at addressing the security and risk management challenges posed by AI systems, but they focus on different aspects and serve complementary roles.

Like MITRE ATT&CK®, ATLAS was developed by MITRE to improve cybersecurity, yet they serve distinct areas within the security domain. ATLAS focuses on protecting Artificial Intelligence (AI) systems from unique threats like data tampering and tricking AI into making wrong decisions. It gives detailed advice on how to spot and stop attacks specifically aimed at AI technologies.

> Source: [Foundations of AI Security - ATTACKIQ Academy](https://www.academy.attackiq.com/courses/foundations-of-ai-security)

## ATLAS VS ATT&CK

![ATLAS VS ATT&CK](../.res/2024-06-17-08-02-00.png)

> Source: [Foundations of AI Security - ATTACKIQ Academy](https://www.academy.attackiq.com/courses/foundations-of-ai-security)

## ATLAS Tactics Overview

ATLAS consists of 14 tactics, all with a range of techniques that apply to them.  Several of the tactics utilized in ATLAS will be familiar if you have experience with ATT&CK.  

### Reconnaissance

Adversaries engage in reconnaissance to collect information about a target's machine learning (ML) system, which helps them prepare for future attacks.  
This process involves both active and passive methods to gather data on an organization's ML technologies and projects. The insights obtained can guide adversaries in acquiring ML artifacts related to the target, attacking the target's ML applications, customizing assaults for the target's specific models, or informing further reconnaissance activities.

#### Techniques: Search for Victim's Publicly Available Research Materials

- Many organizations deploy open-source model architectures enhanced with their own proprietary data
- Academic journals
- Conference proceedings
- Pre-print repositories
- Technical blogs

#### Techniques: Search for Publicly Available Adversarial Vulnerability Analysis

- Attackers look for existing research on the vulnerabilities of commonly used machine learning models.
- Academic papers
- Pre-existing implementations attacks

#### Techniques: Search Victim-Owned Websites

- Attackers review target websites for information for their attacks.
- May provide insights into technical aspects of ML enabled products
- May contain detail on organization structure

#### Techniques: Search Applications Repositories

- Attackers may explore open application repositories as part of their targeting strategy. These repositories can include popular platforms like Google Play, the iOS App Store, the macOS App Store, and the Microsoft Store.

#### Techniques: Active Scanning

- An adversary may actively probe or scan the victim's system to collect information for targeting.
- Involves direct interactions with the target system

### Resource Development

Adversaries develop resources to support their operations, which includes creating, buying, or stealing assets like machine learning artifacts, infrastructure, accounts, or capabilities. These resources assist in further stages of an attack, such as preparing for a machine learning attack.  

For instance, an attacker could create a fake social media account to gather data on an organization's employees. This account could then be used to launch spear-phishing attacks aimed at obtaining access to the organization's machine learning systems.

- Key resources: ML artifacts, infrastructure, accounts and capabilities

#### Techniques: Acquire public ML Artifacts

- Aversary look at public sources like cloud storage, public-facing servicesm and software data repos for ML artifacts
- Artifacts include software stack used for training and deploying models
- Artifacts also include training and testing, and model configurations

> Acquiring some artifacts might require actions like registration, providing AWS keys, or written requests, which might lead the adversary to establish fake accounts.  
> Example, an attacker targeting a company's ML-based spam filter might search for adversarial attack tools that can generate email content designed to bypass the filter. Upon finding a suitable tool, the attacker customizes it to mimic the writing style and content of legitimate emails typically sent within the company. By doing so, the attacker can craft emails that the spam filter fails to flag as spam, allowing malicious messages to reach the intended targets within the organization.

#### Techniques: Obtain Capabilities

- Adversaries look for software capabilities
- Tools can be customized or modified by adversary

> Example, an attacker targeting a company's ML-based spam filter might search for adversarial attack tools that can generate email content designed to bypass the filter.  
> Upon finding a suitable tool, the attacker customizes it to mimic the writing style and content of legitimate emails typically sent within the company.  
> By doing so, the attacker can craft emails that the spam filter fails to flag as spam, allowing malicious messages to reach the intended targets within the organization.

#### Techniques: Develop Capabilities

- Adversaries will develop their own tools if unable to obtain them
- Tools do not always rely on machine learning themselves

> Example, an attacker targeting a company's ML-based image recognition system might develop a website that hosts images embedded with subtle adversarial patterns.  
> These patterns are designed to trick the company's ML system into misclassifying the images when they are uploaded by unsuspecting users.  
> The attacker's website could appear as a legitimate photo-sharing platform, but its real purpose is to gather and distribute these adversarial images to undermine the effectiveness of the target's ML system.

#### Techniques: Acquire Infrastructure

- Purchasing, leasing or renting various resources
- Infrastructure can range from servers to domains, mobile devices and third party web services
- May choose infrastructure that is difficult to trace and set up/decommissioned quickly

> Example, An attacker might rent cloud servers to host a command-and-control server for a botnet. By using a cloud service provider, the attacker can easily scale the operation, remain anonymous, and shut down the infrastructure if it's at risk of being discovered. This flexibility and anonymity make it a preferred choice for adversaries looking to execute their operations discreetly.

#### Techniques: Publish Poisoned Datasets

- Adversaries contaminate training data and release it publicly
- Could be an entirely new or tainted version of an existing open source dataset
- Compromised data is integrated into a victim's system through a compromise in the supply chain

> Example, an attacker could modify a widely used open-source dataset for facial recognition, adding subtle distortions to the images. If a company unknowingly uses this poisoned dataset to train its facial recognition system, the system's accuracy could be significantly reduced, leading to security vulnerabilities.

#### Techniques: Poison Training data

Adversaries may try to undermine machine learning  models by tampering with the datasets used for training. They can do this by altering the data or its labels, creating hidden weaknesses in the models that are hard to spot. These attacks, known as data poisoning, might involve changing the labels or just the data itself. The planted flaw can later be exploited using specific data samples with a backdoor trigger.

> The key difference between this technique and the Publish Poisoned Datasets technique is that the Publish Poisoned Datasets technique focuses on publicly releasing compromised data for widespread use, while the Poison Training Data technique involves directly tampering with the training data of a specific target's ML models.

#### Techniques: Establish Accounts

- Attackers might set up accounts with different services to help them in their attacks.

> Example, Clearview AI, a company that provides a facial recognition tool, had a misconfigured source code repository that allowed anyone to register an account. This security flaw enabled an external researcher to access Clearview AI's private code repository, which contained production credentials, keys to cloud storage buckets with 70,000 video samples, copies of its applications, and Slack tokens.

### Initial Access

The adversary’s goal is to infiltrate an AI system during initial access.  This can be accomplished through a range of techniques.  
For example, in a carefully orchestrated cyber attack, attackers aimed to circumvent a facial recognition system for initial access.  
Their plan involved acquiring inexpensive mobile phones as the operation's hardware foundation. They customized these devices with special Android ROMs and a virtual camera app, setting the stage for their attack.  
The attackers then procured software capable of transforming static photographs into dynamic videos that simulate real human gestures, such as blinking, to lend authenticity to the images. By purchasing personal data and high-definition facial photographs from the dark web, they created digital profiles mimicking the targeted victim. Leveraging the virtual camera application, these animated videos were presented to the machine learning-based facial recognition service during the verification process.  
This sophisticated approach successfully deceived the facial recognition system, allowing the attackers to impersonate the victim and gain unauthorized access to their tax information.  

#### ML Supply chain compromise

- Attackers target specific parts of the ML supply chain
- GPU hardware
- Data and annotations
- Components of the ML software stack
- The ML model

To detail the ways in which an attacker can compromise the supply chain, 4 sub-techniques make up this technique:

- GPU Hardware - Machine learning systems often rely on specialized hardware, such as GPUs. Attackers can target these systems by focusing on the supply chain of the GPUs.
- ML Software - Machine learning systems often depend on a few key frameworks. By compromising one of these frameworks, an attacker could gain access to many different systems. Additionally, many projects use open-source algorithms that could be targeted for compromise, allowing attackers to access specific systems.
- Data - Many projects use large, publicly available open-source datasets, which could be tampered with by adversaries. This tampering could involve poisoning the training data or embedding traditional malware.  Attackers can also target private datasets during the labeling phase. Creating private datasets often involves using external labeling services. An attacker could manipulate the dataset by altering the labels produced by these services.
- Model - Organizations typically download open-source models from external sources and use them as a base, fine-tuning them on smaller, private datasets. Loading these models often involves executing code saved in model files, which can be compromised with traditional malware or through adversarial machine-learning techniques.

#### Technique: Valid Accounts

- Get and misuse login details from existing accounts
- Might include username/password or API keys

#### Technique: Evade ML Model

- Attackers create altered data that tricks ML model into misidentifying what's in the data

#### Technique: Exploit Public Facing Application

- Attackers exploit a flaw in a computer system or program that's connected to the internet.
- Flaw can be a bug, a glitch, or a design flaw.
- Websites are common targets
- Attackers can also target databases (like SQL), standard services (like SMB or SSH), network device management protocols (like SNMP and Smart Install), and any other internet-connected applications, such as web servers and their services.

#### Technique: LLM Prompt Injection

- Attacker uses harmful inputs, called "prompt injections," to make a large language model (LLM) behave in unexpected ways.
- Trick the model into ignoring its original instructions
- May be used to get around LLM's denses or to execute special commands
- Can inject malicious prompts directly or use the LLM to create harmful content.

The LLM Prompt Injection technique is further detailed in 2 sub-techniques:

- Direct - An attacker directly inputs harmful prompts into a large language model (LLM) to either gain access to the system or misuse the LLM, such as creating harmful content.
- Indirect - An attacker indirectly inputs harmful prompts into a large language model (LLM) by using separate data sources like text or multimedia from databases or websites. These prompts can be hidden or disguised, allowing the attacker to gain access to the system or target an unsuspecting user.

#### Technique: Phishing

- Adversaries may send phishing messages to gain access to victim systems. 
- All forms of phishing are electronically delivered social engineering.
- LLMs can help attackers run large-scale phishing campaigns
- Deepfakes can also be used to be someone else

The Phishing technique also contains the sub-technique Spearphishing via Social Engineering LLM in which LLMs can chat with users and be set up to phish for private information.  They can also be targeted towards particular personas defined by the adversary.

### ML Model Access

Adversaries aim to obtain some level of access to a machine learning  model, which can range from full insight into the model's internal workings to merely interacting with the data input mechanisms. Gaining access allows them to gather information, tailor attacks, or feed specific data into the model.
For instance, an attacker could target a publicly available ML model provided through an API or manipulate a service that incorporates ML, influencing the model's outputs to their advantage. A practical example of this could involve a hacker accessing a voice recognition system used in virtual assistants. By understanding how the system processes voice commands, the attacker could input malicious commands or exploit vulnerabilities, affecting the assistant's behavior or accessing restricted information.

#### Technique: ML Model Inference API Access

- Attackers can gain access to a machine learning model through legitimate use of the model's inference API.
- This access can help the attacker learn about the model's structure and type, set up an attack, or introduce data to the system to cause harm or avoid detection.

#### Technique: ML-Enabled Product or Service

- Attackers can access a machine learning model indirectly by using a product or service that incorporates machine learning.
- This approach might expose information about the model or its predictions in logs or metadata.

#### Technique: Physical Environment Access

- Attackers target the physical environment where data is collected for machine learning models.
- If the model uses data from the real world, attackers can manipulate the model by interfering with the data collection process.

#### Technique: And, Full ML Model Access

- Attackers gain complete access to a machine learning model.
- Known as "white-box" access - they know everything about the model
- They can then use this information to create data that tricks the model

### Execution

Adversaries use execution techniques to run harmful code on both local and remote systems by embedding it within machine learning artifacts or software.
For example, an attacker could insert a malicious script into a machine learning model's code, which is then executed when the model processes data, giving the attacker unauthorized access to the system's data or operations.

#### Technique: User Execution

- An attacker might depend on a user's actions to carry out their attack.
- could also trick users into running malicious code by convincing them to open a dangerous document or click on a harmful link.

The User Execution technique also contains the sub-technique Unsafe ML Artifacts:

- Attackers create unsafe ml artifacts that, when run, can damage a system
- Can use this method to maintain long-term access to systems.
- Introduced through a compromise in the machine learning supply chain.

#### Technique: Command and Scripting Interpreter

- Attackers can misuse command and script interpreters to run commands, scripts, or programs
- Includes OS-specific interpreters
- Includes non-OS specific interpreters like Python
- Attackers may embed commands or scripts in malicious files or emails
- Attackers can also run commandes remotely through interactive terminals

#### Technique: LLM Plugin Compromise

- Adversaries exploit a large language model (LLM) that is integrated into a broader system by targeting its connected plugins
- Can range from connections to other applications, access to various data sources (public or private), and the ability to execute code.

### Persistence

Adversaries aim to keep their unauthorized access to a system, even through restarts or security updates, by using persistence techniques involving machine learning artifacts or software.
As an example, an attacker might modify a machine learning model or its training data to create a backdoor, ensuring they can regain access anytime without detection.

#### Poison Training Data

- Technique used as part of resource devlopment
- Also used to achive goal of persistence

#### Backdoor ML Model

- Adversaries implant a hidden backdoor into an ML model.
- Model functions normally under regular circumstances
- Creates a lasting presence for the adversary within the victim's system.

The Backdoor ML Model technique is further broken down into two sub-techniques:

- **Poison ML Model**
  - Adversaries can insert a backdoor into a machine learning model either by training the model with poisoned data or by tampering with its training process.
  - Model is manipulated to learn an association between a specific trigger and the output the adversary wants.
- **Inject payload**
  - Adversaries insert a backdoor into a machine learning model by adding a malicious payload into the model's file.
  - This payload is designed to recognize the presence of a specific trigger and, when detected, it bypasses the normal model processing to produce the output desired by the adversary, rather than the output the model would normally generate.

#### LLM Prompt Injection

See above for details about Prompt Injection.

### Privilege Escalation

Adversaries aim to gain higher-level permissions to achieve their objectives, such as accessing sensitive data or systems. Privilege Escalation involves exploiting system weaknesses, misconfigurations, or vulnerabilities to obtain elevated permissions like SYSTEM/root level, local administrator, or specific user accounts with admin-like access.

For instance, an adversary might exploit a vulnerability to gain local administrator rights, allowing them to install malicious software or access restricted areas. This process often works in tandem with Persistence techniques, where the methods used to maintain access can also provide higher privileges.

Privilege Escalation includes the techniques LLM Prompt Injection, LLM Plugin Compromise, and LLM Jailbreak.  We’ve covered LLM Prompt Injection and LLM Plugin Compromise and previous sections.  For the Privilege Escalation Tactic, we will take a look at the LLM Jailbreak technique.  

#### Technique LLM Jailbreak

- Aversary exploits an LLM by crafting a prompt that tricks the LLM into entering a state that ignores limitations or safeguards
- LLM will respond to any user input without restrictions.

### Defense Evasion

Adversaries employ Defense Evasion strategies to avoid detection by machine learning-enabled security systems, like malware detectors. These techniques are designed to slip past AI-driven safeguards, ensuring their malicious activities remain unnoticed.

As an example, an attacker might modify the code of their malware to evade detection algorithms, allowing them to infiltrate a network without triggering any alerts.

Defense evasion includes three techniques that we have covered under previous tactics; Evade ML Model, LLM Prompt Injection, and LLM Jailbreak.  

### Credential Access

Adversaries engage in Credential Access to acquire usernames and passwords through methods such as keylogging or credential dumping. This enables them to access systems discreetly and create additional accounts for further malicious activities.

For instance, an attacker might use a phishing email to trick a user into entering their login details on a fake webpage, thereby capturing those credentials for unauthorized access.

#### Technique: Unsecured Credentials

- Attackers search systems they've compromised to locate and acquire credentials not stored in a secure manner
- Can be found in plaintext files, environment variables, system/application-specifif repos
- Can be found in other files or artifacts, like private key files.

### Discovery

Adversaries engage in Discovery to learn about a system and its network. This process helps them understand the environment, identify what they control, and find potential advantages for their objectives. Often, they use the system's own tools for this post-compromise reconnaissance.

For example, the attacker might use scripts or automated tools to scan for accessible machine learning APIs or data repositories. Upon discovering an open API used for machine learning model predictions, the adversary assesses it for vulnerabilities, such as weak authentication mechanisms, which could then be exploited to inject malicious data, access sensitive model information, or manipulate model behavior.

#### Technique: Discover ML Model Ontology

- Adversaries try to learn classification structure, of a machine learning model's output.
- Attacker could do this by repeatedly querying the model to make it reveal all possible outputs.
- Ontology in a configuration file or in the model's documentation.

#### Technique Discover ML Model Family

- Adversaries may try to identify the general category or family of a machine learning model.
- May be found in documentation
- May use spcially crafted examples and analyze the model's responses to categorize it
- Understanding the model's family can assist in identifying potential attacks methods.

#### Technique Discover ML Artifacts

- Adversaries may search private sources on a system to find and gather information about machine learning artifacts.
- Include the software stack used for training and deploying models, systems for managing training and testing data, container registries, software repositories, and collections of models known as model zoos.

#### Technique LLM Meta Prompt Injection

- Adversaries manipulate a large language model (LLM) to reveal its initial instructions,
- Known as the "meta prompt."
- Stealing the meta prompt can be a way for adversaries to obtain valuable intellectual property related to the design and operation of the LLM.

### Collection

An adversary aims to collect machine learning (ML) artifacts and relevant data to further their goals. This process involves using various methods to compile information that aids in achieving the adversary's objectives, such as theft of ML artifacts or preparation for future attacks. Key targets for collection include software repositories, model repositories, and data storage systems.

As an example, an attacker might infiltrate a cloud storage service used by a company to store ML models and training data. By accessing this repository, the attacker collects sensitive ML models and datasets, which could be used to understand the company's ML capabilities, replicate the models for malicious purposes, or identify vulnerabilities for further exploitation.

#### Technique ML Artifact Collection

- Adversaries may gather machine learning artifacts for two main purposes: to steal (Exfiltrate) them or to use them in preparing (Staging) for a machine learning-based attack.
- ML artifacts consist of models and datasets, as well as other data generated when interacting with a model, such as logs or output results.

#### Technique Data from Information Repositories

- Adversaries may use information repositories to gather valuable information.
- Tools designed for storing information
- Data type can vary depending on the particular system or environment.
- Examples: SharePoint, Confluence, and enterprise databases

#### Technique Data from Local Systems

- Adversaries search local system sources, to find files of interest
- Can include basic identifying system information or sensitive data like SSH keys

### ML Attack Staging

An adversary uses their understanding and access to a target system to customize their attack specifically for that system. This involves preparing various strategies like creating similar models for practice (proxy models), tampering with the target model's data (poisoning), or designing misleading inputs (adversarial data) to deceive the target model. These preparations, often done before the attack and without online detection, pave the way for the main assault aimed at compromising the ML model.

For instance, an attacker targeting a bank's fraud detection ML system might first develop a proxy model that mimics the bank's system. By understanding how the bank's model works, the attacker can craft financial transactions that appear normal to the bank's ML system but are actually fraudulent, effectively bypassing the fraud detection mechanisms.

#### Technique Create Proxy ML Model

- Adversaries acquire models that act as stand-ins for the target model.
- Simulate full access to the target model in a completely offline environment.
- Adversaries train models using datasets that closely resemble the target model's training data, try to replicate models based on the responses from the victim's inference APIs, or use pre-trained models that are publicly available.

The Create Proxy ML Model technique consists of three sub-techniques:

- Train Proxy via Gathered ML Artifacts - Proxy models created using machine learning artifacts, such as data, model architectures, and pre-trained models, that closely resemble the target model.
- Train Poxy via Replication - Adversaries can copy a private model by frequently querying the victim's machine learning model's inference API. Through these queries, they gather the responses (inferences) of the target model and compile them into a dataset. This dataset, containing the inferences as labels, is then used to train a separate model offline.
- Use Pre-Trained Model - Adversaries may use a readily available pre-trained model as a substitute for the victim's model to help prepare for an attack. This pre-trained model, which has already been trained on a similar task, can be used to simulate the behavior of the victim's model, allowing the adversary to plan and refine their attack strategies.

#### Technique Backdoor ML Model

See above

#### Technique Verify Attack

Adversaries can verify the effectiveness of their attack through an inference API or by accessing an offline copy of the target model. An inference API is a tool that allows users to interact with a machine learning model to get predictions based on new input data.

#### Technique Craft Adversarial Data

- Inputs that have been subtly altered to trick a machine learning  model into making a wrong decision.
- Alterations are usually small enough that humans wouldn't notice a difference, but they can significantly impact the model's output.
- Adversaries can create adversarial data using various methods, depending on how much they know about the ML model they're targeting.

The Craft Adversarial Data technique consists of 5 sub-techniques:

- White-Box Optimization - When an adversary has full access to a machine learning model, they can directly manipulate the input data to create adversarial examples. These examples are specifically tailored to deceive the model into making incorrect predictions or classifications. Because the adversary can closely analyze and adjust the data based on the model's inner workings, the resulting adversarial examples are highly effective at misleading the target model. This direct optimization approach allows the adversary to fine-tune the adversarial examples for maximum impact against the specific model they have access to.
- Black-Box Optimization - In Black-Box attacks, the adversary doesn't have direct access to the inner workings of the target machine learning model. Instead, they interact with the model through an external interface, such as an API, which allows them to input data and receive the model's predictions. This limited access means the adversary can't directly optimize their adversarial examples as precisely as they could in White-Box attacks, where they have full access to the model. As a result, Black-Box attacks typically require more attempts to find effective adversarial examples and are generally less efficient than White-Box attacks. However, Black-Box attacks are more common in real-world scenarios because they require much less access to the target system, making them a more practical choice for adversaries.
- Black-Box Transfer - In Black-Box Transfer attacks, the adversary uses one or more substitute models that they have full control over and which are similar to the target model. These substitute models, known as proxy models, are developed through techniques like creating proxy ML models or training proxies via replication. The attacker then employs White-Box Optimization techniques on these proxy models to create adversarial examples, which are inputs designed to trick the models.The key idea behind Black-Box Transfer attacks is that if the proxy models are similar enough to the target model, the adversarial examples that fool the proxy models are likely to also fool the target model. This means that the attack crafted for the proxy models can be transferred to the target model, hence the name "transfer" attack.This approach differs from Black-Box Optimization attacks, where the adversary interacts directly with the target model via an API and tries to find adversarial examples without having access to the model's internals. In contrast, Black-Box Transfer attacks involve an additional step of using proxy models to craft the adversarial examples.
- Manual Modification - Adversaries can manually alter input data to create adversarial examples, which are designed to trick a machine learning model. They might use their understanding of the target model to change parts of the data that they believe are important for the model's performance. The attacker may experiment with different modifications until they find a change that successfully deceives the model. This process of trial and error continues until they can confirm they have created an effective adversarial example.
- Insert Backdoor Trigger - Adversaries can add a perceptual trigger, which is a subtle alteration or hidden pattern, into the data used for making predictions in a machine learning model. This trigger is crafted to be difficult for humans to notice, either because it's very small or disguised within the data. This technique is commonly used along with poisoning a machine learning model. The purpose of the perceptual trigger is to activate a specific response or behavior in the target model, allowing the adversary to control its output or actions in a way that suits their goals.

### Exfiltration

An adversary aims to steal valuable machine learning artifacts or data from a system. This process, known as Exfiltration, involves methods to secretly remove data from a network. The stolen data, often containing sensitive intellectual property or information critical for planning future attacks, is typically transmitted via a control channel established by the adversary. They may also use alternative methods for data transfer, sometimes imposing size limits to avoid detection.

#### Technique Exfiltration via ML Inference API

- Adversaries extract private information using access to a machine learning model's inference API.
- Machine learning models can inadvertently reveal details about their training data

Exfiltration via ML Inference API is broken down even further in three sub-techniques:

- Infer Training Data Membership – Adversaries may determine if a specific data sample was part of a machine learning model's training set, which can lead to privacy breaches. Some methods involve using a similar model (a shadow model) that could be created by replicating the victim's model. Other methods analyze the model's prediction scores to draw conclusions about the training data.
- Invert ML Model – Adversaries can use machine learning models' confidence scores, which are accessible through an inference API, to reconstruct the training data. By carefully crafting queries to the inference API, adversaries can extract private information that was part of the training data. This can lead to privacy breaches if the attacker is able to piece together data about sensitive features used in the algorithm.For example, consider a machine learning model trained to predict health outcomes based on patient data. By analyzing the confidence scores provided by the model's inference API, an attacker might be able to infer details about the patients' medical history or conditions, which are sensitive and private information.
- Extract ML Model - Adversaries can steal a copy of a private machine learning model by making repeated requests to the model's API and collecting its responses. They use these responses to train a new model that behaves like the original. This allows them to avoid paying for each use of the model in a service that charges per query. Stealing the model in this way is also a form of theft of the model's intellectual property, as they obtain the valuable model without permission.

#### Techinque Exfiltration via Cyber Means

Adversaries may exfiltrate ML artifacts or other information relevant to their goals via traditional methods of exfiltration that have been detailed in the ATT&CK framework.

#### Technique LLM Meta Prompt Extraction

See above.

#### Technique LLM Data Leakage

Adversaries can manipulate large language models (LLMs) by providing specific prompts that cause the LLM to reveal sensitive information. This could include private data about individuals or confidential company information. The sensitive information might originate from the LLM's training data, other databases it has access to, or even from previous interactions with different users of the LLM.

### Impact

An adversary aims to disrupt or damage your AI systems and data, affecting their availability, integrity, or credibility. This could involve actions like tampering with or destroying data to hinder business operations or sway them in the attacker's favor. While some processes may appear normal, they could have been subtly altered to serve the adversary's objectives, ultimately enabling them to achieve their goals or mask a breach of data confidentiality.

For example, an attacker might target a company's AI-powered supply chain system, subtly altering the algorithm to delay the delivery of critical components. This manipulation not only disrupts the company's operations but could also give the attacker's affiliated businesses a competitive advantage.

#### Technique Evade ML Model

See above.

#### Technique Denial of ML Service

- Adversaries attack machine learning systems by overwhelming them with an excessive number of requests, aiming to slow down or completely shut down the service. Many machine learning systems rely on specialized and costly computing resources, making them vulnerable to becoming overloaded. Attackers can deliberately create inputs that force the machine learning system to perform a large amount of unnecessary computations, further straining the system's resources.

#### Spamming ML System with Chaff Data

Adversaries might flood the machine learning system with irrelevant or meaningless data, known as chaff data, leading to an increase in false detections. This can result in analysts at the targeted organization spending valuable time reviewing and rectifying incorrect inferences, thus diverting their attention from genuine threats.

> Chaff refers to small strips of metal or radar-reflective material that are released into the air by aircraft to confuse and distract enemy radar systems by creating numerous false targets.

#### Erode ML Model Integrity

Adversaries can weaken the performance of a target machine learning model by feeding it adversarial data inputs, gradually undermining trust in the system. This erosion of confidence can cause the victim organization to waste time and money trying to repair the system and resorting to manual processes for tasks that were supposed to be automated by the model.

#### Cost Harvesting

Adversaries can attack various machine learning services by sending pointless queries or inputs that require a lot of computing power. This tactic aims to increase the operational costs for the victim organization. One specific form of adversarial data, known as sponge examples, is crafted to maximize energy consumption, thereby driving up the cost of running the services.

#### External Harms

Adversaries may exploit their access to a victim's system to carry out actions that cause damage beyond the immediate system. This damage can impact various aspects of the organization, its users, or society as a whole.  

The External Harms technique is detailed in 5 sub-techniques:

- Financial Harm – the loss of money, property, or other valuable assets due to various illegal activities. This can include theft, where the adversary directly steals money or assets; fraud or forgery, where the adversary deceives the victim to gain financial advantage; or extortion, where the adversary pressures the victim into giving them money or financial resources.
- Reputational Harm – a decrease in the public's perception and trust in an organization. This can be caused by various incidents, such as scandals where the organization is involved in unethical or illegal activities, or false impersonations where someone pretends to be associated with the organization to mislead others. These events can damage the organization's image and reputation, leading to a loss of trust from customers, partners, and the general public.
- Societal Harm –negative outcomes that affect the general public or specific vulnerable groups. One example of societal harm is the exposure of children to vulgar or inappropriate content. This type of harm can have a wide-reaching impact on society, as it can influence the behavior and development of children and contribute to a decline in social values and norms.
- User Harm – various types of negative impacts that individuals, rather than organizations, experience as a result of an attack. These harms can include financial losses, such as stolen money or unauthorized transactions, and reputational damage, such as defamation or identity theft. The key difference is that these harms are directed at or experienced by individual users rather than at the broader organizational level.
- ML Intellectual Property Theft - Adversaries may steal machine learning  artifacts, such as proprietary training data and models, to harm the victim organization economically and gain an unfair advantage.Collecting and annotating training data is an expensive and time-consuming process. If adversaries steal this data, they can save on these costs and potentially undermine the competitive edge of the victim organization.  Furthermore, Machine Learning as a Service (MLaaS) providers typically charge for access to their APIs. If an adversary steals an ML model, they can use the service without paying, leading to financial losses for the service provider and unauthorized access to valuable intellectual property.

> Source: [Foundations of AI Security - ATTACKIQ Academy](https://www.academy.attackiq.com/courses/foundations-of-ai-security)

## Resources

- [Foundations of AI Security - ATTACKIQ Academy](https://www.academy.attackiq.com/courses/foundations-of-ai-security)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [AI Security 101](https://atlas.mitre.org/resources/ai-security-101)
- [MITRE | ATLAS Playlist](https://www.youtube.com/playlist?list=PLkTApXQou_8J6-t2_7QTTVDLBQlKFXPOu)
- [MITRE | ATLAS - Github](https://github.com/mitre-atlas)
- [AI under Attack - How to Secure Machine Learning in Security Systems - Alexander Chistyakov, Alexey Andreev - Kaspersky Threat Research](https://content.kaspersky-labs.com/se/media/en/business-security/enterprise/machine-learning-cybersecurity-whitepaper.pdf)
- [Case studies from Mitre ATLAS](https://atlas.mitre.org/studies)