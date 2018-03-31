---
title: Azure implementacji usługi Mobile Engagement dla aplikacji z nośnika
description: Nośnik scenariusz aplikacji do wdrożenia usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8e5d4585b47a4ca26b81b7168aa499f25722415a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-media-app"></a>Wdrożenie usługi Mobile Engagement z aplikacji z nośnika
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

## <a name="overview"></a>Przegląd
Jan jest kierownikiem przenośnych projektu firmy big nośnika. Niedawno on uruchomiony nowej aplikacji, która ma liczbę bardzo duże pobierania. On osiągnęła jego cele do pobrania, ale nadal jego wrócić na Investment(ROI) dla każdego użytkownika spełnia jego wymagania. 

Jan zidentyfikował już dlaczego jego ROI jest zbyt niski. Użytkownicy często Zatrzymaj przy użyciu jego aplikacji po tylko 2 tygodnie, a większość z nich nie wrócić. Chce zwiększyć przechowywanie swoich aplikacji.

Po początkowej niektórych testów, dowiedziała się on po zaangażuje jego użytkownikom powiadomienia wypychane charakteryzują się kontynuować korzystanie z jego aplikacji. Również użytkowników, które były nieaktywne często powrót do aplikacji, w zależności od tego powiadomienia, który wysyła on je. Jan decyduje się na inwestycji w rodzaju Program zaangażowania dla swojej aplikacji, która używa zaawansowane korzystających z powiadomień wypychanych.

Ostatnio zapoznał Jan [usługi Azure Mobile Engagement - Getting Started Guide z najlepszymi rozwiązaniami](mobile-engagement-getting-started-best-practices.md) i podjęto decyzję o zaimplementować zalecenia z przewodnika.

## <a name="objectives-and-kpis"></a>Celów i wskaźników KPI
Spełnia wymagania najważniejszych uczestników Jan w aplikacji. Jak użytkownicy uzyskują dostęp do jego nośnika biznesowa jest generowany na podstawie reklam. Zwiększenie zawartości używane dla każdego użytkownika, Jan zwiększa jego przychodów. Wszystkie zgodzić się na jednym z głównych celów: zwiększenie sprzedaży z ads o 25%. Tworzenia biznesowej kluczowych wskaźników wydajności (KPI) do mierzenia i dysk ten cel

* Liczba reklam kliknięty na użytkownika
* Ile strony artykułu odwiedzone (na użytkownika / sesji / tygodniowo / miesięcznie...)
* Co to są Ulubione kategorie

Na podstawie jego Jan spotkania z najważniejszych uczestników został on zdefiniowany jego biznesowe wskaźniki KPI dotyczące. Wykonuje, część 1 z [usługi Azure Mobile Engagement - Getting Started Guide z najlepszymi rozwiązaniami](mobile-engagement-getting-started-best-practices.md). 

Następnie tworzy następujące kluczowe wskaźniki wydajności zaangażowania zapewniające osiągnięcie celów:

* Monitorowanie przechowywania za pośrednictwem następujących odstępach: codziennie, co tydzień, co dwa tygodnie i miesięczne.
* Liczba aktywnych użytkowników
* Przechowuje Klasyfikacja aplikacji w aplikacji

Na podstawie zaleceń IT przez zespół, następujące kluczowe wskaźniki wydajności techniczne zostały dodane do odpowiedzieć na następujące pytania:

* Co to jest ścieżka do użytkownika (odwiedzenia strony, która ilu użytkowników czas poświęcony na jego)
* Liczba awarii (Crash) i zgłaszanie usterek napotkał na sesję?
* Które wersje systemu operacyjnego działają moich użytkowników?
* Co to jest to średni rozmiar ekranu dla moich użytkowników?
* Jakiego rodzaju połączenia z Internetem masz moich użytkowników?

Dla poszczególnych wskaźników on klasyfikuje dane wymagane i on rejestruje w jego podręcznika dotyczącego właściwego położenia.

## <a name="engagement-program-and-integration"></a>Program zaangażowania i integracja
Teraz tego Jan zakończył określenie jego wskaźników KPI, przechodzi jego fazy strategia zaangażowania przez definiowanie 4 zaangażowania i ich cele: ![][1]

Jan przejdzie głębiej przez wyszczególnieniem powiadomień wypychanych dla każdego programu. Powiadomienia wypychane są definiowane przez pięć elementów:

1. Cel: nowości w celu powiadomienia.
2. Jak można osiągnąć cel
3. Cel: kto otrzyma powiadomienie?
4. Zawartość: Co to jest treść i format powiadomienia (w App/Out z aplikacji)
5. Kiedy: co to jest najlepszym chwili wysłania powiadomienia wypychane
   
    ![][2]

Więcej informacji można znaleźć w [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Zgodnie z części 2 oficjalny dokument Jan używa sekcji docelowej w celu zdefiniowania posiada służąca do gromadzenia danych i zapisuje jego Tag planu wspólnie z zespołu IT wdrożenia rozwiązania. Po 1 tydzień wdrożenia oraz testów akceptacyjnych przez użytkowników Jan na koniec można uruchamiać swoje programy.

## <a name="program-results"></a>Program wyników
później, 4 miesięcy Jan przegląda parametrów programy. Program powitalny i tygodniowego programu są spełniane jego celów. Zmniejsza liczbę użytkowników z tylko jedną sesję, więcej funkcji aplikacji są używane i podwójny ma liczbę połączeń na tydzień.

**Nieaktywne Program** pomaga zrozumieć tendencji użytkownika John. Wygląda na to, że 15% nieaktywnych użytkowników wróć do aplikacji. Jednak większość z nich nie pozostają aktywne więcej niż 1 miesiąc. Jan przewiduje potencjalnych optymalizacji sekwencji przy użyciu dodatkowych powiadomień i rozszerzanie jego wybranej zawartości.

**Odnajdywania Program** nie działa prawidłowo. Zwiększa się między sprzedaży, ale mało do osiągnięcia jego celów. Jan identyfikuje adresat nie ma wystarczającej ilości danych, aby odpowiednich elementów docelowych i proponuje odpowiedniej zawartości. On zatrzymuje ten program i koncentruje się na wysyłanie "powiadomień wypychanych redakcyjne" przy użyciu usługi Azure Mobile Engagement. Jego dziennikarze już rozwiązanie CMS do wysyłania powiadomień wypychanych i nie chcesz zmienić.

Jan decyduje o tym użyć interfejsu API Reach czyli interfejsu API REST protokołu HTTP, który umożliwia zarządzanie kampanie Zasięgowe, bez konieczności używania interfejsu sieci Web AZME. Z tej metody Jan może zbierać dane on musi i umożliwić jego autorów, aby nadal używać rozwiązanie CMS.

Aby upewnić się, że ta funkcja działa poprawnie, Jan zapyta zespół IT wzmożonej uwagi na następujące kwestie:

1. **Systemy operacyjne** : wszystkie mają własne zasady do administrowania powiadomienia wypychane, tak aby Jan decyduje o tym wyświetlić listę wszystkich przypadkach i sprawdza, czy interfejsy API jego obsługa.
   Np.: System Android wypychania umożliwia duży obraz, który nie jest w przypadku systemu iOS.
2. **Przedział czasu**: Jacek chce interfejsu API, który przedział czasu i ustawić zakończenia kampanii. Chce zachować użytkowników z dowolnego sabotaż destrukcyjne powiadomień.
3. **Kategorie**: zespołu marketingu przygotowuje szablonu dla każdego typu alertu. Jan zapyta, dział IT, aby ustawić kategorii w interfejsie API.

Po niektórych testów Jan jest spełniony. Dzięki użyciu tego interfejsu API dziennikarze nadal może wysyłać powiadomienia wypychane przy użyciu ich CMS i usługi Azure Mobile Engagement zbiera wszystkie dane behawioralnej dla nich

Po te 4 najpierw miesiące, wyniki odzwierciedlają dobrej ogólnej wydajności oraz zapewnia ufności Jan i jego tablicy ROI na zwiększa użytkownika na 15% i przenośnych sprzedaży reprezentują 17,5 cala % całkowitej sprzedaży, wzrost % 7.5 tylko czterech miesięcy.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
