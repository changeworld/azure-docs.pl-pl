---
title: Przypadek użycia — profilowanie klientów
description: Dowiedz się, jak usługa Azure Data Factory jest używana do tworzenia opartego na danych przepływu pracy (potoku) dla klientów zajmujących się grami w profilu.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c570f988dea894b8106405f4e427edb386a3e74a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969285"
---
# <a name="use-case---customer-profiling"></a>Przypadek użycia — profilowanie klientów
Usługa Azure Data Factory jest jedną z wielu usług używanych do implementowania pakietu Cortana Intelligence Suite akceleratorów rozwiązań.  Aby uzyskać więcej informacji o Cortana Intelligence, odwiedź [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). W tym dokumencie opisano prosty przypadek użycia, aby ułatwić rozpoczęcie pracy ze zrozumieniem, jak usługa Azure Data Factory może rozwiązać typowe problemy z analizą.

## <a name="scenario"></a>Scenariusz
Contoso to firma zajmująca się grami, która tworzy gry na wiele platform: konsole do gier, urządzenia ręczne i komputery osobiste (komputery). Gdy gracze grają w te gry, powstaje duża ilość danych dziennika, które śledzą wzorce użytkowania, styl gry i preferencje użytkownika.  W połączeniu z danymi demograficznymi, regionalnymi i danymi o produktach firma Contoso może przeprowadzać analizy, aby pomóc im w ulepszaniu doświadczeń graczy i kierowaniu ich na ulepszenia i zakupy w grze. 

Celem firmy Contoso jest zidentyfikowanie możliwości sprzedaży/ sprzedaży krzyżowej w oparciu o historię gier swoich graczy i dodanie atrakcyjnych funkcji, aby stymulować rozwój firmy i zapewnić klientom lepsze wrażenia. W tym przypadku używamy firmy zajmującej się grami jako przykładem firmy. Firma chce zoptymalizować swoje gry w oparciu o zachowanie graczy. Zasady te mają zastosowanie do każdej firmy, która chce zaangażować swoich klientów wokół swoich towarów i usług i poprawić doświadczenie swoich klientów.

W tym rozwiązaniu firma Contoso chce ocenić skuteczność kampanii marketingowej, która niedawno rozpoczęła. Zaczynamy od surowych dzienników gier, przetwarzamy i wzbogacamy je danymi geolokalizacyjnymi, dołączamy do nich z danymi referencyjnymi reklam i wreszcie skopiujemy je do bazy danych SQL Azure w celu przeanalizowania wpływu kampanii.

## <a name="deploy-solution"></a>Wdrażanie rozwiązania
Wszystko, czego potrzebujesz, aby uzyskać dostęp i wypróbować ten prosty przypadek użycia, to [subskrypcja platformy Azure,](https://azure.microsoft.com/pricing/free-trial/) [konto magazynu obiektów Blob platformy Azure](../../storage/common/storage-account-create.md)i baza danych SQL [Azure.](../../sql-database/sql-database-get-started.md) Potok profilowania klienta można wdrożyć z kafelka **Przykładowe potoki** na stronie głównej fabryki danych.

1. Utwórz fabrykę danych lub otwórz istniejącą fabrykę danych. Zobacz [Kopiowanie danych z magazynu obiektów blob do bazy danych SQL przy użyciu fabryki danych,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby wykonać kroki tworzenia fabryki danych.
2. W bloku **DATA FACTORY** dla fabryki danych kliknij kafelek **Przykładowe potoki.**

    ![Przykładowy kafelek potoków](./media/data-factory-samples/SamplePipelinesTile.png)
3. W **przykładowym potoku** bloku kliknij **profilowanie klienta,** które chcesz wdrożyć.

    ![Przykładowe ostrze rurociągów](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji dla próbki. Na przykład nazwa i klucz konta magazynu platformy Azure, nazwa serwera SQL platformy Azure, baza danych, identyfikator użytkownika i hasło.

    ![Ostrze próbki](./media/data-factory-samples/SampleBlade.png)
5. Po zakończeniu określania ustawień konfiguracji kliknij przycisk **Utwórz,** aby utworzyć/wdrożyć przykładowe potoki i połączone usługi/tabele używane przez potoki.
6. Widać stan wdrożenia na przykładowym kafelku klikniętym wcześniej w bloku **Przykładowe potoki.**

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Po **wyświetleniu komunikatu Wdrożenie powiodło się** na kafelku dla próbki, zamknij **przykładowego potoku** bloku.  
8. W **bloku DATA FACTORY** zobaczysz, że połączone usługi, zestawy danych i potoki są dodawane do fabryki danych.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Omówienie rozwiązania
Ten przypadek prostego użycia może służyć jako przykład sposobu używania usługi Azure Data Factory do pozyskiwania, przygotowywania, przekształcania, analizowania i publikowania danych.

![Kompletny przepływ pracy](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Na rysunku przedstawiono sposób, w jaki potoki danych są wyświetlane w witrynie Azure portal po ich wdrożeniu.

1. **PartitionGameLogsPipeline** odczytuje zdarzenia raw gry z magazynu obiektów blob i tworzy partycje na podstawie roku, miesiąca i dnia.
2. **EnrichGameLogsPipeline** łączy zdarzenia gry na partycje z danymi referencyjnymi kodu geograficznego i wzbogaca dane, mapując adresy IP do odpowiednich lokalizacji geograficznych.
3. **Potok AnalyzeMarketingCampaignPipeline** wykorzystuje wzbogacone dane i przetwarza je z danymi reklamowymi w celu utworzenia ostatecznego wyjścia zawierającego skuteczność kampanii marketingowej.

W tym przykładzie usługa Data Factory jest używana do organizowania działań, które kopiują dane wejściowe, przekształcają i przetwarzają dane i przetwarzają dane końcowe do bazy danych SQL Azure.  Można również wizualizować sieć potoków danych, zarządzać nimi i monitorować ich stan z interfejsu użytkownika.

## <a name="benefits"></a>Korzyści
Optymalizując analizę profilu użytkownika i dostosowując ją do celów biznesowych, firma zajmująca się grami jest w stanie szybko zbierać wzorce użytkowania i analizować skuteczność swoich kampanii marketingowych.

