---
title: Azure Data Catalog common scenarios (Typowe scenariusze dla usługi Azure Data Catalog)
description: Przegląd typowe scenariusze dotyczące usługi Azure Data Catalog, łącznie z rejestracji i odnajdywania źródeł danych wysokiej wartości, umożliwiając samoobsługowa analiza biznesowa i przechwytywanie istniejących wiedzę na temat źródeł danych i procesów.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: e95cc64b9086a6fb4c5e2d42521a5fd3f44244ba
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010810"
---
# <a name="azure-data-catalog-common-scenarios"></a>Azure Data Catalog common scenarios (Typowe scenariusze dla usługi Azure Data Catalog)
W tym artykule przedstawiono typowe scenariusze, w którym usługi Azure Data Catalog może pomóc Twojej organizacji wydobyć więcej ze swoich istniejących źródeł danych.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenariusz 1: Rejestracja źródeł danych w centralnej
Organizacje często mają wiele źródeł danych wysokiej wartości. Te źródła danych obejmują line-of-business online transakcji przetwarzania systemów (OLTP), magazynów danych i baz danych analizy/analiz biznesowych. Liczba systemów i nakładanie się między nimi, zwiększa się wraz z upływem czasu zwykle w miarę ewolucji potrzeb biznesowych i samego biznesu ewoluuje za pośrednictwem, na przykład fuzje i przejęcia.

Może być trudne dla członków organizacji wiedzieć, gdzie umieścić dane w ramach tych źródeł danych. Wszystkie zbyt popularne są pytania podobne do następującego:

* Systemów trzy HR używany w firmie, które należy użyć do utworzenia tego typu raportu?
* Gdzie należy przejść do uzyskania certyfikowanych danych liczbowych sprzedaży w roku obrachunkowego, który po prostu zakończył się?
* Kto mogę zadawać lub co to jest proces, którego należy użyć do uzyskania dostępu do magazynu danych?
* Nie wiem, jeśli te liczby są odpowiednie. Kto mogę zadawać uzyskać wgląd w sposób te dane mają można użyć, aby udostępnić ten pulpit nawigacyjny Mój zespół?

Na te i inne pytania dotyczące usługi Azure Data Catalog można podać odpowiedzi. Źródła danych centralnej o wysokiej wartości, dział IT, które są używane w organizacji są często logiczne punktem wyjścia do wypełniania wykazu. Mimo że każdy użytkownik może zarejestrować źródła danych, posiadające kick-started ze źródłami danych, które są najczęściej podania wartości do największej liczbie użytkowników wykazu pomaga wspomóc przyjęcie i korzystania z systemu. 

Gdy dopiero rozpoczynasz pracę z usługą Azure Data Catalog, zidentyfikowanie i zarejestrowanie najważniejszymi źródłami danych, które są używane przez wiele różnych zespołów konsumenci danych mogą być Twoim pierwszym krokiem, tak aby wskazywał Powodzenie.

W tym scenariuszu prezentuje też możliwość dodawać adnotacje do źródeł danych wysokiej wartości, aby ułatwić ich zrozumienie i dostępem. Jeden kluczowe aspekty tej pracy jest zawierają informacje, w jaki sposób użytkownicy mogą żądać dostępu do źródła danych. Usługa Azure Data Catalog możesz podać adres e-mail użytkownika lub zespołu, który jest odpowiedzialny za kontrolowanie dostępu do źródła danych, linki do istniejących narzędzi lub dokumentacji lub dowolny tekst opisujący procesu żądania dostępu. Informacje te pomagają członków, którzy Odnajdywanie źródeł danych zarejestrowanych, ale który jeszcze nie masz uprawnień dostępu do danych, aby łatwo poprosić o dostęp za pomocą procesów, które są zdefiniowane i kontrolowane przez właścicieli źródła danych.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenariusz 2: Samoobsługowa analiza biznesowa
Chociaż tradycyjne rozwiązania analizy biznesowej firmy w dalszym ciągu być bezcenne częścią krajobrazów danych w wielu organizacjach, zmiany w tempie firm podejścia biznesowego uczyniło samoobsługowego rozwiązania analizy Biznesowej bardziej ważne. Za pomocą Samoobsługowego rozwiązania analizy Biznesowej, pracowników przetwarzających informacje i analitycy można utworzyć własne raporty, skoroszyty i pulpity nawigacyjne bez polegania na centralny zespół IT lub jest ograniczone przez harmonogram i dostępności tego zespołu IT.

W scenariuszach samoobsługi Power BI użytkownicy często Połącz dane z wielu źródeł, z których wiele może nie była wcześniej używana do analizy Biznesowej i analizy. Mimo że niektóre z tych źródeł danych mogą już być znane, może stanowić wyzwanie, aby dowiedzieć się, co należy zrobić, aby zlokalizować i oceniać potencjalnych źródeł danych dla danego zadania.

Tradycyjnie, ten proces odnajdywania jest ręczne: analityków umożliwia zidentyfikowanie osoby, które pracować z danymi, niestabilna przez ich połączenia sieciowe elementów równorzędnych. Po źródło danych zostanie odnaleziony i jest używany, proces powtarza się ponownie dla każdego kolejnych samoobsługi BI nakładzie pracy z wieloma użytkownikami wykonywania nadmiarowe ręczny proces odnajdowania.

Za pomocą usługi Azure Data Catalog Twoja organizacja może przerwać ten cykl nakładu pracy. Po odnalezieniu źródła danych w sposób tradycyjny, analityk można zarejestrować ją do bardziej łatwa do przeglądania przez innych użytkowników w przyszłości. Analityk można dodać więcej korzyści przez dodawanie adnotacji do zarejestrowanych zasobów danych, ta adnotacja nie trzeba mieć miejsce, w tym samym czasie jako rejestracji. Użytkownicy mogą przyczynić się wraz z upływem czasu jako ich zezwolenie harmonogramy, stopniowo dodając wartość do źródeł danych zarejestrowanych w wykazie.

Ten wzrost organicznych zawartości katalogu jest naturalnym uzupełnienie ponoszonych z góry rejestracji źródła danych centralnej. Wstępne wypełnianie wykazu danych wymagających wielu użytkowników może być motivator do początkowego użycia i odnajdywania. Umożliwienie użytkownikom rejestrowania i dodawać adnotacje do źródeł dodatkowe może być sposób zachowania ich i inni członkowie organizacji zaangażowane.

Warto zauważyć, że chociaż w tym scenariuszu przedstawiono szczegółowe informacje dotyczące samoobsługowego rozwiązania analizy Biznesowej, tych samych wzorców i wyzwania mają zastosowanie do firmowych BI projektów w dużej skali oraz. Korzystając z wykazu danych, Twoja organizacja może poprawić wszelkie nakład pracy, który obejmuje ręczny proces odnajdowania źródeł danych.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenariusz 3: Specjalistyczna wiedza dostępna dla przechwytywania
Skąd wiadomo, jakie dane, które należy wykonać zadania i gdzie można znaleźć te dane?

Jeśli jesteś w ramach zadania od pewnego czasu, prawdopodobnie wystarczy wiedzieć. Po przeszli stopniowego procesie nauki i wraz z upływem czasu zawarto informacje o źródłach danych, które są kluczem do codziennej pracy.

W przypadku nowych pracownika łączy Twój zespół, jak osoba wiedzieć, jakie dane są wymagane dla zadania i gdzie można je znaleźć?

Ryzyko wystąpienia są nową osobę dołączono do Ciebie odpowiedzi na następujące pytania.

To bieżące przeniesienie specjalistyczna wiedza dostępna jest częścią procesu odnajdywania źródła danych w małych i dużych organizacjach. Więcej starszy i doświadczeniem członków zespołu utworzone w ciągu lat wiedzy i nowszej członków zespołu zawarto informacje na poproś go, gdy mają pytania. Najbardziej istotnych informacji często istnieje tylko w głowic garstka kluczowych pracowników, a przy tym osobom są na urlopie lub opuszczaliby zespół, niska organizacji.

Ekspertów ds. danych zazwyczaj postarać się dokumentu swojej wiedzy na temat udostępniania go za pośrednictwem poczty e-mail lub w dokumentach programu Word w witrynie zespołu programu SharePoint. Mimo że takie podejście może być przydatny, wprowadza nowy problem odnajdywania: jak użytkownicy wiedzieli, jakie dokumentacji istnieje i gdzie można go znaleźć?

Za pomocą usługi Azure Data Catalog Twoja organizacja ma jednym, centralnym miejscu do przechowywania i udostępniania to specjalistyczna wiedza i składania łatwa do przeglądania. W wykazie danych ekspertów ds. usługi danych można bezpośrednio dodawać adnotacje do zasobów danych i zawierają łącza do dokumentacji istniejących. Jeśli członkowie organizacji korzystają z katalogu, aby odnaleźć źródła danych, znajdą one nie tylko źródło automatycznie, ale także wiedzę, które wcześniej istniały tylko u ekspertów w Twojej organizacji.
