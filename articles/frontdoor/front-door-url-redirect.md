---
title: Azure Front drzwiczki Service — adres URL przekierowania | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak Azure drzwiami frontowymi usługa obsługuje przekierowywanie adresu URL ich trasy, jeśli skonfigurowane.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332897"
---
# <a name="url-redirect"></a>Adres URL przekierowania
Usługa drzwiami frontowymi Azure umożliwia przekierowywanie ruchu. Można przekierować ruch na wielu poziomach (protokół, nazwy hosta, ścieżki, ciąg zapytania), a wszystkie funkcje można skonfigurować dla poszczególnych mikrousług przekierowania jest oparty na ścieżkach. Upraszcza to konfigurację aplikacji, optymalne wykorzystanie zasobów i obsługuje nowe scenariusze przekierowywania w tym globalnym i opartego na ścieżkach przekierowania.
</br>

![Usługa Azure drzwiami frontowymi adres URL przekierowania][1]

## <a name="redirection-types"></a>Typy przekierowania
Typ przekierowania ustawia kod stanu odpowiedzi dla klientów, zrozumienie przeznaczenia przekierowania. Obsługiwane są następujące typy przekierowania:

- **301 (trwale przeniesiona)** : Wskazuje, czy zasób docelowy został przypisany nowy identyfikator URI trwałe i odwołania do tego zasobu powinna użyć jednej z ujęty identyfikatorów URI. Na użytek 301 kod stanu HTTP do przekierowania protokołu HTTPS. 
- **(Znaleziono) 302**: Wskazuje, że zasób docelowy znajduje się tymczasowo pod inny identyfikator URI. Ponieważ przekierowania może zostać zmieniony czasami, klient powinny nadal używać identyfikatora URI żądania obowiązujące dla kolejnych żądań.
- **307 (Przekierowanie tymczasowe)** : Wskazuje, czy zasób docelowy znajduje się chwilowo pod inny identyfikator URI i agent użytkownika nie może zmienić metodę żądania, jeśli wykonuje automatyczne przekierowanie do tego identyfikatora URI. Ponieważ przekierowania mogą się zmieniać wraz z upływem czasu, klient powinny nadal korzystać z oryginalnego identyfikatora URI żądania obowiązujące dla kolejnych żądań.
- **308 (trwałe przekierowanie)** : Wskazuje, czy zasób docelowy został przypisany nowy identyfikator URI trwałe i odwołania do tego zasobu powinna użyć jednej z ujęty identyfikatorów URI. Klienci z linkiem, edytowania, powinna automatycznie Skonsoliduj odwołań do skutecznego identyfikator URI żądania do jednego lub więcej nowych odwołań wysłanych przez serwer, jeśli jest to możliwe.

## <a name="redirection-protocol"></a>Przekierowania protokołu
Możesz ustawić protokół, który będzie używany dla przekierowania. Pozwala to na jedną z najbardziej typowe przypadki użycia funkcji przekierowania, który polega na ustawieniu protokołu HTTP do przekierowania protokołu HTTPS.

- **Tylko HTTPS**: Skonfiguruj protokół HTTPS, jeśli chcesz przekierowywanie ruchu z protokołu HTTP do HTTPS. Usługa drzwiami frontowymi zaleca się, że zawsze należy ustawić przekierowywanie HTTPS tylko.
- **Tylko HTTP**: Żądanie przychodzące to przekierowania HTTP. Użyj tej wartości, tylko wtedy, gdy chcesz zachować swoje ruch HTTP, niezaszyfrowane.
- **Żądanie dopasowanie**: Ta opcja spowoduje zachowanie protokół używany przez żądania przychodzącego. Tak żądanie HTTP pozostaje HTTP i żądania HTTPS pozostaje przekierowania post protokołu HTTPS.

## <a name="destination-host"></a>Host docelowy
W ramach konfigurowania routingu przekierowania możesz również zmienić nazwę hosta lub domenę dla żądania przekierowania. Można ustawić tego pola, aby zmienić nazwę hosta, adres URL przekierowania lub w przeciwnym razie Zachowaj nazwę hosta z żądania przychodzącego. Tak, za pomocą tego pola można przekierować wszystkie żądania wysyłane https://www.contoso.com/ * do https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Ścieżka docelowa
W przypadkach, w których chcesz zastąpić segmentu ścieżki adresu URL jako część przekierowania możesz ustawić to pole z nową wartością ścieżki. W przeciwnym razie można zachować wartość ścieżki jako część przekierowania. Tak, za pomocą tego pola, można przekierować wszystkie żądania wysyłane do https://www.contoso.com/ * do https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Parametry ciągu zapytania
Możesz również zastąpić parametry ciągu zapytania w adresie URL przekierowane. Aby zastąpić wszelkie istniejące ciąg zapytania z adresu URL żądania przychodzącego, Ustaw to pole na "Replace", a następnie ustaw odpowiednią wartość. W przeciwnym razie można zachować oryginalny zestaw ciągów zapytań przez ustawienie pola "Preserve". Na przykład za pomocą tego pola można przekierować cały ruch wysyłany do https://www.contoso.com/foo/bar do https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Fragment docelowego
Fragment docelowy to część adresu URL po znaku "#", zwykle używane przez przeglądarki powiązana z określonej sekcji na stronie. Można ustawić tego pola, aby dodać fragmentu adresu URL przekierowania.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png