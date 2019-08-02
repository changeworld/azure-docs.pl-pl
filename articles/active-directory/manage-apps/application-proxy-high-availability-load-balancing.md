---
title: Wysoka dostępność i równoważenie obciążenia dla serwer proxy aplikacji usługi Azure AD platformy Azure | Microsoft Docs
description: Jak dystrybucja ruchu współdziała z wdrożeniem serwera proxy aplikacji. Zawiera wskazówki dotyczące optymalizowania wydajności łącznika i korzystania z równoważenia obciążenia dla serwerów zaplecza.
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
ms.date: 07/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff5f814eac095770990ecbc0c4b01d2e0cc6f931
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667197"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Wysoka dostępność i równoważenie obciążenia łączników i aplikacji serwera proxy aplikacji

W tym artykule wyjaśniono, jak dystrybucja ruchu współdziała z wdrożeniem serwera proxy aplikacji. Będziemy omawiać następujące kwestie:

- Sposób dystrybucji ruchu między użytkownikami i łącznikami wraz ze wskazówkami dotyczącymi optymalizacji wydajności łącznika

- Sposób przepływu ruchu między łącznikami i serwerami aplikacji zaplecza z zaleceniami dotyczącymi równoważenia obciążenia między wieloma serwerami zaplecza

## <a name="traffic-distribution-across-connectors"></a>Rozkład ruchu między łącznikami

Łączniki ustanawiają połączenia w oparciu o zasady wysokiej dostępności. Nie ma gwarancji, że ruch będzie zawsze równomiernie dystrybuowany między łącznikami i nie istnieje koligacja sesji. Jednak użycie zmienia się i żądania są losowo wysyłane do wystąpień usługi serwera proxy aplikacji. W efekcie ruch jest zwykle rozkładany niemal nawet przez łączniki. Na poniższej ilustracji przedstawiono sposób ustanawiania połączeń między użytkownikami i łącznikami.

![Diagram przedstawiający połączenia między użytkownikami i łącznikami](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Użytkownik na urządzeniu klienckim próbuje uzyskać dostęp do aplikacji lokalnej opublikowanej za pomocą serwera proxy aplikacji.
2. Żądanie przechodzi przez Azure Load Balancer, aby określić, które wystąpienie usługi serwera proxy aplikacji powinno pobrać żądanie. Na region dostępne są dziesiątki wystąpień do zaakceptowania żądania. Ta metoda ułatwia równomierne dystrybuowanie ruchu między wystąpieniami usługi.
3. Żądanie jest wysyłane do [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Service Bus sprawdza, czy połączenie używało wcześniej istniejącego łącznika w grupie łączników. Jeśli tak, ponownie używa połączenia. Jeśli łącznik nie jest jeszcze sparowany z połączeniem, wybiera dostępny łącznik losowo, aby sygnalizować. Następnie łącznik wybiera żądanie z Service Bus.

   - W kroku 2 żądania przechodzą do różnych wystąpień usługi serwera proxy aplikacji, więc połączenia są bardziej podobne do różnych łączników. W związku z tym łączniki są prawie stosowane w grupie.

   - Połączenie jest ustanawiane tylko wtedy, gdy połączenie jest zerwane lub występuje okres bezczynności wynoszący 10 minut. Na przykład połączenie może zostać przerwane w przypadku ponownego uruchomienia maszyny lub łącznika lub zerwania przerwy w działaniu sieci.

5. Łącznik przekazuje żądanie do serwera zaplecza aplikacji. Następnie aplikacja wysyła odpowiedź z powrotem do łącznika.
6. Łącznik kończy odpowiedź, otwierając połączenie wychodzące do wystąpienia usługi, z którego pochodzi żądanie. To połączenie jest natychmiast zamykane. Domyślnie każdy łącznik jest ograniczony do 200 jednoczesnych połączeń wychodzących.
7. Odpowiedź jest następnie przenoszona z powrotem do klienta z wystąpienia usługi.
8. Kolejne żądania z tego samego połączenia powtarzają powyższe kroki, dopóki to połączenie nie zostanie zerwane lub jest bezczynne przez 10 minut.

Aplikacja często ma wiele zasobów i otwiera wiele połączeń po ich załadowaniu. Każde połączenie przechodzi przez kroki opisane powyżej, aby zostać przydzieloną do wystąpienia usługi, wybierz nowy dostępny łącznik, jeśli połączenie nie zostało jeszcze wcześniej sparowane z łącznikiem.


## <a name="best-practices-for-high-availability-of-connectors"></a>Najlepsze rozwiązania dotyczące wysokiej dostępności łączników

- Ze względu na sposób, w jaki ruch jest dystrybuowany między łącznikami w celu zapewnienia wysokiej dostępności, należy zawsze mieć co najmniej dwa łączniki w grupie łączników. Trzy łączniki są preferowane w celu zapewnienia dodatkowego buforu między łącznikami. Aby określić poprawną liczbę łączników, które są potrzebne, postępuj zgodnie z dokumentacją dotyczącą planowania pojemności.

- Umieść łączniki w różnych połączeniach wychodzących, aby uniknąć single point of failure. Jeśli łączniki korzystają z tego samego połączenia wychodzącego, problem z siecią z połączeniem może mieć wpływ na wszystkie łączniki, z których korzysta.

- Unikaj wymuszania ponownego uruchomienia łączników w przypadku połączenia z aplikacjami produkcyjnymi. Takie działanie może mieć negatywny wpływ na rozkład ruchu między łącznikami. Ponowne uruchomienie łączników powoduje, że więcej łączników jest niedostępnych i wymusza połączenia z pozostałymi dostępnymi łącznikami. Wynikiem jest nierówne użycie łączników.

- Unikaj wszystkich form inspekcji wbudowanej na wychodzącej komunikacji TLS między łącznikami i platformą Azure. Ten typ inspekcji wbudowanej powoduje spadek przepływu komunikacji.

- Pamiętaj, aby zachować aktualizacje automatyczne uruchomione dla łączników. Jeśli usługa Aktualizator łączników serwera proxy aplikacji jest uruchomiona, łączniki są automatycznie aktualizowane i otrzymują najnowsze uaktualnienia. Jeśli na serwerze nie widzisz usługi Aktualizator łączników, należy ponownie zainstalować łącznik, aby pobrać aktualizacje.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Przepływ ruchu między łącznikami i serwerami aplikacji zaplecza

Innym kluczem, gdzie wysoka dostępność jest współczynnik, jest połączenie między łącznikami i serwerami zaplecza. Gdy aplikacja jest publikowana za pośrednictwem usługi Azure serwer proxy aplikacji usługi Azure AD, ruch od użytkowników do aplikacji przechodzi przez trzy przeskoki:

1. Użytkownik nawiązuje połączenie z publicznym punktem końcowym usługi Azure serwer proxy aplikacji usługi Azure AD na platformie Azure. Połączenie jest nawiązywane między adresem IP klienta (Public) klienta i adresem IP punktu końcowego serwera proxy aplikacji.
2. Łącznik serwera proxy aplikacji pobiera żądanie HTTP klienta z usługi serwera proxy aplikacji.
3. Łącznik serwera proxy aplikacji nawiązuje połączenie z aplikacją docelową. Łącznik używa własnego adresu IP do nawiązywania połączenia.

![Diagram użytkownika łączącego się z aplikacją za pośrednictwem serwera proxy aplikacji](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-forwardd-for — zagadnienia dotyczące pól nagłówka
W niektórych sytuacjach (takich jak Inspekcja, równoważenie obciążenia itp.) Udostępnianie źródłowego adresu IP klienta zewnętrznego przy użyciu środowiska lokalnego jest wymagane. Aby rozwiązać ten wymóg, łącznik usługi Azure serwer proxy aplikacji usługi Azure AD dodaje pole X-forwardd-for z źródłowym adresem IP klienta (Public) do żądania HTTP. Odpowiednie urządzenie sieciowe (moduł równoważenia obciążenia, Zapora) lub serwer sieci Web lub aplikacja zaplecza mogą następnie odczytywać i korzystać z tych informacji.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Najlepsze rozwiązania dotyczące równoważenia obciążenia między wieloma serwerami aplikacji
Gdy grupa łączników przypisana do aplikacji serwera proxy aplikacji ma dwa lub więcej łączników, a uruchomiona jest aplikacja sieci Web zaplecza na wielu serwerach (farmie serwerów), wymagana jest właściwa strategia równoważenia obciążenia. Dobrą strategią jest zagwarantowanie, że serwery będą odbierać żądania klientów równomiernie i uniemożliwiają użycie serwerów w farmie serwerów.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenariusz 1: Aplikacja zaplecza nie wymaga trwałości sesji
Najprostszym scenariuszem jest to, że aplikacja sieci Web zaplecza nie wymaga lepkość sesji (trwałości sesji). Każde żądanie od użytkownika może być obsługiwane przez dowolne wystąpienie aplikacji zaplecza w farmie serwerów. Możesz użyć modułu równoważenia obciążenia warstwy 4 i skonfigurować go bez koligacji. Niektóre opcje obejmują Równoważenie obciążenia sieciowego firmy Microsoft i Azure Load Balancer lub moduł równoważenia obciążenia od innego dostawcy. Alternatywnie można skonfigurować usługę DNS z działaniem okrężnym.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenariusz 2: Aplikacja zaplecza wymaga trwałości sesji
W tym scenariuszu aplikacja sieci Web zaplecza wymaga lepkość sesji (trwałości sesji) podczas sesji uwierzytelnionej. Wszystkie żądania od użytkownika muszą być obsługiwane przez wystąpienie aplikacji zaplecza, które działa na tym samym serwerze w farmie serwerów.
Ten scenariusz może być bardziej skomplikowany, ponieważ klient zazwyczaj nawiązuje wiele połączeń z usługą serwera proxy aplikacji. Żądania przez różne połączenia mogą pojawić się na różnych łącznikach i serwerach w farmie. Ponieważ każdy łącznik używa własnego adresu IP do komunikacji, moduł równoważenia obciążenia nie może zapewnić lepkość sesji na podstawie adresu IP łączników. Koligacja źródłowego adresu IP nie może być używana.
Poniżej przedstawiono kilka opcji scenariusza 2:

- Option 1: Zapodstawową trwałość sesji w pliku cookie sesji ustawionym przez moduł równoważenia obciążenia. Ta opcja jest zalecana, ponieważ umożliwia równomierne rozłożenie obciążenia między serwerami zaplecza. Wymaga modułu równoważenia obciążenia warstwy 7 z tą możliwością, który może obsłużyć ruch HTTP i zakończyć połączenie SSL. Możesz użyć usługi Azure Application Gateway (koligacja sesji) lub modułu równoważenia obciążenia od innego dostawcy.

- Opcja 2: Podstawę trwałości sesji dla pola nagłówka X-forwardd-for. Ta opcja wymaga modułu równoważenia obciążenia warstwy 7 z tą możliwością, która może obsługiwać ruch HTTP i przerywa połączenie SSL.  

- Opcja 3: Skonfiguruj aplikację zaplecza, aby nie wymagała trwałości sesji.

Zapoznaj się z dokumentacją dostawcy oprogramowania, aby poznać wymagania dotyczące równoważenia obciążenia aplikacji zaplecza.

## <a name="next-steps"></a>Następne kroki

- [Włącz serwer Proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
- [Włącz dostęp warunkowy](application-proxy-integrate-with-sharepoint-server.md)
- [Rozwiązywanie problemów z serwerem Proxy aplikacji](application-proxy-troubleshoot.md)
