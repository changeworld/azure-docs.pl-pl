---
title: "Struktura projektów z szablonem procesu nauki danych Team | Dokumentacja firmy Microsoft"
description: "Jak utworzyć wystąpienia zespołu danych nauki procesu (TDSP) szablonów usługi Azure Machine Learning struktury projektów współpracy"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: 0f7a6d0a58e2dcfd94e20a81ae12b42f238016c8
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Struktura projektów z szablonem procesu nauki danych zespołu

Ten dokument zawiera instrukcje dotyczące sposobu tworzenia projektów analizy danych w usłudze Azure Machine Learning z szablonami zespołu danych nauki procesu (TDSP). Szablony ułatwiają do projektów struktury współpracy i powtarzalności. 


## <a name="what-is-the-team-data-science-process"></a>Co to jest zespołowe przetwarzanie danych dla celów naukowych?
TDSP jest procesem nauki danych agile, iteracji, wykonywanie i opracowywania rozwiązań zaawansowana analityka. Został zaprojektowany tak, aby poprawić współpracy i wydajności zespołów nauki danych w organizacji przedsiębiorstwa. Obsługuje ona tych celów z czterech najważniejsze składniki:

   * Standard [analizy danych w cyklu życia](../team-data-science-process/lifecycle.md) definicji.
   * Struktury projektu standardowych, [dokumentacji i raportowania szablonów projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Infrastrukturę i zasobów do wykonania projektu, takie jak odpowiednio obliczeniowej i pamięci masowej infrastruktury i kodu repozytoriów.
   * [Narzędzia](https://github.com/Azure/Azure-TDSP-Utilities) do analizy danych zadania w projekcie, takich jak:
      - Kontrola wersji współpracy
      - Przegląd kodu
      - Eksploracja danych i modelowanie
      - Planowanie pracy

Aby uzyskać bardziej szczegółowe omówienie TDSP, zobacz [Omówienie procesu nauki danych zespołu](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Dlaczego należy użyć TDSP struktury i szablony?
Standaryzacja struktury, cykl życia i dokumentacji projektów nauki danych ma kluczowe znaczenie dla ułatwienia skutecznej współpracy w zespołach analizy danych. Tworzenie projektów learning maszyny z szablonem TDSP do zapewnienia takiego framework zespołową skoordynowany sposób.

Wcześniej wydane [repozytorium GitHub TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate) celu osiągnięcie tych celów. Ale nie było możliwe, do tej pory do tworzenia wystąpienia struktury TDSP i szablonów w ramach narzędzia analizy danych. Obecnie istnieje możliwość utworzenia projektu uczenia maszynowego, tworzącym TDSP Szablony struktury i dokumentacji. 

## <a name="things-to-note-before-creating-a-new-project"></a>Rzeczy do uwzględnienia przed utworzeniem nowego projektu
Sprawdź następujące elementy *przed* Tworzenie nowego projektu:
* Przejrzyj uczenia maszynowego TDSP [szablonu](https://aka.ms/tdspamlgithubrepo).
* Zawartość (inne niż co to jest już obecna w folderze "dokumenty") muszą zawierać mniej niż 25 MB. Patrz Notatka poniżej tej listy.
* Przykład\_folderu danych jest tylko dla małych plików danych (mniej niż 5 MB) z którymi można przetestować kod lub uruchomić wczesne programowanie.
* Przechowywanie plików, takich jak Word i pliki programu PowerPoint, może znacznie zwiększyć rozmiar folderu "dokumenty". Zaleca się, że wyszukiwanie współpracy typu Wiki, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), lub innego zasobu współpracy do przechowywania tych plików.
* Aby dowiedzieć się, jak obsługiwać duże pliki i dane wyjściowe w uczeniu maszynowym, przeczytaj [wprowadzeniem trwałych zmian i pracy z dużymi plikami](http://aka.ms/aml-largefiles).

> [!NOTE]
> Wszystkie powiązane dokumentacji zawartości (tekstu, promocjach cenowych obrazów i inne pliki dokumentu) *nie* używane podczas wykonywania projekt, inne niż plik readme.md musi znajdować się w folderze o nazwie "dokumenty" (tylko małe litery). Folder "dokumenty" jest specjalne ignorowane przez wykonanie uczenia maszynowego, więc zawartość w tym folderze nie pobrać kopiowana niepotrzebnie obliczeniowe elementy docelowe. Obiekty w tym folderze również nie są wliczane do zakończenia 25 MB rozmiar projektu. Folder "dokumenty" jest na przykład miejsce do przechowywania plików duży obraz potrzebnych w dokumentacji. Te pliki nadal są śledzone przez Git za pomocą historii uruchamiania. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Utwórz wystąpienie struktury TDSP i szablony z galerii szablonów uczenia maszynowego
Aby utworzyć nowy projekt z szablonami TDSP struktury i dokumentacji, wykonaj następujące procedury.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu
Aby utworzyć nowy projekt, otwórz uczenia maszynowego Azure. W obszarze **projekty** w lewym górnym okienku wybierz znak plus (**+**), a następnie wybierz **nowy projekt**.

![Nowy projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Utwórz nowy projekt strukturę TDSP
   1. Określ parametry i informacje w odpowiednie pole wyboru lub na liście:

      - Nazwa projektu
      - Katalog projektu
      - Opis projektu
      - Pusta ścieżka repozytorium Git
      - Nazwa obszaru roboczego

   2. Następnie w **wyszukiwania** wprowadź **TDSP**. 
   3. Gdy **struktury projektu z TDSP** opcja jest dostępna, zaznacz ten szablon. 
   4. Wybierz **Utwórz** przycisk, aby utworzyć nowy projekt o strukturze TDSP. Jeśli podasz puste repozytorium Git podczas tworzenia projektu (w odpowiednim polu tekstowym), a następnie tego repozytorium po utworzeniu projektu zostaną wyświetlone zawartości i struktury projektu.

![Tworzenie projektu TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Sprawdź TDSP struktury projektu
Po utworzeniu nowego projektu można sprawdzić jego struktury (zobacz lewego panelu na poniższej ilustracji). Zawiera wszystkie aspekty standardowych dokumentacji dla zrozumienia biznesowych. Elementy te obejmują etapy cyklu życia TDSP, lokalizacja danych definicje i architektura ten szablon dokumentacji. 

Struktura wyświetlany jest pochodną struktury TDSP, który jest opublikowany w [szablony projektów TDSP struktury, dokumentów i artefaktu](https://github.com/Azure/Azure-TDSP-ProjectTemplate), z niektórych zmian. Na przykład kilka szablonów dokumentów są scalane w jednym języku znaczników markdown, to znaczy, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Struktura folderów projektu
Szablon projektu TDSP zawiera następujące foldery najwyższego poziomu:
   - **Kod**: zawiera kod.
   - **Dokumentacja**: zawiera niezbędne dokumentację dotyczącą projektu (na przykład pliki markdown i powiązane z).
   - **sample_data**: zawiera **próbki (małe)** dane, które można użyć do wczesnego rozwoju lub testowania. Te zestawy nie są zwykle większych niż kilka (5) MB. Ten folder nie jest pełny lub dużych zestawów danych.

![Dane przykładowe](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Użyj TDSP struktury i szablony
Należy dodać informacje specyficzne dla projektu do struktury i szablony. W przypadku oczekiwać wypełnić je z kodem i informacje niezbędne do wykonania i dostarczania projektu. [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) plik jest szablon, który należy zmodyfikować informacje istotne dla projektu. Zawiera zestaw pytań, które ułatwiają Wypełnij informacje dla każdego z czterech etapów [cyklu życia TDSP](../team-data-science-process/lifecycle.md).

Przykład struktury projektu wygląd podczas wykonywania lub zakończenia jest wyświetlany w lewym panelu na poniższej ilustracji. Ten projekt jest z [proces nauki danych zespołu przykładowy projekt: klasyfikowania przychody z USA danych spisu w usłudze Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) przykładowy projekt.

![Przykład struktury projektu](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Dokument projektu
Zapoznaj się [TDSP dokumentacji szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate) informacji dotyczących dokumentu projektu. W bieżącym szablonie dokumentacji Machine Learning TDSP, zaleca się, czy zawiera wszystkie informacje w [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) pliku. Ten szablon powinien zostać wypełnione informacje specyficzne dla projektu. 

Firma Microsoft udostępnia również [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) szablonu. Można użyć tego szablonu, aby uwzględnić wszystkie informacje, która nie znajduje się w dokumencie projekt podstawowy, ale jest nadal przydatne do dokumentu. 

### <a name="example-project-report"></a>Przykładowy raport projektu
Możesz uzyskać [przykładowy raport projektu](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Ten raport projektu dla [USA dochodu klasyfikacji przykładowy projekt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) pokazano, jak utworzyć wystąpienia i szablon TDSP dla projektu analizy danych.

## <a name="next-steps"></a>Kolejne kroki
Aby ułatwić zrozumienie działania dotyczące sposobu używania struktury TDSP i szablonów w projektach uczenia maszynowego, udostępniamy kilka przykładów projektu zakończone w dokumentacji dotyczącej usługi Machine Learning:

- Dla przykładu, który pokazuje, jak utworzyć projekt TDSP w uczeniu maszynowym, zobacz [proces nauki danych zespołu przykładowy projekt: klasyfikowania przychody z danych nam spisu w usłudze Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Dla przykładu korzystającego z głębokiego learning w języku naturalnym przetwarzania (NLP) w projekcie wystąpienia TDSP w Machine Learning, zobacz [mnie medyczne rozpoznawania jednostek przy użyciu języka naturalnego przetwarzania za pomocą bezpośrednich learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

