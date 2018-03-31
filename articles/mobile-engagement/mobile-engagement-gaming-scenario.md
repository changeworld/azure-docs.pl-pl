---
title: Azure implementacji usługi Mobile Engagement dla aplikacji do grania
description: Gry scenariusz aplikacji do wdrożenia usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 96e827b57e804cc91798859bc906e49046decede
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Wdrożenie usługi Mobile Engagement z aplikacji do grania
## <a name="overview"></a>Przegląd
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Uruchamiania gier uruchomiła nową aplikację gier role play/strategii połowy na podstawie. Gry została uruchomiona przez 6 miesięcy. Gry jest ogromny sukces i ma miliony pliki do pobrania i przechowywania w porównaniu do innych aplikacji gry rozruchu jest bardzo duże. Na zgromadzeniu co kwartał przeglądu uczestników zgodę potrzebnych do zwiększenia Średni przychód na użytkownika. Premium w grze pakiety są dostępne jako ofertach specjalnych. Te pakiety gier Zezwalaj użytkownikom na wygląd i wydajności ich linii połowy i przynętą lub tackles w grze uaktualniania. Jednak sprzedaży pakietu są bardzo niskie. Tak zdecydują najpierw przeanalizować obsługi klienta za pomocą narzędzia analizy, a następnie do opracowywania konsultacje segmentacji Zaawansowane na zwiększenie sprzedaży przy użyciu programu.

Na podstawie [usługi Azure Mobile Engagement - Getting Started Guide z najlepszymi rozwiązaniami](mobile-engagement-getting-started-best-practices.md) tworzenia strategia zaangażowania.

## <a name="objectives-and-kpis"></a>Celów i wskaźników KPI
Spełnia wymagania najważniejszych uczestników gry. Wszystkie zgodzić się na jednym z celów głównego — zwiększenie sprzedaży pakietu premium 15%. Tworzenia biznesowej kluczowych wskaźników wydajności (KPI) do mierzenia i dysk ten cel

* Na poziom gry te pakiety zakupionych?
* Co to jest przychód na użytkownika, na sesję punktów w tygodniu i miesiącu?
* Co to są typy zakupu ulubionych?

Część 1 z [Getting Started Guide](mobile-engagement-getting-started-best-practices.md) wyjaśniono, jak zdefiniować celów i wskaźników KPI. 

Z biznesowe wskaźniki KPI dotyczące teraz zdefiniowany Menedżer produktu Mobile tworzy zaangażowania kluczowych wskaźników wydajności, aby określić nowy użytkownik trendów i przechowywania.

* Monitorowanie przechowywania i używać w następujących odstępach: codziennie, co 2 dni, co tydzień, co miesiąc i co 3 miesiące
* Liczby aktywnych użytkowników
* Klasyfikacja aplikacji w magazynie

Na podstawie zaleceń IT przez zespół, następujące kluczowe wskaźniki wydajności techniczne zostały dodane do odpowiedzieć na następujące pytania:

* Co to jest ścieżka do użytkownika (odwiedzenia strony, ile czasu użytkownicy poświęcają na nim)
* Liczba awarii (Crash) i zgłaszanie usterek napotkał na sesję
* Które wersje systemu operacyjnego działają moich użytkowników?
* Co to jest to średni rozmiar ekranu dla moich użytkowników?
* Jakiego rodzaju łączności z Internetem masz moich użytkowników?

Dla poszczególnych wskaźników Mobile Manager produktu określa potrzebuje i gdzie znajduje się w jej podręcznika dotyczącego danych.

## <a name="engagement-program-and-integration"></a>Program zaangażowania i integracja
Przed zbudowaniem program zaangażowania zaawansowane, dyrektor projektu Mobile odpowiedzialnym za projektu powinien mieć bezpośredni zrozumienia, jak i produktów są używane przez użytkowników.

Po 3 miesiące Dyrektor projektu Mobile zebrał wystarczającej ilości danych w celu zwiększenia jego sprzedaży powiadomień wypychanych w aplikacji. ADAM uzyskuje informacje o który:

* Pierwszy zakup zwykle odbywa się na poziomie 14. 90% tych przypadkach zakupu jest bronie legendarnych $3.
* W takich przypadkach 80% użytkowników, które dokonały zakupu, kontynuuj produktu i wprowadź więcej zakupów.
* Użytkownicy, którzy przekazanego poziom 20, uruchom poświęcić więcej niż 10/ tydzień.
* Użytkownicy często kupić pakiety premium na poziomie 16, 24 i 32.

Dzięki tej analizy Dyrektor projektu Mobile decyduje się na utworzenie konkretnych sekwencji powiadomień, zwiększenie sprzedaży aplikacji. Tworzy trzy sekwencji powiadomień wypychanych, które wywołuje on: powitalny programu, Program sprzedaży i Program nieaktywne. Więcej informacji można znaleźć w [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks) ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
