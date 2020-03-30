---
title: Co to jest proces nauki o danych zespołu?
description: Zapewnia metodologię nauki o danych, aby dostarczać rozwiązania do analizy predykcyjnej i inteligentne aplikacje.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79088078"
---
# <a name="what-is-the-team-data-science-process"></a>Co to jest proces nauki o danych zespołu?

Team Data Science Process (TDSP) to zwinna, iteracyjna metodologia analizy danych, która zapewnia wydajne rozwiązania do analizy predykcyjnej i inteligentne aplikacje. TDSP pomaga usprawnić współpracę zespołową i uczenie się, sugerując, jak najlepiej ze sobą działają role zespołowe. TDSP zawiera najlepsze rozwiązania i struktury firmy Microsoft i innych liderów branży, aby pomóc w pomyślnym wdrożeniu inicjatyw do nauki o danych. Ma ona na celu pomoc firmom w pełnym wykorzystaniu zalet programu analitycznego.

Ten artykuł zawiera omówienie TDSP i jego głównych składników. Zapewniamy ogólny opis procesu tutaj, który można zaimplementować za pomocą różnych rodzajów narzędzi. Bardziej szczegółowy opis zadań projektu i ról zaangażowanych w cykl życia procesu znajduje się w dodatkowych połączonych tematach. Dostępne są również wskazówki dotyczące implementacji programu TDSP przy użyciu określonego zestawu narzędzi i infrastruktury firmy Microsoft, których używamy do implementacji programu TDSP w naszych zespołach.

## <a name="key-components-of-the-tdsp"></a>Kluczowe elementy TDSP

TDSP składa się z następujących kluczowych składników:

- Definicja **cyklu życia nauki o danych**
- **Znormalizowana struktura projektu**
- **Infrastruktura i zasoby** dla projektów do nauki o danych
- **Narzędzia i narzędzia** do realizacji projektu


## <a name="data-science-lifecycle"></a>Cykl życia nauki o danych

Proces nauki o danych zespołu (TDSP) zapewnia cykl życia do struktury rozwoju projektów nauki o danych. Cykl życia przedstawia pełne kroki, które po pomyślnym projektach.

Jeśli używasz innego cyklu życia nauki o danych, takiego jak [CRISP-DM,](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [KDD](https://wikipedia.org/wiki/Data_mining#Process) lub własny proces niestandardowy organizacji, nadal możesz używać TDSP opartego na zadaniach w kontekście tych cykli życia programów. Na wysokim poziomie, te różne metodologie mają wiele wspólnego. 

Ten cykl życia został zaprojektowany dla projektów do nauki o danych dostarczanych w ramach inteligentnych aplikacji. Te aplikacje wdrażają modele uczenia maszynowego lub sztucznej inteligencji do analizy predykcyjnej. Z tego procesu mogą również skorzystać projekty do nauki o danych odkrywczych lub improwizowane projekty analityczne. Ale w takich przypadkach niektóre z opisanych kroków mogą nie być potrzebne.    

Cykl życia przedstawia główne etapy, które projekty zazwyczaj wykonują, często iteracyjne:

* **Zrozumienie biznesowe**
* **Pozyskiwanie i rozumienie danych**
* **Modelowanie**
* **wdrażania**
* **Akceptacja klienta**

Oto wizualna reprezentacja **cyklu życia procesu nauki o danych zespołu.** 

![Cykl życia TDSP2](./media/overview/tdsp-lifecycle2.png) 

Cele, zadania i artefakty dokumentacji dla każdego etapu cyklu życia w TDSP są opisane w temacie [cyklu życia procesu nauki o danych zespołu.](lifecycle.md) Te zadania i artefakty są skojarzone z rolami projektu:

- Architekt rozwiązań
- Menedżer projektu
- Analityk danych
- Lider projektu 

Poniższy diagram zawiera widok siatki zadań (na niebiesko) i artefaktów (na zielono) skojarzonych z każdym etapem cyklu życia (na osi poziomej) dla tych ról (na osi pionowej). 

[![TDSP-role-i-zadania](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standaryzowana struktura projektu

Posiadanie wszystkich projektów współużytkuje strukturę katalogów i używa szablonów dla dokumentów projektu ułatwia członkom zespołu znajdowanie informacji o ich projektach. Cały kod i dokumenty są przechowywane w systemie kontroli wersji (VCS), takim jak Git, TFS lub Subversion, aby umożliwić współpracę zespołową. Śledzenie zadań i funkcji w uakwalnym systemie śledzenia projektów, takim jak Jira, Rally i Azure DevOps, umożliwia bliższe śledzenie kodu dla poszczególnych funkcji. Takie śledzenie umożliwia również zespołom uzyskanie lepszych szacunków kosztów. TDSP zaleca utworzenie osobnego repozytorium dla każdego projektu w vccs do przechowywania wersji, zabezpieczeń informacji i współpracy. Znormalizowana struktura wszystkich projektów pomaga budować wiedzę instytucjonalną w całej organizacji.

Dostarczamy szablony struktury folderów i wymagane dokumenty w standardowych lokalizacjach. Ta struktura folderów organizuje pliki, które zawierają kod do eksploracji danych i wyodrębniania funkcji i iteracji modelu rekordu. Te szablony ułatwiają członkom zespołu zrozumienie pracy wykonanej przez inne osoby i dodawanie nowych członków do zespołów. Łatwo jest przeglądać i aktualizować szablony dokumentów w formacie znaczników. Szablony umożliwiają wyświetlanie list kontrolnych z kluczowymi pytaniami dla każdego projektu, aby zapewnić, że problem jest dobrze zdefiniowany i że elementy dostarczane spełniają oczekiwaną jakość. Przykłady:

- kartę projektu dokumentu dokumentując problem biznesowy i zakres projektu
- danych w celu udokumentowania struktury i statystyk surowych danych
- raporty modelu dokumentunia funkcji pochodnych
- metryki wydajności modelu, takie jak krzywe ROC lub MSE


[![Katalogi TDSP](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Struktura katalogów może być sklonowana z [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktura i zasoby dla projektów do nauki o danych  

TDSP zawiera zalecenia dotyczące zarządzania współdzieloną infrastrukturą analityczną i pamięci masowej, takie jak:

- systemy plików w chmurze do przechowywania zestawów danych 
- bazy danych
- klastry big data (Hadoop lub Spark) 
- usługa uczenia maszynowego 

Infrastruktura analizy i magazynu, w której przechowywane są nieprzetworzone i przetworzone zestawy danych, może znajdować się w chmurze lub lokalnie. Infrastruktura ta umożliwia powtarzalną analizę. Pozwala to również uniknąć powielania działań, co może prowadzić do niespójności i niepotrzebnych kosztów infrastruktury. Dostępne są narzędzia do inicjowania obsługi administracyjnej zasobów udostępnionych, śledzenia ich i umożliwienia każdemu członkowi zespołu bezpiecznego łączenia się z tymi zasobami. Jest również dobrą praktyką, aby członkowie projektu utworzyć spójne środowisko obliczeniowe. Różni członkowie zespołu mogą następnie replikować i weryfikować eksperymenty.

Oto przykład zespołu pracującego nad wieloma projektami i udostępniającego różne składniki infrastruktury analizy chmury.

[![TDSP-infrastruktura](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Narzędzia i narzędzia do realizacji projektu

Wprowadzenie procesów w większości organizacji jest wyzwaniem. Narzędzia do wdrażania procesu nauki o danych i cyklu życia pomagają zmniejszyć bariery i zwiększyć spójność ich przyjęcia. TDSP zapewnia początkowy zestaw narzędzi i skryptów, aby szybko rozpocząć przyjęcie TDSP w zespole. Pomaga również zautomatyzować niektóre z typowych zadań w cyklu życia nauki o danych, takich jak eksploracja danych i modelowanie bazowe. Istnieje dobrze zdefiniowana struktura przewidziana dla osób fizycznych, aby wnieść udostępnione narzędzia i narzędzia do repozytorium udostępnionego kodu zespołu. Te zasoby mogą być następnie wykorzystywane przez inne projekty w zespole lub organizacji. TDSP planuje również umożliwić wkład narzędzi i narzędzi dla całej społeczności. Narzędzia TDSP można sklonować z [gitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Następne kroki

[Proces nauki o danych zespołowych: role i zadania](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Przedstawia kluczowe role personelu i ich skojarzone zadania dla zespołu nauki o danych, który standaryzuje ten proces. 
