---
title: Co to jest zespołowe przetwarzanie danych dla celów naukowych?
description: Udostępnia metodologia nauki o danych w celu dostarczania rozwiązań do analizy predykcyjnej i inteligentnych aplikacji.
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
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79088078"
---
# <a name="what-is-the-team-data-science-process"></a>Co to jest zespołowe przetwarzanie danych dla celów naukowych?

Proces do nauki o danych zespołu (TDSP) jest metodologia nauki o danych agile, iteracyjne wydajnie dostarczać rozwiązania do analizy predykcyjnej i inteligentnych aplikacji. PRZETWARZANIA TDSP pomaga ulepszyć współpracę zespołową i uczenie się, sugerując, w jaki sposób role zespołu najlepiej współpracują ze sobą. PRZETWARZANIA TDSP zawiera najlepsze rozwiązania i struktury od firmy Microsoft i innych liderów branżowych, aby pomóc w pomyślnym wdrożeniu inicjatyw dotyczących analizy danych. Ma ona na celu pomoc firmom w pełnym wykorzystaniu zalet programu analitycznego.

Ten artykuł zawiera omówienie przetwarzania TDSP i jego głównych składnikach. Udostępniamy ogólny opis procesu, który można zaimplementować przy użyciu różnych rodzajów narzędzi. Bardziej szczegółowy opis zadania projektu oraz role zaangażowane w cyklu życia procesu jest dostępna w dodatkowych powiązanych tematach. Udostępniane są również wskazówki dotyczące implementowania przetwarzania TDSP korzystających z określonego zestawu narzędzi firmy Microsoft i infrastrukturą, używaną do zaimplementowania przetwarzania TDSP w naszych zespołów.

## <a name="key-components-of-the-tdsp"></a>Najważniejsze składniki przetwarzania TDSP

Przetwarzanie TDSP składa się z następujących kluczowych składników:

- Definicja **cyklu życia analizy danych**
- **Standardowa struktura projektu**
- **Infrastruktura i zasoby** dla projektów analizy danych
- **Narzędzia i programy** do wykonania projektu


## <a name="data-science-lifecycle"></a>Cykl życia analizy danych

Proces do nauki o danych zespołu (TDSP) zawiera cykl życia struktury opracowywania projektów do nauki o danych. Cykl życia przedstawia pełne kroki, które są wykonywane przez pomyślne projekty.

Jeśli jest używany inny cykl życia analizy [danych, taki jak](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [KDD,](https://wikipedia.org/wiki/Data_mining#Process) lub własny niestandardowy proces organizacji, można nadal używać przetwarzania TDSP opartego na zadaniach w kontekście tych cykli rozwoju. Na wysokim poziomie te różne metody mają wiele wspólnych. 

Ten cykl życia został zaprojektowany tak, aby uzyskać projekty do nauki o danych, które są dostarczane jako część inteligentnych aplikacji. Te aplikacje wdrażania modeli uczenia i sztucznej inteligencji maszynowego do analizy predykcyjnej. Dzięki temu procesowi mogą korzystać projekty analizy danych w nauce lub projekty analiz Improvised. W takich przypadkach niektóre procedury opisanej może nie być jednak potrzebna.    

Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

* **Zrozumienie biznesowe**
* **Pozyskiwanie i zrozumienie danych**
* **Modelu**
* **Wdrożenie**
* **Akceptacja klienta**

Poniżej przedstawiono wizualną reprezentację **cyklu życia zespołowego procesu nauki danych**. 

![Lifecycle2 przetwarzania TDSP](./media/overview/tdsp-lifecycle2.png) 

Artefakty cele, zadania i dokumentacja dotyczące poszczególnych etapów cyklu życia w programie przetwarzania TDSP są opisane w temacie [cykl życia procesu nauki o danych zespołowych](lifecycle.md) . Te zadania i artefakty są skojarzone z rolami projektu:

- Architekt rozwiązań
- Menedżer projektu
- Analityk danych
- Lider projektu 

Na poniższym diagramie przedstawiono widoku siatki zadań (na niebiesko) i artefakty (w kolorze zielonym), które zostały skojarzone z każdym etapie cyklu życia (na osi poziomej) dla tych ról (na osi pionowej). 

[![przetwarzania TDSP — role i zadania](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Struktura projektu standardowe

Wszystkie projekty, udostępniać strukturę katalogów i korzystanie z szablonów projektów dokumentów o ułatwia członkom zespołu znaleźć informacje dotyczące ich projektów. Cały kod i dokumenty są przechowywane w systemie kontroli wersji (klastry Wirtualne), takich jak Git, TFS lub Subversion, umożliwiające współpracę zespołową. Śledzenie zadań i funkcji w projekcie agile śledzenia systemu, np. Jira, wyścigów i DevOps platformy Azure umożliwia bliżej śledzenia kodu dla poszczególnych funkcji. Takie śledzenia umożliwia także zespołów w celu uzyskania lepszego szacunkowy koszt. Przetwarzanie TDSP zaleca się utworzenie oddzielnych repozytorium dla każdego projektu w VC przechowywania wersji, bezpieczeństwo informacji i współpracy. Standardowej struktury dla wszystkich projektów pomaga w tworzeniu instytucjonalnych wiedzy w organizacji.

Firma Microsoft zapewnia szablony dla struktury folderów i dokumentów wymagane w lokalizacjach. Tej struktury folderów organizuje pliki zawierające kod do eksploracji danych i wyodrębniania funkcji, a które Zapisz iteracji modelu. Te szablony ułatwiają członków zespołu, aby zrozumieć pracy wykonanej przez innych użytkowników i dodawać nowych członków zespołów. To ułatwia wyświetlanie i aktualizowanie szablonów dokumentów w formacie markdown. Użyj szablonów, aby podać listy kontrolne z kluczowymi pytaniami dla każdego projektu, aby upewnić się, że problem jest dobrze zdefiniowany i że dostarczane dane spełniają wymagania jakości. Przykłady:

- karty Projekt, aby udokumentować problem biznesowy i zakres projektu
- dane raportów do dokumentu struktury i statystyki dla danych nieprzetworzonych
- Raporty modelu pochodnej funkcji dokumentować
- Model metryk wydajności, takich jak krzywych ROC lub MSE


[![przetwarzania TDSP — katalogi](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Strukturę katalogów można sklonować z usługi [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktura i zasoby dla projektów do nauki o danych  

Przetwarzanie TDSP zawiera zalecenia dotyczące zarządzania udostępnionego analizy i infrastruktury magazynu, takie jak:

- systemy plików w chmurze do przechowywania zestawów danych 
- bazy danych
- dane big data klastrów (usługi Hadoop lub Spark) 
- Usługa Machine learning 

Infrastruktura analizy i magazynu, w której przechowywane są nieprzetworzone i przetworzone zestawy danych, może znajdować się w chmurze lub lokalnie. Infrastruktura ta umożliwia analizę do odtworzenia. Unika również duplikatów, co może doprowadzić do niespójności i zbędnych kosztów infrastruktury. Inicjowanie obsługi administracyjnej zasobów udostępnionych, ich śledzenie i zezwolić na poszczególnych członków zespołu, bezpieczne łączenie do tych zasobów są dostępne narzędzia. Jest również dobrym rozwiązaniem członkowie projektu utworzenia środowiska obliczeniowego spójne. Różni członkowie zespołu można replikować i weryfikowanie eksperymentów.

Oto przykład udostępnianie różne składniki infrastruktury analizy cloud i Praca nad wieloma projektami zespołu.

[![przetwarzania TDSP — infrastruktura](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Narzędzia i narzędzia umożliwiające wykonywanie projektu

Wprowadzenie do procesów, które w większości organizacji staje się wyzwaniem. Narzędzia podane Implementowanie pomocy procesu i cyklem życia nauki o danych niższych barier i poprawia spójność ich wdrażania. Przetwarzanie TDSP zapewnia początkowy zestaw narzędzi i skryptów, które pozwalają szybko rozpocząć wdrożenie przetwarzania TDSP w zespole. Pomaga również zautomatyzować niektóre typowe zadania w cykl życia analizy danych, takich jak Eksploracja danych i modelowania linii bazowej. Istnieje dobrze zdefiniowana struktura, która umożliwia osobom Współtworzenie współużytkowanych narzędzi i narzędzi w repozytorium kodu udostępnionego zespołu. Te zasoby mogą zostać następnie wykorzystane przez inne projekty w ramach zespołu lub organizacji. Przetwarzanie TDSP planuje Włącz wkładów narzędzia i programy narzędziowe całej społeczności. Narzędzia przetwarzania TDSP można sklonować z usługi [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Następne kroki

[Proces nauki danych zespołu: role i zadania](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Przedstawia podstawowe role pracowników i powiązane z nimi zadania dla zespołu ds. analizy danych, które są standaryzacją tego procesu. 
