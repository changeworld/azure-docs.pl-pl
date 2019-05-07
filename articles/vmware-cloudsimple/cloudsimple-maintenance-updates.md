---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple - CloudSimple konserwacji i aktualizacji
description: W tym artykule opisano proces usługi CloudSimple zaplanowanej konserwacji i aktualizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160248"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple konserwacji i aktualizacji

W środowisku chmury prywatnej jest przeznaczona do ma żadnego pojedynczego punktu awarii:

* ESXi klastry są skonfigurowane z wysoką dostępnością vSphere. Klastry mają rozmiar występuje co najmniej jeden węzeł wolnym pod kątem odporności.
* Magazyn nadmiarowy podstawowy jest zapewniana przez sieć vSAN, która wymaga co najmniej trzy węzły w celu zapewnienia ochrony przed awarią jednego. Aby zapewnić odporność wyższe w przypadku większych klastrów można skonfigurować sieci vSAN.
* vCenter, PSC i NSX Menedżera maszyn wirtualnych są skonfigurowane za pomocą macierzy RAID 10 magazynu zasad w celu ochrony przed awariami magazynu. Maszyny wirtualne są chronione przed awariami węzła i sieci przez vSphere wysokiej dostępności.
* Hosty ESXi mają wentylatory nadmiarowe i kart sieciowych.
* Przełączniki TOR i grzbietu są konfigurowane w parze wysokiej dostępności, aby zapewnić odporność.

CloudSimple stale monitoruje następujące maszyny wirtualne dotyczące czasu dostępności i dostępności i udostępnia umów SLA dotyczących dostępności:

* Hosty ESXi
* vCenter
* PSC
* Menedżer NSX

CloudSimple również monitoruje stale następujące błędy:

* Dyski twarde
* Portów fizycznej karty Sieciowej
* Serwery
* Fanów
* Zasilania
* Przełączniki
* Porty przełącznika

W przypadku niepowodzenia dysku lub węzła nowego węzła jest automatycznie dodawany do zainfekowanego klaster VMware, aby natychmiast przywrócić go do kondycji.

CloudSimple tworzy kopie zapasowe, przechowuje i aktualizuje te elementy programu VMware w chmurach prywatnych:

* ESXi
* vCenter usługi platformy
* Kontroler
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Kopia zapasowa CloudSimple zawiera:

* Przyrostowych kopii zapasowych w nocy vCenter, PSC i DVS reguły.
* Użycie programu vCenter natywnych interfejsów API, aby utworzyć kopię zapasową składniki w warstwie aplikacji.
* Automatyczne kopie zapasowe przed przystąpieniem do wszelkich aktualizacji lub uaktualnienia oprogramowania VMware do zarządzania.
* Szyfrowanie danych w źródle, przez program vCenter, zanim transferu danych za pośrednictwem szyfrowanego kanału TLS1.2 na platformie Azure. Dane są przechowywane w obiektu blob platformy Azure, w których są replikowane między regionami.

Możesz poprosić przywracania, otwierając [żądania pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Konserwacja

CloudSimple wykonuje kilka typów planowanej konserwacji.

### <a name="backendinternal-maintenance"></a>Wewnętrznej bazy danych/wewnętrznej konserwacji

Ta konserwacja zwykle obejmuje ponownej konfiguracji fizycznych zasobów, a następnie instalowania poprawek oprogramowania. Nie wpływa na normalne zużycie zasobów są obsługiwane. Za pomocą nadmiarowych zamierza każdy stojak fizycznych kart sieciowych nie ma wpływu na ruch sieciowy normalnego i operacji w chmurze prywatnej. Wpływ na wydajność może się okazać tylko wtedy, gdy Twoja organizacja spodziewa się korzystać z pełnej nadmiarowe przepustowości przedział czasu obsługi.

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple obsługi portalu

Przestój ograniczone usługi jest wymagany po zaktualizowaniu CloudSimple płaszczyznę kontroli ani infrastruktury. Obecnie konserwacje może być tak często, jako jeden raz na miesiąc. Częstotliwość oczekuje się, aby odrzucić wraz z upływem czasu. CloudSimple zapewnia powiadomienie o konserwacji portalu i utrzymuje możliwie krótki interwał. Przedział czasu obsługi portalu następujące usługi nadal działać bez żadnego wpływu na:

* Płaszczyzna zarządzania VMware i aplikacji
* dostęp vCenter
* Wszystkie sieci i magazynu
* Całego ruchu platformy Azure

### <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury programu VMware

Czasami zachodzi konieczność wprowadzania zmian w konfiguracji infrastruktury VMware.  Obecnie interwałami może wystąpić co 1 – 2 miesiące, ale oczekiwano częstotliwość odrzucenia wraz z upływem czasu. Ten typ obsługi zwykle może odbywać się bez zakłócania normalnej korzystania z usług CloudSimple. Przedział czasu obsługi VMware następujące usługi nadal działać bez żadnego wpływu na:

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

[Utwórz kopię zapasową obciążenia maszyn wirtualnych przy użyciu Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).