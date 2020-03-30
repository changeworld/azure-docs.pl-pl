---
title: Jak działa usługa Azure Traffic Manager | Dokumenty firmy Microsoft
description: Z tego artykułu dowiesz się, jak Usługa Traffic Manager kieruje ruch pod kątem wysokiej wydajności i dostępności aplikacji internetowych
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: rohink
ms.openlocfilehash: 4863ffd383cfcd46bad462156e26293d145fd418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294868"
---
# <a name="how-traffic-manager-works"></a>Jak działa Menedżer ruchu

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Traffic Manager zapewnia dwie kluczowe korzyści:

- Rozkład ruchu zgodnie z jedną z kilku [metod wyznaczania tras ruchu](traffic-manager-routing-methods.md)
- [Ciągłe monitorowanie kondycji punktu końcowego](traffic-manager-monitoring.md) i automatyczna praca awaryjna, gdy punkty końcowe nie powiodą się

Klient, który próbuje nawiązać połączenie z usługą, musi najpierw przekształcić nazwę DNS usługi na adres IP. Następnie klient łączy się z tym adresem IP, aby uzyskać dostęp do usługi.

**Najważniejszym punktem do zrozumienia jest to, że Usługa Traffic Manager działa na poziomie DNS.**  Usługa Traffic Manager używa systemu DNS do kierowania klientów do określonych punktów końcowych usługi na podstawie reguł metody routingu ruchu. Klienci łączą się **bezpośrednio**z wybranym punktem końcowym . Usługa Traffic Manager nie jest serwerem proxy ani bramą. Usługa Traffic Manager nie widzi ruchu przechodzącego między klientem a usługą.

## <a name="traffic-manager-example"></a>Przykład Menedżera ruchu

Firma Contoso Corp opracowała nowy portal partnerski. Adres URL tego `https://partners.contoso.com/login.aspx`portalu to . Aplikacja jest hostowana w trzech regionach platformy Azure. Aby zwiększyć dostępność i zmaksymalizować globalną wydajność, używają usługi Traffic Manager do dystrybucji ruchu klienta do najbliższego dostępnego punktu końcowego.

Aby osiągnąć tę konfigurację, wykonają następujące kroki:

1. Wdrażanie trzech wystąpień ich usługi. Nazwy DNS tych wdrożeń to "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" i "contoso-asia.cloudapp.net".
1. Utwórz profil usługi Traffic Manager o nazwie "contoso.trafficmanager.net" i skonfiguruj go tak, aby używał metody routingu ruchu "Wydajność" w trzech punktach końcowych.
1. Skonfiguruj nazwę domeny próżności "partners.contoso.com", aby wskazywał "contoso.trafficmanager.net", używając rekordu CNAME DNS.

![Konfiguracja DNS usługi Traffic Manager][1]

> [!NOTE]
> Podczas korzystania z domeny próżności z usługi Azure Traffic Manager, należy użyć CNAME, aby skierować nazwę domeny próżności do nazwy domeny usługi Traffic Manager. Standardy DNS nie zezwalają na tworzenie CNAME w "wierzchołku" (lub katalogu głównym) domeny. W związku z tym nie można utworzyć CNAME dla "contoso.com" (czasami nazywane "nagi" domeny). Możesz utworzyć tylko CNAME dla domeny w obszarze "contoso.com", na przykład "www.contoso.com". Aby obejść to ograniczenie, zalecamy hostowanie domeny DNS w [usłudze Azure DNS](../dns/dns-overview.md) i używanie [rekordów aliasu](../dns/tutorial-alias-tm.md) do wskazywanie profilu menedżera ruchu. Alternatywnie można użyć prostego przekierowania HTTP do bezpośredniego żądania "contoso.com" do alternatywnej nazwy, takiej jak "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Jak klienci łączą się za pomocą Usługi Traffic Manager

Kontynuując od poprzedniego przykładu, gdy `https://partners.contoso.com/login.aspx`klient żąda strony, klient wykonuje następujące kroki, aby rozpoznać nazwę DNS i ustanowić połączenie:

![Ustanowienie połączenia za pomocą usługi Traffic Manager][2]

1. Klient wysyła kwerendę DNS do skonfigurowaną cykliczną usługę DNS, aby rozpoznać nazwę "partners.contoso.com". Cykliczna usługa DNS, czasami nazywana "lokalną usługą DNS", nie obsługuje bezpośrednio domen DNS. Zamiast tego klient odciąża pracę kontaktowania się z różnymi autorytatywnymi usługami DNS w Internecie potrzebnymi do rozpoznania nazwy DNS.
2. Aby rozpoznać nazwę DNS, cykliczna usługa DNS znajduje serwery nazw domeny "contoso.com". Następnie kontaktuje się z tymi serwerami nazw, aby zażądać rekordu DNS "partners.contoso.com". Serwery DNS contoso.com zwracają rekord CNAME, który wskazuje contoso.trafficmanager.net.
3. Następnie cykliczna usługa DNS znajduje serwery nazw domeny "trafficmanager.net", które są dostarczane przez usługę Azure Traffic Manager. Następnie wysyła żądanie rekordu DNS "contoso.trafficmanager.net" do tych serwerów DNS.
4. Serwery nazw Menedżera ruchu odbierają żądanie. Wybierają punkt końcowy na podstawie:

    - Skonfigurowany stan każdego punktu końcowego (wyłączone punkty końcowe nie są zwracane)
    - Bieżący stan każdego punktu końcowego, zgodnie z kontrolą kondycji menedżera ruchu. Aby uzyskać więcej informacji, zobacz [Monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md).
    - Wybrana metoda routingu ruchu. Aby uzyskać więcej informacji, zobacz [Metody routingu usługi Traffic Manager](traffic-manager-routing-methods.md).

5. Wybrany punkt końcowy jest zwracany jako inny rekord CNAME DNS. W takim przypadku załóżmy, contoso-us.cloudapp.net jest zwracany.
6. Następnie cykliczna usługa DNS znajduje serwery nazw domeny "cloudapp.net". Kontaktuje się z tymi serwerami nazw, aby zażądać rekordu DNS "contoso-us.cloudapp.net". Zwracany jest rekord DNS "A" zawierający adres IP punktu końcowego usługi z siedzibą w USA.
7. Cykliczna usługa DNS konsoliduje wyniki i zwraca klientowi pojedynczą odpowiedź DNS.
8. Klient odbiera wyniki DNS i łączy się z podanym adresem IP. Klient łączy się bezpośrednio z punktem końcowym usługi aplikacji, a nie za pośrednictwem usługi Traffic Manager. Ponieważ jest to punkt końcowy HTTPS, klient wykonuje niezbędne uzgadnianie SSL/TLS, a następnie wykonuje żądanie HTTP GET dla strony "/login.aspx".

Cykliczna usługa DNS buforuje odbierane odpowiedzi DNS. Program rozpoznawania nazw DNS na urządzeniu klienckim również buforuje wynik. Buforowanie umożliwia odpowiedzi na kolejne zapytania DNS przy użyciu danych z pamięci podręcznej, a nie na wykonywanie zapytań z innych serwerów nazw. Czas trwania pamięci podręcznej jest określany przez właściwość "czas wygaśnięcia" każdego rekordu DNS. Krótsze wartości powodują szybsze wygasanie pamięci podręcznej, a tym samym więcej rund do serwerów nazw menedżera ruchu. Dłuższe wartości oznaczają, że może trwać dłużej, aby skierować ruch z dala od punktu końcowego, który uległ awarii. Usługa Traffic Manager umożliwia skonfigurowanie czasu wygaśnięcia używanego w odpowiedziach DNS usługi Traffic Manager na poziomie zaledwie 0 sekund i do 2 147 483 647 sekund (maksymalny zakres zgodny z [RFC-1035),](https://www.ietf.org/rfc/rfc1035.txt)co pozwala wybrać wartość, która najlepiej odpowiada potrzebom aplikacji.

## <a name="faqs"></a>Często zadawane pytania

* [Jaki adres IP używa Usługi Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Jakie typy ruchu mogą być kierowane za pomocą Usługi Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Czy Traffic Manager obsługuje "lepkie" sesje?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Dlaczego podczas korzystania z usługi Traffic Manager pojawia się błąd HTTP?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Jaki jest wpływ na wydajność korzystania z usługi Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Jakich protokołów aplikacji można używać z usługą Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Czy mogę używać Usługi Traffic Manager z "nagią" nazwą domeny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Czy Menedżer ruchu uwzględnia adres podsieci klienta podczas obsługi zapytań DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Co to jest DNS TTL i jak wpływa na moich użytkowników?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Jak wysoki lub niski można ustawić czas wygaśnięcia odpowiedzi usługi Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Jak mogę zrozumieć ilość zapytań pojawia się w moim profilu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu punktu końcowego usługi Traffic Manager [i automatycznej pracy awaryjnej.](traffic-manager-monitoring.md)

Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md)usługi Traffic Manager .

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

