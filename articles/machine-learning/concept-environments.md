---
title: Informacje o środowiskach Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ten artykuł zawiera informacje o zaletach środowisk uczenia maszynowego, które umożliwiają powtarzalnych, poddawanych inspekcji i przenośnych definicji zależności Machine Learning w różnych obiektach docelowych obliczeń.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045848"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co to są środowiska Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Środowiska Azure Machine Learning określają pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleń i oceniania. Określają one również czasy wykonywania (Python, Spark lub Docker). Są one zarządzane i obsługiwane przez program w obszarze roboczym Machine Learning, które umożliwiają powtarzalny, z inspekcją i przenośne przepływy pracy uczenia maszynowego w różnych obiektach docelowych obliczeń.

Możesz użyć obiektu `Environment` w lokalnym obliczeniu, aby:
* Opracowywanie skryptu szkoleniowego.
* Ponownie Użyj tego samego środowiska na Azure Machine Learning COMPUTE na potrzeby szkolenia modeli na dużą skalę.
* Wdróż model przy użyciu tego samego środowiska.

Na poniższym diagramie pokazano, w jaki sposób można użyć pojedynczego obiektu `Environment` w konfiguracji przebiegu, na potrzeby szkoleń oraz konfiguracji wnioskowania i wdrożenia w przypadku wdrożeń usług sieci Web.

![Diagram środowiska w przepływie pracy uczenia maszynowego](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Typy środowiska

Środowiska mogą być szeroko podzielone na trzy kategorie: *nadzorowane*, *zarządzane przez użytkownika*i *zarządzane przez system*.

Środowiska nadzorowane są udostępniane przez Azure Machine Learning i są domyślnie dostępne w obszarze roboczym. Zawierają one kolekcje pakietów i ustawień języka Python, które ułatwiają rozpoczęcie pracy z różnymi platformami uczenia maszynowego. 

W środowiskach zarządzanych przez użytkownika użytkownik jest odpowiedzialny za skonfigurowanie środowiska i zainstalowanie każdego pakietu, którego potrzebuje skrypt szkoleniowy w celu obliczenia. Conda nie sprawdza Twojego środowiska ani nie instaluje żadnych elementów. W przypadku definiowania własnego środowiska należy wyświetlić listę `azureml-defaults` z wersją `>= 1.0.45` jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web.

Środowiska zarządzane przez system są używane, gdy chcesz, aby [Conda](https://conda.io/docs/) zarządzać środowiskiem Python i zależnościami skryptów. Usługa domyślnie przyjmuje ten typ środowiska, z powodu jego użyteczności w przypadku zdalnych obiektów docelowych obliczeń, które nie są ręcznie konfigurowane.

## <a name="create-and-manage-environments"></a>Tworzenie środowisk i zarządzanie nimi

Środowiska można tworzyć według:

* Definiowanie nowych obiektów `Environment` przy użyciu nadzorowanego środowiska lub przez Definiowanie własnych zależności.
* Używanie istniejących obiektów `Environment` w obszarze roboczym. Takie podejście umożliwia spójność i odtwarzalność z zależnościami.
* Importowanie z istniejącej definicji środowiska Anaconda.
* Korzystanie z interfejsu wiersza polecenia Azure Machine Learning

Aby zapoznać się z konkretnymi przykładami kodu, zobacz sekcję "Tworzenie środowiska" w temacie [ponowne używanie środowisk do szkolenia i wdrażania](how-to-use-environments.md#create-an-environment). Środowiska są również łatwo zarządzane za poorednictwem obszaru roboczego. Obejmują one następujące funkcje:

* Środowiska są automatycznie rejestrowane w obszarze roboczym podczas przesyłania eksperymentu. Można je również zarejestrować ręcznie.
* Możesz pobrać środowiska z obszaru roboczego, aby użyć go do szkolenia lub wdrożenia lub wprowadzić zmiany w definicji środowiska.
* Dzięki wersji można zobaczyć zmiany w środowiskach w czasie, co zapewnia odtwarzalność.
* Możesz automatycznie tworzyć obrazy platformy Docker w swoich środowiskach.

Aby zapoznać się z przykładami kodu, zobacz sekcję "zarządzanie środowiskami" w temacie [ponowne używanie środowisk do szkolenia i wdrażania](how-to-use-environments.md#manage-environments).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak tworzyć środowiska i korzystać](how-to-use-environments.md) z nich w Azure Machine Learning.
* Zobacz dokumentację referencyjną zestawu SDK języka Python dla [klasy Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zobacz dokumentację referencyjną języka R SDK dla [środowisk](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
