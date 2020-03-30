---
title: Omówienie programu Microsoft Azure StorSimple Data Manager | Dokumenty firmy Microsoft
description: Zawiera omówienie usługi StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876099"
---
# <a name="storsimple-data-manager-solution-overview"></a>Omówienie rozwiązania Usługi StorSimple Data Manager

## <a name="overview"></a>Omówienie

Usługa Microsoft Azure StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie warstw danych w magazynie lokalnym i chmurze. Dane są przechowywane w chmurze w formacie deduped i skompresowane dla maksymalnej wydajności i niższych kosztów. Ponieważ dane są przechowywane w formacie StorSimple, nie jest łatwo zużywalny przez inne aplikacje w chmurze, które mogą chcieć użyć.

Menedżer danych StorSimple umożliwia bezproblemowy dostęp do danych w formacie StorSimple i korzystanie z nich w chmurze. W tym celu można przekształcić format StorSimple w natywne obiekty BLOB i pliki, których można używać z innymi usługami, takimi jak Usługi Azure Media Services, usługa Azure HDInsights i usługa Azure Machine Learning.

Ten artykuł zawiera omówienie rozwiązania StorSimple Data Manager. Wyjaśniono również, jak można używać tej usługi do pisania aplikacji, które używają danych StorSimple i innych usług platformy Azure w chmurze.

## <a name="how-it-works"></a>Jak to działa?

Usługa StorSimple Data Manager identyfikuje dane StorSimple w chmurze z urządzenia lokalnego storsimple serii 8000. StorSimple danych w chmurze jest deduped, skompresowany StorSimple formatu. Usługa Menedżer danych udostępnia interfejsy API, aby wyodrębnić dane formatu StorSimple i przekształcić je w inne formaty, takie jak obiekty blob platformy Azure i pliki azure. Te przekształcone dane są następnie łatwo używane przez usługi Azure HDInsight i usługi Azure Media. Transformacja danych umożliwia w ten sposób te usługi do pracy na przekształconych danych StorSimple z StorSimple 8000 serii urządzeń lokalnych. Ten przepływ jest zilustrowany na poniższym diagramie.

![Diagram wysokiego poziomu](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Przypadki użycia Menedżera danych

Za pomocą menedżera danych z usługami Azure Functions, Azure Automation i Azure Data Factory można używać, aby przepływy pracy były uruchamiane na danych w miarę wprowadzania danych do storsimple. Można przetworzyć zawartość multimedialną, która jest przechowywana w storsimple z usługi Azure Media Services lub uruchomić algorytm uczenia maszynowego na tych danych lub wywołać klaster Hadoop do analizy danych przechowywanych na StorSimple. Dzięki szerokiej gamie usług dostępnych na platformie Azure w połączeniu z danymi na StorSimple możesz odblokować moc swoich danych.


## <a name="region-availability"></a>Dostępność w danym regionie

Menedżer danych StorSimple jest dostępny w następujących 7 regionach:

 - Azja Południowo-Wschodnia
 - Wschodnie stany USA
 - Zachodnie stany USA
 - Zachodnie stany USA 2
 - Zachodnio-środkowe stany USA
 - Europa Północna
 - Europa Zachodnia

Jednak StorSimple Data Manager może służyć do przekształcania danych w następujących regionach. 

![Regiony dostępne dla danych](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ten zestaw jest większy, ponieważ wdrożenie zasobów w dowolnym z powyższych regionów jest w stanie wychowania procesu transformacji w poniższych regionach. Tak długo, jak dane znajdują się w jednym z 19 regionów, można przekształcić dane za pomocą tej usługi.


## <a name="choosing-a-region"></a>Wybieranie regionu

Zalecenia:
 - Konto magazynu źródłowego (skojarzone z urządzeniem StorSimple) i docelowe konto magazynu (gdzie chcesz dane w formacie macierzystym) znajdują się w tym samym regionie platformy Azure.
 - Menedżer danych i definicja zadania są dostępne w regionie, który zawiera konto magazynu StorSimple. Jeśli nie jest to możliwe, należy wywołać Menedżera danych w najbliższym regionie platformy Azure, a następnie utworzyć definicję zadania w tym samym regionie, co konto magazynu StorSimple. 

    Jeśli konto magazynu StorSimple nie znajduje się w 26 regionach obsługujących tworzenie definicji zadań, zaleca się, aby nie uruchamiać Menedżera danych StorSimple, ponieważ są widoczne długie opóźnienia i potencjalne opłaty za ruch wychodzący.
    
Firma Microsoft dokłada wszelkich starań, aby usługi platformy Azure były zawsze dostępne we wszystkich regionach. Jednak nieplanowane przerwy w dostawie usług mogą wystąpić przez krótki czas w określonym regionie. W takich przypadkach można wywołać Menedżera danych i definicji zadania w regionie, który nie ma wpływu na awarię, i uruchomić zadanie transformacji. Może wystąpić pewne dodatkowe opóźnienia w takim scenariuszu, ale może to być strategia odzyskiwania w rzadkich przypadkach awarii regionalnej.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Menedżer danych StorSimple potrzebuje klucza szyfrowania danych usługi, aby przekształcić z formatu StorSimple do formatu macierzystego. Klucz szyfrowania danych usługi jest generowany, gdy pierwsze urządzenie rejestruje się w usłudze StorSimple. Aby uzyskać więcej informacji na temat tego klucza, przejdź do [storsimple zabezpieczeń](storsimple-8000-security.md).

Klucz szyfrowania danych usługi podany jako dane wejściowe jest przechowywany w magazynie kluczy, który jest tworzony podczas tworzenia Menedżera danych. Przechowalnia znajduje się w tym samym regionie platformy Azure co Menedżer danych StorSimple. Ten klucz jest usuwany po usunięciu usługi Menedżer danych.

Ten klucz jest używany przez zasoby obliczeniowe do przeprowadzenia transformacji. Te zasoby obliczeniowe znajdują się w tym samym regionie platformy Azure, co definicja zadania. Ten region może lub nie może być taki sam jak region, w którym można przywołać Menedżera danych.

Jeśli region Menedżera danych różni się od regionu definicji zadania, ważne jest, aby zrozumieć, jakie dane/metadane znajdują się w każdym z tych regionów. Na poniższym diagramie przedstawiono wpływ posiadania różnych regionów dla Menedżera danych i definicji zadania.

![Definicja usługi i zadania w różnych regionach](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Zarządzanie danymi osobowymi

Menedżer danych StorSimple nie gromadzi ani nie wyświetla żadnych informacji osobistych. Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Znane ograniczenia

Usługa ma obecnie następujące ograniczenia:
- StorSimple Data Manager obecnie nie działa z woluminów, które są szyfrowane funkcji bitlocker. Jeśli spróbujesz uruchomić usługę za pomocą zaszyfrowanego dysku, zostaną wyświetlone błędy zadania.
- Niektóre metadane plików (w tym listy ACL) nie będą zachowywane w przekształconych danych.
- Ta usługa działa tylko z woluminami NTFS.
- Długość ścieżki pliku musi być mniejsza niż 256 znaków, w przeciwnym razie zadanie zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

[Aby przekształcić dane, użyj interfejsu użytkownika Programu StorSimple Data Manager.](storsimple-data-manager-ui.md)
