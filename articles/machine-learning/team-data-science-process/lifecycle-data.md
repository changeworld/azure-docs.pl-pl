---
title: Pozyskiwanie i rozumienie procesu nauki o danych zespołu
description: Cele, zadania i rezultaty na etapie pozyskiwania i zrozumienia danych w projektach analityki danych
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720507"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Etap pozyskiwania i zrozumienia danych w procesie nauki o danych zespołu

W tym artykule opisano cele, zadania i rezultaty skojarzone z etapem pozyskiwania i zrozumienia danych procesu nauki o danych zespołu (TDSP). Ten proces zapewnia zalecany cykl życia, którego można użyć do struktury projektów nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj wykonują, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **wdrażania**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Wyprodukuj czysty, wysokiej jakości zestaw danych, którego relacja ze zmiennymi docelowymi jest zrozumiała. Zlokalizuj zestaw danych w odpowiednim środowisku analitycznym, aby można było przystąpić do modelowania.
* Opracowanie architektury rozwiązania potoku danych, który odświeża i regularnie ocenia dane.

## <a name="how-to-do-it"></a>Jak to zrobić
Na tym etapie zajęto się trzema głównymi zadaniami:

   * **Połknąć dane** do docelowego środowiska analitycznego.
   * **Zapoznaj się z danymi,** aby ustalić, czy jakość danych jest odpowiednia, aby odpowiedzieć na pytanie. 
   * **Skonfiguruj potok danych,** aby uzyskać nowe lub regularnie odświeżane dane.

### <a name="ingest-the-data"></a>Połknienie danych
Skonfiguruj proces, aby przenieść dane z lokalizacji źródłowych do lokalizacji docelowych, w których uruchamiasz operacje analityczne, takie jak szkolenia i prognozy. Aby uzyskać szczegółowe informacje techniczne i opcje dotyczące przenoszenia danych za pomocą różnych usług danych platformy Azure, zobacz [Ładowanie danych do środowisk magazynu w celu analizy.](ingest-data.md) 

### <a name="explore-the-data"></a>Eksplorowanie danych
Przed szkoleniem modeli należy opracować rzetelne zrozumienie danych. Rzeczywiste zestawy danych są często hałaśliwe, brakuje wartości lub mają wiele innych rozbieżności. Podsumowanie i wizualizację danych służy do inspekcji jakości danych i dostarczania informacji potrzebnych do przetworzenia danych, zanim będą gotowe do modelowania. Proces ten jest często iteracyjny.

TDSP zapewnia zautomatyzowane narzędzie o nazwie [IDEAR,](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)aby pomóc wizualizować dane i przygotowywać raporty podsumowania danych. Zaleca się, aby rozpocząć z IDEAR pierwszy do eksplorowania danych, aby pomóc w opracowywaniu początkowych danych zrozumienia interaktywnie bez kodowania. Następnie można napisać niestandardowy kod do eksploracji i wizualizacji danych. Aby uzyskać wskazówki dotyczące czyszczenia danych, zobacz [Zadania, aby przygotować dane do rozszerzonego uczenia maszynowego](prepare-data.md).  

Po zadowoleniu z jakości oczyszczonych danych następnym krokiem jest lepsze zrozumienie wzorców, które są nieodłączne w danych. Ta analiza danych pomaga wybrać i opracować odpowiedni model predykcyjny dla obiektu docelowego. Poszukaj dowodów na to, jak dobrze połączone są dane z obiektem docelowym. Następnie należy określić, czy istnieje wystarczająca ilość danych, aby przejść do przodu z następnych kroków modelowania. Ponownie, proces ten jest często iteracyjny. Może być konieczne znalezienie nowych źródeł danych z dokładniejszymi lub bardziej odpowiednimi danymi, aby rozszerzyć zestaw danych wstępnie zidentyfikowanych w poprzednim etapie. 

### <a name="set-up-a-data-pipeline"></a>Konfigurowanie potoku danych
Oprócz początkowego pozyskiwania i czyszczenia danych, zazwyczaj należy skonfigurować proces, aby zdobyć nowe dane lub regularnie odświeżać dane w ramach trwającego procesu uczenia się. Ocenianie może zostać zakończone za pomocą potoku danych lub przepływu pracy. W [artykule Przenieś dane z lokalnego wystąpienia programu SQL Server do usługi Azure SQL Database za pomocą usługi Azure Data Factory](move-sql-azure-adf.md) artykuł podaje przykład konfigurowania potoku za pomocą usługi Azure Data [Factory.](https://azure.microsoft.com/services/data-factory/) 

Na tym etapie można opracować architekturę rozwiązania potoku danych. Tworzenie potoku równolegle z następnym etapem projektu nauki o danych. W zależności od potrzeb biznesowych i ograniczeń istniejących systemów, z którymi to rozwiązanie jest zintegrowane, potok może być jedną z następujących opcji: 

   * Oparte na partiach
   * Przesyłanie strumieniowe lub w czasie rzeczywistym 
   * Hybryda 

## <a name="artifacts"></a>Artefakty
Na tym etapie są następujące elementy:

   * [Raport jakości danych:](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md)Ten raport zawiera podsumowania danych, relacje między każdym atrybutem a celem, ranking zmiennych i inne. Narzędzie [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) dostarczone jako część TDSP może szybko wygenerować ten raport na dowolnym zestawie danych tabelarycznym, takim jak plik CSV lub tabela relacyjna. 
   * **Architektura rozwiązania:** Architektura rozwiązania może być diagram lub opis potoku danych, który służy do uruchamiania oceniania lub prognozowania na nowe dane po utworzeniu modelu. Zawiera również potoku do ponownego przeszkolenia modelu na podstawie nowych danych. Dokument należy przechowywać w katalogu [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) podczas korzystania z szablonu struktury katalogów TDSP.
   * **Decyzja punktu kontrolnego:** Przed rozpoczęciem inżynierii pełnej funkcji i budowania modelu można ponownie ocenić projekt, aby ustalić, czy oczekiwana wartość jest wystarczająca do kontynuowania jego realizacji. Możesz na przykład być gotowy do kontynuowania, trzeba zebrać więcej danych lub porzucić projekt, ponieważ dane nie istnieje, aby odpowiedzieć na pytanie.

## <a name="next-steps"></a>Następne kroki

Oto łącza do każdego kroku w cyklu życia TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [wdrażania](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy pełne wskazówki, które pokazują wszystkie kroki w procesie dla określonych scenariuszy. [Przykładowy](walkthroughs.md) artykuł zawiera listę scenariuszy z łączami i opisami miniatur. W instruktażu pokazano, jak połączyć chmurę, narzędzia lokalne i usługi w przepływie pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w usługach TDSP korzystających z usługi Azure Machine Learning Studio, zobacz [Korzystanie z usługi TDSP z usługą Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data)
