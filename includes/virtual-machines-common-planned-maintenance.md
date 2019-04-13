---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c26c037455b6d14a906894ec39bf46630826950b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551485"
---
Azure okresowo aktualizuje platformy w celu poprawy niezawodności, wydajności i bezpieczeństwa infrastruktury hosta dla maszyn wirtualnych. Aktualizacje rozwiązania z zakresu od stosowania poprawek składnikami oprogramowania w środowisku hostingu uaktualniania składników sieciowych do likwidacji sprzętu. Większość tych aktualizacji nie mają wpływu na obsługiwanych maszynach wirtualnych. Jednak istnieją przypadki, w których aktualizacje mają wpływ i Azure wybiera najmniej udane metody aktualizacji:

- Jeśli aktualizacja rebootful nie jest możliwe, maszyna wirtualna jest wstrzymana, gdy host jest aktualizowany lub znajduje się na żywo zmigrowana do już zaktualizowanego hosta.

- Jeśli przeprowadzenia konserwacji wymagane jest ponowne uruchomienie komputera, otrzymasz powiadomienie o podczas planowanej konserwacji. Platforma Azure umożliwi także przedział czasu, w którym można uruchomić konserwację samodzielnie, w czasie, który Ci odpowiada. Azure inwestuje w technologie, aby zmniejszyć przypadków, gdy maszyny wirtualne mają zostać uruchomiony ponownie w celu przeprowadzenia konserwacji planowanej platformy. 

Na tej stronie opisano, jak platforma Azure przeprowadza oba rodzaje konserwacji. Aby dowiedzieć się więcej o nieplanowanych zdarzeń (awarii), zobacz Zarządzanie dostępnością maszyn wirtualnych w celu [Windows](../articles/virtual-machines/windows/manage-availability.md) lub [Linux](../articles/virtual-machines/linux/manage-availability.md).

W maszynie Wirtualnej powiadomienie o zbliżającej się konserwacji można uzyskać za pomocą zaplanowanych zdarzeń dla [Windows](../articles/virtual-machines/windows/scheduled-events.md) lub [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Aby uzyskać "porad" informacji o zarządzaniu planowanej konserwacji, zobacz "Obsługa Planned maintenance notifications zaplanowanej" dla [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) lub [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Konserwacji nie wymaga ponownego uruchomienia

Celem większości konserwacji, które nie wymagają ponownego uruchomienia jest mniejsza niż 10 sekund wstrzymać maszyny wirtualnej. W niektórych przypadkach pamięci, zachowywanie konserwacji mechanizmów są używane, który wstrzymuje maszynę Wirtualną do 30 sekund i zachowuje pamięci RAM. Następnie wznawiać jej działanie maszyny wirtualnej i zegar maszyny wirtualnej jest automatycznie synchronizowany. Platforma Azure jest coraz większym stopniu przy użyciu technologii migracji na żywo i poprawianie pamięci zachowywanie mechanizm obsługi, aby zmniejszyć czas trwania pauzy.

Te operacje konserwacji bez rebootful są domena błędów zastosowane przez domenę błędów, a postęp zostanie zatrzymany, jeśli wszystkie sygnały kondycji ostrzeżenia są odbierane. 

Niektóre aplikacje może mieć wpływ na tego rodzaju aktualizacji. W przypadku, gdy maszyna wirtualna znajduje się na żywo zmigrowana do innego hosta, niektórych poufnych obciążeń zauważyć spadek wydajności nieznaczne w ciągu kilku minut prowadzących do wstrzymanie maszyny Wirtualnej. Takie aplikacje mogą korzystać z przy użyciu zaplanowanych zdarzeń dla [Windows](../articles/virtual-machines/windows/scheduled-events.md) lub [Linux](../articles/virtual-machines/linux/scheduled-events.md) dotyczące przygotowania do obsługi maszyn wirtualnych i nie mają wpływu podczas konserwacji platformy Azure. Azure pracuje również funkcje kontroli konserwacji na potrzeby takich aplikacji niezwykle ważne. 


## <a name="maintenance-requiring-a-reboot"></a>Konserwacja konieczności ponownego uruchamiania

W rzadkich przypadkach gdy maszyny wirtualne muszą zostać przeprowadzony ponowny rozruch o zaplanowanej konserwacji, otrzymasz powiadomienie z wyprzedzeniem. Planowana konserwacja obejmuje dwa etapy: samoobsługi okna i okna zaplanowanej konserwacji.

**Samoobsługi okna** umożliwia uruchamianie konserwacji na maszynach wirtualnych. W tym czasie można badać każdej maszyny Wirtualnej, aby wyświetlić ich stan i sprawdzić wynik ostatniego żądania obsługi.

Po uruchomieniu konserwacji samoobsługowej, maszyna wirtualna jest ponownie wdrożyć już zaktualizowany węzeł. Ponieważ maszyna wirtualna jest ponownie uruchamiana, dysk tymczasowy to dysk utracone i dynamiczne adresy IP skojarzoną z interfejsem sieci wirtualnej zostały zaktualizowane.

Jeśli rozpoczęciem konserwacji samoobsługowej, występuje błąd podczas procesu operacja nie zostanie zatrzymana, maszyna wirtualna nie jest aktualizowany i uzyskasz opcję, aby ponowić próbę konserwacji samoobsługowej. 

Po upływie okna samoobsługi **zaplanowanego okna obsługi** rozpoczyna się. W tym przedziale czasu mogą nadal wyszukiwać dla okna obsługi, ale nie można uruchomić konserwację samodzielnie.

Uzyskać informacji o zarządzaniu konserwacji wymagające ponownego uruchomienia systemu, zobacz "Obsługa Planned maintenance notifications zaplanowanej" dla [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) lub [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Zagadnienia dotyczące dostępności podczas zaplanowanej konserwacji 

Jeśli zdecydujesz się zaczekać okna zaplanowanej konserwacji, istnieje kilka kwestii, które należy wziąć pod uwagę utrzymywania najwyższą dostępność maszyn wirtualnych. 

#### <a name="paired-regions"></a>Sparowane regiony

Każdy region platformy Azure jest powiązany z innym regionem w obrębie tego samego obszaru geograficznego, a wspólnie umożliwiają parę regionalną. W fazie zaplanowanej konserwacji platformy Azure będzie aktualizować jedynie te maszyny wirtualne w jednym regionie parze regionów. Na przykład podczas aktualizowania maszyny Wirtualnej w północno-środkowe stany USA, platforma Azure nie będzie zaktualizować dowolnej maszyny Wirtualnej w południowo-środkowe stany USA w tym samym czasie. Inne regiony, takie jak Europa Północna, mogą być jednak w trakcie konserwacji w tym samym czasie, co region Wschodnie stany USA. Zrozumienie, jak działają pary regionów mogą ułatwić lepiej dystrybuować maszyn wirtualnych między regionami. Aby uzyskać więcej informacji, zobacz [par regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Zestawy dostępności i zestawów skalowania

W przypadku wdrażania obciążeń na maszynach wirtualnych platformy Azure, można utworzyć maszyny wirtualne w zestaw dostępności, aby zapewnić wysoką dostępność aplikacji. Dzięki temu podczas awarii lub zdarzenia konserwacji rebootful co najmniej jednej maszyny Wirtualnej są dostępne.

W zestawie dostępności poszczególne maszyny wirtualne są rozproszone między maksymalnie 20 domen aktualizacji (domenami aktualizacji). Podczas zaplanowanej konserwacji tylko jedną aktualizację domeny jest aktualizowana w danym momencie. Domeny aktualizacji aktualizowana niekoniecznie nie będzie sekwencyjnie. 

Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrożenie zestawu identycznych maszyn wirtualnych, jako pojedynczy zasób oraz zarządzanie. Zestaw skalowania automatycznie jest wdrażana w domenach aktualizacji, takich jak maszyny wirtualne w zestawie dostępności. Podobnie jak z zestawami dostępności za pomocą zestawów skalowania tylko jedną aktualizację domeny jest aktualizowana w dowolnym momencie podczas zaplanowanej konserwacji.

Aby uzyskać więcej informacji na temat konfigurowania maszyn wirtualnych wysokiej dostępności, zobacz Zarządzanie dostępnością maszyn wirtualnych dla [Windows](../articles/virtual-machines/windows/manage-availability.md) lub [Linux](../articles/virtual-machines/linux/manage-availability.md).
