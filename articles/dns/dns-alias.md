---
title: Usługa Azure alias DNS rekordów — omówienie
description: Omówienie obsługi rekordów aliasów w systemie Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 5/13/2019
ms.author: victorh
ms.openlocfilehash: b34baa6f1ba91935fc6307dbb1617393786043b9
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692852"
---
# <a name="azure-dns-alias-records-overview"></a>Usługa Azure alias DNS rekordów — omówienie

Usługa Azure DNS rekordów aliasów są kwalifikacje w zestawie rekordów DNS. Mogą odwoływać się do innych zasobów platformy Azure z w obrębie strefy DNS. Na przykład można utworzyć zestaw rekordów alias, który odwołuje się do platformy Azure publiczny adres IP zamiast rekord. Alias punkty zestawu rekordów do platformy Azure publiczny adres IP adres wystąpienia usługi dynamicznie. W rezultacie zestaw rekordów aliasów bezproblemowo aktualizuje się sama podczas rozpoznawania nazw DNS.

Zestaw rekordów aliasów jest obsługiwana dla następujących typów rekordów w strefie usługi Azure DNS: 

- A
- AAAA
- CNAME

> [!NOTE]
> Jeśli zamierzasz użyć rekordu aliasu dla typów rekordów A lub AAAA, aby wskazywał [profilu usługi Azure Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md) należy się upewnić, że profil usługi Traffic Manager ma tylko [zewnętrzne punkty końcowe](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Musisz podać adres IPv4 lub IPv6 dla zewnętrznych punktów końcowych w usłudze Traffic Manager. Nie można używać w pełni kwalifikowanych nazw domen (FQDN) punktów końcowych. W idealnym przypadku używania statycznych adresów IP.

## <a name="capabilities"></a>Możliwości

- **Wskaż publicznego zasobu adresu IP z usługi DNS A/AAAA zestawu rekordów.** Można utworzyć zestawu rekordów A/AAAA i ułatwiają zestawie aliasu rekordu, aby wskazywał publiczny zasób adresu IP. Zestaw rekordów DNS jest automatycznie, jeśli publiczny adres IP zmieni się lub jest usunięte. Delegujące DNS unika się rekordy, które wskazują na niepoprawne adresy IP.

- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME.** Możesz utworzyć A/AAAA lub rekordu CNAME określić hasło i użyć rekordów aliasów, aby wskazywał profilu usługi Traffic Manager. Może to być szczególnie przydatne, gdy należy kierować ruchem w wierzchołku strefy, jak tradycyjnych rekordy CNAME nie są obsługiwane w wierzchołku strefy. Na przykład załóżmy, że profilu usługi Traffic Manager jest myprofile.trafficmanager.net i strefy DNS firmy to contoso.com. Można utworzyć aliasu rekordu zbiór typu A/AAAA dla domeny contoso.com (wierzchołku strefy) i wskaż myprofile.trafficmanager.net.
- **Wskaż punktu końcowego usługi Azure Content Delivery Network (CDN)** . Jest to przydatne podczas tworzenia statycznych witryn internetowych przy użyciu usługi Azure storage i Azure CDN.
- **Wskaż inny zestaw rekordów DNS w ramach tej samej strefie.** Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestawu rekordów CNAME systemu DNS może być aliasem do innego zestawu rekordów CNAME. To rozwiązanie jest przydatne w przypadku niektórych zestawów rekordów aliasów się i niektórych innych aliasów.

## <a name="scenarios"></a>Scenariusze

Istnieje kilka typowych scenariuszy dla rekordów aliasów.

### <a name="prevent-dangling-dns-records"></a>Zapobiegaj delegujące rekordów DNS

Powszechny problem z tradycyjnych rekordy DNS jest delegujące rekordów. Na przykład rekordy DNS, które nie zostały zaktualizowane w celu odzwierciedlenia zmian do adresów IP. Ten problem występuje, szczególnie w przypadku A/AAAA lub CNAME typy rekordów.

Przy użyciu tradycyjnych rekordu strefy DNS jeśli docelowy adres IP lub CNAME już nie istnieje, rekord DNS skojarzone z nią należy je ręcznie zaktualizować. W niektórych organizacjach ręcznej aktualizacji nie może się zdarzyć w czasie z powodu problemów z procesem lub z powodu separacji ról i poziomów uprawnień skojarzonych. Na przykład rola może mieć uprawnienia do usuwania rekordu CNAME lub adres IP należy do aplikacji. Ale nie ma wystarczających uprawnień do zaktualizowania rekordu DNS, który wskazuje na tych celów. Opóźnienie podczas aktualizowania rekordów DNS może powodować przestoju dla użytkowników.

Rekordów aliasów uniemożliwić delegujące przez sprzęganiu cyklu życia rekord DNS przy użyciu zasobów platformy Azure. Rozważmy na przykład rekord DNS, który zakwalifikował jako rekord aliasu, aby wskazywał publiczny adres IP lub w profilu usługi Traffic Manager. Usunięcie tych podstawowych zasobów rekord aliasu DNS zostanie usunięty w tym samym czasie.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Automatyczna aktualizacja DNS record-set w przypadku zmian adresów IP aplikacji

Ten scenariusz jest podobny do poprzedniego. Być może aplikacja zostanie przeniesiona lub ponownym uruchomieniu maszyny wirtualnej stanowiącej podstawę. Rekord aliasu następnie automatycznie aktualizuje zmiany adresu IP podstawowego publicznego zasobu adresu IP. Umożliwia to uniknięcie potencjalnych zagrożeń bezpieczeństwa kierowanie użytkowników do innej aplikacji, która ma przypisane stare publiczny adres IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Hostowanie aplikacji ze zrównoważonym obciążeniem w wierzchołku strefy

Protokół DNS uniemożliwia przypisania rekordów CNAME w wierzchołku strefy. Na przykład jeśli Twoja domena to contoso.com; można utworzyć rekordy CNAME dla somelable.contoso.com; ale nie można utworzyć rekordu CNAME dla domeny contoso.com, sam.
Ograniczenie to stanowi problem dla właścicieli aplikacji, mających równoważenia obciążenia aplikacji za zaporą [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Ponieważ za pomocą profilu usługi Traffic Manager wymaga utworzenia rekordu CNAME, nie jest możliwe wskazywała na profil usługi Traffic Manager w wierzchołku strefy.

Ten problem można rozwiązać przy użyciu rekordów aliasów. W przeciwieństwie do rekordów CNAME można utworzyć aliasu rekordów w wierzchołku strefy i właścicieli aplikacji może użyć go do profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe wskazują ich rekordów w wierzchołku strefy. Właściciele aplikacji może wskazywać tego samego profilu usługi Traffic Manager, używanego do innej domeny w swojej strefie DNS.

Na przykład contoso.com i www\.contoso.com może wskazywać tego samego profilu usługi Traffic Manager. Aby dowiedzieć się więcej o korzystaniu z rekordów aliasów z profilami usługi Azure Traffic Manager, zobacz sekcji Następne kroki.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Wskaż wierzchołku strefy punktów końcowych usługi Azure CDN

Podobnie jak profil usługi Traffic Manager umożliwia także rekordów aliasów wskaż punktów końcowych usługi CDN Azure wierzchołku strefy usługi DNS. Jest to przydatne podczas tworzenia statycznych witryn internetowych przy użyciu usługi Azure storage i Azure CDN. Można następnie uzyskać dostęp do witryny bez as "www", do nazwy DNS.

Na przykład jeśli statycznej witryny sieci Web ma nazwę www.contoso.com, Twoje użytkownicy mają dostęp witryny przy użyciu domeny contoso.com, bez konieczności można poprzedzić www na nazwę DNS.

Zgodnie z wcześniejszym opisem rekordów CNAME w wierzchołku strefy są nieobsługiwane. Tak nie można użyć rekordu CNAME, aby wskazywał contoso.com punktu końcowego usługi CDN. Zamiast tego można rekord aliasu wskaż wierzchołku strefy punktu końcowego usługi CDN bezpośrednio.

> [!NOTE]
> Wskazuje wierzchołku strefy punktów końcowych usługi CDN dla usługi Azure CDN from Akamai jest obecnie nieobsługiwana.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat rekordów aliasów, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu do odwoływania się do platformy Azure, publiczny adres IP](tutorial-alias-pip.md)
- [Samouczek: Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
