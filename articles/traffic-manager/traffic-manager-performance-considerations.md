---
title: Zagadnienia dotyczące wydajności usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Zrozumienie wydajności na Menedżera ruchu i jak przetestować wydajność witryny sieci Web, korzystając z Menedżera ruchu
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876390"
---
# <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Ta strona opisano zagadnienia dotyczące wydajności przy użyciu usługi Traffic Manager. Rozważmy następujący scenariusz:

Masz wystąpień witryny sieci Web w regionach WestUS i EastAsia. Jedno z wystąpień kończy się niepowodzeniem sprawdzenie kondycji do badania Menedżera ruchu. Aplikacja ruch jest kierowany do regionu dobrej kondycji. Oczekiwano tej pracy awaryjnej, ale wydajność może być problem związany z opóźnienia ruchu teraz podróży oddalonych regionu.

## <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Tylko wpływu na wydajność mające przez Menedżera ruchu w witrynie sieci Web jest początkowa wyszukiwania DNS. Żądanie DNS dla nazwy profilu Menedżera ruchu jest obsługiwany przez główny serwer DNS firmy Microsoft, który hostuje tę strefę trafficmanager.net. Menedżer ruchu wypełnia i regularnie aktualizuje, serwerów głównych DNS firmy Microsoft na podstawie zasad Menedżera ruchu i wyników badania. Dzięki temu nawet podczas początkowej wyszukiwania DNS nie ma zapytań DNS są wysyłane do usługi Traffic Manager.

Menedżer ruchu składa się z kilku składników: nazwa DNS, serwery, usługi interfejsu API warstwy magazynu i punkt końcowy usługi monitorowania. W przypadku niepowodzenia składnika usługi Traffic Manager brak nie wpływa na nazwę DNS skojarzone z profilem Menedżera ruchu. Rekordy w serwerach DNS firmy Microsoft pozostają niezmienione. Jednak nie nawiązały monitorowania punktów końcowych i aktualizacji DNS. W związku z tym Traffic Manager nie jest w stanie zaktualizować punktu do lokacji trybu failover systemowi lokacji głównej w systemie DNS.

Rozpoznawanie nazwy DNS jest szybkie i wyniki są buforowane. Na serwerach DNS, których klient używa do rozpoznawania nazw zależy od szybkości początkowej wyszukiwania DNS. Zazwyczaj klienta można ukończyć wyszukiwania DNS w ms ~ 50. Wyniki wyszukiwania są buforowane na czas trwania DNS Time-to-live (TTL). Domyślny czas wygaśnięcia Traffic Manager to 300 sekund.

Ruch nie przepływać przez Menedżera ruchu. Po zakończeniu wyszukiwania DNS, klient ma adres IP wystąpienia witryny sieci web. Klient łączy się bezpośrednio do tego adresu i nie zostały spełnione przez Menedżera ruchu. Wybrane zasady Menedżera ruchu nie ma wpływu na wydajność systemu DNS. Jednak wydajności routingu metody może niekorzystnie wpłynąć na środowisko aplikacji. Na przykład jeśli zasady przekierowuje ruch z Ameryki Północnej do wystąpienia hostowanej w Azji, opóźnienia sieci dla tych sesji może być problem z wydajnością.

## <a name="measuring-traffic-manager-performance"></a>Pomiaru wydajności usługi Traffic Manager

Istnieje kilka witryn internetowych, używanych do zrozumienia wydajności i zachowanie profilu usługi Traffic Manager. Wiele z tych witryn jest bezpłatne, ale mogą mieć ograniczenia. Niektóre witryny oferują rozszerzonego monitorowania i raportowania za opłatą.

Narzędzia te lokacje miary DNS opóźnienia i wyświetlania adresy IP rozwiązany dla klienta lokalizacji na całym świecie. Większość tych narzędzi nie buforują wyniki DNS. W związku z tym narzędzi Pokaż pełne wyszukiwanie DNS zawsze, gdy test jest uruchamiany. Podczas testowania z własnego klienta występują tylko pełną wydajność wyszukiwania DNS raz w okresie czas wygaśnięcia.

## <a name="sample-tools-to-measure-dns-performance"></a>Przykładowe narzędzia do pomiaru wydajności DNS

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS oferuje wiele narzędzi wydajności. Narzędzie do porównywania DNS można sprawdzić, jak długo trwa rozpoznać nazwę DNS i jak który porównuje do innych dostawców usług DNS.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Jest jednym z narzędzi najprostszym WebSitePulse. Wprowadź adres URL, aby zobaczyć czas rozpoznawania nazw DNS, pierwszy bajt, ostatniego bajtu i innych danych statystycznych wydajności. Można wybrać trzy testu różnych lokalizacjach. W tym przykładzie widać, że pierwszy wykonywania wskazuje, że wyszukiwania DNS ma 0.204 s.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Ponieważ wyniki są buforowane, drugi test dla tego samego punktu końcowego Menedżera ruchu wyszukiwania DNS ma 0,002 s.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor aplikacji syntetycznych urzędu certyfikacji](https://asm.ca.com/en/checkit.php)

    Wcześniej znana jako narzędzie Watchmouse wyboru witryny sieci Web, ta lokacja pokazują czas rozpoznawania nazw DNS w wielu regionach geograficznych jednocześnie. Wprowadź adres URL, aby zobaczyć czas rozpoznawania nazw DNS, czas połączenia i szybkość z wielu lokalizacji geograficznych. Użyj tego testu, aby zobaczyć, które usługi hostowanej są zwracane do innej lokalizacji na całym świecie.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    To narzędzie umożliwia statystyki dla każdego elementu strony sieci web. Karta strony analizy przedstawia procent czasu poświęconego na wyszukiwania DNS.

* [Co to jest Mój DNS?](http://www.whatsmydns.net/)

    Ta witryna nie wyszukiwania DNS z różnych lokalizacji 20 i wyświetla wyniki na mapie.

* [Szczegółowej interfejs sieci Web](http://www.digwebinterface.com)

    Ta lokacja zawiera bardziej szczegółowe informacje dotyczące systemu DNS, w tym rekordów CNAME i. Upewnij się, sprawdź "Koloruj dane wyjściowe" i "Statystykę" w obszarze Opcje i wybierz "All" w obszarze Nameservers.

## <a name="next-steps"></a>Następne kroki

[O metodach routingu ruchu Menedżera ruchu](traffic-manager-routing-methods.md)

[Testowanie ustawień Menedżera ruchu](traffic-manager-testing-settings.md)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet systemu Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)

