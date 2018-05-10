---
title: Skalowanie przetwarzania multimediów omówienie | Dokumentacja firmy Microsoft
description: Ten temat zawiera omówienie skalowania przetwarzania nośnika za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 894b403b59624b6c42ce947169e9c9ac30ec76b9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="scaling-media-processing-overview"></a>Skalowanie przetwarzania multimediów — omówienie
Ta strona zawiera podstawowe informacje o i dlaczego skalowania przetwarzania multimediów. 

## <a name="overview"></a>Przegląd
Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. Aby uzyskać więcej informacji, zobacz [typy jednostek zarezerwowanych](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oprócz określenie typu jednostki zarezerwowane, możesz określić Aby udostępnić konta zastrzeżone jednostki. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostki zarezerwowanego, następnie nośnika pięć zadań będą uruchomione jednocześnie tak długo, jak istnieją zadania do przetworzenia. Pozostałych zadań będzie czekać w kolejce i ma pobrać pobierany do przetwarzania sekwencyjnie, po zakończeniu uruchomione zadanie. Jeśli konto nie ma żadnych jednostek zarezerwowanego zainicjowano obsługę administracyjną, następnie zadania będą pobierane po kolei. W takim przypadku czas oczekiwania między jedno zadanie zakończenia i kolejnej początkowe zależy od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybór między różnych zastrzeżonych typów jednostek
Poniższa tabela ułatwia podjęcie decyzji, wybierając między różnych szybkościach kodowania. Również zapewnia kilka przypadków testu porównawczego i udostępnia SAS adresów URL, który służy do pobierania plików wideo, na których można wykonywać własne testy:

| Scenariusze | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Zamierzonym użyciem case |Kodowanie pojedynczej szybkości transmisji bitów. <br/>Pliki SD lub poniżej rozwiązania, czas nie poufne, niskich kosztów. |Pojedynczej szybkości transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Normalnego użycia zarówno SD, jak i HD kodowania. |Pojedynczej szybkości transmisji bitów i kodowanie wielu szybkości transmisji bitów.<br/>Pełna HD oraz 4K rozpoznawania wideo. Czas przetwarzania poufnych, szybszych kodowania. |
| Testu porównawczego |[Plik wejściowy: 640x360p na 29,97 5 minut długich ramek/sekundę](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>Kodowanie do pojedynczej szybkości transmisji bitów plik MP4 o takiej samej rozdzielczości trwa około 11 minut. |[Plik wejściowy: 1280x720p na 29,97 5 minut długich ramek/sekundę](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>Kodowanie z "H264 pojedynczej szybkości transmisji bitów 720p" wstępnie trwa około 5 minut.<br/><br/>Z kodowaniem "H264 szybkość transmisji bitów 720p" ustawienie wstępne trwa około 11,5 minut. |[Plik wejściowy: 1920x1080p na 29,97 5 minut długich ramek/sekundę](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>Kodowanie z "H264 pojedynczej szybkości transmisji bitów 1080p" wstępnie trwa około 2.7 minut.<br/><br/>Z kodowaniem "H264 szybkość transmisji bitów 1080p" ustawienie wstępne trwa około wersji 5.7 minut. |

## <a name="considerations"></a>Zagadnienia do rozważenia
> [!IMPORTANT]
> Zapoznaj się z uwagami opisane w tej sekcji.  
> 
> 

* Zastrzeżone jednostki działa w przypadku parallelizing przetwarzania nośnika, w tym Indeksowanie zadań za pomocą usługi Azure Media indeksatora.  Jednak w przeciwieństwie do kodowania zadania indeksowania nie są przetwarzane szybciej przy użyciu szybszych jednostek zarezerwowanych.
* Jeśli za pomocą współużytkowanej puli, oznacza to, bez żadnych jednostek zarezerwowanego, zadań Koduj mieć tego samego wydajności tak jak w przypadku S1 RUs. Niestety nie są nie górną granicę do chwili poświęcić zadań w kolejce stanu i w dowolnym momencie, będą uruchomione co najwyżej tylko jedno zadanie.

## <a name="billing"></a>Rozliczenia

Opłaty są naliczane na podstawie rzeczywistej liczby minut użycia jednostek zarezerwowanych multimediów. Aby uzyskać szczegółowy opis, zobacz sekcję Często zadawane pytania dotyczące [cenach usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia
Aby uzyskać informacje o przydziały i ograniczenia oraz sposobu otwierania biletu pomocy technicznej, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Następny krok
Osiągnięcia skalowania zadania przetwarzania multimediów z jednej z tych technologii: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Aby uzyskać najnowszą wersję zestawu SDK języka Java i zacząć programować w języku Java, zobacz [Rozpoczynanie korzystania z zestawu SDK klienta Java dla usług Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Aby pobrać najnowszy zestaw SDK języka PHP dla usługi Media Services, poszukaj wersji 0.5.7 pakietu Microsoft/WindowAzure w [repozytorium Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

