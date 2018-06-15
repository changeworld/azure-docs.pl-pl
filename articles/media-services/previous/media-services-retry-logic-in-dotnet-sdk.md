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
ms.openlocfilehash: 34125712c59938b3a74e7cdc150f3f16b694b92f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790426"
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
| WebException<br/>Aby uzyskać więcej informacji, zobacz [kodów stanu WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) sekcji. |Yes |Yes |Yes |Yes |
| DataServiceClientException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceQueryException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceRequestException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceTransportException |Nie |Nie |Yes |Yes |
| TimeoutException |Yes |Yes |Yes |Nie |
| Socketexception — |Yes |Yes |Yes |Yes |
| StorageException |Nie |Yes |Nie |Nie |
| IOException |Nie |Yes |Nie |Nie |

### <a name="WebExceptionStatus"></a> Kody stanu WebException
W poniższej tabeli przedstawiono dla kody błędów WebException Logika ponawiania jest zaimplementowana. [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) wyliczenie definiuje kodów stanu.  

| Stan | Żądanie sieci Web | Magazyn | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Yes |Yes |Yes |Yes |
| NameResolutionFailure |Yes |Yes |Yes |Yes |
| ProxyNameResolutionFailure |Yes |Yes |Yes |Yes |
| SendFailure |Yes |Yes |Yes |Yes |
| PipelineFailure |Yes |Yes |Yes |Nie |
| ConnectionClosed |Yes |Yes |Yes |Nie |
| KeepAliveFailure |Yes |Yes |Yes |Nie |
| Nieznany |Yes |Yes |Yes |Nie |
| ReceiveFailure |Yes |Yes |Yes |Nie |
| RequestCanceled |Yes |Yes |Yes |Nie |
| Limit czasu |Yes |Yes |Yes |Nie |
| ProtocolError <br/>Ponów próbę wykonania na ProtocolError jest kontrolowana przez Obsługa kod stanu HTTP. Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Yes |Yes |Yes |Yes |

### <a name="HTTPStatusCode"></a> Kody stanu błędu HTTP
Podczas operacji zapytania i metody SaveChanges throw DataServiceClientException, DataServiceQueryException lub DataServiceQueryException, kod stanu HTTP Błąd jest zwracany w Właściwość StatusCode.  W poniższej tabeli przedstawiono dla kody błędów Logika ponawiania jest zaimplementowana.  

| Stan | Żądanie sieci Web | Magazyn | Zapytanie | Metody SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nie |Yes |Nie |Nie |
| 403 |Nie |Yes<br/>Obsługa ponownych prób z czeka dłużej. |Nie |Nie |
| 408 |Yes |Yes |Yes |Yes |
| 429 |Yes |Yes |Yes |Yes |
| 500 |Yes |Yes |Yes |Nie |
| 502 |Yes |Yes |Yes |Nie |
| 503 |Yes |Yes |Yes |Yes |
| 504 |Yes |Yes |Yes |Nie |

Aby przyjrzeć rzeczywistego wykonania SDK usługi Media Services dla platformy .NET Logika ponawiania, zobacz [azure sdk media services dla](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

