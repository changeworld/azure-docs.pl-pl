---
title: Usługi Azure Mobile Engagement przewodnik - wypychanych/dotarcia do rozwiązywania problemów
description: Rozwiązywanie problemów interakcji i powiadomienie użytkownika w usłudze Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bc94353e60288ba1acfb1855b3a7b416a03e73bc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Podręcznik rozwiązywania problemów dotyczących wypychania i dostarczania
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Poniżej przedstawiono możliwe problemy, które mogą się pojawić w sposób usługi Azure Mobile Engagement wysyła informacje do użytkowników.

## <a name="push-failures"></a>Błędy wypychania
### <a name="issue"></a>Problem
* Wypchnięcia nie działają (w aplikacji poza aplikacji lub obie).

### <a name="causes"></a>Powoduje, że
* Wiele razy awarii wypychania jest wskazanie, że usługi Azure Mobile Engagement, zasięg lub innego zaawansowanych funkcji usługi Azure Mobile Engagement nie jest poprawnie zintegrowana lub że uaktualnienie nie jest wymagana w zestawie SDK naprawy znany problem w nowa platforma systemu operacyjnego lub urządzenia.
* Przetestuj tylko wypychania w aplikacji i po prostu wypychanych z aplikacji do ustalenia, czy jest coś problemu z aplikacji lub w aplikacji.
* Test z interfejsu użytkownika i interfejsu API podczas rozwiązywania problemów, jakie dodatkowe informacje o błędzie jest dostępna obu tych miejscach.
* Poza aplikacją wypchnięć nie będzie działać, chyba że zarówno usługi Azure Mobile Engagement i zasięgu są zintegrowane w zestawie SDK.
* Wypchnięcia nie będzie działać, jeśli certyfikaty są nieprawidłowe lub są za pomocą vs produkcyjną. DEV poprawnie (tylko iOS). (**Uwaga:** "poza aplikacją" powiadomień wypychanych nie można dostarczać w systemach iOS, jeśli masz Programowanie (deweloperów) i wersji produkcyjnej (produkcyjną) aplikacji zainstalowany na tym samym urządzeniu, ponieważ token zabezpieczający skojarzone z certyfikatu może unieważniona przez firmę Apple. Aby rozwiązać ten problem, odinstaluj deweloperów i produkcyjną wersje aplikacji i ponownie zainstalować tylko jedną wersję na urządzeniu.)
* Poza wypychanych aplikacji liczby obsługi inaczej w różnych platform (iOS pokazuje informacje o mniej niż Android wyłączenie natywnego wypchnięć na urządzeniu, interfejsu API może zapewnić więcej informacji niż w Interfejsie użytkownika Statystyka wypychania).
* Poza aplikacją wypchnięć mogą zostać zablokowane przez klientów na poziomie systemu operacyjnego (iOS i Android).
* Poza aplikacją wypchnięć będzie wyświetlana jako wyłączona w Interfejsie użytkownika usługi Azure Mobile Engagement, jeśli nie są poprawnie zintegrowane, ale może nie powieść w trybie dyskretnym z interfejsu API.
* W aplikacji wypchnięć nie będzie działać, chyba że zarówno usługi Azure Mobile Engagement i zasięgu są zintegrowane w zestawie SDK.
* Wypchnięcia usługi GCM i ADM nie będzie działać, jeśli usługi Azure Mobile Engagement i określonego serwera są zintegrowane w zestawie SDK (tylko Android).
* W aplikacji i z aplikacji wypchnięć powinny być testowane oddzielnie do określenia, czy jest to problem wypychania lub Reach.
* W aplikacji, które wypchnięć wymagają aplikacji otwarty w celu odebrania.
* W aplikacji wypchnięć są często Instalatora, aby filtrować według znaczników informacje o opcjonalnych lub rezygnacji z aplikacji.
* Jeśli używasz kategorię niestandardową w zasięgu wyświetlać powiadomienia w aplikacji, należy wykonać poprawną cyklu powiadomienia, w przeciwnym razie powiadomienia nie może wyczyścić gdy użytkownik je zamknąć.
* Jeśli początkowo kampanii bez daty zakończenia i urządzenie odbiera w aplikacji powiadomienie, ale nie wyświetlaj go jeszcze, nadal będą użytkownika odbierać powiadomienia przy następnym zalogowaniu do aplikacji, nawet jeśli ręcznie zakończenia kampanii.
* Problemy z interfejsem API Push upewnij się, czy naprawdę chcesz (ponieważ jest to częściej stosowana jest interfejsu API Reach) za pomocą interfejsu API wypychania zamiast interfejsu API Reach i że nie są skomplikowana parametrów "ładunku" i "zgłaszający".
* Przetestowanie kampanii wypychania z obu urządzenie podłączone za pośrednictwem sieci Wi-Fi i sieci 3G, aby wyeliminować połączenia sieciowego w postaci źródłem problemów.

## <a name="push-testing"></a>Wypychanie testowania
### <a name="issue"></a>Problem
* Wypchnięcia mogą być wysyłane do określonego urządzenia oparte na identyfikatorem urządzenia.

### <a name="causes"></a>Powoduje, że
* Urządzenia testu są skonfigurowane inaczej dla każdej platformy, ale powoduje zdarzenie w aplikacji na urządzeniu testu i wyszukując identyfikator urządzenia w portalu powinny działać, aby znaleźć identyfikator urządzenia dla wszystkich platform.
* Działanie urządzeń testowych z identyfikator IDFA vs. IDFV (tylko iOS).

## <a name="push-customization"></a>Dostosowywanie wypychania
### <a name="issue"></a>Problem
* Zaawansowane wypychania zawartości elementu nie będzie działać (znaczków, pierścienia, Włącz wibrację, obraz, itp.).
* Łącza z wypchnięć nie działają (poza aplikacji w aplikacji do witryny sieci Web, do lokalizacji w aplikacji).
* Statystyki wypychania pokazują, że wypychanej nie została wysłana do osób tyle zgodnie z oczekiwaniami (zbyt wiele lub zbyt mała liczba).
* Zduplikowany i odebranych dwa razy.
* Nie można zarejestrować urządzenie testowe dla usługi Azure Mobile Engagement wypycha (za pomocą własnych produkcyjną lub deweloperów aplikacji).

### <a name="causes"></a>Powoduje, że
* Aby utworzyć łącze do określonej lokalizacji w aplikacji wymaga "kategorie" (tylko Android).
* Bezpośrednich połączeń schematy przekierowywać użytkowników w innej lokalizacji po kliknięciu przycisku powiadomienie wypychane muszą być utworzone w i zarządza bezpośrednio aplikacji i systemu operacyjnego urządzenia nie przez usługi Mobile Engagement. (**Uwaga:** poza aplikacją powiadomień nie może połączyć bezpośrednio do lokalizacji aplikacji z systemem iOS jak w przypadku systemu Android.)
* Serwery zewnętrzne obrazu muszą być można używać protokołu HTTP "GET" i "HEAD" dla szerszej Wypychanie do pracy (tylko Android).
* W kodzie można wyłączyć agenta usługi Azure Mobile Engagement, po otwarciu klawiatury i kod ponownie uaktywnić agenta usługi Azure Mobile Engagement po zamknięciu klawiatury, dzięki czemu klawiatury nie mają wpływu na wygląd powiadomienia (tylko iOS).
* Niektóre elementy nie działają w symulacji testu, ale tylko rzeczywistych kampanii (znaczków, pierścienia, Włącz wibrację, obraz, itp.).
* Brak danych po stronie serwera jest rejestrowane, gdy przycisk umożliwia wypchnięć "test". Dane są rejestrowane tylko dla kampanie wypychania prawdziwe.
* Aby wyizolować problem, rozwiązywanie problemów z: test, symulować i rzeczywistych kampanii, ponieważ każdy z nich działać nieco inaczej.
* Czas, przez użytkownika "w aplikacji" i "w dowolnym momencie" kampanii są zaplanowane do uruchomienia może mieć wpływ dostarczania cyfry od kampanii będą dostarczane tylko do użytkowników, którzy są "w aplikacji", podczas wykonywania kampanii (i użytkowników, którzy mają swoje urządzenia ustawień do odbierania powiadomień "poza aplikacją").
* Różnice między sposób obsługi systemu Android i iOS poza powiadomienia z aplikacji utrudnia bezpośrednio porównywanie wypychania danych statystycznych dotyczących systemów Android i iOS wersji aplikacji. Android zawiera więcej informacji powiadomień z poziomu systemu operacyjnego niż iOS. Android informuje, kiedy natywnych powiadomień zostało odebrane, kliknięty lub usunięte w Centrum powiadomień, ale z systemem iOS nie raportuje te informacje, chyba że powiadomienie zostanie kliknięty. 
* Głównym celem numery "wciśnięcia" różnią się od innego niż "dostarczane" numerów osiągnąć kampanii jest czy "w aplikacji" i "poza aplikacją" powiadomienia są zliczane inaczej. "W aplikacji" powiadomienia są obsługiwane przez usługi Mobile Engagement, ale "poza aplikacją" powiadomienia są obsługiwane przez Centrum powiadomień w wersji systemu operacyjnego urządzenia.

## <a name="push-targeting"></a>Wypychanie elementów docelowych
### <a name="issue"></a>Problem
* Wbudowane elementów docelowych nie działa zgodnie z oczekiwaniami.
* Informacje o aplikacji Tag przeznaczonych dla nie działa zgodnie z oczekiwaniami.
* Lokalizacja geograficzna elementów docelowych nie działa zgodnie z oczekiwaniami.
* Opcje języka nie działają zgodnie z oczekiwaniami.

### <a name="causes"></a>Powoduje, że
* Upewnij się, że zostały przekazane tagi informacje o aplikacji za pomocą usługi Azure Mobile Engagement w interfejsie użytkownika lub interfejsu API.
* Ograniczanie szybkość wypychania lub przydział wypychania na poziomie aplikacji lub ograniczenie odbiorców na poziomie kampanii można zapobiec osoby odbieranie wypychanych określonych nawet wtedy, gdy spełniają innych kryteriów określania wartości docelowej. 
* Ustawienie "Język" jest inny niż docelowy na podstawie kraju lub ustawień regionalnych, które może również inne niż docelowy na podstawie lokalizacji geograficznej na podstawie lokalizacji telefonu lub GPS lokalizacji.
* Komunikat w języku"domyślne" są wysyłane do dowolnego klienta, który nie ma swojego urządzenia, wartość alternatywne języki, które określisz.

## <a name="push-scheduling"></a>Wypychanie planowania
### <a name="issue"></a>Problem
* Planowanie wypychania nie działa zgodnie z oczekiwaniami (wysyłane zbyt wczesny lub opóźnione).

### <a name="causes"></a>Powoduje, że
* Strefy czasowe można problemy związane z planowaniem, zwłaszcza w przypadku korzystania z strefa czasowa użytkownika końcowego.
* Funkcje zaawansowane wypychania można opóźnić wypchnięć.
* Celem oparte na numer telefonu, którego ustawienia (zamiast znaczniki informacje o aplikacji) może opóźnić wypycha od usługi Azure Mobile Engagement może być konieczne żądania danych od czasu rzeczywistego telefonu przed wysłaniem wypychania.
* Kampanie utworzone bez daty zakończenia przechowywania naciśnięcie lokalnie na urządzeniu i pokazać przy następnym otwarciu aplikacji, nawet jeśli ręcznie zakończenia kampanii.
* Uruchamianie więcej niż jednej kampanii w tym samym czasie może potrwać dłuższy czas, aby przeskanować bazy użytkowników (spróbuj tylko rozpocząć jednej kampanii w czasie składającą się z maksymalnie cztery, również docelowych tylko aktywni użytkownicy, tak aby stary użytkownicy nie muszą być skanowane).
* Jeśli używasz opcji "Ignoruj odbiorców, wypychane będą wysyłane do użytkowników za pośrednictwem interfejsu API" w sekcji "Kampanii" kampanii Reach kampanii nie będzie automatycznie wysyłać, należy wysłać go ręcznie za pośrednictwem interfejsu API Reach.
* Jeśli używasz kategorię niestandardową w zasięgu wyświetlać powiadomienia w aplikacji, należy wykonać poprawną cyklu powiadomienia, w przeciwnym razie powiadomienia nie może wyczyścić gdy użytkownik je zamknąć.

