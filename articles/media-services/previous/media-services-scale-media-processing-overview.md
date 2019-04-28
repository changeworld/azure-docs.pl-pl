---
title: Skalowanie przetwarzania multimediów — omówienie | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono omówienie skalowanie przetwarzania multimediów za pomocą usługi Azure Media Services.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51d168474fd593dd537a25c0434e240a426c2cbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817330"
---
# <a name="scaling-media-processing-overview"></a>Skalowanie przetwarzania multimediów — omówienie 
Ta strona zawiera omówienie, jak i dlaczego skalowanie przetwarzania multimediów. 

## <a name="overview"></a>Omówienie
Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać jeden z następujących typów jednostki zarezerwowanej: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**. Aby uzyskać więcej informacji, zobacz [typy jednostek zarezerwowanych](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/).

Oprócz określenia typu jednostki zarezerwowanej, można określić aprowizację swojego konta przy użyciu jednostek zarezerwowanych. Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta. Na przykład jeśli konto ma pięć jednostek zarezerwowanych, a następnie pięć zadań będą uruchomione jednocześnie tak długo, jak istnieją zadania do przetworzenia. Pozostałe zadania będzie oczekiwać w kolejce i będzie pobrać wybrany do przetwarzania sekwencyjnie, po zakończeniu bieżące zadanie. Jeśli konto nie ma żadnych jednostek zarezerwowanych zainicjowano obsługę administracyjną, następnie zadania zostaną pobrane po kolei. W tym przypadku czas oczekiwania między jedno zadanie zostało ukończone, a następnie bazujący będzie zależeć od dostępności zasobów w systemie.

## <a name="choosing-between-different-reserved-unit-types"></a>Wybieranie między różnych zastrzeżonych typów jednostek
Poniższa tabela pomaga Ci w podjęciu decyzji, wybierając między różne szybkości kodowania. Zawiera także kilka przypadków testów porównawczych na [film wideo, który można pobrać](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z) przeprowadzić własne testy:

|Typ jednostek|Scenariusz|Przykładowe wyniki dla [7 min 1080 p wideo](https://nimbuspmteam.blob.core.windows.net/asset-46f1f723-5d76-477e-a153-3fd0f9f90f73/SeattlePikePlaceMarket_7min.ts?sv=2015-07-08&sr=c&si=013ab6a6-5ebf-431e-8243-9983a6b5b01c&sig=YCgEB8DxYKK%2B8W9LnBykzm1ZRUTwQAAH9QFUGw%2BIWuc%3D&se=2118-09-21T19%3A28%3A57Z)|
|---|---|---|
| **S1**|Pojedyncza szybkość transmisji bitów kodowania. <br/>Pliki SD większa niż rozwiązania, nie raz poufnych, niskich kosztów.|Kodowanie do pojedynczego szybkości transmisji bitów SD rozpoznawania plik MP4 przy użyciu "Jednej transmisji bitów H264 SD 16 x 9" Trwa 10 minut.|
| **S2**|Pojedyncza szybkość transmisji bitów i wielu kodowanie szybkości transmisji bitów.<br/>Normalnego użycia na potrzeby SD i HD kodowania.|Za pomocą "Pojedynczy szybkość transmisji bitów H264 720p" ustawienie wstępne kodowania zajmuje około 8 minut.<br/><br/>Kodowanie za pomocą "H264 szybkość transmisji bitów h264 720p" ustawienie wstępne zajmuje około 16,8 minut.|
| **S3**|Pojedyncza szybkość transmisji bitów i wielu kodowanie szybkości transmisji bitów.<br/>Pełne HD i 4K rozdzielczości wideo. Czas przetwarzania poufnych, szybsze kodowanie.|Za pomocą "Pojedynczy szybkość transmisji bitów H264 1080p" ustawienie wstępne kodowania zajmuje około 4 minuty.<br/><br/>Kodowanie za pomocą "H264 szybkość transmisji bitów h264 1080p" ustawienie wstępne zajmuje około 8 minut.|

## <a name="considerations"></a>Zagadnienia do rozważenia
> [!IMPORTANT]
> Zapoznaj się z uwagami opisane w tej sekcji.  
> 
> 

* Dla zadania analiza Audio i wideo w analizy, które są wyzwalane przez usługi Media Services v3 lub Video Indexer zdecydowanie zaleca się typ jednostki S3.
* Jeśli z udostępnionej puli, oznacza to, bez żadnych jednostek zarezerwowanych następnie zadań Koduj mieć ta sama wydajność podobnie jak w przypadku jednostek zarezerwowanych S1. Jednak istnieje nie górną granicę do chwili, gdy zadania podrzędne mogą poświęcać w stanie umieszczenia w kolejce i w dowolnym momencie, będzie uruchamiany co najwyżej tylko jedno zadanie.

## <a name="billing"></a>Rozliczenia

Możesz są naliczane na podstawie liczby minut działania aprowizowanych jednostek zarezerwowanych multimediów w ramach Twojego konta. Dzieje się tak niezależnie od czy istnieją wszystkie zadania uruchomione w ramach Twojego konta. Aby uzyskać szczegółowy opis, zobacz sekcję Często zadawane pytania dotyczące [cennika usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/) strony.   

## <a name="quotas-and-limitations"></a>Limity przydziału i ograniczenia
Aby uzyskać informacje o limitach przydziałów i ograniczeń i sposobu otwierania biletu pomocy technicznej, zobacz [przydziały i ograniczenia](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Następny krok
Osiągnięcia skalowania zadań przetwarzania multimediów za pomocą jednej z tych technologii: 

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

