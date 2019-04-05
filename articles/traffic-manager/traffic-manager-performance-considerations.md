---
title: Zagadnienia dotyczące wydajności usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Zrozumienie wydajności usługi Traffic Manager oraz sposób testowania wydajności witryny sieci Web, korzystając z usługi Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: c2821890408e38d0d2be730da10b7926d77697d4
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048303"
---
# <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Ta strona opisano zagadnienia związane z wydajnością za pomocą usługi Traffic Manager. Rozważmy następujący scenariusz:

Masz wystąpień witryny sieci Web w regionach WestUS i Azja Wschodnia. Jedno z wystąpień kończy się niepowodzeniem sprawdzenie kondycji sondy Menedżer ruchu. Aplikacja ruch będzie kierowany do regionu dobrej kondycji. Oczekiwano pracy w trybie failover, ale wydajność może być problem związany z czasem oczekiwania wynoszącym ruchu teraz podróżującym na odległym regionie.

## <a name="performance-considerations-for-traffic-manager"></a>Zagadnienia dotyczące wydajności dla usługi Traffic Manager

Jedynie wpływ na wydajność usługi Traffic Manager może mieć w witrynie sieci Web jest początkowa wyszukiwania DNS. Żądanie DNS dla nazwy profilu usługi Traffic Manager jest obsługiwana przez główny serwer DNS firmy Microsoft, który hostuje strefy trafficmanager.net. Traffic Manager umożliwia wypełnienie i regularnie aktualizuje, głównych serwerów DNS firmy Microsoft opartych na zasadach usługi Traffic Manager i wyniki badania. Dlatego nawet w trakcie początkowego wyszukiwania DNS nie ma zapytań DNS są wysyłane do usługi Traffic Manager.

Usługa Traffic Manager składa się z kilku składników: Nazwy DNS serwerów, usługi interfejsu API, warstwa magazynu i punkt końcowy usługi monitorowania. W przypadku awarii składnika Usługa Traffic Manager nie ma żadnego wpływu na nazwę DNS skojarzone z profilu usługi Traffic Manager. Rekordów na serwerach DNS firmy Microsoft pozostaną bez zmian. Jednak nie nawiązały monitorowania punktu końcowego i aktualizacji DNS. W związku z tym usługi Traffic Manager nie będzie mógł aktualizowanie systemu DNS, aby wskazywał lokacji trybu failover, gdy ulegnie awarii lokacji głównej.

Rozpoznawanie nazw DNS to szybka i wyniki są buforowane. Na serwerach DNS, którego klient używa do rozpoznawania nazw zależy od prędkości początkowej wyszukiwania DNS. Zazwyczaj klient można wykonać wyszukiwania DNS, w ramach ~ 50 ms. Wyniki wyszukiwania są buforowane na czas trwania DNS Time-to-live (TTL). Wartość domyślna TTL usługa Traffic Manager to 300 sekund.

Ruch nie przepływać za pośrednictwem usługi Traffic Manager. Po wykonaniu wyszukiwania DNS klient ma adres IP dla wystąpienia usługi witryny sieci web. Klient łączy się bezpośrednio z tego adresu i nie zostały spełnione przy użyciu usługi Traffic Manager. Zasady usługi Traffic Manager, które wybierzesz nie ma wpływu na wydajność systemu DNS. Jednak wydajności routing metody może niekorzystnie wpłynąć na środowisko aplikacji. Na przykład jeśli zasady przekierowuje ruch z Ameryki Północnej z wystąpieniem hostowanych w Azji, opóźnienie sieci komunikacji z tymi sesjami może być problem z wydajnością.

## <a name="measuring-traffic-manager-performance"></a>Pomiar wydajności usługi Traffic Manager

Istnieje kilka witryn sieci Web, których można użyć, aby zrozumieć, wydajności i zachowań, profilu usługi Traffic Manager. Wiele z tych witryn jest bezpłatne, ale mogą mieć ograniczenia. Niektóre witryny oferują rozszerzonego monitorowania i raportowania za opłatą.

Narzędzia te lokacje miary DNS opóźnienia i wyświetlania adresy IP rozwiązania dla klienta lokalizacji na całym świecie. Większość tych narzędzi nie buforują wyniki DNS. W związku z tym narzędzia Pokaż pełne wyszukiwanie DNS, każdym razem, gdy test jest wykonywany. Podczas testowania z własnego klienta występują tylko pełną wydajność wyszukiwania DNS, jeden raz w okresie czasu wygaśnięcia.

## <a name="sample-tools-to-measure-dns-performance"></a>Przykładowe narzędzia, aby zmierzyć wydajność systemu DNS

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS oferuje wiele narzędzi wydajności. Narzędzie do porównywania DNS można pokazać, jak długo trwa rozwiązać nazwę DNS i że porównanie innych dostawców usług DNS.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Jednym z narzędzi najprostszą jest WebSitePulse. Wprowadź adres URL, aby wyświetlić czas rozpoznawania nazw DNS, pierwszy bajt, ostatni bajt i inne statystyk wydajności. Możesz wybrać trzy lokalizacje innego testu. W tym przykładzie zobaczysz pierwszym wykonaniu pokazuje, że wyszukiwanie DNS trwa 0.204 s.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Ponieważ wyniki są buforowane, drugi test dla tego samego punktu końcowego usługi Traffic Manager wyszukiwania DNS ma 0,002 s.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Monitorowanie aplikacji syntetycznych urzędu certyfikacji](https://asm.ca.com/en/checkit.php)

    Wcześniej znane jako narzędzia myszy Obejrzyj wyboru witryny sieci Web, ta lokacja pokazują czas rozpoznawania nazw DNS z wielu regionów geograficznych jednocześnie. Wprowadź adres URL, aby wyświetlić czas rozpoznawania nazw DNS, czas połączenia i szybkość, z kilku lokalizacjach geograficznych. Użyj tego testu, aby zobaczyć, które usługa hostowana jest zwracany w różnych lokalizacjach na całym świecie.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    To narzędzie udostępnia statystyk wydajności dla każdego elementu strony sieci web. Karta analizy strona przedstawia procent czasu poświęconego na wyszukiwania DNS.

* [Co to jest Moja DNS?](https://www.whatsmydns.net/)

    Ta witryna nie wyszukiwania DNS z 20 różnych lokalizacji i wyświetla wyniki na mapie.

* [Szczegółowa analiza interfejsu sieci Web](https://www.digwebinterface.com)

    Ta witryna zawiera bardziej szczegółowe informacje dotyczące systemu DNS, w tym rekordy CNAME i. Upewnij się, sprawdź "Output koloruj" i "Statystyki" w obszarze Opcje i wybrać "All" w ramach serwerów nazw.

## <a name="next-steps"></a>Następne kroki

[Metody routingu ruchu w usłudze Traffic Manager](traffic-manager-routing-methods.md)

[Testowanie ustawień usługi Traffic Manager](traffic-manager-testing-settings.md)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet usługi Azure Traffic Manager](https://docs.microsoft.com/powershell/module/az.trafficmanager)

