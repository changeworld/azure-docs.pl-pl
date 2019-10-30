---
title: Schemat zdarzenia Azure Key Vault Azure Event Grid
description: Opisuje właściwości i schemat udostępnione dla zdarzeń Azure Key Vault z Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033526"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Schemat zdarzeń Azure Event Grid dla Azure Key Vault (wersja zapoznawcza)

Ten artykuł zawiera właściwości i schemat zdarzeń [Azure Key Vault](../key-vault/index.yml) , obecnie w wersji zapoznawczej. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Konto Azure Key Vault emituje następujące typy zdarzeń:

| Pełna nazwa zdarzenia | Nazwa wyświetlana zdarzenia | description |
| ---------- | ----------- |---|
| Microsoft. CertificateNewVersionCreated — magazyn. | Utworzono nową wersję certyfikatu | Wyzwalane, gdy zostanie utworzony nowy certyfikat lub Nowa wersja certyfikatu |
| Microsoft. CertificateNearExpiry — magazyn. | Certyfikat niedługo wygaśnie | Wyzwalane, gdy bieżąca wersja certyfikatu wkrótce wygaśnie (wartość domyślna to 30 dni przed datą wygaśnięcia) |
| Microsoft. CertificateExpired — magazyn. | Certyfikat wygasł | Wyzwalane po wygaśnięciu certyfikatu |
| Microsoft. KeyNewVersionCreated — magazyn. | Utworzono nową wersję klucza | Wyzwalane, gdy zostanie utworzony nowy klucz lub Nowa wersja klucza |
| Microsoft. KeyNearExpiry — magazyn. | Klucz bliski wygaśnięcia | Wyzwalane, gdy bieżąca wersja klucza wkrótce wygaśnie (wartość domyślna to 30 dni przed datą wygaśnięcia) |
| Magazyn kluczy Microsoft. kluczy. wygasł | Klucz wygasł | Wyzwalane po wygaśnięciu klucza |
| Microsoft. SecretNewVersionCreated — magazyn. | Utworzono wpis tajny nowej wersji | Wyzwalane po utworzeniu nowej tajnej lub nowej wersji klucza tajnego |
| Microsoft. SecretNearExpiry — magazyn. | Wpis tajny blisko wygaśnięcia | Wyzwalane, gdy bieżąca wersja wpisu tajnego wygaśnie (domyślnie 30 dni przed datą wygaśnięcia) |
| Microsoft. SecretExpired — magazyn. | Klucz tajny wygasł | Wyzwalane po wygaśnięciu wpisu tajnego |

## <a name="event-examples"></a>Przykłady zdarzeń

W poniższym przykładzie przedstawiono schemat dla elementu **Microsoft. SecretNewVersionCreated**.

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
| id | string | Identyfikator obiektu, który wyzwolił to zdarzenie. |
| vaultName | string | Nazwa magazynu kluczy obiektu, który wyzwolił to zdarzenie. |
| objectType | string | Typ obiektu, który wyzwolił to zdarzenie. |
| Obiektu | string | Nazwa obiektu, który wyzwolił to zdarzenie |
| version | string | Wersja obiektu, który wyzwolił to zdarzenie |
| NBF | numer | Nie wcześniejsza niż data w sekundach od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |
| EXP | numer | Data wygaśnięcia (w sekundach) od 1970 r-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
* Aby dowiedzieć się więcej na temat integracji Key Vault/Event Grid, zobacz [monitorowanie Key Vault z Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-overview.md)
* Aby zapoznać się z samouczkiem dotyczącym integracji Key Vault/Event Grid, zobacz [How to: Route Key Vault Events to Automation Runbook (wersja zapoznawcza)](../key-vault/event-grid-tutorial.md).

- [Przegląd Azure Key Vault](../key-vault/key-vault-overview.md)
- [Przegląd Azure Event Grid](overview.md)
- [Key Vault monitorowania z Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-overview.md)
- [Instrukcje: kierowanie zdarzeń Key Vault do elementu Runbook usługi Automation (wersja zapoznawcza)](../key-vault/event-grid-tutorial.md).
- [Omówienie usługi Azure Automation](../automation/index.yml)
