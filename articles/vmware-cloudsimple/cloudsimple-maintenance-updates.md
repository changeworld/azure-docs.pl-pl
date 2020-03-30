---
title: Konserwacja i aktualizacje CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje proces usługi CloudSimple dla zaplanowanej konserwacji i aktualizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 826fae1123b355a4143118b53ba649f0939acaf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025031"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Konserwacja i aktualizacje CloudSimple

Środowisko private cloud jest zaprojektowany, aby mieć jeden punkt awarii.

* Klastry ESXi są skonfigurowane z vSphere High Availability (HA). Klastry mają rozmiar, aby mieć co najmniej jeden węzeł zapasowy dla odporności.
* Nadmiarowy magazyn podstawowy jest dostarczany przez vSAN, który wymaga co najmniej trzech węzłów, aby zapewnić ochronę przed pojedynczym błędem. VSAN można skonfigurować w celu zapewnienia większej odporności dla większych klastrów.
* Maszyny wirtualne vCenter, PSC i NSX Manager są skonfigurowane z pamięcią masową RAID-10 w celu ochrony przed awarią magazynu. Maszyny wirtualne są chronione przed awariami węzłów/sieci przez vSphere HA.
* Hosty ESXi mają nadmiarowe wentylatory i karty sieciowe.
* Przełączniki TOR i kręgosłupa są skonfigurowane w parach wysokiej dostępności, aby zapewnić odporność.

CloudSimple stale monitoruje następujące maszyny wirtualne pod kątem czasu pracy bez przestojów i dostępności oraz udostępnia łącze SLA dostępności:

* Hosty ESXi
* vCenter
* Psc
* Menedżer NSX

CloudSimple monitoruje również następujące stale na awarie:

* Dyski twarde
* Fizyczne porty karty sieciowej
* Serwery
* Wentylatory
* Zasilanie
* Przełączniki
* Przełączanie portów

Jeśli dysk lub węzeł ulegnie awarii, nowy węzeł jest automatycznie dodawany do klastra VMware, którego dotyczy problem, aby natychmiast przywrócić go do stanu zdrowia.

CloudSimple łączy w sobie, przechowuje i aktualizuje te elementy VMware w chmurach prywatnych:

* Esxi
* Usługi platformy vCenter
* Kontrolera
* Vsan
* Nsx

## <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopia zapasowa CloudSimple zawiera:

* Nocne przyrostowe kopie zapasowe reguł vCenter, PSC i DVS.
* Natywne interfejsy API vCenter do tworzenia kopii zapasowych składników w warstwie aplikacji.
* Automatyczna kopia zapasowa przed aktualizacją lub uaktualnieniem oprogramowania do zarządzania VMware.
* Szyfrowanie danych vCenter u źródła przed przesłaniem danych za pomocą zaszyfrowanego kanału TLS1.2 na platformę Azure. Dane są przechowywane w obiekcie blob platformy Azure, gdzie jest replikowany w różnych regionach.

Można zażądać przywrócenia, otwierając [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Konserwacja

CloudSimple wykonuje kilka typów planowanej konserwacji.

### <a name="backendinternal-maintenance"></a>Zaplecze/konserwacja wewnętrzna

Ta konserwacja zazwyczaj polega na ponownym skonfigurowaniu zasobów fizycznych lub zainstalowaniu poprawek oprogramowania. Nie ma to wpływu na normalne zużycie obsługiwanych zasobów. W razie konieczności przechodzenia do każdego fizycznego stojaka nie ma to wpływu na nadmiarowe karty sieciowe przechodzące do każdego fizycznego stojaka, normalny ruch sieciowy i operacje w chmurze prywatnej. Wpływ na wydajność może wystąpić tylko wtedy, gdy organizacja spodziewa się użyć pełnej nadmiarowej przepustowości w okresie międzyobsługowym.

### <a name="cloudsimple-portal-maintenance"></a>Konserwacja portalu CloudSimple

Niektóre ograniczone przestoje usługi jest wymagane, gdy cloudSimple płaszczyzny sterowania lub infrastruktury jest aktualizowana. Obecnie okresy międzyobsługowe mogą być tak częste, jak raz w miesiącu. Oczekuje się, że częstotliwość będzie spadać w czasie. CloudSimple zapewnia powiadomienie o konserwacji portalu i utrzymuje interwał tak krótki, jak to możliwe. Podczas interwału konserwacji portalu następujące usługi nadal działają bez żadnego wpływu:

* Płaszczyzna zarządzania VMware i aplikacje
* Dostęp do vCenter
* Cała sieć i pamięć masowa
* Cały ruch na platformie Azure

### <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

Od czasu do czasu konieczne jest wprowadzenie zmian w konfiguracji infrastruktury VMware.  Obecnie te odstępy mogą występować co 1-2 miesiące, ale oczekuje się, że częstotliwość będzie spadać w czasie. Ten typ konserwacji można zwykle wykonać bez przerywania normalnego zużycia usług CloudSimple. Podczas interwału konserwacji VMware następujące usługi nadal działają bez żadnego wpływu:

* Płaszczyzna zarządzania VMware i aplikacje
* Dostęp do vCenter
* Cała sieć i pamięć masowa
* Cały ruch na platformie Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i uaktualnienia

CloudSimple jest odpowiedzialny za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki błędów wydane przez VMware.
* **Aktualizacje**. Drobna zmiana wersji składnika stosu VMware.
* **Uaktualnienia**. Główna zmiana wersji składnika stosu VMware.

CloudSimple testuje krytyczną poprawkę zabezpieczeń, gdy tylko stanie się dostępna z VMware. Zgodnie z umowy SLA CloudSimple wprowadza poprawkę zabezpieczeń do środowisk chmury prywatnej w ciągu tygodnia.

CloudSimple zapewnia kwartalne aktualizacje konserwacji składników oprogramowania VMware. Gdy dostępna jest nowa główna wersja oprogramowania VMware, CloudSimple współpracuje z klientami w celu koordynowania odpowiedniego okna konserwacji w celu uaktualnienia.

## <a name="next-steps"></a>Następne kroki

[Tworzenie kopii zapasowych maszyn wirtualnych z obciążeniem przy użyciu usługi Veeam](backup-workloads-veeam.md)
