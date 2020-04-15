---
title: Wysoka dostępność i równoważenie obciążenia — serwer proxy aplikacji usługi Azure AD
description: Jak dystrybucja ruchu działa z wdrożeniem serwera proxy aplikacji. Zawiera wskazówki dotyczące optymalizacji wydajności łącznika i równoważenia obciążenia dla serwerów zaplecza.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 992075378737552e890bd2d6fed3c519e6c62aa7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312945"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Wysoka dostępność i równoważenie obciążenia złączy i aplikacji serwera proxy aplikacji

W tym artykule wyjaśniono, jak dystrybucja ruchu działa z wdrożeniem serwera proxy aplikacji. Omówimy:

- Jak ruch jest rozmieszczany między użytkownikami i złączami, wraz ze wskazówkami dotyczącymi optymalizacji wydajności złączy

- Sposób przepływu ruchu między łącznikami i serwerami aplikacji zaplecza z zaleceniami dotyczącymi równoważenia obciążenia między wieloma serwerami zaplecza

## <a name="traffic-distribution-across-connectors"></a>Rozkład ruchu między złączami

Łączniki ustanawiają swoje połączenia na podstawie zasad wysokiej dostępności. Nie ma żadnej gwarancji, że ruch będzie zawsze równomiernie rozłożone między łącznikami i nie ma koligacji sesji. However, usage varies and requests are randomly sent to Application Proxy service instances. W rezultacie ruch jest zazwyczaj rozłożone prawie równomiernie między łącznikami. Poniższy diagram i kroki ilustrują sposób nawiązywalania połączeń między użytkownikami a łącznikami.

![Diagram przedstawiający połączenia między użytkownikami a łącznikami](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Użytkownik na urządzeniu klienckim próbuje uzyskać dostęp do aplikacji lokalnej opublikowanej za pośrednictwem serwera proxy aplikacji.
2. The request goes through an Azure Load Balancer to determine which Application Proxy service instance should take the request. Na region dostępne są dziesiątki wystąpień do zaakceptowania żądania. Ta metoda pomaga równomiernie rozprowadzać ruch między wystąpieniami usługi.
3. Żądanie jest wysyłane do [usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Usługa Service Bus sygnalizuje do dostępnego złącza. Następnie łącznik odbiera żądanie z usługi Service Bus.
   - W kroku 2 żądania przejść do różnych wystąpień usługi serwera proxy aplikacji, więc połączenia są bardziej prawdopodobne, aby być wykonane z różnych łączników. W rezultacie złącza są prawie równomiernie używane w grupie.
5. Łącznik przekazuje żądanie do serwera zaplecza aplikacji. Następnie aplikacja wysyła odpowiedź z powrotem do łącznika.
6. Łącznik kończy odpowiedź, otwierając połączenie wychodzące z wystąpieniem usługi, z którego pochodzi żądanie. Następnie to połączenie jest natychmiast zamknięte. Domyślnie każdy łącznik jest ograniczony do 200 równoczesnych połączeń wychodzących.
7. Odpowiedź jest następnie przekazywane z powrotem do klienta z wystąpienia usługi.
8. Kolejne żądania z tego samego połączenia powtarzają powyższe kroki.

Aplikacja często ma wiele zasobów i otwiera wiele połączeń po załadowaniu. Każde połączenie przechodzi przez powyższe kroki, aby zostać przydzielonym do wystąpienia usługi, wybierz nowy dostępny łącznik, jeśli połączenie nie zostało jeszcze sparowane ze łącznikiem.


## <a name="best-practices-for-high-availability-of-connectors"></a>Najważniejsze wskazówki dotyczące wysokiej dostępności złączy

- Ze względu na sposób, w jaki ruch jest rozmieszczany między łącznikami w celu uzyskania wysokiej dostępności, ważne jest, aby zawsze mieć co najmniej dwa łączniki w grupie łączników. Preferowane są trzy łączniki, aby zapewnić dodatkowy bufor między złączami. Aby określić prawidłową liczbę potrzebnych łączników, postępuj zgodnie z dokumentacją planowania pojemności.

- Umieść łączniki na różnych połączeniach wychodzących, aby uniknąć pojedynczego punktu awarii. Jeśli łączniki używają tego samego połączenia wychodzącego, problem z siecią z połączeniem może mieć wpływ na wszystkie łączniki używające go.

- Należy unikać wymuszania złącza do ponownego uruchamiania po podłączeniu do aplikacji produkcyjnych. Może to negatywnie wpłynąć na rozkład ruchu między łącznikami. Ponowne uruchamianie łączników powoduje, że więcej łączników jest niedostępnych i wymusza połączenia z pozostałym dostępnym łącznikiem. Rezultatem jest nierównomierne użycie złączy początkowo.

- Unikaj wszystkich form inspekcji wbudowanej w wychodzącej komunikacji TLS między łącznikami a platformą Azure. Ten typ kontroli wbudowanej powoduje degradację przepływu komunikacji.

- Upewnij się, że automatyczne aktualizacje są uruchomione dla łączników. Jeśli usługa aktualizacji łącznika serwera proxy aplikacji jest uruchomiona, łączniki są aktualizowane automatycznie i odbierają najnowsze uaktualnione. Jeśli nie widzisz usługi aktualizacji łączników na serwerze, musisz ponownie zainstalować łącznik, aby uzyskać wszelkie aktualizacje.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Przepływ ruchu między łącznikami a serwerami aplikacji zaplecza

Innym kluczowym obszarem, w którym wysoka dostępność jest czynnikiem, jest połączenie między łącznikami a serwerami zaplecza. Gdy aplikacja jest publikowana za pośrednictwem serwera proxy aplikacji usługi Azure AD, ruch od użytkowników do aplikacji przepływa przez trzy przeskoki:

1. Użytkownik łączy się z publicznym punktem końcowym usługi azure ad application proxy na platformie Azure. Połączenie jest nawiązywało między źródłowym adresem IP klienta (publicznym) klienta a adresem IP punktu końcowego serwera proxy aplikacji.
2. Łącznik serwera proxy aplikacji pobiera żądanie HTTP klienta z usługi proxy aplikacji.
3. Łącznik serwera proxy aplikacji łączy się z aplikacją docelową. Łącznik używa własnego adresu IP do ustanawiania połączenia.

![Diagram użytkownika łączącego się z aplikacją za pośrednictwem serwera proxy aplikacji](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Forwarded-For uwagi pola nagłówka
W niektórych sytuacjach (takich jak inspekcja, równoważenie obciążenia itp.), udostępnianie źródłowego adresu IP klienta zewnętrznego ze środowiskiem lokalnym jest wymagane. Aby rozwiązać to wymaganie, łącznik serwera proxy aplikacji usługi Azure AD dodaje pole nagłówka X-Forwarded-For z źródłowym adresem IP klienta (publicznym) do żądania HTTP. Odpowiednie urządzenie sieciowe (moduł równoważenia obciążenia, zapora sieciowa) lub serwer www lub aplikacja zaplecza mogą następnie odczytywać i wykorzystywać informacje.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Najważniejsze wskazówki dotyczące równoważenia obciążenia między wieloma serwerami aplikacji
Gdy grupa łączników, która jest przypisana do aplikacji Proxy aplikacji ma dwa lub więcej łączników i używasz aplikacji sieci web zaplecza na wielu serwerach (farmie serwerów), wymagana jest dobra strategia równoważenia obciążenia. Dobra strategia gwarantuje, że serwery odbierają żądania klientów równomiernie i zapobiega nadmiernemu lub niedostatecznie wykorzystywaniu serwerów w farmie serwerów.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenariusz 1: Aplikacja zaplecza nie wymaga trwałości sesji
Najprostszym scenariuszem jest, gdy aplikacja sieci web zaplecza nie wymaga lepkości sesji (trwałość sesji). Każde żądanie od użytkownika może być obsługiwane przez wystąpienie dowolnej aplikacji zaplecza w farmie serwerów. Można użyć modułu równoważenia obciążenia warstwy 4 i skonfigurować go bez koligacji. Niektóre opcje obejmują równoważenie obciążenia sieci firmy Microsoft i moduł równoważenia obciążenia platformy Azure lub moduł równoważenia obciążenia innego dostawcy. Alternatywnie można skonfigurować system DNS okrężny.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenariusz 2: Aplikacja zaplecza wymaga trwałości sesji
W tym scenariuszu zaplecza aplikacji sieci web wymaga lepkości sesji (trwałość sesji) podczas sesji uwierzytelnionej. Wszystkie żądania od użytkownika muszą być obsługiwane przez wystąpienie aplikacji zaplecza, które działa na tym samym serwerze w farmie serwerów.
Ten scenariusz może być bardziej skomplikowane, ponieważ klient zwykle ustanawia wiele połączeń z usługą serwera proxy aplikacji. Żądania za różnych połączeń mogą docierać do różnych łączników i serwerów w farmie. Ponieważ każdy łącznik używa własnego adresu IP dla tej komunikacji, moduł równoważenia obciążenia nie może zapewnić lepkości sesji na podstawie adresu IP łączników. Nie można również użyć źródłowego koligacji IP.
Oto kilka opcji dla scenariusza 2:

- Opcja 1: Oprzeć trwałość sesji na plik cookie sesji ustawiony przez moduł równoważenia obciążenia. Ta opcja jest zalecana, ponieważ umożliwia bardziej równomierne rozłożenie obciążenia między serwery zaplecza. Wymaga modułu równoważenia obciążenia warstwy 7 z tą funkcją, która może obsługiwać ruch HTTP i zakończyć połączenie TLS. Można użyć bramy aplikacji platformy Azure (koligacji sesji) lub modułu równoważenia obciążenia od innego dostawcy.

- Opcja 2: Oparcie trwałości sesji na polu nagłówka X-Forwarded-For. Ta opcja wymaga modułu równoważenia obciążenia warstwy 7 z tą funkcją, która może obsługiwać ruch HTTP i zakończyć połączenie TLS.  

- Opcja 3: Skonfiguruj aplikację zaplecza, aby nie wymagać trwałości sesji.

Zapoznaj się z dokumentacją dostawcy oprogramowania, aby zapoznać się z wymaganiami dotyczącymi równoważenia obciążenia aplikacji zaplecza.

## <a name="next-steps"></a>Następne kroki

- [Włącz serwer proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
- [Włączanie dostępu warunkowego](application-proxy-integrate-with-sharepoint-server.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)
- [Dowiedz się, jak architektura usługi Azure AD obsługuje wysoką dostępność](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
