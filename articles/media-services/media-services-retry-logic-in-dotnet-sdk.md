---
title: Spróbuj ponownie logikę w zestawie SDK usługi multimediów dla platformy .NET | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie logiki ponawiania próby w zestawie SDK usługi multimediów dla platformy .NET.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 859dd76db4ba06196a853469a1385703d835fa22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23860794"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Spróbuj ponownie logikę w zestawie SDK usługi multimediów dla platformy .NET
Podczas pracy z usługami Microsoft Azure, może wystąpić błędów przejściowych. W przypadku wystąpienia błędu przejściowego, w większości przypadków po kilku próbach powiedzie się. SDK usługi Media Services dla platformy .NET implementuje logikę ponawiania do obsługi błędów przejściowych skojarzone z wyjątków i błędów spowodowanych przez żądania sieci web, wykonywanie kwerend, zapisywanie zmian i operacje magazynu.  Domyślnie SDK usługi Media Services dla platformy .NET wykonuje czterech ponownych prób przed ponownym zgłoszeniem wyjątku do aplikacji. Kod w aplikacji następnie musi poprawnie obsługiwać tego wyjątku.  

 Poniżej znajduje się krótki wskazówek dotyczących żądania sieci Web, magazynu, zapytania i metody SaveChanges zasad:  

* Zasady przechowywania jest używany dla operacje magazynu obiektów blob (przekazywanie i pobieranie plików zasobów).  
* Zasada żądania sieci Web jest używana do żądania ogólnego sieci web (na przykład w przypadku uzyskiwanie tokenu uwierzytelniania i rozwiązywania punktu końcowego klastra użytkowników).  
* Zasady zapytania są używane do wykonywania zapytań jednostek z REST (na przykład mediaContext.Assets.Where(...)).  
* Metody SaveChanges zasady są używane do wykonywania wszystkich elementów, które zmiany danych w ramach usługi (na przykład tworzenie jednostki aktualizowania jednostki, dla operacji podczas wywoływania funkcji usługi).  
  
  Ten temat zawiera listę typów wyjątków i Logika ponawiania próby kody błędów, które są obsługiwane przez zestaw SDK usługi multimediów dla platformy .NET.  

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli opisano wyjątki, które SDK usługi Media Services dla platformy .NET obsługuje lub nie obsługuje dla niektórych operacji, które mogą być przyczyną błędów przejściowych.  

| Wyjątek | Żądanie sieci Web | Magazyn | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Aby uzyskać więcej informacji, zobacz [kodów stanu WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) sekcji. |Tak |Tak |Tak |Tak |
| DataServiceClientException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceQueryException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceRequestException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceTransportException |Nie |Nie |Tak |Tak |
| TimeoutException |Tak |Tak |Tak |Nie |
| Socketexception — |Tak |Tak |Tak |Tak |
| StorageException |Nie |Tak |Nie |Nie |
| IOException |Nie |Tak |Nie |Nie |

### <a name="WebExceptionStatus"></a>Kody stanu WebException
W poniższej tabeli przedstawiono dla kody błędów WebException Logika ponawiania jest zaimplementowana. [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) wyliczenie definiuje kodów stanu.  

| Stan | Żądanie sieci Web | Magazyn | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Tak |Tak |Tak |Tak |
| NameResolutionFailure |Tak |Tak |Tak |Tak |
| ProxyNameResolutionFailure |Tak |Tak |Tak |Tak |
| SendFailure |Tak |Tak |Tak |Tak |
| PipelineFailure |Tak |Tak |Tak |Nie |
| ConnectionClosed |Tak |Tak |Tak |Nie |
| KeepAliveFailure |Tak |Tak |Tak |Nie |
| Nieznany |Tak |Tak |Tak |Nie |
| ReceiveFailure |Tak |Tak |Tak |Nie |
| RequestCanceled |Tak |Tak |Tak |Nie |
| Limit czasu |Tak |Tak |Tak |Nie |
| ProtocolError <br/>Ponów próbę wykonania na ProtocolError jest kontrolowana przez Obsługa kod stanu HTTP. Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Tak |Tak |Tak |Tak |

### <a name="HTTPStatusCode"></a>Kody stanu błędu HTTP
Podczas operacji zapytania i metody SaveChanges throw DataServiceClientException, DataServiceQueryException lub DataServiceQueryException, kod stanu HTTP Błąd jest zwracany w Właściwość StatusCode.  W poniższej tabeli przedstawiono dla kody błędów Logika ponawiania jest zaimplementowana.  

| Stan | Żądanie sieci Web | Magazyn | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nie |Tak |Nie |Nie |
| 403 |Nie |Tak<br/>Obsługa ponownych prób z czeka dłużej. |Nie |Nie |
| 408 |Tak |Tak |Tak |Tak |
| 429 |Tak |Tak |Tak |Tak |
| 500 |Tak |Tak |Tak |Nie |
| 502 |Tak |Tak |Tak |Nie |
| 503 |Tak |Tak |Tak |Tak |
| 504 |Tak |Tak |Tak |Nie |

Aby przyjrzeć rzeczywistego wykonania SDK usługi Media Services dla platformy .NET Logika ponawiania, zobacz [azure sdk media services dla](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

