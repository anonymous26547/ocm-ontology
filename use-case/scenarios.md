## Use Case

The use case considers an imaginary application, **OncoAid**, designed to support cancer patient health monitoring by enabling them to manage their medical and health-related data. The app is integrated with a smartwatch that continuously collects and monitors vital signs (e.g., heart rate and blood pressure), which are stored in the patient's personal Knowledge Graph (KG).

In addition to real-time monitoring, OncoAid provides access to key medical data, including electronic medical records, lab results, imaging, medication plans, and doctor diagnostic notes. This data is maintained in the hospital's KG.

Patients using OncoAid can view and manage their personal data through their own KG while also accessing relevant data from the hospital KG. Doctors are responsible for managing hospital data and, when necessary, accessing the patient’s KG to provide informed medical care.

### Running Example

The running example, depicted in Figure `use-case`, illustrates the process of inpatient care at **CityCare Hospital**, where Alice is admitted for treatment. Upon admission, her doctor, **Dr. Smith**, performs various laboratory tests.

By the end of her stay, a diagnosis is concluded, and all diagnostic notes and laboratory results are stored in the hospital KG. During Alice's hospitalization, the following scenarios are envisaged:

- **Scenario 1 (S1).**  
  During her stay, Dr. Smith orders lab tests, and the results are stored in the hospital KG. Dr. Smith **must** share a treatment plan with Alice within **1 day** of the results becoming available.

- **Scenario 2 (S2).**  
  Once Dr. Smith shares the treatment plan with Alice, Alice **must** acknowledge receipt of the plan within **6 hours**. This acknowledgement is recorded in the hospital KG.

- **Scenario 3 (S3).**  
  As Alice’s expected discharge date approaches, she **must** review and electronically sign her discharge form before the scheduled end of her inpatient care. The electronically signed document is securely stored in the hospital’s KG.

- **Scenario 4 (S4).**  
  After Alice is discharged from CityCare Hospital, her doctor **must** sign and finalize her diagnostic report within **12 hours**. Alice's discharge marks the actual end of her inpatient care.

The different scenarios highlight various obligations governing data usage. Each obligation is regulated by temporal rules that determine the time frame in which these obligations must be fulfilled.

## Instantiation

Using the use case, we show how to represent the different scenarios using the Obligation Compliance Model. The use case is described using the **Human Inpatient Care (HIC)** ontology to express the different scenarios surrounding obligations.^[Throughout the paper, concepts from the HIC ontology are described using the prefix *hc*.]

### Scenario 1

Scenario 1 describes an obligation requiring Dr. Smith to share a treatment plan with Alice. In this setting, we extend the Obligation Core Model with the properties `ocm:sender` and `ocm:receiver`, modeled as subproperties of `ocm:entity`, to represent the entities involved in sending and receiving a resource as part of a sharing action. Consequently, both Alice and Dr. Smith are modeled as instances of the class `Entity`.

The specified duration of one day from the completion of the laboratory test implicitly defines the activation condition of the obligation as the event corresponding to the availability of the lab result. For simplicity, this event is represented by associating a lab result with the property `ocm:numericAtTime` within our event model.

This obligation is formalized as follows:

```turtle
ex:obligShareTreatmentPlan rdf:type ocm:Obligation ;
    ocm:obligationContent ex:tempShareTreatmentPlan ;
    ocm:activationCondition ex:labResult1 .

ex:tempShareTreatmentPlan rdf:type ocm:TemporalAction ;
    ocm:actionContent ex:actionShareTreatmentPlan ;
    ocm:numericDuration "86400000"^^xsd:long .

ex:actionShareTreatmentPlan rdf:type ocm:RegulatedAction ;
    ocm:sender ex:drSmith ;
    ocm:action ex:share ;
    ocm:resource ex:treatmentPlan11 ;
    ocm:receiver ex:alice .

ex:labResult1 ocm:numericAtTime "1767781800000"^^xsd:long .
```

The start time and deadline of this obligation are derived using SWRL rules and SWRL built-in predicates. In particular, the start time of the obligation is defined to coincide with the time at which `ex:labResult1` becomes available.

```text
ocm:Obligation(?obligShareTreatmentPlan)
∧ ocm:obligationContent(?obligShareTreatmentPlan, ?tempShareTreatmentPlan)
∧ ocm:TemporalAction(?tempShareTreatmentPlan)
∧ ocm:activationCondition(?obligShareTreatmentPlan, ?labResult)
∧ ocm:numericAtTime(?labResult, ?timeLabResult)
→ ocm:numericStartTime(?tempShareTreatmentPlan, ?timeLabResult)
```

The deadline is computed by adding one day to the start time of the obligation.

```text
ocm:Obligation(?obligShareTreatmentPlan)
∧ ocm:obligationContent(?obligShareTreatmentPlan, ?tempShareTreatmentPlan)
∧ ocm:TemporalAction(?tempShareTreatmentPlan)
∧ ocm:numericStartTime(?tempShareTreatmentPlan, ?st)
∧ ocm:numericDuration(?tempShareTreatmentPlan, ?dur)
∧ swrlb:add(?dead, ?st, ?dur)
→ ocm:numericDeadline(?tempShareTreatmentPlan, ?dead)
```

---

### Scenario 2

Scenario 2 describes an obligation requiring Alice to acknowledge receipt of the treatment plan within six hours of Dr. Smith sharing the plan with her. The event of sharing the treatment plan by Dr. Smith (described in Scenario 1) constitutes the activation condition of this obligation.

```turtle
ex:obligAcknowledgeReceipt a ocm:Obligation ;
    ocm:obligationContent ex:tempAcknowledgeReceipt ;
    ocm:activationCondition ex:actionShareTreatmentPlan .

ex:tempAcknowledgeReceipt a ocm:TemporalAction ;
    ocm:actionContent ex:actionAcknowledgeReceipt ;
    ocm:numericDuration "21600000"^^xsd:long .

ex:actionAcknowledgeReceipt a ocm:RegulatedAction ;
    ocm:entity ex:alice ;
    ocm:action ex:acknowledge ;
    ocm:resource ex:receiptPlan1 .
```

The start time of the obligation is defined as the time at which Dr. Smith shared the treatment plan with Alice. The deadline is derived by adding six hours to the start time.

Both temporal constraints are formalized using SWRL rules as follows:

```text
ocm:Obligation(?obligAcknowledgeReceipt)
∧ ocm:obligationContent(?obligAcknowledgeReceipt, ?tempAcknowledgeReceipt)
∧ ocm:TemporalAction(?tempAcknowledgeReceipt)
∧ ocm:activationCondition(?obligAcknowledgeReceipt, ?actionShareTreatmentPlan)
∧ ocm:numericAtTime(?actionShareTreatmentPlan, ?timeShareTreatmentPlan)
→ ocm:numericStartTime(?tempAcknowledgeReceipt, ?timeShareTreatmentPlan)

ocm:Obligation(?obligShareTreatmentPlan)
∧ ocm:obligationContent(?obligAcknowledgeReceipt, ?tempAcknowledgeReceipt)
∧ ocm:TemporalAction(?tempAcknowledgeReceipt)
∧ ocm:numericStartTime(?tempAcknowledgeReceipt, ?st)
∧ ocm:numericDuration(?tempAcknowledgeReceipt, ?dur)
∧ swrlb:add(?dead, ?st, ?dur)
→ ocm:numericDeadline(?tempAcknowledgeReceipt, ?dead)
```

---

### Scenario 3

Scenario 3 does not explicitly specify a start time for the obligation. Instead, the start time is implicitly defined as the obligation’s creation time. Consequently, the activation condition of this obligation is determined by its creation time.

```turtle
ex:obligSignDischargeForm a ocm:Obligation ;
    ocm:numericCreatedAtTime "1767781800000"^^xsd:long ;
    ocm:obligationContent ex:tempSignDischargeForm ;
    ocm:activationCondition ex:obligSignDischargeForm .

ex:tempSignDischargeForm a ocm:TemporalAction ;
    ocm:actionContent ex:actionSignDischargeForm .

ex:actionSignDischargeForm a ocm:RegulatedAction ;
    ocm:entity ex:alice ;
    ocm:action ex:sign ;
    ocm:resource ex:dischargeForm1 .
```

The start time of this obligation coincides with its creation time, while the deadline is defined by Alice’s expected admission end date, which should also be expressed in the ABox using the HIC ontology.

```text
ocm:Obligation(?obligSignDischargeForm)
∧ ocm:obligationContent(?obligSignDischargeForm, ?tempSignDischargeForm)
∧ ocm:TemporalAction(?tempSignDischargeForm)
∧ ocm:activationCondition(?obligSignDischargeForm, ?obligSignDischargeForm)
∧ ocm:numericAtTime(?obligSignDischargeForm, ?timeObligationCreation)
→ ocm:numericStartTime(?tempSignDischargeForm, ?timeObligationCreation)

ocm:Obligation(?obligSignDischargeForm)
∧ ocm:obligationContent(?obligSignDischargeForm, ?tempSignDischargeForm)
∧ ocm:TemporalAction(?tempSignDischargeForm)
∧ ocm:entity(?tempSignDischargeForm, ?patient)
∧ hc:Admission(?admission)
∧ hc:hasPatient(?admission, ?patient)
∧ hc:hasExpectedAdmissionEndDate(?admission, ?expectedAdmissionEndDate)
→ ocm:numericDeadline(?tempSignDischargeForm, ?expectedAdmissionEndDate)
```

---

### Scenario 4

This scenario can be modeled without an explicit activation condition. In such cases, the activation condition is considered always true and is represented by the instance `oem:triggeredEvent`, which is of type `oem:TriggeredEvent`.

For obligations involving additional parties, such as Alice being associated with a diagnosis report, the Obligation Core Model is extended with the property `ocm:party`, which links a specific resource to an entity.

```turtle
ex:obligSignReport a ocm:Obligation ;
    ocm:obligationContent ex:tempActionSignReport ;
    ocm:activationCondition ex:triggeredEvent .

ex:tempActionSignReport a ocm:TemporalAction ;
    ocm:actionContent ex:actionSignReport ;
    ocm:numericDuration "43200000"^^xsd:long .

ex:actionSignReport a ocm:RegulatedAction ;
    ocm:entity ex:drSmith ;
    ocm:action ex:sign ;
    ocm:resource ex:diagnosisReport1 .
```

The start time of this obligation coincides with the admission end date of Alice's hospital stay, while the deadline is computed by adding twelve hours to the start time.

```text
ocm:Obligation(?obligSignReport)
∧ ocm:obligationContent(?obligSignReport, ?tempActionSignReport)
∧ ocm:TemporalAction(?tempActionSignReport)
∧ ocm:resource(?tempActionSignReport, ?diagnosisReport)
∧ ocm:party(?diagnosisReport, ?patient)
∧ ocm:activationCondition(?obligSignReport, ?admission)
∧ ocm:numericAtTime(?admission, ?timeAdmissionEndDate)
∧ hc:hasPatient(?admission, ?patient)
→ ocm:numericStartTime(?tempActionSignReport, ?timeAdmissionEndDate)

ocm:Obligation(?obligSignReport)
∧ ocm:obligationContent(?obligSignReport, ?tempActionSignReport)
∧ ocm:TemporalAction(?tempActionSignReport)
∧ ocm:numericStartTime(?tempActionSignReport, ?startTime)
∧ ocm:numericDuration(?tempActionSignReport, ?duration)
∧ swrlb:add(?deadline, ?startTime, ?duration)
→ ocm:numericDeadline(?tempActionSignReport, ?deadline)
```
