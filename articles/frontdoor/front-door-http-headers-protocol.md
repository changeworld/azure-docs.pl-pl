---
title: Obsługa protokołu dla nagłówków HTTP w usłudze Azure front-drzwi | Microsoft Docs
description: W tym artykule opisano protokoły nagłówkowe protokołu HTTP obsługiwane przez usługę front-drzwi.
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
ms.openlocfilehash: 7c77527b7300c1149e96c94a4dbe122da226ac6d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382566"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Obsługa protokołu dla nagłówków HTTP w usłudze Azure front-drzwi
W tym artykule opisano protokół, który obsługuje usługa front-drzwi z częściami ścieżki wywołania (Zobacz obraz). Poniższe sekcje zawierają więcej informacji na temat nagłówków HTTP obsługiwanych przez usługę front-drzwi.

![Protokół nagłówków HTTP usługi Azure front-drzwi][1]

>[!IMPORTANT]
>Usługa front-drzwi nie zaświadcza żadnych nagłówków HTTP, które nie są udokumentowane w tym miejscu.

## <a name="client-to-front-door-service"></a>Klient do usługi front-drzwi
Usługa front-drzwi akceptuje większość nagłówków z przychodzącego żądania bez ich modyfikowania. Niektóre zarezerwowane nagłówki są usuwane z przychodzącego żądania, jeśli są wysyłane, włącznie z nagłówkami z prefiksem X-FD-*.

## <a name="front-door-service-to-backend"></a>Usługa front-drzwi do zaplecza

Usługa front-drzwi obejmuje nagłówki z przychodzącego żądania, chyba że zostaną usunięte ze względu na ograniczenia. Drzwi z przodu również dodaje następujące nagłówki:

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| Korzystając |  Za pośrednictwem: 1,1 Azure </br> Przód drzwi dodaje wersję HTTP klienta, a następnie *platformę Azure* jako wartość dla nagłówka Via. Oznacza to, że wersja HTTP klienta i drzwi przednich były pośrednim odbiorcą dla żądania między klientem a zapleczem.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Reprezentuje adres IP klienta skojarzony z przetwarzanym żądaniem. Na przykład żądanie pochodzące z serwera proxy może dodać nagłówek X-Forward-for, aby wskazać adres IP oryginalnego obiektu wywołującego. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Reprezentuje adres IP gniazda skojarzonego z połączeniem TCP, z którego pochodzi bieżące żądanie. Adres IP klienta żądania może nie być taki sam jak adres IP gniazda, ponieważ może zostać zawolnie nadpisany przez użytkownika.|
| X-Azure-Ref |  X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi. Jest on używany do wyszukiwania dzienników dostępu i do rozwiązywania problemów.|
| X-Azure-RequestChain |  X-Azure-RequestChain: przeskoki = 1 </br> Nagłówek, za pomocą którego przede wszystkim wykrywa pętle żądań, a użytkownicy nie powinni na niej korzystać. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Pole nagłówka HTTP X-Forwarded-For (XFF) często identyfikuje źródłowy adres IP klienta łączącego się z serwerem sieci Web za pośrednictwem serwera proxy HTTP lub modułu równoważenia obciążenia. Jeśli istnieje nagłówek XFF, następnie drzwi do przodu dołącza do niego adres IP gniazda klienta lub dodaje nagłówek XFF z adresem IP gniazda klienta. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Pole nagłówka HTTP X-forwardd-host jest wspólną metodą służącą do identyfikowania oryginalnego hosta żądanego przez klienta w nagłówku żądania HTTP hosta. Wynika to z faktu, że nazwa hosta z drzwi przednich może się różnić w przypadku serwera wewnętrznej bazy danych obsługującego żądanie. |
| X-Forwarded-Proto | X-Forwarded-proto: http </br> Pole nagłówka HTTP X-Forwarded-proto jest często używane do identyfikowania źródłowego protokołu żądania HTTP, ponieważ przód drzwi na podstawie konfiguracji mogą komunikować się z zapleczem przy użyciu protokołu HTTPS. Jest to prawdziwe, nawet jeśli żądanie do zwrotnego serwera proxy jest HTTP. |
| X-FD-HealthProbe | Pole nagłówka HTTP X-FD-HealthProbe służy do identyfikowania sondy kondycji z czołowych drzwi. Jeśli ten nagłówek ma ustawioną wartość 1, żądanie jest sondą kondycji. Można użyć, gdy chcesz ścisły dostęp z konkretnych drzwi z przodu przy użyciu pola nagłówka X-forwardd-host. |

## <a name="front-door-service-to-client"></a>Usługa front-drzwi do klienta

Wszystkie nagłówki wysyłane do przednich drzwi z zaplecza są również przekazywane do klienta. Poniżej przedstawiono nagłówki wysyłane z czołowych drzwi do klientów.

| Nagłówek  | Przykład |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> To jest unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi. Jest to ważne w przypadku rozwiązywania problemów, ponieważ służy do wyszukiwania dzienników dostępu.|

## <a name="next-steps"></a>Następne kroki

- [Utwórz drzwi tylne](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
