---
title: Informacje o sieci w usłudze odzyskiwania po awarii maszyny wirtualnej platformy Azure z Azure Site Recovery
description: Zawiera omówienie sieci na potrzeby replikacji maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 1/23/2020
ms.author: sutalasi
ms.openlocfilehash: aeab1960b065538635fdd63c43d779287f8cd9ee
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759827"
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
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja z usługą Site Recovery mogła się odbywać z poziomu maszyny wirtualnej.
*.servicebus.windows.net | Wymagane, aby dane dotyczące monitorowania i diagnostyki Site Recovery mogły być zapisywane z poziomu maszyny wirtualnej.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Połączenia ruchu wychodzącego dla zakresów adresów IP

Jeśli używasz sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, te Tagi usług muszą być dozwolone.

- Wszystkie zakresy adresów IP, które odpowiadają kontom magazynu w regionie źródłowym
    - Utwórz opartą na [znaczniku usługi magazynu](../virtual-network/security-overview.md#service-tags) regułę sieciowej grupy zabezpieczeń dla regionu źródłowego.
    - Zezwalaj na te adresy, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej z poziomu maszyny wirtualnej.
- Tworzenie [tag usługi Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) na podstawie reguły sieciowej grupy zabezpieczeń do zezwalania na dostęp do wszystkich adresów IP odpowiadającej usługi AAD
- Utwórz regułę sieciowej grupy zabezpieczeń opartą na tagu usługi EventsHub dla regionu docelowego, umożliwiając dostęp do monitorowania Site Recovery.
- Utwórz regułę sieciowej grupy zabezpieczeń opartą na znacznikach usługi AzureSiteRecovery, aby umożliwić dostęp do usługi Site Recovery w dowolnym regionie.
- Zalecamy utworzenie wymaganych reguł sieciowej grupy zabezpieczeń na testowym sieciowej grupy zabezpieczeń i sprawdzenie, czy nie ma żadnych problemów przed utworzeniem reguł na sieciowej grupy zabezpieczeń produkcyjnej.

## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

Ten przykład pokazuje, jak skonfigurować reguły sieciowej grupy zabezpieczeń dla maszyny wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, Użyj reguł "Zezwalaj na ruch wychodzący protokołu HTTPS" na port: 443 dla wszystkich wymaganych zakresów adresów IP.
- Przykład zakłada, że lokalizacją źródłową maszyny wirtualnej jest "Wschodnie stany USA", a lokalizacja docelowa to "środkowe stany USA".

### <a name="nsg-rules---east-us"></a>Reguły sieciowej grupy zabezpieczeń — Wschodnie stany USA

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. Wschód" na sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![Magazyn — tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![AAD — tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Podobnie jak w przypadku powyższych reguł zabezpieczeń, Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "EventHub. środkowe" w sieciowej grupy zabezpieczeń, który odpowiada lokalizacji docelowej. Pozwala to na dostęp do monitorowania Site Recovery.

4. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "AzureSiteRecovery" w sieciowej grupy zabezpieczeń. Pozwala to na dostęp do usługi Site Recovery w dowolnym regionie.

### <a name="nsg-rules---central-us"></a>Reguły sieciowej grupy zabezpieczeń — środkowe stany USA

Te reguły są wymagane, aby można było włączyć replikację z regionu docelowego do obszaru źródłowego po zakończeniu pracy w trybie failover:

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. środkowe" w sieciowej grupy zabezpieczeń.

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń.

3. Podobnie jak powyżej reguły zabezpieczeń, Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "EventHub. Wschód" w sieciowej grupy zabezpieczeń, który odpowiada lokalizacji źródłowej. Pozwala to na dostęp do monitorowania Site Recovery.

4. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "AzureSiteRecovery" w sieciowej grupy zabezpieczeń. Pozwala to na dostęp do usługi Site Recovery w dowolnym regionie.

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
