---
title: Komponenty VMware w chmurze prywatnej
titleSuffix: Azure VMware Solution by CloudSimple
description: W tym artykule opisano sposób instalowania składników VMware w chmurze prywatnej
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279509"
---
# <a name="private-cloud-vmware-components"></a>Komponenty VMware w chmurze prywatnej

Private Cloud to izolowany stos VMware (hosty ESXi, vCenter, vSAN i NSX) zarządzany przez serwer vCenter w domenie zarządzania.  Usługa CloudSimple umożliwia wdrażanie VMware natywnie na infrastrukturze platformy Azure bez systemu operacyjnego w lokalizacjach platformy Azure.  Chmury prywatne są zintegrowane z resztą usługi Azure Cloud.  Chmura prywatna jest wdrażana z następującymi składnikami stosu VMware:

* **VMware ESXi -** Funkcja Hypervisor na dedykowanych węzłach platformy Azure
* **VMware vCenter -** Urządzenie do scentralizowanego zarządzania środowiskiem vSphere chmury prywatnej
* **VMware vSAN -** Rozwiązanie infrastruktury hiperkonwergentnej
* **Centrum danych VMware NSX -** Oprogramowanie do wirtualizacji i zabezpieczeń sieci  

## <a name="vmware-component-versions"></a>Wersje komponentów VMware

Stos VMware w chmurze prywatnej jest wdrażany z następującą wersją oprogramowania.

| Składnik | Wersja | Wersja licencjonowana |
|-----------|---------|------------------|
| Esxi | 6,72 | Przedsiębiorstwo Plus |
| vCenter | 6,72 | vCenter Standard |
| Vsan | 6.7 | Enterprise |
| Centrum danych NSX | 2.4.1 | Zaawansowane |

## <a name="esxi"></a>Esxi

VMware ESXi jest instalowany w aprowizgowych węzłach CloudSimple podczas tworzenia chmury prywatnej.  ESXi udostępnia hipernadzorcę do wdrażania maszyn wirtualnych obciążenia (VM).  Węzły zapewniają infrastrukturę hiperkonwergentną (obliczeniową i magazynową) w chmurze prywatnej.  Węzły są częścią klastra vSphere w chmurze prywatnej.  Każdy węzeł ma cztery interfejsy sieci fizycznych podłączone do sieci podkładów.  Dwa fizyczne interfejsy sieciowe są używane do tworzenia **vSphere Distributed Switch (VDS)** w vCenter i dwa są używane do tworzenia **nsx zarządzanych wirtualnego przełącznika rozproszonego (N-VDS)**.  Interfejsy sieciowe są konfigurowane w trybie aktywnym i aktywnym w celu zapewnienia wysokiej dostępności.

Dowiedz się więcej o VMware ESXi

## <a name="vcenter-server-appliance"></a>Urządzenie serwera vCenter

VCenter Server Appliance (VCSA) udostępnia funkcje uwierzytelniania, zarządzania i aranżacji dla VMware Solution by CloudSimple. Usługa VCSA z wbudowanym kontrolerem usług platformy (PSC) jest wdrażana podczas tworzenia chmury prywatnej.  Usługa VCSA jest wdrażana w klastrze vSphere, który jest tworzony podczas wdrażania chmury prywatnej.  Każda chmura prywatna ma swój własny VCSA.  Rozszerzenie chmury prywatnej dodaje węzły do VCSA w chmurze prywatnej.

### <a name="vcenter-single-sign-on"></a>VCenter logowanie jednokrotne

Wbudowany kontroler usług platformy w u usług VCSA jest skojarzony z **domeną rejestracji jednokrotnej vCenter**.  Nazwa domeny to **cloudsimple.local**.  Użytkownik domyślny **CloudOwner@cloudsimple.com** jest tworzony, aby uzyskać dostęp do vCenter.  Możesz dodać lokalne/azureowe źródła tożsamości usługi active directory [dla vCenter](set-vcenter-identity.md).

## <a name="vsan-storage"></a>Pamięć masowa vSAN

Chmury prywatne są tworzone z w pełni skonfigurowaną pamięcią masową vSAN typu all-flash, lokalną dla klastra.  Co najmniej trzy węzły tej samej jednostki SKU są wymagane do utworzenia klastra vSphere z magazynem danych vSAN.  Dezduplikacji i kompresji są domyślnie włączone w magazynie danych vSAN.  W każdym węźle klastra vSphere tworzone są dwie grupy dysków. Każda grupa dysków zawiera jeden dysk pamięci podręcznej i trzy dyski pojemności.

Domyślna zasada magazynu vSAN jest tworzona w klastrze vSphere i stosowana do magazynu danych vSAN.  Ta zasada określa sposób aprowizowania i przydzielania obiektów magazynu maszyn wirtualnych w magazynie danych w celu zagwarantowania wymaganego poziomu usługi.  Zasady magazynowania definiuje **błędy tolerowania (FTT)** i **metoda tolerancji awarii**.  Można utworzyć nowe zasady magazynu i zastosować je do maszyn wirtualnych. Aby utrzymać umowy SLA, 25% wolnych mocy produkcyjnych musi być utrzymywane w magazynie danych vSAN.  

### <a name="default-vsan-storage-policy"></a>Domyślne zasady przechowywania vSAN

W poniższej tabeli przedstawiono domyślne parametry zasad magazynu vSAN.

| Liczba węzłów w klastrze vSphere | Ftt | Metoda tolerancji awarii |
|------------------------------------|-----|--------------------------|
| 3 i 4 węzły | 1 | RAID 1 (dublowanie) - tworzy 2 kopie |
| Od 5 do 16 węzłów | 2 | RAID 1 (dublowanie) - tworzy 3 kopie |

## <a name="nsx-data-center"></a>Centrum danych NSX

NSX Data Center zapewnia wirtualizację sieci, mikrosegmentację i funkcje zabezpieczeń sieci w chmurze prywatnej.  Wszystkie usługi obsługiwane przez NSX Data Center można skonfigurować w chmurze prywatnej za pośrednictwem sieci NSX.  Podczas tworzenia chmury prywatnej następujące składniki NSX są instalowane i konfigurowane.

* Menedżer NSXT
* Strefy transportu
* Profil hosta i edge'a uplink
* Przełącznik logiczny do transportu krawędzi, Ext1 i Ext2
* Pula adresów IP dla węzła transportu ESXi
* Pula adresów IP dla węzła transportu krawędzi
* Węzły krawędzi
* Reguła przeciw koligacji DRS dla kontrolerów i maszyn wirtualnych krawędzi
* Router warstwy 0
* Włączanie protokołu BGP na routerze Tier0

## <a name="vsphere-cluster"></a>Klaster vSphere

Hosty ESXi są skonfigurowane jako klaster, aby zapewnić wysoką dostępność chmury prywatnej.  Podczas tworzenia chmury prywatnej składniki zarządzania vSphere są wdrażane w pierwszym klastrze.  Pula zasobów jest tworzona dla składników zarządzania i wszystkie maszyny wirtualne zarządzania są wdrażane w tej puli zasobów. Nie można usunąć pierwszego klastra, aby zmniejszyć chmurę prywatną.  Klaster vSphere zapewnia wysoką dostępność dla maszyn wirtualnych przy użyciu **vSphere HA**.  Błędy tolerowania są oparte na liczbie dostępnych węzłów w klastrze.  Można użyć ```Number of nodes = 2N+1``` formuły, ```N``` gdzie jest liczba błędów tolerować.

### <a name="vsphere-cluster-limits"></a>Limity klastra vSphere

| Zasób | Limit |
|----------|-------|
| Minimalna liczba węzłów do utworzenia chmury prywatnej (pierwszy klaster vSphere) | 3 |
| Maksymalna liczba węzłów w klastrze vSphere w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Maksymalna liczba klastrów vSphere w chmurze prywatnej | 21 |
| Minimalna liczba węzłów w nowym klastrze vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

Od czasu do czasu konieczne jest wprowadzenie zmian w konfiguracji infrastruktury VMware. Obecnie te odstępy mogą występować co 1-2 miesiące, ale oczekuje się, że częstotliwość będzie spadać w czasie. Ten typ konserwacji można zwykle wykonać bez przerywania normalnego zużycia usług CloudSimple. Podczas interwału konserwacji VMware następujące usługi nadal działają bez żadnego wpływu:

* Płaszczyzna zarządzania VMware i aplikacje
* Dostęp do vCenter
* Cała sieć i pamięć masowa
* Cały ruch na platformie Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i aktualizacje

CloudSimple jest odpowiedzialny za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki błędów wydane przez VMware.
* **Aktualizacje**. Drobna zmiana wersji składnika stosu VMware.
* **Uaktualnienia**. Główna zmiana wersji składnika stosu VMware.

CloudSimple testuje krytyczną poprawkę zabezpieczeń, gdy tylko stanie się dostępna z VMware. Zgodnie z umowy SLA CloudSimple wprowadza poprawkę zabezpieczeń do środowisk chmury prywatnej w ciągu tygodnia.

CloudSimple zapewnia kwartalne aktualizacje konserwacji składników oprogramowania VMware. Gdy dostępna jest nowa główna wersja oprogramowania VMware, CloudSimple współpracuje z klientami w celu koordynowania odpowiedniego okna konserwacji w celu uaktualnienia.  

## <a name="next-steps"></a>Następne kroki

* [Konserwacja i aktualizacje CloudSimple](cloudsimple-maintenance-updates.md)
