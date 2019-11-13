---
title: Informacje o sieci w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure z Azure Site Recovery
description: Zawiera omówienie sieci na potrzeby replikacji maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/22/2019
ms.author: sutalasi
ms.openlocfilehash: 09cd814ade25be438a17b83fb73e74b89c14e22f
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954210"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Informacje o sieci w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure



Ten artykuł zawiera wskazówki dotyczące sieci podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Site Recovery zapewnia odzyskiwanie po awarii w [tym scenariuszu](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Typowa infrastruktura sieci

Na poniższym diagramie przedstawiono typowe środowisko platformy Azure dla aplikacji uruchamianych na maszynach wirtualnych platformy Azure:

![Klient-środowisko](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Jeśli używasz usługi Azure ExpressRoute lub połączenia sieci VPN z sieci lokalnej z platformą Azure, środowisko jest następujące:

![Klient-środowisko](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Zwykle sieci są chronione za pomocą zapór i sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Zapory korzystają z adresów URL lub listy dozwolonych opartych na protokole IP w celu kontrolowania łączności sieciowej. Sieciowych grup zabezpieczeń zapewniają reguły korzystające z zakresów adresów IP w celu kontrolowania łączności sieciowej.

>[!IMPORTANT]
> Używanie uwierzytelnionego serwera proxy do sterowania łącznością sieciową nie jest obsługiwane przez Site Recovery i nie można włączyć replikacji.


## <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na następujące adresy URL Site Recovery:


**Adres URL** | **Szczegóły**  
--- | ---
*.blob.core.windows.net | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z poziomu maszyny wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz zezwolić na dostęp do adresów URL określonych kont magazynu (np. cache1.blob.core.windows.net i cache2.blob.core.windows.net) zamiast *. blob.core.windows.net
login.microsoftonline.com | Wymagany do autoryzacji i uwierzytelniania do adresów URL usługi Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja z usługą Site Recovery mogła się odbywać z poziomu maszyny wirtualnej. W przypadku, gdy serwer proxy zapory obsługuje adresy IP, można użyć odpowiedniego "Site Recovery".
*.servicebus.windows.net | Wymagane, aby dane dotyczące monitorowania i diagnostyki Site Recovery mogły być zapisywane z poziomu maszyny wirtualnej. W przypadku, gdy serwer proxy zapory obsługuje adresy IP, można użyć odpowiadającego "Site Recovery monitorowanie".

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz serwera proxy zapory opartego na protokole IP lub reguł sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, te zakresy adresów IP muszą być dozwolone.

- Wszystkie zakresy adresów IP, które odpowiadają kontom magazynu w regionie źródłowym
    - Utwórz opartą na [znaczniku usługi magazynu](../virtual-network/security-overview.md#service-tags) regułę sieciowej grupy zabezpieczeń dla regionu źródłowego.
    - Zezwalaj na te adresy, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej z poziomu maszyny wirtualnej.
- Tworzenie [tag usługi Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na podstawie reguły sieciowej grupy zabezpieczeń do zezwalania na dostęp do wszystkich adresów IP odpowiadającej usługi AAD
    - Nowe adresy zostaną dodane do usługi Azure Active Directory (AAD) w przyszłości, należy utworzyć nowe reguły sieciowej grupy zabezpieczeń.
- Site Recovery adresy IP punktów końcowych usługi — dostępne w [pliku XML](https://aka.ms/site-recovery-public-ips) i zależą od lokalizacji docelowej. 
- Zalecamy utworzenie wymaganych reguł sieciowej grupy zabezpieczeń na testowym sieciowej grupy zabezpieczeń i sprawdzenie, czy nie ma żadnych problemów przed utworzeniem reguł na sieciowej grupy zabezpieczeń produkcyjnej.


Zakresy adresów IP Site Recovery są następujące:

   **Obiekt docelowy** | **Adres IP Site Recovery** |  **Adres IP monitorowania Site Recovery**
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
   Australia Środkowa| 20.36.34.70 | 20.36.46.142
   Australia Środkowa 2| 20.36.69.62 | 20.36.74.130
   Północna Republika Południowej Afryki | 102.133.72.51 | 102.133.26.128
   Północna Republika Południowej Afryki | 102.133.160.44 | 102.133.154.128
   Administracja USA — Wirginia | 52.227.178.114 | 23.97.0.197
   US Gov Iowa | 13.72.184.23 | 23.97.16.186
   Administracja USA — Arizona | 52.244.205.45 | 52.244.48.85
   Administracja USA — Teksas | 52.238.119.218 | 52.238.116.60
   US DoD — wschodnie stany | 52.181.164.103 | 52.181.162.129
   US DoD — środkowe stany | 52.182.95.237 | 52.182.90.133
   Chiny Północne | 40.125.202.254 | 42.159.4.151
   Chiny Północne 2 | 40.73.35.193 | 40.73.33.230
   Chiny Wschodnie | 42.159.205.45 | 42.159.132.40
   Chiny Wschodnie 2 | 40.73.118.52| 40.73.100.125
   Niemcy Północne| 51.116.208.58| 51.116.58.128
   Niemcy Środkowo-Zachodnie | 51.116.156.176 | 51.116.154.192
   Szwajcaria Zachodnia | 51.107.231.223| 51.107.154.128
   Szwajcaria Północna | 51.107.68.31| 51.107.58.128
   Norwegia Wschodnia | 51.120.100.64| 51.120.98.128
   Norwegia Zachodnia | 51.120.220.65| 51.120.218.160

## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

Ten przykład pokazuje, jak skonfigurować reguły sieciowej grupy zabezpieczeń dla maszyny wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, Użyj reguł "Zezwalaj na ruch wychodzący protokołu HTTPS" na port: 443 dla wszystkich wymaganych zakresów adresów IP.
- Przykład zakłada, że lokalizacją źródłową maszyny wirtualnej jest "Wschodnie stany USA", a lokalizacja docelowa to "środkowe stany USA".

### <a name="nsg-rules---east-us"></a>Reguły sieciowej grupy zabezpieczeń — Wschodnie stany USA

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. Wschód" na sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![Magazyn — tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![AAD — tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Utwórz reguły wychodzącego protokołu HTTPS (443) dla adresów IP Site Recovery, które odpowiadają lokalizacji docelowej:

   **Lokalizacja** | **Site Recovery adres IP** |  **Site Recovery monitorowania adresu IP**
    --- | --- | ---
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reguły sieciowej grupy zabezpieczeń — środkowe stany USA

Te reguły są wymagane, aby można było włączyć replikację z regionu docelowego do obszaru źródłowego po zakończeniu pracy w trybie failover:

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. środkowe" w sieciowej grupy zabezpieczeń.

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń.

3. Utwórz reguły wychodzącego protokołu HTTPS (443) dla Site Recovery adresów IP, które odpowiadają lokalizacji źródłowej:

   **Lokalizacja** | **Site Recovery adres IP** |  **Site Recovery monitorowania adresu IP**
    --- | --- | ---
   Wschodnie stany USA | 13.82.88.226 | 104.45.147.24


## <a name="network-virtual-appliance-configuration"></a>Konfiguracja wirtualnego urządzenia sieciowego

Jeśli używasz wirtualnych urządzeń sieciowych (urządzeń WUS) do kontrolowania wychodzącego ruchu sieciowego z maszyn wirtualnych, urządzenie może zostać ograniczone, jeśli cały ruch związany z replikacją przechodzi przez urządzenie WUS. Zalecamy utworzenie punktu końcowego usługi sieciowej w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie przechodził do urządzenie WUS.

### <a name="create-network-service-endpoint-for-storage"></a>Utwórz punkt końcowy usługi sieciowej dla magazynu
W sieci wirtualnej można utworzyć punkt końcowy usługi sieciowej dla magazynu, aby ruch związany z replikacją nie opuszcza granicy platformy Azure.

- Wybierz sieć wirtualną platformy Azure i kliknij pozycję "punkty końcowe usługi"

    ![Magazyn — punkt końcowy](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kliknij przycisk "Dodaj" i Otwórz kartę "Dodaj punkty końcowe usługi"
- Wybierz pozycję "Microsoft. Storage" w obszarze "Service" i wymagane podsieci w polu "podsieci" i kliknij pozycję "Dodaj".

>[!NOTE]
>Nie ograniczaj dostępu do sieci wirtualnej do kont magazynu używanych na potrzeby usługi ASR. Należy zezwolić na dostęp ze wszystkich sieci

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Można zastąpić domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 z [trasą niestandardową](../virtual-network/virtual-networks-udr-overview.md#custom-routes) i przekierować ruch maszyny wirtualnej do lokalnego urządzenia sieciowego (urządzenie WUS), ale ta konfiguracja nie jest zalecana w przypadku replikacji Site Recovery. Jeśli używasz tras niestandardowych, należy [utworzyć punkt końcowy usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla elementu "Storage", aby ruch związany z replikacją nie opuszcza granicy platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Rozpocznij ochronę obciążeń przez [replikowanie maszyn wirtualnych platformy Azure](site-recovery-azure-to-azure.md).
- Dowiedz się więcej o [przechowywaniu adresów IP](site-recovery-retain-ip-azure-vm-failover.md) w trybie failover dla maszyny wirtualnej platformy Azure.
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure przy użyciu usługi ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md).
