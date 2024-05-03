---
id: usage
title: Usage
sidebar_label: Usage
---

<!--
WARNING: this file was automatically generated by Mia-Platform Doc Aggregator.
DO NOT MODIFY IT BY HAND.
Instead, modify the source file and run the aggregator to regenerate this file.
-->

The Therapy and Monitoring Manager exposes different APIs for the management of the Therapy, Monitoring and Detection entities.

## Therapy & Monitoring

The therapy and monitoring data models share the majority of the properties. The common part of the data model is composed by the following properties:

| Name | Required | Default | Description |  |
|---|---|---|---|---|
| planName | Yes | - | Name of the therapy or monitoring. |  |
| prototypeId | Yes | - | Identifier of the prototype to use in the validation of the detection values. |  |
| startDate | Yes | - | Start date of the therapy or monitoring plan. |  |
| endDate | No | - | End date of the therapy or monitoring plan. |  |
| doctorId | Yes | - | The reference to the doctor that created the therapy or monitoring. |  |
| patientId | Yes | - | The reference to the patient the therapy or monitoring refers to. |  |
| adherenceStatus | No | `DEFAULT_ADHERENCE_STATUS` | If the adherence is computed (`enabled`) or not (`disabled`), as long as the therapy or monitoring specifies a schedule. |  |
| each | If `adherenceStatus` is `enabled` | - | Frequency expressed in terms of days of week. The array must contain only the value `day` or any combination of days of the week (`monday`, `tuesday`, `wednesday`, `thursday`, `friday`, `saturday`, `sunday`). If this field is set, one between `times` and `each` must be set, but not both. |  |
| times | If `adherenceStatus` is `enabled` and `hours` is not set | - | Frequency expressed in number of repetitions per day. This field is mutually exclusive with `hours` and can only be set if `each` is set. |  |
| hours | If `adherenceStatus` is `enabled` and `times` is not set | - | Hours at which the patient need to carry out the action prescribed by the physician. This field is mutually exclusive with `times` and can only be set if `each` is set. |  |
| adherenceToleranceTime | If `adherenceStatus` is `enabled` and `hours` is set | `DEFAULT_ADHERENCE_TOLERANCE_TIME` | Time tolerance in terms of hours that can be accepted between the timing prescribed by the physician and the actual timing performed by the patient. If the action performed by the patient exceeds the tolerance, the detection will be marked as non-adherent. This field can only be set when the `hours` field is set. |  |
| adherenceToleranceFrequency | If `adherenceStatus` is `enabled` and `times` is set | `DEFAULT_ADHERENCE_TOLERANCE_FREQUENCY` | Frequency tolerance that can be accepted between the frequency prescribed by the physician and the frequency with which the patient actually performed the actions. This field can only be set when the `times` field is set. |  |
| adherenceMinimumPercentage | If `adherenceStatus` is `enabled` | `DEFAULT_ADHERENCE_MINIMUM_PERCENTAGE` | Minimum ratio expressed in percentage between the number of days when the patient is adherent and the total number of the expected days with detections. For instance, a minimum adherence percentage of 90% means that the patient is considered adherent to the plan if it has completed the assigned tasks on time at least on 9 days out of 10, according to the therapy or monitoring schedule |  |
| complianceStatus | No | `DEFAULT_COMPLIANCE_STATUS` | If the compliance is computed (`enabled`) or not (`disabled`). |  |
| complianceMinimumPercentage | If `complianceStatus` is `enabled` | `DEFAULT_COMPLIANCE_MINIMUM_PERCENTAGE` | Minimum ratio expressed in percentage between the number of days when the patient is compliant and the total number of days with detections. A patient is compliant on a given day if all detections for that day are compliant. For example, a minimum compliance percentage of 90% means that the patient is considered compliant if and only if it has completed correctly all assignments on at least 9 days out of 10, according to the compliance definition. |  |
| isPatientAdherent | No |  | If the patient is adherent to the therapy or monitoring, taking into consideration the tolerance settings. This field is accessible in read-only mode from the API. |  |
| isPatientAdherentLastUpdatedAt | No |  | Date/time of the last update to the `isPatientAdherent` field. This field is accessible in read-only mode from the API. |  |
| isPatientCompliant | No |  | If the patient is compliant to the therapy or monitoring, taking into consideration the tolerance settings. This field is accessible in read-only mode from the API. |  |
| isPatientCompliantLastUpdatedAt | No |  | Date/time of the last update of the `isPatientCompliant` field. This field is accessible in read-only mode from the API. |  |

:::note
The `times` and `hours` fields are overlapping and only one of them needs to be specified. The `times` property must be set when an operation is to be performed a certain number of times within the same day but without specifying the hour of the day. The `hours` property, instead, must be set when an operation has to be performed in determined hours of the day. If none is set, the adherence metric is not computed.
:::

:::note
 The `adherenceToleranceTime` is considered only when `hours` field is defined.
:::

:::note
The `adherenceToleranceFrequency` is considered only when `times` field is defined. 
:::

:::info
For further information on the concepts of adherence and compliance, please refer to the appropriate section on the [Overview page][adherence-compliance]
:::

The therapy data model add the concept of `directives`, which is an object following the schema defined in the prototype associated to the therapy.

The monitoring data model add the concept of `notes` and `thresholds`. The notes field contains the physician prescriptions, while a threshold is an object with the following properties:

* `propertyName`: name of the property on which the threshold is evaluated;
* `thresholdOperator`: operator to use in the threshold evaluation. Available options are: `gt`, `lt`, `gte`, `lte`, `eq`, `between`, `notBetween`;
* `thresholdValue`: the value with which to evaluate the threshold (a single number for `gt`, `lt`, `gte`, `lte` and `eq` operators, an array of two numbers indicating a range for the `between` and `notBetween` operators).

### `GET /<therapies|monitorings>/`

Retrieve all the therapies or monitorings matching a given query. These endpoints are a proxy to the CRUD `GET /` endpoint.

These endpoints accept the following CRUD-like query parameters:

- pagination: `_sk` and `_l`;
- sorting: `_s`;
- filtering: `_q`, `_st_` or any CRUD field name, such as `planName`, `startDate`, `endDate`, `doctorId` and `patientId`.

These endpoints return 200 and an array with the therapies or monitorings matching the query.

### `GET /<therapies|monitorings>/count`

Return the number of therapies or monitorings matching a given query. These endpoints are a proxy to the CRUD `GET /count` endpoint.

These endpoints accept the CRUD-like query parameters to filter the records: `_q`, `_st_` or any CRUD field name, such as `planName`, `startDate`, `endDate`, `doctorId` and `patientId`.

These endpoints return 200 and the number of therapies or monitorings matching the query.

### `POST /<therapies|monitorings>/`

Insert a new therapy or monitoring in the CRUD. These endpoints are a proxy to the CRUD `POST /` endpoint.

The body of the request must contain a JSON object representing a valid therapy or monitoring, for example:

- a therapy to administer drugs every day at 10am for 15 consecutive days starting on June 1st, 2022:

```json
{
  "planName": "Drug therapy",
  "prototypeId": "medication",
  "directives": {
    "drugName": "Aspirin 500mg",
    "drugDosage": "500mg/day"
  },
  "startDate": "2022-06-01",
  "endDate": "2022-06-15",
  "doctorId": "auth0|doctorId",
  "patientId": "auth0|patientId",
  "each": ["day"],
  "hours": ["10"],
  "adherenceStatus": "enabled",
  "adherenceToleranceTime": 1,
  "adherenceMinimumPercentage": 90,
  "complianceStatus": "enabled",
  "complianceMinimumPercentage": 90,
}
```

- a monitoring plan to measure blood pressure twice a day for 15 consecutive days starting on June 1st, 2022:

```json
{
  "planName": "Blood pressure monitoring",
  "prototypeId": "bloodPressure",
  "notes": "Takes the blood pressure twice a day",
  "startDate": "2022-06-01",
  "endDate": "2022-06-15",
  "doctorId": "auth0|doctorId",
  "patientId": "auth0|patientId",
  "each": ["day"],
  "times": 2,
  "adherenceStatus": "enabled",
  "adherenceToleranceFrequency": 1,
  "adherenceMinimumPercentage": 90,
  "complianceStatus": "enabled",
  "complianceMinimumPercentage": 90,
  "thresholds": [
    {
      "propertyName": "minimumBloodPressure",
      "thresholdOperator": "between",
      "thresholdValue": [60, 100]
    },
    {
      "propertyName": "maximumBloodPressure",
      "thresholdOperator": "between",
      "thresholdValue": [100, 140]
    }
  ],
}
```

These endpoints return 200 and an object with an `_id` field containing the ID of the created CRUD record.

These endpoints return 400 and a body with the structure shown below if any of the following conditions is met:

- the therapy or monitoring is not valid;
- the client tries to set read-only fields;
- the current number of active therapies or monitorings associated to the given prototype and patient is equal to the `MAX_PATIENT_ACTIVE_PLANS` env var (if set).

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "<monitoring|therapy> is not valid",
  "requestId": "<Request ID>",
  "resource": {},
  "validationErrors": [
    "Validation error on a field",
    "Plan exceeded limit on patient active plans"
  ],
}
```

The following fields will be initialized with default values if the value is required ([see table][therapy-monitoring]) but no value is provided:

- `adherenceStatus`
- `adherenceToleranceFrequency`
- `adherenceToleranceTime`
- `adherenceMinimumPercentage`
- `complianceStatus`
- `complianceMinimumPercentage`

The following table summarizes which environment variable contains the default value for the corresponding therapy or monitoring field.

| Field                       | Environment variable                  |
|-----------------------------|---------------------------------------|
| adherenceStatus             | DEFAULT_ADHERENCE_STATUS              |
| adherenceToleranceFrequency | DEFAULT_ADHERENCE_TOLERANCE_FREQUENCY |
| adherenceToleranceTime      | DEFAULT_ADHERENCE_TOLERANCE_TIME      |
| adherenceMinimumPercentage  | DEFAULT_ADHERENCE_MINIMUM_PERCENTAGE  |
| complianceStatus            | DEFAULT_COMPLIANCE_STATUS             |
| complianceMinimumPercentage | DEFAULT_COMPLIANCE_MINIMUM_PERCENTAGE |

If the **NOTIFICATION_MANAGER_URL** environment variable is correctly set, the handler sends a request to the `POST /notification-events/` endpoint of the Notification Manager, including the following information in the request body:

- **key** (string, required)*: the identifier for the newly created therapy or monitoring.
- **name** (string, required)*: the event name, which is `TMM/TherapyCreated/v1` for therapies and `TMM/MonitoringCreated/v1` for monitorings.
- **payload** (object, required)*: the request body.

Here are two examples of events related to a new therapy and monitoring created respectively.

```json
{
  "key": "f81f1bad-6e6f-49c0-b3d6-1494cfe2853f",
  "name": "TMM/TherapyCreated/v1",
  "payload": {
    "planName": "Drug therapy",
    "prototypeId": "medication",
    "directives": {
      "drugName": "Aspirin 500mg",
      "drugDosage": "500mg/day"
    },
    "startDate": "2022-06-01",
    "endDate": "2022-06-15",
    "doctorId": "auth0|doctorId",
    "patientId": "auth0|patientId",
    "each": ["day"],
    "hours": ["10"],
    "adherenceStatus": "enabled",
    "adherenceToleranceTime": 1,
    "adherenceMinimumPercentage": 90,
    "complianceStatus": "enabled",
    "complianceMinimumPercentage": 90,
  }
}
```

```json
{
  "key": "dd486151-9b53-4fd4-b7d6-74c5c6dfbba5",
  "name": "TMM/MonitoringCreated/v1",
  "payload": {
    "planName": "Blood pressure monitoring",
    "prototypeId": "bloodPressure",
    "notes": "Takes the blood pressure twice a day",
    "startDate": "2022-06-01",
    "endDate": "2022-06-15",
    "doctorId": "auth0|doctorId",
    "patientId": "auth0|patientId",
    "each": ["day"],
    "times": 2,
    "adherenceStatus": "enabled",
    "adherenceToleranceFrequency": 1,
    "adherenceMinimumPercentage": 90,
    "complianceStatus": "enabled",
    "complianceMinimumPercentage": 90,
    "thresholds": [
      {
        "propertyName": "minimumBloodPressure",
        "thresholdOperator": "between",
        "thresholdValue": [60, 100]
      },
      {
        "propertyName": "maximumBloodPressure",
        "thresholdOperator": "between",
        "thresholdValue": [100, 140]
      }
    ],
  }
}
```

### `PATCH /<therapies|monitorings>/:id`

Update an existing therapy or monitoring plan identified by the `:id` path parameter. These endpoints are a proxy to the CRUD `PATCH /:id` endpoint.

If the therapy or monitoring does not have a schedule, and the fields `each` and one between `times` or `hours` is set, the remaining fields of the schedule are added with defaults values (see the table in the previous section).

These endpoints return 400 and a body with the structure shown below if one of following errors occur:

- the client tries to update fields that affect the therapy or monitoring plan, and consequently the adherence and compliance metrics, and one or more detections have already been submitted by the patient:

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "Patched <monitoring|therapy> is not valid",
  "requestId": "<Request ID>",
  "resource": {},
  "validationErrors": [
    "Patching field <field> after detections have been submitted is not permitted. Please create a new plan instead."
  ],
}
```

- the client tries to update one or more read-only fields:

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "Patched <monitoring|therapy> is not valid",
  "requestId": "<Request ID>",
  "resource": {},
  "validationErrors": [
    "'<field>' is a read-only property"
  ],
}
```

- the therapy or monitoring plan resulting from the patch is not valid (for example, if after the update both the `times` and `hours` fields would be set):

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "Patched <monitoring|therapy> is not valid",
  "requestId": "<Request ID>",
  "resource": {
    "planName": "Drug therapy",
    "prototypeId": "medication",
    "directives": {
      "drugName": "Aspiring 500mg",
      "drugDosage": "500mg/day"
    },
    "startDate": "2022-06-01",
    "endDate": "2022-06-15",
    "doctorId": "auth0|doctorId",
    "patientId": "auth0|patientId",
    "each": ["day"],
    "hours": ["10"],
    "adherenceStatus": "enabled",
    "adherenceToleranceTime": 1,
    "adherenceMinimumPercentage": 90,
    "complianceStatus": "enabled",
    "complianceMinimumPercentage": 90,
    "isPatientAdherent": true,
    "isPatientCompliant": true
  },
  "validationErrors": [
    "'times' and 'hours' are mutually exclusive fields, found both"
  ],
}
```

- the number of active therapies or monitorings associated to the given prototype and patient is equal to the `MAX_PATIENT_ACTIVE_PLANS` env var (if set):

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "Patched <monitoring|therapy> is not valid",
  "requestId": "<Request ID>",
  "resource": {},
  "validationErrors": [
    "Plan exceeded limit on patient active plans"
  ]
}
```

If the **NOTIFICATION_MANAGER_URL** environment variable is correctly set, the handler will send a request to the `POST /notification-events/` endpoint of the Notification Manager, including the following information in the request body:
- **key**(*string*): the identifier for the updated therapy or monitoring.
- **name**(*string*):  the event name, which is `TMM/TherapyUpdated/v1` for therapies and `TMM/MonitoringUpdated/v1` for monitorings.
- **payload**(*object*):
  - <**originalTherapy**||**originalMonitoring**>(*object*): The original therapy  or monitoring (before the update).
  - <**currentTherapy**||**currentMonitoring**>(*object*): The updated therapy or monitoring (after the update).

### `DELETE /<therapies|monitorings>/:id`

Delete an existing therapy or monitoring plan identified by the `:id` path parameter. These endpoints are a proxy to the CRUD `DELETE /:id` endpoint.

These endpoints return 404 if no therapy or monitoring with given id is found.

## Detections

Detections represent tasks performed by the patient and are related to therapies or monitorings. The data model is the following:

| Name                   | Required (Yes/No)   | Description                                                             |
|------------------------|---------------------|-------------------------------------------------------------------------|
| planType               | Yes                 | Type of plan - `therapy` or `monitoring` - the detection refers to.   |
| planId                 | Yes                 | Identifier of the therapy or monitoring plan the detection refers to. |
| value                  | Only for monitoring | The detection value is an arbitrary object and must match the validation schema defined by the associated prototype. Note that the association between the prototype and the detection is not available in the detection data model, but it is defined at the therapy or monitoring level. |
| planId                 | Yes                 | Identifier of the therapy or monitoring plan the detection refers to. |
| observedAt             | Yes                 | Date/time at which the detection has been performed. |
| isCompliant | Yes                 | It indicates if the detection is compliant to what the physician has descripted in the plan. This value is submitted by the patient. |
| doctorId               | No                  | Identifier of the doctor that creates the detection. Note that the doctor that creates the detection can be different from the physician that created the monitoring or therapy plan. |
| patientId              | Yes                 | Identifier of the patient that creates the detection. |

If the **NOTIFICATION_MANAGER_URL** environment variable is correctly set, the handler will send a request to the `POST /notification-events/` endpoint of the Notification Manager, including the following information in the request body:
- **key** (string): the identifier of the deleted therapy or monitoring.
- **name** (string): the event name, which is `TMM/TherapyDeleted/v1` for therapies and `TMM/MonitoringDeleted/v1` for monitorings.
- **payload**(*object*): the deleted therap or monitoring.

### `GET /detections/`

Retrieve all the detections matching a given query. This endpoint is a proxy to the CRUD `GET /` endpoint.

This endpoint accepts the following CRUD-like query parameters:

- pagination: `_sk` and `_l`;
- sorting: `_s`;
- filtering: `_q`, `_st_` or any CRUD field name, such as `planType` and `planId`.

This endpoint returns 200 and an array with the detections matching the query.

### `GET /detections/count`

Return the number of detections matching a given query. This endpoint is a proxy to the CRUD `GET /count` endpoint.

This endpoint accepts the CRUD-like query parameters to filter the records: `_q`, `_st_` or any CRUD field name, such as `planType`, `planId`, `doctorId` and `patientId`.

These endpoints return 200 and the number of detections matching the query.

### `POST /detections/`

Insert a new detection in the CRUD and performs the following operations:

- if the [`VALIDATION_SERVICE` environment variable][environment-variables] is set, check if the detection exceeds any of the monitoring thresholds;
- if the [`NOTIFICATION_MANAGER_URL` environment variable][environment-variables] is set, send a notification to the physician through the Notification Manager service.

This endpoint is a proxy to the CRUD `POST /` endpoint.

The body of the request must contain a JSON object representing a valid detection, like:

```json
{
  "planType": "monitoring",
  "planId": "abc123",
  "isCompliant": true,
  "value": {
    "minimumBloodPressure": 97,
    "maximumBloodPressure": 134
  },
  "observedAt": "2022-06-01T10:00:00.000Z",
  "doctorId": "auth0|doctorId",
  "patientId": "auth0|patientId"
}
```

This endpoint returns 200 and an object with an `_id` field containing the ID of the created CRUD record.

This endpoint returns 400 and a body with the structure shown below if the detection is not valid:

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "Detection is not valid",
  "requestId": "<Request ID>",
  "resource": {
    "planType": "monitoring",
    "planId": "abc123",
    "isCompliant": true,
    "value": {
      "minimumBloodPressure": 97,
      "maximumBloodPressure": 134
    },
    "observedAt": "2022-06-01T10:00:00.000Z",
    "doctorId": "auth0|doctorId",
    "patientId": "auth0|patientId"
  },
  "validationErrors": [
    "The detection value is required for monitoring plans.",
    "The 'observedAt' string does not represent a valid date/time.",
    "The 'observedAt' date/time cannot be later than now."
  ],
}
```

### `PATCH /detections/:id`

Update an existing detection identified by the `:id` path parameter and performs the following operations:

- if the [`VALIDATION_SERVICE` environment variable][environment-variables] is set, check if the detection exceeds any of the monitoring thresholds;
- if the [`NOTIFICATION_MANAGER_URL` environment variable][environment-variables] is set, send a notification to the physician through the Notification Manager service.

This endpoint is a proxy to the CRUD `PATCH /:id` endpoint.

This endpoint returns 404 if one of following errors occur:

- the detection with the given id does not exist (see CRUD response format);
- the monitoring or therapy plan associated to the detection does not exist (see CRUD response format);
- the prototype associated to the therapy or monitoring plan does not exist.

```json
{
  "statusCode": 404,
  "error": "Prototype Not Found",
  "message": "Prototype not found",
  "requestId": "<Request ID>",
  "prototypeId": "<Prototype identifier>",
}
```

This endpoint returns 400 and a body with the structure shown below if one of following errors occur:

- the detection resulting from the patch is not valid (for example if the `observedAt` field is not a valid ISO 8601 date-time string):

```json
{
  "statusCode": 400,
  "error": "Invalid CRUD Resource",
  "message": "Patched detection is not valid",
  "requestId": "<Request ID>",
  "resource": {
    "planType": "monitoring",
    "planId": "abc123",
    "isCompliant": true,
    "value": {
      "minimumBloodPressure": 97,
      "maximumBloodPressure": 134
    },
    "observedAt": "2022-02-31T10:00:00.000Z",
    "doctorId": "auth0|doctorId",
    "patientId": "auth0|patientId"
  },
  "validationErrors": [
    "The 'observedAt' string does not represent a valid date/time."
  ],
}
```

- the detection value, after patching, does not match the prototype:

```json
{
  "statusCode": 400,
  "error": "Detection Not Valid",
  "message": "Detection value does not match prototype schema",
  "requestId": "<Request ID>",
  "detection": {
    "planType": "monitoring",
    "planId": "abc123",
    "isCompliant": true,
    "value": {
      "minimumBloodPressure": 97,
      "maximumBloodPressure": 50
    },
    "observedAt": "2022-02-31T10:00:00.000Z",
    "doctorId": "auth0|doctorId",
    "patientId": "auth0|patientId"
  },
  "prototype": {
    "identifier": "bloodPressure",
    "type": "measurement",
    "name": "Blood Pressure Prototype",
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
    }
  },
}
```

### `DELETE /detections/:id`

Delete an existing detection identified by the `:id` path parameter. This endpoint is a proxy to the CRUD `DELETE /:id` endpoint.

This endpoint returns 404 if no detection with given id is found.

## Prototypes

The service uses prototypes to validate therapy and monitoring detections. For futher info about the prototypes and their data model and definition, it is recommended to consult the [overview][overview] page and the [configuration][configuration] page, respectively.

### `GET /prototypes/`

Return all the prototypes matching a given query.

This endpoint supports the following query parameters:

- pagination: `_sk` and `_l`;
- filtering with detection properties: `identifier`, `type` and `name`.

This endpoint returns 200 and a list of the prototypes matching the query.

- A response with a prototype example for therapies:
```json
[{
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
}]
```

- A response with a prototype example for monitorings:

```json
[{
    "identifier": "bloodPressure",
    "type": "measurement",
    "name": "Blood Pressure Prototype",
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
      "required": ["minimumBloodPressure", "maximumBloodPressure"]
    },
    "labels": {
      "minimumBloodPressure": {
        "en": "minimum pressure",
        "it": "pressione minima"
      },
      "maximumBloodPressure": {
        "en": "maximum pressure",
        "it": "pressione massima"
      }
    }
  }]
```

### `GET /prototypes/count`

Return all the prototypes matching a given query.

This endpoint supports the following query parameters:

- filtering with detection properties: `identifier`, `type` and `name`.

This endpoint returns 200 and the number of prototypes matching the query. 

## Adherence and compliance

:::info

In this section, a day represents the period of time between 00:00:00.000 and 23:59:59.999 in the detections time zone (see [`DETECTIONS_TIME_ZONE` environment variable][environment-variables]).

:::

This service includes a background job running on a given schedule to update adherence and compliance metrics for all *active plans*.

The schedule can be configured by setting the [`CRON_SCHEDULE` environment variable][environment-variables] using a valid [CRON expression][crontab-guru]. By default, the job runs once a day at midnight according to the time zone set in the [`DETECTIONS_TIME_ZONE` environment variable][environment-variables].

On each execution, the background job computes, for each active plan, the adherence and compliance metrics on all existing detections and update the following monitoring or therapy fields:

- `isPatientAdherent`: if the patient is adherent to the plan;
- `isPatientCompliant`: if the patient is compliant to the plan;
- `isPatientAdherentLastUpdatedAt`, `isPatientCompliantLastUpdatedAt`: the date/time of the update. 

### Active plans

A therapy or monitoring plan is considered active if and only if:

- the start date is earlier than the current date/time;
- the end date is not set or is greater or equal than the current date/time minus the grace period (see [`DETECTIONS_GRACE_PERIOD`][environment-variables]).

For example, if the cron job runs every day at midnight and the grace period is 30 days, we add 30 days to the end date to account for detections submitted during the grace period. We also add one more day to ensure that we correctly update the adherence and compliance metrics for the last day, whenever it ends between two consecutive executions of the background job.

### Adherence computation

The adherence of a patient to a plan is computed only for active plans with adherence enabled, i.e. having the `adherenceStatus` field set to `enabled`.

The computation is performed for each plan according to the following algorithm, using all submitted detections.

1. Compute the expected number of days with detections, from the start date of the plan until the last day.
2. Compute, for each day with detections, if the patient is adherent in that specific day.
3. Compute the ratio between the number of days when the patient is adherent and the total number of expected days with detections (from step 1).
4. Compute if the ratio from the previous step, expressed as percentage and rounded to the closest integer, is greater or equal than the `adherenceMinimumPercentage` threshold set for the plan.
5. If the condition at the previous step is satisfied, the patient is reported as adherent.

To determine if a patient is adherent in a specific day (step 2 above) we follow this algorithm.

1. Check if each detection, taken individually, is adherent, meaning that all the following conditions are satisfied:
  - the detection was observed after the plan start date;
  - the detection was observed before the plan end date, if set;
  - the detection was observed before the current date/time (not in the future);
  - the detection was observed on a week day matching the plan (see `each` field);
  - when `hours` field is set, the detection matches at least an hour, within the tolerance.
2. If `times` is set, check that the number of detections is adherent with the plan frequency (`times`) and tolerance (`adherenceToleranceFrequency`).
3. If `hours` is set, check that the detections are adherent to the hours, including tolerance, which means there is exactly one detection for each hour (the earliest detection matching the earliest hour).
4. If and only if all the previous conditions are satisfied the patient is reported to be adherent to the plan in the given day.

### Compliance computation

The compliance of a patient to a plan is computed only for active plans with compliance enabled, i.e. having the `complianceStatus` field set to `enabled`.

The computation is performed for each plan according to the following algorithm, using all submitted detections.

1. Compute, for each day with detections, if the patient is compliant in that specific day, meaning if all detections are compliant (`isCompliant` is `true`).
2. Compute the ratio between the number of days when the patient is compliant and the total number of days with detections.
3. Compute if the ratio from the previous step, expressed as percentage and rounded to the closest integer, is greater or equal than the `complianceMinimumPercentage` threshold set for the plan.
4. If the condition at the previous step is satisfied, the patient is reported as compliant.


[crontab-guru]: https://crontab.guru/ "Crontab.guru"

[overview]: ./10_overview.md "Overview page"
[adherence-compliance]: ./10_overview.md#adherence-and-compliance "Adherence and compliance | Overview"

[configuration]: ./20_configuration.md "Configuration page"
[environment-variables]: ./20_configuration.md#environment-variables "Environment variables | Configuration"

[therapy-monitoring]: #therapy--monitoring "Therapy & Monitoring | Usage"