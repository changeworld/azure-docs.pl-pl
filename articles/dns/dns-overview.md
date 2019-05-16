---
title: Co to jest system DNS platformy Azure?
description: Omówienie usługi hostingu DNS na platformie Microsoft Azure. Hostuj swoją domenę na platformie Microsoft Azure.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 530730dcf303a54803816e6f2af8787298c1c658
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65753752"
---
# <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

Azure DNS to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Za pomocą usługi Azure DNS nie można kupić nazwy domeny. Za roczną opłatą możesz kupić nazwę domeny, używając [domen usługi App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) lub innego rejestratora nazw domen. Następnie możesz hostować domeny w usłudze Azure DNS na potrzeby zarządzania rekordami. Więcej informacji można znaleźć w temacie [Delegowanie domeny do usługi DNS platformy Azure](dns-domain-delegation.md).

W usłudze Azure DNS są dostępne poniższe funkcje.

## <a name="reliability-and-performance"></a>Wydajność i niezawodność

W usłudze Azure DNS domeny DNS są hostowane w globalnej sieci serwerów nazw DNS na platformie Azure. W usłudze Azure DNS jest stosowana emisja dowolna. Każde zapytanie DNS jest obsługiwane przez najbliższy dostępny serwer DNS, co zapewnia szybkie działanie i wysoką dostępność domeny.

## <a name="security"></a>Bezpieczeństwo

 Usługa Azure DNS jest oparta na usłudze Azure Resource Manager, która zapewnia następujące funkcje:

* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwia kontrolowanie, kto może wykonywać określone czynności w organizacji.

* [Dzienniki aktywności](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) umożliwiają monitorowanie sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji, lub znalezienie błędu podczas rozwiązywania problemów.

* [Blokowanie zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) umożliwia blokowanie subskrypcji, grupy zasobów lub zasobu. Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usunięcie lub zmodyfikowanie krytycznych zasobów.

Aby uzyskać więcej informacji, zobacz [How to protect DNS zones and records (Jak chronić strefy i rekordy DNS)](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Usługa Azure DNS aktualnie nie obsługuje rozszerzeń DNSSEC. W większości przypadków mogą zmniejszyć potrzebę rozszerzeń DNSSEC, stale przy użyciu protokołu HTTPS/TLS w swoich aplikacjach. Rozszerzenia DNSSEC w przypadku najważniejszych wymagań dla stref DNS, może obsługiwać te strefy za pomocą dostawcy hostingu DNS innej.

## <a name="ease-of-use"></a>Łatwość obsługi

 Usługa Azure DNS umożliwia zarządzanie rekordami DNS dla platformy Azure oraz obsługę DNS dla zasobów zewnętrznych. Usługa Azure DNS jest zintegrowana z witryną Azure Portal i są w niej używane te same poświadczenia, umowy na obsługę techniczną oraz rozliczenia, co w pozostałych usługach platformy Azure. 

Opłaty związane z usługą DNS są naliczane na podstawie liczby stref DNS hostowanych na platformie Azure i liczby odbieranych zapytań systemu DNS. Aby dowiedzieć się więcej o cenach, zobacz [Cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Możesz zarządzać domenami i rekordami za pomocą witryny Azure Portal, poleceń cmdlet programu Azure PowerShell oraz międzyplatformowego interfejsu wiersza polecenia platformy Azure. Aplikacje, które wymagają automatycznego zarządzania systemem DNS, można zintegrować z usługą za pomocą interfejsu API REST oraz zestawów SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Konfigurowalne sieci wirtualne z domenami prywatnymi

Usługa Azure DNS obsługuje również prywatne domeny DNS za pomocą funkcji będącej obecnie w publicznej wersji zapoznawczej. Ta funkcja umożliwia korzystanie z własnych niestandardowych nazw domen w prywatnych sieciach wirtualnych, zamiast z dostępnych obecnie nazw udostępnianych przez platformę Azure.

Aby uzyskać więcej informacji, zobacz [Use Azure DNS for private domains (Korzystanie z usługi Azure DNS na potrzeby domen prywatnych)](private-dns-overview.md).

## <a name="alias-records"></a>Rekordy aliasu

Usługa Azure DNS obsługuje zestawy rekordów aliasu. Zestaw rekordów aliasów służy do odwoływania się do zasobu platformy Azure, takich jak Azure publiczny adres IP, profilu usługi Azure Traffic Manager lub punkt końcowy usługi Azure Content Delivery Network (CDN). Jeśli adres IP zasobu źródłowego ulegnie zmianie, zestaw rekordów aliasu płynnie zaktualizuje się podczas rozpoznawania nazw DNS. Zestaw rekordów aliasu wskazuje wystąpienie usługi, a wystąpienie usługi jest skojarzone z adresem IP.

Ponadto można teraz wskazać z wierzchołku lub domenę do profilu usługi Traffic Manager lub punkt końcowy usługi CDN przy użyciu rekordu aliasu. Przykładowa domena to contoso.com.

Aby uzyskać więcej informacji, zobacz temat [Overview of Azure DNS alias records (Omówienie rekordów aliasów usługi Azure DNS)](dns-alias.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat stref i rekordów DNS, zobacz [DNS zones and records overview (Omówienie stref i rekordów DNS)](dns-zones-records.md).

* Aby dowiedzieć się, jak utworzyć strefę w usłudze Azure DNS, zobacz [Create a DNS Zone (Tworzenie strefy DNS)](./dns-getstarted-create-dnszone-portal.md).

* Zobacz często zadawane pytania dotyczące usługi Azure DNS: [Azure DNS FAQ (Azure DNS — często zadawane pytania)](dns-faq.md).

