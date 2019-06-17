---
title: Rozwiązywanie problemów — Rozwiązywanie problemów z konfiguracją usługi Azure Service wejściu | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak rozwiązać niektóre typowe problemy, które mogą się spodziewać dla Twojego drzwiami frontowymi samodzielnie.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 7a261d65a7bd3eea150dd764c65b94ddd47466b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736126"
---
# <a name="troubleshooting-common-routing-issues"></a>Rozwiązywanie typowych problemów routingu
W tym artykule opisano, jak do rozwiązywania niektórych typowych problemów routingu, które mogą się spodziewać dla danej konfiguracji usługi drzwiami frontowymi platformy Azure. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Nazwa hosta nie routingu do wewnętrznej bazy danych i zwraca kod stanu 400


### <a name="symptom"></a>Objaw
- Utworzono drzwiami frontowymi, ale zwraca kod stanu HTTP 400 żądań do hosta serwera sieci Web.

  - Utworzono DNS mapowanie z domeny niestandardowej do hosta frontonu zostały skonfigurowane. Jednak wysyła żądania do nazwy hosta domeny niestandardowej zwraca kod stanu HTTP 400 i wydaje się kierować do backend(s) zostały skonfigurowane.

### <a name="cause"></a>Przyczyna
- Ten symptom może się zdarzyć, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która zostanie dodany jako hosta serwera sieci Web. Reguły routingu należy jawnie dodać tego hosta serwera sieci Web nawet, jeśli jeden został już skonfigurowany dla hosta serwera sieci Web w obszarze poddomeny drzwiami frontowymi (*. azurefd.net) czy domeny niestandardowej ma mapowanie DNS.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
- Dodaj regułę routingu z domeny niestandardowej do puli zaplecza żądaną.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Limit czasu żądania nazwy hosta frontonu kod stanu 404 zwraca

### <a name="symptom"></a>Objaw
- Utworzono drzwiami frontowymi i skonfigurować hosta serwera sieci Web, pulę zaplecza przy użyciu co najmniej jednego zaplecza w nim i reguły routingu, który nawiązuje połączenie z hosta frontonu do puli zaplecza. Zawartość nie wydaje się być dostępny podczas wysyłania żądania do hosta skonfigurowanego serwera sieci Web, ponieważ zwracany jest kod stanu HTTP 404.

### <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn to objaw:
 - Wewnętrznej bazy danych nie jest publiczny zaplecza mające połączenie z Internetem i nie jest widoczna w usłudze wejściu.

- Wewnętrznej bazy danych jest błędnie skonfigurowane, który powoduje usługi drzwiami frontowymi można wysłać nieprawidłowe żądania (oznacza, że zaplecza akceptuje tylko HTTP, ale nie jest zaznaczone, dzięki czemu żądania HTTPS, więc drzwiami frontowymi próbuje do przesyłania dalej protokołu HTTPS).
- Zaplecze odrzuca nagłówek hosta, który został przekazany z żądania do zaplecza.
- Konfiguracja dla wewnętrznej bazy danych nie ma jeszcze w pełni wdrażane.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
1. Czas wdrożenia
    - Upewnij się, że minęło ~ 10 minut, można wdrożyć w konfiguracji.

2. Sprawdź ustawienia wewnętrznej bazy danych
   - Przejdź do puli zaplecza, który żądanie powinno być routingu w celu (w zależności od sposobu ma regułę routingu skonfigurowane) i sprawdź, czy _typ hosta zaplecza_ i nazwy hosta wewnętrznej bazy danych są poprawne. Wewnętrznej bazy danych w przypadku niestandardowego hosta, upewnij się, że możesz mieć poprawna go. 

   - Sprawdź swoje portach HTTP i HTTPS. W większości przypadków 80 i 443 (odpowiednio), są poprawne, a żadne zmiany nie będą wymagane. Istnieje jednak możliwość, że wewnętrzną bazą danych nie została skonfigurowana w ten sposób i nasłuchuje na innym porcie.

     - Sprawdź _nagłówek hosta zaplecza_ skonfigurowany dla zaplecza aplikacji, których routingu hosta serwera sieci Web w celu. W większości przypadków ten nagłówek powinna być taka sama jak _wewnętrznej bazy danych, nazwy hosta_. Może to spowodować jednak niepoprawną wartość różne kody stanu HTTP 4xx, wewnętrznej bazy danych oczekuje, że coś innego. Jeśli należy wprowadzić adres IP zaplecza, konieczne może być ustawiona _nagłówek hosta zaplecza_ zgodnie z nazwą hosta wewnętrznej bazy danych.


3. Sprawdź ustawienia reguły routingu
     - Przejdź do reguły routingu, który powinien trasy z danego nazwy hosta frontonu do puli zaplecza. Upewnij się, protokoły akceptowane są poprawnie skonfigurowane lub jeśli nie, upewnij się, że protokół, który drzwiami frontowymi zostanie użyty podczas przesyłania żądania został poprawnie skonfigurowany. _Zaakceptowane protokołów_ Określa, który żąda drzwiami frontowymi powinna obsługiwać i _przekazywania protokołu_ w obszarze _zaawansowane_ kartę Określa, jakie protokół drzwi należy używać do przesyłania żądania do zaplecza.
          - Na przykład jeśli wewnętrznej bazy danych będzie akceptować tylko żądania HTTP następujące konfiguracje są prawidłowymi:
               - _Zaakceptowane protokołów_ to HTTP i HTTPS. _Przekazywanie protokołu_ , jest protokół HTTP. Żądanie dopasowanie nie będzie działać, ponieważ protokół HTTPS jest dozwolony protokół, a jeśli żądanie pochodzi jako protokołu HTTPS, drzwiami frontowymi spróbuj przekazywać je przy użyciu protokołu HTTPS.

               - _Zaakceptowane protokołów_ używają protokołu HTTP. _Przekazywanie protokołu_ jest albo są zgodne, żądanie lub HTTPS.

   - Kliknij pozycję _zaawansowane_ kartę w górnej części okienka konfiguracji reguły routingu. _Ponowne zapisywanie adresów URL_ jest domyślnie wyłączona, a w tym polu należy używać tylko, jeśli chcesz zawęzić zakres zasobów z hostowanej wewnętrznej bazy danych, które mają być dostępne. Po wyłączeniu drzwiami frontowymi prześle tej samej ścieżki żądania, który odbierze. Istnieje możliwość, że to pole jest nieprawidłowo skonfigurowana i drzwiami frontowymi żąda zasobu z wewnętrznej bazy danych, która nie jest dostępna, więc zwracając kod stanu HTTP 404.

