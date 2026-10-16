# AI Services

Single-service Azure AI (Cognitive Services) accounts deployed separately from the Foundry multi-service account, when they need their own networking, identity, region or lifecycle. All use the AVM module `Azure/avm-res-cognitiveservices-account/azurerm` with a different `kind`, a private endpoint using the platform `privatelink.cognitiveservices.azure.com` zone, and local auth disabled.
