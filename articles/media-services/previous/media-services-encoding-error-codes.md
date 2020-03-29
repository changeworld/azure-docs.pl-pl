---
title: Kody błędów kodowania usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie wymieniono kody błędów, które mogą zostać zwrócone w przypadku wystąpienia błędu podczas wykonywania zadania kodowania..
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64709531"
---
# <a name="encoding-error-codes"></a>Kody błędów kodowania

W poniższej tabeli wymieniono kody błędów, które mogą zostać zwrócone w przypadku wystąpienia błędu podczas wykonywania zadania kodowania.  Aby uzyskać szczegółowe informacje o błędzie w kodzie platformy .NET, należy użyć [errorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) klasy. Aby uzyskać szczegółowe informacje o błędzie w kodzie REST, użyj interfejsu API [REST funkcji ErrorDetail.](https://msdn.microsoft.com/library/jj853026.aspx)

| BłądDetail.Code | Możliwe przyczyny błędu |
| --- | --- |
| Nieznane |Nieznany błąd podczas wykonywania zadania |
| ErrorDownloadingInputAssetMalformedContent |Kategoria błędów, która obejmuje błędy w pobieraniu zasobów wejściowych, takie jak złe nazwy plików, pliki o zerowej długości, nieprawidłowe formaty i tak dalej. |
| Funkcja ErrorDownloadingInputAssetServiceFailure |Kategoria błędów, która obejmuje problemy po stronie usługi — na przykład błędy sieci lub magazynu podczas pobierania. |
| Konfiguracja errorparsing |Kategoria błędów, w \<których zadanie zobacz cref="MediaTask.PrivateData"/> (konfiguracja) jest nieprawidłowa, na przykład konfiguracja nie jest prawidłowym ustawieniem systemu lub zawiera nieprawidłowy kod XML. |
| BłądWykonanietaskMalformedContent |Kategoria błędów podczas wykonywania zadania, w którym problemy wewnątrz wejściowych plików multimedialnych powodują błąd. |
| BłądWykonaniezazakZawsk ObsługiwaneFormat |Kategoria błędów, w których procesor nośników nie może przetworzyć dostarczonych plików - format nośnika nie jest obsługiwany lub nie jest zgodny z konfiguracją. Na przykład próba wytworzenia wyjścia tylko audio z zasobu, który ma tylko |
| BłądProcessingTask |Kategoria innych błędów napotkanych przez procesor nośników podczas przetwarzania zadania, które nie są związane z zawartością. |
| Zestaw errorUploadingOutputAsset |Kategoria błędów podczas przesyłania zasobu wyjściowego |
| BłądCancelingTask |Kategoria błędów w celu pokrycia błędów podczas próby anulowania zadania |
| PrzemijająceError |Kategoria błędów w celu uwzględnienia problemów przejściowych (np. tymczasowe problemy z siecią Azure Storage) |

Aby uzyskać pomoc od zespołu **usługi Media Services,** otwórz [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowywanie ustawień predefiniowanych programu Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Przydziały i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
