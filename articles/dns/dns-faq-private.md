---
title: Prywatna strefa DNS — Często zadawane pytania
description: Często zadawane pytania dotyczące usługi Azure Prywatna strefa DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155702"
---
# <a name="azure-private-dns-faq"></a>Prywatna strefa DNS — Często zadawane pytania

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>Czy Azure DNS obsługuje domeny prywatne?

Obsługa domen prywatnych jest obsługiwana przy użyciu funkcji strefy Prywatna strefa DNS platformy Azure. Strefy Prywatna strefa DNS są zarządzane przy użyciu tych samych narzędzi, co strefy Azure DNS dostępne w Internecie. Są one rozpoznawalne tylko w określonych sieciach wirtualnych. Aby uzyskać więcej informacji, zobacz [Omówienie](private-dns-overview.md).

Aby uzyskać informacje na temat innych wewnętrznych opcji DNS na platformie Azure, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Czy Azure DNS Private Zones będzie działała w regionach platformy Azure?

Tak. Strefy prywatne są obsługiwane w przypadku rozpoznawania nazw DNS między sieciami wirtualnymi w różnych regionach platformy Azure. Strefy prywatne działają nawet bez jawnego komunikacji równorzędnej z sieciami wirtualnymi. Wszystkie sieci wirtualne należy określić jako rozwiązania sieci wirtualne dla strefy prywatnej. Może być konieczne połączenie komunikacji równorzędnej sieci wirtualnych z ruchem TCP/HTTP i przepływem z jednego regionu do innego.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Czy połączenie z Internetem jest nawiązywane przez sieci wirtualne wymagane dla stref prywatnych?

Nie. Strefy prywatne działają razem z sieciami wirtualnymi. Są one używane do zarządzania domenami maszyn wirtualnych lub innych zasobów w ramach i między sieciami wirtualnymi. Łączność z Internetem nie jest wymagana do rozpoznawania nazw.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Czy można użyć tej samej strefy prywatnej dla kilku sieci wirtualnych do rozwiązania?

Tak. Można skojarzyć maksymalnie 1000 sieci wirtualne z pojedynczą strefą prywatną.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Czy sieć wirtualna, która należy do innej subskrypcji, zostanie dodana jako połączona sieć wirtualna do strefy prywatnej?

Tak. Musisz mieć uprawnienie do zapisu w sieciach wirtualnych i prywatnej strefie DNS. Uprawnienie do zapisu można przyznawać do kilku ról RBAC. Na przykład rola RBAC współautor sieci klasycznej ma uprawnienia do zapisu sieci wirtualnych. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Czy rekordy DNS maszyn wirtualnych automatycznie zarejestrowane w strefie prywatnej będą automatycznie usuwane po usunięciu maszyny wirtualnej?

Tak. Jeśli usuniesz maszynę wirtualną w połączonej sieci wirtualnej z włączoną funkcją automatycznego rejestrowania, zarejestrowane rekordy zostaną automatycznie usunięte.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Czy automatycznie zarejestrowano rekord maszyny wirtualnej w strefie prywatnej z połączonej sieci wirtualnej?

Tak. Automatycznie zarejestrowane rekordy DNS można zastąpić ręcznie utworzony rekord DNS w strefie. Poniższe pytania i odpowiedzi dotyczą tego tematu.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co się stanie w przypadku próby ręcznego utworzenia nowego rekordu DNS w strefie prywatnej, która ma taką samą nazwę hosta, jak automatycznie zarejestrowana istniejąca maszyna wirtualna w połączonej sieci wirtualnej?

Spróbuj ręcznie utworzyć nowy rekord DNS w strefie prywatnej, która ma taką samą nazwę hosta jak istniejąca, automatycznie zarejestrowana maszyna wirtualna w połączonej sieci wirtualnej. Gdy to zrobisz, nowy rekord DNS zastępuje rekord automatycznie zarejestrowanej maszyny wirtualnej. Jeśli spróbujesz usunąć ręcznie utworzony rekord DNS z strefy, usuwanie zakończy się pomyślnie. Automatyczna rejestracja jest wykonywana ponownie, o ile maszyna wirtualna nadal istnieje i ma dołączony prywatny adres IP. Rekord DNS jest automatycznie tworzony w strefie.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co się stanie po rozłączeniu połączonej sieci wirtualnej ze strefy prywatnej? Czy automatycznie zarejestrowane rekordy maszyn wirtualnych z sieci wirtualnej zostaną usunięte ze strefy?

Tak. Aby odłączyć połączoną sieć wirtualną ze strefy prywatnej, należy zaktualizować strefę DNS, aby usunąć skojarzone łącze sieci wirtualnej. W tym procesie rekordy maszyn wirtualnych, które zostały zarejestrowane automatycznie, są usuwane ze strefy.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co się stanie po usunięciu połączonej sieci wirtualnej połączonej ze strefą prywatną? Czy trzeba ręcznie zaktualizować strefę prywatną w celu odłączenia sieci wirtualnej jako połączonej sieci wirtualnej ze strefy?

Tak. Po usunięciu połączonej sieci wirtualnej bez odłączenia jej od strefy prywatnej najpierw operacja usuwania zostanie zakończona pomyślnie. Jednak Sieć wirtualna nie jest automatycznie odłączana od strefy prywatnej, jeśli istnieje. Musisz ręcznie odłączyć sieć wirtualną od strefy prywatnej. Z tego powodu Odłącz sieć wirtualną od strefy prywatnej przed jej usunięciem.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Czy rozpoznawanie nazw DNS przy użyciu domyślnej nazwy FQDN (internal.cloudapp.net) nadal działa nawet wtedy, gdy strefa prywatna (na przykład private.contoso.com) jest połączona z siecią wirtualną?

Tak. Strefy prywatne nie zastępują domyślnych rozwiązań DNS przy użyciu strefy internal.cloudapp.net udostępnionej przez platformę Azure. Jest oferowana jako dodatkowa funkcja lub ulepszenie. Bez względu na to, czy korzystasz z internal.cloudapp.net opartego na platformie Azure, czy we własnej strefie prywatnej, użyj nazwy FQDN strefy, względem której chcesz rozwiązać ten problem.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Czy sufiks DNS maszyn wirtualnych w połączonej sieci wirtualnej zostanie zmieniony na tę strefę prywatną?

Nie. Sufiks DNS na maszynach wirtualnych w połączonej sieci wirtualnej pozostaje jako domyślny sufiks dostarczony przez platformę Azure ("*. internal.cloudapp.net"). Sufiks DNS można zmienić ręcznie na maszynach wirtualnych w ramach strefy prywatnej.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Jakie są limity użycia usługi Azure Prywatna strefa DNS?

W przypadku korzystania z usługi Azure Prywatna strefa DNS są stosowane następujące domyślne limity.

| Resource | Domyślny limit |
| --- | --- |
|Strefy Prywatna strefa DNS na subskrypcję|1000|
|Zestawy rekordów na Prywatna strefa DNS strefę|25,000|
|Rekordy na zestaw rekordów|20|
|Linki Virtual Network na prywatną strefę DNS|1000|
|Linki sieci wirtualnych na prywatne strefy DNS z włączoną rejestracją autorejestrowania|100|
|Liczba prywatnych stref DNS, z którymi można połączyć sieć wirtualną z włączoną funkcją autorejestracji|1|
|Liczba prywatnych stref DNS, do których można połączyć sieć wirtualną|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Czy w portalu są obsługiwane strefy prywatne?

Tak, a strefy prywatne, które zostały już utworzone za pośrednictwem interfejsów API, programu PowerShell, interfejsu wiersza polecenia i zestawów SDK, są widoczne na Azure Portal.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Dlaczego moje istniejące prywatne strefy DNS nie są wyświetlane w nowym środowisku portalu?

W ramach wersji zapoznawczej Refresh dostarczono Nowy model zasobów dla prywatnych stref DNS. Istniejące prywatne strefy DNS należy zmigrować do nowego modelu zasobów, zanim będzie można je wyświetlić w nowym środowisku portalu. Aby uzyskać instrukcje dotyczące migracji do nowego modelu zasobów, zobacz poniżej.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Jak mogę zmigrować istniejące prywatne strefy DNS do nowego modelu?
Zdecydowanie zalecamy przeprowadzenie migracji do nowego modelu zasobów najszybciej, jak to możliwe. Starsza wersja modelu zasobów będzie obsługiwana, jednak dalsze funkcje nie będą opracowywane na tym modelu. W przyszłości zamierzamy ją zaniechać na rzecz nowego modelu zasobów. Aby uzyskać wskazówki dotyczące migrowania istniejących prywatnych stref DNS do nowego modelu zasobów, zobacz[Przewodnik migracji Azure DNS stref prywatnych](private-dns-migration-guide.md).

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usłudze Azure Prywatna strefa DNS](private-dns-overview.md)