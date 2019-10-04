---
title: Co to jest Azure DNS?
description: Omówienie usługi hostingu DNS na Microsoft Azure. Hostowanie domeny w Microsoft Azure.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 5055734ae3529d7fca87688c5f9410f38368a634
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959371"
---
# <a name="what-is-azure-dns"></a>Co to jest Azure DNS?

Azure DNS to usługa hostingu dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Hostowanie domen na platformie Azure pozwala na Zarządzanie rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co w przypadku innych usług platformy Azure.

Nie można użyć Azure DNS do zakupu nazwy domeny. W przypadku opłaty rocznej można kupić nazwę domeny za pomocą [domen App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) lub rejestratora nazw domen innej firmy. Domeny mogą być hostowane w Azure DNS na potrzeby zarządzania rekordami. Aby uzyskać więcej informacji, zobacz [delegowanie domeny do Azure DNS](dns-domain-delegation.md).

Poniższe funkcje są zawarte w Azure DNS.

## <a name="reliability-and-performance"></a>Niezawodność i wydajność

Domeny DNS w Azure DNS są hostowane w globalnej sieci serwerów nazw DNS na platformie Azure. Azure DNS używa sieci emisji dowolnej. Każde zapytanie DNS jest odpowiedzią przez najbliższy dostępny serwer DNS, aby zapewnić szybką wydajność i wysoką dostępność dla domeny.

## <a name="security"></a>Zabezpieczenia

 Azure DNS jest oparta na Azure Resource Manager, która udostępnia funkcje takie jak:

* [Kontrola dostępu oparta na rolach](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) w celu kontrolowania, kto ma dostęp do określonych działań w organizacji.

* [Dzienniki aktywności](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) do monitorowania sposobu, w jaki użytkownik w organizacji zmodyfikował zasób lub aby znaleźć błąd podczas rozwiązywania problemów.

* [Blokowanie zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) w celu zablokowania subskrypcji, grupy zasobów lub zasobu. Blokowanie uniemożliwia innym użytkownikom w organizacji Przypadkowe usunięcie lub zmodyfikowanie zasobów krytycznych.

Aby uzyskać więcej informacji, zobacz [jak chronić strefy i rekordy DNS](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS obecnie nie obsługuje rozszerzeń DNSSEC. W większości przypadków można ograniczyć potrzebę stosowania rozszerzeń DNSSEC przez spójne użycie protokołu HTTPS/TLS w aplikacjach. Jeśli rozszerzenia DNSSEC mają krytyczne znaczenie dla stref DNS, można hostować te strefy z dostawcami hostingu DNS innych firm.

## <a name="ease-of-use"></a>Łatwość użycia

 Azure DNS mogą zarządzać rekordami DNS dla usług platformy Azure i zapewnić serwer DNS dla zasobów zewnętrznych. Azure DNS jest zintegrowana z Azure Portal i używa tych samych poświadczeń, kontraktu pomocy technicznej i rozliczeń jako innych usług platformy Azure. 

Opłaty za usługę DNS są naliczane na podstawie liczby stref DNS hostowanych na platformie Azure oraz liczby odebranych zapytań DNS. Aby dowiedzieć się więcej o cenach, zobacz [cennik Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Domenami i rekordami można zarządzać przy użyciu Azure Portal, Azure PowerShell poleceń cmdlet i międzyplatformowego interfejsu wiersza polecenia platformy Azure. Aplikacje wymagające zautomatyzowanego zarządzania systemem DNS można zintegrować z usługą za pomocą interfejsu API REST i zestawów SDK.

## <a name="customizable-virtual-networks-with-private-domains"></a>Dostosowywalne sieci wirtualne z domenami prywatnymi

Azure DNS obsługuje również prywatne domeny DNS. Ta funkcja umożliwia korzystanie z własnych niestandardowych nazw domen w prywatnych sieciach wirtualnych, a nie dostępnych już dzisiaj nazw udostępnianych przez platformę Azure.

Aby uzyskać więcej informacji, zobacz [używanie Azure DNS w przypadku domen prywatnych](private-dns-overview.md).

## <a name="alias-records"></a>Rekordy aliasów

Azure DNS obsługuje zestawy rekordów aliasów. Możesz użyć zestawu rekordów aliasu, aby odwołać się do zasobu platformy Azure, takiego jak publiczny adres IP platformy Azure, profil usługi Azure Traffic Manager lub punkt końcowy usługi Azure Content Delivery Network (CDN). Jeśli adres IP bazowego zasobu ulegnie zmianie, rekord aliasu zostanie bezproblemowo zaktualizowany podczas rozpoznawania nazw DNS. Zestaw rekordów aliasu wskazuje na wystąpienie usługi, a wystąpienie usługi jest skojarzone z adresem IP.

Ponadto można teraz wskazać wierzchołek lub domenę wykorzystaną w profilu Traffic Manager lub punkcie końcowym usługi CDN przy użyciu rekordu aliasu. Przykładem jest contoso.com.

Aby uzyskać więcej informacji, zobacz [Omówienie rekordów aliasów Azure DNS](dns-alias.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o strefach i rekordach DNS, zobacz [Omówienie stref i rekordów DNS](dns-zones-records.md).

* Aby dowiedzieć się, jak utworzyć strefę w Azure DNS, zobacz [Tworzenie strefy DNS](./dns-getstarted-create-dnszone-portal.md).

* Często zadawane pytania dotyczące Azure DNS można znaleźć w temacie [Azure DNS często zadawane](dns-faq.md)pytania.

