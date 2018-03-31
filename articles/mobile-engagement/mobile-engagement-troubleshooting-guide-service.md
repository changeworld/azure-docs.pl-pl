---
title: Usługi Azure Mobile Engagement przewodnik — usługa rozwiązywania problemów
description: Rozwiązywanie problemów z przewodniki dotyczące usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 64bd4849e3bb9a37fb32940548d5ecf01fffe0ca
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-service-issues"></a>Przewodnik rozwiązywania problemów dotyczących usługi
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Poniżej przedstawiono możliwe problemy, które mogą się pojawić w sposób uruchamiania usługi Azure Mobile Engagement.

## <a name="service-outages"></a>Awarie usługi
### <a name="issue"></a>Problem
* Problemy, które wydają się być spowodowane awarie usługi systemu Azure Mobile Engagement.

### <a name="causes"></a>Powoduje, że
* Problemy, które wydają się być spowodowane przez usługi Azure Mobile Engagement usługi awarii może być spowodowana przez kilka różnych problemów:
  * Izolowane problemy, które pierwotnie pojawiają się systemowych do wszystkich usługi Azure Mobile Engagement
  * Znane problemy spowodowane awarii serwera (nie zawsze pokazuje stan serwera):
  * Planowanie opóźnienia, błędy docelowych, problemy aktualizacja wskaźnika, statystyki zatrzymania zbierania wypychania przestanie działać, interfejsu API przestają działać, nowe aplikacje lub użytkowników nie można utworzyć, błędy DNS i błędy przekroczenia limitu czasu w aplikacji interfejsu użytkownika, interfejsu API lub na urządzeniu.
  * Awarie zależności w chmurze [stan usługi Azure](http://status.azure.com/)
  * Awarie zależności usług (PNS) powiadomień wypychanych
  * Awarie sklepu z aplikacjami

1) Aby test, aby sprawdzić, czy problem jest systemowych można przetestować tej samej funkcji z innego

* Usługa Azure Mobile Engagement zintegrowanych aplikacji
* Urządzenie testowe
* Wersja systemu operacyjnego urządzenia testu
* Kampanii
* Konta administratora
* Przeglądarka (tj., Firefox Chrome, itp.)
* Computer (Komputer)

2) Aby sprawdzić, czy problem dotyczy tylko interfejsu użytkownika lub interfejsu API:

* Przetestuj tę samą funkcję z interfejsu użytkownika usługi Azure Mobile Engagement i Azure Mobile Engagement API.

3) Aby sprawdzić, czy problem dotyczy sieci telefon komórkowy:

* Testowanie podczas połączenia z Internetem za pośrednictwem sieci Wi-Fi i za pośrednictwem sieci 3G telefonu komórkowego.
* Upewnij się, że Zapora nie blokuje Azure Mobile Engagement adresy i porty.

4) Aby sprawdzić, czy problem dotyczy urządzenia:

* Należy sprawdzić, czy urządzenie jest w stanie nawiązać połączenia z usługi Azure Mobile Engagement z innej aplikacji zintegrowane usługi Azure Mobile Engagement.
* Test, który może generować zdarzenia, zadań i awarii (Crash) na telefonie, które są widoczne w Interfejsie użytkownika usługi Azure Mobile Engagement. 
* Sprawdzić, czy można wysyłać powiadomienia wypychane w interfejsie użytkownika usługi Azure Mobile Engagement na urządzenie oparte na jej identyfikator urządzenia. 

5) Aby sprawdzić, czy problem dotyczy aplikacji:

* Instalowanie i testowanie aplikacji w emulatorze zamiast z fizycznego urządzenia:

6) Aby sprawdzić, czy problem dotyczy uaktualnień systemu operacyjnego dla użytkownika końcowego urządzeń, które wymagają uaktualnienia zestawu SDK do rozwiązania:

* Przetestować aplikację na różnych urządzeniach z różnymi wersjami systemu operacyjnego.
* Upewnij się, że używasz najnowszej wersji zestawu SDK.

## <a name="connectivity-and-incorrect-information-issues"></a>Łączność i problemy nieprawidłowe informacje
### <a name="issue"></a>Problem
* Problemy z logowania w Interfejsie użytkownika usługi Azure Mobile Engagement.
* Błędy połączenia z Azure Mobile Engagement API.
* Problemy z przekazywaniem tagi informacje o aplikacji za pomocą interfejsu API urządzenia.
* Pobieranie dzienników lub wyeksportowanych danych z usługi Azure Mobile Engagement problemów.
* Nieprawidłowe informacje wyświetlane w Interfejsie użytkownika usługi Azure Mobile Engagement.
* Niepoprawne informacje widoczne w dziennikach usługi Azure Mobile Engagement.

### <a name="causes"></a>Powoduje, że
* Upewnij się, że konto użytkownika ma wystarczające uprawnienia do wykonania zadania.
* Upewnij się, że problem nie jest izolowane do jednego komputera lub sieci lokalnej.
* Upewnij się, że nie ma usługi Azure Mobile Engagement zgłoszone awarii.
* Upewnij się, czy pliki Tag informacje o aplikacji wykonaj wszystkie te reguły:
  * Użyj tylko zestaw znaków UTF8 (zestaw znaków ANSI nie jest obsługiwane).
  * Użyj przecinka "," jako znak separatora (można otworzyć usługi do żądania zmiany znak separatora CSV ze przecinek "," do innego znaku, np. średnikiem ";").
  * Użyj małe litery logiczna wartości "true" i "false".
  * Używany plik, który jest mniejszy niż maksymalny rozmiar pliku 35 MB.

