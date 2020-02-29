---
title: Obciążenia Red Hat na platformie Azure — omówienie | Microsoft Docs
description: Dowiedz się więcej o ofercie produktów Red Hat dostępnych na platformie Azure
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 424ef37885d685829a11d1864a72b043a562231c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920556"
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

### <a name="red-hat-gold-images-rhel-byos"></a>Obrazy Red Hat Gold (`rhel-byos`)
Platforma Azure oferuje również obrazy Red Hat Gold. Te obrazy mogą być przydatne dla klientów, którzy mają istniejące subskrypcje firmy Red Hat i chcą korzystać z nich na platformie Azure. Musisz włączyć istniejące subskrypcje Red Hat dla usługi Red Hat Cloud Access, aby móc używać ich na platformie Azure. Dostęp do tych obrazów jest udzielany automatycznie po włączeniu subskrypcji Red Hat w celu uzyskania dostępu do chmury i spełnienia wymagań dotyczących uprawnień. Użycie tych obrazów umożliwia klientowi uniknięcie podwójnego rozliczania, które mogą być naliczane z używania obrazów PAYG.
* [Dowiedz się, jak włączyć subskrypcję Red Hat dla dostępu do chmury przy użyciu platformy Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Dowiedz się, jak znaleźć obrazy Red Hat Gold w Azure Portal, CLI lub cmdlet programu PowerShell](./byos.md)

> [!NOTE]
> Uwaga dotycząca podwójnego rozliczania: podwójne rozliczenie jest naliczane, gdy użytkownik płaci dwa razy dla subskrypcji RHEL. Dzieje się tak zwykle, gdy klient korzysta z Menedżera subskrypcji w celu dołączenia uprawnienia do maszyny wirtualnej RHEL PAYG. Na przykład klient korzystający z Menedżera subskrypcji w celu dołączenia uprawnień do pakietów SAP w obrazie RHEL PAYG zostanie policzony dwukrotnie, ponieważ będzie obciążany dwa razy dla RHEL — za pośrednictwem opłaty za subskrypcję w wersji Premium, a raz za pośrednictwem subskrypcji SAP. Nie będzie to miało miejsce w przypadku użytkowników obrazu BYOS.

### <a name="generation-2-images"></a>Obrazy generacji 2
Maszyny wirtualne 2. generacji zapewniają nowe funkcje w porównaniu z maszynami wirtualnymi generacji 1. Szczegółowe informacje znajdują się w [dokumentacji generacji 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). Różnica klucza z perspektywy obrazu RHEL polega na tym, że maszyny wirtualne 2. generacji używają interfejsu UEFI zamiast z poziomu oprogramowania układowego BIOS i używają tabeli partycji GUID (GPT) zamiast głównego rekordu rozruchowego (MBR) w czasie rozruchu. Pozwala to na, między innymi, rozmiar dysku systemu operacyjnego większy niż 2 TB. Ponadto [maszyny wirtualne z serii Mv2](../../mv2-series.md) są uruchamiane tylko na obrazach generacji 2.

Obrazy generacji RHEL 2 są dostępne w portalu Marketplace. Poszukaj pozycji "Gen2" w jednostce SKU obrazu podczas wyświetlania listy wszystkich obrazów przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie przejdź do karty "Zaawansowane" w procesie wdrażania maszyny wirtualnej, aby wdrożyć maszynę wirtualną generacji 2.

## <a name="red-hat-update-infrastructure-rhui"></a>Infrastruktura aktualizacji Red Hat (RHUI)
Platforma Azure udostępnia infrastrukturę aktualizacji firmy Red Hat tylko dla maszyn wirtualnych RHEL PAYG (VM). RHUI jest skutecznym dublowaniem Red Hat sieci CDN, ale jest dostępny tylko dla maszyn wirtualnych RHEL platformy Azure. Będziesz mieć dostęp do odpowiednich pakietów w zależności od tego, który obraz RHEL został wdrożony. Na przykład obraz RHEL dla oprogramowania SAP będzie miał dostęp do pakietów SAP oprócz podstawowych pakietów RHEL.

### <a name="rhui-update-behavior"></a>Zachowanie aktualizacji RHUI
Po uruchomieniu `yum update` obrazy RHEL połączone z programem RHUI domyślnie zostaną zaktualizowane do najnowszej wersji pomocniczej RHEL. Takie zachowanie oznacza, że maszyna wirtualna z systemem RHEL 7,4 może zostać uaktualniona do RHEL 7,7, jeśli na niej zostanie uruchomiona operacja `yum update`. Jest to zaprojektowane z RHUI. Takie zachowanie uaktualnienia można jednak ograniczyć, przechodząc od zwykłych repozytoriów RHEL do [repozytoriów z obsługą aktualizacji rozszerzonych (EUS)](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obrazach RHEL na platformie Azure](./redhat-images.md)
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md)
* Dowiedz się więcej na temat [oferty Red Hat Gold Image (`rhel-byos`)](./byos.md)
