---
title: Prywatna strefa DNS — Często zadawane pytania
description: W tym artykule zapoznaj się z często zadawanymi pytaniami dotyczącymi usługi Azure Private DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939424"
---
# <a name="azure-private-dns-faq"></a>Prywatna strefa DNS — Często zadawane pytania

Poniżej często zadawane są pytania dotyczące prywatnego systemu DNS platformy Azure.

## <a name="does-azure-dns-support-private-domains"></a>Czy usługa Azure DNS obsługuje domeny prywatne?

Domeny prywatne są obsługiwane przy użyciu funkcji prywatnych stref DNS platformy Azure. Prywatne strefy DNS można rozwiązywać tylko z określonych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [omówienie](private-dns-overview.md).

Aby uzyskać informacje na temat innych wewnętrznych opcji DNS na platformie Azure, zobacz [Rozpoznawanie nazw maszyn wirtualnych i wystąpień ról](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Czy prywatne strefy DNS platformy Azure będą działać w różnych regionach platformy Azure?

Tak. Strefy prywatne są obsługiwane w zakresie rozpoznawania dns między sieciami wirtualnymi w regionach platformy Azure. Strefy prywatne działają nawet bez jawnego równania sieci wirtualnych. Wszystkie sieci wirtualne muszą być połączone z prywatną strefą DNS.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Czy łączność z Internetem z sieci wirtualnych jest wymagana dla stref prywatnych?

Nie. Strefy prywatne współpracują z sieciami wirtualnymi. Służy im do zarządzania domenami maszyn wirtualnych lub innych zasobów w sieciach wirtualnych i między nimi. Połączenie z Internetem nie jest wymagane do rozpoznawania nazw.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Czy ta sama strefa prywatna może być używana dla kilku sieci wirtualnych w celu rozwiązania?

Tak. Można połączyć prywatną strefę DNS z tysiącami sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [Limity DNS platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Czy sieć wirtualna należąca do innej subskrypcji może być połączona ze strefą prywatną?

Tak. Musisz mieć uprawnienie operacji zapisu w sieciach wirtualnych i prywatnej strefie DNS. Uprawnienie do zapisu można przyznać kilku rolach RBAC. Na przykład rola RBAC klasycznego współautora sieci ma uprawnienia do zapisu w sieciach wirtualnych, a rola Współautor prywatnych stref DNS ma uprawnienia do zapisu w prywatnych strefach DNS. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Czy automatycznie zarejestrowane rekordy DNS maszyny wirtualnej w strefie prywatnej zostaną automatycznie usunięte po usunięciu maszyny wirtualnej?

Tak. Jeśli usuniesz maszynę wirtualną w połączonej sieci wirtualnej z włączoną autoregistracją, zarejestrowane rekordy zostaną automatycznie usunięte.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Czy automatycznie zarejestrowany rekord maszyny wirtualnej w strefie prywatnej z połączonej sieci wirtualnej może zostać usunięty ręcznie?

Tak. Automatycznie zarejestrowane rekordy DNS można zastąpić ręcznie utworzonym rekordem DNS w strefie. Poniższe pytanie i odpowiedź dotyczą tego tematu.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Co się stanie, gdy spróbuję ręcznie utworzyć nowy rekord DNS w strefie prywatnej, która ma taką samą nazwa hosta jak automatycznie zarejestrowana istniejąca maszyna wirtualna w połączonej sieci wirtualnej?

Spróbuj ręcznie utworzyć nowy rekord DNS w strefie prywatnej, która ma taką samą nazwa hosta jak istniejąca, automatycznie zarejestrowana maszyna wirtualna w połączonej sieci wirtualnej. Po wykonaniu tej czynnej pracy nowy rekord DNS zastępuje automatycznie zarejestrowany rekord maszyny wirtualnej. Jeśli spróbujesz ponownie usunąć ten ręcznie utworzony rekord DNS ze strefy, usunięcie zakończy się pomyślnie. Automatyczna rejestracja odbywa się ponownie tak długo, jak maszyna wirtualna nadal istnieje i ma prywatny adres IP dołączony do niego. Rekord DNS jest tworzony automatycznie w strefie.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co się stanie, gdy odłączymy połączą połączonyej sieci wirtualnej ze strefy prywatnej? Czy automatycznie zarejestrowane rekordy maszyny wirtualnej z sieci wirtualnej również zostaną usunięte ze strefy?

Tak. Aby odłączyć połącz połączona sieć wirtualna od strefy prywatnej, należy zaktualizować strefę DNS, aby usunąć skojarzone łącze sieci wirtualnej. W tym procesie rekordy maszyny wirtualnej, które zostały automatycznie zarejestrowane są usuwane ze strefy.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Co się stanie, gdy usuniemy połączonyej sieci wirtualnej, która jest połączona ze strefą prywatną? Czy musimy ręcznie zaktualizować strefę prywatną, aby odłączyć sieć wirtualną jako połączonyą sieć wirtualną od strefy?

Nie. Po usunięciu połączonej sieci wirtualnej bez wcześniejszego odłączenia jej ze strefy prywatnej operacja usuwania zakończy się pomyślnie, a łącza do strefy DNS są automatycznie czyszczone.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Czy rozpoznawanie dns przy użyciu domyślnej nazwy FQDN (internal.cloudapp.net) będzie nadal działać nawet wtedy, gdy strefa prywatna (na przykład private.contoso.com) jest połączona z siecią wirtualną?

Tak. Strefy prywatne nie zastępują domyślnej strefy internal.cloudapp.net dostarczonej przez platformę Azure. Niezależnie od tego, czy korzystasz z internal.cloudapp.net dostarczonych przez platformę Azure, czy na własnej strefie prywatnej, użyj sieci FQDN strefy, którą chcesz rozwiązać.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Czy sufiks DNS na maszynach wirtualnych w połączonej sieci wirtualnej zostanie zmieniony na sufiks strefy prywatnej?

Nie. Sufiks DNS na maszynach wirtualnych w połączonej sieci wirtualnej pozostaje domyślnym sufiksem dostarczonym przez platformę Azure ("*.internal.cloudapp.net"). Ten sufiks DNS na maszynach wirtualnych można ręcznie zmienić na strefę prywatną.
Aby uzyskać wskazówki dotyczące zmiany tego sufiksu, zobacz [Używanie dynamicznego systemu DNS do rejestrowania nazw hostów na własnym serwerze DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Jakie są limity użycia dla stref prywatnych dns platformy Azure?

Szczegółowe informacje na temat limitów użycia stref prywatnych usługi Azure DNS można znaleźć w [limitach dns platformy Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Dlaczego moje istniejące prywatne strefy DNS nie są wyświetlane w nowym działanie portalu?

Jeśli istniejąca prywatna strefa DNS została utworzona przy użyciu interfejsu API w wersji zapoznawczej, należy przeprowadzić migrację tych stref do nowego modelu zasobów. Prywatne strefy DNS utworzone przy użyciu interfejsu API w wersji zapoznawczej nie będą wyświetlane w nowym interfejsie portalu. Poniżej znajdują się instrukcje dotyczące migracji do nowego modelu zasobów.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Jak przeprowadzić migrację istniejących prywatnych stref DNS do nowego modelu?

Zdecydowanie zaleca się jak najszybsze migrację do nowego modelu zasobów. Starszy model zasobów będzie obsługiwany, jednak dalsze funkcje nie zostaną opracowane na górze tego modelu. W przyszłości zamierzamy przestarzałe go na rzecz nowego modelu zasobów. Aby uzyskać wskazówki dotyczące migracji istniejących prywatnych stref DNS do nowego modelu zasobów, zobacz[przewodnik po migracji dla stref prywatnych usługi Azure DNS](private-dns-migration-guide.md).

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usłudze Azure Private DNS](private-dns-overview.md)
