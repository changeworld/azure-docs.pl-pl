---
title: Klasyfikacji dochodu — proces nauki danych zespołu - Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak używać szablonu procesu nauki danych Team do tworzenia projektu w usługi Azure Machine Learning klasyfikuje przychodów Stanów Zjednoczonych.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: 48c88f541f650fac3bdec431f3164138fb0f3205
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Klasyfikacja przychody z projektu zespołowego danych nauki procesu (TDSP)

## <a name="introduction"></a>Wprowadzenie

Normalizacji struktury i analizy danych w dokumentacji projektów, który jest zakotwiczona ustalonych [analizy danych w cyklu życia](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), ma kluczowe znaczenie dla ułatwienia skutecznej współpracy w zespołach analizy danych. Tworzenie projektów usługi Azure Machine Learning z [zespołu danych nauki procesu (TDSP)](https://github.com/Azure/Microsoft-TDSP) szablonu zapewnia platformę dla takich normalizacji.

Była wcześniej opublikowane [repozytorium GitHub TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ale nie było możliwe, dopóki teraz do tworzenia wystąpienia struktury TDSP i szablonów w ramach narzędzia analizy danych. Firma Microsoft ma teraz włączone tworzenia projektów usługi Azure Machine Learning, które zostały utworzone z [TDSP struktury i dokumentacja szablonów dla usługi Azure Machine Learning](https://github.com/amlsamples/tdsp). Podano instrukcje dotyczące sposobu używania struktury TDSP i szablonów w usłudze Azure Machine Learning [tutaj](https://aka.ms/how-to-use-tdsp-in-aml). W tym miejscu udostępniamy przykładem jak uczenia maszynowego rzeczywiste projektu można tworzyć przy użyciu struktury TDSP, wypełniane przy użyciu określonego projektu kodu, artefakty i dokumentów i wykonywane w ramach usługi Azure Machine Learning.

## <a name="link-to-github-repository"></a>Połącz z repozytorium GitHub
Firma Microsoft udostępnia podsumowania dokumentacji [tutaj](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) dotyczących przykładu. Bardziej szczegółową dokumentację można znaleźć w witrynie GitHub.

### <a name="purpose"></a>Przeznaczenie
Głównym celem tego przykładu jest przedstawiają sposób tworzenia wystąpienia i wykonać maszynę uczenia projekt za pomocą [zespołu danych nauki procesu (TDSP)](https://github.com/Azure/Microsoft-TDSP) struktury i szablonów w programie Azure Machine Learning. W tym celu używamy dobrze znanego [danych spisu nam 1994 r. z repozytorium UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/adult). Zadanie modelowania jest do prognozowania USA roczne klasy przychody z NAMI spisu informacji (na przykład, wieku, wyścigu, poziom edukacji, kraj pochodzenia, itp.)

### <a name="scope"></a>Zakres
 * Eksploracja danych, szkolenia i wdrażanie modelu uczenia maszynowego, której adres prognozowania problem opisany w przeglądzie przypadek użycia. 
 * Wykonanie projektu w usłudze Azure Machine Learning przy użyciu szablonu zespołu danych nauki procesu (TDSP) z usługi Azure Machine Learning dla tego projektu. Do wykonania projektu i raportowania chcemy użyć cyklu życia TDSP.
 * Operationalization rozwiązanie bezpośrednio z usługi Azure Machine Learning w usługi kontenera platformy Azure.

 Projekt zawiera wyróżnione kilka funkcji usługi Azure Machine Learning takie podczas tworzenia wystąpienia struktury TDSP i użycia, wykonanie kodu z notesów Jupyter, a także pliki języka Python i łatwe operationalization w usługi kontenera platformy Azure przy użyciu rozwiązania Docker i Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Cykl życia procesu nauki danych (TDSP) zespołu
Zobacz [zespołu cyklu życia procesu (TDSP) nauki danych](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="required-subscription-hardware-software"></a>Wymagane: subskrypcji, sprzęt, oprogramowanie
1. Azure [subskrypcji](https://azure.microsoft.com). Możesz uzyskać [wolne subskrypcji](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) można również wykonać w tym przykładzie.
2. [Azure danych nauki maszyny wirtualnej (DSVM) systemu Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (rozmiar maszyny Wirtualnej: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), 4 procesory wirtualne i 14 Gb pamięci RAM). Mimo że przetestowane na DSVM Azure, prawdopodobnie będzie działać na dowolnym komputerze z systemem Windows 10.
3. Przejrzyj dokumentację dotyczącą uczenie maszynowe Azure i jej powiązane usługi (poniżej podano linki).
4. Upewnij się, że poprawnie zainstalowano uczenia maszynowego Azure przez [Przewodnik Szybki start instalacji](../service/quickstart-installation.md).

Zestaw danych dla tego przykładu znajduje się w repozytorium ML UCI [[łącze]](https://archive.ics.uci.edu/ml/datasets/adult). On jest pobierana z bazy danych spisu nam 1994 r. i zawiera informacje o około 50 000 użytkowników indywidualnych spisu i przychody. To strukturze zestawu danych o wartości liczbowych i funkcji podzielone na kategorie i docelowy podzielone na kategorie, składające się z dwóch kategorii dochodu ("> 50 K" lub "< = 50 K"). 

### <a name="optional-version-control-repository"></a>Opcjonalne: Repozytorium kontroli wersji
Jeśli chcesz zapisać i wersji projektu i jego zawartość, musisz mieć repozytorium kontroli wersji, gdy będzie to możliwe. Podczas tworzenia nowego projektu przy użyciu szablonu TDSP w usłudze Azure Machine Learning, można wprowadzić lokalizację repozytorium Git. Zobacz [sposób użycia narzędzia Git w usłudze Azure Machine Learning](using-git-ml-project.md) uzyskać więcej szczegółowych informacji.

### <a name="informational-about-azure-machine-learning"></a>Informacja: O uczenie maszynowe Azure
* [Często zadawane pytania — wprowadzenie](frequently-asked-questions.md)
* [Omówienie](../service/overview-what-is-azure-ml.md)
* [Instalacja](../service/quickstart-installation.md)
* [Wykonanie](experimentation-service-configuration.md)
* [Przy użyciu TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Odczytywanie i zapisywanie plików](how-to-read-write-files.md)
* [Using Git with Azure Machine Learning (Używanie usługi Git z usługą Azure Machine Learning)](using-git-ml-project.md)
* [Wdrażanie model usługi uczenie Maszynowe jako usługi sieci web](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablon:
1.  Otwórz Azure Machine Learning Workbench
2.  Na **projekty** kliknij przycisk **+** podpisywania i wybierz **nowy projekt**
3.  W **Utwórz nowy projekt** okienka, wypełnij informacje dla nowego projektu
4.  W **szablony projektów wyszukiwania** pole wyszukiwania, wpisz "Przychody Klasyfikuj USA - TDSP projektu" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

Jeśli podasz pustą lokalizację repozytorium Git podczas tworzenia projektu (w odpowiednim polu), a następnie tego repozytorium zostanie wypełniona zawartości i struktury projektu po utworzeniu projektu.

## <a name="use-case-overview"></a>Omówienie przypadków użycia
Problem jest, aby zrozumieć, jak socjoekonomicznych dane zarejestrowane w nam spisu może pomóc przewidzieć rocznego dochodu osobom w USA. W oparciu o takich funkcji spisu, zadania uczenia maszyny jest określenie, czy dochód osoby ponad 50 000 $ nie jest (zadanie klasyfikacji binarnej).

## <a name="data-description"></a>Opis elementu danych
Aby uzyskać szczegółowe informacje o danych, zobacz [opis](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) w repozytorium UCI. 

Te dane został wyodrębniony z bazy danych spisu Biuro znaleźć pod adresem: https://www.census.gov/en.html. 


* Istnieją łączną liczbę wystąpień 48,842 (przed żadnego filtrowania), mieszać ciągłych i dyskretnych (uczenia = 32 561, przetestować = 16, 281)
* Prawdopodobieństwo etykiety "> 50 K": 23.93% / 24.78% (bez elementy nieznane)
* Prawdopodobieństwo etykiety "< = 50 K": 76.07% / 75.22% (bez elementy nieznane)  

* **DOCELOWY**: klasa dochodu "> 50 K", "< = 50 K". Te zostały zastąpione przez 1 i 0 odpowiednio w fazie przygotowywania danych.
* **Funkcje**: wiek, klasa pracy, poziom wykształcenia, poziom wykształcenia, wyścigu, płeć, godzin pracy na tydzień itp.


## <a name="project-structure-execution-and-reporting"></a>Struktury projektu, wykonanie i raportowanie

### <a name="structure"></a>Struktura
Dla tego projektu, możemy użyć TDSP folderu struktury i dokumentacji szablonów (patrz poniżej), które następujące [cyklu życia TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projekt jest tworzone w oparciu instrukcjami [tutaj](https://aka.ms/how-to-use-tdsp-in-aml). Po jest wypełniany kodu projektu i artefaktów, struktura wygląda następująco (patrz struktury projektu opakowany na czerwono na poniższym rysunku).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Wykonanie
W tym przykładzie mamy wykonanie kodu w **lokalnym środowisku**. Odwołuje się do usługi Azure Machine Learning dokumentów, aby uzyskać więcej informacji na [opcje wykonywania](experimentation-service-configuration.md).

Wykonywanie skryptu języka Python w lokalnym środowisku uruchomieniowym języka Python jest prosty:

    az ml experiment submit -c local my_script.py

Pliki notesu IPython można dwukrotnie kliknięty ze struktury projektu po lewej stronie interfejsu użytkownika Azure Machine Learning i uruchom na serwerze Jypyter notesu.


Pracy do analizy danych krok został w następujący sposób:

* [**Uzyskiwanie danych i zrozumienie**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Pobrano dane w postaci pliku CSV z adresów URL w repozytorium ML UCI [[łącze]](https://archive.ics.uci.edu/ml/datasets/adult). Funkcje i celu oraz ich przekształcenia opisano szczegółowo w pliku ProjectReport.md.

Kod gromadzenia danych i zrozumienie znajduje się w: / kod/01_data_acquisition_and_understanding.

Eksploracja danych odbywa się przy użyciu Python 3 [IDEAR (interaktywne Eksplorowanie danych oraz raportowania) Narzędzie](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) opublikowane jako część [TDSP pakietu narzędzi do analizy danych](https://github.com/Azure/Azure-TDSP-Utilities). Pomaga to narzędzie do generowania raportów Eksploracja danych standardowych danych zawierający funkcje numeryczne i podzielone na kategorie i obiekt docelowy. Szczegółowe informacje dotyczące korzystania z narzędzia Python 3 IDEAR podano poniżej. 

Lokalizacja raportu Eksploracja danych końcowych jest [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). Widok raportu IDEAR przedstawiono poniżej:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modeling**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Utworzyliśmy dwa modele z 3-fold krzyżowego sprawdzania poprawności: elastycznej Net oraz losowe lasu. My używamy [próbkowania 59 punktu](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) wyszukiwania losowe siatki jako strategii optymalizacji parametr krzyżowego sprawdzania poprawności i modelu. Dokładność modeli zostały zmierzone za pomocą AUC (obszarze krzywej) na testowego zestawu danych. 

Kod dla modelowania znajduje się w: / kod/02_modeling.

AUC elastycznej Net i modele lasu losowe zostały > 0.85. Firma Microsoft zapisywania oba modele pickled.pkl pliki i dane wyjściowe, które ROC geograficzne dla obu modeli. Model AUC losowe lasu było 0.92 i który Net elastyczny model został 0,90. Interpretacji modelu funkcji znaczenie dla model lasu losowe są dodatkowo danych wyjściowych w pliku CSV i pdf (pierwsza 20. wg funkcji predykcyjnej tylko) na wykresie.

Krzywą ROC **model lasu losowe** na test dane są wyświetlane poniżej. To model, który został wdrożony:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Poniżej przedstawiono znaczenie funkcji (u góry 20) model lasu losowych. Zawiera funkcje kwota kapitału korzyści, eduction, stanu cywilnego, mieć najwyższą ważność funkcji.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**wdrożenia**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Wdrożyliśmy model lasu losowe jako usługę sieci web w klastrze w [usługi kontenera platformy Azure (ACS)](https://azure.microsoft.com/services/container-service/). Operationalization środowiska przepisy Docker i Kubernetes w klastrze, aby zarządzać wdrażaniem usługi sieci web. Można znaleźć dodatkowe informacje na temat procesu operationalization [tutaj](model-management-service-deploy.md). 

Kod dla wdrożenia znajduje się w: / kod/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Raport końcowy projektu](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Szczegółowe informacje o każdym z powyższej sekcji znajdują się w raporcie skompilowany projekt końcowego [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Raport projektu zawiera także szczegółowe informacje na temat przypadek użycia, metryki wydajności modelu, wdrożenia i infrastruktury, na którym projekt został opracowany i wdrożone.

Raport projektu oraz zawartość folderu całego projektu i wersji repozytorium kontroli mogą być dostarczane do klienta.


## <a name="conclusion"></a>Podsumowanie

W tym przykładzie pokazano, możemy teraz, aby używać struktury TDSP i szablonów w usłudze Azure Machine Learning. Za pomocą szablonów dokumentów i artefaktu można wykonać następujące czynności:
1. Poprawnie zdefiniować cel i zakres projektu
2. Tworzenie zespołu projektu z rozproszonego role i obowiązki
3. Struktury i wykonywanie projektów zgodnie z TDSP etapy cyklu życia
4. Opracowywanie standardowych raportów za pomocą narzędzi analizy danych TDSP (na przykład IDEAR danych eksploracji i wizualizacja raportu).
5. Przygotowanie raportu projektu nauki ostateczne dane mogą być dostarczane do klienta

Mamy nadzieję, że ułatwienia normalizacji i współpracy w obrębie zespołów nauki danych za pomocą tej funkcji usługi Azure Machine Learning.

## <a name="next-steps"></a>Następne kroki

Zobacz informacje poniżej, aby rozpocząć:

[Jak używać zespołu danych nauki procesu (TDSP) w usłudze Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Proces nauki danych zespołu (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Szablon projektu TDSP dla usługi Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Przychód USA zestaw danych z repozytorium UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)