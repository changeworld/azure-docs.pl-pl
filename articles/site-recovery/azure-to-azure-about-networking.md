---
title: O technologiach sieciowych w odzyskiwania po awarii z platformy Azure do platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Omówienie sieci w przypadku replikacji maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/17/2018
ms.author: sujayt
ms.openlocfilehash: e3acedf4135166f5239b95eb21eb5dfd66d6100f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
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

- Wszystkie zakresy adresów IP, odpowiadające kont magazynu w regionie źródła
    - Utwórz [numer seryjny magazynu](../virtual-network/security-overview.md#service-tags) na podstawie reguły NSG dla regionu źródła.
    - Zezwalaj na te adresy tak, aby dane mogą być zapisywane na koncie magazynu pamięci podręcznej z maszyny Wirtualnej.
- Wszystkie zakresy adresów IP, które odpowiadają usługi Office 365 [uwierzytelnianie i tożsamość punkty końcowe IPv4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
    - Nowe adresy zostaną dodane do usługi Office 365 zakresów w przyszłości, należy utworzyć nowe reguły NSG.
- Lokacja odzyskiwania usługi punktu końcowego adresy IP — dostępne w [pliku XML](https://aka.ms/site-recovery-public-ips) i zależą od lokalizacji docelowej.
-  Możesz [pobranie i użycie tego skryptu](https://aka.ms/nsg-rule-script), aby automatycznie utworzyć wymagane zasady w grupie NSG.
- Zaleca się tworzenie wymaganych reguł NSG na test NSG i sprawdź, czy nie ma żadnych problemów przed utworzeniem reguły produkcyjnych NSG.


Zakresy adresów IP odzyskiwania lokacji są następujące:

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
   Francja Środkowa | 52.143.138.106 | 52.143.136.55
   Francja Południowa | 52.136.139.227 |52.136.136.62


## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja grupy NSG

Ten przykład przedstawia sposób konfigurowania reguły NSG dla maszyny Wirtualnej do replikacji.

- Jeśli używasz reguły NSG do kontrolowania łączność wychodząca, za pomocą reguł "Zezwalaj HTTPS wychodzącego" numer portu: 443 dla wszystkich wymaganych zakresy adresów IP.
- Przykładzie przyjęto założenie, że lokalizacja źródłowa maszyna wirtualna jest "Wschodnie nam" i lokalizacja docelowa jest "Centralnej US".

### <a name="nsg-rules---east-us"></a>Reguły NSG — wschodnie stany USA

1. W "Storage.EastUS" w grupie NSG, jak pokazano na poniższym zrzucie ekranu, należy utworzyć regułę ruchu wychodzącego zabezpieczeń HTTPS (port 443).

      ![tag magazynu](./media/azure-to-azure-about-networking/storage-tag.png)

2. Tworzenie reguły ruchu wychodzącego HTTPS (port 443) dla wszystkich zakresów adresów IP, które odpowiadają usługi Office 365 [uwierzytelnianie i tożsamość punkty końcowe IPv4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. Tworzenie reguły ruchu wychodzącego HTTPS (port 443) dla adresów IP odzyskiwania lokacji, który odpowiada lokalizacji docelowej:

   **Lokalizacja** | **Adres IP odzyskiwania lokacji** |  **Adres IP monitorowania odzyskiwania lokacji**
    --- | --- | ---
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reguły NSG - środkowe stany USA

Te zasady są niezbędne, dzięki czemu można włączyć replikację z region docelowy do źródła region po pracy w trybie failover:

1. W "Storage.CentralUS" w grupie NSG, należy utworzyć regułę ruchu wychodzącego zabezpieczeń HTTPS (port 443).

2. Tworzenie reguły ruchu wychodzącego HTTPS (port 443) dla wszystkich zakresów adresów IP, które odpowiadają usługi Office 365 [uwierzytelnianie i tożsamość punkty końcowe IPv4](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

3. Tworzenie reguły ruchu wychodzącego HTTPS (port 443) dla adresów IP odzyskiwania lokacji, który odpowiada lokalizacji źródłowej:

   **Lokalizacja** | **Adres IP odzyskiwania lokacji** |  **Adres IP monitorowania odzyskiwania lokacji**
    --- | --- | ---
   Środkowe stany USA | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Konfiguracja sieci urządzenie wirtualne

Jeśli korzystasz z wirtualnych urządzeń sieciowych (NVAs) do kontroli wychodzącego ruchu sieciowego z maszyn wirtualnych, jeśli cały ruch replikacji przechodzi przez analizę NVA może pobrać ograniczony urządzenia. Zaleca się utworzenie punkt końcowy usługi sieci w sieci wirtualnej "Magazynu", tak aby ruch związany z replikacją nie przejść do analizę NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Utwórz punkt końcowy usługi sieci magazynu
Punkt końcowy usługi sieci w sieci wirtualnej można utworzyć w "Magazyn", aby ruch związany z replikacją nie opuszczają granicy Azure.

- Wybierz sieci wirtualnej platformy Azure i kliknij polecenie "Punktów końcowych usługi"

    ![punkt końcowy magazynu](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kliknij przycisk "Dodaj" i "Dodawanie punktów końcowych usługi" karcie otwiera
- Wybierz "Microsoft.Storage" w obszarze "Usługa" i podsieci wymagane dla pola "Podsieci", a następnie kliknij przycisk "Dodaj"

>[!NOTE]
>Nie ograniczaj dostępu do sieci wirtualnej do kont magazynu używane do automatycznego odzyskiwania systemu. Użytkownik powinien zezwalać na dostęp z "Wszystkie sieci"

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Można zastąpić Azure domyślną systemu trasę dla prefiksu adresu 0.0.0.0/0 z [tras niestandardowych](../virtual-network/virtual-networks-udr-overview.md#custom-routes) odwrócenia ruchu maszyny Wirtualnej do urządzenia wirtualnych sieci lokalnych (NVA), ale ta konfiguracja nie jest zalecana dla usługi Site Recovery Replikacja. Jeśli używasz niestandardowych tras [Tworzenie punktu końcowego usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w wirtualnej sieci "Magazynu", aby ruch związany z replikacją nie opuszczają granicy Azure.

## <a name="next-steps"></a>Kolejne kroki
- Włączyć ochronę obciążeń przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
- Dowiedz się więcej o [przechowywania adresów IP](site-recovery-retain-ip-azure-vm-failover.md) dla trybu failover maszyny wirtualnej platformy Azure.
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure z ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
