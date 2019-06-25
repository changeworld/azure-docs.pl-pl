---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183211"
---
Azure okresowo aktualizuje swoją platformę w celu poprawy niezawodności, wydajności i bezpieczeństwa infrastruktury hosta dla maszyn wirtualnych. Celem tych zakresów aktualizacje z poprawek składnikami oprogramowania w środowisku hostingu uaktualnianie składniki sieciowe lub likwidowanie sprzętu. 

Aktualizacje rzadko wpływają na obsługiwanych maszynach wirtualnych. Gdy aktualizacje efektu, Azure wybiera najmniej udane metody aktualizacji:

- Jeśli aktualizacja nie wymaga ponownego uruchomienia systemu, maszyna wirtualna jest wstrzymana, gdy host jest aktualizowany, lub maszyna wirtualna znajduje się migracji na żywo do już zaktualizowanego hosta.

- W przypadku przeprowadzenia konserwacji wymagane jest ponowne uruchomienie komputera, otrzymasz powiadomienie o planowanej konserwacji. System Azure oferuje także przedział czasu, w którym możesz uruchomić konserwację samodzielnie, w czasie, który Ci odpowiada. Okno samodzielna konserwacja zwykle wynosi 30 dni, chyba że pilnych konserwacji. Azure inwestuje w technologie, aby zmniejszyć liczbę przypadków, w których konserwacji planowanej platformy wymaga maszyn wirtualnych, należy ponownie uruchomić. 

Na tej stronie opisano, jak platforma Azure przeprowadza oba rodzaje konserwacji. Aby uzyskać więcej informacji na temat nieplanowanych zdarzeń (awarii), zobacz [Zarządzanie dostępnością maszyn wirtualnych dla Windows](../articles/virtual-machines/windows/manage-availability.md) lub odpowiedni artykuł dotyczący [Linux](../articles/virtual-machines/linux/manage-availability.md).

Na maszynie wirtualnej, można otrzymywać powiadomienia o zbliżającej się konserwacji przez [przy użyciu zaplanowanych zdarzeń dla Windows](../articles/virtual-machines/windows/scheduled-events.md) lub [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Aby uzyskać instrukcje na temat zarządzania planowanej konserwacji, zobacz [obsługi Planned maintenance notifications zaplanowanej dla systemu Linux](../articles/virtual-machines/linux/maintenance-notifications.md) lub odpowiedni artykuł dotyczący [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Konserwacja, która nie wymaga ponownego uruchomienia

Jak wspomniano wcześniej, większość aktualizacji platformy nie mają wpływu na maszyny wirtualne klientów. Podczas aktualizacji bez zakłócania normalnej nie jest możliwe, Azure wybiera mechanizm aktualizacji, który jest co najmniej impactful na maszynach wirtualnych klientów. 

Większość wartość różną od zera wpływ konserwacji wstrzymuje maszyny Wirtualnej przez mniej niż 10 sekund. W niektórych przypadkach platforma Azure używa chroniącej pamięć mechanizmy obsługi. Te mechanizmy wstrzymanie maszyny Wirtualnej do 30 sekund i Zachowaj pamięci RAM. Następnie wznawiać jej działanie maszyny Wirtualnej, a jego zegara jest automatycznie synchronizowany. 

Konserwacja chroniącej pamięć działa w przypadku ponad 90% maszyn wirtualnych platformy Azure. To nie zadziała serii G, M, N i H. Azure coraz większym stopniu korzystają z technologii migracji na żywo i zwiększa chroniącej pamięć mechanizmy obsługi, aby zmniejszyć czas trwania wstrzymania.  

Te operacje konserwacji, które nie wymagają ponownego uruchomienia komputera są stosowane jednej domenie błędów w danym momencie. Zatrzymaj ich, jeśli otrzymają wszystkie sygnały kondycji ostrzeżenia. 

Tego rodzaju aktualizacji może wpłynąć na niektóre aplikacje. Gdy maszyna wirtualna znajduje się migracji na żywo do innego hosta, niektórych poufne obciążeń może wyświetlać degradacji niewielkim wzroście wydajności w ciągu kilku minut prowadzących do wstrzymanie maszyny Wirtualnej. Aby przygotować się do obsługi maszyn wirtualnych i ograniczyć wpływ podczas konserwacji platformy Azure, wypróbuj [przy użyciu zaplanowanych zdarzeń dla Windows](../articles/virtual-machines/windows/scheduled-events.md) lub [Linux](../articles/virtual-machines/linux/scheduled-events.md) na potrzeby takich aplikacji. Azure pracuje w funkcje obsługi sterowania dla tych aplikacji zawierających poufne dane. 

### <a name="live-migration"></a>Migracja na żywo

Migracja na żywo jest operacją, która nie wymaga ponownego uruchomienia systemu i zachowują o pamięci dla maszyny Wirtualnej. Powoduje wstrzymania lub blokowanie, trwające zwykle nie więcej niż 5 sekund. Z wyjątkiem G serii M, N i H, cała infrastruktura jako usługa (IaaS) maszyn wirtualnych, kwalifikują się do migracji na żywo. Uprawnione maszyny wirtualne reprezentują ponad 90% maszyn wirtualnych IaaS, które są wdrażane w floty platformy Azure. 

Platforma Azure rozpoczyna się migracji na żywo w następujących scenariuszach:
- Planowana konserwacja
- Awaria sprzętowa
- Optymalizacje alokacji

Niektóre scenariusze planowaną konserwację używanie migracji na żywo i zaplanowanych zdarzeń można użyć tylko wiedzieć o wcześniej na żywo rozpocznie się operacji migracji.

Migracja na żywo można również przenieść maszyny wirtualne, gdy algorytmów uczenia maszynowego Azure przewidzieć zbliżającego się awarii sprzętu lub gdy chcesz zoptymalizować alokacji maszyny Wirtualnej. Aby uzyskać więcej informacji na temat modelowanie predykcyjne, który wykryje wystąpienia obniżonej wydajności sprzętu, zobacz [odporności poprawy maszyny Wirtualnej platformy Azure przy użyciu uczenia maszynowego predykcyjnych i migracji na żywo](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Migracja na żywo powiadomienia są wyświetlane w witrynie Azure portal w Monitor i kondycji usług dziennikach również jak Scheduled Events, korzystając z tych usług.

## <a name="maintenance-that-requires-a-reboot"></a>Konserwacji, co wymaga ponownego uruchomienia

W rzadkich przypadkach, w której maszyny wirtualne muszą zostać przeprowadzony ponowny rozruch o zaplanowanej konserwacji zostanie wyświetlone powiadomienie z wyprzedzeniem. Planowana konserwacja ma dwie fazy: fazę samoobsługi i fazy zaplanowanej konserwacji.

Podczas *samoobsługi fazy*uruchomić konserwację na maszynach wirtualnych, które zwykle trwa cztery tygodnie. W ramach samoobsługi można tworzyć zapytania każdej maszyny Wirtualnej, aby wyświetlić stan i wynik ostatniego żądania obsługi.

Po uruchomieniu konserwacji samoobsługowej, maszyna wirtualna jest ponownie wdrożyć już zaktualizowany węzeł. Ponieważ maszyna wirtualna jest ponownie uruchamiana, dysk tymczasowy to dysk utracone i dynamiczne adresy IP skojarzoną z interfejsem sieci wirtualnej zostały zaktualizowane.

Jeśli błąd pojawia się podczas konserwacji samoobsługowej, operacja zostanie ono zatrzymane, maszyna wirtualna nie jest uaktualniany, a uzyskasz opcję, aby ponowić próbę konserwacji samoobsługowej. 

Po zakończeniu fazy samoobsługi, *fazy zaplanowanej konserwacji* rozpoczyna się. W tej fazie mogą nadal wyszukiwać w fazie konserwacji, ale konserwacji nie można uruchomić samodzielnie.

Aby uzyskać więcej informacji na temat zarządzania konserwacji, co wymaga ponownego uruchomienia, zobacz [obsługi Planned maintenance notifications zaplanowanej dla systemu Linux](../articles/virtual-machines/linux/maintenance-notifications.md) lub odpowiedni artykuł dotyczący [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Zagadnienia dotyczące dostępności podczas zaplanowanej konserwacji 

Jeśli zdecydujesz się zaczekać fazy zaplanowanej konserwacji, istnieje kilka kwestii, które należy wziąć pod uwagę do utrzymania najwyższą dostępność maszyn wirtualnych. 

#### <a name="paired-regions"></a>Sparowane regiony

Każdy region platformy Azure jest powiązany z innym regionem w obrębie tego samego sąsiedztwa geograficznych. Razem dokonają parze regionów. Podczas fazy zaplanowanej konserwacji Azure aktualizuje tylko maszyny wirtualne w jednym regionie parze regionów. Na przykład podczas aktualizowania maszyny Wirtualnej w północno-środkowe stany USA, Azure nie powoduje aktualizacji dowolnej maszyny Wirtualnej w południowo-środkowe stany USA, w tym samym czasie. Inne regiony, takie jak Europa Północna, mogą być jednak w trakcie konserwacji w tym samym czasie, co region Wschodnie stany USA. Zrozumienie, jak działają pary regionów mogą ułatwić lepiej dystrybuować maszyn wirtualnych między regionami. Aby uzyskać więcej informacji, zobacz [par regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Zestawy dostępności i zestawów skalowania

W przypadku wdrażania obciążeń na maszynach wirtualnych platformy Azure, można utworzyć maszyny wirtualne w ramach *zestaw dostępności* umożliwiające uzyskanie wysokiej dostępności do Twojej aplikacji. Przy użyciu zestawów dostępności, można zagwarantować, że podczas przestoju lub obsługi zdarzeń, które wymagają ponownego uruchomienia systemu, co najmniej jedna maszyna wirtualna jest dostępna.

W zestawie dostępności poszczególne maszyny wirtualne są rozproszone między maksymalnie 20 domen aktualizacji (domenami aktualizacji). Podczas zaplanowanej konserwacji tylko jeden UD jest aktualizowana w danym momencie. Domenami aktualizacji niekoniecznie nie zostaną zaktualizowane sekwencyjnie. 

Maszyna wirtualna *zestawów skalowania* są usługi Azure compute zasób, który umożliwia wdrożenie zestawu identycznych maszyn wirtualnych, jako pojedynczy zasób oraz zarządzanie. Zestaw skalowania jest automatycznie wdrażane między domenami aktualizacji, takich jak maszyny wirtualne w zestawie dostępności. Jako z zestawami dostępności, korzystając z zestawów skalowania, tylko jeden UD jest aktualizowana w dowolnym momencie podczas zaplanowanej konserwacji.

Aby uzyskać więcej informacji na temat konfigurowania maszyn wirtualnych wysokiej dostępności, zobacz [Zarządzanie dostępnością usługi maszyny wirtualne dla Windows](../articles/virtual-machines/windows/manage-availability.md) lub odpowiedni artykuł dotyczący [Linux](../articles/virtual-machines/linux/manage-availability.md).
