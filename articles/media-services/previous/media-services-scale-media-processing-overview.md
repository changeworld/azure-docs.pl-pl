---
title: Omówienie skalowania przetwarzania multimediów | Dokumenty firmy Microsoft
description: Ten temat jest omówieniem skalowania przetwarzania multimediów za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 780d3ab5047bff321d0c554880ba2995bcf25524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102909"
---
# <a name="scaling-media-processing-overview"></a>Skalowanie przetwarzania multimediów — omówienie 
Ta strona zawiera omówienie sposobu i powodów skalowania przetwarzania multimediów. 

## <a name="overview"></a>Omówienie
Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać między następującymi typami jednostek zarezerwowanych: **S1**, **S2**lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. Aby uzyskać więcej informacji, zobacz [typy jednostek zarezerwowanych](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oprócz określenia typu jednostki zarezerwowanej można określić, aby aprowizować konto za pomocą jednostek zarezerwowanych. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Jeśli na przykład konto ma pięć jednostek zarezerwowanych, pięć zadań multimedialnych będzie uruchamianych jednocześnie, o ile istnieją zadania do przetworzenia. Pozostałe zadania będą czekać w kolejce i zostaną odebrane do przetwarzania sekwencyjnie po zakończeniu uruchomionego zadania. Jeśli konto nie ma żadnych jednostek zarezerwowanych aprowizowanych, zadania będą pobierane kolejno. W takim przypadku czas oczekiwania między jednym zakończeniem zadania a następnym uruchomieniem będzie zależeć od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybór między różnymi typami jednostek zarezerwowanych
Poniższa tabela ułatwia podejmowanie decyzji przy wyborze między różnymi szybkościami kodowania. Zawiera również kilka przypadków porównawczych na [filmie, który można pobrać,](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) aby wykonać własne testy:

|Typ RU|Scenariusz|Przykładowe wyniki [wideo 7 min 1080p](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Kodowanie pojedynczej szybkości transmisji bitów. <br/>Pliki w rozdzielczości SD lub poniżej, nie wrażliwe na czas, niskie koszty.|Kodowanie do pojedynczego pliku MP4 o rozdzielczości bitrate SD za pomocą "H264 Single Bitrate SD 16x9" trwa około 7 minut.|
| **S2**|Kodowanie pojedynczej szybkości transmisji bitów i wielu bitrate.<br/>Normalne użycie zarówno kodowania SD, jak i HD.|Kodowanie z predefiniowane "H264 Single Bitrate 720p" trwa około 6 minut.<br/><br/>Kodowanie z ustawieniem predefiniowane "H264 Multiple Bitrate 720p" trwa około 12 minut.|
| **S3**|Kodowanie pojedynczej szybkości transmisji bitów i wielu bitrate.<br/>Filmy w rozdzielczości Full HD i 4K. Kodowanie zależne od czasu i szybsze.|Kodowanie z predefiniowaniem "H264 Single Bitrate 1080p" trwa około 3 minut.<br/><br/>Kodowanie z ustawieniem predefiniowane "H264 Multiple Bitrate 1080p" trwa około 8 minut.|

## <a name="considerations"></a>Zagadnienia do rozważenia
> [!IMPORTANT]
> Przejrzyj zagadnienia opisane w tej sekcji.  
> 
> 

* W przypadku zadań analizy audio i analizy wideo, które są wyzwalane przez usługi Media Services w wersji 3 lub indeksatora wideo, zdecydowanie zaleca się typ jednostki S3.
* Jeśli używasz puli udostępnionej, oznacza to, że bez żadnych jednostek zarezerwowanych, następnie zadania kodowania mają taką samą wydajność jak w przypadku jednostek RUs S1. Jednak nie ma górnej granicy czasu zadania można spędzić w stanie kolejki, a w danym momencie, co najwyżej tylko jedno zadanie będzie uruchomiony.

## <a name="billing"></a>Rozliczenia

Opłata jest naliczana na podstawie liczby minut, przez którą jednostki zarezerwowane multimediów są aprowizowane na twoim koncie. Dzieje się tak niezależnie od tego, czy na twoim koncie są uruchomione zadania. Szczegółowe informacje można znaleźć w sekcji Często zadawane pytania na stronie [cennik usługi Media Services.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia
Aby uzyskać informacje o przydziałach i ograniczeniach oraz o tym, jak otworzyć bilet pomocy technicznej, zobacz [Przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Następny krok
Osiągnij zadanie przetwarzania nośników skalowania za pomocą jednej z następujących technologii: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [Reszta](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
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

