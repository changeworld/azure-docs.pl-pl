---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a04df7ed283a17ddad6af87cf8215ff8d39a5079
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202460"
---
Szeregowanie dysków jest obecnie funkcją w wersji zapoznawczej dla dysków SSD Premium. Rozbicie jest obsługiwane na dowolnym rozmiarze dysków SSD w warstwie Premium < = 512 GiB (P20 lub poniżej). Te rozmiary dysków umożliwiają szybkie Rozliczanie i używanie systemu kredytowego do zarządzania rozliczeniami. Kredyty są gromadzone w zasobniku obejmującym cały czas, gdy ruch dysku jest niższy od obsługiwanego miejsca docelowego wydajności dla rozmiaru dysku, i zużywa kredyty, gdy ruch przekracza miejsce docelowe. Ruch dyskowy jest śledzony w odniesieniu do operacji we/wy i przepustowości w miejscu docelowym aprowizacji. Na rozerwanie dysków nie będą pomijane ograniczenia rozmiaru maszyny wirtualnej w przypadku operacji we/wy lub przepływności.

Funkcja przenoszenia dysków jest domyślnie włączona w nowych wdrożeniach obsługiwanych rozmiarów dysków. Istniejące rozmiary dysków, jeśli obsługują rozbicie dysku, mogą umożliwić Przechodzenie przez jedną z następujących metod:

- Odłącz i ponownie podłącz dysk.
- Zatrzymaj i uruchom maszynę wirtualną.

## <a name="burst-states"></a>Stany serii

Wszystkie odpowiednie rozmiary dysków będą rozpoczynać się od pełnego zasobnika środków w przypadku podłączenia dysku do maszyny wirtualnej. Maksymalny czas trwania serii jest określany na podstawie rozmiaru zasobnika środków. Możesz jedynie zbierać niewykorzystane środki w wysokości do rozmiaru zasobnika kredytowego. W dowolnym momencie zasobnik kredytowy dla serii dysku może być w jednym z trzech następujących stanów: 

- Naliczanie, gdy ruch na dysku jest używany mniej niż miejsce docelowe wydajności obsługiwanej. Możesz zakumulowanić środki w przypadku, gdy ruch dysku przekracza liczbę operacji wejścia/wyjścia na sekundę lub przepustowość. Można nadal zbierać środki w ramach operacji we/wy, gdy zużywasz pełną przepustowość dysku na odwrót.  

- Odrzucanie, gdy ruch z dysku jest używany więcej niż przychodzący obiekt docelowy wydajności. Ruch z serii będzie niezależnie zużywał kredyty z liczby operacji we/wy lub przepustowości. 

- Pozostała stała, gdy ruch dyskowy jest dokładnie na miejscu docelowym wydajności. 

W poniższej tabeli zestawiono rozmiary dysków zapewniające pomoc techniczną dotyczącą tworzenia serii oraz specyfikacje dotyczące serii.

## <a name="regional-availability"></a>Dostępność regionalna

Obecnie przenoszenie dysków jest dostępne tylko w regionie zachodnie stany USA.

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Przykładowe scenariusze

Aby lepiej zrozumieć, jak to działa, Oto kilka przykładowych scenariuszy:

- Typowym scenariuszem, który może przynieść korzyści wynikające z wykorzystania dysku, jest szybsze uruchamianie maszyn wirtualnych i uruchamiania aplikacji na dyskach systemu operacyjnego. Zapoznaj się z maszyną wirtualną z systemem Linux przy użyciu 8-GiBego obrazu systemu operacyjnego. Jeśli korzystamy z dysku P2 jako dysku systemu operacyjnego, zainicjowany obiekt docelowy to 120 operacji we/wy i 25 MB/s. Po uruchomieniu maszyny wirtualnej zostanie przeczytany odczyt z dysku systemu operacyjnego ładującego pliki rozruchowe. Wraz z wprowadzeniem możliwości przenoszenia można odczytać maksymalną prędkość serii 3500 operacji we/wy i 170 MB/s, skracając czas ładowania przez co najmniej 6x. Po rozruchu maszyny wirtualnej poziom ruchu na dysku systemu operacyjnego jest zazwyczaj niski, ponieważ większość operacji na danych aplikacji będzie odnosi się do dołączonych dysków danych. Jeśli ruch jest poniżej zainicjowanego obiektu docelowego, zostaną nagromadzone kredyty.

- Jeśli jest obsługiwane zdalne środowisko pulpitu wirtualnego, za każdym razem, gdy aktywny użytkownik uruchamia aplikację, taką jak AutoCAD, znacznie wzrasta ruch do dysku systemu operacyjnego. W takim przypadku ruch związany z obciążeniem będzie zużywać kredyty w wysokości, co pozwala na przechodzenie poza zainicjowaną obsługę administracyjną i szybkie uruchamianie aplikacji.

- Dysk P1 ma zainicjowany element docelowy 120 operacji we/wy i 25 MB/s. Jeśli rzeczywisty ruch na dysku wynosił 100 operacji we/wy i 20 MB/s w ciągu ostatnich 1 sekund, a następnie niewykorzystane 20 systemów IOs i 5 MB zostaną zaliczone do zasobnika na dysku. Kredyty w zasobniku szeregowym mogą być później używane, gdy ruch przekracza przypuszczalną wartość docelową. Maksymalny limit serii określa granicę ruchu na dysku, nawet jeśli masz środki na korzystanie z serii. W takim przypadku, nawet jeśli masz 10 000 IOs w zasobniku kredytowym, dysk P1 nie może wymusić więcej niż maksymalna liczba operacji we/wy na sekundę dla 3 500.  
