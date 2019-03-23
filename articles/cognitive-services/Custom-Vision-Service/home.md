---
title: Co to jest usługa Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą usługi Custom Vision kompilować klasyfikatory obrazów niestandardowych w chmurze platformy Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 50935aca20af931eec63717921ef7a73267d2373
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350474"
---
# <a name="what-is-azure-custom-vision"></a>Co to jest usługa Azure Custom Vision?

Usługa Azure Custom Vision to usługa poznawcza umożliwiająca tworzenie, wdrażanie i ulepszanie własnych klasyfikatorów obrazów. Klasyfikator obrazów to usługa sztucznej Inteligencji, która stosuje etykiety (reprezentujące _klasy_) do obrazów zgodnie z ich cechami wizualnymi. W odróżnieniu od usługi [przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) usługa Custom Vision umożliwia określenie etykiet do stosowania.

## <a name="what-it-does"></a>Wyniki działania

Usługa Custom Vision stosuje etykiety do obrazów przy użyciu algorytmu uczenia maszynowego. Jako deweloper musisz przesłać grupy obrazów, które zawierają określone cechy lub ich nie posiadają. Etykiety obrazów należy określić samodzielnie podczas przesyłania. Następnie algorytm uczy się na podstawie tych danych i oblicza swoją dokładność, wykonując na sobie test w oparciu o te same obrazy. Po zakończeniu trenowania algorytmu można go przetestować, nauczyć ponownie lub ewentualnie użyć do klasyfikowania nowych obrazów zgodnie z potrzebami aplikacji. Możesz także wyeksportować sam model do użytku w trybie offline.

### <a name="classification-and-object-detection"></a>Klasyfikacja i wykrywanie obiektów

Funkcje usługi Custom Vision można podzielić na dwie kategorie. **Klasyfikacja obrazów** stosuje jedną lub więcej etykiet do obrazu. **Wykrywanie obiektów** jest podobne, ale zwraca także współrzędne na obrazie, których dotyczą zastosowane etykiety.

### <a name="optimization"></a>Optymalizacja

Usługa Custom Vision jest zoptymalizowana pod kątem szybkiego rozpoznawania znacznych różnic między obrazami. Umożliwia to rozpoczęcie prototypowania modelu przy użyciu małej ilości danych. 50 obrazów na etykietę to zazwyczaj dobry początek. Oznacza to jednak, że usługa nie jest najlepszym wyborem do wykrywania subtelnych różnic między obrazami (na przykład wykrywanie niewielkich rys lub wgnieceń w scenariuszach związanych z zapewnianiem jakości.

Ponadto możesz wybrać spośród kilku różnych typów algorytmu usługi Custom Vision, które są zoptymalizowane pod kątem obrazów z określonymi materiałami &mdash;, na przykład elementami krajobrazu lub produktami sprzedaży detalicznej. Zobacz przewodnik [Tworzenie klasyfikatora](getting-started-build-a-classifier.md), aby uzyskać więcej informacji na ten temat.

## <a name="what-it-includes"></a>Co zawiera

Usługa Custom Vision jest dostępna jako zestaw natywnych zestawów SDK oraz za pośrednictwem interfejsu opartego na Internecie na [stronie głównej usługi Custom Vision](https://customvision.ai/). Możesz tworzyć, testować i trenować model za pośrednictwem jednego z tych dwóch interfejsów lub obu jednocześnie.

![Strona główna usługi Custom Vision w oknie przeglądarki Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Custom Vision powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z przewodnikiem [Tworzenie klasyfikatora](getting-started-build-a-classifier.md), aby rozpocząć korzystanie z usługi Custom Vision w Internecie, lub ukończ [Samouczek klasyfikacji obrazów](csharp-tutorial.md), aby zaimplementować podstawowy scenariusz w kodzie.
