---
title: Korzystanie z usługi Azure Media Clipper w portalu | Microsoft Docs
description: Tworzenie klipów przy użyciu usługi Azure Media Clipper w witrynie Azure Portal
services: media-services
keywords: Clip; subclip; kodowanie; nośnik
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684989"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Tworzenie klipów za pomocą usługi Azure Media Clipper w portalu  

Możesz użyć usługi Azure Media Clipper w portalu, aby utworzyć klipy z zasobów na kontach usługi Media Services. Aby rozpocząć, przejdź do swojego konta usługi Media Services w portalu. Następnie wybierz kartę **podklip** .

Na karcie **podklipu** można rozpocząć tworzenie klipów. W portalu usługa Clipper ładuje pliki MP4 o pojedynczej szybkości transmisji bitów, wiele szybkości transmisji bitów pliki MP4 i archiwa na żywo, które są publikowane z prawidłowym lokalizatorem przesyłania strumieniowego. Nieopublikowane zasoby nie są ładowane.

Ten Clipper jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać dostęp do programu Clipper w Azure Portal, przejdź do tej [publicznej strony w wersji zapoznawczej](https://portal.azure.com/?feature.subclipper=true).

Na poniższej ilustracji przedstawiono stronę docelową programu Clipper na koncie usługi Media Services: ![Azure Media Clipper w Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Tworzenie klipów
Aby utworzyć klip, przeciągnij i upuść element zawartości na interfejs klipu. Jeśli znaki znacznika są znane, możesz je wprowadzić ręcznie w interfejsie. W przeciwnym razie Odtwórz element zawartości lub przeciągnij głowicę odtwarzania, aby znaleźć żądany czas znacznika i znacznika. Jeśli nie podano czasu znacznika lub znacznika, klip rozpocznie się od początku lub kontynuuje na końcu wejściowego elementu zawartości.

Aby poruszać się z dokładnością do ramki/grupę GOP, użyj przycisków Frame-Forward/grupę GOP-forward lub Frame-tył/grupę GOP-tył. W przypadku wycinków dla wielu zasobów przeciągnij i upuść wiele zasobów do interfejsu klipu z panelu wybór elementu zawartości. Można wybrać i zmienić kolejność zasobów w interfejsie na żądaną kolejność. Panel wybór zasobów zawiera informacje o czasie trwania zasobów, typie i metadanych rozwiązania dla każdego elementu zawartości. Łącząc jednocześnie wiele zasobów, należy wziąć pod uwagę rozdzielczość źródłową poszczególnych plików wejściowych. Jeśli rozdzielczość źródła różni się, dane wejściowe niższej rozdzielczości są skalowane w celu spełnienia rozdzielczości najwyższej wartości elementu zawartości. Aby wyświetlić podgląd danych wyjściowych zadania przycinania, wybierz przycisk podglądu, a klip jest odtwarzany z wybranych godzin znacznika.

## <a name="producing-dynamic-manifest-filters"></a>Generowanie filtrów manifestu dynamicznego
[Filtry manifestu dynamicznego](https://azure.microsoft.com/blog/dynamic-manifest/) opisują zestaw reguł na podstawie atrybutów manifestu i osi czasu zasobów. Te reguły określają, jak punkt końcowy przesyłania strumieniowego manipuluje wyjściową listą odtwarzania (manifest). Filtr może służyć do zmiany, które segmenty są przesyłane strumieniowo na potrzeby odtwarzania. Filtry utworzone przez Clipper są filtrami lokalnymi i są specyficzne dla zasobu źródłowego. W przeciwieństwie do renderowanych klipów, filtry nie są nowymi zasobami i nie wymagają zadania kodowania do produkcji. Można je szybko utworzyć za pośrednictwem [zestawu .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) lub [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), jednak są one tylko dokładne grupę GOP. Typowo, zasoby kodowane dla przesyłania strumieniowego mają rozmiar grupę GOP dwóch sekund.

Aby utworzyć filtr manifestu dynamicznego, przejdź do karty **składniki** i wybierz żądany element zawartości. Wybierz przycisk **podklipu** dla górnego menu. Wybierz opcję Filtr manifestu dynamicznego jako tryb przycinania z menu Ustawienia zaawansowane. Następnie można wykonać ten sam proces, aby utworzyć renderowany klip do utworzenia filtru. Filtry można wytwarzać tylko z jednego elementu zawartości.

Na poniższej ilustracji przedstawiono tworzenie programu Clipper w trybie dynamicznego filtrowania manifestu w Azure Portal: ![Azure Media Clipper w trybie dynamicznego filtrowania manifestu w Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Przesyłanie zadań przycinania
Po zakończeniu redagowania klipu wybierz przycisk Prześlij zadanie w celu zainicjowania odpowiedniego zadania przycinania lub dynamicznego wywołania manifestu.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z usługi Azure Media Clipper, przeczytaj artykuł [wprowadzenie](media-services-azure-media-clipper-getting-started.md) , aby uzyskać szczegółowe informacje na temat sposobu wdrażania widżetu.
