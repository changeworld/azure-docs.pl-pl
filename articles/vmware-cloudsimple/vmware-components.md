---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple - składników VMware chmury prywatnej
description: W tym artykule opisano, jak VMware składniki są zainstalowane w chmurze prywatnej
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160188"
---
# <a name="private-cloud-vmware-components"></a>Składniki programu VMware chmury prywatnej

Chmura prywatna to izolowane stos oprogramowania VMware (hosty ESXi, vCenter, sieć vSAN i NSX) środowiska zarządzanego przez serwer vCenter w domenie zarządzania.  Usługa CloudSimple umożliwia wdrażanie VMware natywnie w infrastrukturze platformy Azure bez systemu operacyjnego w lokalizacji platformy Azure.  Chmury prywatne są zintegrowane z pozostałą częścią chmury platformy Azure.  Chmura prywatna jest wdrażana za pomocą następujących składników stos oprogramowania VMware:

* **Host VMware ESXi -** węzły dedykowane funkcji Hypervisor na platformie Azure
* **VMware vCenter -** urządzenia w celu scentralizowanego zarządzania środowiska vSphere chmury prywatnej
* **Sieć vSAN firmy VMware -** rozwiązania hiperkonwergentnego infrastruktury
* **Centrum danych NSX VMware -** sieci wirtualizacji i oprogramowanie zabezpieczające  

## <a name="vmware-component-versions"></a>Wersje składników oprogramowania VMware

Stos oprogramowania VMware Chmura prywatna jest wdrażana przy użyciu następujących wersji oprogramowania.

| Składnik | Version | Wersji licencjonowanej |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX centrum danych | 2.3 | Zaawansowane |

## <a name="esxi"></a>ESXi

Host VMware ESXi jest zainstalowany na zakupione CloudSimple węzły, podczas tworzenia chmury prywatnej.  ESXi zapewnia funkcji hypervisor wdrażania obciążeń maszyn wirtualnych (VM).  Węzły zawierają hiper zbieżności infrastruktury (obliczanie i magazynowanie) w chmurze prywatnej.  Węzły są częścią klastra vSphere w chmurze prywatnej.  Każdy węzeł ma cztery interfejsy sieci fizycznej połączone underlay sieci.  Dwa interfejsy sieci fizycznej są używane do tworzenia **vSphere rozproszonego przełącznika (VDS)** vCenter i dwa są używane do tworzenia **zarządzane NSX rozproszonego przełącznika wirtualnego (N VDS)** .  Interfejsy sieciowe są konfigurowane w trybie aktywny aktywny w celu zapewnienia wysokiej dostępności.

Dowiedz się więcej o VMware ESXi

## <a name="vcenter-server-appliance"></a>urządzenie serwera vCenter

urządzenia programu vCenter server (VCSA) oferuje funkcje uwierzytelniania, zarządzania i aranżacji VMware Solution by CloudSimple. VCSA przy użyciu osadzonych platformy usługi kontrolera (PSC) jest wdrażany, podczas tworzenia chmury prywatnej.  VCSA została wdrożona w klastrze vSphere, który jest tworzony podczas wdrażania chmury prywatnej.  Każda Chmura prywatna ma swój własny VCSA.  Rozszerzanie chmury prywatnej dodaje węzły do VCSA w chmurze prywatnej.

### <a name="vcenter-single-sign-on"></a>vCenter logowanie jednokrotne

Osadzony kontrolera usług platformy, na VCSA jest skojarzony z **vCenter logowanie jednokrotne domeny**.  Nazwa domeny jest **cloudsimple.local**.  Użytkownik domyślny **CloudOwner@cloudsimple.com** zostanie utworzony w celu uzyskania dostępu vCenter.  Można dodać usługi active directory na lokalnego/platformy Azure [źródła tożsamości dla vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>vSAN storage

Chmury prywatne są tworzone za pomocą sieć vsan, która pełni skonfigurowanej samych pamięci flash z magazynu lokalnego do klastra.  Minimalna trzy węzły w tej samej jednostki SKU są wymagane do utworzenia klastra vSphere z magazynu danych sieci vSAN.  Deduplikacja i kompresja są domyślnie włączone na magazyn danych sieci vSAN.  Dwie grupy dysków są tworzone w każdym węźle klastra vSphere. Każda grupa dysku zawiera dysk pamięci podręcznej i trzy dyski pojemności.

Domyślne zasady przechowywania sieć vsan, która jest utworzona w klastrze vSphere i zastosowane do magazynu danych sieci vSAN.  Ta zasada Określa, jak zainicjowano obsługę administracyjną i w magazynie danych, aby zagwarantować wymagany poziom usług obiektów magazynu maszyny Wirtualnej.  Zasady przechowywania określają **(FTT) tolerowanych niepowodzeń** i **metoda tolerancji błędów**.  Można tworzyć nowe zasady przechowywania i zastosować je do maszyn wirtualnych. Aby zachować umowę SLA, 25% wolnego miejsca musi być utrzymywana w magazynie danych sieci vSAN.  

### <a name="default-vsan-storage-policy"></a>Domyślne zasady przechowywania sieci vSAN

W poniższej tabeli przedstawiono domyślne parametry zasad magazynu sieci vSAN.

| Liczba węzłów w klastrze vSphere | FTT | Metoda tolerancji błędów |
|------------------------------------|-----|--------------------------|
| 3 i 4 węzłami | 1 | RAID 1 (dublowanie) — tworzy kopie 2 |
| węzły 5-16 | 2 | RAID 1 (dublowanie) — tworzy 3 kopie |

## <a name="nsx-data-center"></a>NSX centrum danych

Centrum danych NSX zapewnia wirtualizację sieci, micro segmentacji i funkcje zabezpieczeń sieci w chmurze prywatnej.  Można skonfigurować wszystkich usług obsługiwanych przez usługę NSX centrum danych w chmurze prywatnej przy użyciu NSX.  Podczas tworzenia chmury prywatnej, następujące składniki NSX są zainstalowane i skonfigurowane.

* Menedżer NSXT
* Transport Zones
* Host i profilu pasma krawędzi
* Przełącznik logiczny dla transportu, Ext1 i Ext2
* Puli adresów IP dla węzła transportu ESXi
* Puli adresów IP dla węzła transportu krawędzi
* Węzły brzegowe
* Reguły konfiguracji zapobiegającej koligacji usługi rejestracji urządzeń dla kontrolera i maszyny wirtualne usługi Edge
* Router warstwy 0
* Włącz protokół BGP w warstwie Tier0 routera

## <a name="vsphere-cluster"></a>klastrze vSphere

Hosty ESXi są konfigurowane jako klaster, aby zapewnić wysoką dostępność chmury prywatnej.  Podczas tworzenia chmury prywatnej, składniki zarządzania vSphere są wdrażane w klastrze pierwszego.  Pula zasobów jest tworzony dla składniki zarządzania i wszystkie maszyny wirtualne zarządzania są wdrażane w tej puli zasobów. Nie można usunąć pierwszy klaster można zmniejszyć chmury prywatnej.  vSphere klastra zapewnia wysoką dostępność dla maszyn wirtualnych przy użyciu **vSphere HA**.  Tolerowana liczba niepowodzeń są oparte na liczbie dostępnych węzłów w klastrze.  Można użyć formuły ```Number of nodes = 2N+1``` gdzie ```N``` jest liczba tolerowana liczba niepowodzeń.

### <a name="vsphere-cluster-limits"></a>limity klastrze vSphere

| Resource | Limit |
|----------|-------|
| Minimalna liczba węzłów w celu utworzenia chmury prywatnej (pierwszy klaster vSphere) | 3 |
| Maksymalna liczba węzłów w vSphere klastra w chmurze prywatnej | 16 |
| Maksymalna liczba węzłów w chmurze prywatnej | 64 |
| Maksymalna liczba vSphere klastrów w chmurze prywatnej | 21 |
| Minimalna liczba węzłów w oprogramowaniu vSphere nowego klastra | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury programu VMware

Czasami zachodzi konieczność wprowadzania zmian w konfiguracji infrastruktury VMware. Obecnie interwałami może wystąpić co 1 – 2 miesiące, ale oczekiwano częstotliwość odrzucenia wraz z upływem czasu. Ten typ obsługi zwykle może odbywać się bez zakłócania normalnej korzystania z usług CloudSimple. Przedział czasu obsługi VMware następujące usługi nadal działać bez żadnego wpływu na:

* Płaszczyzna zarządzania VMware i aplikacji
* dostęp vCenter
* Wszystkie sieci i magazynu
* Całego ruchu platformy Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i uaktualnienia

CloudSimple jest odpowiedzialny za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki wydane firmy VMware.
* **Aktualizacje**. Zmiana wersji pomocniczej składnika stos oprogramowania VMware.
* **Uaktualnienia**. Zmiana wersji głównej części stosu oprogramowania VMware.

CloudSimple testy poprawki krytycznych, jak staje się ona dostępna z programu VMware. Na umowie SLA CloudSimple wprowadza poprawki zabezpieczeń w środowiskach chmury prywatnej w ciągu tygodnia.

CloudSimple zapewnia konserwacji kwartalnej aktualizacji składników oprogramowania VMware. Po udostępnieniu nowej wersji głównej oprogramowanie VMware CloudSimple współpracuje z klientów do koordynowania okna obsługi odpowiednie dla uaktualnienie.  

## <a name="next-steps"></a>Kolejne kroki

* [CloudSimple konserwacji i aktualizacji](cloudsimple-maintenance-updates.md)