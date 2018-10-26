---
title: Usługa Azure alias DNS rekordów — omówienie
description: Omówienie obsługi rekordów aliasów w systemie Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 52b42e964e7abe207064aff49f7f8f27f8476ef4
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092846"
---
# <a name="azure-dns-alias-records-overview"></a>Usługa Azure alias DNS rekordów — omówienie

Usługa Azure DNS rekordów aliasów są kwalifikacje w zestawie rekordów DNS. Mogą odwoływać się do innych zasobów platformy Azure z w obrębie strefy DNS. Na przykład można utworzyć zestaw rekordów alias, który odwołuje się do platformy Azure publiczny adres IP zamiast rekord. Alias punkty zestawu rekordów do platformy Azure publiczny adres IP adres wystąpienia usługi dynamicznie. W rezultacie zestaw rekordów aliasów bezproblemowo aktualizuje się sama podczas rozpoznawania nazw DNS.

Zestaw rekordów aliasów jest obsługiwana dla następujących typów rekordów w strefie usługi Azure DNS: 

- A 
- AAAA 
- CNAME 

> [!NOTE]
> Rekordów aliasów dla typów rekordów A lub AAAA dla usługi Azure Traffic Manager są obsługiwane tylko dla typów zewnętrznego punktu końcowego. Musisz podać adres IPv4 lub IPv6, zgodnie z potrzebami, w przypadku zewnętrznych punktów końcowych w usłudze Traffic Manager. W idealnym przypadku należy użyć statyczne adresy IP dla adresu.

## <a name="capabilities"></a>Możliwości

- **Wskaż publicznego zasobu adresu IP z usługi DNS A/AAAA zestawu rekordów.** Można utworzyć zestawu rekordów A/AAAA i ułatwiają zestawie aliasu rekordu, aby wskazywał publiczny zasób adresu IP.

- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME.** Możesz wskazać CNAME profilu usługi Traffic Manager, z zestawu rekordów CNAME w systemie DNS. Przykładem jest contoso.trafficmanager.net. Teraz możesz też wskazać profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe z rekordu A lub AAAA ustawiana w strefie DNS.

   > [!NOTE]
   > Rekordów aliasów dla typów rekordów A lub AAAA usługi Traffic Manager są obsługiwane tylko dla typów zewnętrznego punktu końcowego. Musisz podać adres IPv4 lub IPv6, zgodnie z potrzebami, w przypadku zewnętrznych punktów końcowych w usłudze Traffic Manager. W idealnym przypadku należy użyć statyczne adresy IP dla adresu.
   
- **Wskaż inny zestaw rekordów DNS w ramach tej samej strefie.** Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestawu rekordów CNAME systemu DNS może być aliasem do innego zestawu rekordów CNAME tego samego typu. To rozwiązanie jest przydatne w przypadku niektórych zestawów rekordów aliasów się i niektórych innych aliasów.

## <a name="scenarios"></a>Scenariusze
Istnieje kilka typowych scenariuszy dla rekordów aliasów.

### <a name="prevent-dangling-dns-records"></a>Zapobiegaj delegujące rekordów DNS
 W ramach strefy usługi Azure DNS rekordów aliasów może służyć do ściśle śledzenia cyklem życia zasobów platformy Azure. Zasoby obejmują publiczny adres IP lub w profilu usługi Traffic Manager. Powszechny problem z tradycyjnych rekordy DNS jest delegujące rekordów. Ten problem występuje, szczególnie w przypadku A/AAAA lub CNAME typy rekordów. 

Przy użyciu tradycyjnych rekordu strefy DNS jeśli docelowy adres IP lub CNAME już nie istnieje, rekord DNS w strefie nie może ustalić go. W rezultacie rekord muszą być aktualizowane ręcznie. W niektórych organizacjach to ręcznej aktualizacji nie może się zdarzyć w czasie. Również może być problematyczne ze względu na separacji ról i poziomów uprawnień skojarzonych.

Na przykład rola może mieć uprawnienia do usuwania rekordu CNAME lub adres IP należy do aplikacji. Ale nie ma wystarczających uprawnień do zaktualizowania rekordu DNS, który wskazuje na tych celów. Opóźnienie czasowe odbywa się między, gdy adres IP lub CNAME zostanie usunięty i rekord DNS, który wskazuje na jej zostanie usunięte. To opóźnienie godziny powodować przestoju dla użytkowników.

Rekordów aliasów Usuń złożoność skojarzonych z tym scenariuszem. Pomagają zapobiegać delegujące odwołania. Weźmy na przykład rekord DNS, który zakwalifikował jako rekord aliasu, aby wskazywał publiczny adres IP lub w profilu usługi Traffic Manager. Usunięcie tych podstawowych zasobów rekord aliasu DNS zostanie usunięty w tym samym czasie. Ten proces pozwala się upewnić, że użytkownicy nigdy nie będzie występować ulegnie awarii.

### <a name="update-dns-zones-automatically-when-application-ips-change"></a>Automatyczne aktualizowanie strefy DNS, gdy zmienią się aplikacji adresy IP

Ten scenariusz jest podobny do poprzedniego. Być może aplikacja zostanie przeniesiona lub ponownym uruchomieniu maszyny wirtualnej stanowiącej podstawę. Rekord aliasu następnie automatycznie aktualizuje zmiany adresu IP podstawowego publicznego zasobu adresu IP. Aby uniknąć zagrożenia bezpieczeństwa, należy przekierować użytkowników do innej aplikacji, która ma stary adres IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostowanie aplikacji ze zrównoważonym obciążeniem w wierzchołku strefy

Protokół DNS zapobiega przypisanie coś innego niż rekord A lub AAAA w wierzchołku strefy. Przykładem jest contoso.com. Ograniczenie to stanowi problem dla właścicieli aplikacji, mających równoważenia obciążenia aplikacji za zaporą usługi Traffic Manager. Nie jest możliwe wskazywała na profil usługi Traffic Manager z rekordu wierzchołku strefy. W wyniku właścicieli aplikacji należy użyć, aby uzyskać obejście tego problemu. Przekierowanie w warstwie aplikacji musi przekierowywać domenę w wierzchołku strefy do innej domeny. Przykładem jest przekierowanie z contoso.com www.contoso.com. To rozwiązanie przedstawia informacje o pojedynczym punktem awarii dla funkcji przekierowania.

Przy użyciu rekordów aliasów ten problem już nie istnieje. Teraz właścicieli aplikacji można wskazać ich rekordów w wierzchołku strefy profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe. Właściciele aplikacji może wskazywać tego samego profilu usługi Traffic Manager, używanego do innej domeny w swojej strefie DNS. Na przykład contoso.com i www.contoso.com może wskazywać tego samego profilu usługi Traffic Manager. Jest to możliwe tak długo, jak profil usługi Traffic Manager ma tylko zewnętrzne punkty końcowe skonfigurowane.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rekordów aliasów, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu do odwoływania się do platformy Azure, publiczny adres IP](tutorial-alias-pip.md)
- [Samouczek: Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
