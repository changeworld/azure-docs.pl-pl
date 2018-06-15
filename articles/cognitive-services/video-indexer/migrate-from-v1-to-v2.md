---
title: Migracja z indeksatora wideo Azure API v1 do v2 | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, jak przeprowadzić migrację z interfejsu API Azure wideo indeksatora v1 do v2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/13/2018
ms.author: juliako
ms.openlocfilehash: 00f5a0d7c9a37c24866cd5e1259800d5e431ccc3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356475"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migracja z v1 wideo indeksatora API do v2

> [!Note]
> Interfejsy API V1 indeksatora wideo są teraz przestarzałe i zostanie usunięte w dniu 1 sierpnia 2018. Należy rozpocząć przy użyciu interfejsów API w wersji 2 indeksatora wideo, aby uniknąć zakłóceń.
>
> Aby opracować z interfejsami API v2 indeksatora wideo, można zapoznać się z instrukcjami znaleziono [tutaj](https://api-portal.videoindexer.ai/). 

W tym artykule opisano zmiany, które zostały wprowadzone w wersji 2.  

## <a name="api-changes"></a>Zmiany interfejsu API

### <a name="authorization-and-operations"></a>Autoryzacja i operacje

W wersji v2 indeksatora wideo zmienić model uwierzytelniania i autoryzacji interfejsu API. Istnieją dwa zestawy interfejsów API: 

* Autoryzacja 
* Operacje

**Autoryzacji** interfejsu API jest używany do uzyskiwania tokenów dostępu dla wywołania **operacji** interfejsu API. **Operacji** interfejs API zawiera wszystkie wideo indeksatora interfejsy API, takich jak przekazywanie wideo, Get insights i innych operacji.

Gdy [subskrypcji](video-indexer-get-started.md) do **autoryzacji** interfejsu API, będzie możliwe uzyskanie tokenów dostępu przez przekazanie klucza subskrypcji (podobnie jak w wersji 1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Pobieranie i przy użyciu tokenu dostępu dla operacji interfejsów API

Podczas wywoływania metody **operacji** interfejsów API, klucz Subskrypcja nie będzie można już używać. Zamiast tego zostanie przekazany tokenów dostępu uzyskany przez **autoryzacji** interfejsu API. 

Każde żądanie ma prawidłowy token, dopasowania poziom dostępu do wywoływania interfejsu API. Na przykład operacje na użytkownika, takie jak pobieranie Twoje konta, wymagają tokenu dostępu. Operacje na koncie poziomu, takie jak listy wszystkich plików wideo, wymagają tokenu dostępu konta. Operacje na wideo, takich jak wideo indeksowanie wymagają tokenu dostępu konta lub tokenu dostępu wideo.

Aby ułatwić, można użyć **autoryzacji** interfejsu API > **GetAccounts** można pobrać kont bez uzyskania użytkownik tokenu najpierw. Możesz również poprosić można uzyskać konta z prawidłowych tokenów, dzięki któremu można pominąć dodatkowe wywołanie dotyczące pobrania tokenu konta.

Aby uzyskać więcej informacji na temat tokenów dostępu różnych zobacz [Użyj Azure wideo indeksatora API](video-indexer-use-apis.md).

### <a name="locations"></a>Lokalizacje

Każde wywołanie interfejsu API powinna zawierać lokalizację konta indeksatora wideo. Wywołania API bez lokalizacji lub z nieprawidłową lokalizację zakończy się niepowodzeniem.

Zastosowanie wartości opisane w poniższej tabeli. **Wartość Param** wartość, gdy przekazujesz używa interfejsu API.

|**Nazwa**|**Wartości parametrów**|**Opis**|
|---|---|---|
|Wersja próbna|wersja próbna|Używana dla konta wersji próbnej. Na przykład: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Zachodnie stany USA|westus2|Używane w regionie Azure zachodnie nam 2.  Na przykład: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Europa Północna |northeurope|Używane dla regionu Azure, Europa Północna. Przykład: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Azja Wschodnia|eastasia|Używane dla regionu Azja Wschodnia Azure. Przykład: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Model danych

Indeksator wideo ma teraz modelu danych uproszczony dostarczyć wiele lepszy wgląd. Aby uzyskać więcej informacji na temat dane wyjściowe, generowane przez interfejs API w wersji 2, zobacz [Sprawdź, czy wyjście wideo indeksatora produkowane przez interfejs API w wersji 2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Definicje interfejsu API indeksatora wideo zostały odpowiednio aktualizowane i są dostępne do pobrania za pośrednictwem [portal interfejsu API](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>Vs v1, V2 przykłady

Nowych interfejsów API w wersji 2 obejmuje 3 podstawowe parametry:

1. [Lokalizacja] - zgodnie z powyższym opisem. Wersja próbna, westus2, northeurope lub eastasia.
2. [YOUR_ACCOUNT_ID] - identyfikator A Guid Twojego konta. Pobrane podczas pobierania wszystkich kont (opisanych poniżej).
3. [YOUR_VIDEO_ID] - identyfikator wideo (np. "d4fa369abc"). Zwrócony podczas przekazywania wideo lub podczas wyszukiwania plików wideo.

#### <a name="uploading-a-video-in-v1"></a>Przekazywanie pliku wideo w wersji 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Przekazywanie pliku wideo w wersji 2:

1. Uzyskaj token dostępu dla żądania przekazywania:

  Możesz uzyskać albo wszystkich kont i tokenów dostępu:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Lub uzyskaj token dostępu określonego konta:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Przekazywanie pliku wideo:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Uzyskiwanie szczegółowych informacji w wersji 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Uzyskiwanie szczegółowych informacji w wersji 2:

1. Użyj tokenu dostępu konta lub uzyskaj token dostępu na poziomie wideo:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Uzyskiwanie szczegółowych informacji:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Pobieranie stanu przetwarzania wideo w wersji 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Pobieranie stanu przetwarzania wideo w wersji 2:

W wersji 2 interfejsu API stan przetwarzania jest zwracana jako część interfejsu API uzyskać indeks wideo.

1. Użyj tokenu dostępu konta lub uzyskaj token dostępu na poziomie wideo:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Uzyskiwanie szczegółowych informacji:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Kolejne kroki

[Użyj Azure indeksatora wideo interfejsu API](video-indexer-use-apis.md)

