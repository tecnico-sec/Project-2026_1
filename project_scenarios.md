Instituto Superior Técnico, Universidade de Lisboa

**Network and Computer Security**

# Preamble

This document explains the project scenarios.  
Please read the project overview first.

The scenarios describe a richer application context than the one that will be actually needed to be implemented.  
The security aspects should be the focus of your work.
User interface design, although critical in a real application, is secondary in this project.

Each project team can change the document format for each scenario, but the fields shown must still be included in some way.

# Project Scenarios

_Congratulations! You have been hired by one of the following companies._

Each company has its business context and, more importantly, a document format that is pivotal for their business.  
Later in the project, a security challenge will be revealed and your team will have to respond to it with a suitable solution.

**Note: each group should pick and handle just one of the security challenges**

----

# Scenarios

- [DeathNode](#DeathNode)
- [ChainOfProduct](#ChainOfProduct)
- [CivicEcho](#CivicEcho)

----

## DeathNode

DeathNode is an anonymous reporting platform used by a clandestine group of individuals calling themselves The Cult of Kika.
Members report details of alleged crimes or suspects to the network before such information reaches the authorities. The system must ensure full username anonymity of participants, prevent tampering of reports, and resist attacks attempting to deanonymize or block users. DeathNode operates as a peer-backed system, where each node stores encrypted reports locally and periodically synchronizes with all peers via a server. 

A sample core data exchanged between nodes follows the JSON structure below:

```json
{
  "report_id": "abc123",
  "timestamp": "2025-10-28T12:00:00Z",
  "reporter_pseudonym": "shadow_fox",
  "content": {
    "suspect": "john_doe",
    "description": "Alleged involvement in organized crime",
    "location": "Tokyo, Japan"
  },
  "version": 2,
  "status": "pending_validation"
}
```


### Protection Needs

Reports exchanged through DeathNode contain potentially sensitive information and must be protected to maintain participant anonymity and platform integrity. The following security requirements must be met:

- [SR1: Confidentiality] Only authorized nodes in the peer-to-peer network can decrypt reports.
- [SR2: Integrity 1] Each node must verify that a report has not been modified since it was first submitted.
- [SR3: Integrity 2] It must be possible to detect if any reports are missing, duplicated, or out of order during synchronization.
- [SR4: Consistency] Nodes must be able to validate that other peers provide consistent synchronization data and detect forged or diverging histories.

### Security Challenges - pick one

The _Cult_ leadership is worried that too much decentralization may actually be a disservice to the cause and now want to enforce member legitimacy.

#### Security Challenge A

Design an _authorization_ mechanism that lets new participants join the network without exposing personal information. 
A _separate server_, controlled by the leadership, must receive the joining request, establish a secure negotiated session, and provide time-limited access credentials enabling participation in the network.

#### Security Challenge B

Some members flood the network with fake/spam reports.
Design a protection mechanism where a _vigilant server_, appointed by the leadership, monitors the network and identifies misbehaving members.
The vigilant can then ban the member, meaning that reports from that member should no longer be accepted.
The ban can be temporary or permanent, as decided by the vigilant.

----

## ChainOfProduct

A Chain of Product (CoP) involves a network of companies that produce and deliver a product, from raw materials to the final customer.
It is often marked by distrust between businesses and the need to protect trade secrets. The smartphone company Samchunga chose to use a public third-party service to record and validate transactions among the companies participating in the CoP. This service keeps transactions confidential while allowing businesses to selectively disclose information to other parties when needed to establish trust.

The data structure of a delivery-vs-payment (DvP) transaction is a JSON document with the following structure:

```json
{
  "id": 123,
  "timestamp": 17663363400,
  "seller": "Ching Chong Extractions",
  "buyer": "Lays Chips",
  "product": "Indium", 
  "units": 40000,
  "amount": 90000000,
}
```

### Protection Needs

The third party must ensure the integrity and confidentiality of the transactions. The businesses should also be able to verify with whom the transaction was shared with. 

Ensure the following requirments are met:

- [SR1: Confidentiality] Only the seller, the buyer and the parties to which the transaction was disclosed to can see the transaction.
- [SR2: Authentication] Only the seller and the buyer can share transaction details with other parties. 
- [SR3: Integrity 1] The seller and the buyer can verify that the transaction information was not tampered with.
- [SR4: Integrity 2] The seller can verify with whom the buyer shared the transaction with and vice-versa. 

### Security Challenges - pick one

#### Security Challenge A

Deciding transaction disclosure for each individual partner is laborious.
Introduce the concept of _groups of partners_ where a transaction can now also be disclosed to a group.
When disclosed, all partners of the group can read and validate the transaction information.
A _separate server_ must dynamically track groups and members to enforce the rules valid at the moment.

#### Security Challenge B

Some companies are complaining that rivals are learning trade secrets due to the “all-or-nothing” transaction disclosure.
To address this, we now need to add _multiple protection layers_ to transaction documents to allow selective disclosure of data.
It should now be possible to disclose some parts of a transaction, but still keep others confidential.
A _separate server_ must dynamically track the disclosure of document sections to specific partners.

----

## CivicEcho

CivicEcho is a public participation system allowing citizens to report local issues (e.g., unsafe roads, corruption, or noise complaints) pseudo-anonymously, while municipalities verify authenticity and avoid spam or disinformation. Reports are transmitted through a centralized party that cannot access or modify them; its only role is to act as a mediator. Citizens should be able to share reports between other citizens. 

The data structure of a citizen report is a JSON document with the following structure:


```json
{
  "report_id": "echo_00218",
  "timestamp": "2025-10-05T14:30:00Z",
  "category": "infrastructure",
  "location": "Lisbon, Portugal",
  "coordinates": {
    "latitude": 38.72052,
    "longitude": -9.14583
  },
  "description": "Broken traffic light on Avenida da Liberdade",
}
```

### Protection Needs

The system must ensure anonymous reporting while preventing spam and maintaining network resilience.

Ensure the following requirements are met:

- [SR1: Confidentiality] Reports cannot be traced back to their author.
- [SR2: Integrity] Municipal servers must verify reports were not altered.
- [SR3: Authentication] Only verified citizens may submit reports.
- [SR4: Non-repudiation] Authorities must be able to verify that a valid report was received.

### Security Challenges - pick one

#### Security Challenge A

Some reports affect multiple municipalities like damage in shared roads or river pollution.
Citizens should now be able to share reports with other municipalities.
A _separate server_ must act as a dynamic *authentication bridge* between the home and external municipalities.
This works by treating each municipality as its own authentication realm, where the citizen’s home remains the same, and a trust link between realms allows access to the external municipality, without requiring citizens to create separate accounts there.

#### Security Challenge B

Some users repeatedly post the same issue to inflate its priority. 
To curb this, citizens can only make limited number of reports, enforced by a _separate server_ that manages publication _tokens_. 
The server authenticates the citizen and issues a limited set of tokens. 
Each report requires one token to be spent and each token can only be spent once.

---

[SIRS Faculty](mailto:meic-sirs@disciplinas.tecnico.ulisboa.pt)
