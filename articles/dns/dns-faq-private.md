---
title: Usługa Azure prywatne DNS — często zadawane pytania
description: Często zadawane pytania dotyczące prywatnej strefy DNS platformy Azure
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082848"
---
# <a name="azure-private-dns-faq"></a>Usługa Azure prywatne DNS — często zadawane pytania

> [!IMPORTANT]
> Prywatnej usługi DNS platformy Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>Usługa DNS platformy Azure obsługuje domen prywatnych?

Obsługa domen prywatnych jest obsługiwana przy użyciu funkcji Azure prywatne strefy DNS. Prywatnych stref DNS są zarządzane przy użyciu tych samych narzędzi, jak strefy DNS platformy Azure dostępnego z Internetu. Są one możliwej do rozpoznania tylko z w ramach określonej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Przegląd](private-dns-overview.md).

Aby uzyskać informacje na temat innych wewnętrznych DNS na platformie Azure, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Usługa Azure DNS Private Zones zadziała regionami platformy Azure?

Tak. Private Zones jest obsługiwana dla rozpoznawania nazw DNS między sieciami wirtualnymi w różnych regionach platformy Azure. Strefy prywatne działa nawet bez jawnie komunikacji równorzędnej sieci wirtualnych. Wszystkie sieci wirtualne muszą być określone jako sieciami wirtualnymi rozpoznawania prywatnej strefy. Klienci, może być konieczne sieci wirtualne można równorzędne dla protokołu TCP/HTTP przepływ ruchu z jednego regionu do innego.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Jest łączność z Internetem z wymagane w przypadku stref prywatnych sieci wirtualnych?

Nie. Prywatnych stref pracy wraz z sieciami wirtualnymi. Klienci ich używać do zarządzania domenami dla maszyn wirtualnych lub innych zasobów w ramach i między sieciami wirtualnymi. Łączność z Internetem nie jest wymagana do rozpoznawania nazw.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Można użyć tej samej prywatnej strefy dla wielu sieci wirtualnych rozpoznawania?

Tak. Z jednej prywatnej strefy można skojarzyć maksymalnie 1000 sieci wirtualnych.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Sieć wirtualną należącą do innej subskrypcji dodaniem jako połączone sieci wirtualnej do prywatnej strefy?

Tak. Musi mieć uprawnienia do operacji zapisu w sieci wirtualnych i prywatnej strefy DNS. Do wielu ról RBAC można udzielić uprawnienia do zapisu. Na przykład rola klasycznego RBAC Współautor sieci ma uprawnienia do zapisu w sieciach wirtualnych. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Zostaną automatycznie zarejestrowane maszyny wirtualnej rekordy DNS w prywatnej strefy automatycznie usunięte po usunięciu maszyny wirtualnej?

Tak. Jeśli usuniesz maszynę wirtualną w ramach sieci wirtualnej połączonej za pomocą autorejestracją włączone zarejestrowany rekordy są automatycznie usuwane.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Rekord maszyny wirtualnej automatycznie zarejestrowane w prywatnej strefy z połączonej sieci wirtualnej można usunąć ręcznie?

Tak. Może spowodować zastąpienie automatycznie zarejestrowanych rekordów DNS przy użyciu ręcznie utworzonego rekordu DNS w strefie. Poniższe pytania i odpowiedzi dotyczą w tym temacie.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co się stanie, gdy próbuję ręczne tworzenie nowego rekordu DNS do strefy prywatnej, o tej samej nazwy hosta jako automatycznie zarejestrowane istniejącej maszyny wirtualnej w sieci wirtualnej połączonej?

Spróbuj ręcznie utworzyć nowego rekordu DNS do strefy prywatnej, o tej samej nazwy hosta jako maszyna wirtualna istniejących, automatycznie zarejestrowane w sieci wirtualnej połączonej. Po wykonaniu nowego rekordu DNS zastępuje rekord automatycznie zarejestrowane maszyny wirtualnej. Jeśli spróbujesz ponownie usunąć to ręczne tworzenie rekordu DNS w strefie, Usuń zakończy się pomyślnie. Automatyczna rejestracja odbywa się ponownie, tak długo, jak maszyna wirtualna nadal istnieje i ma dołączoną prywatny adres IP. Rekord DNS jest ponownie tworzone automatycznie w strefie.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co się stanie, gdy firma Microsoft Odłącz sieć wirtualna połączona z prywatnej strefy? Zostaną rekordy automatycznie zarejestrowane maszyny wirtualnej z sieci wirtualnej usunięte ze strefy zbyt?

Tak. Aby odłączyć sieci wirtualnej połączonej z prywatnej strefy, należy zaktualizować strefę DNS, aby usunąć połączenie skojarzonej sieci wirtualnej. W ramach tego procesu rekordy maszyn wirtualnych, które zostały automatycznie rejestrowane są usuwane ze strefy.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co się stanie po usunięciu połączonej sieci wirtualnej, która jest połączona z prywatnej strefy? Czy mamy ręcznie zaktualizować prywatnej strefy można odłączyć sieci wirtualnej co sieć wirtualna połączona ze strefy?

Tak. Po usunięciu połączonej sieci wirtualnej bez najpierw odłączanie ze strefą prywatną operacja usuwania powiedzie się. Jednak sieci wirtualnej nie jest automatycznie odłączone z prywatnej strefy, jeśli istnieje. Należy ręcznie Odłącz sieć wirtualną z prywatnej strefy. Z tego powodu Odłącz sieć wirtualną z prywatnej strefy, zanim usuniesz je.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Rozpoznawanie nazw DNS przy użyciu domyślnej nazwy FQDN (internal.cloudapp.net) wciąż działają nawet wtedy, gdy strefę prywatną (na przykład private.contoso.com) jest połączona z siecią wirtualną?

Tak. Strefy prywatne w usłudze nie zastępuje domyślne rozwiązania DNS przy użyciu strefy internal.cloudapp.net platformy Azure. Jest dostępna jako dodatkową funkcję lub ulepszenie. Czy możesz polegać na internal.cloudapp.net platformy Azure lub w prywatnej strefy, użyj nazwy FQDN strefy, którą chcesz usunąć względem.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Sufiks DNS na maszynach wirtualnych w ramach połączonych sieci wirtualnej zostanie zmieniona na z prywatnej strefy?

Nie. Sufiks DNS na maszynach wirtualnych w sieci wirtualnej połączonej pozostaje jako domyślny sufiks platformy Azure ("*. internal.cloudapp.net"). Można ręcznie zmienić ten sufiks DNS na maszynach wirtualnych, na który strefa prywatna.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Jakie są limity użycia dla prywatnej strefy DNS platformy Azure?

Następujące limity mają zastosowanie, korzystając z prywatnej strefy DNS platformy Azure.

| Resource | Limit domyślny |
| --- | --- |
|Prywatnych stref DNS na subskrypcję|1000|
|Zestawy rekordów dla strefy prywatnej DNS|25,000|
|Rekordów na zestawu rekordów|20|
|Łączy sieci wirtualnych na strefę prywatną DNS|1000|
|Włączone łączy sieci wirtualnych na prywatnych stref DNS z automatyczną rejestracją|100|
|Liczba prywatnych stref DNS, które mogą uzyskać połączone sieci wirtualnej z włączoną automatyczną rejestrację|1|
|Liczba prywatnych stref DNS, które mogą uzyskać połączone sieci wirtualnej|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Czy istnieje portalu pomocy technicznej w przypadku stref prywatnych?

Tak i strefami prywatnymi, które zostały już utworzone za pomocą interfejsów API, PowerShell, interfejsu wiersza polecenia i zestawy SDK są widoczne w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o prywatnej strefy DNS platformy Azure](private-dns-overview.md)