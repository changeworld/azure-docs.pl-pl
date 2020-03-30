---
title: Obsługa protokołu dla nagłówków HTTP w drzwiach frontowych platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano protokoły nagłówka HTTP, które obsługują drzwi frontowe.
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
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471680"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Obsługa protokołu dla nagłówków HTTP w drzwiach frontowych platformy Azure
W tym artykule opisano protokół, który drzwiami frontowymi obsługują części ścieżki wywołania (zobacz obraz). Poniższe sekcje zawierają więcej informacji na temat nagłówków HTTP obsługiwanych przez drzwi frontowe.

![Protokół nagłówków HTTP drzwi y frontowej usługi Azure][1]

>[!IMPORTANT]
>Drzwi frontowe nie certyfikują żadnych nagłówków HTTP, które nie są tutaj udokumentowane.

## <a name="client-to-front-door"></a>Od klienta do drzwi wejściowych
Drzwi frontowe akceptuje większość nagłówków z żądania przychodzącego bez modyfikowania ich. Niektóre zarezerwowane nagłówki są usuwane z żądania przychodzącego, jeśli są wysyłane, w tym nagłówki z prefiksem X-FD-*.

## <a name="front-door-to-backend"></a>Drzwi frontowe do wewnętrznej bazy danych

Drzwi frontowe zawierają nagłówki z żądania przychodzącego, chyba że zostaną usunięte z powodu ograniczeń. Drzwi frontowe dodaje również następujące nagłówki:

| Nagłówek  | Przykład i opis |
| ------------- | ------------- |
| Via |  Przez: 1.1 Azure </br> Drzwi frontowe dodaje wersję HTTP klienta, a następnie *platformy Azure* jako wartość dla Via nagłówka. Ten nagłówek wskazuje wersję HTTP klienta i że drzwiami frontowymi był odbiorcą pośrednim dla żądania między klientem a wewnętrznej bazy danych.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Reprezentuje adres IP klienta skojarzony z przetwarzanym żądaniem. Na przykład żądanie pochodzące z serwera proxy może dodać nagłówek X-Forwarded-For, aby wskazać adres IP oryginalnego wywołującego. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Reprezentuje adres IP gniazda skojarzony z połączeniem TCP, z którego pochodzi bieżące żądanie. Adres IP klienta żądania może nie być równy jego adresowi IP gniazda, ponieważ może być dowolnie zastępowany przez użytkownika.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez drzwiami frontowymi. Służy do wyszukiwania dzienników dostępu i krytyczne dla rozwiązywania problemów.|
| X-Azure-RequestChain |  X-Azure-RequestChain: przeskok=1 </br> Nagłówek, który drzwiami frontowymi używa do wykrywania pętli żądań, a użytkownicy nie powinni przyjmować zależności od niego. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Pole nagłówka HTTP X-Forwarded-For (XFF) często identyfikuje źródłowy adres IP klienta łączącego się z serwerem sieci web za pośrednictwem serwera proxy HTTP lub modułu równoważenia obciążenia. Jeśli istnieje istniejący nagłówek XFF, następnie drzwiami frontowymi dołącza ip gniazda klienta do niego lub dodaje nagłówek XFF z ip gniazda klienta. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Pole nagłówka HTTP hosta X-Forwarded-Host jest powszechną metodą używaną do identyfikowania oryginalnego hosta żądanego przez klienta w nagłówku żądania HTTP hosta. Dzieje się tak, ponieważ nazwa hosta z drzwiami frontowymi może się różnić w przypadku serwera wewnętrznej bazy danych obsługującego żądanie. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Pole nagłówka HTTP X-Forwarded-Proto jest często używane do identyfikowania początkowego protokołu żądania HTTP, ponieważ drzwiami frontowymi, oparte na konfiguracji, mogą komunikować się z zapleczem przy użyciu protokołu HTTPS. Jest to prawdą, nawet jeśli żądanie do odwrotnego serwera proxy jest HTTP. |
| X-FD-ZdrowieProbe | X-FD-HealthProbe HTTP pole nagłówka służy do identyfikacji sondy kondycji z drzwiami frontowymi. Jeśli ten nagłówek ustawiony na 1, żądanie jest sonda kondycji. Można użyć, gdy chcesz ścisłego dostępu z określonych drzwi frontowych z X-Forwarded-Host pola nagłówka. |

## <a name="front-door-to-client"></a>Drzwi wejściowe do klienta

Wszystkie nagłówki wysyłane do drzwi frontowych z wewnętrznej bazy danych są również przekazywane do klienta. Poniżej przedstawiono nagłówki wysyłane z drzwi frontowych do klientów.

| Nagłówek  | Przykład |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Jest to unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez drzwiami frontowymi. Ma to kluczowe znaczenie dla rozwiązywania problemów, ponieważ jest on używany do wyszukiwania dzienników dostępu.|

## <a name="next-steps"></a>Następne kroki

- [Tworzenie usługi Front Door](quickstart-create-front-door.md)
- [Jak działają drzwi wejściowe](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
