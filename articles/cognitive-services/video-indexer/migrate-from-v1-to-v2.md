---
title: Migrowanie z interfejsu API indeksatora wideo platformy Azure w wersji 1 do wersji 2
titlesuffix: Azure Cognitive Services
description: W tym temacie wyjaśniono, jak przeprowadzić migrację z interfejsu API indeksatora wideo platformy Azure w wersji 1 do 2.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3c70bbe11d94e6b03d615b8d1394ccdca6bd3790
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985632"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrowanie z interfejsu API indeksatora wideo w wersji 1 do wersji 2

> [!Note]
> Interfejsu API w wersji 1 indeksatora wideo została zakończona w dniu 1 sierpnia 2018. Teraz należy używać interfejsu API indeksatora wideo w wersji 2. <br/>Tworzenie za pomocą interfejsów API w wersji 2 indeksatora wideo, można zapoznać się z instrukcjami znaleziono [tutaj](https://api-portal.videoindexer.ai/). 

W tym artykule opisano zmiany, które zostały wprowadzone w wersji 2.  

## <a name="api-changes"></a>Zmiany interfejsu API

### <a name="authorization-and-operations"></a>Autoryzacja i operacje

W wersji v2 Video Indexer zmienić model uwierzytelniania i autoryzacji, interfejsu API. Istnieją dwa zestawy interfejsów API: 

* Autoryzacja 
* Operacje

**Autoryzacji** API jest używane do uzyskania tokenów dostępu do wywoływania **operacji** interfejsu API. **Operacji** interfejs API zawiera wszystkie wideo indeksatora interfejsy API, takich jak przekazywanie wideo, Get insights i innych operacji.

Po [subskrybowania](video-indexer-get-started.md) do **autoryzacji** interfejsu API, będzie umożliwiające uzyskanie tokenów dostępu, przekazując klucz subskrypcji (podobnie jak w wersji 1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Pobieranie i przy użyciu tokenu dostępu dla operacji interfejsów API

Podczas wywoływania **operacji** nie będzie można już używać interfejsów API, klucz subskrypcji. Zamiast tego będzie przekazywać tokenów dostępu uzyskany przez **autoryzacji** interfejsu API. 

Każde żądanie powinno mieć prawidłowy token dopasowania poziom dostępu interfejsu API to wywołanie. Na przykład operacje na użytkownika, takie jak pobieranie konta, wymagają tokenu dostępu użytkownika. Operacje na koncie poziomu, takich jak lista wszystkich filmów wideo, wymaga tokenu dostępu do konta. Operacje dotyczące filmów wideo, takich jak wideo reindex wymaga tokenu dostępu do konta lub tokenu dostępu wideo.

Aby je ułatwić, można użyć **autoryzacji** interfejs API > **GetAccounts** można uzyskać kont bez uzyskania użytkownika tokenu najpierw. Można również zadawać można pobrać konta z prawidłową tokenów, dzięki któremu można pominąć dodatkowe wywołanie w celu uzyskania tokenu konta.

Aby uzyskać więcej informacji na temat tokenów dostępu różnych zobacz [Użyj interfejsu API indeksatora wideo Azure](video-indexer-use-apis.md).

### <a name="locations"></a>Lokalizacje

Każde wywołanie interfejsu API powinno obejmować lokalizacji swoje konto usługi Video Indexer. Wywołania interfejsu API bez lokalizacji lub z nieprawidłową lokalizację zakończy się niepowodzeniem.

Stosowanie wartości opisanych w poniższej tabeli. **Param value** jest wartość, gdy przekazujesz przy użyciu interfejsu API.

|**Nazwa**|**Wartości parametrów**|**Opis**|
|---|---|---|
|Wersja próbna|wersja próbna|Używany dla konta wersji próbnej. Na przykład: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Zachodnie stany USA|westus2|Używane w regionie platformy Azure zachodnie stany USA 2.  Na przykład: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Europa Północna |northeurope|Używane w regionie Europa Północna platformy Azure. Przykład: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Azja Wschodnia|eastasia|Używane dla regionu Azja Wschodnia platformy Azure. Przykład: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Model danych

Indeksator wideo ma teraz model danych, aby zapewniają dużo bardziej zrozumiały szczegółowe informacje. Aby uzyskać więcej informacji na temat danych wyjściowych wytworzonych przez interfejsy API wersji 2 zobacz [zbadanie danych wyjściowych indeksatora wideo utworzone przez interfejs API w wersji 2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Definicje interfejsu API indeksatora wideo zostały odpowiednio aktualizowana i są dostępne do pobrania za pośrednictwem [portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>Vs v1, V2 przykłady

Nowe interfejsy API w wersji 2 obejmuje 3 podstawowe parametry:

1. [Lokalizacja] - zgodnie z powyższym opisem. Wersja próbna, westus2, northeurope lub Azja Wschodnia.
2. [YOUR_ACCOUNT_ID] - Identyfikator Guid konta. Pobierane podczas pobierania wszystkich kont (opisanych poniżej).
3. [YOUR_VIDEO_ID] - identyfikator wideo (np. "d4fa369abc"). Zwrócony podczas przekazywania filmu wideo lub podczas wyszukiwania filmów wideo.

#### <a name="uploading-a-video-in-v1"></a>Przekazywanie wideo w wersji 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Przekazywanie wideo w wersji 2:

1. Uzyskiwanie tokenu dostępu na żądanie przekazania:

  Możesz uzyskać wszystkie konta i tokenów dostępu:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Lub uzyskiwanie tokenu dostępu do określonego konta:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Przekazywanie wideo:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Uzyskiwanie szczegółowych danych w wersji 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Uzyskiwanie szczegółowych danych w wersji 2:

1. Użyj tokenu dostępu do konta lub uzyskać token dostępu na poziomie wideo:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Uzyskiwanie szczegółowych informacji:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Pobieranie stanu przetwarzania plików wideo w wersji 1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Pobieranie stanu przetwarzania plików wideo w wersji 2:

W wersji 2 interfejsu API stan przetwarzania są zwracane jako część uzyskać indeks interfejsu API wideo.

1. Użyj tokenu dostępu do konta lub uzyskać token dostępu na poziomie wideo:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Uzyskiwanie szczegółowych informacji:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Kolejne kroki

[Użyj interfejsu API indeksatora wideo platformy Azure](video-indexer-use-apis.md)

