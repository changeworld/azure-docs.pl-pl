---
title: Uzyskiwanie tokenów dostępu do usługi
description: W tym artykule opisano sposób tworzenia tokenów dostępu do interfejsów API REST ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681196"
---
# <a name="get-service-access-tokens"></a>Uzyskiwanie tokenów dostępu do usługi

Dostęp do interfejsów API REST ARR jest przyznawany tylko autoryzowanym użytkownikom. Aby udowodnić autoryzację, należy wysłać *token dostępu* wraz z żądaniami REST. Tokeny te są wystawiane przez *usługę bezpiecznego tokenu* (STS) w zamian za klucz konta. Tokeny mają **okres istnienia 24 godzin** i w związku z tym mogą być wydawane użytkownikom bez udzielania im pełnego dostępu do usługi.

W tym artykule opisano sposób tworzenia takiego tokenu dostępu.

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto ARR](create-an-account.md), jeśli jeszcze go nie masz.

## <a name="token-service-rest-api"></a>Interfejs API REST usługi tokenu

Aby utworzyć tokeny dostępu, *usługa bezpiecznego tokenu* udostępnia jeden interfejs API REST. Adres URL usługi ARR STS to [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com).

### <a name="get-token-request"></a>Żądanie "Pobierz token"

| Identyfikator URI | Metoda |
|-----------|:-----------|
| /konta/**accountId**/token | GET |

| Nagłówek | Wartość |
|--------|:------|
| Autoryzacja | "Konto na **okazicielaId:****accountKey"** |

Zastąp *kontoId* i *accountKey* odpowiednimi danymi.

### <a name="get-token-response"></a>Odpowiedź "Pobierz token"

| Kod stanu | Ładowność JSON | Komentarze |
|-----------|:-----------|:-----------|
| 200 | AccessToken: ciąg | Powodzenie |

| Nagłówek | Przeznaczenie |
|--------|:------|
| MS-CV | Ta wartość może służyć do śledzenia wywołania w usłudze |

## <a name="getting-a-token-using-powershell"></a>Uzyskiwanie tokenu przy użyciu programu PowerShell

Poniższy kod programu PowerShell pokazuje, jak wysłać niezbędne żądanie REST do STS. Następnie drukuje token do monitu programu PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skrypt po prostu drukuje token do danych wyjściowych, z którego można skopiować & wkleić go. W przypadku prawdziwego projektu należy zautomatyzować ten proces.

## <a name="next-steps"></a>Następne kroki

* [Przykładowe skrypty programu PowerShell](../samples/powershell-example-scripts.md)
* [Interfejsy API interfejsu frontendu platformy Azure](../how-tos/frontend-apis.md)
* [Interfejs API REST zarządzania sesjami](../how-tos/session-rest-api.md)
