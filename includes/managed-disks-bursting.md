---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 84736b7f1dcdf8b186fddbced5dd773e008c0dd2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887409"
---
Rozerwanie dysku jest obsługiwane dla dysków SSD klasy premium. Bursting jest obsługiwany na dowolnym rozmiarze dysków SSD premium <= 512 GiB (P20 lub poniżej). Te rozmiary dysków obsługują pękanie na podstawie najlepszego wysiłku i wykorzystują system kredytów do zarządzania pęknięciem. Kredyty gromadzą się w zasobniku serii, gdy ruch dysku jest poniżej aprowizowanego celu wydajności dla ich rozmiaru dysku i zużywają kredyty, gdy ruch zostanie opublikowany poza obiekt docelowy. Ruch dysku jest śledzony zarówno w przypadku usług We/Wy, jak i przepustowości w aprowizowanym celu. Bursting dysku nie będzie pomijać ograniczenia rozmiaru maszyny wirtualnej (VM) na we/wy lub przepływności.

Bursting dysku jest domyślnie włączona w nowych wdrożeniach rozmiarów dysków, które go obsługują. Istniejące rozmiary dysków, jeśli obsługują pękanie dysku, mogą umożliwiać pękanie za pomocą jednej z następujących metod:

- Odłącz i podłącz ponownie dysk.
- Zatrzymaj i uruchom maszynę wirtualną.

## <a name="burst-states"></a>Stany rozerwania

Wszystkie rozmiary dysków serii odpowiednie rozpocznie się w pełnej wersji wybuchu zasobnika, gdy dysk jest dołączony do maszyny wirtualnej. Maksymalny czas trwania rozerwania zależy od rozmiaru wiadra kredytów serii. Możesz gromadzić nieużywane kredyty tylko do wielkości zasobnika kredytowego. W dowolnym momencie wiadro kredytowe rozerwanie dysku może znajdować się w jednym z następujących trzech stanów: 

- Naliczanie, gdy ruch dysku używa mniej niż aprowizowana docelowa wydajności. Można gromadzić kredytów, jeśli ruch dysku jest poza IOPS lub docelowych przepustowości lub obu. Nadal można gromadzić kredyty we/wy, gdy zużywasz pełną przepustowość dysku, na odwrót.  

- Maleje, gdy ruch dysku używa więcej niż aprowizowana docelowa wydajności. Ruch seryjny będzie niezależnie zużywać kredyty z usług We/Wy lub przepustowości. 

- Pozostała stała, gdy ruch dysku jest dokładnie na aprowizowanym miejscu docelowym wydajności. 

Rozmiary dysków, które zapewniają obsługę rozerwania wraz ze specyfikacjami serii są podsumowane w poniższej tabeli.

## <a name="regional-availability"></a>Dostępność regionalna

Rozerwanie dysku jest dostępne we wszystkich regionach w chmurze publicznej.

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Przykładowe scenariusze

Aby lepiej zorientować się, jak to działa, oto kilka przykładowych scenariuszy:

- Typowym scenariuszem, który może korzystać z rozerwania dysku jest szybsze uruchamianie i uruchamianie aplikacji na dyskach systemu operacyjnego. Weź maszynę wirtualną z systemem Linux z obrazem 8 GiB OS jako przykład. Jeśli używamy dysku P2 jako dysku systemu operacyjnego, aprowizowanym celem jest 120 IOPS i 25 MiB. Po uruchomieniu maszyny Wirtualnej nastąpi skok odczytu do dysku systemu operacyjnego ładowania plików rozruchowych. Wraz z wprowadzeniem rozerwania, można odczytać z maksymalną prędkością serii 3500 IOPS i 170 MiB, przyspieszając czas ładowania o co najmniej 6x. Po uruchomieniu maszyny Wirtualnej poziom ruchu na dysku systemu operacyjnego jest zwykle niski, ponieważ większość operacji danych przez aplikację będzie na dyskach dołączonych danych. Jeśli ruch jest poniżej aprowizowanego celu, zgromadzisz kredyty.

- Jeśli hostujesz środowisko pulpitu zdalnego wirtualnego, za każdym razem, gdy aktywny użytkownik uruchamia aplikację, taką jak AutoCAD, ruch odczytu na dysku systemu operacyjnego znacznie wzrasta. W takim przypadku ruch seryjny zużywa zgromadzone kredyty, co pozwala wyjść poza aprowizowany cel i uruchamiać aplikację znacznie szybciej.

- Dysk P1 ma aprowizowanym obiektem docelowym 120 IOPS i 25 MiB. Jeśli rzeczywisty ruch na dysku wynosił 100 IOPS i 20 MiB w ciągu ostatnich 1 sekundy interwału, nieużywane 20 IOs i 5 MB są zapisywane na zasobniku seryjnym dysku. Kredyty w zasobniku serii mogą być później używane, gdy ruch przekracza aprowizowanego obiektu docelowego, do maksymalnego limitu serii. Maksymalny limit serii definiuje pułap ruchu dysku, nawet jeśli masz kredyty serii do wykorzystania z. W takim przypadku, nawet jeśli masz 10 000 we/wiadra kredytów, dysk P1 nie może wydać więcej niż maksymalna seria 3500 we/wy na sekundę.  
