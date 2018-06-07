---
title: Przypadek użycia — profilowanie klientów
description: Dowiedz się, jak fabryki danych Azure służy do tworzenia opartych na danych przepływu pracy (potoku) do profilu gier klientów.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: cc9dad4584c8edc47181e4a73ffe11a2e08de2f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620816"
---
# <a name="use-case---customer-profiling"></a>Przypadek użycia — profilowanie klientów
Fabryka danych Azure to jedna z wielu usług, używaną do zaimplementowania pakietu Cortana Intelligence Suite akceleratorów rozwiązania.  Aby uzyskać więcej informacji na temat Cortana Intelligence, odwiedź stronę [pakietu Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). W tym dokumencie opisano przypadek użycia prostego, aby pomóc Ci rozpocząć zrozumienie, jak fabryki danych Azure można rozwiązywania typowych problemów analytics.

## <a name="scenario"></a>Scenariusz
Contoso to firma gier, która tworzy gry dla wielu platform: gier konsole, ręcznie posiadanych urządzeń i komputerów osobistych (komputery). Jak gry te odtwarzacze, duża ilość danych dziennika jest tworzony śledzący wzorce użycia, gier, styl i preferencje użytkownika.  W połączeniu z demograficznych, regionalnych i danych produktu Contoso można wykonywać analizy prowadzące ich temat udoskonalenie graczy docelowe ich do uaktualnienia i w grze zakupów. 

Celem firmy Contoso jest określenie możliwości up sprzedaje/sprzedaży w oparciu o historię gier jego odtwarzaczy i dodawania atrakcyjnych funkcji do rozwoju firmy dysku i skuteczniejsze do klientów. Dla tego przypadku użycia używamy firmy gier na przykład firma. Firma chce, aby zoptymalizować jego gry, w oparciu o zachowanie graczy. Te zasady mają zastosowanie do dowolnego rodzaju działalności, które chce zaangażowania klientów wokół jego towarów i usług oraz ulepszanie środowiska swoich klientów.

W tym rozwiązaniu firma Contoso chce ocenić skuteczność marketing kampanii, która została ostatnio uruchomiona. Możemy zaczynać dzienniki raw gier, przetworzyć i wzbogacić je z danymi używanie funkcji geolokalizacji przyłączyć go z danych referencyjnych reklamy i koniec skopiuj je do bazy danych SQL Azure do analizy wpływu kampanii.

## <a name="deploy-solution"></a>Wdrażanie rozwiązania
Dostęp i wypróbować ten przypadek użycia prostego wystarczy [subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/), [konta magazynu obiektów Blob Azure](../../storage/common/storage-create-storage-account.md#create-a-storage-account)i [bazy danych SQL Azure](../../sql-database/sql-database-get-started.md). Wdrażanie klienta profilowania potoku z **przykładowe potoki** kafelka na stronie głównej w fabryce danych.

1. Tworzenie fabryki danych lub Otwórz istniejącą fabrykę danych. Zobacz [skopiowanie danych z magazynu obiektów Blob do bazy danych SQL przy użyciu fabryki danych](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) czynności można utworzyć fabryki danych.
2. W **FABRYKI danych** bloku dla fabryki danych, kliknij przycisk **przykładowe potoki** kafelka.

    ![Przykładowe potoki kafelka](./media/data-factory-samples/SamplePipelinesTile.png)
3. W **przykładowe potoki** bloku, kliknij przycisk **klienta profilowania** , który chcesz wdrożyć.

    ![Przykład potoki bloku](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji przykładowej. Na przykład z nazwy konta magazynu Azure i klucza, nazwy serwera Azure SQL bazy danych, identyfikator użytkownika i hasło.

    ![Przykładowe bloku](./media/data-factory-samples/SampleBlade.png)
5. Po wykonaniu Określanie ustawień konfiguracji, kliknij przycisk **Utwórz** Utwórz/wdrażania potoki próbki i usług/tabel połączonych używane przez potoków.
6. Wyświetlany jest stan wdrożenia na kafelku próbki kliknięto wcześniej na **przykładowe potoki** bloku.

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Po wyświetleniu **wdrożenie zakończyło się pomyślnie** wiadomości na kafelku, próbki, Zamknij **przykładowe potoki** bloku.  
8. Na **FABRYKI danych** bloku, zostanie wyświetlony połączone usługi, zestawy danych i potoki są dodawane z fabryką danych.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Przegląd rozwiązania
Ten przypadek użycia prostego może służyć jako przykład sposobu użycia usługi fabryka danych Azure w celu pozyskiwania, przygotowywanie, przekształcanie, analizowanie i publikować dane.

![Kompletny przepływ pracy](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Ilustracja przedstawia, jak potoki danych są wyświetlane w portalu Azure po ich wdrożeniu.

1. **PartitionGameLogsPipeline** odczytuje raw gier zdarzenia z magazynu obiektów blob i tworzy partycje oparte na rok, miesiąc i dzień.
2. **EnrichGameLogsPipeline** przyłączy partycjonowanej gier zdarzenia z danych referencyjnych kod: replikacji geograficznej — warstwa i poszerza dane mapowania adresów IP do odpowiedniej lokalizacji geo.
3. **AnalyzeMarketingCampaignPipeline** potoku wykorzystuje wzbogaconego danych i przetwarza je z danymi reklamy przez sieć do utworzenia ostateczne dane wyjściowe, który zawiera skuteczności kampanii marketingowych.

W tym przykładzie fabryki danych jest używany do organizowania działań, które kopiowania danych wejściowych, przekształcanie i przetwarzania danych, a dane wyjściowe ostateczne dane do bazy danych SQL Azure.  Można również wizualizacji sieci danych, potoki, zarządzać nimi i monitorowanie ich stan w interfejsie użytkownika.

## <a name="benefits"></a>Korzyści
Optymalizacja ich analytics profilu użytkownika i dostosowanie jej cele biznesowe, gier firmy jest szybko wzorców użycia zbieranie i analizowanie skuteczności jego kampanii marketingowych.

