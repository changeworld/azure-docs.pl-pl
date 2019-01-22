---
title: Co to jest usługa Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak za pomocą usługi Custom Vision kompilować klasyfikatory obrazów niestandardowych w chmurze platformy Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: d9b5473e87d83912dd2f33c00e983e346226e4aa
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54242951"
---
# <a name="what-is-azure-custom-vision"></a>Co to jest usługa Azure Custom Vision?

Interfejs API usługi Azure Custom Vision to usługa poznawcza umożliwiająca tworzenie, wdrażanie i ulepszanie klasyfikatorów obrazów niestandardowych. Klasyfikator obrazu to usługa sztucznej inteligencji, która sortuje obrazy w klasy (tagi) według określonych charakterystyk. W odróżnieniu od usługi [przetwarzania obrazów](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) usługa Custom Vision umożliwia tworzenie własnych klasyfikacji.

## <a name="what-it-does"></a>Wyniki działania

Usługa Custom Vision klasyfikuje obrazy przy użyciu algorytmu uczenia maszynowego. Jako deweloper musisz przesłać grupy obrazów, które przedstawiają określone klasyfikacje lub ich brak. Prawidłowe tagi obrazów określa się podczas przesyłania. Następnie algorytm uczy się na podstawie tych danych i oblicza swoją dokładność, wykonując na sobie test w oparciu o te same dane. Po zakończeniu uczenia model można przetestować, nauczyć ponownie lub ewentualnie użyć go do klasyfikowania nowych obrazów zgodnie z potrzebami aplikacji. Możesz także wyeksportować sam model do użytku w trybie offline.

### <a name="classification-and-object-detection"></a>Klasyfikacja i wykrywanie obiektów

Funkcje usługi Custom Vision można podzielić na dwie kategorie. **Klasyfikacja obrazów** przypisuje dystrybucję klasyfikacji do każdego obrazu. Obsługiwane są modele klasyfikacji wieloklasowej (jeden tag na obraz) i wieloetykietowej (dowolna liczba tagów na obraz). **Wykrywanie obiektów** jest podobne do klasyfikacji wieloetykietowej, ale zwraca również współrzędne na obrazie, w których można znaleźć zastosowane etykiety.

### <a name="optimization"></a>Optymalizacja

Ogólnie metody używane przez usługę Custom Vision są odporne na różnice, co umożliwia rozpoczęcie tworzenia prototypów z małą ilością danych. 50 obrazów na tag to zazwyczaj dobry początek. Oznacza to jednak, że usługa nie jest najlepszym wyborem do wykrywania subtelnych różnic między obrazami (na przykład wykrywanie niewielkich rys lub wgnieceń w scenariuszach związanych z zapewnianiem jakości.

Ponadto możesz wybrać spośród kilku różnych typów algorytmu usługi Custom Vision, które są zoptymalizowane pod kątem określonych materiałów, &mdash;na przykład elementy krajobrazu lub produkty sprzedaży detalicznej. Zobacz przewodnik [Tworzenie klasyfikatora](getting-started-build-a-classifier.md), aby uzyskać więcej informacji na ten temat.

## <a name="what-it-includes"></a>Co zawiera
Usługa Custom Vision jest dostępna jako zestaw natywnych zestawów SDK oraz za pośrednictwem interfejsu opartego na Internecie na [stronie głównej usługi Custom Vision](https://customvision.ai/). Możesz tworzyć, testować i uczyć model za pośrednictwem jednego z tych dwóch interfejsów lub obu jednocześnie.

![Strona główna usługi Custom Vision w oknie przeglądarki Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Custom Vision powinni znać zasady firmy Microsoft dotyczące danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z przewodnikiem [Tworzenie klasyfikatora](getting-started-build-a-classifier.md), aby rozpocząć korzystanie z usługi Custom Vision w Internecie, lub ukończ [Samouczek klasyfikacji obrazów](csharp-tutorial.md), aby zaimplementować ten scenariusz w kodzie.
