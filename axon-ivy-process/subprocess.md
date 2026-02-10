# Subprocess Elements

## SubProcessCall (Call a Subprocess)

```json
{
  "id": "f3",
  "type": "SubProcessCall",
  "name": "Upload Document Checker",
  "config": {
    "processCall": "Functional Processes/UploadDocumentItemChecker:call(IvyDocument)",
    "call": {
      "map": {
        "param.uploadFile": "in.uploadedFile"
      }
    },
    "output": {
      "map": {
        "out.message": "result.message"
      }
    }
  },
  "visual": { "at": { "x": 400, "y": 64 } },
  "connect": [{ "id": "f10", "to": "f4" }]
}
```

- `processCall` — path to subprocess signature: `"Folder/ProcessName:methodName(ParamTypes)"`
- `call.map` — maps current data to subprocess `param.*`
- `output.map` — maps subprocess `result.*` back to current data

**CRITICAL — Path separator rule**: `processCall` uses `/` (slash) separators for process paths, NOT `.` (dot).

```text
WRONG: "hr.onboarding.agent.ExtractEmployeeData:extractEmployeeData(String)"
RIGHT: "hr/onboarding/agent/ExtractEmployeeData:extractEmployeeData(String)"
```

Note: `DialogCall.dialog` uses `.` (dot) package separators — do NOT confuse the two conventions.

## CallSubStart (Callable Subprocess Entry)

Used in processes with `"kind": "SUB"`. Defines the entry point that other processes call.

```json
{
  "id": "f0",
  "type": "CallSubStart",
  "name": "call(ICase,String)",
  "config": {
    "signature": "call",
    "input": {
      "params": [
        { "name": "businessCase", "type": "ch.ivyteam.ivy.workflow.ICase", "desc": "" },
        { "name": "documentName", "type": "String", "desc": "" }
      ],
      "map": {
        "out.businessCase": "param.businessCase",
        "out.documentName": "param.documentName"
      }
    },
    "result": {
      "params": [
        { "name": "message", "type": "String", "desc": "" }
      ],
      "map": {
        "result.message": "in.message"
      }
    }
  },
  "visual": { "at": { "x": 96, "y": 64 } },
  "connect": [{ "id": "f10", "to": "f1" }]
}
```

- `input.params` — parameter definitions (name, type, desc)
- `input.map` — maps `param.*` to process data `out.*`
- `result.params` — return value definitions
- `result.map` — maps process data `in.*` back to `result.*`

## CallSubEnd (Callable Subprocess Exit)

```json
{
  "id": "f5",
  "type": "CallSubEnd",
  "name": "",
  "visual": { "at": { "x": 700, "y": 64 } }
}
```

## SUB Process Structure

A callable subprocess uses `"kind": "SUB"`:

```json
{
  "$schema": "https://json-schema.axonivy.com/process/13.2.0/process.json",
  "id": "UNIQUE_HEX_ID",
  "kind": "SUB",
  "config": {
    "data": "package.name.DataClass"
  },
  "elements": [
    { "type": "CallSubStart", ... },
    { "type": "Script", ... },
    { "type": "CallSubEnd", ... }
  ]
}
```
