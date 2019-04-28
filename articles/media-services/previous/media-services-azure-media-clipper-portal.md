---
title: Użyj usługi Azure Media Clipper w portalu | Dokumentacja firmy Microsoft
description: Twórz klipy przy użyciu usługi Azure Media Clipper z witryny Azure Portal
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8c88caefb0909da55de87116a23fa520c1679cc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465851"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Twórz klipy z usługi Azure Media Clipper w portalu  

Usługi Azure Media Clipper w portalu umożliwia Twórz klipy z zasobów na kontach usługi media services. Aby rozpocząć, przejdź do konta usługi media services w portalu. Następnie wybierz pozycję **klipu podrzędnego** kartę.

Na **klipu podrzędnego** karty, możesz rozpocząć tworzenie klipów. W portalu Clipper ładuje pliki mp4 o pojedynczej szybkości transmisji bitów, pliki mp4 o różnej szybkości transmisji bitów i archiwa na żywo, które są publikowane za pomocą nieprawidłowy Lokalizator przesyłania strumieniowego. Cofnięto publikowanie zasobów nie są ładowane.

Clipper jest obecnie dostępna w publicznej wersji zapoznawczej. Aby uzyskać dostęp Clipper w witrynie Azure portal, przejdź do tej [strony publicznej wersji zapoznawczej](https://portal.azure.com/?feature.subclipper=true).

Na poniższym obrazie przedstawiono stroną docelową Clipper w konto usługi media services: ![Usługa Azure Media Clipper w witrynie Azure portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Tworzenie klipów
Do utworzenia klipu, przeciągnij i upuść element zawartości na interfejsie klipu. Jeśli znacznik i godziny są znane, możesz ręcznie wprowadzić je w interfejsie. W przeciwnym razie, odtwarzania elementu zawartości lub przeciągnij wskaźnik odtwarzania, aby znajdź żądany znak do i czas znacznika. Jeśli nie zostanie podany czas w znaku lub znak w poziomie, klip rozpoczyna się od początku lub kontynuuje do końca danych wejściowych zasobu, odpowiednio.

Aby przejść z ramki dokładność/GOP-dokładności, przyciski GOP następny, ramki do przodu w/lub ramki — ze starszymi wersjami/GOP — do tyłu. Dla wycinka względem wielu zasobów, przeciągnij i upuść wiele zasobów w interfejsie klipu z panelu wyboru zasobów. Można wybrać i zmienić kolejność zasoby w interfejsie żądanej kolejności. Panel wyboru zasobów zawiera czas trwania elementu zawartości, typ i rozpoznawanie metadanych dla każdego zasobu. Podczas łączenia ze sobą wiele zasobów, należy wziąć pod uwagę rozpoznanie źródła każdego pliku wejściowego. Jeśli są różne rozwiązania źródła, niższych rozdzielczości danych wejściowych jest upscaled do spełnienia rozwiązanie najwyższego elementu rozpoznawania zawartości. Aby wyświetlić podgląd danych wyjściowych zadania wycinka elementu, wybierz przycisk podglądu i odtwarza klip z razy wybranego zapytania.

## <a name="producing-dynamic-manifest-filters"></a>Tworzenie filtry manifestów dynamicznych
[Filtry manifestów dynamicznych](https://azure.microsoft.com/blog/dynamic-manifest/) opisują zestaw reguł na podstawie atrybutów manifestu i zawartości osi czasu. Reguły te określają, jak punkt końcowy przesyłania strumieniowego obsługuje listy odtwarzania danych wyjściowych (manifest). Filtr może służyć do zmiany, które segmenty są przesyłane strumieniowo do odtwarzania. Filtry produkowane przez Clipper znajdują się filtry lokalnych i są specyficzne dla zawartości źródłowej. W odróżnieniu od renderowanych klipów filtry nie są nowe zasoby i nie wymagają zadania kodowania do produkcji. Może być szybko tworzone przez [zestawu .NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) lub [interfejsu API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), jednak są one tylko GOP dokładne. Zazwyczaj zasoby zakodowany na potrzeby przesyłania strumieniowego mają rozmiar GOP dwóch sekund.

Aby utworzyć filtr manifestów dynamicznych, przejdź do **zasoby** kartę, a następnie wybierz żądany zasób. Wybierz **klipu podrzędnego** przycisk menu u góry. Wybierz filtr manifestów dynamicznych, jako tryb przycinania w menu Ustawienia zaawansowane. Można następnie postępuj zgodnie z tym samym procesie do tworzenia renderowanych klipów do utworzenia filtru. Filtry można tworzyć tylko z jednego zasobu.

Na poniższym obrazie przedstawiono Clipper w trybie dynamicznym manifestu filtru w witrynie Azure portal: ![Usługa Azure Media Clipper w trybie dynamicznym manifestu filtru w witrynie Azure portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Przesyłanie zadań przycinania
Po zakończeniu tworzenia klip przycisk Prześlij zadanie dla inicjowania odpowiedniego wycinka zadania lub wywołania dynamicznego manifestu.

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć korzystanie z usługi Azure Media Clipper przeczytaj [wprowadzenie](media-services-azure-media-clipper-getting-started.md) artykuł, aby uzyskać szczegółowe informacje na temat wdrażania elementu widget.
