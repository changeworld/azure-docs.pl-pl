---
title: Usługa Azure Media Services encoding kody błędów | Dokumentacja firmy Microsoft
description: Ten temat zawiera listę kodów błędów, które mogą być zwracane w przypadku Napotkano błąd podczas wykonywania zadania kodowania.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 7a1733175f796a0d8c0c0d4247b2db2dd47e4674
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="encoding-error-codes"></a>Kodowanie kody błędów

W poniższej tabeli przedstawiono kody błędów, które mogą być zwracane w przypadku Napotkano błąd podczas wykonywania zadania kodowania.  Aby uzyskać szczegóły błędu w kodzie .NET, należy użyć [szczegóły błędu](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) klasy. Aby uzyskać szczegóły błędu w kodzie REST, należy użyć [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) interfejsu API REST.

| ErrorDetail.Code | Możliwe przyczyny błędu |
| --- | --- |
| Brak informacji |Wystąpił nieznany błąd podczas wykonywania zadania |
| ErrorDownloadingInputAssetMalformedContent |Kategorii błędów, który obejmuje błędy w czasie pobierania wejściowych zasobów, takich jak nazwy uszkodzonych plików, zero pliki o długości, niepoprawny formatuje i tak dalej. |
| ErrorDownloadingInputAssetServiceFailure |Kategoria błędów obejmuje problemów po stronie usługi — na przykład sieci lub magazynu błędy podczas pobierania. |
| ErrorParsingConfiguration |Kategoria błędów, gdy zadanie <see cref="MediaTask.PrivateData"/> (Konfiguracja) jest nieprawidłowa, na przykład konfiguracji nie jest poprawnym systemem ustawienia wstępnego lub zawiera nieprawidłowy kod XML. |
| ErrorExecutingTaskMalformedContent |Kategoria błędy podczas wykonywania zadań, gdy problemy wewnątrz multimedialnych plików wejściowych spowodować niepowodzenie. |
| ErrorExecutingTaskUnsupportedFormat |Kategoria błędy, gdy procesor multimediów nie może przetworzyć pliki udostępniane — formatu media nie obsługiwany lub nie jest zgodna z konfiguracją. Na przykład w trakcie generowania tylko dźwięk danych wyjściowych z zasobu, który zawiera tylko wideo |
| ErrorProcessingTask |Kategoria innych błędów, które procesor multimediów napotka podczas przetwarzania zadania niezwiązane zawartości. |
| ErrorUploadingOutputAsset |Kategoria błędy podczas przekazywania elementu zawartości wyjściowej |
| ErrorCancelingTask |Kategoria błędów, aby pokrywał błędy przy próbie anulowania zadania |
| TransientError |Kategoria błędów, aby pokrywał przejściowych problemów (np.) tymczasowe problemy z siecią z usługą Azure Storage) |

Aby uzyskać pomoc od **Media Services** zespół, otwórz [obsługuje biletu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywania zaawansowanych zadań kodowania dostosowując ustawienia standardu Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
