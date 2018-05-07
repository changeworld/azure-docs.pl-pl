---
title: "Użyj w portalu Azure Media funkcji tworzenia wycinków | Dokumentacja firmy Microsoft"
description: "Utwórz klipy przy użyciu usługi Azure Media funkcji tworzenia wycinków w portalu Azure"
services: media-services
keywords: "klip subclip; kodowanie; nośnika"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1deca68cd6a61ede7536c4d5544036a10c54209b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Utwórz klipy z usługi Azure Media funkcji tworzenia wycinków w portalu
Azure Media funkcji tworzenia wycinków w portalu służy do tworzenia klipy z zasobów kont usługi multimediów. Aby rozpocząć, przejdź do Twojego konta usługi media services w portalu. Następnie wybierz pozycję **Subclip** kartę.

Na **Subclip** karcie, możesz rozpocząć tworzenie klipów. W portalu funkcji tworzenia wycinków ładuje MP4s pojedynczej szybkości transmisji bitów, MP4s wielokrotnej szybkości transmisji bitów i archiwa na żywo, które są publikowane z prawidłową Lokalizator przesyłania strumieniowego. Nie załadowano nieopublikowane zasoby.

Funkcji tworzenia wycinków jest obecnie w wersji zapoznawczej. Aby uzyskać dostęp do funkcji tworzenia wycinków w portalu Azure, przejdź do tego [strony publicznej wersji zapoznawczej](https://portal.azure.com/?feature.subclipper=true).

Na poniższym obrazie przedstawiono strony docelowej funkcji tworzenia wycinków w Twojego konta usługi media services: ![Azure Media funkcji tworzenia wycinków w portalu Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Tworzenie klipów
Aby utworzyć klip, należy przeciągnij i upuść zasobów na interfejsie klip. Jeśli znacznik czasu są znane, należy ręcznie wprowadzić je w interfejsie. W przeciwnym razie wartość odtwarzania zasobu lub przeciągnij głowica Znajdź z żądaną znaku w i czas znacznika. Jeśli czas znacznika lub znacznik końcowy nie zostanie podany, klip rozpoczyna się od początku lub w dalszym ciągu koniec zasobów wejściowych, odpowiednio.

Do nawigacji z ramki dokładność/GOP-dokładność, użyj przycisków ramki do przodu/GOP-następny lub ramki wstecz/GOP — do tyłu. Dla wycinka przed wiele zasobów, należy przeciągnąć i upuścić wiele zasobów w interfejsie klip z panelu wyboru zasobów. Można wybrać i zmienić kolejność zasoby w interfejsie w wymaganej kolejności. Panel wyboru zasobów zawiera czas trwania zasobów, typ i rozpoznawania metadanych dla każdego zasobu. Podczas łączenia ze sobą wiele zasobów, należy wziąć pod uwagę rozpoznanie źródła każdego pliku wejściowego. Jeśli rozwiązania źródła są różne, dolnej wprowadzania rozpoznawania jest upscaled do spełnienia rozpoznanie najwyższy zasobów rozpoznawania. Aby wyświetlić dane wyjściowe zadania wycinka, wybierz przycisk i odtwarza klip z razy wybranego znaku.

## <a name="producing-dynamic-manifest-filters"></a>Tworzenie filtrów dynamicznych manifestu
[Filtrów dynamicznych manifestu](https://azure.microsoft.com/blog/dynamic-manifest/) opisano zestaw reguł na podstawie atrybutów manifestu i zasobów osi czasu. Reguły te określają, jak punkt końcowy przesyłania strumieniowego manipuluje odtwarzania danych wyjściowych (manifest). Filtr można zmienić segmentów, które są przesyłane strumieniowo do odtwarzania. Filtry utworzonego przez funkcji tworzenia wycinków filtrów lokalnego i są specyficzne dla zawartości źródłowej. W odróżnieniu od renderowanego klipy filtry nie są nowe zasoby i nie wymagają zadania kodowania w celu utworzenia. Można można szybko utworzyć za pomocą [zestawu .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) lub [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), jednak są tylko GOP-prawidłowe. Zazwyczaj zasoby zakodowanego do przesyłania strumieniowego mieć rozmiar GOP dwie sekundy.

Aby utworzyć filtr dynamiczny manifestu, przejdź do **zasoby** i wybierz odpowiednią zasobów. Wybierz **Subclip** przycisk menu u góry. Wybierz filtr dynamiczny manifestu jako tryb przycinania w menu Ustawienia zaawansowane. Można następnie wykonaj te same czynności wygenerowało klipem renderowanych do tworzenia filtru. Filtry można dostarczać tylko z pojedynczego zasobu.

Na poniższym obrazie przedstawiono funkcji tworzenia wycinków w trybie dynamicznym manifestu filtru w portalu Azure: ![Azure Media funkcji tworzenia wycinków w trybie dynamicznym manifestu filtru w portalu Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Przesyłanie zadań wycinka
Po zakończeniu tworzenia klip przycisk przesyłania zadania dla inicjowania odpowiedniego zadania wycinka lub wywołania dynamicznego manifestu.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z funkcji tworzenia wycinków multimediów Azure, przeczytaj [wprowadzenie](media-services-azure-media-clipper-getting-started.md) artykułu, aby uzyskać więcej informacji na temat wdrażania elementu widget.