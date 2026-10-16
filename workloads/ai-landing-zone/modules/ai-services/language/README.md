# AI Language

Standalone Azure AI Language resource (`Microsoft.CognitiveServices/accounts`, `kind = "TextAnalytics"`): sentiment analysis, named entity recognition, PII detection, conversational language understanding (CLU), question answering and summarization.

- Custom subdomain required (needed for Entra ID auth and private endpoints)
- Public network access disabled; private endpoint to the platform `privatelink.cognitiveservices.azure.com` zone
- Local auth (keys) disabled; access via managed identity / Entra ID RBAC (`Cognitive Services Language Reader` / `Owner`)
- Custom features (CLU, custom NER, question answering) require a linked Storage account; question answering also requires AI Search
- Diagnostic settings to the platform Log Analytics workspace

## Files

| File | Purpose |
|---|---|
| `main.tf` | Language account, private endpoint + DNS zone group, diagnostics, optional Storage/Search integration |
| `modules/storage-access/` | Storage Blob Data Contributor for the Language identity + Language Studio CORS on an existing Storage account |
| `../../../params/language.dev.tfvars` | Dev variable values |
