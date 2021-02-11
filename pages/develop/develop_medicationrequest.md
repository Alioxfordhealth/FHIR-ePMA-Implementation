---
title: MedicationRequest
keywords: design
tags: [design]
sidebar: overview_sidebar
permalink: develop_medicationrequest.html
summary: Implementation guidance for populating and consuming the FHIR MedicationRequest resource
---

## Introduction

Links to the definitions of the **MedicationRequest** resource within the specifications covered within this guidance.

- MedicationRequest resource within [STU3](https://www.hl7.org/fhir/STU3/medicationrequest.html)
- MedicationRequest resource within [CareConnect](https://fhir.hl7.org.uk/STU3/StructureDefinition/CareConnect-MedicationRequest-1)
- MedicationRequest resource within [R4](http://hl7.org/fhir/medicationrequest.html)

## Minimum Viable Product (MVP)

Elements marked as **MVP** denote those recommended to be required for an MVP for the target use case.

### Elements common across FHIR Versions

| Element                                                               | MVP     |
| --------------------------------------------------------------------- | ------- |
| [id](develop_medicationrequest.html#id)                               | **MVP** |
| text                                                                  |         |
| identifier                                                            |         |
| [status](develop_medicationrequest.html#status)                       | **MVP** |
| [intent](develop_medicationrequest.html#intent)                       | **MVP** |
| [category](develop_medicationrequest.html#category)                   | **MVP** |
| priority                                                              |         |
| [medication](develop_medicationrequest.html#medicationx)              | **MVP** |
| [subject](develop_medicationrequest.html#subject)                     | **MVP** |
| supportingInformation                                                 |         |
| [authoredOn](develop_medicationrequest.html#authoredOn)               | **MVP** |
| [requester](develop_medicationrequest.html#requester)                 | **MVP** |
| [recorder](develop_medicationrequest.html#recorder)                   | **MVP** |
| reasonCode                                                            |         |
| reasonReference                                                       |         |
| note                                                                  |         |
| [dosageInstruction](develop_medicationrequest.html#dosageInstruction) | **MVP** |
| dispenseRequest                                                       |         |
| [substitution](develop_medicationrequest.html#substitution)           | **MVP** |
| priorPrescription                                                     |         |
| detectedIssue                                                         |         |
| eventHistory                                                          |         |

### Elements as CareConnect Extensions

| Element           | MVP |
| ----------------- | --- |
| repeatInformation |     |
| statusReason      |     |
| prescriptionType  |     |

### Elements New in R4

| Element                                | MVP |
| -------------------------------------- | --- |
| statusReason                           |     |
| doNotPerform                           |     |
| reported[x]                            |     |
| encounter                              |     |
| performer                              |     |
| performerType                          |     |
| instantiatesCanonical                  |     |
| instantiatesUri                        |     |
| courseOfTherapyType                    |     |
| insurance                              |     |
| dispenseRequest.initialFill            |     |
| dispenseRequest.dispenseInterval       |     |
| dispenseRequest.numberOfRepeatsAllowed |     |

### Elements Removed from R4

| Element              | MVP |
| -------------------- | --- |
| context              |     |
| definition           |     |
| requester.agent      |     |
| requester.onBehalfOf |     |

## MedicationRequest elements

### id

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Id</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Unique logical identifier for the resource.</td>
  </tr>
</table>

It is highly recommended that the logical id value is a Universally Unique Identifier (UUID) using a standard UUID generator available in most programming languages.

The most comprehensive current definition of the logical id is within the FHIR [R4](https://hl7.org/fhir/R4/resource.html#Resource) standard. The key phrase from the FHIR standard is the logical id is "..._assigned by the server responsible for storing it_".

Vendors have advised that there could be several different scenarios where a MedicationRequest could be stored and it is an implementation decision about where the master MedicationRequest should reside. Possible scenarios for the master MedicationRequest location include;

- ePMA system FHIR Server
- Dispensing system FHIR Server
- Centrally hosted FHIR server (inside hospital network)
- Centrally hosted FHIR server (cloud hosted) **Note**: This would be the scenario when the NHS Electronic Prescription Service implements the FHIR standard.

A further implementation decision is who creates the logical id. The following is in line with the [STU3 FHIR RESTful standards](http://hl7.org/fhir/STU3/http.html#update) and [R4 FHIR RESTful standards](https://hl7.org/fhir/R4/http.html).

- **Server**: If the hosting system creates the logical id then the client system would send the MedicationRequest (without an id) as a REST POST command. The created logical id should always be included within the HTTP 201 response so the client can persist this id. For centrally hosted FHIR servers it should always be the hosting system the creates the id so that the server can be responsible for logical id uniqueness.
- **Client**: If the client system creates the logical id then the client system would send the MedicationRequest (with an id) as a REST PUT command. The REST PUT command will update a record, but if it does not exist create a new record. If the client system sends the MedicationRequest resource via an asynchronous interoperability pattern then the logical id must be populated with a UUID by the client system. An example would be sending a MedicationRequest within a FHIR payload via the NHS Digital MESH platform.

If either a client or server wants to use an additional business identifier this would be added as an MedicationRequest.identifier and the minimum requirement is:

- A system value: defining the source of the system it has come from
- A value: giving the identifier value

If a client system creates the logical id and there are multiple clients sending MedicationRequests there is a possibility a duplicate logical id occurs. In this event the duplicate id (the second code received) will be rejected with a response stating the logical id is duplicated. The requesting system will need to send the request again with a new logical identifier.

For this reason, within an implementation where multiple clients are POSTing to a FHIR server, it is highly recommended that the FHIR server creates the logical id to remove the risk of duplication.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### identifier

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Identifier</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>External ids for this request. Identifiers associated with this medication request that are defined by business processes and/or used to refer to it when a direct URL reference to the resource itself is not appropriate. They are business identifiers assigned to this resource by the performer or other systems and remain constant as the resource is updated and propagates from server to server.</td>
  </tr>
</table>

Use if a local implementation requires bespoke identifiers to track medication requests between the ePMA and pharmacy systems.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### text

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Narrative</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Text summary of the resource, for human interpretation</td>
  </tr>
</table>

{% include important.html content="The use of this element is under NHS Digital technical review." %}

It is recommended this element is **not implemented** as part of an MVP.

or

Where the user of the ePMA system has selected a pre-defined medication order, often known as an "_order sentence_", the textual description of the order sentence should be populated as the **text** for this resource. This is so that the text chosen on-screen by the user is captured within the MedicationRequest resource. Where an ePMA system does not use a concept akin to selecting an order sentence then this element does not form part of the recommended MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### status

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Code</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required (STU3) Mandatory (R4) 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A code specifying the current state of the medication order.</td>
  </tr>
</table>

The cardinality of **status** has been changed from 0..1 in STU3 to 1..1 in R4.

When used it must be populated with a fixed value set defined within the FHIR standard.

It is expected that most implementations will require the use of **status** to support workflow.

The scope of **status** may vary depending on the nature of the implementation. The FHIR standard defines the status of `Completed` as "_All actions that are implied by the prescription have occurred_". This allows for different design decisions for tracking a medication request status.

- An implementation may track the status through to the receipt of the medication request at the pharmacy, after which the status is `completed`
- An implementation may track the status through to completion of dispensing events, after which the status is `completed`
- An implementation may track the status through to completion of administration events for the medication that has been dispensed, after which the status is `completed`.

The following guidance is based on **an implementation tracking the status through to completion of dispensing events**.

| Status             | FHIR Definition                                                                                                                                                                                                                                                       | Implementation Guidance                                                                                                                                    |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Draft`            | The prescription is not yet 'actionable', e.g. it is a work in progress, requires sign-off, verification or needs to be run through decision support process.                                                                                                         | The order is work in progress within the ePMA system and has not yet sent to the pharmacy.                                                                 |
| `Active`           | The prescription is 'actionable', but not all actions that are implied by it have occurred yet.                                                                                                                                                                       | The order has been sent and accepted by the pharmacy. Dispensing and administration activities may of started but are not yet complete.                    |
| `Completed`        | All actions that are implied by the prescription have occurred.                                                                                                                                                                                                       | Dispensing activities have been completed for the medication defined within the order.                                                                     |
| `On Hold`          | Actions implied by the prescription are to be temporarily halted, but are expected to continue later. May also be called 'suspended'.                                                                                                                                 | Will prevent the order being sent to the pharmacy. If already sent, an update needs to be sent to the pharmacy to temporarily halt further dispensing.     |
| `Cancelled`        | The prescription has been withdrawn before any administrations have occurred.                                                                                                                                                                                         | Will prevent the order being sent to the pharmacy. If already sent, an update needs to be sent to the pharmacy so that no further medication is dispensed. |
| `Stopped`          | Actions implied by the prescription are to be permanently halted, before all of the administrations occurred. This should not be used if the original order was entered in error.                                                                                     | The order needs to be stopped on clinical grounds. An update needs to be sent to the pharmacy so that no further medication is dispensed.                  |
| `Entered in Error` | Some of the actions that are implied by the medication request may have occurred. For example, the medication may have been dispensed and the patient may have taken some of the medication. Clinical decision support systems should take this status into account.  | The order needs to be stopped due to human data entry error. An update needs to be sent to the pharmacy so that no further medication is dispensed.        |
| `Unknown`          | The authoring/source system does not know which of the status values currently applies for this observation. Note: This concept is not to be used for 'other' - one of the listed statuses is presumed to apply, but the authoring/source system does not know which. | **Recommended not to be supported** as the use case for this status value is unclear.                                                                      |

#### Status transitions

This state transition diagram is an enhancement over the generic [State Machine](https://www.hl7.org/fhir/request.html#statemachine) defined within the FHIR R4 specification. It includes the status values associated with a medication request with transitions applicable to a UK implementation.

![Status Transitions](images/medicationrequest_status_diagram.jpg)

| Previous Status | Future Status      | Interoperability Guidance                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| --------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Draft`         | `Active`           | This transition will trigger the sending/sharing of the MedicationRequest from the ePMA system to the pharmacy system to start dispensing activities. Within a RESTful implementation this would be typically implemented as an HTTP POST.                                                                                                                                                                                                                                                                   |
| `Draft`         | `Cancelled`        | Contained within the ePMA system.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `Draft`         | `On-Hold`          | Contained within the ePMA system.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `On-Hold`       | `Draft`            | Contained within the ePMA system.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `On-Hold`       | `Active`           | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to restart dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH.                                                                                                                                                                                                                                                            |
| `Active`        | `Active`           | Not a MedicationRequest status transition but the pharmacy system could send/share dispensing activities with the ePMA system, typically using a FHIR profile based on **MedicationDispense**. Within a RESTful implementation this would be typically implemented as an HTTP POST.                                                                                                                                                                                                                          |
| `Active`        | `On-Hold`          | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to suspend dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH. If dispensing has already occurred but meds have not been delivered to the ward then they can stay within the pharmacy until the request is re-activated. If meds have been delivered to the ward then there is no action required by the pharmacy system. |
| `Active`        | `Entered in Error` | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to stop dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH.                                                                                                                                                                                                                                                               |
| `Active`        | `Stopped`          | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to stop dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH.                                                                                                                                                                                                                                                               |
| `Active`        | `Completed`        | Contained within the ePMA system. All requested medication has been received from pharmacy and has been recorded/confirmed within the ePMA system.                                                                                                                                                                                                                                                                                                                                                           |

Jump back to [top](develop_medicationrequest.html)

<hr/>

### intent

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>code</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code>  <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Describes the nature of the medication request.</td>
  </tr>
</table>

The value `order` should always be used to denote this is a medication request order.

FHIR R4 extends the value set to; `proposal`, `plan`, `order`, `original-order`, `reflex-order`, `filler-order`, `instance-order` and `option`. The recommendation for the target use case MVP is to continue to use `order` unless it is locally decided that the extended R4 value set better supports the business requirements.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### category

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code>  <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Type of medication usage. Indicates the type of medication request. For example, where the medication is expected to be consumed or administered, i.e. inpatient or outpatient.</td>
  </tr>
</table>

It is expected that any implementation will need to distinguish between medication orders for processes for dispensing and/or administration so this element is **business required**.

The STU3 suggested value-set is defined as; `inpatient`, `outpatient` and `community`. The R4 suggested value-set is extended with `discharge`. For a UK implementation, it is recommended to further extend with `leave`.

| Category     | FHIR Definition / Description                                                                                                                                                                                                                                                                                                   |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `inpatient`  | Includes requests for medications to be administered or consumed in an inpatient or acute care setting.                                                                                                                                                                                                                         |
| `outpatient` | Includes requests for medications to be administered or consumed in an outpatient setting (for example, Emergency Department, Outpatient Clinic, Outpatient Surgery, Doctor's office).                                                                                                                                          |
| `community`  | Includes requests for medications to be administered or consumed by the patient in their home (this would include long term care or nursing homes, hospices, etc.).                                                                                                                                                             |
| `discharge`  | Includes requests for medications created when the patient is being released from a facility.                                                                                                                                                                                                                                   |
| `leave`      | **Note: Not included within the FHIR standard.** Requests for medications that the patient will take away with them during any short break from inpatient care. Typically requests would be dispensed by the hospital pharmacy to be self-administered at home with or without the assistance of community based nursing staff. |

#### Category values mapped to target use cases

The in-scope use cases for this version of implementation guidance are;

**Inpatient**

- _Use case_ : Inpatient medication requests, for a named patient, to be dispensed by the hospital pharmacy and intended for administration on a hospital ward
- _Category_ : `inpatient`
- _Example scenario_ : Patient Ruby White is admitted as an inpatient is prescribed Amoxicillin 500mg orally three times daily for a suspected chest infection

**Inpatient**

- _Use case_ : Medication requests, for a named patient who is on short-term leave from an inpatient stay (but is not discharged), to be dispensed by the hospital pharmacy and intended for administration at home
- _Category_ : `leave`
- _Example scenario_ : Current inpatient Susan Scarlet is going home for weekend leave and is prescribed 3 days supply of Citalopram 20mg tablets one to be taken in the morning.

**Inpatient**

- _Use case_ : Discharge medications requests, for a named patient, to be dispensed by the hospital pharmacy and issued on discharge for administration at home
- _Category_ : `discharge`
- _Example scenario_ : Patient Laura Brown is discharged from hospital following an admission where she was diagnosed as being diabetic. She was prescribed 4 weeks quantity of Metformin 500mg tablets to take twice daily on an on-going basis. Her GP will be instructed to continue this course of medication within a discharge note.

**Outpatient**

- _Use case_ : Outpatient medication requests, for a named patient, to be dispensed by the hospital pharmacy and intended for administration in the Outpatients department, Accident and Emergency department, or Day unit
- _Category_ : `outpatient`
- _Example scenario_ : Patient John Green is prescribed a Goserelin 3.6mg implant to be administered by IM injection in the Outpatients department on 1st May 2020.

**Outpatient**

- _Use case_ : Outpatient medication requests, for a named patient, to be dispensed by the hospital pharmacy for administration at home
- _Category_ : `discharge`
- _Example scenario_ : Patient James Black has attended the dermatology outpatients clinic and is prescribed Betamethasone 0.1% cream to be applied sparingly twice daily for 2 weeks.

**Note**: A `community` medication request would trigger the printing and signing of a paper FP10HNC prescription, or (when implemented by the Trust) an electronic prescription sent to the NHS Electronic Prescription Service.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### priority

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Code</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates how quickly the medication request should be addressed with respect to other requests.</td>
  </tr>
</table>

Recommended **not** to be used within an implementation or **used with caution**.

The stating of a priority, in any business context including healthcare, is often de-valued as given the choice, every clinician wants medication urgently for their patients.

The FHIR standard uses a fixed value-set of `routine`, `urgent`, `asap` and `stat`, which denote a priority in increasing orders of magnitude, with `stat` being the highest possible priority, e.g. an emergency.

The use of the term `stat` is potentially confusing as when used within a dosage instruction can also mean "_give once immediately_".

If to be used, consider only initially supporting `routine` and `urgent` and set clear criteria for when a medication request should be marked as `urgent`.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### medication[x]

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference or CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Medication requested for dispensing.</td>
  </tr>
</table>

Where the requested medication is contained within the NHS dm+d then it must be recorded using the dm+d standard. Implementation is recommended to be via a referenced [Medication](develop_medication.html) resource.

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

**Note**: At the time of writing an alpha implementation of a dm+d FHIR Medication Resource Server is available from the North East CSU as a [demonstrator](https://dmdsite-uks-test-web.azurewebsites.net) and associated [API](https://apidmd001.azurewebsites.net/index.html).

It is recommended that the `medicationReference.display` is populated with the medication description as selected by the clinician. This may be slightly different to the medication described as returned by a SNOMED/dm+d terminology FHIR server if the ePMA system has not fully implemented dm+d into their medication picking list.

#### Requested medication with no dm+d code

Medication not published within the dm+d may be requested in the Acute care setting. In this scenario it is recommended to use the **CodeableConcept** variant for this element. Software logic can then clearly distinguish this from nationally coded dm+d medication.

If the ePMA system has both a locally assigned code and description for the medication then;

- The `medicationCodeableConcept.text` should be the description for the medication.
- The `medicationCodeableConcept.coding.code` should be the code for the medication.
- The `medicationCodeableConcept.coding.display` should be the description for the medication, i.e. the same value as `medicationCodeableConcept.text`.

If the ePMA system only has a description for the medication then;

- The `medicationCodeableConcept.text` should be the locally assigned description for the medication.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### subject

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A link to a resource representing the person or set of individuals to whom the medication will be given. </td>
  </tr>
</table>

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

**Note**: It is acknowledged that a typical Hospital Patient Administration System (PAS) available today will not expose a FHIR interface so referencing by URL will most likely not be available for some time. However this should be a target architecture so that the FHIR-enabled PAS can be used as a trusted source of Patient resources across multiple hospital systems.

See population of a [Patient](develop_patient.html) resource.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### supportingInformation

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Information to support ordering of the medication.</td>
  </tr>
</table>

See the [Overview](develop_overview.html) page for guidance on using FHIR References.

It is recommended this element is **not implemented** as part of an MVP.

Can reference any number of FHIR resources and if implemented, some examples of use include;

- Reference an **Observation** resource to share data like the patient's height and weight.
- Reference a **Condition** resource to share a patient's condition if this influences the pharmacy dispensing process. For example; "105502003 Dependence on renal dialysis (finding)“ or “46177005 End stage renal disease (disorder)" would justify an unusual dosage on a prescription.
- Reference an **AllergyIntolorance** resource to share a patient's allergy to make it clear why certain medication is being requested. For example, a Penicillin allergy.
- Reference a **CarePlan** resource where medication dispensing is considered as part of a specific treatment regimen. For example, care plans for stroke patients.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### authoredOn

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>dateTime</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The date, and maybe also time, when the prescription was initially written.</td>
  </tr>
</table>

Recommended as business required element for most implementations.

Recommended to specify as a complete date and time, e.g. "2020-03-26T15:00:00". Note that the FHIR specification requires that if hours and minutes are specified, a time zone shall be populated, e.g. `2020-03-26T15:00:00+01:00`.

Recommended that the date and time is the same as recorded and visible within the ePMA system.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### requester

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The clinician requesting the medication.</td>
  </tr>
</table>

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

Recommended as a required business element for most MVP implementations.

Recommended to be the **prescriber** recorded on the ePMA system for the medication request.

Recommended to implement as a reference a FHIR [Practitioner](develop_practitioner.html) resource.

Where an implementation requires the identification of a person plus an organisation then the solution differs depending on the version of FHIR. Within an STU3 implementation, reference an **Organization** resource within the **requester.onBehalfOf** element. Within an R4 implementation replace the **Practitioner** resource with a **PractitionerRole** resource.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### recorder

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The user who entered the data entry to record the medication request.</td>
  </tr>
</table>

Recommended as a required business element for most MVP implementations to be used as an **additional point of contact for the pharmacy**, together with the [requester](develop_medicationrequest.html#requester) for any queries related to the medication request.

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

#### Requester and Recorder combination examples

| Scenario                                                                                                                         | Requester | Recorder |
| -------------------------------------------------------------------------------------------------------------------------------- | --------- | -------- |
| Prescribing clinician 'Fred' is requesting new medication                                                                        | Fred      | Fred     |
| Prescribing clinician 'Fred' is re-ordering previous medication he previously prescribed                                         | Fred      | Fred     |
| Prescribing clinician 'Jane' is re-ordering previous medication, without any clinical changes, that 'Fred' previously prescribed | Fred      | Jane     |
| Prescribing clinician 'Jane' is re-ordering previous medication that 'Fred' previously prescribed, with clinical changes         | Jane      | Jane     |
| Nurse 'Roger' is ordering medication prescribed by 'Fred'                                                                        | Fred      | Roger    |
| Pharmacy technician 'Sally' is re-ordering previous medication prescribed by 'Fred'                                              | Fred      | Sally    |

Jump back to [top](develop_medicationrequest.html)

<hr/>

### reasonCode

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Reason or indication for requesting the medication for the patient.</td>
  </tr>
</table>

Optional but useful to the wider clinical team as an additional safety check, especially if the requested medication is normally prescribed for different reasons, to avoid confusion between clinical teams. Recording an indication against a medication request also gives valuable insight when data is collated for secondary uses, especially if linked with outcome data.

Where possible this should be a coded term from the SNOMED-CT hierarchy as a descendant of the concept 404684003 (Clinical finding) however but free-text reasons are also acceptable.

It is recommended that if implemented, **reasonCode** and [reasonReference](develop_medicationrequest.html#reasonReference) are mutually exclusive and not both populated.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### reasonReference

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Condition or observation that supports why the prescription is being written.</td>
  </tr>
</table>

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

A reference to a FHIR **Condition** or **Observation** resource.

It is recommended this element is **not implemented** as part of an MVP. However if the clinical system has recorded the reason for medication, often known as the "indication", as either a **Condition** or **Observation** resource, a logical link to that resource has business benefit.

It is recommended that if implemented, [reasonCode](develop_medicationrequest.html#reasonCode) and **reasonReference** are mutually exclusive and not both populated.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### basedOn

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A plan or request that is fulfilled in whole or in part by this medication request.</td>
  </tr>
</table>

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

A reference to any number of **CarePlan**, **MedicationRequest**, **ServiceRequest** or **ImmunizationRecommendation** resources.

It is recommended this element is **not implemented** as part of an MVP to reference a previous medication request. Instead use [priorPrescription](develop_medicationrequest.html#priorPrescription).

If the clinical system has implemented the **CarePlan** resource, a logical link to the care plan for which the medication request is based has business benefit.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### groupIdentifier

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Identifier</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A shared identifier common to all requests that were authorized more or less simultaneously by a single author, representing the identifier of the requisition or prescription.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

The ability to group medication requests from the same [requester](develop_medicationrequest.html#requester), and in additional for the same patient may add business benefit in an enhanced implementation. Such medication requests may or may not be contained within the same interaction Bundle. Where Bundles are not used, or where requests are split across Bundles then the **groupIdentifier** provides the means to link requests by the same prescriber for the same patient.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### note

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Annotation</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Information about the prescription for the dispenser.</td>
  </tr>
</table>

An optional element for when the ePMA user wishes to provides supporting textual information to the pharmacy. This element **must not** be used for dosing instructions, see [dosageInstruction](develop_medicationrequest.html#dosageInstruction).

Where no supporting information is required, this element can be omitted.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### dosageInstruction

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Dosage</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>How the medication should be taken.</td>
  </tr>
</table>

A required business element for all MVP implementations. Population of just the `dosageInstruction.text` element would be unacceptable for a successful implementation.

Refer to [FHIR Dose Syntax Implementation Guidance](https://developer.nhs.uk/apis/dose-syntax-implementation/) (or any subsequent version) for guidance.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### dispenseRequest

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>BackboneElement</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Information related to the dispensing of the medication.</td>
  </tr>
</table>

Used to convey specific dispensing requests to the pharmacy that are not otherwise detailed within the [dosageInstruction](develop_medicationrequest.html#dosageinstruction).

The element is not deemed business required for the MVP but should be considered for an implementation.

For most medication requests the hospital pharmacy will typically dispense a quantity of medication appropriate for the medication and dosage, as per their local agreed best practice, to balance the quantity of medication held in pharmacy to that held on the ward.

The inclusion of a **dispenseRequest** may be useful when requesting **discharge** medication requests. It would instruct the pharmacy to dispense a specific quantity of medication, either expressed as a number of days or as a dose form quantity.

For example;

Original order = Medication:`Paracetomol` Dosage:`1g four times a day`

Discharge order = Medication:`Paracetomol` Dosage:`1g four times a day` Dispense Request:`7 days` or `56 tablet`

Another use case would be any inpatient scenario where the pharmacy is instructed to dispense a specific quantity of medication. For example, if the patient is known to be discharged soon.

For example;

Original order = Medication:`Atenolol` Dosage:`50mg daily`

Re-Supply order = Medication:`Atenolol` Dosage:`50mg daily` Dispense Request:`3 tablet`

Jump back to [top](develop_medicationrequest.html)

<hr/>

### substitution

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>BackboneElement</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates whether or not substitution can or should be part of the dispense. In some cases, substitution must happen, in other cases substitution must not happen. This block explains the prescriber's intent. If nothing is specified substitution may be done.</td>
  </tr>
</table>

Within UK healthcare, substitution is not the norm so the international FHIR definition where "_If nothing is specified substitution may be done._" does to align with UK healthcare prescribing best practice.

It could be unwise to assume all UK implementations will prevent substitution if not explicitly stated, especially if the same clinical system has been previously implemented outside the UK. It is therefore recommended that this element is **business required** with a default boolean value of `false` to denote substitution is **not** allowed.

#### Allowing substitution

Where substitution to be be allowed, set to "**true**". The inclusion of the coded reason is optional as the value-set defined in FHIR is of limited benefit to UK healthcare.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### priorPrescription

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>An order/prescription that is being replaced AND a link to a resource representing an earlier order related order or prescription.</td>
  </tr>
</table>

An optional element for when either the ePMA or pharmacy system would benefit from being able to link re-supply requests with a previous request.

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

The published FHIR specifications described this element is slightly different ways in different parts of the FHIR specification;

"_A link to a resource representing an earlier order related order or prescription_"

and

"_An order/prescription that is being replaced_".

The following guidance applies to each use case.

#### Linking to an earlier order

A medication request that is a [re-supply medication request](explore_overview.html#re-supply-medication-request) based on a previous request referenced within **priorPrescription**. This would allow both the ePMA and pharmacy systems to logically link requests and add verification checks to flag any differences to the user.

#### Linking to an order that is being replaced

The medicationRequest being replaced will be referenced within **priorPrescription**. It would be expected that the referenced resource would be updated with a [status](develop_medicationrequest.html#status) of `cancelled`, `entered-in-error` or `stopped`. This will allow both the ePMA and pharmacy systems to make it clear to the human user that one medication request replaces another.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### detectedIssue

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates an actual or potential clinical issue with or between one or more active or proposed clinical actions for a patient; e.g. Drug-drug interaction, duplicate therapy, dosage alert etc.</td>
  </tr>
</table>

It is recommended this element is **optional** for an MVP implementation.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### eventHistory

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Links to Provenance records for past versions of this resource or fulfilling request or event resources that identify key state transitions or updates that are likely to be relevant to a user looking at the current version of the resource.</td>
  </tr>
</table>

It is recommended this element is **optional** for an MVP implementation.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### extension (repeatInformation)

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Extension</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>CareConnect</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Medication repeat information extension to support the NHS Electronic Prescription Service and electronic repeat dispensing.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### extension (statusReason)

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Extension</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>CareConnect</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>To record the reason the medication (plan or order) was stopped and the date this occurred.</td>
  </tr>
</table>

This extension was added to the international standard within FHIR R4 as [statusreason](develop_medicationrequest.html#statusreason) so provides a migration path from CareConnect to R4.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### extension (prescriptionType)

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Extension</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>CareConnect</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>To record the type of prescription. An extension to support the NHS Electronic Prescription Service for the NHSBSA to identify the type of prescription.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

**Note**: The value set for this STU3 extension aligns with the legacy HL7v3 'PrescriptionTreatmentType' vocab; `acute`, `repeat`, `repeat dispensing` and `delayed prescribing`. If UK Core R4 is extended to support this type of data then the extension name should ideally not be called 'prescriptionType' as it confused with a different legacy HL7v3 vocab for 'prescriptionType' which serves a different purpose.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### statusReason

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Captures the reason for the current state of the MedicationRequest.</td>
  </tr>
</table>

Within the CareConnect implementation of STU3, this element was modelled as an [extension (statusreason)](develop_medicationrequest.html#extension-statusreason). If used as part of a CareConnect implementation then it would migrate to being the **statusReason** within an R4 implementation.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### doNotPerform

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Boolean</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>If true indicates that the provider is asking for the medication request not to occur.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### reported[x]

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Boolean or Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates if this record was captured as a secondary 'reported' record rather than as an original primary source-of-truth record. It may also indicate the source of the report.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### encounter

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The Encounter during which this MedicationRequest was created or to which the creation of this record is tightly associated.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Within STU3, this element was modelled as the [context](develop_medicationrequest.html#context) so if used as part of an STU3 implementation then it would migrate to being the **encounter** within an R4 implementation.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### performer

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The specified desired performer of the medication treatment, e.g. the performer of the medication administration.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

**IMPORTANT**: Do not confuse this element with **dispenseRequest.performer** which defines any intended dispenser for the medication request using a referenced FHIR [Organization](develop_organization.html) resource.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### performerType

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates the type of performer of the administration of the medication.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### instantiatesCanonical

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>canonical</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The URL pointing to a protocol, guideline, orderset, or other definition that is adhered to in whole or in part by this MedicationRequest.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### instantiatesUri

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>uri</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The URL pointing to an externally maintained protocol, guideline, orderset or other definition that is adhered to in whole or in part by this MedicationRequest.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### courseOfTherapyType

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The description of the overall pattern of the administration of the medication to the patient.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)<hr/>

### insurance

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Insurance plans, coverage extensions, pre-authorizations and/or pre-determinations that may be required for delivering the requested service.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### context

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A link to an encounter or episode of care, that identifies the particular occurrence or set occurrences of contact between patient and health care provider.</td>
  </tr>
</table>

See the [Overview](develop_overview.html#using-fhir-references) page for guidance on using FHIR References.

This element has been removed from the FHIR R4 standard.

If implemented as a reference to an **Encounter** resource, within R4 this is supported by a new [encounter](develop_medicationrequest.html#encounter) element, giving a clear migration path.

Any reference to an **EpisodeOfCare** resource within an STU3 or CareConnect implementation will not be supported if migrated to FHIR R4. It is recommended not to reference an EpisodeOfCare resource within an STU3 implementation.

Jump back to [top](develop_medicationrequest.html)

<hr/>

### definition

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required / Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Protocol or definition followed by this request.</td>
  </tr>
</table>

It is recommended this element is **not implemented** as part of an MVP.

This element has been removed from FHIR R4 therefore there is no migration path from an STU3 or CareConnect implementation.

Jump back to [top](develop_medicationrequest.html)

<hr/>
