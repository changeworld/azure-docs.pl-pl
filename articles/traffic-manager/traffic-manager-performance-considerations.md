---
title: Zagadnienia dotyczące wydajności usługi Azure Traffic Manager | Dokumenty firmy Microsoft
description: Opis wydajności usługi Traffic Manager i sposobu testowania wydajności witryny podczas korzystania z Usługi Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 84367a00643c48e7fe2fb7f907bab64589193b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938543"
---
# <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Na tej stronie opisano zagadnienia dotyczące wydajności przy użyciu usługi Traffic Manager. Poniżej przedstawiono przykładowy scenariusz:

Masz wystąpienia swojej witryny w regionach WestUS i EastAsia. Jednym z wystąpień jest niepowodzeniem sprawdzanie kondycji dla sondy menedżera ruchu. Ruch aplikacji jest kierowany do regionu zdrowego. Ta usługa awaryjna jest oczekiwana, ale wydajność może być problem na podstawie opóźnienia ruchu teraz podróżujących do odległego regionu.

## <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Jedynym wpływem na wydajność usługi Traffic Manager w witrynie sieci Web jest początkowe wyszukiwanie DNS. Żądanie DNS dotyczące nazwy profilu usługi Traffic Manager jest obsługiwane przez serwer główny DNS firmy Microsoft, który obsługuje strefę trafficmanager.net. Usługa Traffic Manager wypełnia i regularnie aktualizuje serwery główne DNS firmy Microsoft na podstawie zasad usługi Traffic Manager i wyników sondy. Tak więc nawet podczas początkowego wyszukiwania DNS żadne zapytania DNS nie są wysyłane do Menedżera ruchu.

Usługa Traffic Manager składa się z kilku składników: serwerów nazw DNS, usługi interfejsu API, warstwy magazynu i usługi monitorowania punktów końcowych. If a Traffic Manager service component fails, there is no effect on the DNS name associated with your Traffic Manager profile. Rekordy na serwerach DNS firmy Microsoft pozostają niezmienione. Jednak monitorowanie punktów końcowych i aktualizowanie DNS nie mają miejsca. W związku z tym usługa Traffic Manager nie może zaktualizować systemu DNS w celu wskazania lokacji trybu failover, gdy lokacja główna ustępuje.

Rozpoznawanie nazw DNS jest szybkie, a wyniki są buforowane. Szybkość początkowego wyszukiwania DNS zależy od serwerów DNS używanych przez klienta do rozpoznawania nazw. Zazwyczaj klient może wykonać wyszukiwanie DNS w ciągu ~50 ms. Wyniki wyszukiwania są buforowane przez czas trwania czasu wygaśnięcia DNS (TTL). Domyślny czas wygaśnięcia dla menedżera ruchu wynosi 300 sekund.

Ruch NIE przepływa przez Menedżera ruchu. Po zakończeniu wyszukiwania DNS klient ma adres IP wystąpienia witryny sieci Web. Klient łączy się bezpośrednio z tym adresem i nie przechodzi przez menedżera ruchu. Wybrana zasada usługi Traffic Manager nie ma wpływu na wydajność systemu DNS. Jednak wydajność routingu metody może negatywnie wpłynąć na środowisko aplikacji. Jeśli na przykład zasady przekierowuje ruch z Ameryki Północnej do wystąpienia hostowanego w Azji, opóźnienie sieci dla tych sesji może być problem z wydajnością.

## <a name="measuring-traffic-manager-performance"></a>Pomiar wydajności menedżera ruchu

Istnieje kilka witryn sieci Web, których można użyć, aby zrozumieć skuteczność i zachowanie profilu usługi Traffic Manager. Wiele z tych witryn jest bezpłatnych, ale może mieć ograniczenia. Niektóre witryny oferują ulepszone monitorowanie i raportowanie za opłatą.

Narzędzia w tych witrynach mierzą opóźnienia DNS i wyświetlają rozpoznane adresy IP dla lokalizacji klientów na całym świecie. Większość z tych narzędzi nie buforuje wyników DNS. W związku z tym narzędzia pokazują pełne wyszukiwanie DNS za każdym razem, gdy test jest uruchamiany. Podczas testowania z własnego klienta, tylko raz podczas trwania czasu trwania czasu trwania czasu trwania czasu trwania.

## <a name="sample-tools-to-measure-dns-performance"></a>Przykładowe narzędzia do mierzenia wydajności systemu DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS oferuje wiele narzędzi wydajności. Narzędzie DNS Porównanie może pokazać, jak długo trwa rozpoznanie nazwy DNS i jak to porównać z innymi dostawcami usług DNS.

* [Websitepulse](https://www.websitepulse.com/help/tools.php)

    Jednym z najprostszych narzędzi jest WebSitePulse. Wprowadź adres URL, aby wyświetlić czas rozpoznawania DNS, pierwszy bajt, ostatni bajt i inne statystyki wydajności. Możesz wybrać jedną z trzech różnych lokalizacji testowych. W tym przykładzie zobaczysz, że pierwsze wykonanie pokazuje, że wyszukiwanie DNS trwa 0,204 s.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Ponieważ wyniki są buforowane, drugi test dla tego samego punktu końcowego usługi Traffic Manager wyszukiwania DNS trwa 0,002 s.

    ![puls2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitor syntetyczny aplikacji CA](https://asm.ca.com/en/checkit.php)

    Wcześniej znana jako narzędzie Watch-mouse Check Website, ta witryna pokazuje czas rozpoznawania DNS z wielu regionów geograficznych jednocześnie. Wprowadź adres URL, aby wyświetlić czas rozpoznawania DNS, czas połączenia i szybkość z kilku lokalizacji geograficznych. Użyj tego testu, aby zobaczyć, która usługa hostowana jest zwracana dla różnych lokalizacji na całym świecie.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Świst](https://tools.pingdom.com/)

    To narzędzie udostępnia statystyki wydajności dla każdego elementu strony sieci web. Karta Analiza strony pokazuje procent czasu poświęconego na wyszukiwanie DNS.

* [Co to jest mój DNS?](https://www.whatsmydns.net/)

    Ta witryna wykonuje wyszukiwanie DNS z 20 różnych lokalizacji i wyświetla wyniki na mapie.

* [Interfejs dig web](https://www.digwebinterface.com)

    Ta witryna zawiera bardziej szczegółowe informacje DNS, w tym rekordy CNAME i A. Upewnij się, że w opcjach "Colorize output" i "Stats" możesz sprawdzić opcję "Wszystkie" w obszarze Serwery nazw.

## <a name="next-steps"></a>Następne kroki

[Informacje o metodach routingu ruchu usługi Traffic Manager](traffic-manager-routing-methods.md)

[Testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet usługi Azure Traffic Manager](https://docs.microsoft.com/powershell/module/az.trafficmanager)

