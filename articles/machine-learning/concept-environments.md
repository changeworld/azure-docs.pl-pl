---
title: Co to są środowiska ML
titleSuffix: Azure Machine Learning
description: W tym artykule poznasz zalety środowisk uczenia maszynowego, które umożliwiają odtwarzalność, możliwość przeprowadzenia inspekcji i przenośne definicje zależności między różnymi obiektami docelowymi obliczeniowymi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692734"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co to są środowiska Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Środowiska określają pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleniowych i oceniających oraz czasy uruchamiania (Python, Spark lub Docker). Są one zarządzane i obsługiwane przez program w obszarze roboczym Azure Machine Learning, które umożliwiają powtarzalny, z inspekcją i przenośne przepływy pracy uczenia maszynowego między różnymi obiektami docelowymi obliczeniowymi.

Możesz użyć obiektu środowiska na lokalnym obliczeniu do opracowania skryptu szkoleniowego i ponownie użyć tego samego środowiska na Azure Machine Learning obliczeń dla szkolenia modeli w dużej skali, a nawet wdrożyć model przy użyciu tego samego środowiska.

Poniżej przedstawiono, że ten sam obiekt środowiska może być używany zarówno w konfiguracji uruchamiania na potrzeby szkoleń, jak i w konfiguracji wdrożenia usługi sieci Web.

![Diagram środowiska w przepływie pracy uczenia maszynowego](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typy środowiska

Środowiska mogą być szeroko podzielone na trzy kategorie: **nadzorowane**, **zarządzane przez użytkownika** i **zarządzane przez system**.

Środowiska nadzorowane są udostępniane przez Azure Machine Learning i są domyślnie dostępne w obszarze roboczym. Zawierają one kolekcje pakietów i ustawień języka Python, które ułatwiają rozpoczęcie pracy z różnymi platformami uczenia maszynowego. 

W przypadku środowiska zarządzanego przez użytkownika użytkownik jest odpowiedzialny za skonfigurowanie środowiska i zainstalowanie każdego pakietu potrzebnych do wykonywania skryptów szkoleniowych w miejscu docelowym obliczeń. Conda nie sprawdzi Twojego środowiska ani nie zainstaluje żadnego z nich. Należy pamiętać, że w przypadku definiowania własnego środowiska należy wyświetlić listę `azureml-defaults` z wersją `>= 1.0.45` jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web.

Środowiska zarządzane przez system są używane, gdy chcesz [Conda](https://conda.io/docs/) zarządzać środowiskiem Python i zależnościami skryptów. Usługa domyślnie przyjmuje ten typ środowiska, ze względu na jego użyteczność w przypadku zdalnych obiektów docelowych obliczeń, które nie są ręcznie konfigurowane.

## <a name="creating-and-managing-environments"></a>Tworzenie środowisk i zarządzanie nimi

Środowiska mogą być tworzone przez:

* Definiowanie nowych obiektów `Environment` przy użyciu nadzorowanego środowiska lub przez Definiowanie własnych zależności
* Używanie istniejących obiektów `Environment` w obszarze roboczym. Pozwala to na spójność i odtwarzalność z zależnościami
* Importowanie z istniejącej definicji środowiska Anaconda.

Zobacz [instrukcje](how-to-use-environments.md#create-an-environment) dla konkretnych przykładów kodu. Środowiska są również łatwo zarządzane za pomocą obszaru roboczego i obejmują następujące funkcje:

* Środowiska są automatycznie rejestrowane w obszarze roboczym podczas przesyłania eksperymentu. Można je również zarejestrować ręcznie
* Pobieraj środowiska z obszaru roboczego i używaj ich do szkoleń, wdrażania lub wprowadzania zmian w definicji środowiska
* Przechowywanie wersji umożliwia wyświetlanie zmian w środowiskach w czasie i zapewnia odtwarzalność
* Automatyczne Kompilowanie obrazów platformy Docker ze środowisk

Zobacz sekcję [Zarządzanie środowiskami](how-to-use-environments.md#manage-environments) przykładów kodu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak tworzyć środowiska i korzystać](how-to-use-environments.md) z nich w Azure Machine Learning
* Zobacz dokumenty referencyjne zestawu SDK języka Python dla [klasy Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zobacz dokumenty referencyjne zestawu SDK języka R dla [środowisk](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).