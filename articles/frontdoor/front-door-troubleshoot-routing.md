---
title: Rozwiązywanie problemów z konfiguracją usługi Azure Front drzwiczk
description: W ramach tego samouczka nauczysz się, jak rozwiązywać problemy z niektórymi typowymi problemami, które mogą być używane w przypadku zewnętrznych drzwi.
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
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184556"
---
# <a name="troubleshooting-common-routing-issues"></a>Rozwiązywanie typowych problemów z routingiem
W tym artykule opisano sposób rozwiązywania niektórych typowych problemów z routingiem, które mogą być potrzebne w konfiguracji usługi Azure front-drzwi. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Nazwa hosta nie jest Routing do zaplecza i zwraca kod stanu 400


### <a name="symptom"></a>Objaw
- Utworzono tylne drzwi, ale żądanie do hosta frontonu zwraca kod stanu HTTP 400.

  - Utworzono mapowanie DNS z domeny niestandardowej na skonfigurowany Host frontonu. Jednak wysyłanie żądania do nazwy hosta domeny niestandardowej zwraca kod stanu HTTP 400 i nie wydaje się kierować do skonfigurowanych zaplecza.

### <a name="cause"></a>Przyczyna
- Ten objaw może wystąpić, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która została dodana jako host frontonu. Regułę routingu należy jawnie dodać do tego hosta frontonu, nawet jeśli została już skonfigurowana dla hosta frontonu w poddomenie frontonu (*. azurefd.net), do której domena niestandardowa ma mapowanie DNS.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
- Dodaj regułę routingu z domeny niestandardowej do żądanej puli zaplecza.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Żądanie do nazwy hosta frontonu zwraca kod stanu 404

### <a name="symptom"></a>Objaw
- Utworzono drzwiczki z przodu i skonfigurowano hosta frontonu, pulę zaplecza z co najmniej jednym zaplecem oraz regułę routingu łączącą hosta frontonu z pulą zaplecza. Zawartość nie jest dostępna podczas wysyłania żądania do skonfigurowanego hosta frontonu, ponieważ zwracany jest kod stanu HTTP 404.

### <a name="cause"></a>Przyczyna
Istnieje kilka możliwych przyczyn tego symptomu:
 - Zaplecze nie jest publicznym zaplecem i nie jest widoczne dla usługi front-drzwi.

- Zaplecze jest nieprawidłowo skonfigurowana, co powoduje, że usługa front-drzwi wysyła niewłaściwe żądanie (to oznacza, że zaplecze akceptuje tylko protokół HTTP, ale nie sprawdzono zaznaczenia opcji zezwalania na połączenia HTTPS, dlatego drzwi przed próbami przekazują żądania HTTPS).
- Zaplecze odrzuca nagłówek hosta, który został przekazany z żądaniem do zaplecza.
- Konfiguracja zaplecza nie została jeszcze w pełni wdrożona.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
1. Czas wdrożenia
    - Upewnij się, że w ciągu 10 minut zainstalowano konfigurację do wdrożenia.

2. Sprawdź ustawienia zaplecza
   - Przejdź do puli zaplecza, do której żądanie powinno być routowane (zależnie od tego, jak masz skonfigurowaną regułę routingu) i sprawdź, czy _typ hosta zaplecza_ i nazwa hosta zaplecza są poprawne. Jeśli zaplecze jest hostem niestandardowym, upewnij się, że został wpisany poprawnie. 

   - Sprawdź porty HTTP i HTTPS. W większości przypadków 80 i 443 (odpowiednio) są poprawne i nie będą wymagane żadne zmiany. Istnieje jednak możliwość, że zaplecze nie skonfigurowano w ten sposób i nasłuchuje na innym porcie.

     - Sprawdź _nagłówek hosta zaplecza_ skonfigurowany na potrzeby zaplecza, do którego powinna być kierowanie hosta frontonu. W większości przypadków ten nagłówek powinien być taki sam jak _Nazwa hosta zaplecza_. Jednak nieprawidłowa wartość może spowodować różne kody stanu 4xx HTTP, jeśli zaplecze oczekuje czegoś innego. Jeśli wprowadzasz adres IP zaplecza, może być konieczne ustawienie _nagłówka hosta zaplecza_ na nazwę hosta zaplecza.


3. Sprawdź ustawienia reguły routingu
     - Przejdź do reguły routingu, która powinna kierować z danej nazwy hosta frontonu do puli zaplecza. Upewnij się, że akceptowane protokoły są prawidłowo skonfigurowane lub jeśli nie, upewnij się, że protokół z przodu zostanie użyty podczas przesyłania dalej żądania. _Akceptowane protokoły_ decydują o tym, które żądania powinny być akceptowane, a _Protokół przekazywania_ określa, jakiego protokołu należy użyć do przesyłania dalej żądania do zaplecza.
          - Jeśli na przykład zaplecze akceptuje tylko żądania HTTP, następujące konfiguracje byłyby prawidłowe:
               - _Akceptowane protokoły_ to http i https. _Protokół przekazywania_ to http. Żądanie dopasowania nie będzie działało, ponieważ HTTPS jest dozwolonym protokołem, a jeśli żądanie zostało odebrane jako HTTPS, przód drzwiczki spróbuje przesłać je dalej przy użyciu protokołu HTTPS.

               - _Akceptowane protokoły_ to http. _Protokół przekazywania_ jest zgodny z żądaniem lub https.

   - Ponowne _Zapisywanie adresów URL_ jest domyślnie wyłączone i należy używać tego pola tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych przez zaplecza, które mają być dostępne. Po wyłączeniu drzwi do przodu przekażą tę samą ścieżkę żądania, która otrzymuje. Istnieje możliwość, że to pole jest błędnie skonfigurowane i drzwi przede wszystkim żądają zasobu od zaplecza, który nie jest dostępny, co zwraca kod stanu HTTP 404.

