---
title: Logika ponawiania próby w sdku usługi media dla platformy .NET | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie logiki ponawiania prób w zestawie SDK usług multimedialnych dla platformy .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094659"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika ponawiania próby w sdku usług multimedialnych dla platformy .NET  

Podczas pracy z usługami Platformy Microsoft Azure mogą wystąpić błędy przejściowe. Jeśli wystąpi błąd przejściowy, w większości przypadków po kilku ponownych prób operacji zakończy się pomyślnie. Moduł SDK usługi media services dla platformy .NET implementuje logikę ponawiania próby w celu obsługi błędów przejściowych skojarzonych z wyjątkami i błędami, które są spowodowane przez żądania sieci web, wykonywanie zapytań, zapisywanie zmian i operacje magazynu.  Domyślnie zestaw SDK usług multimedialnych dla platformy .NET wykonuje cztery ponownych prób przed ponownym zgłoszeniem wyjątku do aplikacji. Kod w aplikacji musi następnie poprawnie obsługiwać ten wyjątek.  

 Poniżej przedstawiono krótkie wytyczne dotyczące zasad żądania sieci Web, magazynu, kwerendy i savechanges:  

* Zasady magazynu są używane do operacji przechowywania obiektów blob (przekazywania lub pobierania plików zasobów).  
* Zasady żądania sieci Web są używane dla ogólnych żądań sieci web (na przykład do uzyskiwania tokenu uwierzytelniania i rozpoznawania punktu końcowego klastra użytkowników).  
* Zasady kwerendy są używane do wykonywania zapytań o jednostki z rest (na przykład mediaContext.Assets.Where")..  
* Zasady SaveChanges służy do robienia wszystkiego, co zmienia dane w ramach usługi (na przykład tworzenie jednostki aktualizowanie jednostki, wywoływanie funkcji usługi dla operacji).  
  
  W tym temacie wymieniono typy wyjątków i kody błędów obsługiwane przez zestawie SDK usługi Media Services dla logiki ponawiania prób przez platformę .NET.  

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli opisano wyjątki, które SDK usługi Media Services dla .NET obsługuje lub nie obsługuje dla niektórych operacji, które mogą powodować błędy przejściowe.  

| Wyjątek | Żądanie sieci Web | Magazyn | Zapytanie | Savechanges |
| --- | --- | --- | --- | --- |
| Webexception<br/>Aby uzyskać więcej informacji, zobacz [sekcję Kody stanu WebException.](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) |Tak |Tak |Tak |Tak |
| DataServiceClientException (Z wyjątkiem usługi danych)<br/> Aby uzyskać więcej informacji, zobacz [Kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceQueryException (Nieekceptiona usługi dataserviceexception)<br/> Aby uzyskać więcej informacji, zobacz [Kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceRequestException (Nieeksceptacja usługi dataservicerequestexception)<br/> Aby uzyskać więcej informacji, zobacz [Kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Tak |Tak |Tak |
| DataServiceTransportException (Nieekceptacja usługi danych) |Nie |Nie |Tak |Tak |
| Timeoutexception |Tak |Tak |Tak |Nie |
| Socketexception |Tak |Tak |Tak |Tak |
| Wyjątkowe miejsce w pamięci magazynu |Nie |Tak |Nie |Nie |
| Ioexception |Nie |Tak |Nie |Nie |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>Kody stanu WebException
W poniższej tabeli przedstawiono, dla których kody błędów WebException logika ponawiania jest zaimplementowana. [Wyliczenie WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) definiuje kody stanu.  

| Stan | Żądanie sieci Web | Magazyn | Zapytanie | Savechanges |
| --- | --- | --- | --- | --- |
| ConnectFailure (Połączenie) |Tak |Tak |Tak |Tak |
| NazwaResolutionFailure |Tak |Tak |Tak |Tak |
| ProxyNameResolutionFailure |Tak |Tak |Tak |Tak |
| SendFailure (Niebezpieczeństwo wyślij) |Tak |Tak |Tak |Tak |
| Awaria rurociągu |Tak |Tak |Tak |Nie |
| ConnectionClosed (Zamknięte połączenie) |Tak |Tak |Tak |Nie |
| KeepAliveWłasna |Tak |Tak |Tak |Nie |
| NieznanyError |Tak |Tak |Tak |Nie |
| ReceiveFailure (Nieujmowie) |Tak |Tak |Tak |Nie |
| RequestCanceled (Wyliczył) |Tak |Tak |Tak |Nie |
| Limit czasu |Tak |Tak |Tak |Nie |
| ProtokółError <br/>Ponowna próby na ProtocolError jest kontrolowana przez obsługę kodu stanu HTTP. Aby uzyskać więcej informacji, zobacz [Kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Tak |Tak |Tak |Tak |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>Kody stanu błędów HTTP
Gdy operacje query i SaveChanges zrzucają dataserviceclientexception, DataServiceQueryException lub DataServiceQueryException, kod stanu błędu HTTP jest zwracany we właściwości StatusCode.  W poniższej tabeli przedstawiono kody błędów, dla których implementowana jest logika ponawiania.  

| Stan | Żądanie sieci Web | Magazyn | Zapytanie | Savechanges |
| --- | --- | --- | --- | --- |
| 401 |Nie |Tak |Nie |Nie |
| 403 |Nie |Tak<br/>Obsługa ponownych prób z dłuższym oczekiwaniem. |Nie |Nie |
| 408 |Tak |Tak |Tak |Tak |
| 429 |Tak |Tak |Tak |Tak |
| 500 |Tak |Tak |Tak |Nie |
| 502 |Tak |Tak |Tak |Nie |
| 503 |Tak |Tak |Tak |Tak |
| 504 |Tak |Tak |Tak |Nie |

Jeśli chcesz przyjrzeć się rzeczywistej implementacji zestawu SDK usługi Media Services dla logiki ponawiania ponawiania .NET, zobacz [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

