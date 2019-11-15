---
title: Schemat zdarzeń Azure Event Grid dla Azure Key Vault
description: Opisuje właściwości i schemat udostępnione dla zdarzeń Azure Key Vault z Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082865"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Schemat zdarzeń Azure Event Grid dla Azure Key Vault (wersja zapoznawcza)

Ten artykuł zawiera właściwości i schemat zdarzeń w [Azure Key Vault](../key-vault/index.yml), obecnie w wersji zapoznawczej. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Konto Azure Key Vault generuje następujące typy zdarzeń:

| Pełna nazwa zdarzenia | Nazwa wyświetlana zdarzenia | Opis |
| ---------- | ----------- |---|
| Microsoft. CertificateNewVersionCreated — magazyn. | Utworzono nową wersję certyfikatu | Wyzwalane, gdy zostanie utworzony nowy certyfikat lub Nowa wersja certyfikatu. |
| Microsoft. CertificateNearExpiry — magazyn. | Certyfikat niedługo wygaśnie | Wyzwalane, gdy bieżąca wersja certyfikatu wkrótce wygaśnie. (Wartość domyślna to 30 dni przed datą wygaśnięcia). |
| Microsoft. CertificateExpired — magazyn. | Certyfikat wygasł | Wyzwalane po wygaśnięciu certyfikatu. |
| Microsoft. KeyNewVersionCreated — magazyn. | Utworzono nową wersję klucza | Wyzwalane, gdy zostanie utworzony nowy klucz lub Nowa wersja klucza. |
| Microsoft. KeyNearExpiry — magazyn. | Klucz bliski wygaśnięcia | Wyzwalane, gdy bieżąca wersja klucza wkrótce wygaśnie. (Wartość domyślna to 30 dni przed datą wygaśnięcia). |
| Magazyn kluczy Microsoft. kluczy. wygasł | Klucz wygasł | Wyzwalane po wygaśnięciu klucza. |
| Microsoft. SecretNewVersionCreated — magazyn. | Utworzono wpis tajny nowej wersji | Wyzwalane, gdy zostanie utworzona nowa wartość tajna lub Nowa wersja wpisu tajnego. |
| Microsoft. SecretNearExpiry — magazyn. | Wpis tajny blisko wygaśnięcia | Wyzwalane, gdy bieżąca wersja wpisu tajnego wkrótce wygaśnie. (Wartość domyślna to 30 dni przed datą wygaśnięcia). |
| Microsoft. SecretExpired — magazyn. | Klucz tajny wygasł | Wyzwalane po wygaśnięciu wpisu tajnego. |

## <a name="event-examples"></a>Przykłady zdarzeń

Poniższy przykład przedstawia schemat dla elementu **Microsoft. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| ---------- | ----------- |---|
| id | ciąg | Identyfikator obiektu, który wyzwolił to zdarzenie |
| vaultName | ciąg | Nazwa magazynu kluczy obiektu, który wyzwolił to zdarzenie |
| objectType | ciąg | Typ obiektu, który wyzwolił to zdarzenie. |
| Obiektu | ciąg | Nazwa obiektu, który wyzwolił to zdarzenie |
| version | ciąg | Wersja obiektu, który wyzwolił to zdarzenie |
| nbf | number | Data nie wcześniejsza (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |
| exp | number | Data wygaśnięcia (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby dowiedzieć się więcej na temat integracji Key Vault z usługą Event Grid, zobacz [monitorowanie Key Vault z Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-overview.md).
* Aby zapoznać się z samouczkiem dotyczącym Key Vault integracji z Event Grid, zobacz [Odbieranie i reagowanie na powiadomienia magazynu kluczy za pomocą Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-tutorial.md).
* Aby uzyskać dodatkowe wskazówki dotyczące Key Vault i Azure Automation, zobacz:
    - [Co to jest Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Key Vault monitorowania z Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-overview.md)
    - [Odbieraj powiadomienia dotyczące magazynu kluczy i odpowiadaj na nie Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-tutorial.md)
    - [Omówienie usługi Azure Automation](../automation/index.yml)
