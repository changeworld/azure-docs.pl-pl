---
title: Logika ponawiania w zestawie SDK usługi multimediów dla platformy .NET | Dokumentacja firmy Microsoft
description: Temat zawiera omówienie Logika ponawiania w zestawie SDK usługi multimediów dla platformy .NET.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094659"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logika ponawiania w zestawie SDK usługi multimediów dla platformy .NET  

Podczas pracy z usługami Microsoft Azure, mogą wystąpić błędy przejściowe. Jeśli wystąpi błąd przejściowy, w większości przypadków, po kilku próbach operacja powiedzie się. Media Services SDK dla platformy .NET implementuje logikę ponawiania, do obsługi błędów przejściowych, skojarzone z wyjątków i błędów, które są spowodowane przez żądania sieci web, wykonywanie zapytań, zapisywanie zmian i operacji magazynu.  Domyślnie Media Services SDK dla platformy .NET wykonuje czterech ponownych prób przed ponownego zgłaszania wyjątku do aplikacji. Kod w aplikacji następnie musi poprawnie obsługiwać tego wyjątku.  

 Poniżej przedstawiono krótki opis wytyczne dotyczące zasad żądania sieci Web, przechowywania, zapytania i SaveChanges:  

* Zasady przechowywania są używane dla operacji magazynu obiektów blob (przekazywanie lub pobieranie plików zasobów).  
* Zasady żądania sieci Web jest używany do żądania sieci web ogólnego (na przykład uzyskiwanie tokenu uwierzytelniania i rozpoznawanie punkt końcowy klastra użytkowników).  
* Zasady zapytań są używane do wykonywania zapytań jednostek z REST (na przykład mediaContext.Assets.Where(...)).  
* Zasady SaveChanges są używane do wykonywania wszystkich elementów, które zmiany danych w ramach usługi (na przykład utworzyć jednostkę aktualizowania jednostki, wywołanie funkcji usługi dla operacji).  
  
  Ten temat zawiera listę typów wyjątków i Logika ponawiania próby kody błędów, które są obsługiwane przez usługę Media Services SDK dla platformy .NET.  

## <a name="exception-types"></a>Typy wyjątków
W poniższej tabeli opisano wyjątki, które obsługuje Media Services SDK dla platformy .NET, lub nie obsługuje pewnych operacji, które mogą powodować błędy przejściowe.  

| Wyjątek | Żądania sieci Web | Magazyn | Zapytanie | SaveChanges |
| --- | --- | --- | --- | --- |
| Klasa WebException<br/>Aby uzyskać więcej informacji, zobacz [kodów stanu WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) sekcji. |Yes |Yes |Yes |Yes |
| DataServiceClientException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceQueryException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceRequestException<br/> Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nie |Yes |Yes |Yes |
| DataServiceTransportException |Nie |Nie |Yes |Yes |
| TimeoutException |Yes |Yes |Yes |Nie |
| SocketException |Yes |Yes |Yes |Yes |
| StorageException |Nie |Yes |Nie |Nie |
| IOException |Nie |Yes |Nie |Nie |

### <a name="WebExceptionStatus"></a> Klasa WebException kody stanu
W poniższej tabeli przedstawiono, który kodów błędu WebException Logika ponawiania jest zaimplementowana. [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) wyliczenie definiuje kodów stanu.  

| Stan | Żądania sieci Web | Magazyn | Zapytanie | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Yes |Yes |Yes |Yes |
| NameResolutionFailure |Yes |Yes |Yes |Yes |
| ProxyNameResolutionFailure |Yes |Yes |Yes |Yes |
| SendFailure |Yes |Yes |Yes |Yes |
| PipelineFailure |Yes |Yes |Yes |Nie |
| ConnectionClosed |Yes |Yes |Yes |Nie |
| KeepAliveFailure |Yes |Yes |Yes |Nie |
| Nieznany błąd zostanie |Yes |Yes |Yes |Nie |
| ReceiveFailure |Yes |Yes |Yes |Nie |
| RequestCanceled |Yes |Yes |Yes |Nie |
| Limit czasu |Yes |Yes |Yes |Nie |
| ProtocolError <br/>Ponowienie dotyczące ProtocolError jest kontrolowana przez obsługę kodu stanu HTTP. Aby uzyskać więcej informacji, zobacz [kody stanu błędu HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Yes |Yes |Yes |Yes |

### <a name="HTTPStatusCode"></a> Kody stanu błędu HTTP
Podczas operacji zapytań i SaveChanges zgłosić wyjątek DataServiceClientException, DataServiceQueryException lub DataServiceQueryException, kod statusu błędu HTTP jest zwracany we właściwości StatusCode.  W poniższej tabeli przedstawiono dla kody błędów Logika ponawiania jest zaimplementowana.  

| Stan | Żądania sieci Web | Magazyn | Zapytanie | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nie |Yes |Nie |Nie |
| 403 |Nie |Yes<br/>Obsługa ponownych prób przy użyciu dłuższego oczekiwania. |Nie |Nie |
| 408 |Yes |Yes |Yes |Yes |
| 429 |Yes |Yes |Yes |Yes |
| 500 |Yes |Yes |Yes |Nie |
| 502 |Yes |Yes |Yes |Nie |
| 503 |Yes |Yes |Yes |Yes |
| 504 |Yes |Yes |Yes |Nie |

Jeśli chcesz Przyjrzyj się rzeczywistą implementację Media Services SDK dla platformy .NET logikę ponawiania próby, zobacz [azure sdk media services dla](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Kolejne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

