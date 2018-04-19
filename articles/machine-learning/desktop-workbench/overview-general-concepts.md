---
title: "Omówienie funkcji w wersji zapoznawczej usługi Azure Machine Learning | Dokumentacja firmy Microsoft"
description: "Omówienie funkcji w wersji zapoznawczej usługi Azure Machine Learning, takich jak subskrypcje, kont, obszarów roboczych, projekty itd."
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning---concepts"></a>Uczenie maszynowe Azure — pojęcia

W tym artykule definiuje oraz opisano pojęcia, które trzeba znać do użycia usługi Azure Machine Learning. 

![Hierarchia pojęcia](media/overview-general-concepts/hierarchy.png)

- **Subskrypcja:** subskrypcji platformy Azure przydziela dostęp do zasobów na platformie Azure. Ponieważ usługi Azure Machine Learning jest ściśle zintegrowana z obliczeń, magazynu i wielu innych zasobów platformy Azure i usług, Workbench wymaga, że każdy użytkownik ma dostęp do ważnej subskrypcji platformy Azure. Użytkownicy muszą mieć także wystarczających uprawnień w ramach danej subskrypcji do utworzenia zasobów.


- **Konto eksperymenty:** eksperymenty konto jest zasobem platformy Azure wymagane przez uczenie Maszynowe Azure i rozliczeń pojazdów. Zawiera ona obszarów roboczych, które z kolei zawierają projekty. Można dodać wielu użytkowników, określany jako _licencje na stanowiska_, do konta eksperymenty. Aby można było używać usługi Azure ML Workbench do uruchomienia eksperymenty musi mieć dostęp do konta eksperymenty. 


- **Model konto zarządzania** konto zarządzania modelu jest również zasobem platformy Azure wymagane przez uczenie Maszynowe Azure do zarządzania modeli. Służy on do rejestracji modeli i manifestów, tworzenie usług sieci web konteneryzowanych i wdrażać je lokalnie lub w chmurze. Możliwe jest również inne rozliczeń mechanizm uczenie Maszynowe Azure.


- **Obszar roboczy:** obszaru roboczego jest podstawowym składnikiem do udostępniania i współpracy w uczenie Maszynowe Azure. Projekty są pogrupowane w obszarze roboczym. Obszar roboczy można następnie udostępniać wielu użytkowników, które zostały dodane do konta eksperymenty.


- **Projekt:** w usłudze Azure Machine Learning projektu jest kontenerem logicznym dla wszystkich wykonywanych zadań i rozwiązać problem. Mapuje jeden folder na dysku lokalnym, a można dodać żadnych plików lub sub folderów do niego. Projekt opcjonalnie może być skojarzony z repozytorium Git do kontroli źródła i współpracy.  

- **Eksperymentu:** Azure ml eksperymentu jest jeden lub więcej plików kodu źródłowego, wykonanych z jeden punkt wejścia. Może on zawierać zadań, takich jak wprowadzanie danych, engineering funkcji, szkolenia modelu lub modelu oceny. Obecnie uczenie Maszynowe Azure obsługuje Python lub PySpark tylko eksperymentów.


- **Model:** w usłudze Azure Machine Learning modeli odnosi się do produktu eksperymentu uczenia maszynowego. Są one przepisami która poprawnie zastosowane do danych, wygenerowane przewidywane wartości. Modele można wdrożyć testowym lub produkcyjnym środowisk i używane do oceniania nowych danych. Raz w środowisku produkcyjnym, modeli może być monitorowane pod kątem odejście danych dotyczących wydajności i i retrained zgodnie z wymaganiami. 

- **Cel obliczeniowe:** cel obliczeniowego jest zasób obliczeniowy skonfigurowanego do wykonywania eksperymentów. Można go z komputera lokalnego (Windows lub macOS), w kontenerze Docker działającym na komputerze lokalnym lub maszynie wirtualnej systemu Linux na platformie Azure lub klastra Spark w usłudze HDInsight.


- **Instalacja:** usługi eksperymenty definiuje Uruchom jako przez czas ich istnienia wykonywania eksperymentów w celu obliczeń. Uczenie Maszynowe Azure automatycznie przechwytuje informacje dotyczące każdego przebiegu i przedstawia całą historię danego doświadczenia w formularzu Historia uruchomień.

- **Środowisko:** w usłudze Azure Machine Learning środowisku oznacza określonego zasobów obliczeniowych, używany do wdrażania i zarządzania nimi modeli. Może być komputer lokalny, Maszynę wirtualną systemu Linux na platformie Azure lub klastrze Kubernetes uruchomionych w usłudze kontenera platformy Azure, w zależności od kontekstu i konfiguracji. Model jest obsługiwana w kontenerze Docker działającym w takich środowiskach i widoczne jako punkt końcowy interfejsu API REST.


- **Model zarządzanego:** Model zarządzania umożliwia wdrażanie modeli jako usługi sieci web, zarządzanie różnymi wersjami modeli i monitorowanie ich wydajności i metryki. Modele zarządzane są rejestrowane przy użyciu konta Azure Machine Learning Model zarządzania.

- **Manifesty:** system zarządzania modelu wdrażania modelu w środowisku produkcyjnym, zawiera manifest, która może obejmować modelu, zależności oceniania skryptu, przykładowych danych i schematu. Plik manifestu to przepisu używany do tworzenia obrazu kontenera Docker. Za pomocą modelu zarządzania można automatycznie wygenerować manifesty, utworzyć różne wersje i zarządzanie te manifesty. 


- **Obrazy:** manifestów służy do generowania (i ponownie wygenerować) obrazy usługi Docker. Konteneryzowanych obrazy usługi Docker Utwórz elastyczności, aby uruchomić je w chmurze na maszynach lokalnych lub na urządzeniu IoT. Obrazy są niezależne i obejmują wszystkie zależności wymagane dla nowych danych z modelami oceniania. 

- **Usługi:** Model zarządzania umożliwia wdrażanie modeli jako usługi sieci web. Usługa sieci Web logiki i zależności są hermetyzowane w obrazie. Każda usługa sieci Web jest zestawem kontenerów, gotowy obraz do żądań obsługi do podanego adresu URL. Usługi sieci web będzie traktowany jako pojedynczego wdrożenia.
