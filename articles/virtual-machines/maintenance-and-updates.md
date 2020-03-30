---
title: Konserwacja i aktualizacje
description: Omówienie konserwacji i aktualizacji maszyn wirtualnych działających na platformie Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250233"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Konserwacja maszyn wirtualnych na platformie Azure

Platforma Azure okresowo aktualizuje swoją platformę, aby zwiększyć niezawodność, wydajność i bezpieczeństwo infrastruktury hosta maszyn wirtualnych. Zakres aktualizacji obejmuje tematykę od poprawek składników oprogramowania w środowisku hostingu do uaktualniania składników sieci lub likwidowania sprzętu. 

Aktualizacje rzadko wpływają na hostowane maszyny wirtualne. Gdy aktualizacje mają wpływ, platforma Azure wybiera najmniej skuteczną metodę aktualizacji:

- Jeśli aktualizacja nie wymaga ponownego uruchomienia, maszyna wirtualna jest wstrzymana podczas aktualizacji hosta lub maszyna wirtualna jest migrowana na żywo do już zaktualizowanego hosta. 
- Jeśli konserwacja wymaga ponownego uruchomienia, zostaniesz powiadomiony o planowanej konserwacji. Platforma Azure udostępnia również przedział czasu, w którym można samodzielnie rozpocząć konserwację, w czasie, który działa dla Ciebie. Okno samoobsługi jest zazwyczaj 30 dni, chyba że konserwacja jest pilna. Platforma Azure inwestuje w technologie, aby zmniejszyć liczbę przypadków, w których planowana konserwacja platformy wymaga ponownego uruchomienia maszyn wirtualnych. Aby uzyskać instrukcje dotyczące zarządzania planowaną konserwacją, zobacz Obsługa powiadomień o planowanej konserwacji przy użyciu interfejsu [wiersza polecenia](maintenance-notifications-cli.md)platformy Azure, [programu PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md).

Na tej stronie opisano, jak platforma Azure wykonuje oba typy konserwacji. Aby uzyskać więcej informacji o nieplanowanych zdarzeniach (awariach), zobacz [Zarządzanie dostępnością maszyn wirtualnych dla systemu Windows](./windows/manage-availability.md) lub odpowiedni artykuł dla systemu [Linux](./linux/manage-availability.md).

W ramach maszyny Wirtualnej można otrzymywać powiadomienia o nadchodzącej konserwacji [przy użyciu zaplanowanych zdarzeń dla systemu Windows](./windows/scheduled-events.md) lub systemu [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Konserwacja, która nie wymaga ponownego uruchomienia

Większość aktualizacji platformy nie wpływa na maszyny wirtualne klientów. Gdy aktualizacja bez wpływu nie jest możliwe, platforma Azure wybiera mechanizm aktualizacji, który ma najmniejszy wpływ na maszyny wirtualne klientów. 

Większość nonzero-impact konserwacji wstrzymuje maszynę wirtualną na mniej niż 10 sekund. W niektórych przypadkach platforma Azure używa mechanizmów konserwacji zachowania pamięci. Mechanizmy te wstrzymują maszynę wirtualną na maksymalnie 30 sekund i zachowują pamięć w pamięci RAM. Maszyna wirtualna jest następnie wznawiana, a jej zegar jest automatycznie synchronizowany. 

Konserwacja zachowania pamięci działa dla ponad 90 procent maszyn wirtualnych platformy Azure. To nie działa dla serii G, M, N i H. Platforma Azure coraz częściej korzysta z technologii migracji na żywo i ulepsza mechanizmy konserwacji zachowania pamięci, aby skrócić czas trwania pauzy.  

Te operacje konserwacji, które nie wymagają ponownego uruchomienia, są stosowane w jednej domenie błędów naraz. Zatrzymują się, jeśli otrzymają jakiekolwiek sygnały ostrzegawcze. 

Tego typu aktualizacje mogą mieć wpływ na niektóre aplikacje. Gdy maszyna wirtualna jest migrowana na żywo do innego hosta, niektóre poufne obciążenia mogą wykazywać niewielki spadek wydajności w ciągu kilku minut prowadzących do wstrzymania maszyny Wirtualnej. Aby przygotować się do konserwacji maszyn wirtualnych i zmniejszyć wpływ podczas konserwacji platformy Azure, spróbuj [użyć zaplanowanych zdarzeń dla systemu Windows](./windows/scheduled-events.md) lub [Linux](./linux/scheduled-events.md) dla takich aplikacji. 

Istnieje również funkcja, kontrola konserwacji, w publicznej wersji zapoznawczej, które mogą pomóc w zarządzaniu konserwacji, która nie wymaga ponownego uruchomienia komputera. Musisz używać [hostów dedykowanych platformy Azure](./linux/dedicated-hosts.md) lub [izolowanej maszyny Wirtualnej.](../security/fundamentals/isolation-choices.md) Kontrola konserwacji umożliwia pomijanie aktualizacji platformy i stosowanie aktualizacji w wybranym czasie w 35-dniowym oknie kroczącym. Aby uzyskać więcej informacji, zobacz [Aktualizacje sterowania za pomocą kontroli konserwacji i interfejsu wiersza polecenia platformy Azure](maintenance-control-cli.md).


### <a name="live-migration"></a>Migracja na żywo

Migracja na żywo to operacja, która nie wymaga ponownego uruchomienia i która zachowuje pamięć dla maszyny Wirtualnej. Powoduje to pauzę lub zamrożenie, zwykle trwające nie dłużej niż 5 sekund. Z wyjątkiem serii G, M, N i H wszystkie maszyny wirtualne infrastruktury jako usługi (IaaS) kwalifikują się do migracji na żywo. Kwalifikujące się maszyny wirtualne stanowią ponad 90 procent maszyn wirtualnych IaaS, które są wdrażane w usłudze Azure floty. 

Platforma Azure rozpoczyna migrację na żywo w następujących scenariuszach:
- Planowana konserwacja
- Awaria sprzętu
- Optymalizacje alokacji

Niektóre scenariusze planowanej konserwacji używają migracji na żywo i można użyć zaplanowanych zdarzeń, aby wiedzieć z wyprzedzeniem, kiedy rozpocznie się operacje migracji na żywo.

Migracja na żywo może również służyć do przenoszenia maszyn wirtualnych, gdy algorytmy usługi Azure Machine Learning przewidują zbliżającą się awarię sprzętu lub jeśli chcesz zoptymalizować alokacje maszyn wirtualnych. Aby uzyskać więcej informacji na temat modelowania predykcyjnego, które wykrywa wystąpienia zdegradowanego sprzętu, zobacz [Zwiększanie odporności maszyny Wirtualnej platformy Azure za pomocą predykcyjnego uczenia maszynowego i migracji na żywo.](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) Powiadomienia o migracji na żywo są wyświetlane w witrynie Azure portal w dziennikach monitora i kondycji usługi, a także w zaplanowanych zdarzeniach, jeśli korzystasz z tych usług.

## <a name="maintenance-that-requires-a-reboot"></a>Konserwacja, która wymaga ponownego uruchomienia

W rzadkich przypadkach, gdy maszyny wirtualne muszą zostać ponownie uruchomione w celu planowanej konserwacji, zostaniesz powiadomiony z wyprzedzeniem. Planowana konserwacja ma dwa etapy: fazę samoobsługową i fazę zaplanowanej konserwacji.

Podczas *fazy samoobsługowej*, która zazwyczaj trwa cztery tygodnie, należy rozpocząć konserwację maszyn wirtualnych. W ramach samoobsługi można zbadać każdą maszynę wirtualną, aby zobaczyć jej stan i wynik ostatniego żądania konserwacji.

Po uruchomieniu samoobsługowej konserwacji maszyna wirtualna jest ponownie wcielona do już zaktualizowanego węzła. Ponieważ maszyna wirtualna zostanie ponownie uruchomiona, dysk tymczasowy zostanie utracony, a dynamiczne adresy IP skojarzone z interfejsem sieci wirtualnej są aktualizowane.

Jeśli podczas konserwacji samoobsługowej wystąpi błąd, operacja zostanie zatrzymana, maszyna wirtualna nie zostanie zaktualizowana i pojawi się opcja ponowienia próby samoobsługowej konserwacji. 

Po zakończeniu fazy samoobsługowej rozpoczyna *się faza zaplanowanej konserwacji.* Podczas tej fazy nadal można zbadać fazę konserwacji, ale nie można samodzielnie rozpocząć konserwacji.

Aby uzyskać więcej informacji na temat zarządzania konserwacją, która wymaga ponownego uruchomienia, zobacz **Obsługa powiadomień o planowanej konserwacji** przy użyciu interfejsu [wiersza polecenia](maintenance-notifications-cli.md)platformy Azure, programu [PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Zagadnienia dotyczące dostępności podczas zaplanowanej konserwacji 

Jeśli zdecydujesz się poczekać do fazy zaplanowanej konserwacji, istnieje kilka rzeczy, które należy wziąć pod uwagę, aby zachować najwyższą dostępność maszyn wirtualnych. 

#### <a name="paired-regions"></a>Sparowane regiony

Każdy region platformy Azure jest sparowany z innym regionem w tym samym sąsiedztwie geograficznym. Razem tworzą parę regionu. Podczas fazy zaplanowanej konserwacji platforma Azure aktualizuje tylko maszyny wirtualne w jednym regionie pary regionów. Na przykład podczas aktualizowania maszyny Wirtualnej w północno-środkowej części stanów USA platforma Azure nie aktualizuje żadnej maszyny Wirtualnej w południowo-środkowej części USA w tym samym czasie. Inne regiony, takie jak Europa Północna, mogą być jednak w trakcie konserwacji w tym samym czasie, co region Wschodnie stany USA. Zrozumienie, jak działają pary regionów, może pomóc w lepszej dystrybucji maszyn wirtualnych w różnych regionach. Aby uzyskać więcej informacji, zobacz [pary regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Zestawy dostępności i zestawy skalowania

Podczas wdrażania obciążenia na maszynach wirtualnych platformy Azure, można utworzyć maszyny wirtualne w ramach *zestawu dostępności,* aby zapewnić wysoką dostępność do aplikacji. Za pomocą zestawów dostępności, można upewnić się, że podczas awarii lub konserwacji zdarzenia, które wymagają ponownego uruchomienia, co najmniej jedna maszyna wirtualna jest dostępna.

W ramach zestawu dostępności poszczególne maszyny wirtualne są rozłożone na maksymalnie 20 domen aktualizacji. Podczas zaplanowanej konserwacji tylko jedna domena aktualizacji jest aktualizowana w danym momencie. Domeny aktualizacji nie muszą być aktualizowane sekwencyjnie. 

Zestawy *skalowania* maszyny wirtualnej to zasób obliczeniowy platformy Azure, którego można używać do wdrażania zestawu identycznych maszyn wirtualnych jako pojedynczego zasobu i zarządzania nimi. Zestaw skalowania jest automatycznie wdrażany w uds, takich jak maszyny wirtualne w zestawie dostępności. Podobnie jak w przypadku zestawów dostępności, podczas korzystania z zestawów skalowania tylko jeden UD jest aktualizowany w danym momencie podczas zaplanowanej konserwacji.

Aby uzyskać więcej informacji na temat konfigurowania maszyn wirtualnych w celu zapewnienia wysokiej dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych dla systemu Windows](./windows/manage-availability.md) lub odpowiedni artykuł dla systemu [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Strefy dostępności

Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie platformy Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. 

Strefa dostępności to połączenie domeny błędów i domeny aktualizacji. Jeśli utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie platformy Azure, maszyny wirtualne są skutecznie dystrybuowane między trzema domenami błędów i trzema domenami aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

Każda aktualizacja infrastruktury wprowadza strefę po strefie w obrębie jednego regionu. Ale można mieć wdrożenia dzieje się w strefie 1 i różnych wdrożeń będzie w strefie 2, w tym samym czasie. Wdrożenia nie są wszystkie serializowane. Jednak jedno wdrożenie wprowadza tylko jedną strefę naraz, aby zmniejszyć ryzyko.

## <a name="next-steps"></a>Następne kroki 

Można użyć [interfejsu wiersza polecenia platformy Azure,](maintenance-notifications-cli.md) [programu Azure PowerShell](maintenance-notifications-powershell.md) lub [portalu](maintenance-notifications-portal.md) do zarządzania planowaną konserwacją. 