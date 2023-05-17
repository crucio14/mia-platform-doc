---
id: overview
title: Therapy and Monitoring Manager
sidebar_label: Overview
---
The therapy and monitoring manager (TMM) is a service that enables health care professionals to manage patients **therapies** and **monitor** patients health conditions, **adherence** and **compliance** to therapy. The service allows you to define **thresholds** and send notifications to alert the involved entities, typically physician and patient, of events which occur during the therapy and monitoring period.

In addition to therapies and monitorings, there are also **detections**. Detections are entered by the patient to whom the therapy and monitoring are directed, and represent the steps taken by the patient to meet the plan.

The adherence and compliance metrics will be calculated based on the detections entered by the patient. These metrics will trigger the sending of notifications, as set by the physician who created the therapy or monitoring.

:::note
Sending notifications based on adherence and compliance metrics is a feature currently under development and planned to be released in the future.
:::

For such detections, you can define an archetype, called **prototype**, to act as a validator. For example, you could define a prototype to validate detections related to body temperature, verifying that the detection value is a number between 34 and 42 °C.

In the documentation, when you encounter any of the terms listed below, you should assume they have the described meaning, unless stated otherwise.

| Term                 | Meaning      |
|----------------------|--------------|
| *Adherence*          | A therapy or monitoring metric indicating if the patient is performing the tasks according to the timeframe defined by the physician. |
| *Compliance*         | A therapy or monitoring metric indicating if the patient is performing the correct tasks as prescribed by the physician. |
| *Doctor*             | See *Physician*. |
| *Monitoring*         | A physician prescription to monitor health conditions (blood pressure, body temperature, vital signs, etc.). |
| *Notification*       | A message sent via email, SMS, etc. to one or more users regarding events occurring during the therapy or monitoring period. |
| *Detection*          | A patient feedback on a therapy (e.g. I took the drug at 9) or monitoring (e.g. the minimum and maximum blood pressure measured). |
| *Patient*            | A person undergoing therapy or monitoring. |
| *Physician*          | A doctor prescribing therapies or monitorings to a patient. |
| *Plan*               | A therapy or monitoring plan prescribed by a physician. |
| *Prototype*          | An archetype providing generic (applicable to all patients) constraints to validate detection values, for example to ensure a medical device is reporting correctly, or therapy directives. |
| *Schedule*           | A schedule the patient need to follow when submitting detections for a therapy or monitoring. |
| *Therapy*            | A physician prescription for some therapies (taking drugs, etc.). |
| *Threshold*          | Monitoring thresholds specific for a patient therapy or monitoring, for example his/her expected blood pressure. |
| *TMM*                | Common abbreviation for the Therapy and Monitoring Manager. |
| *Validation service* | The integrated or external service used to validate detections against monitoring thresholds. |

## Adherence and compliance

The Therapy and Monitoring Manager models the concepts of adherence and compliance. These two metrics define how well the patient is adhering to the guidelines given by the physician for a monitoring or therapy.

Let us define the two metrics. Formally:

* A patient is considered **adherent** to a therapy or monitoring if that patient performs the assigned tasks within the timeframe defined by the physician, net of tolerances that may be defined.
* A patient is considered **compliant** to a therapy or monitoring if that patient performs the tasks exactly as described in the description of the plan by the physician (e.g. takes the correct drug in the correct amount).

:::note
Please note that the **compliance** with a therapy or monitoring is not influenced by the timeframe.
:::

In order to better clarify the difference between the two metrics, let's make a couple of examples:

1. Let us suppose that a physician prescribes the following therapy:

    ```txt
    The patient must takes the pill X with dosage Y twice a day.
    ```

    where the adherence tolerance frequency is 1, minimum adherence percentage is 90% and minimum compliance percentage is 80%.  

    The patient is considered:
    * **adherent** to therapy if, for at least 90% of the days, that patient complies with the frequency of detections, thus twice a day, plus or minus the given tolerance, which in this example means taking the pill at least once and at most three times a day;
    * **compliant** to therapy if, for at least 80% of the days, that patient complied exactly what the physician defined in the directives, so take exactly the pill X with dosage Y.

2. Let us suppose that a physician prescribes the following therapy:

    ```txt
    The patient must takes the pill X at 10am and 2pm.
    ```

    where the adherence tolerance time (in hours) is 1, minimum adherence percentage is 90% and minium compliance percentage is 80%.

    The patient is considered:
    * **adherent** to therapy if, for at least 90% of the days, the patient complies with the timeframes of detections, thus at 10am and 2pm, also considering tolerance, which in this example is 1 hour, so it is allowed to take pill between 9am and 11am and between 1pm and 3pm;
    * **compliant** to therapy if, for at least 80% of the days, the patient does exactly what the physician defined in the directives, so take exactly the pill X with dosage Y.

The **adherence** and **compliance** metrics are not necessary linked. Being **compliant** does not mean being **adherent**. This is true for the reverse as well.

Indeed, a patient can be **adherent but not compliant**. Looking at the above example, this is the case if the patient takes drug twice per day but not the correct drug.

A patient can also be **compliant but not adherent**. Considering the above example, this is the case if the patient takes the correct drug but not at the right hours, for instance if he takes it at 5pm and 9pm.

## Therapies

The service allows you to create, update and delete therapies for patients, such as administering a medication for a specific time frame, by defining:

* the name of the plan;
* the directives of the doctor drafting the therapy;
* the reference to the prototype containing the validation rules for the directives entered by the doctor;
* the start date of the therapy;
* the end date of the therapy (this value is not required if the therapy has an indefinite time frame);
* the frequency or the timeframes of therapy (this value is not required if the therapy does not include a periodic performance of the actions described by the physician);
* the reference to the physician who created the therapy;
* the reference to the patient to whom the therapy is addressed;
* the tolerance on the frequency or time of intake for which a particular detection entered by the patient is still considered adherent to the therapy;
* the minimum percentage value for which a patient is considered adherent to therapy with respect to reported detections;
* the minimum percentage value for which a patient is considered compliant to therapy with respect to reported detections;
* if adherence and/or compliance metrics should be calculated.

## Monitorings

The service allows you to create, update and delete monitorings for patients, such as monitoring blood pressure for a specific period of time, by defining:

* the name of the plan;
* the notes of the physician writing the monitoring;
* the reference to the prototype containing the validation rules for the detections entered by the patient;
* the start date of the monitoring;
* the end date of the monitoring (this value is not required if the monitoring has an indefinite duration);
* the frequency of monitoring operations (this value is not required if the monitoring does not include periodic performance of the actions described by the physician);
* the reference of the physician who created the monitoring;
* the reference of the patient to whom the monitoring is addressed;
* the tolerance on the frequency or time of intake for which a particular detection entered by the patient is still considered adherent or compliant to the monitoring;
* the minimum percentage value for which a patient is considered adherent to monitoring with respect to reported detections;
* the minimum percentage value for which a patient is considered compliant to monitoring with respect to reported detections;
* the detections thresholds;
* if adherence and/or compliance metrics should be calculated.

## Detections

The service allows tracking actions performed by the patient concerning a specific therapy or monitoring, such as reporting body temperature or taking certain medication at specific time. The detection is defined by:

* the reference to the therapy or monitoring to which the detection is linked;
* the value of the detection (this value is an arbitrary object; for example, a detection for the blood pressure could have two fields, the minimum and a maximum value measured);
* the compliance of the detection with respect to what prescribed by the physician;
* the date of the detection; 

:::note

Please note that the date the detection was entered may be different from the date the detection was conducted.

:::

* the reference to the referring physician for the therapy or monitoring;
* the reference to the patient to whom the therapy or monitoring relates.

## Prototypes

The prototypes define the specifications of acceptable values related to monitoring detections or therapy directives.

These prototypes are nothing but JSON Schema that are applied to monitoring detections or therapy directives to validate the respective values. These prototypes are defined at the microservice configuration level.

The prototypes should be used to validate the doctor directives or patient detections and ensure they are reliable, in particular to prevent human errors when submitting values read from a medical device.

:::tip

If your medical device can report measurements between a well-defined range, you should use the range maximum and minimum to validate the detection submitted by the user.

:::

:::danger

Prototypes should not be used to monitor the expected vital signs of patients subject to monitorings, but thresholds should be used instead. Check the [*Thresholds* section](10_overview.md#thresholds) for more details.

:::

For each prototype you need to define:

- the unique identifier;
- the type (can be `measurement` or `therapy`);
- the name;
- the schema;
- the labels for the schema fields (**required** only if you use the TMM with the companion FE component);
- the hints for the schema fields (only for therapy directives, should provide a list of admitted or suggested values for a schema field).

TMM supports localization for `name`, `labels` and `hints`, using a syntax like the following (`en` is ISO 639-1 language code for English, `it` for Italian):

```json
{
  "name": {
    "en": "English name",
    "it": "Italian name"
  },
  "labels": {
    "bodyTemperature": {
      "en": "Body Temperature",
      "it": "Temperatura corporea",
    },
    "diet": {
      "en": "Diet",
      "it": "Dieta"
    }
  },
  "hints": {
    "diet": [
      {
        "en": "Low calorie 1200 KCAL (BEE ≤ 1300KCAL)",
        "it": "Ipocalorica 1200 KCAL (BEE ≤ 1300KCAL)"
      },
      {
        "en": "Low calorie 1400 KCAL (BEE [1400-1600] KCAL)",
        "it": "Ipocalorica 1400 KCAL (BEE [1400-1600] KCAL)"
      },
      {
        "en": "Low calorie 1600 KCAL (BEE [1600-1800] KCAL)",
        "it": "Ipocalorica 1600 KCAL (BEE [1600-1800] KCAL)"
      },
      {
        "en": "Low calorie 1800 KCAL (BEE [1800-2000] KCAL)",
        "it": "Ipocalorica 1800 KCAL (BEE [1800-2000] KCAL)"
      }
    ]
  }
}
```
:::caution

If you plan to use the TMM in combination with the companion FE component, be aware that, if you replace the string with a translation object, you must provide a translation for all languages that you want to support.

:::

Let's see a couple of examples:

- a prototype to ensure all relevant directives for a therapy are provided by the doctor when the therapy is created;

```json
{
  "identifier": "drugPrescription",
  "type": "therapy",
  "name": "Drug prescription",
  "schema": {
    "type": "object",
    "properties": {
      "drugName": {
        "type": "string"
      },
      "drugDosage": {
        "type": "string"
      }
    },
    "required": [
      "drugName",
      "drugDosage"
    ]
  },
  "labels": {
    "drugName": {
      "en": "Drug name",
      "it": "Nome del farmaco"
    },
    "drugDosage": {
      "en": "Drug dosage",
      "it": "Dosaggio farmaco"
    }
  },
  "hints": {
    "drugName": [{
      "en": "Amoxicillin",
      "it": "Amoxicillina"
    },
    {
      "en": "Levofloxacin",
      "it": "Levofloxacina"
    },
    {
      "en": "Moxifloxacin",
      "it": "Moxifloxacina"
    }],
    "drugDosage": [{
      "en": "One per day",
      "it": "Una volta al giorno"
    },
    {
      "en": "Two per day",
      "it": "Due volte al giorno"
    },
    {
      "en": "Three per day",
      "it": "Tre volte al giorno"
    }]
  }
}
```

- a prototype to ensure the body temperature reported by the patient is between 34 and 42: in this example we expect the detection value to be an object with a field `bodyTemperature`;

```json
{
  "identifier": "bodyTemperature",
  "type": "measurement",
  "name": "Body Temperature",
  "schema": {
    "type": "object",
    "properties": {
      "bodyTemperature": {
        "type": "number",
        "minimum": 34,
        "maximum": 42
      }
    },
    "required": [
      "bodyTemperature"
    ]
  },
  "labels": {
    "bodyTemperature": {
      "en": "Body Temperature",
      "it": "Temperatura corporea",
    }
  }
}
```

- a prototype to ensure the reported blood pressure has a minimum between 60 and 150 and a maximum between 80 and 250: in this example we expect the detection value to be an object with two fields, `minimumBloodPressure` and `maximumBloodPressure`;

```json
{
  "identifier": "bloodPressure",
  "type": "measurement",
  "name": {
    "en": "Blood Pressure",
    "it": "Pressione Sanguigna"
  },
  "schema": {
    "type": "object",
    "properties": {
      "minimumBloodPressure": {
        "type": "integer",
        "minimum": 60,
        "maximum": 150
      },
      "maximumBloodPressure": {
        "type": "integer",
        "minimum": 80,
        "maximum": 250
      }
    },
    "required": [
      "minimumBloodPressure",
      "maximumBloodPressure"
    ]
  },
  "labels": {
    "minimumBloodPressure": "Minimum pressure",
    "maximumBloodPressure": "Maximum pressure"
  }
}
```

## Thresholds

When a new detection is created or the value of an existing detection is updated, the TMM checks if the detection value exceeds any of the monitoring thresholds and optionally sends a notification to the physician (specified in the `doctorId` field of the monitoring plan) if one or more thresholds are exceeded.

The validation process should check all the monitoring thresholds and return, for each one of them, if it was exceeded or not by the given detection.

We currently support two operating modes:

- the thresholds validation is performed directly by TMM according to the rules described below (**default**);
- the thresholds validation is performed by an external service, which must satisfy certain API requirements.

The integrated validation system currently supports the following threshold validation rules:

- *maximum value* (`gt`, `gte`): allows you to specify a maximum value for a detection property, if the actual detection property has a value strictly greater (when using the `gt` operator) or greater or equal (when using the `gte` operator), the threshold is considered exceeded;

```json
{
  "propertyName": "maximumBloodPressure",
  "thresholdOperator": "gt",
  "thresholdValue": 120
}
```

- *minimum value* (`lt`, `lte`): allows you to specify a minimum value for a detection property, if the actual detection property has a value strictly lower (when using the `lt` operator) or lower or equal (when using the `lte` operator), the threshold is considered exceeded;

```json
{
  "propertyName": "minimumBloodPressure",
  "thresholdOperator": "lt",
  "thresholdValue": 60
}
```

- *exact value* (`eq`): allows you to specify the exact value for a detection property, if the actual detection property has a different value, the threshold is considered exceeded.

```json
{
  "propertyName": "minimumBloodPressure",
  "thresholdOperator": "eq",
  "thresholdValue": 80
}
```

- *numeric range* (`between`, `notBetween`): allows you to specify a minimum and maximum value, and if the actual detection property has a value inside (when using the `between` operator) or outside (when using the `notBetween` operator) that range, the threshold is considered exceeded;

```json
{
  "propertyName": "minimumBloodPressure",
  "thresholdOperator": "between",
  "thresholdValue": [40, 70]
}
```

```json
{
  "propertyName": "minimumBloodPressure",
  "thresholdOperator": "notBetween",
  "thresholdValue": [50, 100]
}
```

:::danger

The integrated validation system is designed under the assumption that the property of the detection value referred by a threshold contains one or two numeric values. If the value is not a number or an array of two numbers, depending on the operator, an error is returned.

:::

Additional information about the API requirements and the setup of the external validation service are available in the [*Configuration* section](20_configuration.md#thresholds-validation).

## Notifications

The TMM allows you to send notifications on various channels to patients and physicians when certain events occur during the lifetime of a monitoring or therapy plan.

For example, when a patient submits a detection and its value exceeds one or more monitoring thresholds, the TMM can send a notification to the physician.

More details about the configuration of the notification service are available in the [*Configuration* section](20_configuration.md#notifications).

:::tip

If you are using an external validation system and want that system to be responsible for sending notifications, you can simply implement your custom logic inside it and disable notifications on the TMM side.

:::