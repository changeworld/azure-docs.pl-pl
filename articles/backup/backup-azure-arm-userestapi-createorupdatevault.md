---
title: Tworzenie magazynów usług odzyskiwania przy użyciu interfejsu API REST
description: W tym artykule dowiesz się, jak zarządzać operacjami tworzenia kopii zapasowych i przywracania kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu interfejsu API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173412"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Tworzenie magazynu usług odzyskiwania platformy Azure przy użyciu interfejsu API REST

Kroki tworzenia usługi Azure Recovery Services Vault przy użyciu interfejsu API REST są opisane w dokumentacji [interfejsu API programu Create REST magazynu.](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) Użyjmy tego dokumentu jako odwołania do utworzenia magazynu o nazwie "testVault" w "Zachodnie stany USA".

Aby utworzyć lub zaktualizować magazyn usług Azure Recovery Services, należy użyć następującej operacji *PUT.*

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Tworzenie żądania

Aby utworzyć żądanie *PUT,* `{subscription-id}` wymagany jest parametr. Jeśli masz wiele subskrypcji, zobacz [Praca z wieloma subskrypcjami](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Definiujesz `{resourceGroupName}` i `{vaultName}` dla zasobów, wraz `api-version` z parametrem. W tym `api-version=2016-06-01`artykule użyto pliku .

Wymagane są następujące nagłówki:

| Nagłówek żądania   | Opis |
|------------------|-----------------|
| *Content-Type:*  | Wymagany. Ustaw wartość `application/json`. |
| *Authorization:* | Wymagany. Ustaw na prawidłowy  [token dostępu](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)`Bearer`. |

Aby uzyskać więcej informacji na temat tworzenia żądania, zobacz [Składniki żądania/odpowiedzi interfejsu API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Tworzenie treści żądania

Następujące typowe definicje są używane do tworzenia treści żądania:

|Nazwa  |Wymagany  |Typ  |Opis  |
|---------|---------|---------|---------|
|Etag     |         |   Ciąg      |  Opcjonalny eTag       |
|location     |  true       |Ciąg         |   Lokalizacja zasobu      |
|properties     |         | [Właściwości VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Właściwości przechowalni       |
|sku     |         |  [Numer jednostki magazynowej](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identyfikuje unikatowy identyfikator systemu dla każdego zasobu platformy Azure     |
|tags     |         | Obiekt        |     Tagi zasobów    |

Należy zauważyć, że nazwa przechowalni i nazwa grupy zasobów są podane w identyfikatorze URI PUT. Treść żądania definiuje lokalizację.

## <a name="example-request-body"></a>Przykładowa treść żądania

Poniższa przykładowa treść jest używana do tworzenia przechowalni w "Zachodnie stany USA". Określ lokalizację. Jednostka SKU jest zawsze "Standardowa".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Odpowiedzi

Istnieją dwie pomyślne odpowiedzi dla operacji, aby utworzyć lub zaktualizować magazyn usług odzyskiwania:

|Nazwa  |Typ  |Opis  |
|---------|---------|---------|
|200 ok.     |   [Magazyn](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Utworzono     | [Magazyn](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Utworzone      |

Aby uzyskać więcej informacji na temat odpowiedzi interfejsu API REST, zobacz [Przetwarzanie komunikatu odpowiedzi](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Przykładowa odpowiedź

Skondensowana *odpowiedź 201 Utworzone* z poprzedniej treści żądania przykładu pokazuje *identyfikator* został przypisany i *inicjowania obsługi administracyjnejState* jest *pomyślnie:*

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

## <a name="next-steps"></a>Następne kroki

[Utwórz zasady tworzenia kopii zapasowych do tworzenia kopii zapasowych maszyny Wirtualnej platformy Azure w tym magazynie](backup-azure-arm-userestapi-createorupdatepolicy.md).

Aby uzyskać więcej informacji na temat interfejsów API usługi Azure REST, zobacz następujące dokumenty:

- [Interfejs API REST dostawcy usług odzyskiwania platformy Azure](/rest/api/recoveryservices/)
- [Get started with Azure REST API (Rozpoczęcie pracy z interfejsem API REST platformy Azure)](/rest/api/azure/)
