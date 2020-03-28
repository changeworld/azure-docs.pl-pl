---
title: 'Samouczek: Łączenie kabli z plikiem plików usługi Azure FXT Edge'
description: Jak podłączyć porty sieciowe i podłączyć zasilanie dla sprzętu Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239784"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Samouczek: Nawiązywać połączenia sieciowe i zasilać węzeł Filer usługi Azure FXT Edge

W tym samouczku opisano, jak podłączyć połączenia sieciowe dla węzła sprzętowego usługi Azure FXT Edge Filer.

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jak wybrać typ kabla sieciowego dla środowiska
> * Jak podłączyć węzeł filera usługi Azure FXT Edge do sieci centrów danych
> * Jak kierować kable przez ramię zarządzania kablami (CMA)
> * Jak podłączyć zasilanie do urządzenia stelaża i włączyć go

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka azure FXT Edge Filer powinny być zainstalowane w stojaku sprzętu standardowego. Cma powinny być zainstalowane w węźle filer. 

## <a name="identify-ports"></a>Identyfikowanie portów

Zidentyfikuj różne porty z tyłu urządzenia Azure FXT Edge Filer. 
 
![Tył urządzenia kablowego](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

* Podłącz porty RJ-45 do źródła sieciowego centrum danych zgodnie z opisem w [portach sieciowych.](#network-ports)  
* Bezpiecznie podłącz [port iDRAC](#idrac-port) do oddzielnej sieci za pomocą bezpiecznego serwera DHCP. 
* Użyj portów USB i portu VGA, aby podłączyć klawiaturę i monitor do węzła w celu wstępnej konfiguracji. Należy uruchomić węzeł i [ustawić hasło początkowe,](fxt-node-password.md) aby aktywować inne porty węzła. Przeczytaj [ustaw początkowe hasła,](fxt-node-password.md) aby uzyskać szczegółowe informacje. 

W tym artykule opisano również sposób [łączenia zasilania prądem zmiennym](#connect-power-cables) dla węzła. 

W tym artykule wyjaśniono również, jak połączyć się z [portem szeregowym](#serial-port-only-when-necessary)węzła, jeśli to konieczne do specjalistycznego rozwiązywania problemów. 

### <a name="network-ports"></a>Porty sieciowe 

Każdy węzeł usługi Azure FXT Edge Filer zawiera następujące porty sieciowe: 

* Sześć szybkich portów danych o podwójnej szybkości 25 GbE/10 GbE: 

  * Cztery porty dostarczane przez dwie dwuportowe karty sieciowe wtyczek
  * Dwa porty dostarczane przez kartę sieciową na antresoli płyty głównej 

* Dwa porty 1GbE dostarczane przez kartę sieciową na antresoli płyty głównej 

Szybkie porty danych 25GbE/10GbE są zgodne ze standardowymi klatkami zgodnymi ze standardami SFP28. Aby korzystać z kabli optycznych, należy zainstalować moduły transceiverów optycznych SFP28 (nie podana).

Porty 1GbE mają standardowe złącza RJ-45.

Aby uzyskać pełną listę obsługiwanych kabli, przełączników i tranzysjerów nadawczo-odbiorczych, zapoznaj się z [matrycą interoperacyjności serii Cavium FastlinQ 41000.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)

Typ połączeń używanych w systemie zależy od środowiska centrum danych.

* W przypadku podłączenia do sieci 25GbE należy podłączyć każdy z szybkich portów danych za pomocą jednego z następujących typów kabli:

  * Kabel optyczny i urządzenie nadawczo-odbiorcze optyczne SFP28 z funkcją 25GbE lub dual rate 25GbE/10GbE
  * Kabel twinaxial z obsługą sfp28 typu 25GbE

* W przypadku podłączenia do sieci 10GbE należy podłączyć każdy z szybkich portów danych, aby połączyć jeden z następujących elementów: 

  * Kabel optyczny i urządzenie nadawczo-odbiorcze optyczne SFP28 z funkcją 10GbE lub dual rate 25GbE/10GbE.
  * Kabel twinaxial z obsługą sfp28 typu 25GbE
  * Kabel twinaxial z obsługą sfp28 typu 10GbE

* Porty sieciowe 1GbE są używane do zarządzania klastrami. Zaznacz opcję **Użyj sieci mgmt 1 Gb** podczas tworzenia klastra, jeśli chcesz utworzyć fizycznie oddzielną sieć dla konfiguracji klastra (opisaną w temacie [Konfigurowanie sieci zarządzania](fxt-cluster-create.md#configure-the-management-network)). Kabel portów standardowym kablem Cat5 lub lepszym, jak opisano na liście obsługiwanych kabli.

  Porty 1GbE można pozostawić bez szklenia, jeśli planujesz używać szybkich portów dla całego ruchu. Domyślnie porty sieciowe 1GbE nie są używane, jeśli dostępny jest port danych o większej szybkości.  

### <a name="idrac-port"></a>Port iDRAC  

Port oznaczony iDRAC to połączenie 1Gb, które umożliwia komunikację z kontrolerem dostępu zdalnego używanym do zarządzania sprzętem i monitorowania. Oprogramowanie FXT wykorzystuje interfejs IPMI (Intelligent Platform Management Interface) z tym kontrolerem do rozwiązywania problemów i odzyskiwania. Za pomocą wbudowanego [interfejsu iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) można monitorować sprzęt za pośrednictwem tego portu. Dostęp do iDRAC i IPMI jest domyślnie włączony. 

> [!Note]
> Port iDRAC może ominąć system operacyjny i wchodzić w interakcje bezpośrednio ze sprzętem w węźle. 

Użyj tych strategii zabezpieczeń podczas podłączania i konfigurowania portu iDRAC:

* Porty iDRAC należy łączyć tylko z siecią fizycznie oddzieloną od sieci danych używanej do uzyskiwania dostępu do klastra.
* Ustaw bezpieczne hasło administratora iDRAC w każdym węźle. Musisz ustawić to hasło, aby aktywować sprzęt - postępuj zgodnie z instrukcjami w [Ustaw hasła sprzętowe](fxt-node-password.md).
* Domyślna konfiguracja portu iDRAC używa DHCP i IPv4 do przypisywania adresów IP. Upewnij się, że środowisko DHCP jest dobrze chronione i że połączenia są ograniczone między klientami DHCP a serwerem DHCP. (Panel sterowania klastra zawiera ustawienia, aby zmienić metodę konfiguracji adresów węzłów po utworzeniu klastra).
* Pozostaw port iDRAC ustawiony na "tryb dedykowany" (domyślny), który ogranicza ruch sieciowy iDRAC/IPMI do dedykowanego portu RJ-45.

Port iDRAC nie wymaga szybkiego połączenia sieciowego.
  
### <a name="serial-port-only-when-necessary"></a>Port szeregowy (tylko wtedy, gdy jest to konieczne)

W niektórych sytuacjach usługa i pomoc techniczna firmy Microsoft mogą na zostać nawiązywać do połączenia terminala z portem szeregowym węzła w celu zdiagnozowania problemu.  

Aby podłączyć konsolę:

1. Znajdź port szeregowy (COM1) z tyłu węzła FXT Edge Filer.
1. Użyj kabla modemu zerowego, aby podłączyć port szeregowy do terminalu skonfigurowanego dla ansi-115200-8N1.
1. Zaloguj się do konsoli i podejmij inne kroki zgodnie z zaleceniami personelu pomocy technicznej.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Kable trasy w ramieniu zarządzania kablami (CMA)

Każdy węzeł usługi Azure FXT Edge Filer jest wyposażony w opcjonalne ramię zarządzania kablami. Cma upraszcza prowadzenie kabli i zapewnia łatwiejszy dostęp do tylnej części obudowy bez konieczności odłączania kabli. 

Postępuj zgodnie z poniższymi instrukcjami, aby przekierować kable przez cma: 

1. Za pomocą dostarczonych okładów, zawiązuj kable, gdy wchodzą i wychodzą z koszy, aby nie kolidowały z sąsiednimi systemami (1).
1. Gdy cma znajduje się w pozycji serwisowej, posyłaj wiązkę kabli przez kosze wewnętrzne i zewnętrzne (2).
1. Użyj preinstalowanych pasków hakowych i zapętlonych na obu końcach koszy, aby zabezpieczyć kable (3).
1. Wysuń CMA z powrotem na miejsce na tacy (4).
1. Zainstaluj kabel wskaźnika stanu z tyłu systemu i zabezpiecz kabel, kierując go przez CMA. Przymocuj drugi koniec kabla do rogu zewnętrznego kosza CMA (5). 

   > [!CAUTION]
   > Aby uniknąć potencjalnych uszkodzeń spowodowanych wystającymi kablami, należy zabezpieczyć luz w kablu wskaźnika stanu po przekierowaniu tego kabla przez cma. 

![Ilustracja CMA z zainstalowanymi kablami](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Jeśli nie zainstalowano CMA, użyj dwóch pasków hakowych i zapętlanych dostarczonych w zestawie szyn, aby poprowadzić kable z tyłu systemu.
> 
>  1. Zlokalizuj zewnętrzne wsporniki CMA po wewnętrznych stronach obu kołnierzy.
>  2. Delikatnie zapakuj kable, wyciągając je z złączy systemowych po lewej i prawej stronie.
>  3. Nawlecz paski haka i pętli przez oprzyrządowane szczeliny na zewnętrznych wspornikach CMA po każdej stronie systemu, aby zabezpieczyć wiązki kabli.
> 
>     ![Kable poprowadzone bez CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Wymagania dotyczące adresów IP — informacje

W przypadku węzłów sprzętowych w hybrydowej pamięci podręcznej magazynu usługi Azure FXT Edge Adresy IP są zarządzane przez oprogramowanie klastra.

Każdy węzeł wymaga co najmniej jednego adresu IP, ale adresy węzłów są przypisywane, gdy węzły są dodawane lub usuwane z klastra. 

Całkowita liczba wymaganych adresów IP zależy od liczby węzłów w tym tworzą pamięć podręczną. 

Skonfiguruj zakres adresów IP za pomocą oprogramowania Panelu sterowania po zainstalowaniu węzłów. Aby dowiedzieć się więcej, przeczytaj artykuł [Zbieranie informacji dla klastra](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Podłączanie kabli zasilających

Każdy węzeł usługi Azure FXT Edge Filer używa dwóch zasilaczy (PSU). 

> [!TIP] 
> Aby skorzystać z dwóch nadmiarowych zasilaczy, podłącz każdy kabel zasilania prądem przemiennym do jednostki dystrybucji zasilania (PDU) w niezależnym obwodzie odgałęzienia.  
> 
> Zasilacza UPS można użyć do zasilania urządzeń PDU w celu dodatkowej ochrony. 

1. Podłącz dołączone przewody zasilające do zasilaczy w obudowie. Upewnij się, że przewody i zasilacze są w pełni osadzone. 
1. Podłącz przewody zasilające do jednostek rozdzielczych zasilania w szafie na urządzenia. Jeśli to możliwe, użyj dwóch oddzielnych źródeł zasilania dla dwóch kabli. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Włączanie węzła filera usługi Azure FXT Edge

Aby włączyć węzeł, naciśnij przycisk zasilania z przodu systemu. Przycisk znajduje się na prawym panelu sterowania po prawej stronie. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Wyłącz węzeł filera usługi Azure FXT Edge

Przycisk zasilania może służyć do wyłączania systemu podczas testowania i przed dodaniem go do klastra. Jednak po węźle Azure FXT Edge Filer jest używany jako część klastra, należy użyć oprogramowania panelu sterowania klastra, aby zamknąć sprzęt. Przeczytaj [jak bezpiecznie wyłączyć sprzęt Azure FXT Edge Filer, aby](fxt-power-off.md) uzyskać szczegółowe informacje. 

## <a name="next-steps"></a>Następne kroki

Po zakończeniu okablowania sprzętu włącz każdy z węzłów i zaiszcz je, ustawiając hasła główne. 
> [!div class="nextstepaction"]
> [Ustawianie haseł początkowych](fxt-node-password.md)
