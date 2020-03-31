---
title: Omówienie obciążeń red hat na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o ofertach produktów Red Hat dostępnych na platformie Azure.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970171"
---
# <a name="red-hat-workloads-on-azure"></a>Obciążenia red hat na platformie Azure

Obciążenia Red Hat są obsługiwane za pośrednictwem różnych ofert na platformie Azure. Obrazy Red Hat Enterprise Linux (RHEL) znajdują się w centrum obciążeń RHEL, podobnie jak red hat update infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Obrazy Red Hat Enterprise Linux

Platforma Azure oferuje szeroką ofertę obrazów RHEL na platformie Azure. Te obrazy są udostępniane za pośrednictwem dwóch różnych modeli licencjonowania: pay-as-you-go i bring-your-own-subscription (BYOS). Nowe obrazy RHEL na platformie Azure są publikowane, gdy nowe wersje RHEL są wydawane i aktualizowane w całym cyklu życia, w razie potrzeby.

### <a name="pay-as-you-go-images"></a>Obrazy płatności zgodnie z rzeczywistymi płatnościami

Platforma Azure oferuje wiele obrazów rhel pay-as-you-go. Te obrazy są prawidłowo uprawnione do RHEL i są dołączone do źródła aktualizacji (Red Hat Update Infrastructure). Te obrazy pobierają opłatę premium za uprawnienia i aktualizacje RHEL. Warianty obrazów rhel pay-as-you-go obejmują:

* Standardowy RHEL.
* RHEL dla SAP.
* RHEL dla SAP z usługami o wysokiej dostępności i aktualizacji.

Możesz użyć obrazów płatności zgodnie z rzeczywistym użyciem, jeśli nie chcesz martwić się o płacenie oddzielnie za odpowiednią liczbę subskrypcji.

### <a name="red-hat-gold-images"></a>Red Hat Gold Obrazy

Platforma Azure oferuje również`rhel-byos`obrazy Red Hat Gold ( ). Te obrazy mogą być przydatne dla klientów, którzy mają istniejące subskrypcje Red Hat i chcą ich używać na platformie Azure. Musisz włączyć istniejące subskrypcje Red Hat dla red hat cloud access, zanim będzie można ich używać na platformie Azure. Dostęp do tych obrazów jest przyznawany automatycznie, gdy subskrypcje Red Hat są włączone dla usługi Cloud Access i spełniają wymagania kwalifikacyjne. Korzystanie z tych obrazów pozwala klientowi uniknąć podwójnego fakturowania, które mogą być poniesione przy użyciu obrazów płatności zgodnie z rzeczywistym użyciem.
* Dowiedz się, jak [włączyć subskrypcje Red Hat dla usługi Cloud Access za pomocą platformy Azure.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* Dowiedz się, jak [znaleźć obrazy Red Hat Gold w witrynie Azure portal, platformie Azure CLI lub powiększącym programie PowerShell.](./byos.md)

> [!NOTE]
> Podwójne rozliczenie jest naliczane, gdy użytkownik płaci dwa razy za subskrypcje RHEL. Ten scenariusz zwykle dzieje się, gdy klient używa Red Hat Subscription-Manager do dołączenia uprawnienia na maszynie wirtualnej RHEL pay-as-you-go. Na przykład klient, który używa Menedżera subskrypcji do dołączenia uprawnienia do pakietów SAP na obrazie płatności zgodnie z rzeczywistym użyciem RHEL, jest pośrednio dwukrotnie rozliczany, ponieważ płaci dwa razy za RHEL. Płacą raz za pośrednictwem opłaty premium zgodnie z rzeczywistyma posuwem, a raz za pośrednictwem subskrypcji SAP. Ten scenariusz nie zdarza się użytkownikom obrazów BYOS.

### <a name="generation-2-images"></a>Obrazy generacji 2

Maszyny wirtualne generacji 2 zapewniają kilka nowszych funkcji w porównaniu do maszyn wirtualnych generacji 1. Aby uzyskać więcej informacji, zobacz [dokumentację generacji 2](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2). Kluczową różnicą w porównaniu z perspektywą obrazu RHEL jest to, że maszyny wirtualne generacji 2 używają UEFI zamiast interfejsu oprogramowania układowego BIOS. Używają również tabeli partycji GUID (GPT) zamiast głównego rekordu rozruchowego (MBR) w czasie rozruchu. Użycie GPT pozwala między innymi na rozmiary dysków systemu operacyjnego większe niż 2 TB. Ponadto maszyny [wirtualne z serii Mv2](../../mv2-series.md) działają tylko na obrazach generacji 2.

Obrazy RHEL generacji 2 są dostępne w portalu Azure Marketplace. Poszukaj "gen2" w jednostce SKU obrazu na liście wszystkich obrazów, która pojawia się podczas korzystania z interfejsu wiersza polecenia platformy Azure. Przejdź do karty **Zaawansowane** w procesie wdrażania maszyny Wirtualnej, aby wdrożyć maszynę wirtualną generacji 2.

## <a name="red-hat-update-infrastructure"></a>Infrastruktura aktualizacji systemu Red Hat

Platforma Azure udostępnia infrastrukturę red hat update tylko dla maszyn wirtualnych RHEL zgodnie z rzeczywistym po łączye. RHUI jest skutecznie dublowanie red hat cdn, ale jest dostępny tylko dla platformy Azure pay-as-you-go maszyn wirtualnych RHEL. Masz dostęp do odpowiednich pakietów w zależności od tego, który obraz RHEL został wdrożony. Na przykład obraz RHEL dla SAP ma dostęp do pakietów SAP oprócz podstawowych pakietów RHEL.

### <a name="rhui-update-behavior"></a>Zachowanie aktualizacji RHUI

Obrazy RHEL podłączone domyślnie do aktualizacji RHUI do `yum update` najnowszej wersji pomocniczej RHEL po uruchomieniu. To zachowanie oznacza, że maszyna wirtualna RHEL 7.4 może zostać uaktualniona do RHEL 7.7, jeśli `yum update` operacja jest na nim uruchamiana. To zachowanie jest projektowane dla RHUI. Aby załagodzić to zachowanie uaktualnienia, przełącz się z zwykłych repozytoriów RHEL na [repozytoria rozszerzonej obsługi aktualizacji](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [obrazach RHEL na platformie Azure](./redhat-images.md).
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md).
* Dowiedz się więcej o [ofercie`rhel-byos`Red Hat Gold Image ( )](./byos.md).
