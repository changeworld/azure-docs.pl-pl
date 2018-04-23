---
title: Omówienie programu Microsoft Azure StorSimple Data | Dokumentacja firmy Microsoft
description: Zawiera omówienie usługi Menedżer StorSimple danych
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
ms.date: 02/26/2018
ms.author: vidarmsft
ms.openlocfilehash: 4d27bc3660035ace25436a4f756c41e88200649d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="storsimple-data-manager-solution-overview"></a>Omówienie rozwiązania StorSimple Data Manager

## <a name="overview"></a>Przegląd

Microsoft Azure StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego, i automatycznie danych warstw na magazynu lokalnego i w chmurze. Dane są przechowywane w chmurze w formacie deduplikowanych i skompresowane z maksymalną wydajnością i do obniżenia kosztów. Ponieważ dane są przechowywane w formacie StorSimple, nie jest łatwo dostępne inne aplikacje w chmurze, które chcesz użyć.

Menedżer StorSimple danych umożliwia bezproblemowe dostępu i użycia danych formatu StorSimple w chmurze. Robi to poprzez przekształcanie StorSimple format w obiektach blob natywnej i pliki, których można używać z innych usług, takich jak usługi Azure Media Services, Azure HDInsights i uczenie maszynowe Azure.

Ten artykuł zawiera omówienie rozwiązania StorSimple Data Manager. Wyjaśniono również, jak można użyć tej usługi do pisania aplikacji, które używają danych StorSimple i innych usług Azure w chmurze.

## <a name="how-it-works"></a>Jak to działa?

Usługę Menedżer StorSimple danych identyfikuje dane StorSimple w chmurze z serii StorSimple 8000 lokalnego urządzenia. Danych StorSimple w chmurze jest deduplikacją, skompresowany StorSimple format. Usługa Data Manager udostępnia interfejsy API, aby wyodrębnić dane formatu StorSimple i przekształcenie go w innych formatach, takich jak obiekty BLOB Azure i usługi pliki Azure. To przekształcone dane łatwo jest następnie używane przez usługi Azure HDInsight i multimediów Azure. Przekształcenia danych w związku z tym umożliwia tych usług, które ma być przeprowadzana operacja przekształcone dane StorSimple z urządzeniami lokalnymi serii StorSimple 8000. Na poniższym diagramie przedstawiono tego przepływu.

![Diagram ogólny](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Przypadki użycia Menedżera danych

Menedżer danych z usługi Azure Functions, usługi Automatyzacja Azure i fabryki danych Azure umożliwia ma przepływów pracy uruchomionych na podstawie danych, ponieważ pochodzi do StorSimple. Można przetworzyć zawartości przechowywać na StorSimple w usłudze Azure Media Services, lub uruchom Algorytm uczenia maszynowego danych lub uzupełnić klastra usługi Hadoop do analizowania danych, które są przechowywane na StorSimple multimediów. Z szeroką gamę usług dostępnych na platformie Azure w połączeniu z danymi na StorSimple można odblokować zasilania danych.


## <a name="region-availability"></a>Dostępność w danym regionie

Menedżer StorSimple danych jest dostępna w następujących regionach 7:

 - Azja Południowo-Wschodnia
 - Wschodnie stany USA
 - Zachodnie stany USA
 - Zachodnie stany USA 2
 - Środkowo-zachodnie stany USA
 - Europa Północna
 - Europa Zachodnia

Jednak Menedżer StorSimple danych może służyć do przekształcania danych w następujących regionach. 

![Regiony dostępne dla danych](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ten zestaw jest większy, ponieważ wdrażania zasobów w żadnej z powyższych regionów jest w stanie proces przekształcenia w poniżej obszarach. Tak jak długo dane znajdują się w jednym 19 regionów, można przekształcać dane przy użyciu tej usługi.


## <a name="choosing-a-region"></a>Wybieranie regionu

Zalecamy, aby:
 - Konta magazynu źródłowego (jeden skojarzonych z Twoim urządzeniem StorSimple) i docelowe konto magazynu (gdzie mają dane w formacie native) można w tym samym regionie Azure.
 - Przełącz definicję Menedżera danych i zadań w regionie, który zawiera konta magazynu StorSimple. Jeśli nie jest to możliwe, przełącz się Data Manager w najbliższej region platformy Azure, a następnie utwórz definicji zadania w tym samym regionie co konto magazynu StorSimple. 

    Konta magazynu StorSimple nie jest w regionach 26, które obsługują tworzenie definicji zadania, zaleca się czy nie zostanie uruchomiony Menedżer StorSimple danych Zobacz długie opóźnienia i opłaty za wyjście wysokiego.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Menedżer StorSimple danych wymaga klucza szyfrowania danych usługi do przekształcenia z formatu StorSimple do formatu macierzystego. Klucz szyfrowania danych usługi jest generowany podczas pierwszego urządzenia zarejestrowanie w usłudze StorSimple. Aby uzyskać więcej informacji na ten klucz, przejdź do [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).

Klucz szyfrowania danych usługi podana jako dane wejściowe są przechowywane w magazynie kluczy, który jest tworzony podczas tworzenia Data Manager. Magazyn znajduje się w tym samym regionie Azure jako menedżera StorSimple danych. Ten klucz jest usuwany podczas usuwania usługi Data Manager.

Ten klucz jest używany przez zasoby obliczeniowe na przekształcenie. Tych obliczeń zasobów znajdują się w tym samym regionie Azure jako definicja zadania. Ten region może lub nie może być taki sam jak region, w którym przełączeniem Konfigurowanie Menedżera danych.

Jeśli Twoim regionie Menedżera danych różni się od regionu definicji zadania, ważne jest zrozumienie danych/metadanych znajduje się w każdym z tych obszarów. Poniższy diagram ilustruje efekt o różnych regionach dla definicji Menedżera danych i zadania.

![Definicja usługi i zadania w różnych regionach](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="next-steps"></a>Kolejne kroki

[Użyj Menedżera danych StorSimple interfejsu użytkownika Służącego do przekształcenia danych](storsimple-data-manager-ui.md).
