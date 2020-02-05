---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Konserwacja i aktualizacje dotyczące automatycznej synchronizacji
description: Zawiera opis procesu usługi automatycznej synchronizacji na potrzeby zaplanowanej konserwacji i aktualizacji
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf5937183fc20579ecd21aca8543a0a78d4b9ff3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025031"
---
# <a name="avs-maintenance-and-updates"></a>Konserwacja i aktualizacje automatycznej synchronizacji

Środowisko chmury prywatnej do automatycznej synchronizacji zostało zaprojektowane z założeniami braku single point of failure.

* Klastry ESXi są skonfigurowane z wysoką dostępnością vSphere (HA). Rozmiary klastrów mają co najmniej jeden węzeł zapasowy na potrzeby odporności.
* Nadmiarowy magazyn podstawowy jest dostarczany przez sieci vSAN, który wymaga co najmniej trzech węzłów w celu zapewnienia ochrony przed pojedynczym awarią. Sieci vSAN można skonfigurować w celu zapewnienia większej odporności dla większych klastrów.
* Maszyny wirtualne programu vCenter, PSC i NSX Manager są skonfigurowane z magazynem RAID-10, aby zapewnić ochronę przed awariami magazynu. Maszyny wirtualne są chronione przed awariami węzłów/sieci przez vSphere HA.
* Hosty ESXi mają nadmiarowe wentylatory i karty sieciowe.
* Przełączniki TOR i spin są skonfigurowane w parach HA w celu zapewnienia odporności.

Automatyczna synchronizacja programu umożliwia ciągłe monitorowanie następujących maszyn wirtualnych w celu zapewnienia przestoju i dostępności oraz zapewnia umowy SLA dostępności:

* Hosty ESXi
* vCenter
* PSC
* NSX Manager

Automatyczna synchronizacja jest również monitorowana w następujący sposób w przypadku awarii:

* Dyski twarde
* Fizyczne porty kart sieciowych
* Serwery
* Wachlarz
* Power
* Przełączniki
* Przełącz porty

Jeśli dysk lub węzeł ulegnie awarii, nowy węzeł zostanie automatycznie dodany do klastra programu VMware, którego dotyczy usterka, w celu natychmiastowego przywrócenia kondycji.

Automatyczna synchronizacja kopii zapasowych, zachowywanie i aktualizowanie tych elementów VMware w chmurach prywatnych automatycznej synchronizacji:

* ESXi
* Usługi platformy vCenter
* Kontroler
* Sieci vSAN
* NSX

## <a name="back-up-and-restore"></a>Tworzenie i przywracanie kopii zapasowej

Kopia zapasowa automatycznej synchronizacji obejmuje:

* Nocne przyrostowe kopie zapasowe reguł vCenter, PSC i DVS.
* natywne interfejsy API programu vCenter do tworzenia kopii zapasowych składników w warstwie aplikacji.
* Automatyczna kopia zapasowa przed aktualizacją lub uaktualnieniem oprogramowania do zarządzania VMware.
* Szyfrowanie danych programu vCenter w źródle przed transferem danych przez szyfrowany kanał TLS 1.2 do platformy Azure. Dane są przechowywane w obiekcie blob platformy Azure, w którym są replikowane między regionami.

Możesz zażądać przywrócenia, otwierając [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Konserwacja

Automatyczna synchronizacja jest przeprowadzana w ramach kilku typów planowanej konserwacji.

### <a name="backendinternal-maintenance"></a>Zaplecze/konserwacja wewnętrzna

Ta konserwacja zazwyczaj polega na ponownym konfigurowaniu zasobów fizycznych lub instalowaniu poprawek oprogramowania. Nie ma to wpływu na normalne użycie zasobów, które są w trakcie obsługi. W przypadku nadmiarowych kart sieciowych w każdym stojaku fizycznym nie ma to wpływu na normalny ruch sieciowy i automatyczna synchronizacja operacji w chmurze prywatnej. Możesz zauważyć, że wpływ na wydajność jest możliwy tylko wtedy, gdy organizacja oczekuje, że w czasie konserwacji ma być używana pełna nadmiarowa przepustowość.

### <a name="avs-portal-maintenance"></a>Konserwacja portalu automatycznej konfiguracji

Niektóre ograniczone przestojy usługi są wymagane, gdy jest aktualizowana płaszczyzna lub infrastruktura kontroli automatycznej synchronizacji. Obecnie interwały konserwacji mogą być tak częste jak raz miesięcznie. Częstotliwość powinna odrzucać się w czasie. Automatyczna synchronizacja zapewnia powiadomienia o konserwacji portalu i utrzymuje interwał tak krótki jak to możliwe. W czasie konserwacji portalu następujące usługi kontynuują działanie bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

### <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

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

[Tworzenie kopii zapasowych maszyn wirtualnych obciążeń przy użyciu Veeam](backup-workloads-veeam.md)
