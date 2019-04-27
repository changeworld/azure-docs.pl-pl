---
title: Protokół Obsługa nagłówków HTTP w usłudze Azure Service wejściu | Dokumentacja firmy Microsoft
description: W tym artykule opisano protokołów nagłówka HTTP, które obsługuje usługę drzwi wejściowe.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736648"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Obsługa protokołów dla nagłówków HTTP w usłudze Azure Service drzwi
W tym artykule przedstawiono protokół, który usługa drzwiami frontowymi obsługuje części ścieżki wywołania (zobacz obraz). Poniższe sekcje zawierają więcej informacji o nagłówkach HTTP obsługiwane przez usługę wejściu.

![Azure protokołu nagłówki HTTP usługi drzwiami frontowymi][1]

>[!IMPORTANT]
>Usługa drzwiami frontowymi nie Zatwierdź wszystkie nagłówki HTTP, które nie są opisane tutaj.

## <a name="client-to-front-door-service"></a>Klient usługi drzwi
Usługa drzwiami frontowymi akceptuje większość nagłówków z żądania przychodzącego, bez modyfikacji. Niektóre zastrzeżone nagłówki są usuwane z żądania przychodzącego, jeśli wysyłane, włącznie z nagłówkami z X - FD-* prefiks.

## <a name="front-door-service-to-backend"></a>Usługa wejściu do wewnętrznej bazy danych

Usługa drzwiami frontowymi zawiera nagłówki z przychodzącego żądania, chyba że usunięty ze względu na ograniczenia. Drzwiami frontowymi dodaje również następujące nagłówki:

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| Przez |  Via: 1.1 azure </br> Drzwiami frontowymi dodaje wersji klienta HTTP, następuje *Azure* jako wartość nagłówka Via. Oznacza to, wersja HTTP klienta i tego drzwiami frontowymi była pośrednich adresata dla żądania między klientem i wewnętrznej bazy danych.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Reprezentuje adres IP klienta, które są skojarzone z żądaniem przetwarzany. Na przykład żądanie pochodzące z serwera proxy może dodać nagłówek X-Forwarded-dla do wskazywania adresu IP, oryginalnym obiektu wywołującego. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Reprezentuje adres IP gniazda, które są skojarzone z połączeniem TCP, bieżącego żądania pochodzących z. Adres IP klienta dla żądania nie może być równa jego adres IP gniazda, ponieważ mogą być dowolnie zastąpione przez użytkownika.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwiami frontowymi. Służy do wyszukiwania uzyskiwanie dostępu do dzienników i rozwiązywanie problemów z bardzo ważne.|
| X-Azure-RequestChain |  X-Azure-RequestChain: przeskoki = 1 </br> Nagłówek, czy drzwi wykrywa pętli żądania, i użytkownicy nie powinna przyjmować zależności na nim. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Pola nagłówka X-Forwarded-For (XFF) HTTP identyfikuje często źródłowy adres IP klienta, nawiązywania połączenia z serwerem sieci web za pośrednictwem protokołu HTTP serwera proxy lub moduł równoważenia obciążenia. W przypadku istniejącego nagłówka XFF drzwiami frontowymi dołącza IP gniazda klienta lub dodaje nagłówek XFF z adresem IP gniazda klienta. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Pola nagłówka HTTP X-Forwarded-Host metoda jest często używany do identyfikowania oryginalnego hosta żądanego przez klienta w nagłówku żądania HTTP hosta. Jest to spowodowane nazwę hosta z drzwiami frontowymi mogą się różnić dla serwera wewnętrznej bazy danych, obsługa żądania. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Pola nagłówka HTTP X-Forwarded-Proto jest często używana do zidentyfikowania protokołu zainicjowanego żądania HTTP, ponieważ drzwiami frontowymi, na podstawie konfiguracji, może się komunikować z wewnętrznej bazy danych przy użyciu protokołu HTTPS. Ta zasada obowiązuje, nawet w przypadku żądania zwrotnego serwera proxy HTTP. |

## <a name="front-door-service-to-client"></a>Usługa wejściu do klienta

Również wszelkie nagłówki wysyłane do drzwi z wewnętrznej bazy danych są przekazywane do klienta. Dostępne są następujące nagłówki wysyłane z wejściu do klientów.

| Nagłówek  | Przykład |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jest to ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwi wejściowe. Jest to niezbędne do rozwiązywania problemów, ponieważ jest używany do wyszukiwania uzyskiwanie dostępu do dzienników.|

## <a name="next-steps"></a>Kolejne kroki

- [Utwórz drzwi](quickstart-create-front-door.md)
- [Jak działa drzwi](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png