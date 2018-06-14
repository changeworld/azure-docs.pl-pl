---
title: Połączenie hybrydowe z aplikacji warstwy 2 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć urządzeń wirtualnych i przez, aby utworzyć środowisko wielowarstwową aplikację na platformie Azure
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: jdial
ms.openlocfilehash: 544ba6484b23da425d53594622122b1e18b92359
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2017
ms.locfileid: "23943224"
---
# <a name="virtual-appliance-scenario"></a>Scenariusz urządzenie wirtualne
Typowy scenariusz większych klientów platformy Azure jest konieczność zapewnienia dwuwarstwowej aplikacji, połączenie z Internetem, umożliwiając dostęp do tyłu warstwy z lokalnego centrum danych. Ten dokument przeprowadzi użytkownika scenariusza przy użyciu użytkownika zdefiniowanych tras przez bramy sieci VPN i sieci wirtualnych urządzeń do wdrożenia środowiska dwuwarstwowa, która spełnia następujące wymagania:

* Aplikacja sieci Web musi być dostępny z publicznego Internetu.
* Hosting aplikacji serwera sieci Web musi mieć możliwość uzyskania dostępu do wewnętrznej bazy danych aplikacji serwera.
* Cały ruch z Internetu do aplikacji sieci web musi przechodzić przez urządzenie wirtualne zapory. To urządzenie wirtualne będą używane tylko ruchu internetowego.
* Całego ruchu kierowanego do serwera aplikacji musi przechodzić przez urządzenie wirtualne zapory. To urządzenie wirtualne będą używane do dostępu do serwera zaplecza mające na celu i odbierane z sieci lokalnej za pośrednictwem bramy sieci VPN dostępu.
* Administratorzy muszą mieć możliwość zarządzania urządzenie wirtualne zapory z ich komputerami lokalnymi, przy użyciu innej zapory używane wyłącznie do celów zarządzania urządzenie wirtualne.

Jest to standardowy scenariusz sieci obwodowej i chronionej sieci. Taki scenariusz może być skonstruowany w Azure za pomocą grup NSG, urządzenie wirtualne zapory lub obie te grupy. W poniższej tabeli przedstawiono niektóre z zalet i wad między grupy NSG oraz zapory urządzenia wirtualnego.

|  | Specjaliści | Wady |
| --- | --- | --- |
| GRUPA NSG |Bez kosztów. <br/>Zintegrowane usługi Azure RBAC. <br/>Reguły można tworzyć w szablonów ARM. |Złożoność może się różnić w większych środowiskach. |
| Zapora |Pełną kontrolę nad płaszczyzna danych. <br/>Centralne zarządzanie za pomocą konsoli zapory. |Koszt urządzenia zapory. <br/>Nie jest zintegrowany z Azure RBAC. |

Rozwiązanie poniżej używa urządzenie wirtualne zapory do implementacji scenariusza sieci obwodowej/chronione.

## <a name="considerations"></a>Zagadnienia do rozważenia
Możesz wdrożyć środowiska przedstawionych powyżej na platformie Azure przy użyciu różnych funkcji dostępnych już dziś, wykonując następujące czynności.

* **Sieć wirtualną (VNet)**. Sieć wirtualną platformy Azure działa w podobny sposób z siecią lokalną i może być segmentem w co najmniej jednej podsieci w celu zapewnienia izolacji ruchu danych i separacji.
* **Urządzenie wirtualne**. Kilka partnerów Podaj wirtualnego urządzenia w portalu Azure Marketplace, używanej do trzech zapory opisane powyżej. 
* **Zdefiniowane przez użytkownika tras (przez)**. Tabele tras może zawierać Udr używany przez sieć platformy Azure do sterowania przepływem pakietów w sieci wirtualnej. Te tabele tras może odnosić się do podsieci. Jedną z najnowszych funkcji platformy Azure jest możliwość stosowania tabelę tras dla GatewaySubnet, zapewniając możliwość przekazywania wszystkich danych przesyłanych w sieci wirtualnej platformy Azure przez połączenie hybrydowe do urządzenia wirtualnego.
* **Przesyłanie dalej IP**. Domyślnie aparat sieci Azure przesyłania pakietów do karty interfejsu sieci wirtualnej (NIC) tylko wtedy, gdy pakiet docelowy adres IP odpowiada adresu IP karty Sieciowej. W związku z tym jeśli przez Określa, że pakiet musi być wysyłane do danego urządzenia wirtualnego, aparat sieci platformy Azure będzie porzucić pakietu. Aby upewnić się, że pakiet jest dostarczany z maszyną wirtualną (w tym przypadku urządzenia wirtualnego), który nie jest rzeczywistego przeznaczenia pakietu, należy włączyć przesyłania dalej protokołu IP dla urządzenia wirtualnego.
* **Sieciowe grupy zabezpieczeń (NSG)**. W poniższym przykładzie nie korzystają z grup NSG, ale można dodatkowo filtrować ruch do i z tych podsieci i karty sieciowe za pomocą grup NSG stosowana do podsieci i/lub kart sieciowych w tym rozwiązaniu.

![Łączność IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

W tym przykładzie jest subskrypcji, która zawiera następujące elementy:

* 2 grup zasobów, nie są wyświetlane na diagramie. 
  * **ONPREMRG**. Zawiera wszystkie zasoby niezbędne do symulacji sieci lokalnej.
  * **AZURERG**. Zawiera wszystkie zasoby niezbędne do środowiska sieci wirtualnej platformy Azure. 
* Sieć wirtualną o nazwie **onpremvnet** umożliwia naśladować lokalnego centrum danych segmentem wymienione poniżej.
  * **onpremsn1**. Podsieci zawierającej maszynę wirtualną (VM) systemem Ubuntu, aby naśladował na serwerze lokalnym.
  * **onpremsn2**. Podsieć zawierająca maszyny Wirtualnej z systemem Ubuntu, aby naśladował na komputerze lokalnym używane przez administratora.
* Brak co urządzenie wirtualne zapory o nazwie **OPFW** na **onpremvnet** służy do utrzymywania tunel do **azurevnet**.
* Sieć wirtualną o nazwie **azurevnet** segmentowanych wymienione poniżej.
  * **azsn1**. Zapory zewnętrznej podsieci używane wyłącznie do zapory zewnętrznej. Cały ruch internetowy wejdzie do tej podsieci. Ta podsieć zawiera tylko połączone z zapory zewnętrznej karty Sieciowej.
  * **azsn2**. Hosting maszyny Wirtualnej działa jako serwer sieci web, w której będą mieli dostęp z Internetu podsieci frontonu.
  * **azsn3**. Hosting maszyny Wirtualnej z systemem serwer aplikacji zaplecza, w której będą mieli dostęp przez serwer frontonu sieci web podsieci wewnętrznej bazy danych.
  * **azsn4**. Zarządzanie podsieci używane wyłącznie w celu zapewnienia dostępu administracyjnego do wszystkich urządzeń wirtualnych zapory. Ta podsieć zawiera tylko z kartą Sieciową dla każdej zapory urządzenie wirtualne użyte w rozwiązaniu.
  * **GatewaySubnet**. Wymagane w celu ExpressRoute i Brama sieci VPN do zapewniania łączności między sieciami wirtualnymi platformy Azure i innych sieci podsieci połączenia hybrydowe platformy Azure. 
* Istnieją 3 urządzenie wirtualne zapory w **azurevnet** sieci. 
  * **AZF1**. Połączenie z Internetem publiczny przy użyciu zasób publicznego adresu IP na platformie Azure zapory zewnętrznej. Należy upewnić się, że szablon z witryny Marketplace lub bezpośrednio z dostawcą urządzenia, że przepisy 3-NIC urządzenie wirtualne.
  * **AZF2**. Wewnętrzny zapory używane do kontroli ruchu między **azsn2** i **azsn3**. Dotyczy to również 3-NIC urządzenie wirtualne.
  * **AZF3**. Zarządzanie zaporą dostępne dla administratorów z lokalnego centrum danych i jest połączony z podsiecią zarządzania używany do zarządzania wszystkie urządzenia zapory. Można znaleźć karty Sieciowej 2 Szablony urządzenie wirtualne w witrynie Marketplace, lub zażądać bezpośrednio z dostawcą urządzenia.

## <a name="user-defined-routing-udr"></a>Zdefiniowane przez użytkownika routingu (przez)
Każda podsieć na platformie Azure może odnosić się do tabeli przez używane do definiowania jak ruchu zainicjowane w tej podsieci jest kierowany. Jeśli są zdefiniowane nie Udr, platforma Azure korzysta trasy domyślne zezwalająca na ruch przepływać z jednej podsieci. Aby lepiej zrozumieć Udr, odwiedź stronę [co to są trasy zdefiniowane przez użytkownika i przesyłania dalej protokołu IP](virtual-networks-udr-overview.md).

Aby upewnić się, komunikacja odbywa się za pośrednictwem urządzenie prawym zapory, oparte na ostatnie wymaganie powyżej, należy utworzyć w poniższej tabeli tras zawierający Udr w **azurevnet**.

### <a name="azgwudr"></a>azgwudr
W tym scenariuszu tylko ruch wynikających z lokalnego do platformy Azure będzie służyć do zarządzania zapory, łącząc się z **AZF3**, i że ruchu musi przechodzić przez zaporę wewnętrznej **AZF2**. W związku z tym niezbędne jest tylko jedna trasa **GatewaySubnet** jak pokazano poniżej.

| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Zezwala na ruch do osiągnięcia zapory zarządzania lokalnymi **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Zezwala na ruch do podsieci wewnętrznej bazy danych hostem aplikacji serwera za pomocą **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Zezwala na cały ruch będzie kierowany przez **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Zezwala na ruch do **azsn2** przepływem z aplikacji serwera na serwerze sieci Web za pośrednictwem **AZF2** |

Należy także utworzyć tabel tras dla podsieci w **onpremvnet** aby naśladował lokalnego centrum danych.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Zezwala na ruch do **onpremsn2** za pośrednictwem **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Element docelowy | Następny przeskok | Wyjaśnienie |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Zezwala na ruch do podsieci kopii zapasowej na platformie Azure za pośrednictwem **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Zezwala na ruch do **onpremsn1** za pośrednictwem **OPFW** |

## <a name="ip-forwarding"></a>Przesyłanie dalej IP
PRZEZ i przekazywanie adresów IP są funkcje, których można używać w połączeniu, aby umożliwić urządzeń wirtualnych do użycia w sterowaniu przepływem ruchu w sieci wirtualnej platformy Azure.  Urządzenie wirtualne to po prostu maszyna wirtualna, na której działa aplikacja służąca do obsługi ruchu sieciowego w określony sposób, na przykład zapora lub urządzenie NAT.

Ta maszyna wirtualna musi mieć zdolność odbierania ruchu przychodzącego, który nie jest skierowany do niej samej. Aby umożliwić maszynie wirtualnej odbieranie ruchu kierowanego do innych miejsc docelowych, konieczne jest włączenie dla tej maszyny wirtualnej funkcji przesyłania dalej IP. Jest to ustawienie platformy Azure, a nie systemu operacyjnego gościa. Twoje urządzenie wirtualne nadal wymaga do uruchomienia niektórych typ aplikacji do obsługi ruchu przychodzącego i kierowania go odpowiednio.

Aby dowiedzieć się więcej na temat przekazywania adresów IP, odwiedź stronę [co to są trasy zdefiniowane przez użytkownika i przesyłania dalej protokołu IP](virtual-networks-udr-overview.md).

Na przykład załóżmy, że w sieci wirtualnej platformy Azure są następujące ustawienia:

* Podsieci **onpremsn1** zawiera maszynę Wirtualną o nazwie **onpremvm1**.
* Podsieci **onpremsn2** zawiera maszynę Wirtualną o nazwie **onpremvm2**.
* Urządzenie wirtualne o nazwie **OPFW** jest podłączony do **onpremsn1** i **onpremsn2**.
* Trasy zdefiniowane przez użytkownika są połączone z **onpremsn1** Określa, że cały ruch do **onpremsn2** muszą zostać przesłane do **OPFW**.

W tym momencie Jeśli **onpremvm1** podejmuje próbę nawiązania połączenia z **onpremvm2**, będą używane przez i ruchu zostaną wysłane do **OPFW** w następnym skoku. Należy pamiętać, że docelowy rzeczywiste pakietów nie są zmieniane, nadal wyświetlany jest tekst **onpremvm2** jest miejscem docelowym. 

Bez przesyłania dalej protokołu IP włączone dla **OPFW**, Azure logiki sieci wirtualnych spowoduje porzucenie pakietów, ponieważ umożliwia tylko pakiety, które mają być wysyłane do maszyny Wirtualnej, jeśli adres IP maszyny Wirtualnej jest docelowy dla pakietu.

Z przesyłania dalej protokołu IP logiki sieci wirtualnej platformy Azure będzie przesyłać pakiety OPFW, bez zmiany jego oryginalny adres docelowy. **OPFW** musi obsługiwać pakiety i ustalić, co należy zrobić z nich.

W powyższym scenariuszu działała, należy włączyć przesyłania dalej protokołu IP kart sieciowych dla **OPFW**, **AZF1**, **AZF2**, i **AZF3** służące do routingu (wszystkie karty sieciowe oprócz tych połączonych z podsiecią zarządzania). 

## <a name="firewall-rules"></a>Reguły zapory
Zgodnie z powyższym opisem tylko przesyłania dalej protokołu IP zapewnia, że pakiety są wysyłane do urządzenia wirtualnego. Urządzenia są nadal wymaga do podejmowania decyzji o tych pakietów. W powyższym scenariuszu konieczne będzie utworzenie następujących reguł w urządzeń:

### <a name="opfw"></a>OPFW
OPFW reprezentuje lokalnego urządzenia zawierających następujące reguły:

* **Trasy**: cały ruch do 10.0.0.0/16 (**azurevnet**) musi być przesyłane przez tunel **ONPREMAZURE**.
* **Zasady**: zezwolić na cały ruch dwukierunkowy między **port2** i **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 reprezentuje urządzenie wirtualne Azure zawierających następujące reguły:

* **Zasady**: zezwolić na cały ruch dwukierunkowy między **port1** i **port2**.

### <a name="azf2"></a>AZF2
AZF2 reprezentuje urządzenie wirtualne Azure zawierających następujące reguły:

* **Trasy**: cały ruch do 10.0.0.0/16 (**onpremvnet**) musi zostać wysłany do bramy Azure adresu IP (np. 10.0.0.1), przez **port1**.
* **Zasady**: zezwolić na cały ruch dwukierunkowy między **port1** i **port2**.

## <a name="network-security-groups-nsgs"></a>Grupy zabezpieczeń sieci (NSG)
W tym scenariuszu nie są używane grupy NSG. Można jednak zastosować grupy NSG do każdej podsieci, aby ograniczyć ruch przychodzący i wychodzący. Na przykład można zastosować następujące zasady grupy NSG do zewnętrznego podsieci środowiska .NET Framework.

**Przychodzące**

* Zezwolić na cały ruch TCP z Internetu do portu 80 w żadnej maszyny Wirtualnej należących do podsieci.
* Odmowa cały ruch z Internetu.

**Wychodzące**

* Odmowa cały ruch do Internetu.

## <a name="high-level-steps"></a>Wysokiego poziomu kroków
Aby wdrożyć ten scenariusz, wykonaj poniższe kroki wysokiego poziomu.

1. Zaloguj się do subskrypcji platformy Azure.
2. Jeśli chcesz wdrożyć sieci wirtualnej, aby naśladował sieci lokalnej, należy udostępnić zasoby, które są częścią **ONPREMRG**.
3. Zapewnij zasoby, które są częścią **AZURERG**.
4. Zapewnij tunelu z **onpremvnet** do **azurevnet**.
5. Po udostępnieniu wszystkie zasoby, zaloguj się do **onpremvm2** i zbadaj 10.0.3.101 do testowania łączności między **onpremsn2** i **azsn3**.

