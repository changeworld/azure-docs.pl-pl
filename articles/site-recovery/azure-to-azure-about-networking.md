---
title: Dotyczących sieci w programie odzyskiwanie po awarii Azure – Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Omówienie sieci na potrzeby replikacji maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sujayt
ms.openlocfilehash: a6c9c690efe8b75cd1a939de1c68cf4e5bd40d70
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149318"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>O technologiach sieciowych w replikacji Azure – Azure



Ten artykuł zawiera wskazówki dotyczące sieci, replikację i odzyskiwanie maszyn wirtualnych platformy Azure z jednego regionu do innego, za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Usługa Site Recovery zapewnia odzyskiwanie po awarii dla [w tym scenariuszu](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typowej infrastruktury sieciowej

Poniższy diagram przedstawia typowy środowiska platformy Azure dla aplikacji uruchamianych na maszynach wirtualnych platformy Azure:

![środowisku klienta](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Jeśli używasz usługi Azure ExpressRoute lub połączenie sieci VPN z sieci lokalnej na platformę Azure, w środowisku jest w następujący sposób:

![środowisku klienta](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Zazwyczaj sieci są chronione przy użyciu zapór i sieciowymi grupami zabezpieczeń (NSG). Zapory Użyj adresu URL lub umieszczania na białej liście na podstawie adresu IP do kontrolowania łączności sieciowej. Grupa NSG dostarcza reguły, które korzystają z zakresów adresów IP do kontrolowania łączności sieciowej.

>[!IMPORTANT]
> Korzystanie z uwierzytelnionego serwera proxy do sterowania łącznością sieciową nie jest obsługiwane przez usługę Site Recovery i nie można włączyć replikacji.


## <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresach URL do sterowania ruchem wychodzącym, Zezwalaj na te adresy URL odzyskiwania lokacji:


**Adres URL** | **Szczegóły**  
--- | ---
*.blob.core.windows.net | Wymagane, dzięki czemu dane mogą być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z maszyny Wirtualnej. Jeśli znasz pamięci podręcznej kont magazynu dla maszyn wirtualnych, możesz umieścić na liście dozwolonych adresy URL konta określonego magazynu (np: cache1.blob.core.windows.net i cache2.blob.core.windows.net) zamiast *. blob.core.windows.net
login.microsoftonline.com | Wymagane dla autoryzacji i uwierzytelniania do adresów URL usługi Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja usługi Site Recovery może wystąpić z maszyny Wirtualnej. Odpowiedni "Site Recovery adres IP" można użyć, jeśli Twój serwer proxy zapory obsługuje adresy IP.
*.servicebus.windows.net | Wymagane, aby Usługa Site Recovery danych monitorowania i diagnostyki mogą być zapisywane z maszyny Wirtualnej. Odpowiedni "Site Recovery monitorowania adres IP" można użyć, jeśli Twój serwer proxy zapory obsługuje adresy IP.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz serwera proxy zapory oparte na adresie IP lub reguły sieciowej grupy zabezpieczeń do sterowania ruchem wychodzącym te zakresy adresów IP muszą być dozwolone.

- Wszystkie zakresy adresów IP, które odpowiadają na kontach magazynu w regionie źródłowym
    - Tworzenie [tag usługi Storage](../virtual-network/security-overview.md#service-tags) na podstawie reguły sieciowej grupy zabezpieczeń dla regionu źródłowego.
    - Zezwalaj na te adresy, dzięki czemu dane mogą być zapisywane na koncie magazynu pamięci podręcznej z maszyny Wirtualnej.
- Tworzenie [tag usługi Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na podstawie reguły sieciowej grupy zabezpieczeń do zezwalania na dostęp do wszystkich adresów IP odpowiadającej usługi AAD
    - Nowe adresy zostaną dodane do usługi Azure Active Directory (AAD) w przyszłości, należy utworzyć nowe reguły sieciowej grupy zabezpieczeń.
- Site Recovery adresy punktów końcowych usługi IP - dostępne w [pliku XML](https://aka.ms/site-recovery-public-ips) i zależą od lokalizacji docelowej.
- Firma Microsoft zaleca tworzenie wymaganych reguł sieciowej grupy zabezpieczeń dla testu sieciowej grupy zabezpieczeń i sprawdź, czy nie ma żadnych problemów przed utworzeniem zasad do produkcji sieciowej grupy zabezpieczeń.


Zakresy adresów IP odzyskiwania lokacji są następujące:

   **Docelowy** | **Site Recovery adresu IP** |  **Usługa Site Recovery monitorowania adresów IP**
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
   Korea Południowa | 52.231.198.185 | 52.231.200.144
   Francja Środkowa | 52.143.138.106 | 52.143.136.55
   Francja Południowa | 52.136.139.227 |52.136.136.62
   Australia centralnej| 20.36.34.70 | 20.36.46.142
   Australia Środkowa 2| 20.36.69.62 | 20.36.74.130
   Republika Południowej Afryki (zachód) | 102.133.72.51 | 102.133.26.128
   Republika Południowej Afryki (północ) | 102.133.160.44 | 102.133.154.128
   Administracja USA — Wirginia | 52.227.178.114 | 23.97.0.197
   US Gov Iowa | 13.72.184.23 | 23.97.16.186
   Administracja USA — Arizona | 52.244.205.45 | 52.244.48.85
   Administracja USA — Teksas | 52.238.119.218 | 52.238.116.60
   US DoD — wschodnie stany | 52.181.164.103 | 52.181.162.129
   US DoD — środkowe stany | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowa grupa zabezpieczeń

W tym przykładzie pokazano, jak skonfigurować reguły sieciowej grupy zabezpieczeń dla maszyny Wirtualnej do replikacji.

- Jeśli używasz reguł sieciowych grup zabezpieczeń do sterowania ruchem wychodzącym, na użytek reguły portu: 443 "Zezwalaj wychodzących HTTPS" wszystkich wymaganych zakresów adresów IP.
- Przykład zakłada, że lokalizacja źródłowa maszyna wirtualna jest "Wschodnie stany USA", a docelowa lokalizacja to "Środkowe stany USA".

### <a name="nsg-rules---east-us"></a>Reguły sieciowej grupy zabezpieczeń — wschodnie stany USA

1. Utwórz regułę zabezpieczeń dla ruchu wychodzącego 443 protokołu HTTPS dla "Storage.EastUS" w sieciowej grupie zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![tag magazynu](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz regułę zabezpieczeń dla ruchu wychodzącego HTTPS (443) dla usługi "AzureActiveDirectory" w sieciowej grupie zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![tag usługi AAD](./media/azure-to-azure-about-networking/aad-tag.png)

3. Tworzenie reguły ruchu wychodzącego HTTPS (443) dla adresów IP odzyskiwania lokacji, które odnoszą się do lokalizacji docelowej:

   **Lokalizacja** | **Adres IP odzyskiwania lokacji** |  **Adres IP monitorowania odzyskiwania lokacji**
    --- | --- | ---
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reguły sieciowej grupy zabezpieczeń — środkowe stany USA

Te zasady są niezbędne, tak że można włączyć replikację z regionu docelowego do źródłowego regionu po przełączeniu w tryb failover:

1. Utwórz regułę zabezpieczeń dla ruchu wychodzącego 443 protokołu HTTPS dla "Storage.CentralUS" w sieciowej grupie zabezpieczeń.

2. Utwórz regułę zabezpieczeń dla ruchu wychodzącego HTTPS (443) dla usługi "AzureActiveDirectory" w sieciowej grupie zabezpieczeń.

3. Tworzenie reguły ruchu wychodzącego HTTPS (443) dla adresów IP odzyskiwania lokacji, które odnoszą się do lokalizacji źródłowej:

   **Lokalizacja** | **Adres IP odzyskiwania lokacji** |  **Adres IP monitorowania odzyskiwania lokacji**
    --- | --- | ---
   Środkowe stany USA | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Konfiguracja urządzenia wirtualnego sieci

Korzystania z wirtualnych urządzeń sieciowych (urządzeń WUS) do kontrolowania wychodzącego ruchu sieciowego z maszynami wirtualnymi urządzenie może być ograniczona w przypadku wszystkich ruch związany z replikacją przechodzi przez urządzenia WUS. Zaleca się utworzenie punktu końcowego usługi sieci w sieci wirtualnej na "Magazyn", aby ruch związany z replikacją nie przechodzi do urządzenia WUS.

### <a name="create-network-service-endpoint-for-storage"></a>Tworzenie punktu końcowego usługi sieci magazynu
Punkt końcowy usługi sieci w sieci wirtualnej można utworzyć w "Magazyn", aby ruch związany z replikacją nie opuszczają platformy Azure.

- Wybierz sieci wirtualnej platformy Azure i kliknij pozycję "Punkty końcowe usługi"

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kliknij pozycję "Dodaj" i "Dodaj punkty końcowe usługi" zostanie otwarta karta
- Wybierz pozycję "Microsoft.Storage", w obszarze "Usługa" i wymagane podsieci w ramach pola "Podsieci", a następnie kliknij przycisk "Dodaj"

>[!NOTE]
>Nie ograniczaj dostępu do sieci wirtualnej do kont magazynu używane do automatycznego odzyskiwania systemu. Można zezwalać na dostęp ze "Wszystkich sieci"

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Można zastąpić domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 za pomocą [trasy niestandardowej](../virtual-network/virtual-networks-udr-overview.md#custom-routes) przekierowywać ruch maszyny Wirtualnej do środowiska lokalnego wirtualnego urządzenia sieciowego (WUS), ale ta konfiguracja nie jest zalecana dla usługi Site Recovery Replikacja. Jeśli używasz niestandardowych tras, wykonaj następujące czynności [Tworzenie punktu końcowego usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w wirtualnej sieci dla "Magazyn", aby ruch związany z replikacją nie opuszczają platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
- Zacząć chronić swoje obciążenia przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
- Dowiedz się więcej o [przechowywania adresów IP](site-recovery-retain-ip-azure-vm-failover.md) dla trybu failover maszyny wirtualnej platformy Azure.
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
