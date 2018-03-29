---
title: O technologiach sieciowych w odzyskiwania po awarii z platformy Azure do platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Omówienie sieci w przypadku replikacji maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/26/2018
ms.author: sujayt
ms.openlocfilehash: 48be55632d9c1bece3f1a6e4f9ac12a68f9cb7ab
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>O technologiach sieciowych w replikacji Azure do platformy Azure

>[!NOTE]
> Replikacja lokacji odzyskiwania maszyn wirtualnych platformy Azure jest obecnie w przeglądzie.

Ten artykuł zawiera wskazówki dotyczące sieci podczas replikacji i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego, przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Usługa Site Recovery zapewnia odzyskiwanie po awarii dla [w tym scenariuszu](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typowej infrastruktury sieciowej

Poniższy diagram przedstawia typowy środowiska platformy Azure dla aplikacji działających na maszynach wirtualnych platformy Azure:

![środowisko klienta](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Jeśli używasz usługi Azure ExpressRoute lub połączeń sieci VPN z sieci lokalnej na platformie Azure, środowisko wygląda następująco:

![środowisko klienta](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Zazwyczaj sieci są chronione za pomocą zapory i grup zabezpieczeń sieci (NSG). Zapory używać adresu URL lub opartego na protokole IP listę dozwolonych podobnej do sterowania łączność sieciową. Grupa NSG dostarcza reguł, które umożliwia kontrolowanie łączności sieciowej zakresów adresów IP.

>[!IMPORTANT]
> Przy użyciu z uwierzytelnionego serwera proxy do łączności sieciowej sterowania nie jest obsługiwany przez usługę Site Recovery i nie można włączyć replikacji.


## <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapora oparta na adres URL do kontrolowania łączność wychodząca, Zezwalaj na te adresy URL odzyskiwania lokacji:


**Adres URL** | **Szczegóły**  
--- | ---
*.blob.core.windows.net | Wymagane, aby dane mogą być zapisywane na konto magazynu pamięci podręcznej w regionie źródła z maszyny Wirtualnej.
login.microsoftonline.com | Wymagane w celu autoryzacji i uwierzytelnianie adresów URL usługi Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja usługi Site Recovery może wystąpić z maszyny Wirtualnej.
*.servicebus.windows.net | Wymagane, dzięki czemu można będzie można zapisywać danych monitorowania i diagnostyki usługi Site Recovery z maszyny Wirtualnej.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz serwera proxy oparte na protokole IP zapory lub reguły NSG do kontrolowania łączność wychodząca te zakresy IP, musisz zezwolić.

- Wszystkie zakresy adresów IP odpowiadające lokalizacji źródłowej.
    - Możesz pobrać [zakresów adresów IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Musisz zezwolić na te adresy tak, aby dane mogą być zapisywane na koncie magazynu pamięci podręcznej z maszyny Wirtualnej.
- Wszystkie zakresy adresów IP, które odpowiadają usługi Office 365 [uwierzytelnianie i tożsamość punkty końcowe IPv4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
    - Jeśli nowy adres są dodawane do zakresów usługi Office 365 w przyszłości, należy utworzyć nowe reguły NSG.
- Punkt końcowy usługi odzyskiwania lokacji adresów IP. Są one dostępne w [pliku XML](https://aka.ms/site-recovery-public-ips)i zależą od lokalizacji docelowej.
-  Możesz [pobranie i użycie tego skryptu](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702), aby automatycznie utworzyć wymagane zasady w grupie NSG. 
- Zaleca się tworzenie wymaganych reguł NSG na test NSG i sprawdź, czy nie ma żadnych problemów przed utworzeniem reguły produkcyjnych NSG.
- Aby utworzyć wymaganej liczby elementów reguły NSG, upewnij się, że Twoja subskrypcja jest białej. Skontaktuj się z pomocą Azure obsługuje zwiększania limitu reguły NSG w ramach subskrypcji.

Zakresy adresów IP są następujące:

>
   **docelowy** | **Odzyskiwanie lokacji IP** |  **Usługa Site Recovery monitorowania IP**
   --- | --- | ---
   Azja Wschodnia | 52.175.17.132 | 13.94.47.61
   Azja Południowo-Wschodnia | 52.187.58.193 | 13.76.179.223
   Indie Środkowe | 52.172.187.37 | 104.211.98.185
   Indie Południowe | 52.172.46.220 | 104.211.224.190
   Środkowo-północne stany USA | 23.96.195.247 | 168.62.249.226
   Europa Północna | 40.69.212.238 | 52.169.18.8
   Europa Zachodnia | 52.166.13.64 | 40.68.93.145
   Wschodnie stany USA | 13.82.88.226 | 104.45.147.24
   Zachodnie stany USA | 40.83.179.48 | 104.40.26.199
   Środkowo-południowe stany USA | 13.84.148.14 | 104.210.146.250
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144
   Wschodnie stany USA 2 | 52.184.158.163 | 40.79.44.59
   Japonia Wschodnia | 52.185.150.140 | 138.91.1.105
   Japonia Zachodnia | 52.175.146.69 | 138.91.17.38
   Brazylia Południowa | 191.234.185.172 | 23.97.97.36
   Australia Wschodnia | 104.210.113.114 | 191.239.64.144
   Australia Południowo-Wschodnia | 13.70.159.158 | 191.239.160.45
   Kanada Środkowa | 52.228.36.192 | 40.85.226.62
   Kanada Wschodnia | 52.229.125.98 | 40.86.225.142
   Środkowo-zachodnie stany USA | 52.161.20.168 | 13.78.149.209
   Zachodnie stany USA 2 | 52.183.45.166 | 13.66.228.204
   Zachodnie Zjednoczone Królestwo | 51.141.3.203 | 51.141.14.113
   Południowe Zjednoczone Królestwo | 51.140.43.158 | 51.140.189.52
   Południowe Zjednoczone Królestwo 2 | 13.87.37.4| 13.87.34.139
   Północne Zjednoczone Królestwo | 51.142.209.167 | 13.87.102.68
   Korea Środkowa | 52.231.28.253 | 52.231.32.85
   Korea Południowa | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja grupy NSG

Ten przykład przedstawia sposób konfigurowania reguły NSG dla maszyny Wirtualnej do replikacji. 

- Jeśli używasz reguły NSG do kontrolowania łączność wychodząca, za pomocą reguł "Zezwalaj HTTPS wychodzącego" dla wszystkich wymaganych zakresy adresów IP.
- Przykładzie przyjęto założenie, że lokalizacja źródłowa maszyna wirtualna jest "Wschodnie nam" i lokalizacja docelowa jest "środkowe stany USA.

### <a name="nsg-rules---east-us"></a>Reguły NSG — wschodnie stany USA

1. Tworzenie reguł, które odpowiadają [Wschodnia nam zakresy adresów](https://www.microsoft.com/download/confirmation.aspx?id=41653). Jest to wymagane, dzięki czemu dane mogą być zapisywane na koncie magazynu pamięci podręcznej z maszyny Wirtualnej.
2. Tworzenie reguł dla wszystkich zakresów adresów IP, które odpowiadają usługi Office 365 [uwierzytelnianie i tożsamość punkty końcowe IPv4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Tworzenie reguł, które odnoszą się do lokalizacji docelowej:

   **Lokalizacja** | **Adres IP odzyskiwania lokacji** |  **Adres IP monitorowania odzyskiwania lokacji**
    --- | --- | ---
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reguły NSG - środkowe stany USA 

Te zasady są niezbędne, dzięki czemu można włączyć replikację z region docelowy do źródła region po pracy w trybie failover:

* Zasady, które odpowiadają [zakresy IP USA centralnej](https://www.microsoft.com/download/confirmation.aspx?id=41653). Te są wymagane do konta magazynu pamięci podręcznej można zapisywać danych z maszyny Wirtualnej.

* Zasady dla wszystkich zakresów IP, które odpowiadają usługi Office 365 [uwierzytelnianie i tożsamość punkty końcowe IPv4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

* Reguły, które odnoszą się do lokalizacji źródłowej:
    - Wschodnie stany USA
    - Adres IP odzyskiwania lokacji: 13.82.88.226
    - Lokacja odzyskiwania monitorowania adres IP: 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Jeśli masz połączenie ExpressRoute lub sieci VPN między lokalnymi i lokalizacja platformy Azure, postępuj zgodnie z wytycznymi w tej sekcji.

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Zazwyczaj należy zdefiniować trasy domyślnej (0.0.0.0/0), która wymusza wychodzący ruch internetowy przepływ lokalizacji lokalnej. Firma Microsoft nie jest to zalecane. Ruch związany z replikacją i komunikacji usługi Site Recovery nie opuszczaj Azure granic. Rozwiązanie to dodać trasy zdefiniowane przez użytkownika (Udr) dla [tych zakresów IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) tak, aby ruch związany z replikacją nie działa lokalnie.

### <a name="connectivity"></a>Łączność 

Wykonaj te wytyczne dotyczące połączeń między lokalizacji docelowej i lokalizacji lokalnego:
- Jeśli aplikacja wymaga połączyć się z komputerami lokalnymi lub w przypadku klientów, którzy łączą się z lokalnymi za pośrednictwem sieci VPN/ExpressRoute, upewnij się, że masz co najmniej [połączenie lokacja lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) między urządzenie docelowe region platformy Azure i lokalnego centrum danych.

- Jeśli spodziewasz się dużej ilości na ruch między urządzenie docelowe region platformy Azure i lokalnego centrum danych, należy utworzyć inny [połączenia ExpressRoute](../expressroute/expressroute-introduction.md) między docelowy region platformy Azure i lokalnego centrum danych.

- Jeśli chcesz zachować adresów IP dla maszyn wirtualnych, po ich w tryb failover, Zachowaj połączenia lokacja do witryny/ExpressRoute region docelowy w stanie rozłączenia. To jest upewnienie się, nie zakresu konflikt między region źródła zakresów adresów IP i zakresów adresów IP region docelowy nie istnieje.

### <a name="expressroute-configuration"></a>Konfiguracji usługi ExpressRoute
Wykonaj następujące najlepsze rozwiązania dla konfiguracji usługi ExpressRoute:

- Utworzyć obwodu usługi ExpressRoute w regionach źródłowych i docelowych. Następnie musisz utworzyć połączenie między usługą:
    - Sieć wirtualna źródła i z sieci lokalnej, za pośrednictwem obwodu usługi expressroute w regionie źródła.
    - Docelowy sieci wirtualnej i sieci lokalnej, za pośrednictwem obwodu usługi expressroute w docelowym regionie.


- W ramach standardowego ExpressRoute możesz utworzyć obwody w tym samym regionie geograficznymi. Aby utworzyć obwody usługi ExpressRoute w różnych regionach geograficznymi, Azure ExpressRoute — wersja Premium jest wymagana, która obejmuje przyrostowe kosztów. (Jeśli już używasz usługi ExpressRoute — wersja Premium, jest nie żadnymi dodatkowymi kosztami.) Aby uzyskać więcej informacji, zobacz [dokumentu lokalizacje ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) i [cennik usługi ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- Zalecane jest użycie różnych zakresów IP w regionach źródłowe i docelowe. Obwód usługi expressroute nie można nawiązać połączenia z dwóch sieci wirtualnych platformy Azure z tego samego zakresu adresów IP w tym samym czasie.

- Możesz tworzenie sieci wirtualnych za pomocą tego samego zakresu adresów IP w obu regionach i następnie utwórz obwody usługi ExpressRoute w obu regionach. W przypadku wystąpieniu zdarzenia pracy awaryjnej rozłączyć obwodu źródła sieci wirtualnej, a następnie połącz obwód w docelowej sieci wirtualnej.

 >[!IMPORTANT]
 > Jeśli regionu podstawowego całkowicie jest wyłączony, operację rozłączania może zakończyć się niepowodzeniem. Która uniemożliwi docelowej sieci wirtualnej uzyskiwania połączenia ExpressRoute.

## <a name="next-steps"></a>Kolejne kroki
Włączyć ochronę obciążeń przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
