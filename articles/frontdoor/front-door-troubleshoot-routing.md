---
title: Rozwiązywanie problemów z konfiguracją drzwiami frontowymi platformy Azure
description: W tym samouczku dowiesz się, jak samodzielnie rozwiązywać niektóre z typowych problemów, które mogą napotkać w drzwiach frontowych.
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
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471510"
---
# <a name="troubleshooting-common-routing-issues"></a>Rozwiązywanie typowych problemów z routingiem

W tym artykule opisano, jak rozwiązać niektóre typowe problemy z routingiem, które mogą napotkać w konfiguracji usługi Azure Front Door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 odpowiedź z drzwi wejściowych po kilku sekundach

### <a name="symptom"></a>Objaw

- Regularne żądania wysyłane do wewnętrznej bazy danych bez przechodzenia przez drzwi frontowe są sukcesem, ale przejście przez drzwi frontowe powoduje 503 odpowiedzi na błędy.

- Awaria drzwi przednich pojawia się po kilku sekundach (zazwyczaj po 30 sekundach)

### <a name="cause"></a>Przyczyna

Ten objaw występuje, gdy wewnętrznej bazy danych wykracza poza konfigurację limitu czasu (domyślnie jest 30 sekund), aby otrzymać żądanie z drzwiami frontowymi lub jeśli trwa poza tę wartość limitu czasu, aby wysłać odpowiedź na żądanie z drzwiami frontowymi. 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

- Wyślij żądanie bezpośrednio do wewnętrznej bazy danych (bez przechodzenia przez drzwi frontowe) i zobacz, jaki jest zwykły czas potrzebny do odpowiedzi wewnętrznej bazy danych.
- Wyślij żądanie za pośrednictwem drzwi wejściowych i sprawdź, czy widzisz jakieś 503 odpowiedzi. Jeśli nie, to nie może być problem z limitem czasu. Skontaktuj się z pomocą techniczną.
- Jeśli przejście przez drzwi frontowe powoduje 503 kod odpowiedzi na błąd, a następnie skonfigurować sendReceiveTimeout pole dla drzwi frontowych, aby przedłużyć domyślny limit czasu do 4 minut (240 sekund). Ustawienie znajduje się `backendPoolSettings` w `sendRecvTimeoutSeconds`obszarze i jest wywoływane . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Żądania wysyłane do domeny niestandardowej zwracają kod stanu 400

### <a name="symptom"></a>Objaw

- Utworzono drzwi frontowe, ale żądanie do hosta domeny lub frontendu zwraca kod stanu HTTP 400.

- Utworzono mapowanie DNS z domeny niestandardowej na skonfigurowany host frontendu. Jednak wysłanie żądania do niestandardowej nazwy hosta domeny zwraca kod stanu HTTP 400 i nie wydaje się być kierowana do skonfigurowanych zaplecza.

### <a name="cause"></a>Przyczyna

Ten objaw może się zdarzyć, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która została dodana jako host frontendu. Reguła routingu musi być jawnie dodana dla tego hosta frontendu, nawet jeśli została już skonfigurowana dla hosta frontendu pod poddomeną drzwiami frontowymi (*.azurefd.net), do których domena niestandardowa ma mapowanie DNS.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Dodaj regułę routingu z domeny niestandardowej do żądanej puli wewnętrznej bazy danych.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Drzwi frontowe nie przekierowują protokołu HTTP do protokołu HTTPS

### <a name="symptom"></a>Objaw

Drzwi frontowe mają regułę routingu, która mówi przekierowanie HTTP do HTTPS, ale dostęp do domeny nadal utrzymuje HTTP jako protokół.

### <a name="cause"></a>Przyczyna

To zachowanie może się zdarzyć, jeśli nie poprawnie skonfigurowano reguły routingu dla drzwi frontowych. Zasadniczo bieżąca konfiguracja nie jest specyficzna i może mieć sprzeczne reguły.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Żądanie do nazwy hosta Frontend zwraca kod stanu 404

### <a name="symptom"></a>Objaw

- Utworzono drzwi frontowe i skonfigurowano hosta frontendu, pulę wewnętrznej bazy danych z co najmniej jednym zapleczem oraz regułę routingu łączącą hosta wewnętrznej bazy danych z pulą wewnętrznej bazy danych. Zawartość nie wydaje się być dostępna podczas wysyłania żądania do skonfigurowanego hosta frontendu, ponieważ zwracany jest kod stanu HTTP 404.

### <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn tego objawu:

- Wewnętrznej bazy danych nie jest publicznych stoi zaplecza i nie jest widoczny dla drzwi frontowych.
- Wewnętrznej bazy danych jest nieprawidłowo skonfigurowany, co powoduje, że drzwi frontowe do wysyłania niewłaściwego żądania (oznacza to, że wewnętrznej bazy danych akceptuje tylko HTTP, ale nie zostały odznaczone zezwalając HTTPS, więc drzwi frontowe próbuje przesłać żądania HTTPS).
- Wewnętrznej bazy danych jest odrzucenie nagłówka hosta, który został przekazany z żądaniem do wewnętrznej bazy danych.
- Konfiguracja wewnętrznej bazy danych nie została jeszcze w pełni wdrożona.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Czas wdrożenia
   - Upewnij się, że czekałeś ~10 minut na wdrożenie konfiguracji.

2. Sprawdź ustawienia wewnętrznej bazy danych
    - Przejdź do puli wewnętrznej bazy danych, do której powinno być routing żądania (w zależności od sposobu skonfigurowania reguły routingu) i sprawdź, czy _typ hosta wewnętrznej bazy danych_ i nazwa hosta wewnętrznej bazy danych są poprawne. Jeśli wewnętrznej bazy danych jest hosta niestandardowego, upewnij się, że zostały napisane poprawnie. 

    - Sprawdź porty HTTP i HTTPS. W większości przypadków 80 i 443 (odpowiednio), są poprawne i nie będą wymagane żadne zmiany. Istnieje jednak szansa, że wewnętrznej bazy danych nie jest skonfigurowany w ten sposób i nasłuchuje na innym porcie.

        - Sprawdź _nagłówek hosta wewnętrznej bazy_ danych skonfigurowany dla zaplecza, do których powinien być routing hosta frontu. W większości przypadków ten nagłówek powinien być taki sam jak _nazwa hosta wewnętrznej bazy danych_. Jednak niepoprawna wartość może spowodować różne kody stanu HTTP 4xx, jeśli wewnętrznej bazy danych oczekuje czegoś innego. Jeśli wpisujesz adres IP wewnętrznej bazy danych, może być konieczne ustawienie _nagłówka hosta wewnętrznej bazy danych_ na adres hosta wewnętrznej bazy danych.


3. Sprawdź ustawienia reguły routingu
    - Przejdź do reguły routingu, która powinna być kierowana z nazwy hosta frontonu, o których mowa, do puli wewnętrznej bazy danych. Upewnij się, że zaakceptowane protokoły są poprawnie skonfigurowane, a jeśli nie, upewnij się, że drzwi wejściowe protokołu będą używane podczas przesyłania dalej żądania jest poprawnie skonfigurowany. Pole _Zaakceptowane protokoły_ określa, które żądania drzwiami frontowymi powinny być akceptowane, a _protokół Przekazywanie_ określa, jakiego protokołu drzwiami frontowymi powinny być używane do przekazywania żądania do wewnętrznej bazy danych.
         - Na przykład, jeśli wewnętrznej bazy danych akceptuje tylko żądania HTTP następujące konfiguracje będą prawidłowe:
            - _Akceptowane protokoły_ to HTTP i HTTPS. _Protokół przekazywania jest_ HTTP. Żądanie dopasowania nie będzie działać, ponieważ https jest dozwolonym protokołem, a jeśli żądanie pojawiło się jako HTTPS, drzwi frontowe będą próbowały przesłać go dalej za pomocą protokołu HTTPS.

            - _Akceptowane protokoły_ to HTTP. _Protokół przekazywania jest_ żądaniem dopasowania lub https.

    - _Ponowne przepisywanie adresów URL_ jest domyślnie wyłączone i to pole należy używać tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych zapleczem, które mają być dostępne. Po wyłączeniu drzwiami frontowymi będą przesyłać dalej tę samą ścieżkę żądania, którą otrzymuje. Jest możliwe, że to pole jest nieprawidłowo skonfigurowane i drzwiami frontowymi żądają zasobu z wewnętrznej bazy danych, który nie jest dostępny, zwracając w ten sposób kod stanu HTTP 404.

