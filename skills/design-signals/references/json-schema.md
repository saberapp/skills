# Portable JSON Schema

Apply this contract when `AnswerType` is `json_schema` or when reviewing a customer schema. Saber sends the schema across current Gemini and Azure model paths, so use the shared subset below.

## Shape

- Use an object at the root.
- Keep at most 100 properties in total and at most five object levels.
- Use `string`, `number`, `integer`, `boolean`, `array`, `object`, and `null`.
- Give each node one type, with optional `null`, for example `"type": ["string", "null"]`.
- Give every array one schema object in `items`.
- Define object fields in `properties`.
- Set `additionalProperties` to `false` on every object. Saber also adds it when absent.
- Put every property in `required`. Make a value nullable when it can be unavailable.

Use these keywords when needed:

- `description`, `enum`, `const`
- `minimum`, `maximum`, `exclusiveMinimum`, `exclusiveMaximum`, `multipleOf`
- `minLength`, `maxLength`, `pattern`, `format`
- `minItems`, `maxItems`, `uniqueItems`
- `title`, `examples`, `default`

Do not use composition, references, conditionals, tuple schemas, or dynamic object keys. This excludes `anyOf`, `oneOf`, `allOf`, `not`, `$ref`, `$defs`, `definitions`, `if`, `then`, `else`, `prefixItems`, `patternProperties`, and `unevaluatedProperties`.

## Semantics

- Describe what each field means, including its unit, scope, time period, or null condition when relevant.
- Use an enum only for a stable closed set. Add `other` when a valid value can fall outside the main categories. Add `unknown` or use `null` when evidence can be unavailable.
- Put supporting facts before classifications, summaries, or other derived fields. Saber records `properties` order automatically. Do not add ordering metadata.
- Keep one coherent answer in one schema. Split unrelated facts into separate definitions.
- Keep scoring rules outside the schema so they can change without changing the research contract.

## Example

Question:

> Which CRM does this company use?

```json
{
  "type": "object",
  "additionalProperties": false,
  "properties": {
    "crmName": {
      "type": ["string", "null"],
      "description": "Identified CRM product name. Use null only when no CRM can be identified."
    },
    "crmCategory": {
      "type": "string",
      "enum": ["salesforce", "hubspot", "other", "unknown"],
      "description": "Normalized CRM category. Use other for an identified CRM outside the named categories and unknown when no CRM can be identified."
    }
  },
  "required": ["crmName", "crmCategory"]
}
```

The fact `crmName` comes before the derived `crmCategory`. Consumers must still read fields by name.
