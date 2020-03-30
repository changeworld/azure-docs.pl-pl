---
title: Informacje o środowiskach usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: W tym artykule poznaj zalety środowisk uczenia maszynowego, które umożliwiają odtwarzalne, kontrolowane i przenośne definicje zależności uczenia maszynowego w różnych celach obliczeniowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064193"
---
# <a name="what-are-azure-machine-learning-environments"></a>Co to są środowiska usługi Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Środowiska usługi Azure Machine Learning określają pakiety języka Python, zmienne środowiskowe i ustawienia oprogramowania wokół skryptów szkoleniowych i oceniania. Określają one również czasy wykonywania (Python, Spark lub Docker). Środowiska są zarządzane i wersjonowane jednostek w obszarze roboczym uczenia maszynowego, które umożliwiają odtwarzalne, inspekcji i przenośnych procesów pracy uczenia maszynowego w różnych obiektów docelowych obliczeń.

Obiektu można `Environment` użyć na komputerze lokalnym, aby:
* Opracowanie skryptu szkoleniowego.
* Ponownie użyć tego samego środowiska na platformie Azure Machine Learning Compute do szkolenia modelu na dużą skalę.
* Wdrażanie modelu w tym samym środowisku.

Na poniższym diagramie przedstawiono, `Environment` jak można użyć pojedynczego obiektu w konfiguracji uruchamiania, do szkolenia i konfiguracji wnioskowania i wdrażania dla wdrożeń usługi sieci web.

![Diagram środowiska w przepływie pracy uczenia maszynowego](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Rodzaje środowisk

Środowiska można zasadniczo podzielić na trzy kategorie: *wyselekcjonowany,* *zarządzany przez użytkownika*i zarządzany przez *system.*

Wyselekcjonowane środowiska są dostarczane przez usługę Azure Machine Learning i są domyślnie dostępne w obszarze roboczym. Zawierają one kolekcje pakietów i ustawień języka Python, które ułatwiają rozpoczęcie pracy z różnymi strukturami uczenia maszynowego. 

W środowiskach zarządzanych przez użytkownika użytkownik jest odpowiedzialny za konfigurowanie środowiska i instalowanie każdego pakietu, który skrypt szkolenia potrzebuje na cel obliczeniowy. Conda nie sprawdza środowiska ani nie instaluje niczego za Ciebie. Jeśli definiujesz własne środowisko, musisz wyświetlić `azureml-defaults` `>= 1.0.45` listę z wersją jako zależność pipsa. Ten pakiet zawiera funkcje, które są potrzebne do obsługi modelu jako usługi sieci web.

Środowiska zarządzane przez system są używane, gdy [Conda](https://conda.io/docs/) ma zarządzać środowiskiem języka Python i zależnościami skryptów. Usługa przyjmuje tego typu środowiska domyślnie, ze względu na jego przydatność na zdalnych celów obliczeniowych, które nie są ręcznie konfigurowalne.

## <a name="create-and-manage-environments"></a>Tworzenie środowisk i zarządzanie nimi

Środowiska można tworzyć, korzystając z:

* Definiowanie `Environment` nowych obiektów za pomocą wyselekcjonowanego środowiska lub przez definiowanie własnych zależności.
* Korzystanie `Environment` z istniejących obiektów z obszaru roboczego. Takie podejście umożliwia spójność i odtwarzalność z zależnościami.
* Importowanie z istniejącej definicji środowiska Anaconda.
* Korzystanie z interfejsu wiersza polecenia usługi Azure Machine Learning

Aby uzyskać określone przykłady kodu, zobacz sekcję "Tworzenie środowiska" [w środowiskach ponownego użycia do szkolenia i wdrażania](how-to-use-environments.md#create-an-environment). Środowiska można również łatwo zarządzać za pośrednictwem obszaru roboczego. Obejmują one następujące funkcje:

* Środowiska są automatycznie rejestrowane w obszarze roboczym podczas przesyłania eksperymentu. Można je również zarejestrować ręcznie.
* Środowiska można pobrać z obszaru roboczego do użycia w celu szkolenia lub wdrożenia lub wprowadzić zmiany w definicji środowiska.
* Dzięki wersjowaniu można zobaczyć zmiany w środowiskach w czasie, co zapewnia odtwarzalność.
* Obrazy platformy Docker można tworzyć automatycznie ze środowisk.

Aby zapoznać się z przykładami kodu, zobacz sekcję "Zarządzanie środowiskami" [w środowiskach ponownego użycia do szkolenia i wdrażania](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Budowanie, buforowanie i ponowne wykorzystanie środowiska

Usługa Azure Machine Learning tworzy definicje środowiska w obrazach platformy Docker i środowiskach conda. Buforuje również środowiska, dzięki czemu mogą być ponownie używane w kolejnych przebiegach szkoleniowych i wdrożeniach punktów końcowych usługi.

### <a name="building-environments-as-docker-images"></a>Tworzenie środowisk jako obrazów platformy Docker

Zazwyczaj podczas pierwszego przesyłania uruchomienia przy użyciu środowiska usługa Azure Machine Learning wywołuje [zadanie kompilacji usługi ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) w rejestrze kontenerów platformy Azure (ACR) skojarzone z obszarem roboczym. Wbudowany obraz platformy Docker jest następnie buforowany w programie ACR obszaru roboczego. Na początku wykonywania uruchomienia obraz jest pobierany przez obiekt docelowy obliczeń.

Kompilacja obrazu składa się z dwóch kroków:

 1. Pobieranie obrazu podstawowego i wykonywanie dowolnych kroków platformy Docker
 2. Tworzenie środowiska conda zgodnie z zależnościami conda określonymi w definicji środowiska.

Drugi krok jest pomijany, jeśli określisz [zależności zarządzane przez użytkownika](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). W takim przypadku jesteś odpowiedzialny za zainstalowanie dowolnych pakietów Języka Python, dołączając je do obrazu podstawowego lub określając niestandardowe kroki platformy Docker w pierwszym kroku. Użytkownik jest również odpowiedzialny za określenie poprawnej lokalizacji pliku wykonywalnego języka Python.

### <a name="image-caching-and-reuse"></a>Buforowanie i ponowne wykorzystanie obrazu

Jeśli używasz tej samej definicji środowiska dla innego uruchomienia, usługa Azure Machine Learning ponownie używa buforowanego obrazu z usługi ACR obszaru roboczego. 

Aby wyświetlić szczegóły obrazu w pamięci podręcznej, należy użyć [environment.get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) metody.

Aby ustalić, czy ponownie użyć obrazu w pamięci podręcznej lub utworzyć nowy, usługa oblicza [wartość mieszania](https://en.wikipedia.org/wiki/Hash_table) z definicji środowiska i porównuje go do skrótów istniejących środowisk. Skrót jest oparty na:
 
 * Podstawowa wartość właściwości obrazu
 * Niestandardowa wartość właściwości dokceny
 * Lista pakietów Języka Python w definicji Conda
 * Lista pakietów w definicji platformy Spark 

Skrót nie zależy od nazwy środowiska lub wersji. Zmiany definicji środowiska, takie jak dodawanie lub usuwanie pakietu Python lub zmiana wersji pakietu, powoduje zmianę wartości skrótu i wyzwala przebudowę obrazu. Jeśli jednak po prostu zmienić nazwę środowiska lub utworzyć nowe środowisko z dokładnymi właściwościami i pakietami istniejącego, wartość mieszania pozostanie taka sama i używany jest buforowany obraz.

Zobacz poniższy diagram, który zawiera trzy definicje środowiska. Dwa z nich mają inną nazwę i wersję, ale identyczny obraz podstawowy i pakiety Pythona. Mają ten sam skrót i dlatego odpowiadają temu samemu obrazowi buforowanemu. Trzecie środowisko ma różne pakiety i wersje języka Python i dlatego odpowiada innemu obrazowi w pamięci podręcznej.

![Diagram buforowania środowiska jako obrazów platformy Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Jeśli utworzysz środowisko z nieprzypiętą zależnością pakietu, na przykład, ```numpy```to środowisko będzie nadal używać wersji pakietu _zainstalowanej w momencie tworzenia środowiska_. Ponadto każde przyszłe środowisko z dopasowującą definicją będzie nadal używać starej wersji. 

Aby zaktualizować pakiet, należy określić numer wersji, ```numpy==1.18.1```aby wymusić przebudowę obrazu, na przykład . Należy zauważyć, że nowe zależności, w tym zagnieżdżone zostaną zainstalowane, które mogą przerwać wcześniej działający scenariusz.

> [!WARNING]
>  [Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) Metoda będzie odbudować buforowany obraz, z możliwością efekt uboczny aktualizacji nieprzypiętych pakietów i łamanie odtwarzalności dla wszystkich definicji środowiska odpowiadających tego obrazu buforowanego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [tworzyć i używać środowisk](how-to-use-environments.md) w usłudze Azure Machine Learning.
* Zobacz dokumentację referencyjną SDK języka Python dla [klasy środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Zobacz dokumentację referencyjną SDK R dla [środowisk](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
