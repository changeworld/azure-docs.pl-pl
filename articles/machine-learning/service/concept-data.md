---
title: Dane w Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning współdziałać z danymi i jak są wykorzystywane przez eksperymenty uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4ea0f7092862ef910e9f44e81cc4f5f40849bc70
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822957"
---
# <a name="data-access-in-azure-machine-learning"></a>Dostęp do danych w Azure Machine Learning

Ten artykuł zawiera informacje o rozwiązaniach do zarządzania danymi i integracji z programem Azure Machine Learning. W tym artykule przyjęto założenie, że utworzono już [konto usługi Azure Storage](https://docs.microsoft.comazure/storage/common/storage-quickstart-create-account?tabs=azure-portal) i [usługę Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Gdy wszystko będzie gotowe do użycia danych w magazynie, zalecamy

1. Utwórz magazyn danych Azure Machine Learning.
2. Z tego magazynu danych Utwórz zestaw danych Azure Machine Learning. 
3. Użyj tego zestawu danych w doświadczeniu uczenia maszynowego, wykonując jedną z tych opcji 
    1. Instalowanie jej w celu obliczeń na potrzeby szkolenia modelu

        **OR** 

    1. Korzystanie z niego bezpośrednio w Azure Machine Learning rozwiązaniach, takich jak zautomatyzowany przebiegi eksperymentów maszynowych (zautomatyzowany ML), potoki uczenia maszynowego i [projektant Azure Machine Learning](concept-designer.md).
4. Utwórz monitory zestawu danych dla danych wejściowych i wyjściowych modelu, aby wykryć dryf danych. 
5. Jeśli wykryto dryfowanie danych, zaktualizuj zestaw danych i odpowiednio poszkol model.

Na poniższym diagramie przedstawiono wizualizację tego zalecanego przepływu pracy dostępu do danych.

![Diagram koncepcji danych](media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Dostęp do danych w magazynie

Aby uzyskać dostęp do danych na koncie magazynu, Azure Machine Learning oferuje magazyny i zestawy danych. Magazyny danych zapewniają warstwę abstrakcji za pośrednictwem usługi magazynu. Ta pomoc w zakresie zabezpieczeń i łatwego dostępu do magazynu, ponieważ informacje o połączeniu są przechowywane w magazynie danych i nie są dostępne w skryptach. Zestawy danych wskazują określony plik lub pliki w źródłowym magazynie, które mają być używane na potrzeby eksperymentu uczenia maszynowego. Razem magazyny danych i zestawy danych oferują bezpieczne, skalowalne i odtwarzalne przepływy pracy dostarczania dla zadań uczenia maszynowego.

### <a name="datastores"></a>Magazynów danych

Magazyn danych Azure Machine Learning jest abstrakcją magazynu za pośrednictwem usług Azure Storage. [Zarejestruj i Utwórz magazyn](how-to-access-data.md) danych, aby łatwo połączyć się z kontem usługi Azure Storage i uzyskać dostęp do danych w podstawowych usługach Azure Storage.

Obsługiwane usługi magazynu platformy Azure, które można zarejestrować jako magazyny danych:
+ Kontener obiektów blob platformy Azure
+ Udział plików platformy Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ System plików usługi Databricks
+ Azure Database for MySQL

### <a name="datasets"></a>Zestawy danych

[Utwórz Azure Machine Learning zestaw danych](how-to-create-register-datasets.md) , aby współtworzyć dane w Twoich magazynach danych i spakować dane do obiektu, który można wykorzystywać do wykonywania zadań uczenia maszynowego. Zarejestruj zestaw danych w obszarze roboczym, aby udostępnić go i ponownie użyć w różnych eksperymentach bez złożonych danych.

Zestawy danych mogą być tworzone na podstawie plików lokalnych, publicznych adresów URL, [otwartych zestawów danych platformy Azure](#open)lub określonych plików w magazynach danych. Aby utworzyć zestaw danych na podstawie Pandas pamięci Dataframe, Zapisz dane w lokalnym pliku, takim jak wolumin CSV, i Utwórz zestaw danych z tego pliku. Zbiory danych nie są kopiami, ale odwołują się do danych w usłudze Storage, więc nie są naliczane żadne dodatkowe koszty związane z magazynem. 

Na poniższym diagramie przedstawiono, że jeśli nie masz usługi Azure Storage, możesz utworzyć zestaw danych bezpośrednio z plików lokalnych, publicznych adresów URL lub otwartego zestawu danych platformy Azure. Spowoduje to połączenie zestawu danych z domyślnym magazynem obiektów, który został automatycznie utworzony przy użyciu [obszaru roboczego Azure Machine Learning](concept-workspace.md)eksperymentu.

![Diagram koncepcji danych](media/concept-data/dataset-workflow.svg)

Dodatkowe możliwości zestawów danych można znaleźć w następującej dokumentacji:

+ [Wersja i śledzenie](how-to-version-track-datasets.md) zestawu danych.
+ [Monitoruj zestaw danych](how-to-monitor-datasets.md) , aby pomóc w wykrywaniu dryfowania danych.
+  Aby uzyskać dokumentację dotyczącą dwóch typów zestawów danych, zobacz następujące tematy:
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) reprezentuje dane w formacie tabelarycznym przez analizowanie dostarczonego pliku lub listy plików. Umożliwia zmaterializowania danych do Pandas lub Spark Dataframe w celu dalszej manipulacji i czyszczenia. Aby uzyskać pełną listę plików, z których można utworzyć TabularDatasets, zobacz [Klasa TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Za pomocą tej metody można pobrać lub zainstalować wybrane pliki do obiektu docelowego obliczeń jako obiekt FileDataset.

## <a name="work-with-your-data"></a>Pracuj z danymi

Zestawy danych umożliwiają wykonywanie wielu zadań uczenia maszynowego poprzez bezproblemowe integrację z funkcjami Azure Machine Learning. 

+ [Uczenie modeli uczenia maszynowego](how-to-train-with-datasets.md).
+ Korzystanie z zestawów danych w 
     + [zautomatyzowane eksperymenty ML](how-to-create-portal-experiments.md)
     + [Projektant](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Uzyskaj dostęp do zestawów danych do oceniania za pomocą wnioskowania wsadowego w [potokach uczenia maszynowego](how-to-create-your-first-pipeline.md).
+ Utwórz [projekt etykietowania danych](#label).
+ Skonfiguruj monitor zestawu danych na potrzeby wykrywania [dryfowania danych](#drift) .

<a name="open"></a>

## <a name="azure-open-datasets"></a>Zestawy danych licencji Azure Open

[Otwarte zestawy danych platformy Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i są zintegrowane z Azure Machine Learning. Możesz również uzyskać dostęp do zestawów danych za pomocą interfejsów API i używać ich w innych produktach, takich jak Power BI i Azure Data Factory.

Otwarte zestawy danych platformy Azure obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Możesz również udostępnić publiczne zestawy danych na platformie Azure Otwórz zestaw danych.

<a name="label"></a>

## <a name="data-labeling"></a>Etykietowanie danych

Etykietowanie dużych ilości danych często było kłopotliwej w projektach uczenia maszynowego. Te ze składnikiem przetwarzania obrazów, takie jak Klasyfikacja obrazu lub wykrywanie obiektów, zwykle wymagają tysięcy obrazów i odpowiednich etykiet.

Azure Machine Learning stanowi centralną lokalizację do tworzenia i monitorowania projektów etykietowania oraz zarządzania nimi. Etykietowanie projektów ułatwia koordynowanie danych, etykiet i członków zespołu, co pozwala efektywniej zarządzać zadaniami etykietowania. Obecnie obsługiwane zadania to klasyfikacja obrazów, wiele etykiet lub wiele klas oraz identyfikacja obiektów przy użyciu powiązanych pól.

+ Utwórz [projekt etykietowania danych](how-to-create-labeling-projects.md)i wyjściowy zestaw danych do użycia w eksperymentach uczenia maszynowego.

<a name="drift"></a>

## <a name="data-drift"></a>Dryfowanie danych

W kontekście uczenia maszynowego, dryfowanie danych to zmiana danych wejściowych modelu, które prowadzą do obniżenia wydajności modelu. Jest to jedna z najważniejszych przyczyn, które pogorszą się w miarę upływu czasu, dlatego monitorowanie dryfowania danych pomaga wykrywać problemy z wydajnością modelu.
Zapoznaj się z artykułem [Tworzenie monitora zestawu danych](how-to-monitor-datasets.md) , aby dowiedzieć się więcej na temat wykrywania i generowania alertów dotyczących dryfowania danych w nowych danych w zestawie danych.

## <a name="next-steps"></a>Następne kroki 

+ Utwórz zestaw danych w programie Azure Machine Learning Studio lub z zestawem SDK języka Python, [wykonując następujące kroki.](how-to-create-register-datasets.md)
+ Wypróbuj przykłady szkoleniowe dotyczące zestawu danych, korzystając z naszych [przykładowych notesów](https://aka.ms/dataset-tutorial).
+ Aby zapoznać się z przykładami dryfowania danych, zobacz ten [Samouczek dotyczący dryfowania danych](https://aka.ms/datadrift-notebook).