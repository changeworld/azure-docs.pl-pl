---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/05/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: abf10177f8ce86309043da92d1f2b690775b6d89
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37909933"
---
Platforma Azure jest co pewien czas aktualizowana w celu poprawy niezawodności, wydajności i zabezpieczeń infrastruktury hostowania maszyn wirtualnych. Te aktualizacje obejmują poprawki składników oprogramowania w środowisku hostingu (na przykład systemu operacyjnego, funkcji hypervisor i różnych agentów wdrożonych na hoście), uaktualnienia składników sieciowych i likwidację sprzętu. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na obsługiwanych maszynach wirtualnych. Jednakże istnieją przypadki, w których aktualizacje mają wpływ:

- Jeśli aktualizacja bez ponownego rozruchu jest możliwe, platforma Azure używa zachowywanie konserwacji pamięci można wstrzymać maszyny Wirtualnej, podczas gdy host jest aktualizowany lub maszyna wirtualna zostanie przeniesiona do już zaktualizowanego hosta całkowicie.

- Jeśli przeprowadzenia konserwacji wymagane jest ponowne uruchomienie komputera, otrzymasz powiadomienie o podczas planowanej konserwacji. W takich przypadkach będziesz również mieć przedział czasu, w którym można uruchomić konserwację samodzielnie, w czasie, który Ci odpowiada.

Na tej stronie opisano, jak Microsoft Azure wykonuje oba rodzaje konserwacji. Aby uzyskać więcej informacji o zdarzeniach nieplanowane (awarii), zobacz Zarządzanie dostępnością maszyn wirtualnych [systemu Windows] (../articles/virtual-machines/windows/manage-availability.md) lub [Linux](../articles/virtual-machines/linux/manage-availability.md).

Aplikacje działające na maszynie wirtualnej umożliwia zebranie informacji o nadchodzących aktualizacji za pomocą usługi Azure metadanych dla [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) lub [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md).

Aby uzyskać "porad" informacji o zarządzaniu planowanej konserwacji, zobacz "Obsługa Planned maintenance notifications zaplanowanej" dla [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) lub [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Zachowywanie konserwacji pamięci

Gdy aktualizacje nie wymagają pełnego ponownego uruchomienia systemu, przy zachowaniu mechanizmy obsługi pamięci są używane do ograniczenia wpływu na maszynę wirtualną. Maszyna wirtualna jest wstrzymana na maksymalnie 30 sekund, zachowując pamięci RAM, podczas gdy środowisko hostingu stosuje niezbędne aktualizacje i poprawki lub przenosi maszyny Wirtualnej na hoście już zaktualizowanym. Następnie wznawiać jej działanie maszyny wirtualnej i zegar maszyny wirtualnej jest automatycznie synchronizowany. 

Te operacje konserwacji bez rebootful są domena błędów zastosowane przez domenę błędów, a postęp zostanie zatrzymany, jeśli wszystkie sygnały kondycji ostrzeżenia są odbierane.

Niektóre aplikacje może mieć wpływ na tego rodzaju aktualizacji. Nie można zaprojektować aplikacje, które wykonują w czasie rzeczywistym przetwarzanie zdarzeń, takich jak przesyłania strumieniowego multimediów lub transkodowania ani wysokiej przepływności sieci scenariuszy tolerować 30 wprowadzić jednosekundową przerwę. <!-- sooooo, what should they do? --> W przypadku, gdy maszyna wirtualna jest przenoszona do innego hosta, niektórych poufnych obciążeń zauważyć spadek wydajności nieznaczne w ciągu kilku minut prowadzących do wstrzymanie maszyny wirtualnej. 


## <a name="maintenance-requiring-a-reboot"></a>Konserwacja konieczności ponownego uruchamiania

Jeśli maszyny wirtualne muszą zostać przeprowadzony ponowny rozruch o zaplanowanej konserwacji, otrzymasz powiadomienie z wyprzedzeniem. Planowana konserwacja obejmuje dwa etapy: samoobsługi okna i okna zaplanowanej konserwacji.

**Samoobsługi okna** pozwala na zainicjowanie konserwacji na maszynach wirtualnych. W tym czasie można badać każdej maszyny Wirtualnej, aby wyświetlić ich stan i sprawdzić wynik ostatniego żądania obsługi.

Po uruchomieniu konserwacji samoobsługowej, Twoja maszyna wirtualna zostanie przeniesiona do węzła, który został już zaktualizowany i obsługuje go ponownie. Ponieważ maszyna wirtualna jest ponownie uruchamiana, dysk tymczasowy to dysk utracone i dynamiczne adresy IP skojarzoną z interfejsem sieci wirtualnej zostały zaktualizowane.

Jeśli rozpoczęciem konserwacji samoobsługowej, występuje błąd podczas procesu operacja nie zostanie zatrzymana, maszyna wirtualna nie jest aktualizowany i uzyskasz opcję, aby ponowić próbę konserwacji samoobsługowej. 

Po upływie okna samoobsługi **zaplanowanego okna obsługi** rozpoczyna się. Tym przedziale czasu możesz nadal badać dla okna obsługi, ale nie będzie można uruchomić konserwację samodzielnie.

Uzyskać informacji o zarządzaniu konserwacji wymagające ponownego uruchomienia systemu, zobacz "Obsługa Planned maintenance notifications zaplanowanej" dla [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) lub [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Zagadnienia dotyczące dostępności podczas zaplanowanej konserwacji 

Jeśli zdecydujesz się zaczekać okna zaplanowanej konserwacji, istnieje kilka kwestii, które należy wziąć pod uwagę utrzymywania najwyższą dostępność maszyn wirtualnych. 

#### <a name="paired-regions"></a>Sparowane regiony

Każdy region platformy Azure jest powiązany z innym regionem w obrębie tego samego obszaru geograficznego, ze sobą ich wprowadzić parę regionalną. Podczas zaplanowanej konserwacji platformy Azure będzie aktualizować jedynie te maszyny wirtualne w jednym regionie parze regionów. Na przykład podczas aktualizowania maszyn wirtualnych w regionie Północno-środkowe stany USA platforma Azure nie będzie jednocześnie przeprowadzać aktualizacji żadnych maszyn wirtualnych w regionie Południowo-środkowe stany USA. Inne regiony, takie jak Europa Północna, mogą być jednak w trakcie konserwacji w tym samym czasie, co region Wschodnie stany USA. Zrozumienie, jak działają pary regionów mogą ułatwić lepiej dystrybuować maszyn wirtualnych między regionami. Aby uzyskać więcej informacji, zobacz [par regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Zestawy dostępności i zestawów skalowania

W przypadku wdrażania obciążeń na maszynach wirtualnych platformy Azure, można utworzyć maszyny wirtualne w zestaw dostępności, aby zapewnić wysoką dostępność aplikacji. Dzięki temu podczas awarii lub zdarzenia konserwacji rebootful co najmniej jednej maszyny wirtualnej są dostępne.

W zestawie dostępności poszczególne maszyny wirtualne są rozproszone między maksymalnie 20 domen aktualizacji (domenami aktualizacji). Podczas zaplanowanej konserwacji w dowolnym momencie czy dotyczy to tylko z domeny jedną aktualizację. Należy pamiętać, że kolejność domen aktualizacji wpływem nie zawsze odbywa się po kolei. 

Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrożenie zestawu identycznych maszyn wirtualnych, jako pojedynczy zasób oraz zarządzanie. Zestaw skalowania automatycznie jest wdrażana w domenach aktualizacji, takich jak maszyny wirtualne w zestawie dostępności. Podobnie jak z zestawami dostępności za pomocą zestawów skalowania tylko jedną aktualizację domeny jest wpływ w dowolnym momencie podczas zaplanowanej konserwacji.

Aby uzyskać więcej informacji na temat konfigurowania maszyn wirtualnych w celu zapewnienia wysokiej dostępności, zobacz Zarządzanie dostępnością maszyn wirtualnych dla [Windows](../articles/virtual-machines/windows/manage-availability.md) lub [Linux](../articles/virtual-machines/linux/manage-availability.md).
