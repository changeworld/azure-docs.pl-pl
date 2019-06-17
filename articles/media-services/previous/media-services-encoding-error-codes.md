---
title: Usługa Azure Media Services encoding kody błędów | Dokumentacja firmy Microsoft
description: Ten temat zawiera listę kodów błędów, które mogą być zwracane w przypadku, gdy wystąpił błąd podczas wykonywania zadania kodowania...
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709531"
---
# <a name="encoding-error-codes"></a>Kody błędów kodowania

Poniższa tabela zawiera listę kodów błędów, które mogą być zwracane w przypadku, gdy wystąpił błąd podczas wykonywania zadania kodowania.  Aby uzyskać szczegóły błędu w kodzie .NET, użyj [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) klasy. Aby uzyskać szczegóły błędu w kodzie REST, należy użyć [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) interfejsu API REST.

| ErrorDetail.Code | Możliwe przyczyny błędu |
| --- | --- |
| Nieznane |Nieznany błąd podczas wykonywania zadania |
| ErrorDownloadingInputAssetMalformedContent |Kategorii błędów, który obejmuje błędy podczas pobierania danych wejściowych zasobów, takich jak nazwy uszkodzonych plików, zerowego pliki o długości, niepoprawny formatuje i tak dalej. |
| ErrorDownloadingInputAssetServiceFailure |Kategoria błędów, który obejmuje problemów po stronie usługi — na przykład błędy sieci i magazynu podczas pobierania. |
| ErrorParsingConfiguration |Kategoria błędów, gdy zadanie \<Zobacz cref="MediaTask.PrivateData"/ > (Konfiguracja) nie jest prawidłowa, na przykład konfiguracja nie jest ustawienie wstępne systemu nieprawidłowy lub zawiera nieprawidłowy kod XML. |
| ErrorExecutingTaskMalformedContent |Kategoria błędów podczas wykonywania zadania, w którym problemów wewnątrz multimedialnych plików wejściowych spowodować niepowodzenie. |
| ErrorExecutingTaskUnsupportedFormat |Kategorii błędów, gdy procesor multimediów nie może przetworzyć pliki udostępniane — format multimediów nie jest obsługiwany lub nie jest zgodna z konfiguracją. Na przykład próby wygenerowanie danych wyjściowych zawierających tylko dane audio z zasobu, który zawiera tylko wideo |
| ErrorProcessingTask |Kategoria innych błędów, które procesor multimediów napotka podczas przetwarzania zadania, które są związane z zawartością. |
| ErrorUploadingOutputAsset |Kategoria błędów podczas przekazywania elementu zawartości wyjściowej |
| ErrorCancelingTask |Kategoria błędów, aby pokrywał błędy przy próbie anulowania zadania |
| TransientError |Kategoria błędów przejściowych problemów (np.) tymczasowe problemy sieciowe przy użyciu usługi Azure Storage) |

Aby uzyskać pomoc od **usługi Media Services** zespołu, otwórz [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywania zaawansowanych zadań kodowania, dostosowywanie ustawień wstępnych usługi Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
