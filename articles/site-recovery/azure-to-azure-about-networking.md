---
title: Odzyskiwanie po awarii maszyny Wirtualnej platformy Azure o sieci w usłudze Azure w usłudze Azure Site Recovery
description: Zawiera omówienie sieci replikacji maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: sutalasi
ms.openlocfilehash: 58348c9aed14a5cc9126be780fe01817274a0b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283263"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Informacje o sieci w odzyskiwaniu po awarii maszyny Wirtualnej platformy Azure



Ten artykuł zawiera wskazówki dotyczące sieci podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do drugiego przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak odzyskiwanie witryny zapewnia odzyskiwanie po awarii w [tym scenariuszu.](azure-to-azure-architecture.md)

## <a name="typical-network-infrastructure"></a>Typowa infrastruktura sieciowa

Poniższy diagram przedstawia typowe środowisko platformy Azure dla aplikacji uruchomionych na maszynach wirtualnych platformy Azure:

![środowisko klienta](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Jeśli używasz usługi Azure ExpressRoute lub połączenia sieci VPN z sieci lokalnej do platformy Azure, środowisko jest następujące:

![środowisko klienta](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Zazwyczaj sieci są chronione za pomocą zapór i sieciowych grup zabezpieczeń (NSG). Zapory sieciowe używają białej listy adresów URL lub ip do kontrolowania łączności sieciowej. Sieci zabezpieczeń zapewniają reguły, które używają zakresów adresów IP do kontrolowania łączności sieciowej.

>[!IMPORTANT]
> Korzystanie z uwierzytelnionego serwera proxy do kontrolowania łączności sieciowej nie jest obsługiwane przez usługę Site Recovery i nie można włączyć replikacji.


## <a name="outbound-connectivity-for-urls"></a>Połączenia ruchu wychodzącego dla adresów URL

Jeśli używasz serwera proxy zapory opartej na adresie URL do kontrolowania łączności wychodzącej, zezwól na następujące adresy URL odzyskiwania witryny:


**Adres URL** | **Szczegóły**
--- | ---
*.blob.core.windows.net | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z maszyny Wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz zezwolić na dostęp do określonych adresów URL kont magazynu (Np. cache1.blob.core.windows.net i cache2.blob.core.windows.net) zamiast *.blob.core.windows.net
login.microsoftonline.com | Wymagane do autoryzacji i uwierzytelniania adresów URL usługi site recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja usługi odzyskiwania witryny mogła wystąpić z maszyny Wirtualnej.
*.servicebus.windows.net | Wymagane, aby dane monitorowania i diagnostyki odzyskiwania lokacji mogły być zapisywane z maszyny Wirtualnej.
*.vault.azure.net | Umożliwia dostęp do włączania replikacji dla maszyn wirtualnych z obsługą ADE za pośrednictwem portalu
*.automation.ext.azure.com | Umożliwia automatyczne uaktualnianie agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu

## <a name="outbound-connectivity-using-service-tags"></a>Łączność wychodząca przy użyciu znaczników usługi

Jeśli używasz sieciowej sieciowej sieciowej do kontrolowania łączności wychodzącej, te tagi usługi muszą być dozwolone.

- W przypadku kont magazynu w regionie źródłowym:
    - Utwórz regułę nsg na podstawie [tagu usługi magazynu](../virtual-network/security-overview.md#service-tags) dla regionu źródłowego.
    - Zezwalaj na te adresy, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej z maszyny Wirtualnej.
- Tworzenie [reguły nsg usługi usługi Azure Active Directory (AAD)](../virtual-network/security-overview.md#service-tags) umożliwiającej dostęp do wszystkich adresów IP odpowiadających AAD
- Utwórz regułę nsg opartą na tagu usługi EventsHub dla regionu docelowego, umożliwiając dostęp do monitorowania odzyskiwania witryny.
- Utwórz regułę nsg opartą na usłudze AzureSiteRecovery umożliwiającą dostęp do usługi Site Recovery w dowolnym regionie.
- Utwórz regułę nsg opartą na usłudze AzureKeyVault. Jest to wymagane tylko do włączania replikacji maszyn wirtualnych z obsługą usługi ADE za pośrednictwem portalu.
- Utwórz regułę nsg usługi GuestAndHybridManagement. Jest to wymagane tylko do włączania automatycznego uaktualniania agenta mobilności dla zreplikowanego elementu za pośrednictwem portalu.
- Zaleca się utworzenie wymaganych reguł sieciowej zasady sieciowej sieciowej w testowej sieciowej sieciowej sieciowej i sprawdzenie, czy nie występują żadne problemy przed utworzeniem reguł sieciowej sieciowej sieciowej produkcji.

## <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

W tym przykładzie pokazano, jak skonfigurować reguły sieciowej grupy danych sieciowych dla maszyny Wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej sieciowej do kontrolowania łączności wychodzącej, użyj reguł "Zezwalaj na wychodzące https" do portu:443 dla wszystkich wymaganych zakresów adresów IP.
- W przykładzie zakłada się, że lokalizacja źródło maszyny Wirtualnej jest "Wschodnie stany USA", a lokalizacja docelowa to "Central US".

### <a name="nsg-rules---east-us"></a>Zasady sieciowej sieciowej sieciowej - Wschodnie stany USA

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "Storage.EastUS" w nsg, jak pokazano na poniższym zrzucie ekranu.

      ![znacznik magazynu](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "AzureActiveDirectory" w pliku zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Podobnie jak w przypadku powyższych reguł zabezpieczeń, utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "EventHub.CentralUS" w sieciowej sieciowej sieciowej, która odpowiada lokalizacji docelowej. Umożliwia to dostęp do monitorowania odzyskiwania witryny.

4. Utwórz regułę zabezpieczeń https wychodzących (443) dla "AzureSiteRecovery" na nsg. Umożliwia to dostęp do usługi odzyskiwania witryny w dowolnym regionie.

### <a name="nsg-rules---central-us"></a>Zasady sieciowej sieciowej sieciowej - Central USA

Te reguły są wymagane, aby replikacja mogła być włączona z regionu docelowego do regionu źródłowego po przełączeniu w błąd:

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "Storage.CentralUS" w nsg.

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "AzureActiveDirectory" w nsg.

3. Podobnie jak w przypadku powyższych reguł zabezpieczeń, utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "EventHub.EastUS" w sieciowej sieciowej sieciowej, która odpowiada lokalizacji źródłowej. Umożliwia to dostęp do monitorowania odzyskiwania witryny.

4. Utwórz regułę zabezpieczeń https wychodzących (443) dla "AzureSiteRecovery" na nsg. Umożliwia to dostęp do usługi odzyskiwania witryny w dowolnym regionie.

## <a name="network-virtual-appliance-configuration"></a>Konfiguracja wirtualnego urządzenia sieciowego

Jeśli używasz sieciowych urządzeń wirtualnych (NVA) do kontrolowania wychodzącego ruchu sieciowego z maszyn wirtualnych, urządzenie może zostać ograniczone, jeśli cały ruch replikacji przechodzi przez urządzenie WUS. Zaleca się utworzenie punktu końcowego usługi sieciowej w sieci wirtualnej dla "Magazyn", aby ruch replikacji nie został przesunie się do urządzenia WUS.

### <a name="create-network-service-endpoint-for-storage"></a>Tworzenie punktu końcowego usługi sieciowej magazynu
Można utworzyć punkt końcowy usługi sieciowej w sieci wirtualnej dla "Magazyn", tak aby ruch replikacji nie opuszcza granicy platformy Azure.

- Wybierz swoją sieć wirtualną platformy Azure i kliknij "Punkty końcowe usługi"

    ![punkt końcowy magazynu](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kliknij kartę "Dodaj" i "Dodaj punkty końcowe usługi"
- Wybierz "Microsoft.Storage" w obszarze "Usługa" i wymagane podsieci w polu "Podsieci" i kliknij "Dodaj"

>[!NOTE]
>Nie należy ograniczać dostępu do sieci wirtualnej do kont magazynu używanych dla środowiska ASR. Należy zezwolić na dostęp z "Wszystkie sieci"

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 można zastąpić trasą [niestandardową](../virtual-network/virtual-networks-udr-overview.md#custom-routes) i przekierować ruch maszyn wirtualnych do lokalnego urządzenia wirtualnego (NVA), ale ta konfiguracja nie jest zalecana w przypadku replikacji usługi Site Recovery. Jeśli używasz tras niestandardowych, należy [utworzyć punkt końcowy usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla "Magazyn", tak aby ruch replikacji nie opuszcza granicy platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Rozpocznij ochronę obciążeń, [replikując maszyny wirtualne platformy Azure.](site-recovery-azure-to-azure.md)
- Dowiedz się więcej o [retencji adresów IP](site-recovery-retain-ip-azure-vm-failover.md) dla pracy awaryjnej maszyny wirtualnej platformy Azure.
- Dowiedz się więcej o odzyskiwaniu po awarii [maszyn wirtualnych platformy Azure za pomocą usługi ExpressRoute.](azure-vm-disaster-recovery-with-expressroute.md)
