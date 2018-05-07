---
title: Co to jest zespołowe przetwarzanie danych dla celów naukowych? | Microsoft Docs
description: Udostępnia metodologii nauki danych w celu tworzenia rozwiązań analizy predykcyjnej i inteligentnego aplikacji.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: 4a73b988863a27f872c695cf209d2c46c6bb5f89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="what-is-the-team-data-science-process"></a>Co to jest zespołowe przetwarzanie danych dla celów naukowych?

Proces nauki danych zespołu (TDSP) jest metodologia nauki agile, iteracji danych do tworzenia rozwiązań analizy predykcyjnej i inteligentnego aplikacji wydajnie. Pomaga to zwiększyć jakość uczenia i współpracy w zespole. Ta metodologia obejmuje wybrane najlepsze rozwiązania i struktury firmy Microsoft oraz innych podmiotów w branży, które ułatwiają pomyślne implementowanie inicjatyw w zakresie nauki o danych. Ma ona na celu pomoc firmom w pełnym wykorzystaniu zalet programu analitycznego.

Ten artykuł zawiera omówienie TDSP oraz jej główne składniki. Udostępniamy ogólny opis tutaj procesu, który może być zaimplementowany przy użyciu różnych narzędzi. Bardziej szczegółowy opis zadania projektu i role związane z cyklem życia procesu znajduje się w dodatkowych powiązanych tematach. Dostępne są również wskazówki dotyczące implementowania TDSP przy użyciu określonego zestawu narzędzi firmy Microsoft i infrastruktury, używaną do zaimplementowania TDSP w naszymi zespołami.

## <a name="key-components-of-the-tdsp"></a>Najważniejsze składniki TDSP

TDSP obejmuje następujące składniki klucza:

- A **analizy danych w cyklu życia** definicji
- A **standaryzowane struktury projektu**
- **Infrastrukturę i zasoby** dla projektów analizy danych
- **Narzędzia** do wykonania projektu


## <a name="data-science-lifecycle"></a>Cykl życia analizy danych

Proces nauki danych zespołu (TDSP) zawiera cykl struktury rozwoju projektów analizy danych. Cykl życia omówiono kroki, od początku do końca, że projekty zwykle należy wykonać podczas są wykonywane.

Jeśli używasz innej cykl życia analizy danych, takich jak [DM WYSOKĄ](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) lub procesu niestandardowych w organizacji, można nadal używać w kontekście tych cyklów programowanie TDSP opartego na zadaniach. Na wysokim poziomie te różne metody mają wiele wspólnych. 

Ten cykl życia zostało zaprojektowane dla projektów nauki danych, które są dostarczane jako część inteligentnego aplikacji. Te aplikacje wdrażanie machine learning lub analizy sztucznego modeli analizy predykcyjnej. Projekty nauki poznawcze danych lub projektów ad hoc analytics można również korzystać z tego procesu. W takich przypadkach niektóre kroki opisane może nie być jednak potrzebna.    

Cykl życia przedstawiono główne etapy, które projekty zazwyczaj wykonywane, często wielokrotnie powtarzane:

* **Opis biznesowa**
* **Uzyskiwanie danych i zrozumienie**
* **Modelowanie**
* **Wdrożenie**
* **Akceptacji klienta**

W tym miejscu jest wizualną reprezentacją **cyklu życia procesu nauki danych zespołu**. 

![TDSP Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Cele, zadania i artefakty dokumentacji dla każdego etapu cyklu życia w TDSP są opisane w [cyklu życia procesu nauki danych zespołu](lifecycle.md) tematu. Te zadania i artefakty są skojarzone z rolami projektu:

- Architekt rozwiązań
- Menedżer projektu
- Analityk danych
- Lider projektu 

Na poniższym diagramie przedstawiono widoku siatki zadania (na niebiesko) i artefakty (na zielono), które zostały skojarzone z każdym etapie cyklu życia (na osi poziomej) dla tych ról (na osi pionowej). 

![TDSP-role i zadania](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Struktura standardowych projektu

Wszystkie projekty mają strukturę katalogu i użyj szablonów dokumentów projektu o ułatwia członkom zespołu, aby znaleźć informacje o ich projektów. Cały kod i dokumenty są przechowywane w systemie kontroli wersji (VC) jak Git, TFS lub Podwersją, aby włączyć współpraca z zespołem. Śledzenia zadań i funkcje w projekcie agile systemu, takich jak Jira śledzenia, Rally, Visual Studio Team Services umożliwia bliżej śledzenia z kodu dla poszczególnych funkcji. Takie śledzenia umożliwia również zespołów uzyskać lepsze Szacowanie kosztów. TDSP zaleca utworzenie oddzielnych repozytorium dla każdego projektu w VC przechowywania wersji, bezpieczeństwo informacji i współpracy. Standardowe struktury dla wszystkich projektów pomaga kompilacji instytucjonalnych wiedzy w organizacji.

Udostępniamy szablonów dla struktury folderów i wymagane dokumenty w standardowych lokalizacjach. Ta struktura folderów organizuje pliki zawierające kod Eksploracja danych i wyodrębniania funkcji i który rekord iteracji modelu. Te szablony ułatwiają członków zespołu, aby zrozumieć pracy przez innych użytkowników i dodawać nowych członków do zespołu. To proste wyświetlić i zaktualizować szablonów dokumentów w formacie markdown. Udostępnianie list kontrolnych ważne pytania dla każdego projektu upewnić się, czy problem jest dobrze zdefiniowany i czy materiałów spełniają jakości oczekiwano za pomocą szablonów. Przykłady:

- karty Projekt, aby udokumentować problem biznesowy i zakres projektu
- Raporty danych do dokumentu struktury i statystyki danych pierwotnych
- Raporty modelu do dokumentu funkcje pochodne
- Model metryki wydajności, takich jak krzywych ROC lub MSE


![TDSP katalogów](./media/overview/tdsp-dir-structure.png)

Struktura katalogów można sklonować z [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastrukturę i zasoby danych nauki projektów  

TDSP zawiera zalecenia dotyczące zarządzania udostępnionego analytics i infrastruktury magazynu, takich jak:

- systemy plików chmury do przechowywania zestawów danych 
- bazy danych
- klastry danych big data (Hadoop lub Spark) 
- Machine learning usługi. 

Infrastruktura analizy i Magazyn może być w chmurze lub lokalnie. Jest to, gdzie są przechowywane nieprzetworzone i przetwarzanie zestawów danych. Ta infrastruktura włącza analizę odtworzenia. Unika się również duplikatów, co może prowadzić do niespójności i koszty infrastrukturalne niepotrzebne. Narzędzia są dostępne do udostępnienia zasobów udostępnionych, monitorowanie ich i umożliwia bezpieczne łączenie się z tymi zasobami każdego członka zespołu. Możliwe jest również dobrym rozwiązaniem ma członków projektu utworzyć środowisko spójny obliczeniowej. Członkowie zespołu różnych można replikować i zweryfikować eksperymentów.

Oto przykład zespołu działa na wielu projektów i udostępniania różnych składników infrastruktury analytics chmury.

![TDSP infrastruktury](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Narzędzia i narzędzia umożliwiające wykonanie projektu

Wprowadzenie do procesów w większości organizacji jest trudne. Implementowanie pomocy proces i cyklem życia analizy danych niższych bariery w i zwiększyć zgodność przyjęcia narzędzia. TDSP zapewnia początkowego zestawu narzędzi i skryptów, aby szybko rozpocząć przyjęcia TDSP w zespole. Pomaga również zautomatyzować niektóre typowe zadania w cyklu życia analizy danych, takich jak Eksploracja danych i modelowanie linii bazowej. Brak dobrze zdefiniowanej strukturze podane dla użytkowników indywidualnych do ich współtworzenia z udostępnionego narzędzi do ich zespołu udostępnionego kodu repozytorium. Następnie można użyć tych zasobów przez inne projekty, w obrębie zespołu lub organizacji. TDSP plany włączyć udział narzędzi całej społeczności. Narzędzia TDSP można sklonować z [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Kolejne kroki

[W procesie nauki danych zespołu: Ról i zadań](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) opisano role klucza pracowników i ich skojarzonych zadań dla zespołu nauki danych, która normalizuje w tym procesie. 
