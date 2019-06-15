---
title: Przypadek użycia — profilowanie klientów
description: Dowiedz się, jak usługi Azure Data Factory służy do tworzenia opartych na danych przepływu pracy (potok) do profilowania klientów gier.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: bb7d6531da330bcfbf6de786ffb19984cfd1964e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60487216"
---
# <a name="use-case---customer-profiling"></a>Przypadek użycia — profilowanie klientów
Usługa Azure Data Factory jest jednym z wielu usług, używaną do zaimplementowania pakietu Cortana Intelligence z akceleratorami rozwiązań.  Aby uzyskać więcej informacji na temat pakietu Cortana Intelligence, odwiedź stronę [pakietu Cortana Intelligence](https://www.microsoft.com/cortanaanalytics). W tym dokumencie opisano prosty przypadek użycia, aby pomóc Ci rozpocząć pracę z zrozumienie, jak usługi Azure Data Factory można rozwiązywania typowych problemów analitycznych.

## <a name="scenario"></a>Scenariusz
Contoso to firma gier, który tworzy gry dla wielu platform: gier, konsole, dłoni skierowanej do urządzenia i komputery osobiste (komputery). Ponieważ gracze tych gier, dużej ilości danych dziennika jest generowany, umożliwiający śledzenie wzorców użycia, styl gier i preferencji użytkownika.  W połączeniu z demograficznych, regionalne i dane produktów Contoso można wykonać analizy, które przeprowadzą ich o tym, jak można poprawić funkcjonalność z perspektywy graczy zakupów docelowej je w ramach uaktualnień i gry. 

Celem firmy Contoso polega na określeniu możliwości up sprzedaży/sprzedaży na podstawie historii gier swoich graczy i Dodaj ciekawe funkcje do przyspieszania rozwoju ich firm i zapewnić lepsze doświadczenia klientom. Dla ten przypadek użycia używany na przykład firma tworząca gry. Firma chce zoptymalizować jej gier na podstawie zachowania graczy. Te zasady mają zastosowanie w przypadku każdej firmy, który chce angażować swoich klientów na całym jego towarów i usług oraz ulepszanie środowiska swoim klientom.

W tym rozwiązaniu firma Contoso chce ocena skuteczności kampanii marketingowej, który niedawno został uruchomiony. Firma Microsoft zaczynać od dzienników gier raw, przetwarzania i wzbogacanie je za pomocą dane geolokalizacji, przyłączyć ją z danymi referencyjnymi reklamowych i wreszcie skopiuj je do usługi Azure SQL Database do analizy wpływu kampanii.

## <a name="deploy-solution"></a>Wdrażanie rozwiązania
Wszystko, czego potrzebujesz, aby otworzyć i wypróbować ten prosty przypadek użycia jest [subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/), [konta usługi Azure Blob storage](../../storage/common/storage-quickstart-create-account.md)i [usługi Azure SQL Database](../../sql-database/sql-database-get-started.md). Wdrażanie profilowanie potoku z poziomu klientów **przykładowe potoki** kafelków na stronie głównej fabryki danych.

1. Utwórz fabrykę danych lub Otwórz istniejącą fabrykę danych. Zobacz [kopiowanie danych z magazynu obiektów Blob do usługi SQL Database przy użyciu usługi Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kroki utworzyć fabrykę danych.
2. W **usługi DATA FACTORY** bloku fabryka danych kliknij **przykładowe potoki** kafelka.

    ![Przykładowe potoki kafelka](./media/data-factory-samples/SamplePipelinesTile.png)
3. W **przykładowe potoki** bloku kliknij **profilowanie klientów** , którą chcesz wdrożyć.

    ![Przykładowe potoki bloku](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji dla przykładu. Na przykład Twoja nazwa konta usługi Azure storage i klucz, nazwy serwera Azure SQL, bazy danych, identyfikator użytkownika i hasło.

    ![Przykładowy blok](./media/data-factory-samples/SampleBlade.png)
5. Po wykonaniu Określanie ustawień konfiguracji, kliknij przycisk **Utwórz** do tworzenia/wdrożyć przykładowe potoki i usług/tabel połączonych posługują się potoków.
6. Zostanie wyświetlony stan wdrożenia na kafelku przykładowe wcześniej kliknięta **przykładowe potoki** bloku.

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Po wyświetleniu **wdrażanie zakończyło się pomyślnie** wiadomości na kafelku dla przykładu, Zamknij **przykładowe potoki** bloku.  
8. Na **usługi DATA FACTORY** wyświetlony blok usługi połączone, zestawy danych i potoki są dodawane do usługi data factory.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Omówienie rozwiązania
Ten prosty przypadek użycia może służyć jako przykładu sposobu użycia usługi Azure Data Factory w celu pozyskiwania, przygotowywania, przekształcania, analizowanie i publikować dane.

![Kompletny przepływ pracy](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Na poniższej ilustracji przedstawiono sposób potoków danych pojawiają się w witrynie Azure portal po ich wdrożeniu.

1. **PartitionGameLogsPipeline** odczytuje nieprzetworzonych zdarzeń w grach z magazynu obiektów blob i tworzy partycje oparte na rok, miesiąc i dzień.
2. **EnrichGameLogsPipeline** sprzężenia partycjonowane zdarzeń w grach z danymi referencyjnymi kodu geograficznej i uzupełnia danych przez mapowanie adresów IP do odpowiedniej lokalizacji geograficznej.
3. **AnalyzeMarketingCampaignPipeline** potoku korzysta z danych wzbogaconego i przetwarza je za pomocą danych reklam, aby utworzyć końcowych danych wyjściowych, który zawiera skuteczności kampanii marketingowych.

W tym przykładzie usługi Data Factory służy do organizowania działań, które będzie kopiować dane wejściowe, przekształcanie i przetwarzanie tych danych, a końcowe dane wyjściowe do usługi Azure SQL Database.  Można również wizualizować sieć potoków danych, zarządzanie nimi i monitorowanie ich stan w interfejsie użytkownika.

## <a name="benefits"></a>Korzyści
Optymalizacji ich analizy profilu użytkownika i dostosowanie jej cele biznesowe, gry firmy jest w stanie szybko zbierać wzorców użycia i analizować efektywność swoich kampanii marketingowych.

