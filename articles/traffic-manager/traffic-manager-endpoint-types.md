---
title: Typy punktów końcowych menedżera ruchu | Dokumenty firmy Microsoft
description: W tym artykule opisano różne typy punktów końcowych, które mogą być używane z usługą Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rohink
ms.openlocfilehash: 3d8f899a7899243129d31c2620a51dc764a8e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250935"
---
# <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

Usługa Microsoft Azure Traffic Manager umożliwia kontrolowanie sposobu dystrybucji ruchu sieciowego do wdrożeń aplikacji działających w różnych centrach danych. Każde wdrożenie aplikacji należy skonfigurować jako „punkt końcowy” w usłudze Traffic Manager. Gdy usługa Traffic Manager odbierze żądanie DNS, wybiera dostępny punkt końcowy, który ma zostać zwrócony w odpowiedzi DNS. Menedżer ruchu opiera wybór na bieżący stan punktu końcowego i metody routingu ruchu. Aby uzyskać więcej informacji, zobacz [Jak działa Usługa Traffic Manager](traffic-manager-how-it-works.md).

Istnieją trzy typy punktów końcowych obsługiwanych przez usługę Traffic Manager:

* **Punkty końcowe platformy Azure** są używane na potrzeby usług hostowanych na platformie Azure.
* **Zewnętrzne punkty końcowe** są używane w przypadku adresów IPv4/IPv6, nazw FQDN lub usług hostowanych poza platformą Azure — w środowisku lokalnym lub u innego dostawcy hostingu.
* **Zagnieżdżone punkty końcowe** służą do łączenia profili usługi Traffic Manager w celu tworzenia bardziej elastycznych schematów routingu ruchu na potrzeby obsługi większych, bardziej złożonych wdrożeń.

Nie ma żadnych ograniczeń dotyczących łączenia punktów końcowych różnych typów w jednym profilu usługi Traffic Manager. Każdy profil może zawierać kilka różnych typów punktów końcowych.

W poniższych sekcjach opisano każdy typ punktu końcowego bardziej szczegółowo.

## <a name="azure-endpoints"></a>Punkty końcowe platformy Azure

Punkty końcowe platformy Azure są używane dla usług opartych na platformie Azure w usłudze Traffic Manager. Obsługiwane są następujące typy zasobów platformy Azure:

* Usługi w chmurze PaaS.
* Web Apps
* Miejsca w aplikacjach sieci Web
* PublicIPAddress zasobów (które mogą być podłączone do maszyn wirtualnych bezpośrednio lub za pośrednictwem modułu Azure Load Balancer). Adres publicIpAddress musi mieć przypisaną nazwę DNS do użycia w profilu usługi Traffic Manager.

Zasoby PublicIPAddress to zasoby usługi Azure Resource Manager. Nie istnieją one w klasycznym modelu wdrażania. W związku z tym są one obsługiwane tylko w środowiskach usługi Azure Resource Manager usługi Traffic Manager. Inne typy punktów końcowych są obsługiwane zarówno za pośrednictwem Menedżera zasobów, jak i klasycznego modelu wdrażania.

Podczas korzystania z punktów końcowych platformy Azure usługa Traffic Manager wykrywa, kiedy aplikacja sieci Web jest zatrzymana i uruchomiona. Ten stan jest odzwierciedlany w stanie punktu końcowego. Szczegółowe informacje można [znaleźć w monitorowaniu punktu końcowego usługi Traffic Manager.](traffic-manager-monitoring.md#endpoint-and-profile-status) Po zatrzymaniu usługi podstawowej usługa usługi nie wykonuje kontroli kondycji punktu końcowego ani nie kieruje ruchu do punktu końcowego. W przypadku zatrzymanego wystąpienia nie występują żadne zdarzenia rozliczeniowe usługi Traffic Manager. Po ponownym uruchomieniu usługi, rozliczeń wznawia i punkt końcowy kwalifikuje się do odbioru ruchu. To wykrywanie nie ma zastosowania do publicipaddress punktów końcowych.

## <a name="external-endpoints"></a>Zewnętrzne punkty końcowe

Zewnętrzne punkty końcowe są używane dla adresów IPv4/IPv6, FQDN lub dla usług poza platformą Azure. Użycie punktów końcowych adresu IPv4/IPv6 umożliwia menedżerowi ruchu sprawdzanie kondycji punktów końcowych bez konieczności używania nazwy DNS. W rezultacie usługa Traffic Manager może odpowiadać na zapytania z rekordami A/AAAA podczas zwracania tego punktu końcowego w odpowiedzi. Usługi poza platformą Azure mogą obejmować usługę hostowana lokalnie lub u innego dostawcy. Zewnętrzne punkty końcowe mogą być używane indywidualnie lub w połączeniu z punktami końcowymi platformy Azure w tym samym profilu usługi Traffic Manager, z wyjątkiem punktów końcowych określonych jako adresy IPv4 lub IPv6, które mogą być tylko zewnętrznymi punktami końcowymi. Połączenie punktów końcowych platformy Azure z zewnętrznymi punktami końcowymi umożliwia różne scenariusze:

* Zapewnij zwiększoną nadmiarowość dla istniejącej aplikacji lokalnej w aktywnym lub aktywnym-pasywnym modelu pracy awaryjnej przy użyciu platformy Azure. 
* Kierowanie ruchu do punktów końcowych, które nie mają skojarzonej z nimi nazwy DNS. Ponadto zmniejsz ogólne opóźnienie wyszukiwania DNS, usuwając konieczność uruchomienia drugiej kwerendy DNS w celu uzyskania zwracanego adresu IP nazwy DNS.
* Zmniejsz opóźnienia aplikacji dla użytkowników na całym świecie, rozszerzyj istniejącą aplikację lokalną na dodatkowe lokalizacje geograficzne na platformie Azure. Aby uzyskać więcej informacji, zobacz [Kierowanie ruchem "Wydajność" usługi "Wydajność" usługi Traffic Manager](traffic-manager-routing-methods.md#performance).
* Zapewnij dodatkową pojemność dla istniejącej aplikacji lokalnej, w sposób ciągły lub jako rozwiązanie "burst-to-cloud", aby sprostać wzrostowi popytu przy użyciu platformy Azure.

W niektórych przypadkach warto używać zewnętrznych punktów końcowych do odwoływania się do usług platformy Azure (przykłady można znaleźć w [często zadawanych pytaniach).](traffic-manager-faqs.md#traffic-manager-endpoints) W takim przypadku kontrole kondycji są rozliczane według stawki punktów końcowych platformy Azure, a nie wskaźnika zewnętrznych punktów końcowych. Jednak w przeciwieństwie do punktów końcowych platformy Azure, jeśli zatrzymać lub usunąć podstawowej usługi, sprawdzanie kondycji rozliczeń będzie kontynuowane, dopóki nie wyłączyć lub usunąć punkt końcowy w usłudze Traffic Manager.

## <a name="nested-endpoints"></a>Zagnieżdżone punkty końcowe

Zagnieżdżone punkty końcowe łączą wiele profilów usługi Traffic Manager w celu utworzenia elastycznych schematów routingu ruchu i zaspokojenia potrzeb większych, złożonych wdrożeń. W zagnieżdżonych punktach końcowych profil "podrzędny" jest dodawany jako punkt końcowy do profilu "nadrzędnego". Profile podrzędne i nadrzędne mogą zawierać inne punkty końcowe dowolnego typu, w tym inne profile zagnieżdżone. Aby uzyskać więcej informacji, zobacz [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Aplikacje sieci Web jako punkty końcowe

Podczas konfigurowania aplikacji sieci Web jako punktów końcowych w usłudze Traffic Manager obowiązują pewne dodatkowe zagadnienia:

1. Tylko aplikacje internetowe w jednostce SKU "Standard" lub wyższej kwalifikują się do użytku z Usługą Traffic Manager. Próby dodania aplikacji sieci Web niższej jednostki SKU nie powiodą się. Obniżenie poziomu jednostki SKU istniejącej aplikacji sieci Web powoduje, że usługa Traffic Manager nie wysyła już ruchu do tej aplikacji sieci Web. Aby uzyskać więcej informacji na temat obsługiwanych planów, zobacz [Plany usługi app service](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Gdy punkt końcowy odbiera żądanie HTTP, używa nagłówka "host" w żądaniu, aby określić, która aplikacja sieci Web powinna obsługiwać żądanie. Nagłówek hosta zawiera nazwę DNS używaną do inicjowania żądania, na przykład "contosoapp.azurewebsites.net". Aby użyć innej nazwy DNS z aplikacją sieci Web, nazwa DNS musi być zarejestrowana jako niestandardowa nazwa domeny aplikacji. Podczas dodawania punktu końcowego aplikacji sieci Web jako punktu końcowego platformy Azure nazwa DNS profilu usługi Traffic Manager jest automatycznie rejestrowana dla aplikacji. Ta rejestracja jest automatycznie usuwana po usunięciu punktu końcowego.
3. Każdy profil usługi Traffic Manager może mieć co najwyżej jeden punkt końcowy aplikacji sieci Web z każdego regionu platformy Azure. Aby obejść to ograniczenie, można skonfigurować aplikację sieci Web jako zewnętrzny punkt końcowy. Aby uzyskać więcej informacji, zobacz często zadawane [pytania](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Włączanie i wyłączanie punktów końcowych

Wyłączenie punktu końcowego w usłudze Traffic Manager może być przydatne do tymczasowego usuwania ruchu z punktu końcowego, który jest w trybie konserwacji lub jest ponownie wdrażany. Po ponownym uruchomieniu punktu końcowego można go ponownie włączyć.

Punkty końcowe można włączyć i wyłączyć za pośrednictwem portalu usługi Traffic Manager, powershell, interfejsu wiersza polecenia lub interfejsu API REST.

> [!NOTE]
> Wyłączenie punktu końcowego platformy Azure nie ma nic wspólnego ze stanem wdrożenia na platformie Azure. Usługa platformy Azure (na przykład maszyna wirtualna lub aplikacja sieci Web pozostaje uruchomiona i może odbierać ruch nawet wtedy, gdy jest wyłączona w usłudze Traffic Manager. Ruch można skierować bezpośrednio do wystąpienia usługi, a nie za pośrednictwem nazwy DNS profilu usługi Traffic Manager. Aby uzyskać więcej informacji, [zobacz, jak działa Usługa Traffic Manager](traffic-manager-how-it-works.md).

Bieżące uprawnienia każdego punktu końcowego do odbioru ruchu zależy od następujących czynników:

* Stan profilu (włączony/wyłączony)
* Stan punktu końcowego (włączony/wyłączony)
* Wyniki kontroli kondycji dla tego punktu końcowego

Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Ponieważ usługa Traffic Manager działa na poziomie DNS, nie może wpływać na istniejące połączenia z dowolnym punktem końcowym. Gdy punkt końcowy jest niedostępny, usługa Traffic Manager kieruje nowe połączenia do innego dostępnego punktu końcowego. Jednak host za wyłączone lub złej kondycji punktu końcowego może nadal odbierać ruch za pośrednictwem istniejących połączeń, dopóki te sesje zostaną zakończone. Aplikacje powinny ograniczyć czas trwania sesji, aby umożliwić ruch do drenażu z istniejących połączeń.

Jeśli wszystkie punkty końcowe w profilu są wyłączone lub sam profil jest wyłączony, usługa Traffic Manager wysyła odpowiedź "NXDOMAIN" do nowej kwerendy DNS.

## <a name="faqs"></a>Często zadawane pytania

* [Czy mogę używać usługi Traffic Manager z punktami końcowymi z wielu subskrypcji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Czy mogę używać usługi Traffic Manager z miejscami przejściowymi usługi w chmurze?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Czy usługa Traffic Manager obsługuje punkty końcowe IPv6?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Czy mogę używać usługi Traffic Manager z więcej niż jedną aplikacją sieci Web w tym samym regionie?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Jak przenieść punkty końcowe usługi Azure profilu usługi Azure do innej grupy zasobów?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak działa Usługa Traffic Manager.](traffic-manager-how-it-works.md)
* Dowiedz się więcej o monitorowaniu punktu końcowego usługi Traffic Manager [i automatycznej pracy awaryjnej.](traffic-manager-monitoring.md)
* Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)usługi Traffic Manager .
