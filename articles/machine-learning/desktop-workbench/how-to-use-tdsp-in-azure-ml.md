---
title: Tworzenie struktury projektów za pomocą szablonu procesu do nauki o danych Team | Dokumentacja firmy Microsoft
description: Jak utworzyć szablony Team Data Science naukowych w usłudze Azure Machine Learning struktury projektów do współpracy
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8e13ba0bcbd24dcc3cd08a0d2886f1da9d85de79
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249918"
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Tworzenie struktury projektów za pomocą szablonu procesu do nauki o danych zespołu

Niniejszy dokument zawiera instrukcje dotyczące sposobu tworzenia projektów do nauki o danych w starszej wersji usługi Azure Machine Learning za pomocą szablonów Team Data Science naukowych. Te szablony ułatwiają tworzenie struktury projektów do współpracy i powtarzalności. 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

## <a name="what-is-the-team-data-science-process"></a>Co to jest zespołowe przetwarzanie danych dla celów naukowych?
Przetwarzania TDSP jest agile, iteracji, danych dla celów naukowych wykonywania i dostarczanie zaawansowanych rozwiązań analizy. Został zaprojektowany tak, aby poprawić współpracę i wydajności zespołów do nauki o danych w przedsiębiorstwach. Obsługuje ona te cele przy użyciu czterech kluczowych składników:

   * Standardowa [cykl życia analizy danych](../team-data-science-process/lifecycle.md) definicji.
   * Strukturę projektu standardowych [dokumentacji i raportowania szablony projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * Infrastruktura i zasoby dotyczące wykonywania projektu, takiej jak, odpowiednio, zasobów obliczeniowych i magazynu infrastruktury i kodu repozytoriów.
   * [Narzędzia i programy narzędziowe](https://github.com/Azure/Azure-TDSP-Utilities) do analizy danych zadania w projekcie, takich jak:
      - Kontrola wersji współpracy
      - Przegląd kodu
      - Eksplorowanie i modelowanie danych
      - Planowanie pracy

Pełniejsze omówienie przetwarzania TDSP, zobacz [przegląd danych zespołu dla celów naukowych](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Dlaczego należy użyć środowiska TDSP struktury i szablony?
Standaryzacja struktury, cykl życia i projekty do nauki o danych w dokumentacji jest kluczem ułatwienia skutecznych współpracy w zespołach do nauki o danych. Utwórz machine learning projektów za pomocą szablonu TDSP do zapewnienia takiego framework skoordynowanego pracę zespołową.

Wcześniej wydana [repozytorium GitHub przeznaczone dla przetwarzania TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate) aby ułatwić osiągnięcie tych celów. Ale nie było możliwe, do tej pory do utworzenia wystąpienia struktury przetwarzania TDSP i szablonów w ramach narzędzia do analizy danych. Teraz jest możliwa do utworzenia projektu usługi machine learning, tworzącym przetwarzania TDSP Szablony struktury i dokumentację. 

## <a name="things-to-note-before-creating-a-new-project"></a>Zapamiętaj przed utworzeniem nowego projektu
Sprawdź następujące elementy *przed* Tworzenie nowego projektu:
* Przejrzyj uczenia maszynowego przetwarzania TDSP [szablonu](https://aka.ms/tdspamlgithubrepo).
* Zawartość (inne niż co znajduje się już w folderze "dokumenty") są wymagane do mniej niż 25 MB rozmiar. Zobacz uwagi poniżej tej listy.
* Przykład\_folderu danych jest tylko w przypadku małych plików danych (mniej niż 5 MB) za pomocą których można przetestować kod lub rozpocząć projektowanie wcześniejsze.
* Przechowywanie plików, takich jak Word i pliki programu PowerPoint, można znacznie zwiększyć rozmiar folderu "docs". Zaleca się, że znajdowanie współpracy witrynę typu Wiki, [SharePoint](https://products.office.com/sharepoint/collaboration), lub innego zasobu współpracy do przechowywania tych plików.
* Aby dowiedzieć się, jak do obsługi dużych plików i danych wyjściowych w usłudze Machine Learning, przeczytaj [utrwalania zmian i rozwiązywania problemów związanych z dużymi plikami](https://aka.ms/aml-largefiles).

> [!NOTE]
> Wszystkie związane z dokumentacją zawartość (tekst, promocjach cenowych, obrazy i inne pliki dokumentów) *nie* używane podczas wykonywania projektów, innego niż plik readme.md musi znajdować się w folderze o nazwie "docs" (tylko małe litery). Folder "docs" jest ignorowane przez wykonanie uczenia maszynowego, więc zawartość w tym folderze nie uzyskać kopiowana do celów obliczeń niepotrzebnie specjalnego folderu. Obiekty w tym folderze również nie są wliczane do limitu 25 MB rozmiar projektu. Folder "docs" jest na przykład miejsce do przechowywania plików duży obraz, potrzebnych w dokumentacji. Pliki te nadal są śledzone przez usługi Git za pomocą historii uruchamiania. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Utwórz wystąpienie struktury przetwarzania TDSP i szablonów z galerii szablonów usługi Machine Learning
Aby utworzyć nowy projekt za pomocą szablonów środowiska TDSP struktury i dokumentacji, wykonaj następujące procedury.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu
Aby utworzyć nowy projekt, należy otworzyć usługi Azure Machine Learning. W obszarze **projektów** w lewym górnym rogu okienka, wybierz znak plus (**+**), a następnie wybierz pozycję **nowy projekt**.

![Nowy projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Utwórz nowy projekt ze strukturą przetwarzania TDSP
   1. Określ parametry i informacje w odpowiednie pole lub na liście:

      - Project name (Nazwa projektu)
      - Katalog projektu
      - Opis projektu
      - Pusta ścieżka repozytorium Git
      - Nazwa obszaru roboczego

   2. Następnie w **wyszukiwania** wprowadź **TDSP**. 
   3. Gdy **struktury projektu przy użyciu przetwarzania TDSP** opcja jest wyświetlana, zaznacz ten szablon. 
   4. Wybierz **Utwórz** przycisk, aby utworzyć nowy projekt z przetwarzania TDSP struktury. Jeśli podasz puste repozytorium Git podczas tworzenia projektu (w odpowiednim polu tekstowym), a następnie tego repozytorium zostanie wypełniona zawartości i struktury projektu po utworzeniu projektu.

![Utwórz projekt środowiska TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Sprawdź struktury projektu przetwarzania TDSP
Po utworzeniu nowego projektu można sprawdzić jego strukturę (zobacz w lewym panelu na poniższej ilustracji). Zawiera ona wszystkie aspekty standardowych dokumentacji poznawanie firmy. Elementy te zawierają etapy cyklu życia przetwarzania TDSP, lokalizacja danych, definicji i architektury ten szablon dokumentacji. 

Struktura przedstawiona jest tworzony na podstawie struktury przetwarzania TDSP, który został opublikowany w [Szablony struktury, dokumentów i artefaktów projektu przetwarzania TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate), za pomocą niektórych modyfikacji. Na przykład kilka szablonów dokumentów są scalane w jednym języku znaczników markdown, to znaczy, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Struktura folderów projektu
Szablon projektu przetwarzania TDSP zawiera następujące foldery najwyższego poziomu:
   - **Kod**: zawiera kod.
   - **Dokumentacja**: zawiera niezbędne dokumentacji dotyczącej projektu (na przykład pliki markdown i powiązane z).
   - **sample_data**: zawiera **próbki (mała liczba godzin)** dane, które można użyć do początku tworzenia i testowania. Zazwyczaj te zestawy nie są większe niż kilka (5) MB. Ten folder nie jest pełny lub dużych zestawów danych.

![Dane przykładowe](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Skorzystaj z przetwarzania TDSP struktury i szablony
Musisz dodać informacje specyficzne dla projektu do struktury i szablony. W przypadku oczekiwano do wypełniania tych kodem i informacje niezbędne do wykonywania i dostarczania projektu. [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) plików jest szablon, który trzeba będzie zmodyfikować informacje istotne dla projektu. Zawiera on zestaw pytań, które ułatwiają Wypełnij informacje dla każdego z czterech etapów [przetwarzania TDSP w cyklu życia](../team-data-science-process/lifecycle.md).

Przykład struktury projektu wygląda podobnie jak podczas wykonywania lub po zakończeniu jest wyświetlany w lewym panelu na poniższej ilustracji. Ten projekt jest z [zespołu danych dla celów naukowych przykładowy projekt: klasyfikowanie przychodów USA danych spisu w usłudze Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) przykładowy projekt.

![Przykład struktury projektu](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Dokumentowanie projektu
Zapoznaj się [przetwarzania TDSP dokumentacji szablonów](https://github.com/Azure/Azure-TDSP-ProjectTemplate) instrukcje dotyczące sposobu dokumentowania projektu. W bieżącym szablonie dokumentacja usługi Machine Learning TDSP firma Microsoft zaleca, aby uwzględnić wszystkie informacje w [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) pliku. Ten szablon powinien wypełnione informacjami, które są specyficzne dla projektu. 

Firma Microsoft udostępnia również [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) szablonu. Możesz użyć tego szablonu, aby uwzględnić wszystkie informacje, która nie znajduje się w dokumencie projektu podstawowego, ale która nadal jest użyteczne do dokumentu. 

### <a name="example-project-report"></a>Przykładowy raport projektu
Możesz uzyskać [przykładowy projekt raport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Ten raport projektu, aby [USA dochodu klasyfikacji przykładowy projekt](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) pokazuje, jak tworzyć wystąpienie i użyj szablonu TDSP dla projektu nauki o danych.

## <a name="next-steps"></a>Kolejne kroki
Aby ułatwić zrozumienie na temat korzystania z przetwarzania TDSP struktury i szablony w projektach usługi Machine Learning, firma Microsoft zapewnia kilka przykładów projektu ukończone w dokumentacji dotyczącej usługi Machine Learning:

- Dla przykładu, który pokazuje, jak utworzyć projekt przetwarzania TDSP w usłudze Machine Learning, zobacz [zespołu danych dla celów naukowych przykładowy projekt: klasyfikowanie przychodów dane spisu NAS w usłudze Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Dla przykładu, który korzysta z uczenia głębokiego w języka naturalnego (NLP) w projekcie wystąpienia przetwarzania TDSP w usługi Machine Learning, zobacz [rozpoznawania jednostek medycznych biografia przy użyciu języka naturalnego za pomocą uczenia głębokiego](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

