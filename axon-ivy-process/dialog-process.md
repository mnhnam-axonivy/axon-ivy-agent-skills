# Dialog Process Elements

Element types used in HTML Dialog process files (`src_hd/**/*Process.p.json`).

## HtmlDialogStart (Dialog Entry)

```json
{
  "id": "f0",
  "type": "HtmlDialogStart",
  "name": "start(String)",
  "config": {
    "signature": "start",
    "input": {
      "params": [
        { "name": "callbackUrl", "type": "String" }
      ],
      "map": {
        "out.callbackUrl": "param.callbackUrl"
      },
      "code": "out.dataModel.search();"
    },
    "result": {
      "params": [
        { "name": "project", "type": "package.model.Project", "desc": "" }
      ],
      "map": {
        "result.project": "in.project"
      }
    },
    "guid": "17501D6F0923F1F3"
  },
  "visual": { "at": { "x": 96, "y": 64 } },
  "connect": [{ "id": "f10", "to": "f1" }]
}
```

- `input.params` — parameter definitions
- `input.map` — maps `param.*` to process data
- `input.code` — initialization script
- `result.params` — return value definitions (name, type, desc)
- `result.map` — maps process data `in.*` back to `result.*` for the calling process
- `guid` — unique identifier linking to the dialog component

**CRITICAL — Result section**: Every `HtmlDialogStart` that returns data to a calling process MUST have a `result` section. Without it, the calling process cannot resolve `result.fieldName` in its output mapping, causing `Output 'out.field': Field not found` errors.

## HtmlDialogEnd (Dialog Exit)

```json
{
  "id": "f5",
  "type": "HtmlDialogEnd",
  "name": "",
  "visual": { "at": { "x": 700, "y": 64 } }
}
```

## HtmlDialogMethodStart (Dialog Method)

Entry point for a method callable from the dialog UI (e.g., button actions, data loading).

```json
{
  "id": "f3",
  "type": "HtmlDialogMethodStart",
  "name": "redirect()",
  "config": {
    "signature": "redirect",
    "guid": "15C67E8753E2C68C"
  },
  "visual": { "at": { "x": 96, "y": 200 } },
  "connect": [{ "id": "f12", "to": "f4" }]
}
```

## HtmlDialogEventStart (Dialog Event)

Entry point triggered by a UI event (e.g., row selection, value change).

```json
{
  "id": "f6",
  "type": "HtmlDialogEventStart",
  "name": "onRowSelect()",
  "config": {
    "signature": "onRowSelect",
    "guid": "1A2B3C4D5E6F7890"
  },
  "visual": { "at": { "x": 96, "y": 336 } },
  "connect": [{ "id": "f14", "to": "f7" }]
}
```

## HtmlDialogExit (Event/Method Exit)

Exit point for method and event flows (not the main dialog exit).

```json
{
  "id": "f4",
  "type": "HtmlDialogExit",
  "name": "",
  "visual": { "at": { "x": 400, "y": 200 } }
}
```

## Dialog Process Structure

A dialog process file lives in `src_hd/` and typically contains multiple flows:

```json
{
  "$schema": "https://json-schema.axonivy.com/process/13.2.0/process.json",
  "id": "UNIQUE_HEX_ID",
  "config": {
    "data": "package.name.DialogData"
  },
  "elements": [
    { "type": "HtmlDialogStart", "name": "start()", ... },
    { "type": "HtmlDialogEnd", ... },
    { "type": "HtmlDialogMethodStart", "name": "save()", ... },
    { "type": "HtmlDialogExit", ... },
    { "type": "HtmlDialogEventStart", "name": "onSelect()", ... },
    { "type": "HtmlDialogExit", ... }
  ]
}
```
