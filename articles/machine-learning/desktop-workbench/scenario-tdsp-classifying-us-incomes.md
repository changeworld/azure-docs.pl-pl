---
title: Klasyfikacja dochodu - zespołu danych dla celów naukowych — usługa Azure Machine Learning | Dokumentacja firmy Microsoft
description: Jak utworzyć projekt w usłudze Azure Machine Learning, które klasyfikują przychodów USA za pomocą szablonu procesu do nauki o danych zespołu.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: edc3fc5e2a625a14bcb48b03f32cd99069a0ad53
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42058271"
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Klasyfikacja dochodu z projektem zespołu Data Science naukowych

## <a name="introduction"></a>Wprowadzenie

Normalizacji struktury i dokumentacji do nauki o danych projektów, to znaczy zakotwiczona ustanowionych [cykl życia analizy danych](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), ma kluczowe znaczenie dla ułatwienia skutecznych współpracy w usłudze teams do nauki o danych. Tworzenie projektów usługi Azure Machine Learning za pomocą [Team Data Science naukowych](https://github.com/Azure/Microsoft-TDSP) szablonu zapewnia platformę do takich normalizacji.

Firma Microsoft ma wcześniej wydane [repozytorium GitHub przeznaczone dla przetwarzania TDSP struktury projektu i szablony](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Ale nie było możliwe, dopóki teraz utworzyć wystąpienie przetwarzania TDSP struktury i szablony w narzędziu do nauki o danych. Teraz włączyliśmy tworzenia projektów usługi Azure Machine Learning, które są tworzone za pomocą [przetwarzania TDSP struktury i dokumentacji szablonów dla usługi Azure Machine Learning](https://github.com/amlsamples/tdsp). Podano instrukcje dotyczące sposobu używania przetwarzania TDSP struktury i szablony w usłudze Azure Machine Learning [tutaj](https://aka.ms/how-to-use-tdsp-in-aml). W tym miejscu udostępniamy przykład jak uczenie maszynowe rzeczywistego projektu można tworzyć przy użyciu przetwarzania TDSP struktury, wypełniony kod specyficzny dla projektu, artefakty i dokumentów i wykonywane w ramach usługi Azure Machine Learning.

## <a name="link-to-github-repository"></a>Połącz z repozytorium GitHub
Firma Microsoft zapewnia podsumowania dokumentacji [tutaj](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) dotyczących przykładu. Bardziej szczegółową dokumentację można znaleźć w witrynie GitHub.

### <a name="purpose"></a>Przeznaczenie
Głównym celem tego przykładu jest przedstawiają sposób tworzenia wystąpienia i wykonywania usługi machine learning projekt za pomocą [Team Data Science naukowych](https://github.com/Azure/Microsoft-TDSP) struktury i szablony usługi Azure Machine Learning. W tym celu użyjemy dobrze znanych [1994 r. spisu nam dane z repozytorium Learning maszyny na](https://archive.ics.uci.edu/ml/datasets/adult). Zadanie modelowania jest prognozowanie, roczne klasy dochodu USA z nam spisu informacji (na przykład, wiek, wyścigu, poziomie wykształcenia, kraju pochodzenia, itp.)

### <a name="scope"></a>Zakres
 * Eksploracja danych, szkolenia i wdrażania modelu uczenia maszynowego, która rozwiązać problem prognozowania opisanego w omówieniu przypadku użycia. 
 * Wykonywanie projektu w usłudze Azure Machine Learning, używając szablonu Team Data Science naukowych z usługi Azure Machine Learning dla tego projektu. Wykonywanie projektu i raportowania będziemy używać cyklu przetwarzania TDSP.
 * Operacjonalizacja rozwiązania bezpośrednio z usługi Azure Machine Learning w usłudze Azure Container Service.

 Projekt prezentuje kilka funkcji usługi Azure Machine Learning, takie tworzenia wystąpienia struktury przetwarzania TDSP i użycia, wykonywanie kodu w notesów programu Jupyter, a także pliki języka Python i łatwe operacjonalizacji w usłudze Azure Container Service przy użyciu platformy Docker i Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Cykl życia danych do analizy naukowych zespołu
Zobacz [zespołu cykl życia naukowych (TDSP) do nauki o danych](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Wymagania wstępne
### <a name="required-subscription-hardware-software"></a>Wymagane: subskrypcja, sprzęt, oprogramowanie
1. Azure [subskrypcji](https://azure.microsoft.com). Możesz uzyskać [bezpłatnej subskrypcji](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) można również wykonać w tym przykładzie.
2. [Azure danych do analizy maszyny wirtualnej (DSVM) systemu Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm), (rozmiar maszyny Wirtualnej: [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)z 4 procesory wirtualne i 14 Gb pamięci RAM). Mimo że przetestowane na DSVM platformy Azure, prawdopodobnie działać na dowolnym komputerze z systemem Windows 10.
3. Zapoznaj się z dokumentacją usługi Azure Machine Learning i jej powiązane usługi (poniżej podano linki).
4. Upewnij się, że poprawnie zainstalowano usługi Azure Machine Learning, [przewodnika Szybki start dotyczącego instalacji](../service/quickstart-installation.md).

Zestaw danych dla tego przykładu jest z repozytorium ML UCI [[łącze]](https://archive.ics.uci.edu/ml/datasets/adult). On jest pobierana z bazy danych spisu nam 1994 r. i zawiera informacje o około 50 000 użytkowników indywidualnych spisu i przychody. Jest to ze strukturą zestaw danych o wartości liczbowych, funkcjami podzielonych na kategorie i docelowej podzielonych na kategorie, składający się z dwóch kategorii dochodu ("> 50 tys" lub "< = 50 K"). 

### <a name="optional-version-control-repository"></a>Opcjonalne: Repozytorium kontroli wersji
Jeśli chcesz zapisać i wersji projektu i jego zawartość, musisz mieć repozytorium kontroli wersji, gdzie można to zrobić. Podczas tworzenia nowego projektu za pomocą szablonu TDSP w usłudze Azure Machine Learning, można wprowadzić lokalizację repozytorium Git. Zobacz [jak za pomocą narzędzia Git w usłudze Azure Machine Learning](using-git-ml-project.md) więcej szczegółowych informacji.

### <a name="informational-about-azure-machine-learning"></a>Komunikat informacyjny: O usłudze Azure Machine Learning
* [Często zadawane pytania — jak rozpocząć pracę](frequently-asked-questions.md)
* [Omówienie](../service/overview-what-is-azure-ml.md)
* [Instalacja](../service/quickstart-installation.md)
* [Wykonanie](experimentation-service-configuration.md)
* [Przy użyciu przetwarzania TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Odczytywanie i zapisywanie plików](how-to-read-write-files.md)
* [Using Git with Azure Machine Learning (Używanie usługi Git z usługą Azure Machine Learning)](using-git-ml-project.md)
* [Wdrażanie modeli uczenia Maszynowego jako usługi sieci web](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Utwórz nowy projekt aplikacji workbench

Utwórz nowy projekt za pomocą tego przykładu jako szablonu:
1.  Otwieranie usługi Azure Machine Learning Workbench
2.  Na **projektów** kliknij **+** zalogować się i wybrać **nowy projekt**
3.  W **Utwórz nowy projekt** okienku, wprowadź informacje dla nowego projektu
4.  W **Wyszukaj szablony projektów** polu wyszukiwania wpisz "Przychody Klasyfikuj stany USA — przetwarzanie TDSP projektu" i wybierz szablon
5.  Kliknij przycisk **Utwórz**

Jeśli podasz pustą lokalizację repozytorium Git podczas tworzenia projektu (w odpowiednim polu), a następnie tego repozytorium zostanie wypełniony zawartości i struktury projektu po utworzeniu projektu.

## <a name="use-case-overview"></a>Omówienie przypadków użycia
Problem polega na tym, aby zrozumieć, jak socjoekonomiczne dane przechwycone w USA spisu można ułatwiania prognozowania roczny przychód poszczególne osoby w Stanach Zjednoczonych. Oparte na takich funkcji spisu, zadanie uczenia maszyny jest do prognozowania, jeśli dochód osoba jest ponad 50 000 zł, czy nie (Klasyfikacja binarna zadanie).

## <a name="data-description"></a>Opis danych
Aby uzyskać szczegółowe informacje o danych, zobacz [opis](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) w repozytorium UCI. 

Te dane został wyodrębniony z bazy danych spisu Biuro adrese: https://www.census.gov/en.html. 


* Ma w sumie 48,842 wystąpienia (przed żadnego filtrowania), mieszać ciągła i dyskretnych (szkolenie = 32 561, test = 16, 281)
* Prawdopodobieństwo etykiety "> 50 K': 23.93% / 24.78% (bez elementy nieznane)
* Prawdopodobieństwo etykiety "< = 50 K': 76.07% / 75.22% (bez elementy nieznane)  

* **DOCELOWY**: klasa dochodu "> 50 tys", "< = 50 K". Te są zastępowane przez 1 i 0 odpowiednio w fazie przygotowywania danych.
* **Funkcje**: wiek, klasa pracy, poziomie wykształcenia, poziomie wykształcenia, wyścigu, płeć, godzin pracy w ciągu tygodnia itp.


## <a name="project-structure-execution-and-reporting"></a>Struktura projektu, wykonywania i raportowanie

### <a name="structure"></a>Struktura
Dla tego projektu, używamy przetwarzania TDSP folder struktury i dokumentacji szablonów (patrz poniżej), który następuje [przetwarzania TDSP w cyklu życia](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Projekt jest oparta na instrukcjami [tutaj](https://aka.ms/how-to-use-tdsp-in-aml). Po wypełnieniu z kodem i artefakty projektu strukturę wyglądają następująco (patrz struktury projektu opakowany na czerwono na poniższej ilustracji).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Wykonanie
W tym przykładzie wykonywania kodu w **środowiska obliczeniowego lokalnego**. Odnoszą się do dokumentów usługi Azure Machine Learning, aby uzyskać więcej informacji na [opcje wykonywania](experimentation-service-configuration.md).

Wykonywanie skryptu w języku Python w lokalnym środowisku uruchomieniowym Python jest łatwe:

    az ml experiment submit -c local my_script.py

Program IPython notebook plików można dwukrotnym kliknięciu ze struktury projektu w lewej części interfejsu użytkownika usługi Azure Machine Learning i uruchomić na serwerze notes Jypyter.


Przepływ nauki krok po kroku danych zakończyło się w następujący sposób:

* [**Pozyskiwanie danych i opis**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Pobrano dane w postaci pliku CSV z adresów URL w repozytorium ML UCI [[łącze]](https://archive.ics.uci.edu/ml/datasets/adult). Funkcje i celu oraz ich przekształcenia zostały szczegółowo opisane w pliku ProjectReport.md.

Kod dla pozyskiwanie danych i opis znajduje się w: / kod/01_data_acquisition_and_understanding.

Eksploracja danych odbywa się przy użyciu Python 3 [IDEAR (interakcyjna Eksploracja danych i raportowania) Narzędzie](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) opublikowane jako część [przetwarzania TDSP zestawu narzędzi nauki o danych](https://github.com/Azure/Azure-TDSP-Utilities). Pomaga to narzędzie do generowania raportów eksploracji danych standardowych dla danych zawierających funkcje numeryczne i podzielonych na kategorie i docelowej. Szczegóły dotyczące używania narzędzia języka Python 3 IDEAR znajduje się poniżej. 

Lokalizacja raportu eksploracji danych końcowych jest [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). Widok raportu IDEAR znajdują się poniżej:

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modeling**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Utworzyliśmy dwa modele z 3-fold krzyżowego sprawdzania poprawności: elastyczne Net oraz Random lasu. Użyliśmy [próbkowanie punktu 59](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) wyszukiwania losowe siatki jako strategii optymalizacji parametr krzyżowego sprawdzania poprawności i modelu. Dokładność modele zostały mierzone przy użyciu AUC (powierzchni pod krzywą) na testowego zestawu danych. 

Kod dla modelowania znajduje się w: / kod/02_modeling.

AUC elastycznej netto i modele lasu Random były 0,85 >. Oszczędzamy oba modele w plikach pickled.pkl i danych wyjściowych, który ROC, które w przypadku obu modeli. Model AUC losowych lasu było 0.92 i elastycznych netto modelu było 0,90. Ponadto interpretacji modelu funkcji znaczenie dla model lasu Random są dane wyjściowe w pliku CSV i wykreślane w formacie pdf (20 najpopularniejszych funkcji predykcyjne tylko).

Krzywa ROC **model lasu Random** na test danych znajdują się poniżej. Jest to model, który został wdrożony:

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

Poniżej przedstawiono model lasu Random funkcji znaczenie (pierwsze 20). Pokazuje funkcje kapitału, przyrost ilości, eduction, stan cywilny, mają najwyższą ważność funkcji.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Wdrożenia**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Wdrożone jako usługę internetową w klastrze w model lasu Random [usługi Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Środowisko obsługi operacji Inicjuje obsługę platformy Docker i Kubernetes w klastrze, aby zarządzać wdrożeniem usługi sieci web. Można znaleźć więcej informacji na temat procesu operacjonalizacji [tutaj](model-management-service-deploy.md). 

Kod dla wdrożenia znajduje się w: / kod/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Raport końcowy projektu](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Szczegółowe informacje o każdej z powyższej sekcjach znajdują się w raporcie skompilowanego ostatecznego projektu [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Raport projektu zawiera także szczegółowe informacje na temat przypadek użycia, metryki wydajności modelu, wdrożenie i infrastruktury, na którym opracowane i wdrożyć projektu.

Raport projektu wraz z zawartości całego folderu projektu, a wersja repozytorium kontroli może być dostarczony do klienta.


## <a name="conclusion"></a>Podsumowanie

W tym przykładzie pokazaliśmy teraz, aby użyć środowiska TDSP struktury i szablony w usłudze Azure Machine Learning. Za pomocą szablonów i artefaktów dokumentów można wykonywać następujące czynności:
1. Poprawnie zdefiniować cel i zakres projektu
2. Tworzenie zespołu projektu za pomocą rozproszonej role i obowiązki
3. Struktury, a następnie wykonaj projekty zgodnie z przetwarzania TDSP etapy cyklu życia
4. Twórz standardowe raporty za pomocą narzędzi nauki o danych przetwarzania TDSP, (na przykład IDEAR danych eksploracji i wizualizacji raportu).
5. Przygotowywanie raportu projektu do nauki o danych końcowych, które mogą być dostarczane do klienta

Mamy nadzieję, że używasz tej funkcji usługi Azure Machine Learning ułatwienia z Normalizacyjna i współpracę w obrębie zespołów do nauki o danych.

## <a name="next-steps"></a>Następne kroki

Zobacz informacje poniżej, aby rozpocząć pracę:

[Jak używać Team Data Science naukowych w usłudze Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[Zespołowe przetwarzanie danych naukowych (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[Szablon projektu przetwarzania TDSP dla usługi Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Dochodu USA zestawu danych z repozytorium UCI ML](https://archive.ics.uci.edu/ml/datasets/adult)