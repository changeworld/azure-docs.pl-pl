---
title: Omówienie pojęć dotyczących usługi Azure Machine Learning w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Omówienie pojęć dotyczących funkcje w wersji zapoznawczej usługi Azure Machine Learning, np. subskrypcji, kont, obszarów roboczych, projekty itd.
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: f63b9c077e64b642adfd8c7eed5026563eb6319a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644985"
---
# <a name="azure-machine-learning---concepts"></a>Usługa Azure Machine Learning — pojęcia

W tym artykule definiuje i opisano pojęcia, które są potrzebne do używania usługi Azure Machine Learning. 

![Hierarchia pojęcia](media/overview-general-concepts/hierarchy.png)

- **Subskrypcja:** subskrypcji platformy Azure daje dostęp do zasobów na platformie Azure. Ponieważ usługi Azure Machine Learning jest ściśle zintegrowana z obliczeń, magazynowania oraz wiele innych zasobów platformy Azure i usług, aplikacji Workbench wymaga, że każdy użytkownik ma dostęp do ważnej subskrypcji platformy Azure. Użytkownicy muszą również mieć wystarczających uprawnień w ramach tej subskrypcji do utworzenia zasobów.


- **Konto eksperymentowania:** konta eksperymentowania jest zasobem platformy Azure wymaganych przez uczenie Maszynowe Azure i rozliczeń vehicle. Zawiera ona swoich obszarów roboczych, które z kolei zawierają projekty. Można dodawać wielu użytkowników, nazywane _stanowiska_, do konta eksperymentowania. Musi mieć dostęp do konta eksperymentowania, aby można było używać aplikacji Azure ML Workbench do uruchamiania eksperymentów. 


- **Konto zarządzania modelami** konta zarządzania modelami jest również zasobu platformy Azure wymaganych przez uczenie Maszynowe Azure do zarządzania modelami. Służy on do rejestrowania manifesty i modeli, tworzenie usług konteneryzowanych sieci web i wdrażać je lokalnie lub w chmurze. Jest to również inne pojazdu rozliczeniowego Azure ML.


- **Obszar roboczy:** obszar roboczy jest podstawowym składnikiem udostępniania i współpracy w usłudze Azure ML. Projekty są zgrupowane w obszarze roboczym. Obszar roboczy następnie mogą być udostępniane wielu użytkowników, które zostały dodane do konta eksperymentowania.


- **Projekt:** w usłudze Azure Machine Learning projekt jest kontenerem logicznym dla wszystkich zadań wykonywanych w celu rozwiązania problemu. Jest on mapowany na jeden folder plików na dysku lokalnym i można do niego dodać dowolne pliki lub podfoldery. Projekt może być opcjonalnie skojarzony z repozytorium Git do kontroli źródła i współpracy.  

- **Eksperymentu:** eksperymentu usługi uczenie Maszynowe Azure jest co najmniej jeden plik kodu źródłowy, które mogą być wykonywane z pojedynczego punktu wejścia. Może on zawierać zadania, takie jak pozyskiwanie danych, technicznego opracowywania funkcji, szkolenie modelu lub model oceny. Obecnie usługa uczenie Maszynowe Azure obsługuje język Python lub PySpark experiments tylko.


- **Model:** w usłudze Azure Machine Learning, modele odwoływać się do wynikiem eksperymentu uczenia maszynowego. Są one przepisy, gdy poprawnie zastosowane do danych, należy wygenerować przewidywane wartości. Modele mogą być wdrażane testowym lub produkcyjnym środowisku i używane do oceniania nowych danych. Raz w środowisku produkcyjnym, modeli może być monitorowane pod kątem dryfu dane dotyczące wydajności i i retrained zgodnie z wymaganiami. 

- **Obliczeniowych elementów docelowych:** obliczeniowego elementu docelowego jest zasobu obliczeniowego, skonfigurowanego do wykonywania eksperymentów. Można go z komputera lokalnego (Windows lub macOS), w kontenerze platformy Docker działającym na komputerze lokalnym lub na maszynie wirtualnej systemu Linux na platformie Azure lub klastra usługi HDInsight Spark.


- **Uruchom:** usługa eksperymentowanie w usłudze definiuje przebiegu, jak okres istnienia wykonywania eksperymentów w obliczeniowego elementu docelowego. Uczenie Maszynowe systemu Azure znajdują się informacje dotyczące każdego uruchomienia automatycznie i przedstawia całą historię określonego eksperymentu w formie historii uruchamiania.

- **Środowisko:** w usłudze Azure Machine Learning, środowisko oznacza określonego zasobów obliczeniowych, który służy do wdrażania modeli i zarządzania nimi. Może być komputer lokalny, Maszynę wirtualną systemu Linux na platformie Azure lub klastra Kubernetes uruchomionego w usłudze Azure Container Service, w zależności od kontekstu i konfiguracji. Model jest hostowana w kontenerze platformy Docker, działających w tych środowiskach i udostępniane jako punktu końcowego interfejsu API REST.


- **Model zarządzany:** Zarządzanie modelami umożliwia wdrażanie modeli jako usług sieci web, zarządzanie różnymi wersjami modeli oraz monitorowanie ich wydajności i metryki. Modele zarządzane są rejestrowane przy użyciu konta Zarządzanie modelami w usłudze Azure Machine Learning.

- **Manifesty:** system zarządzania modelami służy do wdrażania modelu w środowisku produkcyjnym, zawiera manifest, który może obejmować modelu, zależności, skrypt oceniania, przykładowe dane i schematu. Manifest jest przepisu, użyty do utworzenia obrazu kontenera platformy Docker. Za pomocą zarządzania modelami, możesz automatycznie wygenerować manifest, utworzyć różne wersje i zarządzać te manifesty. 


- **Obrazy:** manifesty można użyć do wygenerowania (i ponowne wygenerowanie) obrazy platformy Docker. Konteneryzowane obrazów platformy Docker Utwórz elastyczność, aby uruchomić je w chmurze, na komputerach lokalnych lub na urządzeniu IoT. Obrazy są niezależne i zawierają wszystkie zależności wymagane do oceniania nowe dane przy użyciu modeli. 

- **Services:** Zarządzanie modelami umożliwia wdrażanie modeli jako usług sieci web. Usługa sieci Web logiki i zależności są umieszczane na obrazie. Każda usługa sieci Web to zestaw kontenerów oparte na obraz wszystko gotowe do obsługi żądań dla danego adresu URL. Usługa internetowa jest liczona jako pojedyncze wdrożenie.
