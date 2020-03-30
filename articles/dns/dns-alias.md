---
title: Omówienie rekordów aliasu — usługa Azure DNS
description: W tym artykule dowiesz się o obsłudze rekordów aliasów w usłudze Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295486"
---
# <a name="azure-dns-alias-records-overview"></a>Omówienie rekordów aliasów usługi Azure DNS

Rekordy aliasu DNS platformy Azure są kwalifikacjami w zestawie rekordów DNS. Mogą odwoływać się do innych zasobów platformy Azure z poziomu strefy DNS. Na przykład można utworzyć zestaw rekordów aliasu, który odwołuje się do publicznego adresu IP platformy Azure zamiast rekordu A. Zestaw rekordów aliasów dynamicznie wskazuje na wystąpienie usługi publicznego adresu IP platformy Azure. W rezultacie rekord aliasu zestaw bezproblemowo aktualizuje się podczas rozpoznawania DNS.

Zestaw rekordów aliasu jest obsługiwany dla następujących typów rekordów w strefie DNS platformy Azure: 

- A
- AAAA
- CNAME

> [!NOTE]
> Jeśli zamierzasz użyć rekordu aliasu dla typów rekordów A lub AAAA, aby wskazać [profil usługi Azure Traffic Manager,](../traffic-manager/quickstart-create-traffic-manager-profile.md) musisz upewnić się, że profil usługi Traffic Manager ma tylko zewnętrzne punkty [końcowe](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Należy podać adres IPv4 lub IPv6 dla zewnętrznych punktów końcowych w usłudze Traffic Manager. Nie można używać w pełni kwalifikowanych nazw domen (FQDN) w punktach końcowych. Najlepiej użyć statycznych adresów IP.

## <a name="capabilities"></a>Możliwości

- **Wskaż publiczny zasób IP z zestawu rekordów DNS A/AAAA.** Można utworzyć zestaw rekordów A/AAAA i ustawić rekord aliasu wskazujący publiczny zasób IP (standardowy lub podstawowy). Zestaw rekordów DNS zmienia się automatycznie, jeśli publiczny adres IP zmieni się lub zostanie usunięty. Unika się zwisających rekordów DNS wskazujących nieprawidłowe adresy IP.

   Istnieje bieżący limit 20 rekordów aliasów zestawów na zasób.

- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME.** Można utworzyć zestaw rekordów A/AAAA lub CNAME i użyć rekordów aliasu, aby wskazać go na profil usługi Traffic Manager. Jest to szczególnie przydatne, gdy trzeba kierować ruch na wierzchołek strefy, jak tradycyjne rekordy CNAME nie są obsługiwane dla wierzchołka strefy. Załóżmy na przykład, że profil usługi Traffic Manager jest myprofile.trafficmanager.net, a strefa DNS firmy jest contoso.com. Można utworzyć zestaw rekordów aliasu typu A/AAAA dla contoso.com (wierzchołek strefy) i myprofile.trafficmanager.net.
- **Wskaż punkt końcowy sieci dostarczania zawartości platformy Azure (CDN).** Jest to przydatne podczas tworzenia statycznych witryn sieci Web przy użyciu usługi Azure Storage i usługi Azure CDN.
- **Wskaż inny rekord DNS ustawiony w tej samej strefie.** Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów CNAME DNS może być aliasem innego zestawu rekordów CNAME. Ten układ jest przydatny, jeśli niektóre zestawy rekordów mają być aliasami, a niektóre niealiasje.

## <a name="scenarios"></a>Scenariusze

Istnieje kilka typowych scenariuszy dla rekordów aliasu.

### <a name="prevent-dangling-dns-records"></a>Zapobieganie zwisającym rekordom DNS

Częstym problemem z tradycyjnymi rekordami DNS jest zwisające rekordy. Na przykład rekordy DNS, które nie zostały zaktualizowane w celu odzwierciedlenia zmian w adresach IP. Ten problem występuje szczególnie w przypadku typów rekordów A/AAAA lub CNAME.

W przypadku tradycyjnego rekordu strefy DNS, jeśli docelowy adres IP lub CNAME już nie istnieje, skojarzony z nim rekord DNS musi zostać ręcznie zaktualizowany. W niektórych organizacjach ręczna aktualizacja może nie nastąpić w czasie z powodu problemów z procesem lub oddzielenia ról i skojarzonych poziomów uprawnień. Na przykład rola może mieć uprawnienia do usuwania CNAME lub adresu IP, który należy do aplikacji. Ale nie ma wystarczających uprawnień do aktualizacji rekordu DNS, który wskazuje na te cele. Opóźnienie w aktualizacji rekordu DNS może potencjalnie spowodować awarię użytkowników.

Rekordy aliasu zapobiegają zwisaniu odwołań przez ścisłe sprzężenie cyklu życia rekordu DNS z zasobem platformy Azure. Rozważmy na przykład rekord DNS, który jest kwalifikowany jako rekord aliasu, aby wskazać publiczny adres IP lub profil usługi Traffic Manager. Jeśli te zasoby bazowe zostaną usunięte, rekord aliasu DNS stanie się pustym zestawem rekordów. Nie odwołuje się już do usuniętego zasobu.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Automatyczne aktualizowanie rekordu DNS po zmianie adresów IP aplikacji

Ten scenariusz jest podobny do poprzedniego. Być może aplikacja zostanie przeniesiona lub podstawowa maszyna wirtualna zostanie ponownie uruchomiona. Rekord aliasu jest następnie aktualizowany automatycznie, gdy adres IP zmieni się dla podstawowego publicznego zasobu IP. Pozwala to uniknąć potencjalnych zagrożeń bezpieczeństwa związanych z kierowaniem użytkowników do innej aplikacji, do która została przypisana stary publiczny adres IP.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Host z równoważenia obciążenia aplikacji w wierzchołku strefy

Protokół DNS uniemożliwia przypisywanie rekordów CNAME w wierzchołku strefy. Na przykład, jeśli domena jest contoso.com; można tworzyć rekordy CNAME dla somelabel.contoso.com; ale nie można utworzyć CNAME dla contoso.com.
To ograniczenie stanowi problem dla właścicieli aplikacji, którzy mają aplikacje z równoważeniem obciążenia za [usługą Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Ponieważ użycie profilu usługi Traffic Manager wymaga utworzenia rekordu CNAME, nie można wskazać profilu usługi Traffic Manager z wierzchołka strefy.

Ten problem został rozwiązany przy użyciu rekordów aliasu. W przeciwieństwie do rekordów CNAME rekordy aliasów są tworzone w wierzchołku strefy, a właściciele aplikacji mogą go używać do wskazywali swój rekord wierzchołka strefy profilowi usługi Traffic Manager, który ma zewnętrzne punkty końcowe. Właściciele aplikacji wskazują ten sam profil usługi Traffic Manager, który jest używany dla każdej innej domeny w strefie DNS.

Na przykład contoso.com i www\.contoso.com mogą wskazywać ten sam profil usługi Traffic Manager. Aby dowiedzieć się więcej na temat używania rekordów aliasu w profilach usługi Azure Traffic Manager, zobacz sekcję Następne kroki.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Wierzchołek strefy punktowej do punktów końcowych usługi Azure CDN

Podobnie jak profil usługi Traffic Manager, można również użyć rekordów aliasu, aby skierować wierzchołek strefy DNS do punktów końcowych usługi Azure CDN. Jest to przydatne podczas tworzenia statycznych witryn sieci Web przy użyciu usługi Azure Storage i usługi Azure CDN. Następnie możesz uzyskać dostęp do witryny bez dołączania "www" do nazwy DNS.

Na przykład, jeśli statyczna `www.contoso.com, your users can access your site using contoso.com` witryna sieci Web ma nazwę bez konieczności dołączania do nazwy DNS.

Jak opisano wcześniej, rekordy CNAME nie są obsługiwane w wierzchołku strefy. Dlatego nie można użyć rekordu CNAME, aby wskazać contoso.com do punktu końcowego sieci CDN. Zamiast tego można użyć rekordu aliasu, aby bezpośrednio skierować wierzchołek strefy do punktu końcowego sieci CDN.

> [!NOTE]
> Wskazywanie wierzchołka strefy do punktów końcowych usługi CDN dla usługi Azure CDN z usługi Akamai nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rekordach aliasu, zobacz następujące artykuły:

- [Samouczek: Konfigurowanie rekordu aliasu w celu odwoływania się do publicznego adresu IP platformy Azure](tutorial-alias-pip.md)
- [Samouczek: konfigurowanie rekordu aliasu w celu obsługi nazw domen wierzchołkowych przy użyciu usługi Traffic Manager](tutorial-alias-tm.md)
- [Często zadawane pytania na temat systemu DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
