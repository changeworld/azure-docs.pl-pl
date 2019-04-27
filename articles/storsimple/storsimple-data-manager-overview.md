---
title: Microsoft Azure StorSimple Data Manager — omówienie | Dokumentacja firmy Microsoft
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
ms.author: vidarmsft
ms.openlocfilehash: c5ffe3ec2ec3cb06297df6be4ba7021f692633bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630708"
---
# <a name="storsimple-data-manager-solution-overview"></a>Omówienie rozwiązania StorSimple Data Manager

## <a name="overview"></a>Omówienie

Usługa Microsoft Azure StorSimple używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie danych warstwy magazynu lokalnego i w chmurze. Dane są przechowywane w chmurze w formacie deduplikacją i skompresowane osiągnięcie maksymalnej wydajności i obniżenia kosztów. Dane są przechowywane w formacie StorSimple, dlatego nie jest gotowy do wykorzystania przez inne aplikacje w chmurze, które chcesz użyć.

Usługa StorSimple Data Manager pozwala na bezproblemowe dostęp i korzystać z usługi StorSimple formatowanie danych w chmurze. Robi to poprzez przekształcanie StorSimple format do natywnych obiektów blob i plików, których można użyć z innymi usługami, takie jak Azure Media Services, Azure HDInsights i Azure Machine Learning.

Ten artykuł zawiera omówienie rozwiązania StorSimple Data Manager. Wyjaśniono również, jak można używać tej usługi do pisania aplikacji korzystających z danych w usłudze StorSimple i innymi usługami platformy Azure w chmurze.

## <a name="how-it-works"></a>Jak to działa?

Usługa StorSimple Data Manager identyfikuje danych w usłudze StorSimple w chmurze z urządzenia StorSimple 8000 series w środowisku lokalnym. Dane usługi StorSimple w chmurze jest deduplikacją, skompresowany StorSimple format. Usługa Data Manager udostępnia interfejsy API w celu wyodrębniania danych z formatu usługi StorSimple i przekształcenie go w innych formatach, takich jak obiekty BLOB platformy Azure i usługi Azure Files. To przekształcone dane łatwo jest następnie używane przez usługi Azure HDInsight i Azure Media services. Przekształcenie danych w związku z tym umożliwia tych usług, na których przekształconych danych StorSimple z urządzenia lokalnego StorSimple 8000 series. Ten przepływ jest zilustrowane na poniższym diagramie.

![Diagram ogólny](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Przypadki użycia Menedżera danych

Menedżer danych z usługi Azure Functions, Azure Automation i Azure Data Factory służy do przepływów pracy uruchomionych na danych, gdy trafiają one do usługi StorSimple. Możesz chcieć przetwarzania multimediów przechowywać na StorSimple przy użyciu usługi Azure Media Services, lub uruchomić Algorytm uczenia maszynowego na tych danych lub Przenieś klaster Hadoop do analizowania danych, które są przechowywane na StorSimple. Z szeroką gamę usług dostępnych na platformie Azure w połączeniu z danymi na StorSimple można odblokować możliwości danych.


## <a name="region-availability"></a>Dostępność w danym regionie

Usługa StorSimple Data Manager jest dostępna w następujących regionach 7:

 - Azja Południowo-Wschodnia
 - Wschodnie stany USA
 - Zachodnie stany USA
 - Zachodnie stany USA 2
 - Środkowo-zachodnie stany USA
 - Europa Północna
 - Europa Zachodnia

Jednak usługa StorSimple Data Manager może służyć do przekształcania danych w następujących regionach. 

![Regiony dostępne w przypadku danych](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ten zestaw jest większy, ponieważ wdrażania zasobów w żadnej z powyższych regionach jest w stanie się proces przekształcania w poniższych regionach. Dlatego tak długo, jak długo dane znajdują się w jednym z 19 regionach, można transformować dane za pomocą tej usługi.


## <a name="choosing-a-region"></a>Wybierając region

Zalecenia:
 - Źródłowego konta magazynu (jedna skojarzony z urządzeniem StorSimple) i docelowe konto magazynu (gdzie mają dane w formacie natywnym) mieścić się w tym samym regionie platformy Azure.
 - Przenieś definicję Menedżera danych i zadań w regionie, który będzie zawierać konto magazynu StorSimple. Jeśli nie jest to możliwe, Przenieś Konfigurowanie Menedżera danych w najbliższym regionie platformy Azure, a następnie utworzyć definicję zadania w tym samym regionie co konto magazynu usługi StorSimple. 

    Konta magazynu StorSimple nie jest w 26 regionach, które obsługują tworzenie definicji zadania, zaleca się że nie zostanie uruchomione usługi StorSimple Data Manager jak widać, długie opóźnienia i potencjalne opłaty za ruch wychodzący.
    
Firma Microsoft dokłada starań upewnić się, że usługi platformy Azure są zawsze dostępne we wszystkich regionach. Jednak usługi nieplanowanych awarii może być krótkie okresy w danym regionie. W takich przypadkach można przywołać definicji Menedżera danych i zadań w regionie, który nie ma wpływu awarii i uruchomić zadanie przekształcania. Mogą wystąpić pewne dodatkowe opóźnienie w takiej sytuacji, ale może to być strategii odzyskiwania w rzadkich regionalnej awarii.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Usługa StorSimple Data Manager wymaga klucza szyfrowania danych usługi do przekształcenia z formatu usługi StorSimple do formatu macierzystego. Klucz szyfrowania danych usługi jest generowany, gdy pierwsze urządzenie rejestruje się za pomocą usługi StorSimple. Aby uzyskać więcej informacji na temat tego klucza, przejdź do [zabezpieczenia usługi StorSimple](storsimple-8000-security.md).

Klucz szyfrowania danych usługi, podana jako dane wejściowe są przechowywane w magazynie kluczy, który jest tworzony podczas tworzenia Menedżera danych. Magazyn znajduje się w tym samym regionie platformy Azure jako usługi StorSimple Data Manager. Ten klucz jest usuwany podczas usuwania usługi Data Manager.

Ten klucz jest używany przez zasoby obliczeniowe do wykonania przekształcenia. Te obliczenia zasoby znajdują się w tym samym regionie platformy Azure jako definicji zadania. Ten region może lub nie może być taki sam jak region, w którym wprowadzasz Konfigurowanie Menedżera danych.

Jeśli swój region Menedżera danych różni się od regionu definicji zadania, to zrozumieć, jakie dane/metadanych znajduje się w tych regionach. Na poniższym diagramie przedstawiono efekt wystąpienia w różnych regionach definicji Menedżera danych i zadań.

![Definicja usługi i zadania w różnych regionach](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Zarządzanie informacji osobistych

Usługa StorSimple Data Manager nie zbierania lub wyświetlania informacji osobistych. Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Znane ograniczenia

Usługa jest obecnie ma następujące ograniczenia:
- Usługa StorSimple Data Manager aktualnie nie działa z woluminów, które jest zaszyfrowany funkcją bitlocker. Jeśli zostanie podjęta próba uruchomienia usługi z zaszyfrowanego dysku, zostanie wyświetlone niepowodzeń zadań.
- Niektóre metadane plików (w tym listy ACL) nie zostaną zachowane w przekształconych danych.
- Usługa ta działa tylko w przypadku woluminów NTFS.
- Długość ścieżki pliku muszą być mniej niż 256 znaków inne zadanie zakończy się niepowodzeniem.

## <a name="next-steps"></a>Kolejne kroki

[Użyj usługi StorSimple Data Manager interfejsu użytkownika w celu przekształcania danych](storsimple-data-manager-ui.md).
