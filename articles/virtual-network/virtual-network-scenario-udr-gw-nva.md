---
title: Połączenie hybrydowe z aplikacji warstwy 2 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Wdrażanie wirtualnych urządzeń sieciowych i trasa zdefiniowana przez użytkownika, aby utworzyć środowisko aplikacji wielowarstwowej na platformie Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: c959ee3bea24955e3281feb9db66e4e0cadc8bf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034159"
---
# <a name="virtual-appliance-scenario"></a>Urządzenie wirtualne scenariusza
Typowy scenariusz w większych klientów platformy Azure to konieczność zapewnienia dwuwarstwowej aplikacji, połączenie z Internetem, zapewniając dostęp do tyłu warstwy z lokalnego centrum danych. Ten dokument przeprowadzi Cię scenariusz przy użyciu tras zdefiniowanych użytkownika (UDR), bramy sieci VPN i wirtualnych urządzeń sieciowych przeprowadzić wdrożenie w środowisku dwuwarstwowa, która spełnia następujące wymagania:

* Aplikacja sieci Web muszą być dostępne z publicznego Internetu.
* Serwer sieci Web hostuje aplikację musi mieć możliwość dostępu do serwera aplikacji zaplecza.
* Cały ruch z Internetu do aplikacji sieci web musi przejść przez urządzenie wirtualne zapory. To urządzenie wirtualne będą używane tylko dla ruchu internetowego.
* Całego ruchu kierowanego do serwera aplikacji musi przejść przez urządzenie wirtualne zapory. To urządzenie wirtualne będą używane dla dostępu do serwera wewnętrznej bazy danych w celu i dostępu odbierane z siecią lokalną za pośrednictwem bramy sieci VPN.
* Administratorzy muszą mieć możliwość zarządzania urządzenie wirtualne zapory z ich komputerów w środowisku lokalnym, za pomocą innego zapory używane wyłącznie do celów zarządzania urządzenia wirtualnego.

Jest to standardowy scenariusz sieci obwodowej i chronionej sieci. Taki scenariusz można konstruować na platformie Azure przy użyciu sieciowych grup zabezpieczeń, urządzenie wirtualne zapory lub jako kombinację obu tych. W poniższej tabeli przedstawiono niektóre z zalet i wad między grup zabezpieczeń sieci i zapory wirtualnych urządzeń sieciowych.

|  | Specjaliści | Wady |
| --- | --- | --- |
| Sieciowa grupa zabezpieczeń |Żadnych kosztów. <br/>Zintegrowany RBAC platformy Azure. <br/>Zasady mogą być tworzone w szablonach ARM. |Złożoność może się różnić w większych środowiskach. |
| Zapora |Pełną kontrolę nad płaszczyzny danych. <br/>Centralne zarządzanie za pomocą konsoli zapory. |Koszt urządzenia zapory. <br/>Nie jest zintegrowana z usługą Azure RBAC. |

Rozwiązanie poniżej używa urządzenie wirtualne zapory do implementacji scenariusza sieci obwodowej/chronione.

## <a name="considerations"></a>Zagadnienia do rozważenia
Możesz wdrożyć środowisko wyjaśniono powyżej na platformie Azure przy użyciu różnych funkcji dostępnych już dziś, wykonując następujące czynności.

* **Sieć wirtualna (VNet)**. Siecią wirtualną platformy Azure działa w podobny sposób, aby między siecią lokalną i można podzielić na co najmniej jednej podsieci w celu zapewnienia izolacji ruchu i separacji.
* **Urządzenie wirtualne**. Kilka etap to udostępnienie urządzeń wirtualnych w witrynie Azure Marketplace, który może służyć do trzech zapory, opisano powyżej. 
* **Trasy zdefiniowane przez użytkownika**. Tabele tras może zawierać tras zdefiniowanych przez użytkownika używane przez sieci platformy Azure, aby sterować przepływem pakietów w sieci wirtualnej. Te tabele tras można zastosować do podsieci. Jednym z najnowszych funkcji platformy Azure jest możliwość stosowania tabelę tras dla podsieci GatewaySubnet, zapewniając możliwość przekazywania wszystkich ruch w sieci wirtualnej platformy Azure za pośrednictwem połączenia hybrydowego do urządzenia wirtualnego.
* **Przesyłanie dalej IP**. Domyślnie aparat sieci platformy Azure przesyłania pakietów do wirtualne karty sieciowe (NIC), tylko wtedy, gdy pakiet docelowy adres IP jest zgodny z adresem IP karty Sieciowej. W związku z tym Jeśli trasa zdefiniowana przez użytkownika określa, że pakiet musi być wysyłane do danego urządzenia wirtualnego, aparat sieci platformy Azure będzie porzucić pakietu. Aby upewnić się, że jest dostarczany z maszyną wirtualną (w tym przypadku urządzenia wirtualnego), który nie jest rzeczywisty miejsce docelowe dla pakietów, musisz włączyć przekazywanie adresów IP dla urządzenia wirtualnego.
* **Sieciowe grupy zabezpieczeń (NSG)**. W poniższym przykładzie należy używać sieciowych grup zabezpieczeń, ale można użyć grup NSG stosowana do podsieci i/lub kart sieciowych w tym rozwiązaniu do dalszego filtrowania ruchu do i z tych podsieci i karty sieciowe.

![Łączność IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

W tym przykładzie ma subskrypcję, która zawiera następujące elementy:

* 2 grupy zasobów, nie są wyświetlane na diagramie. 
  * **ONPREMRG**. Zawiera wszystkie zasoby niezbędne do symulacji sieci lokalnej.
  * **AZURERG**. Zawiera wszystkie zasoby niezbędne dla środowiska sieci wirtualnej platformy Azure. 
* Sieć wirtualną o nazwie **onpremvnet** używany do naśladowania w lokalnym centrum danych segmentowanych wymienione poniżej.
  * **onpremsn1**. Podsieci zawierającej maszyny wirtualnej (VM) z systemem Ubuntu, aby mógł naśladować na serwerze lokalnym.
  * **onpremsn2**. Podsieci zawierającej maszyny Wirtualnej z systemem Ubuntu, aby mógł naśladować na komputerze lokalnym używane przez administratora.
* Występuje co urządzenie wirtualne zapory, o nazwie **OPFW** na **onpremvnet** służy do utrzymywania tunel do **azurevnet**.
* Sieć wirtualną o nazwie **azurevnet** segmentowanych wymienione poniżej.
  * **azsn1**. Podsieć zaporę zewnętrzną używanej wyłącznie przez zaporę zewnętrzną. Cały ruch z Internetu będzie pochodzić z tej podsieci. Ta podsieć zawiera tylko połączone z zapory zewnętrznej karty Sieciowej.
  * **azsn2**. Podsieć frontonu hostowania maszyny Wirtualnej z systemem jako serwer sieci web, który będzie dostępny z Internetu.
  * **azsn3**. Podsieci wewnętrznej bazy danych hostingu maszyny Wirtualnej z systemem serwera aplikacji zaplecza, która będzie dostępna przez serwer frontonu sieci web.
  * **azsn4**. Używane wyłącznie w celu zapewnienia dostępu do funkcji zarządzania do wszystkich urządzeń wirtualnych Zapora podsieci zarządzania. Ta podsieć zawiera tylko z kartą Sieciową dla każdego urządzenie wirtualne zapory, używane w ramach rozwiązania.
  * **GatewaySubnet**. Podsieć połączenia hybrydowego platformy Azure jest wymagana dla usługi ExpressRoute i bramy sieci VPN do zapewniania łączności między sieciami wirtualnymi platformy Azure i innymi sieciami. 
* Istnieją 3 urządzenie wirtualne zapory w **azurevnet** sieci. 
  * **AZF1**. Zaporę zewnętrzną, udostępniona do publicznej sieci Internet, za pomocą publicznego zasobu adresu IP na platformie Azure. Należy upewnić się, że szablon z witryny Marketplace lub bezpośrednio z dostawcą urządzenia, że przepisy 3-NIC urządzenia wirtualnego.
  * **AZF2**. Wewnętrzny zapory używane do kontrolowania ruchu między **azsn2** i **azsn3**. Dotyczy to również 3-NIC urządzenia wirtualnego.
  * **AZF3**. Zapora zarządzania dostępne dla administratorów z lokalnego centrum danych i podłączone do podsieci zarządzania używany do zarządzania wszystkie urządzenia zapory. Możesz znaleźć karty Sieciowej 2 Szablony urządzenie wirtualne w portalu Marketplace lub o nią wnioskować bezpośrednio z dostawcą urządzenia.

## <a name="user-defined-routing-udr"></a>Zdefiniowane przez użytkownika routingu (trasy zdefiniowanej przez użytkownika)
Każda podsieć w systemie Azure można połączyć tabelę trasy zdefiniowanej przez użytkownika używane do definiowania sposobu ruchu zainicjowane w tej podsieci jest kierowany. Jeśli zdefiniowano nie tras zdefiniowanych przez użytkownika, platforma Azure używa trasy domyślne, aby zezwolić na przepływ ruchu z jednej podsieci do innej. Aby lepiej zrozumieć tras zdefiniowanych przez użytkownika, odwiedź stronę [co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](virtual-networks-udr-overview.md).

Aby upewnić się, komunikacja odbywa się za pośrednictwem urządzenia zapory odpowiednie, na podstawie ostatniego wymagania, powyżej, należy utworzyć w poniższej tabeli tras zawierającą trasy Udr w **azurevnet**.

### <a name="azgwudr"></a>azgwudr
W tym scenariuszu tylko ruchem odbywającym się ze środowiska lokalnego do platformy Azure będzie służyć do zarządzania zapory, łącząc się z **AZF3**, oraz czy ruch musi przejść przez zaporę wewnętrznej **AZF2**. W związku z tym, w konieczne jest tylko jedna trasa **GatewaySubnet** jak pokazano poniżej.

| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Zezwala na ruch w środowisku lokalnym dotrzeć do zarządzania zaporą **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Zezwala na ruch do podsieci wewnętrznej bazy danych, który jest hostem aplikacji serwera za pomocą **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Zezwala na cały ruch będą kierowane za pośrednictwem **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Zezwala na ruch do **azsn2** do usługi flow z serwera aplikacji do serwera sieci Web za pośrednictwem **AZF2** |

Należy także utworzyć tabele tras dla podsieci w **onpremvnet** do naśladowania w lokalnym centrum danych.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Zezwala na ruch do **onpremsn2** za pośrednictwem **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Zezwala na ruch do podsieci, to jest poparte na platformie Azure za pośrednictwem **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Zezwala na ruch do **onpremsn1** za pośrednictwem **OPFW** |

## <a name="ip-forwarding"></a>Przesyłanie dalej IP
Trasa zdefiniowana przez użytkownika i przekazywanie adresów IP są funkcje, których można używać w połączeniu, aby zezwolić na urządzenie wirtualne, które ma być używany w sterowaniu przepływem ruchu w sieci wirtualnej platformy Azure.  Urządzenie wirtualne to po prostu maszyna wirtualna, na której działa aplikacja służąca do obsługi ruchu sieciowego w określony sposób, na przykład zapora lub urządzenie NAT.

Ta maszyna wirtualna musi mieć zdolność odbierania ruchu przychodzącego, który nie jest skierowany do niej samej. Aby umożliwić maszynie wirtualnej odbieranie ruchu kierowanego do innych miejsc docelowych, konieczne jest włączenie dla tej maszyny wirtualnej funkcji przesyłania dalej IP. Jest to ustawienie platformy Azure, a nie systemu operacyjnego gościa. Urządzenie wirtualne nadal musi zostać uruchomiony pewien rodzaj aplikacji do obsługi ruchu przychodzącego i kierowania go odpowiednio.

Aby dowiedzieć się więcej na temat przekazywania adresów IP, odwiedź stronę [co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](virtual-networks-udr-overview.md).

Na przykład załóżmy, że w sieci wirtualnej platformy Azure, masz następujące ustawienia:

* Podsieci **onpremsn1** zawiera maszynę Wirtualną o nazwie **onpremvm1**.
* Podsieci **onpremsn2** zawiera maszynę Wirtualną o nazwie **onpremvm2**.
* Urządzenie wirtualne, o nazwie **OPFW** jest podłączony do **onpremsn1** i **onpremsn2**.
* Trasy zdefiniowane przez użytkownika są połączone z **onpremsn1** Określa, że cały ruch do **onpremsn2** muszą być wysyłane do **OPFW**.

W tym momencie Jeśli **onpremvm1** podejmuje próbę nawiązania połączenia z **onpremvm2**, zdefiniowanej przez użytkownika, który będzie używany, a ruch zostanie wysłane do **OPFW** jako następnego przeskoku. Należy pamiętać o tym, że nie są zmieniane miejsca docelowego pakietu rzeczywiste, nadal wyświetlany jest tekst **onpremvm2** jest miejscem docelowym. 

Bez przekazywania adresów IP włączone dla **OPFW**, logiki sieci wirtualnej platformy Azure będzie się zmniejszać pakietów, ponieważ umożliwia ono tylko pakiety do wysłania do maszyny Wirtualnej, jeśli adres IP maszyny Wirtualnej jest miejscem docelowym dla pakietu.

Przy użyciu funkcji przesyłania dalej IP logiki sieci wirtualnej platformy Azure będzie przesyłać pakiety OPFW, bez zmiany jego oryginalnego adresu docelowego. **OPFW** musi obsługiwać pakiety i określić, co należy zrobić z nimi.

W powyższym scenariuszu działała, należy włączyć przekazywanie adresów IP na kartach interfejsu sieciowego dla **OPFW**, **AZF1**, **AZF2**, i **AZF3** służące do Routing (wszystkie karty sieciowe połączone z podsieci zarządzania wyjątkiem). 

## <a name="firewall-rules"></a>Reguły zapory
Zgodnie z powyższym opisem funkcji przesyłania dalej IP tylko tak gwarantuje, że pakiety są wysyłane do urządzenia wirtualnego. Urządzenie musi nadal zdecydować, co należy zrobić za pomocą tych pakietów. W tym scenariuszu powyżej konieczne będzie utworzenie następujących reguł w urządzeń:

### <a name="opfw"></a>OPFW
OPFW reprezentuje urządzenie w środowisku lokalnym, zawierające następujące reguły:

* **trasy**: Cały ruch do 10.0.0.0/16 (**azurevnet**) musi zostać wysłany za pośrednictwem tunelu **ONPREMAZURE**.
* **Zasady**: Zezwalaj na ruch dwukierunkowy pomiędzy **port2** i **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 reprezentuje urządzenie wirtualne Azure zawierające następujące reguły:

* **Zasady**: Zezwalaj na ruch dwukierunkowy pomiędzy **port1** i **port2**.

### <a name="azf2"></a>AZF2
AZF2 reprezentuje urządzenie wirtualne Azure zawierające następujące reguły:

* **trasy**: Cały ruch do 10.0.0.0/16 (**onpremvnet**) muszą być wysyłane do usługi Azure gateway adresu IP (np. 10.0.0.1), za pośrednictwem **port1**.
* **Zasady**: Zezwalaj na ruch dwukierunkowy pomiędzy **port1** i **port2**.

## <a name="network-security-groups-nsgs"></a>Network Security Groups (NSGs)
W tym scenariuszu nie są używane sieciowe grupy zabezpieczeń. Jednakże można zastosować sieciowe grupy zabezpieczeń w każdej podsieci, aby ograniczyć ruch przychodzący i wychodzący. Na przykład można zastosować następujące reguły sieciowej grupy zabezpieczeń do podsieci zewnętrznego środowiska .NET Framework.

**przychodzące**

* Zezwalaj na cały ruch TCP z Internetu do portu 80 na dowolnej maszynie Wirtualnej w podsieci.
* Odmowa cały pozostały ruch z Internetu.

**Wychodzące**

* Odmowa cały ruch do Internetu.

## <a name="high-level-steps"></a>Wysokiego poziomu kroków
Aby wdrożyć ten scenariusz, wykonaj poniższe kroki wysokiego poziomu.

1. Zaloguj się do subskrypcji platformy Azure.
2. Jeśli chcesz wdrożyć sieć wirtualną do naśladowania sieci lokalnej, zainicjować obsługę administracyjną zasobów, które są częścią **ONPREMRG**.
3. Inicjowanie obsługi administracyjnej zasobów, które są częścią **AZURERG**.
4. Aprowizowanie tunelu z **onpremvnet** do **azurevnet**.
5. Po udostępnieniu wszystkie zasoby, zaloguj się do **onpremvm2** i wysłać polecenie ping 10.0.3.101 do testowania łączności między **onpremsn2** i **azsn3**.

