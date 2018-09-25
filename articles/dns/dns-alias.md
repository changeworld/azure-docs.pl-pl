---
title: Usługa Azure alias DNS rekordów — omówienie
description: Omówienie obsługi rekordów aliasów w systemie Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 05a6a1700de2b8b334b40d9efd9b8d79e9e7241f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957576"
---
# <a name="azure-dns-alias-records-overview"></a>Usługa Azure alias DNS rekordów — omówienie

Rekordów aliasów w usłudze Azure DNS są kwalifikacji na zestaw rekordów DNS można odwoływać się do innych zasobów platformy Azure z w obrębie strefy DNS. Na przykład można utworzyć zestawu rekordów alias, który odwołuje się do platformy Azure publiczny adres IP zamiast tego rekordu. Ponieważ rekordów aliasów wskazuje na wystąpienie usługi Azure publiczny adres IP adres dynamicznie, zestawu rekordów aliasów bezproblemowo aktualizuje się sama podczas rozpoznawania nazw DNS.

Zestaw rekordów aliasów jest obsługiwana dla następujących typów rekordów w strefie usługi Azure DNS: A i AAAA, CNAME. 

> [!NOTE]
> Rekordów aliasów dla typów rekordów A lub AAAA usługi Traffic Manager są obsługiwane tylko dla typów zewnętrznego punktu końcowego. Należy podać adres IPv4 lub IPv6 (najlepiej statyczne adresy IP) zgodnie z potrzebami w przypadku zewnętrznych punktów końcowych w usłudze Traffic Manager.

## <a name="capabilities"></a>Możliwości

- **Wskazywać na zasób publicznego adresu IP z usługi DNS zestawu rekordów A/AAAA**. Tworzenie zestawu rekordów A/AAAA i ułatwiają alias zestawu rekordów, aby wskazywać na zasób publicznego adresu IP.
- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME**. Oprócz możliwości wskazywał CNAME profilu usługi Traffic Manager (na przykład: contoso.trafficmanager.net) z zestawu rekordów CNAME w systemie DNS, można teraz również wskazać profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe z zestawu rekordów A lub AAAA w systemie DNS strefa.
   > [!NOTE]
   > Rekordów aliasów dla typów rekordów A lub AAAA usługi Traffic Manager są obsługiwane tylko dla typów zewnętrznego punktu końcowego. Należy podać adres IPv4 lub IPv6 (najlepiej statyczne adresy IP) zgodnie z potrzebami w przypadku zewnętrznych punktów końcowych w usłudze Traffic Manager.
- **Wskaż inny zestaw rekordów DNS w ramach tej samej strefie**. Alias rekordy mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład można mieć zestawu rekordów CNAME w systemie DNS jako alias do innego rekordów CNAME tego samego typu. Jest to przydatne, jeśli chcesz mieć niektóre zestawy rekordów aliasów się, a niektóre jako innych aliasów pod kątem zachowania.

## <a name="scenarios"></a>Scenariusze
Istnieje kilka typowych scenariuszy dla rekordów aliasu:

### <a name="prevent-dangling-dns-records"></a>Zapobiegaj delegujące rekordów DNS
Z w obrębie strefy usługi Azure DNS rekordów aliasów może służyć do ściśle śledzenia cyklem życia zasobów platformy Azure, takich jak profil publiczny adres IP i usługi Traffic Manager. Jednym z typowych problemów związanych z tradycyjnych rekordy DNS jest "rekordy delegujące", szczególnie w przypadku A/AAAA lub CNAME typy rekordów. 

Jeśli docelowy adres IP lub CNAME już nie istnieje, rekord strefy DNS przy użyciu tradycyjnych rekordu strefy DNS, nie ma informacji o tym fakcie i należy zaktualizować ręcznie. W niektórych organizacjach to ręcznej aktualizacji nie może mieć miejsce, w czasie, lub może być problematyczne ze względu na separacji ról i poziomów uprawnień skojarzonych.

Na przykład rola, która ma uprawnienia do usuwania rekordu CNAME lub adres IP należący do aplikacji może ma wystarczających uprawnień do zaktualizowania rekordu DNS, który wskazuje na tych celów. W rezultacie może być opóźnienie między gdy adres IP lub CNAME zostanie usunięty i rekord DNS, że wskazuje on została usunięta, które mogą potencjalnie powodować przestoju dla użytkowników końcowych.

Rekordów aliasów eliminacja złożoności skojarzonych z tym scenariuszem i pomagać w zapobieganiu delegujące odniesienie. Gdy rekord DNS jest kwalifikowana jako rekord aliasu, aby wskazywał publiczny adres IP lub w profilu usługi Traffic Manager, a tych podstawowych zasobów zostaną usunięte, rekord aliasu systemu DNS zostaną również usunięte, w tym samym czasie. Dzięki temu użytkownicy końcowi nigdy nie ponoszą ulegnie awarii.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Automatyczne aktualizowanie strefy DNS, gdy zmienią się aplikacji adresy IP

Podobnie jak w poprzednim scenariuszu — jeśli aplikacja zostanie przeniesiona lub ponownym uruchomieniu maszyny wirtualnej stanowiącej podstawę, rekord aliasu jest aktualizowane automatycznie po zmianie adresu IP dla bazowego zasobu publicznego adresu IP. Można uniknąć zagrożenia bezpieczeństwa, jeśli użytkownicy końcowi są kierowane do innej aplikacji, która ma stary adres IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostowanie aplikacji ze zrównoważonym obciążeniem w wierzchołku strefy

Protokół DNS uniemożliwia przypisania dowolnego elementu innego niż rekord A lub AAAA w wierzchołku strefy (na przykład: contoso.com). Przedstawia to problem dla właścicieli aplikacji, którzy mają obciążenia ze zrównoważonym aplikacji za zaporą Traffic Manager, ponieważ nie było możliwe wskazywała na profil usługi Traffic Manager z rekordu wierzchołku strefy. W rezultacie właścicieli aplikacji zostały zmuszeni do użycia obejście tego problemu. Na przykład przekierowanie do przekierowywania w wierzchołku strefy do innej domeny (z domeny contoso.com www.contoso.com) w warstwie aplikacji. Przedstawia informacje o pojedynczym punktem awarii pod względem funkcji przekierowania.

Przy użyciu rekordów aliasów ten problem już nie istnieje. Właścicieli aplikacji można teraz wskazać ich rekordów w wierzchołku strefy profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe. Dzięki tej możliwości właścicieli aplikacji może wskazywać tego samego profilu usługi Traffic Manager, używanego do innej domeny w swojej strefie DNS. Na przykład contoso.com i www.contoso.com można wskazują ten sam profil usługi Traffic Manager tak długo, jak profil usługi Traffic Manager ma tylko zewnętrzne punkty końcowe skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki

Aby Dowiedz się więcej o rekordów aliasów, zobacz artykuły follwing:

- [Samouczek: Konfigurowanie rekordu aliasu do odwoływania się do platformy Azure publiczny adres IP](tutorial-alias-pip.md)
- [Samouczek: Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
