---
title: Obciążenia Red Hat na platformie Azure — omówienie | Microsoft Docs
description: Dowiedz się więcej o ofercie produktów Red Hat dostępnych na platformie Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: df787d4102752bdf61e30bc00d0d08307ac12319
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473164"
---
# <a name="red-hat-workloads-on-azure"></a>Obciążenia Red Hat na platformie Azure
Obciążenia Red Hat są obsługiwane przez różne oferty na platformie Azure. Obrazy Red Hat Enterprise Linux (RHEL) są podstawą obciążeń RHEL, podobnie jak infrastruktura aktualizacji Red Hat (RHUI).

## <a name="red-hat-enterprise-linux-rhel-images"></a>Obrazy Red Hat Enterprise Linux (RHEL)
Platforma Azure oferuje szeroką ofertę obrazów RHEL na platformie Azure. Te obrazy są udostępniane przez dwa różne modele licencjonowania: płatność zgodnie z rzeczywistym użyciem (PAYG) i przenoszenie własnych subskrypcji (BYOS). Nowe obrazy RHEL na platformie Azure są publikowane, gdy nowe wersje RHEL zostały wydane i zaktualizowane w całym cyklu życia, zgodnie z potrzebami.

### <a name="pay-as-you-go-payg-images"></a>Obrazy z opcją płatność zgodnie z rzeczywistym użyciem
Platforma Azure oferuje różne obrazy RHEL PAYG. Te obrazy są prawidłowo uprawnione do RHEL i są dołączone do źródła aktualizacji (infrastruktura aktualizacji Red Hat). Te obrazy spowodują naliczenie opłaty za usługę Premium za RHEL i aktualizacje. Warianty obrazu RHEL PAYG obejmują:
* Standardowa RHEL
* RHEL dla SAP
* RHEL for SAP z usługami wysokiej dostępności i aktualizacji.

Możesz chcieć użyć obrazów PAYG, jeśli nie chcesz martwić się o płatność za odpowiednią liczbę subskrypcji.

### <a name="bring-your-own-subscription-byos-images"></a>Przenoszenie obrazów własnych subskrypcji (BYOS)
Platforma Azure oferuje również obrazy RHEL BYOS. Te obrazy mogą być przydatne dla klientów, którzy mają istniejące subskrypcje firmy Red Hat i chcą korzystać z nich na platformie Azure. Musisz przekonwertować istniejące subskrypcje na subskrypcje dostępu w chmurze, aby można było używać ich na platformie Azure. Dostęp do tych obrazów jest udzielany tylko wtedy, gdy Red Hat sprawdzi, czy masz wystarczające subskrypcje dostępu do chmury. Użycie tych obrazów umożliwia klientowi uniknięcie podwójnego rozliczania, które mogą być naliczane z używania obrazów PAYG. W [tym miejscu](https://aka.ms/rhel-byos)możesz zażądać dostępu do obrazów BYOS.

> [!NOTE]
> Uwaga dotycząca podwójnego rozliczania: podwójne rozliczenie jest naliczane, gdy użytkownik płaci dwa razy dla subskrypcji RHEL. Dzieje się tak zwykle, gdy klient korzysta z Menedżera subskrypcji w celu dołączenia uprawnienia do maszyny wirtualnej RHEL PAYG. Na przykład klient korzystający z Menedżera subskrypcji w celu dołączenia uprawnień do pakietów SAP w obrazie RHEL PAYG zostanie policzony dwukrotnie, ponieważ będzie obciążany dwa razy dla RHEL — za pośrednictwem opłaty za subskrypcję w wersji Premium, a raz za pośrednictwem subskrypcji SAP. Nie będzie to miało miejsce w przypadku użytkowników obrazu BYOS.

## <a name="red-hat-update-infrastructure-rhui"></a>Infrastruktura aktualizacji Red Hat (RHUI)
Platforma Azure udostępnia infrastrukturę aktualizacji firmy Red Hat tylko dla maszyn wirtualnych RHEL PAYG (VM). RHUI jest skutecznym dublowaniem Red Hat sieci CDN, ale jest dostępny tylko dla maszyn wirtualnych RHEL platformy Azure. Będziesz mieć dostęp do odpowiednich pakietów w zależności od tego, który obraz RHEL został wdrożony. Na przykład obraz RHEL dla oprogramowania SAP będzie miał dostęp do pakietów SAP oprócz podstawowych pakietów RHEL.

### <a name="rhui-update-behavior"></a>Zachowanie aktualizacji RHUI
Po uruchomieniu `yum update` obrazy RHEL połączone z programem RHUI domyślnie zostaną zaktualizowane do najnowszej wersji pomocniczej RHEL. Takie zachowanie oznacza, że maszyna wirtualna z systemem RHEL 7,4 może zostać uaktualniona do RHEL 7,7, jeśli na niej zostanie uruchomiona operacja `yum update`. Jest to zaprojektowane z RHUI. Takie zachowanie uaktualnienia można jednak ograniczyć, przechodząc od zwykłych repozytoriów RHEL do [repozytoriów z obsługą aktualizacji rozszerzonych (EUS)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obrazach RHEL na platformie Azure](./redhat-images.md)
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md)
* Dowiedz się więcej o [ofercie RHEL BYOS](./redhat-byos.md)