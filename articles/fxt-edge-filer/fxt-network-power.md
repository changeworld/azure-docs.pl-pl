---
title: Zasilacz i połączeniem sieciowym filtr Edge FXT Azure firmy Microsoft
description: Jak okablowanie portów sieciowych i dołączyć zasilania dla sprzętu filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 444835a94559a352bfd749cfa1cb2cd8c3a39373
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450310"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Samouczek: Tworzenie połączeń sieciowych i podaj zasilania do węzła filtr Edge FXT platformy Azure

Ten samouczek omawia sposób okablowanie połączeń sieciowych dla węzła sprzętu filtr Edge FXT platformy Azure.

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jak wybrać typ kabel sieciowy dla danego środowiska
> * Jak połączyć węzłem filtr Edge FXT platformy Azure z siecią centrum danych
> * Jak kable za pośrednictwem kabla ramię zarządzania (CMA)
> * Jak połączyć zasilania do racked urządzenia i włączenie go

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, filtr Edge FXT Azure należy zainstalować w stojaku standardowego sprzętu. CMA należy zainstalować w węźle filtr. 

## <a name="identify-ports"></a>Identyfikowanie portów

Określ różne porty na odwrocie podkładki swoje filtr Edge FXT platformy Azure. 
 
![Tylnej stronie okablowana między urządzeniami](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

* Połącz porty RJ-45 do źródła sieci centrum danych, zgodnie z opisem w [porty sieciowe](#network-ports).  
* Bezpieczne połączenie [portu iDRAC](#idrac-port) do oddzielnej sieci z bezpiecznym serwerem DHCP. 
* Nawiązać jest klawiatura i monitor węzła dla początkowej konfiguracji należy używać portów USB i VGA port. Należy wykonać rozruch węzła i [ustawić początkowe hasło](fxt-node-password.md) aktywować węzła przez inne porty. Odczyt [hasła początkowego](fxt-node-password.md) Aby uzyskać szczegółowe informacje. 

W tym artykule opisano również sposób [połączyć zasilacza](#connect-power-cables) dla węzła. 

W tym artykule wyjaśniono również sposób łączenia z węzłem [portu szeregowego](#serial-port-only-when-necessary), ale portu szeregowego jest używany tylko do zaawansowanego rozwiązywania problemów. 

### <a name="network-ports"></a>Porty sieciowe 

Każdy węzeł filtr Edge FXT Azure zawiera następujące porty sieciowe: 

* Sześć porty o dużej szybkości szybkości podwójnej 25GbE/10 GbE danych: 

  * Cztery porty dostarczone przez dwie karty sieciowe wtyczki Dwuportowe
  * Dwa porty, dostarczone przez kartę sieciową w formacie pośrednim płyty głównej 

* Dwa porty 1GbE dostarczane przez kartę sieciową w formacie pośrednim płyty głównej 

Porty o dużej szybkości danych 25GbE/10 GbE mają standardowa klatkach SFP28 zgodny. Aby użyć kable optyczne, należy zainstalować moduły optyczne odbiorcze SFP28 (niedostępny).

Porty 1GbE mają łączniki standardowe RJ-45.

Aby uzyskać pełną listę obsługiwanych kable, przełączniki i odbiorcze, zapoznaj się [Cavium FastlinQ 41000 serii współdziałanie macierzy](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

Typ połączeń do użycia w systemie zależy od środowiska centrum danych.

* Jeśli połączenie z siecią 25GbE, Podłączanie kabli danych o dużej szybkości portów przy użyciu jednego z następujących typów kabel:

  * Kabel optyczne i SFP28 odbiorcze optyczne 25GbE lub możliwości 25GbE/10 GbE szybkości podwójnej
  * Bezpośrednie obsługą 25GbE typu SFP28 Podłącz kabel twinaxial

* Jeśli połączenie sieciowe 10 GbE, Podłączanie kabli portów danych o dużej szybkości za pomocą jednego z następujących czynności: 

  * Kabel optyczne i SFP28 optyczne odbiornika z 10 GbE lub podwójne współczynnik 25GbE/10 GbE możliwości.
  * Bezpośrednie obsługą 25GbE typu SFP28 Podłącz kabel twinaxial
  * Bezpośrednie 10 GbE obsługą typu SFP28 Podłącz kabel twinaxial

* Porty sieciowe 1GbE są używane dla klastra ruch związany z zarządzaniem. Sprawdź **korzystania z sieci zarządzania 1Gb** opcji podczas tworzenia klastra (opisanego w [skonfigurować sieć zarządzania](fxt-cluster-create.md#configure-the-management-network)). Podłączanie kabli portów za pomocą standardowych kategorii 5 lub kabla lepsze, zgodnie z opisem na liście obsługiwanych kable.

  Możesz pozostawić porty 1GbE uncabled, jeśli planujesz używać szybkich porty dla całego ruchu. Domyślnie porty sieciowe 1GbE nie są używane, jeśli wyższe port danych szybkość jest dostępna.  

### <a name="idrac-port"></a>iDRAC port  

Port etykietą iDRAC jest połączeniu 1Gb, która umożliwia komunikację za pomocą kontrolera używane do zarządzania sprzętem i monitorowania dostępu zdalnego. FXT oprogramowania za pomocą interfejsu zarządzania platformy inteligentną (IPMI) tego kontrolera do rozwiązywania problemów i odzyskiwania. Można użyć wbudowanego [interfejsu iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) do monitorowania sprzętu za pośrednictwem tego portu. iDRAC i dostęp interfejsu IPMI są domyślnie włączone. 

> [!Note]
> IDRAC port można pominąć systemu operacyjnego i bezpośrednią interakcję z sprzętu w węźle. 

Użyj następujących strategii zabezpieczeń, łączenie i konfiguracją portów iDRAC:

* Porty iDRAC łączyć się tylko z sieci, które są fizycznie oddzielone od sieci danych, które umożliwiają dostęp do klastra.
* Ustaw hasło administratora iDRAC bezpieczne w każdym węźle. Należy ustawić hasło można aktywować sprzęt — wykonaj instrukcje z sekcji [Ustawianie haseł sprzętu](fxt-node-password.md).
* Domyślnej konfiguracji portów iDRAC korzysta z protokołu DHCP, jak i IPv4, przypisywania adresów IP. Upewnij się, ochrony środowiska DHCP i czy połączenia są ograniczone pomiędzy klientami DHCP a serwerem DHCP. (Panel sterowania klastra zawiera ustawienia, aby zmienić metody konfiguracji adresów węzłów, po utworzeniu klastra).
* Pozostaw iDRAC port ustawiony na wartość "dedykowane tryb" (domyślnie), który ogranicza ruch sieciowy iDRAC/IPMI dedykowany port RJ-45.

IDRAC port nie wymaga szybkiego połączenia sieciowego.
  
### <a name="serial-port-only-when-necessary"></a>Port szeregowy (tylko wtedy, gdy to konieczne)

W niektórych sytuacjach Microsoft Service i pomocy technicznej mogą wskazać możliwość rozwiązania do łączenia z terminalu do portu szeregowego węzeł, aby zdiagnozować problem.  

Aby dołączyć konsolę:

1. Znajdź portu szeregowego (COM1) z tyłu filtr Edge FXT węzła.
1. Połącz port szeregowy terminalu skonfigurowane dla ANSI-115200-8N1 za pomocą kabla pseudomodemu.
1. Zaloguj się do konsoli i wykonać inne czynności, zgodnie z zaleceniami z pracownikami działu pomocy technicznej.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Kable trasy w ramię zarządzania kablowe (CMA)

Każdy węzeł filtr Edge FXT Azure zawiera opcjonalny kabel ramię zarządzania. CMA upraszcza okablowania i zapewnia ułatwienia dostępu w przypadku tylnej części obudowy bez konieczności rozłączyć kable. 

Wykonaj te instrukcje, aby kierować kable za pośrednictwem CMA: 

1. Za pomocą zawija tie, pod warunkiem, powiązać kable razem wprowadź i zakończyć koszyki, tak aby nie zakłócają sąsiadujących systemów (1).
1. Za pomocą CMA w położeniu usługi należy rozesłać pakietu kabel za pośrednictwem koszyków wewnętrznych i zewnętrznych (2).
1. Użyj wstępnie zainstalowane taśm hook i pętli na końcu koszyków do zabezpieczenia kable (3).
1. Typu Swing CMA w miejscu na pasku zadań (4).
1. Przy użyciu kabla wskaźnika stanu preinstalowanych tyłu system i zabezpieczenia kabel routingu go CMA przez. Dołącz drugiej stronie kabel do rogu zewnętrznej koszyka CMA (5). 

   > [!CAUTION]
   > Aby uniknąć potencjalnego uszkodzenia z wystające kable, należy zabezpieczyć wszelkie slack kabla wskaźnika stanu po routingu tego kabla za pośrednictwem CMA. 

![Ilustracja CMA przy użyciu kabli zainstalowany](media/cma-cabling-scan-400.png)

> [!NOTE]
>  Jeśli CMA nie został zainstalowany, użyj dwóch hook i pętli taśm podane w zestawie szyny kierować kable tyłu systemu.
> 
>  1. Znajdź zewnętrzne nawiasy CMA na stronach posługiwanie się nimi zarówno kołnierze stojaka.
>  2. Pakietu kable delikatnie, pobierając je wyczyść łączników systemu do lewej i prawej stronie.
>  3. Wątek punktu zaczepienia i pętla taśm za pomocą tooled gniazda na zewnętrzne nawiasy CMA po każdej stronie systemu, aby zabezpieczyć pakiety kabel.
> 
>     ![Kable kierowane bez CMA](media/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>O wymaganiach dotyczących adresów IP

W przypadku węzłów sprzętu w pamięci podręcznej Azure FXT krawędzi filtr hybrydowego magazynu adresy IP są zarządzane przez oprogramowanie klastra.

Każdy węzeł wymaga co najmniej jeden adres IP, ale węzła adresy są przypisywane, gdy węzły są dodawane do lub usunięte z klastra. 

Łączna liczba adresów IP, wymagane jest zależna od liczby węzłów w wchodzących w skład pamięci podręcznej. 

Za pomocą oprogramowania do panelu sterowania, po zainstalowaniu węzły, należy skonfigurować zakres adresów IP. Aby dowiedzieć się więcej, przeczytaj [Zbierz informacje dotyczące klastra](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Podłącz kable zasilania

Każdy węzeł filtr Edge FXT Azure używa dwóch jednostek dostaw energii (PSUs). 

> [!TIP] 
> Aby móc korzystać z dwóch PSUs nadmiarowe, należy dołączyć każdego AC przewód zasilający do jednostki dystrybucji zasilania (PDU) na obwód niezależnych gałęzi.  
> 
> UPS służy do PDU dodatkową ochronę. 

1. Połącz się PSUs w obudowie z przewodów zasilania dołączony. Upewnij się, w pełni siedzących kabli i PSUs. 
1. Dołącz przewodów zasilania do jednostki dystrybucji zasilania w stojaku urządzenia. Jeśli to możliwe na użytek dwa źródła zasilania oddzielne dwóch kabli. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Włącz filtr Edge FXT Azure węzła

Do włączenia w węźle, naciśnij przycisk zasilania na pierwszej stronie system. Przycisk znajduje się na panelu sterowania po prawej stronie. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Wyłącz węzeł filtr Edge FXT platformy Azure

Przycisk zasilania może służyć do zamykania systemu podczas testowania i przed dodaniem go do klastra. Jednak po węzłem filtr Edge FXT Azure jest używana jako część klastra, należy użyć oprogramowanie klastra do panelu sterowania do zamykania sprzętu. Odczyt [jak bezpiecznie wyłączyć filtr Edge FXT Azure sprzętu](fxt-power-off.md) Aby uzyskać szczegółowe informacje. 

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu, okablowania sprzętu i zasilania na każdym z węzłów i ich inicjowania, ustawiając swoich haseł w katalogu głównego. 
> [!div class="nextstepaction"]
> [Hasła początkowego zestawu](fxt-node-password.md)
