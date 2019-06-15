---
title: Co to jest zespołowe przetwarzanie danych dla celów naukowych?
description: Udostępnia metodologia nauki o danych w celu dostarczania rozwiązań do analizy predykcyjnej i inteligentnych aplikacji.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 10/20/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b4e8c78d7402c254c91c3e100814e1f3eafc41b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61429420"
---
# <a name="what-is-the-team-data-science-process"></a>Co to jest zespołowe przetwarzanie danych dla celów naukowych?

Proces do nauki o danych zespołu (TDSP) jest metodologia nauki o danych agile, iteracyjne wydajnie dostarczać rozwiązania do analizy predykcyjnej i inteligentnych aplikacji. Pomaga to zwiększyć jakość uczenia i współpracy w zespole. Ta metodologia obejmuje wybrane najlepsze rozwiązania i struktury firmy Microsoft oraz innych podmiotów w branży, które ułatwiają pomyślne implementowanie inicjatyw w zakresie nauki o danych. Ma ona na celu pomoc firmom w pełnym wykorzystaniu zalet programu analitycznego.

Ten artykuł zawiera omówienie przetwarzania TDSP i jego głównych składnikach. Firma Microsoft zapewnia ogólny opis poniżej proces, którą można implementować przy użyciu różnych narzędzi. Bardziej szczegółowy opis zadania projektu oraz role zaangażowane w cyklu życia procesu jest dostępna w dodatkowych powiązanych tematach. Udostępniane są również wskazówki dotyczące implementowania przetwarzania TDSP korzystających z określonego zestawu narzędzi firmy Microsoft i infrastrukturą, używaną do zaimplementowania przetwarzania TDSP w naszych zespołów.

## <a name="key-components-of-the-tdsp"></a>Najważniejsze składniki przetwarzania TDSP

Przetwarzanie TDSP składa się z następujących kluczowych składników:

- A **cykl życia analizy danych** definicji
- A **standaryzowane struktura projektu**
- **Infrastruktury i zasobów** dla projektów do nauki o danych
- **Narzędzia i programy narzędziowe** wykonywanie projektu


## <a name="data-science-lifecycle"></a>Cykl życia analizy danych

Proces do nauki o danych zespołu (TDSP) zawiera cykl życia struktury opracowywania projektów do nauki o danych. Cykl życia zawiera opis kroków, od początku do końca, że projekty zazwyczaj należy wykonać podczas są wykonywane.

Jeśli używasz innego cykl życia analizy danych, takich jak [DM WYSOKĄ](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) lub proces niestandardowej w organizacji można nadal używać opartego na zadaniach przetwarzania TDSP w kontekście tych cyklu rozwoju. Na wysokim poziomie te różne metody mają wiele wspólnych. 

Ten cykl życia został zaprojektowany tak, aby uzyskać projekty do nauki o danych, które są dostarczane jako część inteligentnych aplikacji. Te aplikacje wdrażania modeli uczenia i sztucznej inteligencji maszynowego do analizy predykcyjnej. Projekty do nauki o danych eksploracyjnego lub projekty analizy ad-hoc, można również korzystać z tego procesu. W takich przypadkach niektóre procedury opisanej może nie być jednak potrzebna.    

Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

* **Poznawanie firmy**
* **Pozyskiwanie danych i opis**
* **Modelowanie**
* **Wdrożenie**
* **Akceptacja klienta**

Poniżej przedstawiono reprezentację wizualną **cykl życia zespołowego danych naukowych**. 

![Lifecycle2 przetwarzania TDSP](./media/overview/tdsp-lifecycle2.png) 

Cele, zadania i dokumentacji artefaktów na każdym etapie cyklu życia w przetwarzania TDSP są opisane w [cykl życia zespołowego danych naukowych](lifecycle.md) tematu. Te zadania i artefakty są skojarzone z rolami projektu:

- Architekt rozwiązań
- Menedżer projektu
- Analityk danych
- Lider projektu 

Na poniższym diagramie przedstawiono widoku siatki zadań (na niebiesko) i artefakty (w kolorze zielonym), które zostały skojarzone z każdym etapie cyklu życia (na osi poziomej) dla tych ról (na osi pionowej). 

[![Przetwarzania TDSP-role i zadania](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Struktura projektu standardowe

Wszystkie projekty, udostępniać strukturę katalogów i korzystanie z szablonów projektów dokumentów o ułatwia członkom zespołu znaleźć informacje dotyczące ich projektów. Cały kod i dokumenty są przechowywane w systemie kontroli wersji (klastry Wirtualne), takich jak Git, TFS lub Subversion, umożliwiające współpracę zespołową. Śledzenie zadań i funkcji w projekcie agile śledzenia systemu, np. Jira, wyścigów i DevOps platformy Azure umożliwia bliżej śledzenia kodu dla poszczególnych funkcji. Takie śledzenia umożliwia także zespołów w celu uzyskania lepszego szacunkowy koszt. Przetwarzanie TDSP zaleca się utworzenie oddzielnych repozytorium dla każdego projektu w VC przechowywania wersji, bezpieczeństwo informacji i współpracy. Standardowej struktury dla wszystkich projektów pomaga w tworzeniu instytucjonalnych wiedzy w organizacji.

Firma Microsoft zapewnia szablony dla struktury folderów i dokumentów wymagane w lokalizacjach. Tej struktury folderów organizuje pliki zawierające kod do eksploracji danych i wyodrębniania funkcji, a które Zapisz iteracji modelu. Te szablony ułatwiają członków zespołu, aby zrozumieć pracy wykonanej przez innych użytkowników i dodawać nowych członków zespołów. To ułatwia wyświetlanie i aktualizowanie szablonów dokumentów w formacie markdown. Szablony umożliwiają udostępnianie list kontrolnych kluczowych pytań dla każdego projektu upewnić się, czy problem jest dobrze zdefiniowany i spełniania jakości oczekiwane cele do zrealizowania. Przykłady:

- karty Projekt, aby udokumentować problem biznesowy i zakres projektu
- dane raportów do dokumentu struktury i statystyki dla danych nieprzetworzonych
- Raporty modelu pochodnej funkcji dokumentować
- Model metryk wydajności, takich jak krzywych ROC lub MSE


[![TDSP-directories](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Struktura katalogów mogą być klonowane z [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktura i zasoby dla projektów do nauki o danych  

Przetwarzanie TDSP zawiera zalecenia dotyczące zarządzania udostępnionego analizy i infrastruktury magazynu, takie jak:

- systemy plików w chmurze do przechowywania zestawów danych 
- bazy danych
- dane big data klastrów (usługi Hadoop lub Spark) 
- Usługa Machine learning 

Infrastruktura analizy i magazynowania można w chmurze lub lokalnie. Jest to, gdzie są przechowywane nieprzetworzone i będą przetwarzane zestawów danych. Infrastruktura ta umożliwia analizę do odtworzenia. Unika również duplikatów, co może doprowadzić do niespójności i zbędnych kosztów infrastruktury. Inicjowanie obsługi administracyjnej zasobów udostępnionych, ich śledzenie i zezwolić na poszczególnych członków zespołu, bezpieczne łączenie do tych zasobów są dostępne narzędzia. Jest również dobrym rozwiązaniem członkowie projektu utworzenia środowiska obliczeniowego spójne. Różni członkowie zespołu można replikować i weryfikowanie eksperymentów.

Oto przykład udostępnianie różne składniki infrastruktury analizy cloud i Praca nad wieloma projektami zespołu.

[![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Narzędzia i narzędzia umożliwiające wykonywanie projektu

Wprowadzenie do procesów, które w większości organizacji staje się wyzwaniem. Narzędzia podane Implementowanie pomocy procesu i cyklem życia nauki o danych niższych barier i poprawia spójność ich wdrażania. Przetwarzanie TDSP zapewnia początkowy zestaw narzędzi i skryptów, które pozwalają szybko rozpocząć wdrożenie przetwarzania TDSP w zespole. Pomaga również zautomatyzować niektóre typowe zadania w cykl życia analizy danych, takich jak Eksploracja danych i modelowania linii bazowej. Jest dobrze zdefiniowanej strukturze podane dla użytkowników indywidualnych współtworzyć współużytkowanymi narzędziami i narzędzia do repozytorium udostępnionego kodu swojego zespołu. Te zasoby mogą zostać następnie wykorzystane przez inne projekty w ramach zespołu lub organizacji. Przetwarzanie TDSP planuje Włącz wkładów narzędzia i programy narzędziowe całej społeczności. Programy narzędziowe przetwarzania TDSP mogą być klonowane z [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Kolejne kroki

[Zespół danych dla celów naukowych: Role i zadania](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) opisano role kluczy, personelu i ich skojarzonych zadań dla zespołu do nauki o danych, który standaryzuje na temat tego procesu. 
