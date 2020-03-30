---
title: Azure Data Catalog common scenarios (Typowe scenariusze dla usługi Azure Data Catalog)
description: Omówienie typowych scenariuszy dla usługi Azure Data Catalog, w tym rejestracji i odnajdywania źródeł danych o wysokiej wartości, włączanie samoobsługowej analizy biznesowej i przechwytywanie istniejącej wiedzy na temat źródeł i procesów danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736458"
---
# <a name="azure-data-catalog-common-scenarios"></a>Azure Data Catalog common scenarios (Typowe scenariusze dla usługi Azure Data Catalog)
W tym artykule przedstawiono typowe scenariusze, w których usługa Azure Data Catalog może pomóc organizacji uzyskać większą wartość z istniejących źródeł danych.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenariusz 1: Rejestracja centralnych źródeł danych
Organizacje często mają wiele źródeł danych o wysokiej wartości. Te źródła danych obejmują systemy line-of-business, przetwarzanie transakcji online (OLTP), magazyny danych i bazy danych analizy biznesowej/analizy. Liczba systemów i nakładanie się między nimi zazwyczaj rośnie wraz z rozwojem potrzeb biznesowych, a sama firma ewoluuje na przykład poprzez fuzje i przejęcia.

Członkowie instytucji mogą mieć trudności z poznaniem, gdzie można zlokalizować dane w tych źródłach danych. Pytania takie jak następujące są zbyt powszechne:

* Z trzech systemów HR stosowanych w firmie, których należy użyć do tworzenia tego typu raportów?
* Gdzie należy udać się, aby uzyskać certyfikowane numery sprzedaży dla roku obrachunkowego, który właśnie się zakończył?
* Kogo należy zapytać lub jakiego procesu należy użyć, aby uzyskać dostęp do magazynu danych?
* Nie wiem, czy te liczby są słuszne. Kogo mogę poprosić o wgląd w sposób, w jaki te dane mają być używane, zanim udostępnię ten pulpit nawigacyjny mojemu zespołowi?

Na te i inne pytania usługa Azure Data Catalog może udzielić odpowiedzi. Centralne, wysokiej wartości, it-managed źródeł danych, które są używane w organizacjach są często logicznym punktem wyjścia do wypełniania katalogu. Chociaż każdy użytkownik może zarejestrować źródło danych, rozpoczęcie katalogu ze źródłami danych, które najprawdopodobniej zapewnią wartość największej liczbie użytkowników, pomaga w uzyskiwać i używać systemu. 

Jeśli rozpoczynasz pracę z usługą Azure Data Catalog, identyfikowanie i rejestrowanie kluczowych źródeł danych, które są używane przez wiele różnych zespołów konsumentów danych, może być pierwszym krokiem do sukcesu.

Ten scenariusz przedstawia również możliwość donotowania źródeł danych o wysokiej wartości, aby ułatwić ich zrozumienie i dostęp. Jednym z kluczowych aspektów tego wysiłku jest uwzględnienie informacji o tym, jak użytkownicy mogą żądać dostępu do źródła danych. Za pomocą usługi Azure Data Catalog można podać adres e-mail użytkownika lub zespołu, który jest odpowiedzialny za kontrolowanie dostępu do źródła danych, łącza do istniejących narzędzi lub dokumentacji lub tekst swobodny opisujący proces żądania dostępu. Te informacje pomagają członkom, którzy odnajdują zarejestrowane źródła danych, ale nie mają jeszcze uprawnień dostępu do danych, aby łatwo zażądać dostępu przy użyciu procesów zdefiniowanych i kontrolowanych przez właścicieli źródeł danych.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenariusz 2: Samoobsługowa analiza biznesowa
Chociaż tradycyjne rozwiązania korporacyjnej analizy biznesowej nadal są nieocenioną częścią krajobrazów danych wielu organizacji, zmieniające się tempo działalności sprawiło, że samoobsługowa analiza biznesowa stała się coraz ważniejsza. Korzystając z samoobsługowej analizy biznesowej, pracownicy i analitycy informacji mogą tworzyć własne raporty, skoroszyty i pulpity nawigacyjne bez polegania na centralnym zespole IT lub ograniczenia harmonogramu i dostępności tego zespołu IT.

W scenariuszach samoobsługowej analizy biznesowej użytkownicy często łączą dane z wielu źródeł, z których wiele nie było wcześniej używanych do analizy biznesowej i analizy. Chociaż niektóre z tych źródeł danych mogą być już znane, może być trudne do odnalezienia, co zrobić, aby zlokalizować i ocenić potencjalne źródła danych dla danego zadania.

Tradycyjnie ten proces odnajdywania jest ręczny: analitycy używają swoich równorzędnych połączeń sieciowych do identyfikowania innych osób, które pracują z poszukiwanymi danymi. Po znalezieniu i użyciu źródła danych proces powtarza się ponownie dla każdego kolejnego samoobsługowego wysiłku analizy biznesowej, przy czym wielu użytkowników wykonuje nadmiarowy ręczny proces odnajdywania.

Dzięki usłudze Azure Data Catalog twoja organizacja może przerwać ten cykl wysiłku. Po odnajdowaniu źródła danych za pomocą tradycyjnych środków analityk może go zarejestrować, aby ułatwić jego wykrywalnie przez innych użytkowników w przyszłości. Mimo że analityk może dodać więcej wartości, donośnąc zarejestrowane zasoby danych, adnotacja ta nie musi mieć miejsca w tym samym czasie co rejestracja. Użytkownicy mogą wnieść swój wkład w czasie, jak pozwalają na to ich harmonogramy, stopniowo dodając wartość do źródeł danych zarejestrowanych w katalogu.

Ten organiczny wzrost zawartości katalogu jest naturalnym uzupełnieniem wcześniejszej rejestracji centralnych źródeł danych. Wstępne wypełnianie katalogu danymi, które wielu użytkowników będzie potrzebować, może być motywatorem do początkowego użycia i odnajdowania. Umożliwienie użytkownikom rejestrowania i dodawania adnotacji do dodatkowych źródeł może być sposobem na utrzymanie zaangażowania ich i innych członków organizacji.

Warto zauważyć, że chociaż ten scenariusz koncentruje się w szczególności na samoobsługowej bi, te same wzorce i wyzwania dotyczą również dużych projektów analizy biznesowej dla dużych firm. Korzystając z wykazu danych, organizacja może ulepszyć wszelkie nakłady pracy, które obejmują ręczny proces odnajdowania źródła danych.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenariusz 3: Zdobywanie wiedzy plemiennej
Skąd wiesz, jakie dane są potrzebne do wykonania swojej pracy i gdzie znaleźć te dane?

Jeśli jesteś w pracy na chwilę, prawdopodobnie po prostu wiesz. Przeszedłeś przez proces stopniowego uczenia się i z czasem dowiedziałeś się o źródłach danych, które są kluczowe dla codziennej pracy.

Kiedy nowy pracownik dołącza do Twojego zespołu, skąd ta osoba wie, jakie dane są wymagane do pracy i gdzie je znaleźć?

Szanse są, nowa osoba przychodzi do ciebie z tymi pytaniami.

Ten ciągły transfer wiedzy plemiennej jest częścią procesu odkrywania źródeł danych w dużych i małych organizacjach. Starsi i doświadczeni członkowie zespołu przez lata zdobywali wiedzę, a nowi członkowie zespołu nauczyli się zadawać im pytania. Najważniejsze informacje często istnieją tylko w głowach kilku kluczowych osób, a gdy ci ludzie są na wakacjach lub opuszczają zespół, organizacja cierpi.

Eksperci od danych zwykle starają się udokumentować swoją wiedzę, udostępniając ją za pośrednictwem poczty e-mail lub w dokumentach programu Word w witrynie programu SharePoint zespołu. Chociaż takie podejście może być cenne, wprowadza nowy problem odkrycie: skąd ludzie wiedzą, jaka dokumentacja istnieje i gdzie ją znaleźć?

Dzięki usłudze Azure Data Catalog Twoja organizacja ma jedną, centralną lokalizację do przechowywania i udostępniania tej wiedzy plemiennej oraz do łatwego wykrywania. W wykazie danych eksperci danych mogą bezpośrednio dodawać adnotacje do zasobów danych i udostępniać łącza do istniejącej dokumentacji. Gdy członkowie instytucji używają katalogu do odnajdywanie źródła danych, znajdą nie tylko samo źródło, ale także wiedzę, która wcześniej istniała tylko w umysłach ekspertów twojej organizacji.
