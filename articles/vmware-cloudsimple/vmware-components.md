---
title: Azure VMware Solutions (Automatyczna synchronizacja) — automatyczna synchronizacja składników VMware chmury prywatnej
description: Opisuje, jak składniki oprogramowania VMware są instalowane w chmurze prywatnej automatycznej synchronizacji
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea6c22c3957f72a0a416ce7ae42c62ff5a0791a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016786"
---
# <a name="avs-private-cloud-vmware-components"></a>Automatyczna synchronizacja składników VMware chmury prywatnej

Chmura prywatna w wersji zaciągającej jest izolowanym środowiskiem VMware (ESXi hosty, vCenter, sieci vSAN i NSX) zarządzanym przez serwer vCenter w domenie zarządzania. Usługa automatycznej synchronizacji pozwala na natywne wdrożenie programu VMware na platformie Azure bez systemu operacyjnego w lokalizacjach platformy Azure. Automatyczna synchronizacja chmur prywatnych jest zintegrowana z resztą chmury platformy Azure. Chmura prywatna w wersji zaautomatycznej jest wdrażana z następującymi składnikami stosu VMware:

* **VMware ESXi —** Funkcja hypervisor w dedykowanych węzłach platformy Azure
* **VMware vCenter —** Urządzenie do scentralizowanego zarządzania vSphere środowiska prywatnego chmury prywatnej
* **VMware sieci vSAN —** Rozwiązanie infrastruktury z technologią Hyper-zbieżność
* **Centrum danych VMware NSX —** Oprogramowanie do wirtualizacji sieci i zabezpieczeń  

## <a name="vmware-component-versions"></a>Wersje składników VMware

Stos VMware chmury prywatnej w chmurze jest wdrażany z następującą wersją oprogramowania.

| Składnik | Wersja | Licencjonowana wersja |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | Program vCenter Standard |
| Sieci vSAN | 6.7 | Enterprise |
| Centrum danych NSX | 2.4.1 | Advanced |

## <a name="esxi"></a>ESXi

VMware ESXi jest instalowany w węzłach o zainicjowanej automatycznej synchronizacji podczas tworzenia chmury prywatnej automatycznej synchronizacji. ESXi udostępnia funkcję hypervisor do wdrażania maszyn wirtualnych obciążeń. Węzły zapewniają infrastrukturę prywatną z funkcją automatycznej synchronizacji (obliczeniową i magazynową) w chmurze prywatnej. Węzły są częścią klastra vSphere w chmurze prywatnej automatycznej synchronizacji. Każdy węzeł ma cztery interfejsy sieci fizycznych połączone z siecią underlay. Dwa fizyczne interfejsy sieciowe są używane do tworzenia **przełącznika rozproszonego vSphere (VDS)** w programie vCenter i dwa są używane do tworzenia **wirtualnego przełącznika rozproszonego NSX (N-VDS)** . Interfejsy sieciowe są konfigurowane w trybie aktywny-aktywny w celu zapewnienia wysokiej dostępności.

Dowiedz się więcej na VMware ESXi

## <a name="vcenter-server-appliance"></a>Server Appliance vCenter

Program vCenter Server Appliance (VCSA) zapewnia funkcje uwierzytelniania, zarządzania i aranżacji dla rozwiązań VMware (Automatyczna synchronizacja). VCSA z wbudowanym kontrolerem usług platformy (PSC) jest wdrażany podczas tworzenia chmury prywatnej automatycznej synchronizacji. VCSA jest wdrażana w klastrze vSphere tworzonym podczas wdrażania chmury prywatnej automatycznej synchronizacji. Każda chmura prywatna o automatycznej synchronizacji ma własne VCSA. Rozszerzenie chmury prywatnej automatycznej synchronizacji umożliwia dodanie węzłów do VCSA w chmurze prywatnej automatycznej synchronizacji.

### <a name="vcenter-single-sign-on"></a>Logowanie jednokrotne vCenter

Osadzony kontroler usług platformy VCSA jest skojarzony z **domeną logowania**jednokrotnego. Nazwa domeny jest **Automatyczna**. Zostanie utworzony domyślny **CloudOwner@AVS.com** użytkownika umożliwiający dostęp do programu vCenter. Możesz dodać [źródła tożsamości](set-vcenter-identity.md)lokalnego/usługi Azure Active Directory dla serwera vCenter.

## <a name="vsan-storage"></a>Magazyn sieci vSAN

Chmura prywatna w wersji załączonej jest tworzona przy użyciu w pełni skonfigurowanego magazynu sieci vSAN (Local-Flash) w klastrze. Aby można było utworzyć klaster vSphere z magazynem danych sieci vSAN, wymagane są co najmniej trzy węzły tej samej jednostki SKU. Deduplikacja i kompresja są domyślnie włączone w magazynie danych sieci vSAN. Na każdym węźle klastra vSphere są tworzone dwie grupy dysków. Każda grupa dysków zawiera jeden dysk pamięci podręcznej i trzy dyski pojemności.

Domyślne zasady magazynu sieci vSAN są tworzone w klastrze vSphere i stosowane do magazynu danych sieci vSAN. Te zasady określają, w jaki sposób obiekty magazynu maszyn wirtualnych są obsługiwane i alokowane w magazynie danych w celu zagwarantowania wymaganego poziomu usługi. Zasady magazynu definiują **niepowodzenia (FTT)** i **metodę odporności na awarie**. Można utworzyć nowe zasady magazynu i zastosować je do maszyn wirtualnych. Aby zachować umowę SLA, należy utrzymywać 25% pojemności zapasowej w magazynie danych sieci vSAN. 

### <a name="default-vsan-storage-policy"></a>Domyślne zasady magazynu sieci vSAN

W poniższej tabeli przedstawiono domyślne parametry zasad magazynu sieci vSAN.

| Liczba węzłów w klastrze vSphere | FTT | Metoda odporności na awarie |
|------------------------------------|-----|--------------------------|
| 3 i 4 węzły | 1 | RAID 1 (dublowanie) — tworzy 2 kopie |
| od 5 do 16 węzłów | 2 | RAID 1 (dublowanie) — tworzy 3 kopie |

## <a name="nsx-data-center"></a>Centrum danych NSX

NSX Data Center zapewnia wirtualizację sieci, mikrosegmenty i możliwości zabezpieczeń sieci w chmurze prywatnej automatycznej synchronizacji. Wszystkie usługi obsługiwane przez NSX Data Center można skonfigurować w chmurze prywatnej automatycznej synchronizacji za pomocą usługi NSX. Podczas tworzenia chmury prywatnej do automatycznej synchronizacji są instalowane i konfigurowane następujące składniki NSX.

* NSXT Manager
* Strefy transportu
* Profil pasma hosta i granicy
* Przełącznik logiczny dla transportu brzegowego, EXT1 i EXT2
* Pula adresów IP dla węzła transportu ESXi
* Pula adresów IP dla węzła transportu brzegowego
* Węzły brzegowe
* DRS regułę antykoligacji dla kontrolerów i maszyn wirtualnych z systemem Edge
* Router warstwy 0
* Włącz protokół BGP na routerze tier0

## <a name="vsphere-cluster"></a>klaster vSphere

Hosty ESXi są skonfigurowane jako klaster, aby zapewnić wysoką dostępność chmury prywatnej automatycznej synchronizacji. Podczas tworzenia chmury prywatnej do automatycznej synchronizacji składniki zarządzania programu vSphere są wdrażane w pierwszym klastrze. Pula zasobów jest tworzona dla składników zarządzania, a wszystkie maszyny wirtualne zarządzania są wdrażane w tej puli zasobów. Nie można usunąć pierwszego klastra, aby zmniejszyć chmurę prywatną do automatycznej synchronizacji. klaster vSphere zapewnia wysoką dostępność maszyn wirtualnych korzystających z **VSPHERE ha**. Niepowodzenia do tolerowania są zależne od liczby dostępnych węzłów w klastrze. Można użyć ```Number of nodes = 2N+1``` formuły, gdzie ```N``` jest liczbą niepowodzeń, które mają być tolerowane.

### <a name="vsphere-cluster-limits"></a>limity klastrów vSphere

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej automatycznej synchronizacji (pierwszy klaster vSphere) | 3 |
| Maksymalna liczba węzłów w klastrze vSphere w chmurze prywatnej automatycznej synchronizacji | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej automatycznej synchronizacji | 64 |
| Maksymalna liczba klastrów vSphere w chmurze prywatnej automatycznej synchronizacji | 21 |
| Minimalna liczba węzłów w nowym klastrze vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

Czasami konieczne jest wprowadzenie zmian w konfiguracji infrastruktury VMware. Obecnie te interwały mogą wystąpić co 1-2 miesięcy, ale częstotliwość powinna odrzucać się w czasie. Ten typ konserwacji można zwykle wykonać bez przerywania normalnego zużycia usług automatycznej synchronizacji. W czasie konserwacji programu VMware następujące usługi nadal działają bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i uaktualnienia

Automatyczna synchronizacja jest odpowiedzialna za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej automatycznej synchronizacji.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki błędów wydane przez oprogramowanie VMware.
* **Aktualizacje**. Zmiana wersji pomocniczej składnika stosu VMware.
* **Uaktualnienia**. Główna zmiana wersji składnika stosu VMware.

Automatyczna synchronizacja programu testuje krytyczną poprawkę zabezpieczeń, gdy tylko staną się dostępne z programu VMware. W ramach umowy SLA funkcja automatycznej synchronizacji przedstawia poprawkę zabezpieczeń w celu automatycznej synchronizacji środowisk chmury prywatnej w ciągu tygodnia.

Automatyczna synchronizacja zapewnia aktualizacje kwartalne dla składników oprogramowania VMware. Po udostępnieniu nowej wersji głównej oprogramowania VMware, automatyczna synchronizacja z klientami umożliwia koordynowanie odpowiedniego okna obsługi uaktualnienia. 

## <a name="next-steps"></a>Następne kroki

* [Konserwacja i aktualizacje automatycznej synchronizacji](cloudsimple-maintenance-updates.md)
