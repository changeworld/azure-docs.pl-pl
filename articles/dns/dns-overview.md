---
title: Co to jest system DNS platformy Azure?
description: Omówienie usługi hostingu DNS na platformie Microsoft Azure. Hostuj swoją domenę na platformie Microsoft Azure.
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: f68281f71ed525798410fb80b0d0175d38050c40
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829923"
---
# <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

Azure DNS to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Za pomocą usługi Azure DNS nie można kupić nazwy domeny. Za roczną opłatą możesz kupić nazwę domeny za pośrednictwem usługi [Azure Web Apps](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) lub innego rejestratora nazw domen. Następnie możesz hostować domeny w usłudze Azure DNS na potrzeby zarządzania rekordami. Aby uzyskać szczegółowe informacje, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

W usłudze Azure DNS są dostępne następujące funkcje:

## <a name="reliability-and-performance"></a>Wydajność i niezawodność

W usłudze Azure DNS domeny DNS są hostowane w globalnej sieci serwerów nazw DNS na platformie Azure. Dzięki zastosowaniu emisji dowolnej w usłudze Azure DNS każde zapytanie DNS jest obsługiwane przez najbliższy dostępny serwer DNS, co zapewnia szybkie działanie i wysoką dostępność domeny.

## <a name="security"></a>Bezpieczeństwo

Usługa Azure DNS jest oparta na usłudze Azure Resource Manager i zapewnia następujące funkcje:

* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#access-control) — pozwala kontrolować, kto może wykonywać określone czynności w organizacji.

* [Dzienniki aktywności](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#activity-logs) — umożliwiają monitorowanie sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji, lub znalezienie błędu podczas rozwiązywania problemów.

* [Blokowanie zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) — umożliwia zablokowanie subskrypcji, grupy zasobów lub zasobu, co pozwala zapobiec przypadkowemu usunięciu lub zmodyfikowaniu kluczowych zasobów przez innych użytkowników w organizacji.

Aby uzyskać więcej informacji, zobacz [How to protect DNS zones and records (Jak chronić strefy i rekordy DNS)](dns-protect-zones-recordsets.md). 


## <a name="ease-of-use"></a>Łatwość obsługi

Usługa Azure DNS umożliwia zarządzanie rekordami DNS dla platformy Azure oraz obsługę DNS dla zasobów zewnętrznych. Usługa Azure DNS jest zintegrowana z witryną Azure Portal i są w niej używane te same poświadczenia, umowy na obsługę techniczną oraz rozliczenia, co w pozostałych usługach platformy Azure. 

Opłaty związane z usługą DNS są naliczane na podstawie liczby stref DNS hostowanych na platformie Azure i liczby odbieranych zapytań systemu DNS. Aby dowiedzieć się więcej o cenach, zobacz [Cennik usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Możesz zarządzać domenami i rekordami za pomocą witryny Azure Portal, poleceń cmdlet programu Azure PowerShell oraz międzyplatformowego interfejsu wiersza polecenia platformy Azure. Aplikacje wymagające automatycznego zarządzania systemem DNS można zintegrować z usługą za pomocą interfejsu API REST oraz zestawów SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Konfigurowalne sieci wirtualne z domenami prywatnymi

Usługa Azure DNS obsługuje również prywatne domeny DNS (obecnie w publicznej wersji zapoznawczej). Dzięki temu można korzystać z własnych niestandardowych nazw domen w prywatnych sieciach wirtualnych zamiast z dostępnych obecnie nazw udostępnianych przez platformę Azure.

Aby uzyskać więcej informacji, zobacz [Using Azure DNS for private domains (Korzystanie z usługi Azure DNS na potrzeby domen prywatnych)](private-dns-overview.md).

## <a name="alias-records"></a>Rekordy aliasu

Usługa Azure DNS obsługuje zestawy rekordów aliasu. Możesz użyć zestawu rekordów aliasu, aby odwoływać się do zasobu platformy Azure, na przykład publicznego adresu IP platformy Azure lub profilu usługi Traffic Manager. Jeśli adres IP zasobu źródłowego ulegnie zmianie, zestaw rekordów aliasu płynnie zaktualizuje się podczas rozpoznawania nazw DNS. Zestaw rekordów aliasu wskazuje wystąpienie usługi, a wystąpienie usługi jest skojarzone z adresem IP. 

Dodatkowo możesz teraz skierować domenę wierzchołkową (na przykład contoso.com) do profilu usługi Traffic Manager, używając rekordu aliasu.

Aby uzyskać więcej informacji, zobacz temat [Overview of Azure DNS alias records (Omówienie rekordów aliasów usługi Azure DNS)](dns-alias.md).


## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat stref i rekordów DNS: [DNS zones and records overview (Omówienie stref i rekordów DNS)](dns-zones-records.md).

* Dowiedz się, jak utworzyć strefę i rekord DNS w usłudze Azure DNS: [Tworzenie strefy DNS](./dns-getstarted-create-dnszone-portal.md).

* Zobacz często zadawane pytania dotyczące usługi Azure DNS: [Azure DNS FAQ (Azure DNS — często zadawane pytania)](dns-faq.md).

