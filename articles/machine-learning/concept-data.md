---
title: Dane w usłudze Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak usługa Azure Machine Learning bezpiecznie łączy się z danymi i używa tych danych do zadań uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128489"
---
# <a name="data-access-in-azure-machine-learning"></a>Dostęp do danych w usłudze Azure Machine Learning

Usługa Azure Machine Learning ułatwia łączenie się z danymi w chmurze.  Zapewnia warstwę abstrakcji nad podstawową usługą magazynu, dzięki czemu można bezpiecznie uzyskać dostęp i pracować z danymi bez konieczności pisania kodu specyficznego dla typu magazynu. Usługa Azure Machine Learning udostępnia również następujące funkcje danych:

*    Przechowywanie wersji i śledzenie linii danych
*    Etykietowanie danych 
*    Monitorowanie dryfu danych
*    Współdziałanie z pandami i ramami danych iskier i spark

## <a name="data-workflow"></a>Przepływ pracy danych

Gdy będziesz gotowy do użycia danych w rozwiązaniu magazynu opartego na chmurze, zalecamy następujący przepływ pracy dostarczania danych. Ten przepływ pracy zakłada, że masz [konto magazynu platformy Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) i dane w usłudze magazynu opartego na chmurze na platformie Azure. 

1. Utwórz [magazyn danych usługi Azure Machine Learning,](#datastores) aby przechowywać informacje o połączeniu z magazynem platformy Azure.

2. Z tego magazynu danych utwórz [zestaw danych usługi Azure Machine Learning,](#datasets) aby wskazać określone pliki w podstawowej przestrzeni dyskowej. 

3. Aby użyć tego zestawu danych w eksperymencie uczenia maszynowego, można
    1. Zamontuj go do celu obliczeniowego eksperymentu do szkolenia modelu.

        **Lub** 

    1. Korzystaj z niego bezpośrednio w rozwiązaniach usługi Azure Machine Learning, takich jak zautomatyzowane testy uczenia maszynowego (zautomatyzowane uczenia maszynowego), potoki uczenia maszynowego lub [projektant usługi Azure Machine Learning.](concept-designer.md)

4. Tworzenie [monitorów zestawu danych](#data-drift) dla zestawu danych wyjściowych modelu w celu wykrycia dryfu danych. 

5. Jeśli zostanie wykryty dryft danych, zaktualizuj wejściowy zestaw danych i odpowiednio przeszkolij model.

Poniższy diagram zawiera wizualną demonstrację tego zalecanego przepływu pracy.

![Diagram koncepcyjny danych](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Magazyny danych

Magazyny danych usługi Azure Machine Learning bezpiecznie przechowują informacje o połączeniu z magazynem platformy Azure, więc nie trzeba go kodować w skryptach. [Zarejestruj się i utwórz magazyn danych,](how-to-access-data.md) aby łatwo połączyć się z kontem magazynu i uzyskać dostęp do danych w podstawowej usłudze magazynu platformy Azure. 

Obsługiwane usługi magazynu opartego na chmurze na platformie Azure, które mogą być zarejestrowane jako magazyny danych:

+ Kontener obiektów blob platformy Azure
+ Udział plików platformy Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ System plików usługi Databricks
+ Azure Database for MySQL

## <a name="datasets"></a>Zestawy danych

Zestawy danych usługi Azure Machine Learning są odwołaniami wskazującymi dane w usłudze magazynu. Nie są to kopie twoich danych, więc nie ponosi żadnych dodatkowych kosztów przechowywania. Aby wchodzić w interakcje z danymi w magazynie, [utwórz zestaw danych,](how-to-create-register-datasets.md) aby spakować dane do obiektu zużywalnej do zadań uczenia maszynowego. Zarejestruj zestaw danych w obszarze roboczym, aby udostępnić go i ponownie wykorzystać w różnych eksperymentach bez złożoności pozyskiwania danych.

Zestawy danych można tworzyć na podstawie plików lokalnych, publicznych adresów URL, [otwartych zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/)lub usług magazynu platformy Azure za pośrednictwem magazynów danych. Aby utworzyć zestaw danych z ramki danych pandas w pamięci, zapisz je w pliku lokalnym, takim jak parkiet, i utwórz zestaw danych z tego pliku.  

Obsługujemy 2 typy zestawów danych: 
+ Zestaw [tabelaryczny](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym, analizując podany plik lub listę plików. Można załadować Zestaw danych tabelaryjskich do Pandas lub Spark DataFrame do dalszej manipulacji i oczyszczania. Aby uzyskać pełną listę formatów danych, z których można utworzyć zestawy danych tabelaryczne, zobacz [klasę TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ [Zestaw danych zawiera](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołania do pojedynczych lub wielu plików w magazynach danych lub publicznych adresach URL. Pliki, do których nie można [pobierać lub montować,](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) do których nie ma odwoływanych przez zestawy plików, można pobrać lub zainstalować do obiektu docelowego obliczeń.

Dodatkowe możliwości zestawów danych można znaleźć w następującej dokumentacji:

+ Linia zestawu danych [wersji i śledzenia.](how-to-version-track-datasets.md)
+ [Monitoruj zestaw danych,](how-to-monitor-datasets.md) aby pomóc w wykrywaniu dryfu danych.    

## <a name="work-with-your-data"></a>Praca z danymi

Dzięki zestawom danych można wykonywać wiele zadań uczenia maszynowego dzięki bezproblemowej integracji z funkcjami usługi Azure Machine Learning. 

+ Tworzenie [projektu etykietowania danych](#label).
+ Trenuj modele uczenia maszynowego:
     + [zautomatyzowane eksperymenty ML](how-to-use-automated-ml-for-ml-models.md)
     + [projektant](tutorial-designer-automobile-price-train-score.md#import-data)
     + [Notebooki](how-to-train-with-datasets.md)
     + [Potoki usługi Azure Machine Learning](how-to-create-your-first-pipeline.md)
+ Dostęp do zestawów danych do oceniania z [wnioskowaniem wsadowym w](how-to-use-parallel-run-step.md) [potokach uczenia maszynowego](how-to-create-your-first-pipeline.md).
+ Skonfiguruj monitor zestawu danych do wykrywania [dryfu danych.](#drift)

<a name="label"></a>

## <a name="data-labeling"></a>Etykietowanie danych

Etykietowanie dużych ilości danych często było bólem głowy w projektach uczenia maszynowego. Osoby z komponentem widzenia komputerowego, takim jak klasyfikacja obrazów lub wykrywanie obiektów, zazwyczaj wymagają tysięcy obrazów i odpowiednich etykiet.

Usługa Azure Machine Learning zapewnia centralną lokalizację do tworzenia projektów etykietowania, zarządzania nimi i monitorowania ich. Projekty etykietowania pomagają koordynować dane, etykiety i członków zespołu, co pozwala na bardziej efektywne zarządzanie zadaniami etykietowania. Obecnie obsługiwane zadania to klasyfikacja obrazów, wieloznakowa lub wieloklasowa oraz identyfikacja obiektów przy użyciu ograniczonych pól.

Utwórz [projekt etykietowania danych](how-to-create-labeling-projects.md)i wyśmij zestaw danych do użycia w eksperymentach uczenia maszynowego.

<a name="drift"></a>

## <a name="data-drift"></a>Dryft danych

W kontekście uczenia maszynowego dryft danych jest zmiana danych wejściowych modelu, która prowadzi do obniżenia wydajności modelu. Jest to jeden z głównych powodów, dla których dokładność modelu pogarsza się w czasie, dzięki czemu monitorowanie dryfu danych pomaga wykrywać problemy z wydajnością modelu.

Zobacz [artykuł Tworzenie monitora zestawu danych,](how-to-monitor-datasets.md) aby dowiedzieć się więcej o wykrywaniu i ostrzeganiu przed dryfem danych na nowych danych w zestawie danych.

## <a name="next-steps"></a>Następne kroki 

+ Utwórz zestaw danych w studio usługi Azure Machine Learning lub przy użyciu zestawu SDK języka [Python, wykonując następujące kroki.](how-to-create-register-datasets.md)
+ Wypróbuj przykłady szkoleń z zestawem danych za pomocą [naszych przykładowych notesów.](https://aka.ms/dataset-tutorial)
+ Przykłady dryfu danych można znaleźć w tym [samouczku dotyczących dryfu danych](https://aka.ms/datadrift-notebook).
