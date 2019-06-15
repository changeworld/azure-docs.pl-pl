---
title: 'Usługa Azure Backup: Magazyny usługi Recovery Services za pomocą interfejsu API REST'
description: Zarządzanie kopiami zapasowymi i przywracanie operacji przy użyciu interfejsu API REST usługi Azure VM Backup
services: backup
author: pvrk
manager: shivamg
keywords: INTERFEJS API REST; Kopia zapasowa maszyny Wirtualnej platformy Azure; Przywracanie maszyny Wirtualnej platformy Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646711"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Tworzenie usługi Azure magazyn usługi Recovery Services za pomocą interfejsu API REST

Kroki umożliwiające utworzenie Azure magazyn usługi Recovery Services za pomocą interfejsu API REST są opisane w [Utwórz magazyn usługi interfejsu API REST](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentacji. Daj nam należy używać tego dokumentu jako odwołanie, aby utworzyć magazyn o nazwie "testVault" w "Zachodnie stany USA".

Aby utworzyć lub zaktualizować magazyn usługi Azure Recovery Services, należy użyć następującego *umieścić* operacji.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Utwórz żądanie

Aby utworzyć *umieścić* żądania `{subscription-id}` parametr jest wymagany. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Należy zdefiniować `{resourceGroupName}` i `{vaultName}` dla zasobów, wraz z `api-version` parametru. W tym artykule wykorzystano `api-version=2016-06-01`.

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Content-Type:*  | Wymagany. Ustaw `application/json`. |
| *Authorization:* | Wymagany. Ustawić prawidłową `Bearer` [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Aby uzyskać więcej informacji o tym, jak utworzyć żądanie, zobacz [składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do tworzenia treści żądania:

|Name (Nazwa)  |Wymagane  |Typ  |Opis  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  Opcjonalny element eTag.       |
|location     |  true       |String         |   Lokalizacja zasobu      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Właściwości magazynu       |
|sku     |         |  [Jednostka SKU](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Określa unikatowy identyfikator systemowy dla poszczególnych zasobów platformy Azure     |
|tags     |         | Object        |     Tagi zasobów    |

Należy zauważyć, że magazyn nazwy i nazwy grupy zasobów znajdują się w identyfikatorze URI PUT. Treść żądania określa lokalizację.

## <a name="example-request-body"></a>Przykład treść żądania

Treść poniższy przykład umożliwia utworzenie magazynu w "Zachodnie stany USA". Określ lokalizację. Jednostka SKU jest zawsze "Standardowy".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Responses

Istnieją dwa pomyślne odpowiedzi dla operacji, które można utworzyć lub zaktualizować magazynu usługi Recovery Services:

|Name (Nazwa)  |Typ  |Opis  |
|---------|---------|---------|
|200 OK     |   [Magazyn](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 utworzono     | [Magazyn](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Utworzono      |

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [przetworzyć komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Przykładowa odpowiedź

Skrócone *201 utworzono* odpowiedzi z poprzedniego żądania przykład podstawowy pokazuje *identyfikator* przypisano i *provisioningState* jest *zakończone powodzeniem* :

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie kopii zapasowej zasady tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure, w tym magazynie](backup-azure-arm-userestapi-createorupdatepolicy.md).

Aby uzyskać więcej informacji na temat interfejsów API REST platformy Azure zobacz następujące dokumenty:

- [Interfejs API REST platformy Azure w zakresie dostawcy usługi Recovery Services](/rest/api/recoveryservices/)
- [Wprowadzenie do interfejsu API REST platformy Azure](/rest/api/azure/)
