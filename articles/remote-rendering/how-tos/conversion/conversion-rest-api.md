---
title: Interfejs API REST konwersji zasobów
description: W tym artykule opisano sposób konwertowania zasobu za pośrednictwem interfejsu API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 064a7b8adaec6f47a153743b58f4e1caf50db723
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681508"
---
# <a name="use-the-model-conversion-rest-api"></a>Korzystanie z interfejsu API REST konwersji modelu

Usługa [konwersji modelu](model-conversion.md) jest kontrolowana za pomocą [interfejsu API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). W tym artykule opisano szczegóły interfejsu API usługi konwersji.

## <a name="regions"></a>Regiony

Zobacz [listę dostępnych regionów](../../reference/regions.md) dla podstawowych adresów URL, do które mają być wysyłane żądania.

## <a name="common-headers"></a>Typowe nagłówki

### <a name="common-request-headers"></a>Typowe nagłówki żądań

Te nagłówki muszą być określone dla wszystkich żądań:

- Nagłówek **Autoryzacja** musi mieć wartość "Nośnik [*TOKEN*]", gdzie [*TOKEN*] jest [tokenem dostępu do usługi.](../tokens.md)

### <a name="common-response-headers"></a>Typowe nagłówki odpowiedzi

Wszystkie odpowiedzi zawierają następujące nagłówki:

- Nagłówek **MS-CV** zawiera unikatowy ciąg, który może służyć do śledzenia wywołania w usłudze.

## <a name="endpoints"></a>Punkty końcowe

Usługa konwersji udostępnia trzy punkty końcowe interfejsu API REST do:

- rozpocznij konwersję modelu przy użyciu konta magazynu połączonego z kontem zdalnego renderowania platformy Azure. 
- rozpoczęcie konwersji modelu przy użyciu *dostarczonych sygnatur dostępu współdzielonego (SAS)*.
- zapytanie o stan konwersji

### <a name="start-conversion-using-a-linked-storage-account"></a>Rozpoczynanie konwersji przy użyciu połączonego konta magazynu
Konto zdalnego renderowania platformy Azure musi mieć dostęp do podanego konta magazynu, wykonując kroki dotyczące [łączenia kont magazynu](../create-an-account.md#link-storage-accounts).

| Endpoint | Metoda |
|-----------|:-----------|
| /v1/accounts/accountID /conversion/create**accountID** | POST |

Zwraca identyfikator trwającej konwersji, zawinięty w dokumencie JSON. Nazwa pola to "conversionId".

#### <a name="request-body"></a>Treść żądania


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Rozpoczynanie konwersji przy użyciu dostarczonych podpisów dostępu współdzielonego
Jeśli konto ARR nie jest połączone z kontem magazynu, ten interfejs REST umożliwia zapewnienie dostępu przy użyciu *sygnatur dostępu współdzielonego (SAS).*

| Endpoint | Metoda |
|-----------|:-----------|
| /v1/accounts/ accountID /conversion/createWithSharedAccessSignature /v1/accounts/**accountID**/conversion/createWithSharedAccessSignature /v1/accountID /conversion/createWithSharedAccessSignature /v1/ | POST |

Zwraca identyfikator trwającej konwersji, zawinięty w dokumencie JSON. Nazwa pola to "conversionId".

#### <a name="request-body"></a>Treść żądania

Treść żądania jest taka sama jak w wywołaniu REST powyżej, ale dane wejściowe i wyjściowe zawierają *tokeny sygnatur dostępu współdzielonego (SAS).* Tokeny te zapewniają dostęp do konta magazynu do odczytu danych wejściowych i zapisywania wyniku konwersji.

> [!NOTE]
> Te tokeny URI sygnatury dostępu Współdzielonego są ciągami zapytań, a nie pełnym identyfikatorem URI. 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Stan konwersji ankiety
Stan trwającej konwersji rozpoczętej z jednym z powyższych wywołań REST można zbadać za pomocą następującego interfejsu:


| Endpoint | Metoda |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/conversionId**conversionId** | GET |

Zwraca dokument JSON z polem "stan", które może mieć następujące wartości:

- "Bieganie"
- "Sukces"
- "Awaria"

Jeśli stan to "Błąd", pojawi się dodatkowe pole "błąd" z podpolem "wiadomość" zawierającym informacje o błędzie. Dodatkowe dzienniki zostaną przekazane do kontenera wyjściowego.

## <a name="next-steps"></a>Następne kroki

- [Używanie usługi Azure Blob Storage do konwersji modelu](blob-storage.md)
- [Konwersja modelu](model-conversion.md)
