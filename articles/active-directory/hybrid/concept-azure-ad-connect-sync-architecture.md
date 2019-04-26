---
title: 'Synchronizacja programu Azure AD Connect: Omówienie architektury | Dokumentacja firmy Microsoft'
description: W tym temacie opisano architekturę synchronizacji programu Azure AD Connect i opisano terminy używane.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/13/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: 60a648b24ad16c1b5ea404b5ad9f1f18fd5041da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245957"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizacja programu Azure AD Connect: Opis architektury
W tym temacie opisano podstawową architekturę, do synchronizacji Azure AD Connect. Pod wieloma względami przypomina poprzedników MIIS 2003, ILM 2007 i programem FIM 2010. Synchronizacja programu Azure AD Connect jest rozwoju tych technologii. Osoby zaznajomione z dowolnym z tych technologii wcześniej zawartość tego tematu będzie znane, jak również. Jeśli jesteś nowym użytkownikiem synchronizacji, w tym temacie jest dla Ciebie. Jednak nie jest wymaganie, aby wiedzieć, szczegółowe informacje o tym temacie, aby odnieść sukces w podejmowaniu dostosowań do synchronizacji Azure AD Connect (nazywane aparatem synchronizacji w tym temacie).

## <a name="architecture"></a>Architektura
Aparat synchronizacji tworzy zintegrowany widok obiektów, które są przechowywane w wielu połączonych źródeł danych i zarządza informacji o tożsamościach w tych źródeł danych. Ten widok zintegrowane, jest określana przez informacje o tożsamości, pobierane z połączonych źródeł danych, a zestaw reguł, które określają sposób przetwarzania tych informacji.

### <a name="connected-data-sources-and-connectors"></a>Połączone źródła danych i łączników
Aparat synchronizacji przetwarza informacje o tożsamościach z różnymi danymi repozytoriów — takiej jak usługi Active Directory lub bazy danych programu SQL Server. Każdego repozytorium danych, który organizuje dane w formacie podobne do bazy danych i zapewniającej dostęp do danych standardowych metod jest potencjalnych kandydatem źródła danych do aparatu synchronizacji. Repozytoria danych, które są synchronizowane przez aparat synchronizacji są nazywane **połączonych źródeł danych** lub **połączone katalogi** (polecenie CD).

Aparat synchronizacji hermetyzuje interakcji z połączonego źródła danych w ramach modułu o nazwie **łącznika**. Każdy typ połączonego źródła danych ma określonego łącznika. Łącznik tłumaczy wymaganej operacji do formatu, który rozumie połączonego źródła danych.

Łączniki wykonywania wywołań interfejsu API do wymiany informacji o tożsamości (odczytu i zapisu), za pomocą połączonego źródła danych. Istnieje również możliwość dodawania łącznika niestandardowego za pomocą łącznika extensible connectivity framework. Poniższa ilustracja przedstawia, jak łącznik łączy się z aparatem synchronizacji z połączonego źródła danych.

![Arch1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Można przepływ danych w dowolnym kierunku, ale nie można go jednocześnie przepływu w obu kierunkach. Innymi słowy łącznika można skonfigurować tak, aby umożliwić danych, które będą przepływać z połączonego źródła danych do aparatu synchronizacji lub aparatu synchronizacji do połączonego źródła danych, ale tylko jeden z tych operacji, może wystąpić w dowolnym momencie dla jednego obiektu i atrybut. Kierunek może być różne dla różnych obiektów i dla różnych atrybutów.

Aby skonfigurować łącznik, należy określić typy obiektów, które mają być synchronizowane. Określanie typów obiektu definiuje zakres obiektów, które są uwzględnione w procesie synchronizacji. Następnym krokiem jest wybierz atrybuty, aby wykonać synchronizację, który jest znany jako atrybut Lista dołączania. Te ustawienia można można zmienić w dowolnym momencie w odpowiedzi na zmiany do reguł biznesowych. Korzystając z Kreatora instalacji usługi Azure AD Connect, te ustawienia zostaną skonfigurowane.

Aby wyeksportować obiekty do połączonego źródła danych, lista dołączania atrybutu musi zawierać co najmniej minimalną atrybuty wymagane do utworzenia określonego typu obiektu w połączonego źródła danych. Na przykład **sAMAccountName** atrybutu muszą być zawarte w lista dołączania atrybut, można wyeksportować obiektu użytkownika do usługi Active Directory, ponieważ wszystkie obiekty użytkownika w usłudze Active Directory muszą mieć **sAMAccountName** atrybut zdefiniowany. Ponownie Kreatora instalacji jest to konfiguracja.

Jeśli korzysta z połączonego źródła danych strukturalnych składniki, takie jak partycje lub kontenery do porządkowania obiektów, można ograniczyć obszarów w połączonego źródła danych, które są używane dla danego rozwiązania.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Wewnętrznej struktury przestrzeni nazw aparatu synchronizacji
Przestrzeni nazw aparatu synchronizacji całego składa się z dwóch obszarów nazw, które przechowują informacje o tożsamości. Dwie przestrzenie nazw są:

- Obszar łączników (CS)
- Metaverse (MV)

**Obszaru łącznika** obszaru przemieszczania, zawierający reprezentacje wyznaczonym obiektów z połączonego źródła danych i atrybuty określone na liście atrybutów dołączenia. Aparat synchronizacji używa przestrzeni łącznika, aby określić, co zmieniło się w połączonego źródła danych i przygotuj zmiany przychodzące. Aparat synchronizacji używa również obszaru łącznika do przygotuj zmiany wychodzące eksportowania do połączonego źródła danych. Aparat synchronizacji zachowuje przestrzeni łącznika distinct taki obszar przygotowawczy dla każdego łącznika.

Korzystając z obszaru tymczasowego, aparat synchronizacji pozostają niezależne od połączonych źródeł danych i nie występuje w ich dostępności i ułatwienia dostępu. W rezultacie może przetwarzać informacje o tożsamościach w dowolnym momencie przy użyciu danych w obszarze tymczasowym. Aparat synchronizacji mogą żądać tylko zmiany wprowadzone wewnątrz połączonego źródła danych od czasu ostatniej sesji komunikacji zakończone lub wypychania tylko zmiany informacji o tożsamości, nie otrzymał jeszcze połączonego źródła danych, co zmniejsza sieci ruch między aparatem synchronizacji i połączonego źródła danych.

Ponadto aparat synchronizacji przechowuje informacje o stanie dotyczące wszystkich obiektów, że przygotowuje go w obszarze łącznika. Po odebraniu nowych danych aparatu synchronizacji zawsze ocenia, czy dane została już zsynchronizowana.

**Metaverse** jest w obszarze pamięci masowej, który zawiera informacje o tożsamości zagregowane z wielu połączonych źródeł danych, zapewniając pojedynczy widok, globalne, zintegrowane, wszystkie połączone obiekty. Metaverse obiekty są tworzone w oparciu o informacje o tożsamościach, które są pobierane z połączonych źródeł danych i zestaw reguł, które umożliwiają dostosowanie procesu synchronizacji.

Na poniższej ilustracji pokazano, przestrzeni nazw z przestrzeni łącznika i przestrzeni nazw metaverse w aparacie synchronizacji.

![Arch2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Obiekty tożsamości aparatu synchronizacji
Obiekty w aparacie synchronizacji są oświadczenia albo obiekty w połączonego źródła danych lub zintegrowanego widoku, który aparat synchronizacji ma tych obiektów. Każdy obiekt aparat synchronizacji musi mieć unikatowy identyfikator globalny (GUID). Identyfikatory GUID zapewniają integralność danych i express relacje między obiektami.

### <a name="connector-space-objects"></a>Obiekty przestrzeni łącznika
Gdy aparat synchronizacji komunikuje się z połączonego źródła danych, odczytuje informacje o tożsamości w połączonego źródła danych i używa tych informacji do utworzenia reprezentacji obiektu tożsamości w przestrzeni łącznika. Nie można utworzyć lub usunąć tych obiektów, indywidualnie. Można jednak ręcznie usuń wszystkie obiekty w przestrzeni łącznika.

Wszystkie obiekty w przestrzeni łącznika ma dwa atrybuty:

- Unikatowy identyfikator globalny (GUID)
- Nazwa wyróżniająca (DN)

Jeśli połączonego źródła danych przypisuje unikatowy atrybut do obiektu, następnie obiekty w przestrzeni łącznika może być również atrybutem zakotwiczenia. Atrybut zakotwiczenia w sposób unikatowy identyfikuje obiekt w połączonego źródła danych. Aparat synchronizacji używa zakotwiczenia do zlokalizowania odpowiedniego reprezentację tego obiektu w połączonego źródła danych. Aparat synchronizacji przyjęto założenie, że zakotwiczenia obiektu nigdy się nie zmienia się w okresie istnienia obiektu.

Wiele łączników generować za pomocą znanych Unikatowy identyfikator elementu zakotwiczenia automatycznie dla każdego obiektu po jego zaimportowaniu. Na przykład używa łącznika usługi Active Directory **objectGUID** atrybutów w przypadku elementu zakotwiczenia. Dla połączonych źródeł danych, które nie są oferowane jasno określone Unikatowy identyfikator można określić generowania zakotwiczenia w ramach konfiguracji łącznika.

W tym przypadku zakotwiczenia składa się z jednego lub więcej atrybutów unikatowy obiektu typu ani jakie zmiany, a który jednoznacznie identyfikuje obiekt w obszarze łącznika (na przykład numer pracownika lub identyfikator użytkownika).

Obiektu przestrzeni łącznika może być jednym z następujących czynności:

- Tymczasowy obiekt
- Symbol zastępczy

### <a name="staging-objects"></a>Obiekty przemieszczania
Tymczasowy obiekt reprezentuje wystąpienie wyznaczonym wybierane z połączonego źródła danych. Oprócz identyfikatora GUID, a nazwa wyróżniająca tymczasowy obiekt ma zawsze wartość, która wskazuje typ obiektu.

Obiekty przejściowe, które zostały zaimportowane zawsze mieć wartość dla atrybutu zakotwiczenia. Obiekty przejściowe, które zostały nowo udostępnione przez aparat synchronizacji i Trwa tworzonych w połączonego źródła danych nie mają wartości dla atrybutu zakotwiczenia.

Obiekty przemieszczania również zawierać bieżące wartości atrybutów firmy i informacje operacyjne wymagane przez aparat synchronizacji do wykonania procesu synchronizacji. Informacje operacyjne zawiera flagi wskazujące typ aktualizacji, które są umieszczane na tymczasowej obiektu. Jeśli tymczasowy obiekt otrzyma nowe informacje o tożsamościach z połączonego źródła danych, które nie zostały jeszcze przetworzone, obiekt jest oznaczony jako **importu oczekujących**. Jeśli obiekt przemieszczania zawiera nowe informacje o tożsamości, które nie zostały jeszcze wyeksportowane do połączonego źródła danych, jest oznaczone jako **operacja eksportowania oczekująca**.

Tymczasowy obiekt może być importu lub eksportu obiektu. Aparat synchronizacji tworzy obiekt importu za pomocą informacji o obiekcie otrzymane od połączonego źródła danych. Gdy aparat synchronizacji otrzymuje informacje o istnieniu nowy obiekt, który pasuje do jednego z typów obiektów, wybrane w łączniku, tworzy obiekt importu w przestrzeni łącznika jako reprezentację obiektu w połączonego źródła danych.

Poniższa ilustracja przedstawia obiekt importu, który reprezentuje obiekt w połączonego źródła danych.

![Arch3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Aparat synchronizacji tworzy obiekt eksportu przy użyciu informacji o obiekcie w magazynie metaverse. Eksportuj obiekty są eksportowane do połączonego źródła danych podczas następnej sesji komunikacji. Z punktu widzenia aparatu synchronizacji Eksportuj obiekty nie istnieją w połączonego źródła danych jeszcze. W związku z tym atrybut zakotwiczenia dla obiektu eksportu nie jest dostępna. Po otrzymaniu obiektu z aparatem synchronizacji połączonego źródła danych tworzy unikatową wartość dla atrybutu zakotwiczenia obiektu.

Na poniższej ilustracji przedstawiono, jak obiekt eksportu jest tworzona przy użyciu informacji o tożsamości w magazynie metaverse.

![Arch4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Aparat synchronizacji potwierdza eksportu obiektu, ponownie zaimportować obiekt z połączonego źródła danych. Eksportuj obiekty stają się importowanie obiektów, gdy aparat synchronizacji otrzymuje je podczas następnego importowania danych z tego połączonego źródła danych.

### <a name="placeholders"></a>Symbole zastępcze
Aparat synchronizacji wykorzystuje prosty obszar nazw do przechowywania obiektów. Jednak niektóre połączonych źródeł danych takich jak usługi Active Directory użyć hierarchicznej przestrzeni nazw. Do przekształcania informacji z hierarchicznej przestrzeni nazw prosty obszar nazw, aparat synchronizacji używa symbole zastępcze zachować w hierarchii.

Każdy symbol zastępczy reprezentuje składnik (na przykład, jednostki organizacyjnej) nazwy hierarchiczne obiektu, który nie został zaimportowany do aparatu synchronizacji, ale jest wymagany, aby skonstruować nazwę hierarchiczne. Wypełniania luk utworzone przez odwołań w połączonego źródła danych do obiektów, które nie są przemieszczania obiekty w przestrzeni łącznika.

Aparat synchronizacji korzysta również symbole zastępcze do przechowywania przywoływane obiekty, które nie zostały zaimportowane. Na przykład, jeśli synchronizacja jest skonfigurowany do zawierać atrybut Menedżera *Abbie Spencer* obiektu i odebrano wartość jest obiektem, który nie został jeszcze zaimportowane, takie jak *CN Sperry Kowalski, CN = = Users, DC = fabrikam, DC = com* , informacje są przechowywane jako symbole zastępcze w przestrzeni łącznika. Jeśli później jest importowany obiekt menedżera, obiekt zastępczy jest zastępowana przez przemieszczania obiekt, który reprezentuje menedżera.

### <a name="metaverse-objects"></a>Obiekty metaverse
Obiektu metaverse zawiera zagregowany widok tego aparat synchronizacji ma przemieszczania obiekty w przestrzeni łącznika. Aparat synchronizacji tworzy obiekty metaverse, korzystając z informacji podanych w Importuj obiekty. Niektóre obiekty w przestrzeni łącznika można połączyć obiektu metaverse pojedynczej, ale nie można połączyć obiektu przestrzeni łącznika do więcej niż jednego obiektu metaverse.

Metaverse obiektów nie można ręcznie utworzone lub usunięte. Aparat synchronizacji automatycznie usuwa metaverse obiekty, które nie mają łącza do dowolnego obiektu przestrzeni łącznika w przestrzeni łącznika.

Do mapowania obiektów w ramach połączonego źródła danych do odpowiedniego typu obiektu w obiekcie metaverse, aparat synchronizacji udostępnia Rozszerzalny schemat z zestawem wstępnie zdefiniowanych typów obiektów i skojarzonych z nimi atrybutów. Można tworzyć nowe typy obiektów i atrybutów dla obiektów w magazynie metaverse. Atrybuty mogą być w jednym lub wielowartościowe i typ atrybutu, który może być ciągi, odwołań, numery i wartości logiczne.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relacje między obiektami przemieszczania i metaverse
W ramach przestrzeni nazw aparatu synchronizacji przepływ danych jest włączona, relacja linku między obiektami przemieszczania i metaverse. Tymczasowy obiekt, który jest połączony z obiektu metaverse jest nazywany **dołączonym do obiektu** (lub **obiekt łącznika**). Tymczasowy obiekt, który nie jest połączony z obiektu metaverse jest nazywany **odłączony obiekt** (lub **obiektu izolacyjny**). Warunki i przyłączone do odłączony są preferowane nie należy mylić z łącznikami odpowiedzialny za importowania i eksportowania danych z połączonym katalogu.

Symbole zastępcze nigdy nie są połączone z obiektu metaverse

Przyłączone do obiektu składa się z tymczasowy obiekt i jego relacji do obiektu metaverse jednego. Połączone obiekty są używane do synchronizowania wartości atrybutu od obiektu przestrzeni łącznika do obiektu metaverse.

Gdy tymczasowy obiekt stanie się dołączonym do obiektu podczas synchronizacji, atrybutów może przepływać między przemieszczania obiektu i obiektu metaverse. Przepływ atrybutu jest dwukierunkowe i jest skonfigurowany przy użyciu reguł atrybut importu i eksportu atrybut reguł.

Obiekt miejsca pojedynczy łącznik można połączyć tylko jednego obiektu metaverse. Jednak każdy obiekt magazynu metaverse można połączyć wiele obiektów przestrzeni łącznika, w tym samym lub różnych łącznika miejsca do magazynowania, jak pokazano na poniższej ilustracji.

![Arch5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Połączone relacji między przemieszczania obiektu i obiektu metaverse jest trwałe i mogą być usunięte tylko przez zasady, które określisz.

Obiekt odłączonym jest tymczasowy obiekt, który nie jest połączony z dowolnego obiektu metaverse. Atrybut, który wartości obiektu odłączonym nie są przetwarzane dowolne dalsze w obiekcie metaverse. Wartości atrybutów odpowiadający mu obiekt połączonego źródła danych nie są aktualizowane przez aparat synchronizacji.

Przy użyciu obiektów odłączonym, można przechowywać informacje o tożsamościach w aparacie synchronizacji i Przetwarzaj je później. Utrzymywanie tymczasowy obiekt jako odłączonym obiekt w obszarze łącznika ma wiele zalet. Ponieważ system ma już przygotowane wymaganych informacji dotyczących tego obiektu, nie jest niezbędne do utworzenia reprezentację tego obiektu ponownie podczas następnego importowania z połączonego źródła danych. W ten sposób aparat synchronizacji zawsze ma pełnej migawki połączonego źródła danych, nawet jeśli nie ma bieżącego połączenia do połączonego źródła danych. Odłączonym obiekty mogą być konwertowane na dołączonym do obiektów i odwrotnie, w zależności od reguł, które określisz.

Obiekt importowania zostanie utworzony jako obiekt odłączonym. Obiekt eksportu musi być przyłączony do obiektu. Logiki systemu wymuszają tę regułę i usuwa każdy obiekt eksportu, który nie jest przyłączony do obiektu.

## <a name="sync-engine-identity-management-process"></a>Proces zarządzania tożsamościami aparatu synchronizacji
Proces zarządzania tożsamościami kontroluje sposób aktualizowania informacji o tożsamości między różnych połączonych źródeł danych. Zarządzanie tożsamościami odbywa się w trzech procesów:

- Import
- Synchronizacja
- Eksportowanie

Podczas procesu importowania aparatu synchronizacji ocenia przychodzące informacje o tożsamościach z połączonego źródła danych. Po wykryciu zmiany jej tworzy nowe obiekty tymczasowe lub aktualizuje przemieszczania obiekty istniejące w przestrzeni łącznika do synchronizacji.

Podczas procesu synchronizacji aparatu synchronizacji aktualizacji obiektu metaverse w celu odzwierciedlenia zmian, które miały miejsce w przestrzeni łącznika i aktualizuje przestrzeni łącznika w celu odzwierciedlenia zmian, które miały miejsce w magazynie metaverse.

Podczas procesu eksportowania aparatu synchronizacji wypychanie zmian, są umieszczane na tymczasowej obiektów i które są oznaczone jako oczekujące eksportu.

Poniższa ilustracja przedstawia wszystkich procesów realizowana jako tożsamość przepływ informacji z jednego połączonego źródła danych do innego.

![Arch6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Proces importowania
Podczas procesu importowania aparatu synchronizacji ocenia aktualizacje informacji o tożsamości. Aparat synchronizacji porównuje informacje o tożsamości otrzymane od połączonego źródła danych z informacjami o tożsamości o tymczasowych obiektach i określa, czy obiekt przemieszczania wymaga aktualizacji. Jeśli jest to niezbędne do aktualizacji przemieszczania obiektu za pomocą nowych danych przemieszczania obiektu zostanie oflagowana importu oczekujących.

Przez przemieszczania obiekty w przestrzeni łącznika przed synchronizacją, aparat synchronizacji może przetwarzać informacje tożsamości, które uległy zmianie. Ten proces zapewnia następujące korzyści:

- **Wydajność synchronizacji**. Ilość danych przetwarzanych podczas synchronizacji jest zminimalizowany.
- **Wydajne ponownej synchronizacji**. Można zmienić, aparat synchronizacji przetwarzaniu informacji o tożsamości bez ponownego łączenia się z aparatem synchronizacji ze źródłem danych.
- **Możliwość synchronizacji w wersji zapoznawczej**. Można wyświetlać podgląd synchronizacji, aby sprawdzić, czy Twoje założeń dotyczących procesu zarządzania tożsamościami są poprawne.

Dla każdego obiektu, który określono w łączniku aparat synchronizacji po raz pierwszy próbuje zlokalizować reprezentację obiektu w przestrzeni łącznika łącznika. Aparat synchronizacji sprawdza wszystkie tymczasowe obiekty w przestrzeni łącznika i próbuje znaleźć odpowiedni obiekt przemieszczania, który ma pasujący atrybut zakotwiczenia. Jeśli nie istniejące tymczasowy obiekt ma pasujący atrybut zakotwiczenia, aparat synchronizacji próbuje znaleźć odpowiedni obiekt przemieszczania o takiej samej nazwie wyróżniającej.

Jeśli aparat synchronizacji znajdzie tymczasowy obiekt, który odpowiada nazwę wyróżniającą, ale nie zakotwiczenia, następujące specjalne zachowanie:

- Jeśli obiekt znajdujący się w obszarze łącznika nie ma żadnych kotwicę, a następnie aparatu synchronizacji z obszaru łącznika spowoduje usunięcie tego obiektu i obiektu metaverse jest połączona, co oznacza **ponów udostępniania w następnej synchronizacji Uruchom**. Następnie tworzy nowy obiekt importu.
- Jeśli obiekt znajduje się w obszarze łącznika ma kotwicę, aparat synchronizacji założono, że tego obiektu albo został przeniesiony lub usunięty w połączonym katalogu. Tak, aby go etap przychodzącego obiektu, przypisuje tymczasowego, nowe nazwę wyróżniającą obiektu przestrzeni łącznika. Stary obiekt staje się wówczas **przejściowy**, oczekiwanie na łącznik Aby zaimportować zmiany nazwy lub usuwanie, aby rozwiązać problem.

Jeśli aparat synchronizacji lokalizuje tymczasowy obiekt, który odnosi się do obiektu określonego w łączniku, ustala, jakiego rodzaju zmian do zastosowania. Na przykład aparatu synchronizacji może Zmienianie nazwy lub usuwanie obiektu w połączonego źródła danych lub może aktualizować tylko wartości atrybutów obiektu.

Przemieszczania obiektów przy użyciu zaktualizowanych danych są oznaczone jako oczekujące importu. Dostępne są różne typy oczekujące importów. W zależności od wyniku procesu importowania tymczasowy obiekt w obszarze łącznika ma jedną z następujących oczekujących importowanie typów:

- **Brak**. Żadne zmiany do dowolnych atrybutów przemieszczania obiektu są dostępne. Aparat synchronizacji nie są oznaczane tego typu importu oczekujących.
- **Dodaj**. Obiekt przemieszczania jest nowy obiekt importu w przestrzeni łącznika. Aparat synchronizacji flagi tego typu jako oczekujące na import dla dodatkowego przetwarzania w magazynie metaverse.
- **Aktualizacja**. Aparat synchronizacji znajdzie odpowiedni tymczasowy obiekt w obszarze łącznika i flagi tego typu jako importu oczekujących, tak, aby aktualizacje atrybuty mogą być przetwarzane w magazynie metaverse. Aktualizacje obejmują, zmiana nazwy obiektu.
- **Usuń**. Aparat synchronizacji znajdzie odpowiedni tymczasowy obiekt w obszarze łącznika i flagi tego typu jako importu oczekujących, dzięki czemu można usunąć dołączonym do obiektu.
- **Dodaj/Usuń**. Aparat synchronizacji znajdzie odpowiedni tymczasowy obiekt w obszarze łącznika, ale nie są zgodne typy obiektów. W takim przypadku usuń — Dodawanie są przygotowywane modyfikacji. A delete — dodawanie modyfikacji wskazuje aparat synchronizacji, że pełna ponowna synchronizacja tego obiektu musi wystąpić, ponieważ różne zasady mają zastosowanie do tego obiektu podczas zmiany typ obiektu.

Ustawiając stan oczekującego importowania tymczasowy obiekt, jest można znacznie zmniejszyć ilość danych przetwarzanych podczas synchronizacji, ponieważ spowoduje to więc umożliwia systemowi przetwarzanie tylko tych obiektów, które zostały zaktualizowane dane.

### <a name="synchronization-process"></a>Proces synchronizacji
Synchronizacja składa się z dwóch powiązanych z nim procesów:

- Synchronizacja ruchu przychodzącego, po zaktualizowaniu zawartości obiektu metaverse przy użyciu danych w przestrzeni łącznika.
- Synchronizacji ruchu wychodzącego, gdy zawartość obszaru łącznika zostanie zaktualizowany przy użyciu danych w magazynie metaverse.

Korzystając z informacji podanych umieszczone w przestrzeni łącznika, proces synchronizacji ruchu przychodzącego tworzy w magazynie metaverse zintegrowany widok danych, która jest przechowywana w połączonych źródeł danych. Wszystkie obiekty tymczasowe lub tylko te informacje oczekującego importowania są agregowane w zależności od tego, jak zasady są skonfigurowane.

Aktualizacje procesu synchronizacji ruchu wychodzącego wyeksportowanie obiektów, gdy zmienią się obiekty w magazynie metaverse.

Synchronizacja ruchu przychodzącego tworzy zintegrowany widok w magazynie metaverse programu informacje o tożsamości, które zostały odebrane z połączonych źródeł danych. Aparat synchronizacji może przetwarzać informacje o tożsamościach w dowolnym momencie przy użyciu najnowsze informacje o tożsamości, czy jest on z połączonego źródła danych.

**Synchronizacja ruchu przychodzącego**

Synchronizacja ruchu przychodzącego obejmuje następujące procesy:

- **Aprowizacja** (nazywane również **projekcji** jeśli ważne jest, aby odróżnić ten proces, od zainicjowania obsługi synchronizacji danych wyjściowych). Aparat synchronizacji tworzy nowy obiekt magazynu metaverse oparte na tymczasowy obiekt i łączy je. Aprowizacja jest operacją na poziomie obiektu.
- **Dołącz do**. Aparat synchronizacji łączy tymczasowy obiekt do istniejącego obiektu metaverse. Sprzężenia jest operacją na poziomie obiektu.
- **Importowanie przepływu atrybutu**. Aparat synchronizacji aktualizuje wartości atrybutów o nazwie przepływu atrybutu, obiektu w obiekcie metaverse. Przepływu atrybutu importowania jest operacją poziom atrybutu, która wymaga połączenia między przemieszczania obiektu i obiektu metaverse.

Aprowizacja jest tylko proces, który tworzy obiekty w magazynie metaverse. Aprowizacja wpływa na importowanie tylko te obiekty, które są obiektami odłączonym. Podczas aprowizacji aparat synchronizacji tworzy obiekt metaverse, odnosi się do typu obiektu importowanego obiektu, który nawiązuje połączenie między oba obiekty, w związku z tym tworzenie dołączonym do obiektu.

Proces dołączania także ustanawia łącze między obiektami importu i obiektu metaverse. Różnica między sprzężenia i aprowizować polega na tym, że proces dołączania wymaga, że obiekt importu, są połączone z istniejącego obiektu metaverse, gdy proces aprowizacji tworzy nowy obiekt magazynu metaverse.

Aparat synchronizacji spróbuje przyłączyć obiektu importu do obiektu metaverse przy użyciu kryteriów określonych w konfiguracji reguły synchronizacji.

W procesach dostarczania i sprzężenia aparat synchronizacji łączy odłączonym obiektu do obiektu metaverse, dzięki czemu przyłączone do. Po zakończeniu tych operacji na poziomie obiektu aparatu synchronizacji można zaktualizować wartości atrybutów obiektu metaverse skojarzone. Ten proces jest nazywany przepływu atrybutu importowania.

Przepływ atrybutu import odbywa się na wszystkie obiekty importu zawierają nowe dane, które są połączone z obiektu metaverse.

**Synchronizacji ruchu wychodzącego**

Aktualizacje synchronizacji ruchu wychodzącego wyeksportowanie obiektów, gdy zmiany obiektu metaverse, ale nie zostanie usunięta. Celem synchronizacji ruchu wychodzącego jest do oceny, czy zmiany metaverse obiektów wymagają aktualizacji do obiektów tymczasowych w łącznika miejsca do magazynowania. W niektórych przypadkach zmiany może wymagać tego przemieszczania można zaktualizować obiektów w wszystkich przestrzeni łącznika. Przemieszczania obiekty, które zostały zmienione, są oznaczone jako oczekujące eksportu, dzięki czemu wyeksportować obiekty. Eksportu obiektów są później przekazywane do połączonego źródła danych podczas procesu eksportowania.

Synchronizacji ruchu wychodzącego ma trzy procesy:

- **Aprowizacja**
- **Cofanie zastrzegania**
- **Eksportowanie przepływu atrybutów**

Aprowizacji i cofania aprowizacji są operacjami na poziomie obiektu. Anulowanie aprowizacji, zależy od tego, inicjowania obsługi, ponieważ tylko inicjowania obsługi administracyjnej może inicjować ją. Anulowanie aprowizacji jest wyzwalany, gdy Inicjowanie obsługi administracyjnej usuwa łącze między obiektu metaverse i obiekt eksportu.

Inicjowanie obsługi administracyjnej jest zawsze wyzwalane, gdy zmiany są stosowane do obiektów w obiekcie metaverse. Gdy zostaną wprowadzone zmiany do obiektu metaverse obiektów, aparat synchronizacji można wykonywać następujące zadania w ramach procesu inicjowania obsługi administracyjnej:

- Utwórz połączone obiekty, gdzie obiektu metaverse jest połączone do eksportu nowo utworzonego obiektu.
- Zmień dołączonym do obiektu.
- Odłączenie łącza między obiektu metaverse i przemieszczania obiekty, tworzenie obiektu odłączonym.

Jeśli Inicjowanie obsługi administracyjnej wymaga aparatu synchronizacji, aby utworzyć nowy obiekt łącznika, przemieszczania obiektu, z którą połączony jest obiekt magazynu metaverse jest zawsze obiektu eksportu, ponieważ obiekt nie istnieje jeszcze w połączonego źródła danych.

Anulowanie aprowizacji jest wyzwalany, jeśli Inicjowanie obsługi administracyjnej wymaga aparatu synchronizacji, aby rozłączyć obiekt dołączonym do tworzenia obiektu odłączonym. Proces anulowania aprowizacji usuwa obiekt.

Podczas anulowania aprowizacji usunięcie obiektu eksportu nie powoduje fizycznego usunięcia obiektu. Obiekt jest oznaczony jako **usunięte**, co oznacza, że operacji usuwania są przygotowywane do obiektu.

Eksportowania przepływu atrybutu ma miejsce w procesie synchronizacji danych wyjściowych podobny sposób, że przepływu atrybutu importowania odbywa się podczas synchronizacji ruchu przychodzącego. Eksportowania przepływu atrybutu odbywa się tylko między metaverse i eksportowanie obiektów, które są dołączone.

### <a name="export-process"></a>Proces eksportowania
Podczas procesu eksportowania aparatu synchronizacji sprawdza, czy wszystkie obiekty eksportu, które są oznaczone jako oczekujące eksportu w przestrzeni łącznika, a następnie wysyła aktualizacje do połączonego źródła danych.

Aparat synchronizacji można określić Powodzenie eksportu, ale nie może wystarczająco określić o ukończeniu procesu zarządzania tożsamościami. Zawsze można zmienić przez inne procesy obiektów w połączonego źródła danych. Ponieważ aparat synchronizacji nie ma trwałego połączenia do połączonego źródła danych, nie jest wystarczające, aby wprowadzić założeń dotyczących właściwości obiektu w połączonego źródła danych oparte tylko na powiadomienie pomyślnego eksportu.

Na przykład procesu w połączonego źródła danych można zmienić atrybutów obiektów do ich oryginalnych wartości (czyli połączonego źródła danych może spowodować zastąpienie wartości natychmiast po dane są przekazywane przez aparat synchronizacji i pomyślnie zastosowane w połączonego źródła danych).

Magazyny aparat synchronizacji eksportować i importować informacje o stanie dotyczące poszczególnych obiektów tymczasowych. Jeśli wartości atrybutów, które są określone na liście atrybutów dołączania zostały zmienione od czasu ostatniego eksportu, przechowywania importowanie i eksportowanie aparatu synchronizacji umożliwia stan odpowiednio reagować. Aparat synchronizacji używa procesu importowania, aby potwierdzić wartości atrybutów, które zostały wyeksportowane do połączonego źródła danych. Porównanie zaimportowany i wyeksportowane informacje, jak pokazano na poniższej ilustracji, umożliwia aparat synchronizacji określić, czy Eksportowanie zakończyło się pomyślnie, lub jeśli musi zostać powtórzony.

![Arch7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Na przykład jeśli aparat synchronizacji eksportuje atrybut C, która ma wartość 5, aby połączonego źródła danych, przechowuje C = 5, w swojej pamięci stan eksportu. Każdy dodatkowy eksportu dla tego obiektu wyniki w celu podjęcia próby ponownie wyeksportować języka C = 5 połączonego źródła danych, ponieważ aparat synchronizacji przyjęto założenie, że ta wartość nie trwale zastosowano do obiektu (oznacza to, o ile inna wartość została ostatnio zaimportowana z połączonego źródła danych). Pamięć eksportu jest czyszczona po odebraniu C = 5 podczas operacji importowania do obiektu.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).


