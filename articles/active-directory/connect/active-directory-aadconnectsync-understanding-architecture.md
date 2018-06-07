---
title: 'Synchronizacja programu Azure AD Connect: opis architektury | Dokumentacja firmy Microsoft'
description: W tym temacie opisano architekturę synchronizacja programu Azure AD Connect i opisano terminy używane.
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
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 079dfe772e6c189c0e81ea7af16d11a1c40f3ebe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593490"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizacja programu Azure AD Connect: omówienie architektury
W tym temacie opisano podstawową architekturę do synchronizacji Azure AD Connect. W wielu aspektach jest podobny do poprzedników MIIS 2003, ILM 2007 i FIM 2010. Synchronizacja programu Azure AD Connect powstał na tych technologii. Jeśli znasz za pomocą dowolnego z tych starszych technologii, zawartość tego tematu będzie znane, również. Jeśli jesteś nowym użytkownikiem synchronizacji, w tym temacie jest dla Ciebie. Jednak nie jest wymagane, aby poznać szczegóły tego tematu, aby skutecznie w podejmowaniu dostosowań do synchronizacji Azure AD Connect (nazywane aparatem synchronizacji w tym temacie).

## <a name="architecture"></a>Architektura
Aparat synchronizacji tworzy zintegrowany widok obiektów, które są przechowywane w wielu połączonych źródeł danych i zarządza nimi informacji o tożsamości w tych źródeł danych. Ten widok zintegrowane zależy od informacji o tożsamości pobierane z połączonych źródeł danych i zestaw reguł określających sposób przetworzyć tych informacji.

### <a name="connected-data-sources-and-connectors"></a>Łączników i połączonych źródeł danych
Aparat synchronizacji przetwarza informacje o tożsamości z repozytoria różne dane, takie jak usługi Active Directory lub bazy danych programu SQL Server. Każdy repozytorium danych, która organizuje dane w postaci bazy danych i zapewnia dostęp do danych standardowych metod jest potencjalnych candidate źródła danych dla aparatu synchronizacji. Repozytoria danych, które są synchronizowane przez aparat synchronizacji są nazywane **połączone źródła danych** lub **połączone katalogów** (CD).

Aparat synchronizacji hermetyzuje interakcji z połączonego źródła danych modułu o nazwie **łącznik**. Każdy typ połączonego źródła danych ma określony łącznik. Łącznik tłumaczy wymagane działania w formacie, który obsługuje usługę połączonego źródła danych.

Łączniki wywołań interfejsu API do wymiany informacji o tożsamości (odczytu i zapisu) z połączonego źródła danych. Użytkownik może również dodać niestandardowe łącznik przy użyciu platformy extensible łączności. Na poniższej ilustracji przedstawiono sposób łącznik łączy połączonego źródła danych do aparatu synchronizacji.

![Arch1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Przepływ danych w żadnym kierunku, ale nie można go jednocześnie przepływać w obu kierunkach. Innymi słowy łącznika można skonfigurować tak, aby umożliwić przepływ z połączonego źródła danych do aparatu synchronizacji lub aparatu synchronizacji do połączonego źródła danych danych, ale tylko jeden z tych operacji może wystąpić w dowolnym momencie dla jednego obiektów i atrybutów. Kierunek mogą być różne dla różnych obiektów, jak i dla różnych atrybutów.

Aby skonfigurować łącznik, należy określić typy obiektów, które mają być synchronizowane. Określanie typów obiektu określa zakres obiektów, które są uwzględnione w procesie synchronizacji. Następnym krokiem jest wybierz atrybuty do synchronizowania, czyli tzw. Atrybut Lista dołączania. Te ustawienia można można zmienić w dowolnym momencie w odpowiedzi na zmiany do reguł biznesowych. Korzystając z Kreatora instalacji usługi Azure AD Connect, te ustawienia są konfigurowane automatycznie.

Aby wyeksportować obiekty do połączonego źródła danych, lista dołączania atrybutu musi zawierać co najmniej minimalną atrybuty wymagane do utworzenia określonego typu obiektu w połączonego źródła danych. Na przykład **sAMAccountName** musi być uwzględniony na liście dołączania atrybut można wyeksportować obiektu użytkownika do usługi Active Directory, ponieważ wszystkie obiekty użytkowników w usłudze Active Directory musi mieć **sAMAccountName** zdefiniowanego atrybutu. Kreator instalacji wykonuje ponownie konfigurację za Ciebie.

Jeśli korzysta z połączonego źródła danych strukturalnych składniki, takie jak partycje lub kontenerów do organizowania obiektów, można ograniczyć obszarów w połączonego źródła danych, które są używane dla danego rozwiązania.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Wewnętrzna struktura przestrzeni nazw aparatu synchronizacji
Przestrzeń nazw aparatu synchronizacji całego składa się z dwóch przestrzeni nazw, w których są przechowywane informacje o tożsamości. Dwie przestrzenie nazw są:

* Obszar łączników (CS)
* Metaverse (MV)

**Przestrzeni łącznika** jest obszar tymczasowy zawierający reprezentacje obiektów wyznaczone z połączonego źródła danych i atrybutów określonych na liście atrybutów dołączenia. Aparat synchronizacji używa przestrzeni łącznika, aby określić, co się zmieniło w połączonego źródła danych i przemieszczanie przychodzące zmiany. Aparat synchronizacji używa również przestrzeni łącznika, aby przemieścić wychodzących zmian eksportu do połączonego źródła danych. Aparat synchronizacji przechowuje przestrzeni łącznika różne jako obszaru przemieszczania dla poszczególnych łączników.

Korzystając z obszaru przemieszczania, aparatu synchronizacji pozostaje niezależnie od połączonych źródeł danych i nie ma wpływu na ich dostępności i dostępności. W związku z tym może przetworzyć informacji o tożsamości w dowolnym momencie przy użyciu danych w obszarze tymczasowym. Aparat synchronizacji można zażądać tylko zmiany wprowadzone w połączonego źródła danych od czasu ostatniej sesji komunikacji zakończone lub wypychanej tylko zmiany do tożsamości informacji nie otrzymał jeszcze połączonego źródła danych, co zmniejsza ruch sieciowy między aparatem synchronizacji i połączonego źródła danych.

Ponadto aparat synchronizacji przechowuje informacje o stanie dotyczące wszystkich obiektów, że przygotowuje go do przestrzeni łącznika. Po odebraniu nowych danych aparatu synchronizacji zawsze ocenia, czy zostały już zsynchronizowane dane.

**Metaverse** to obszar pamięci masowej, zawierający informacje o tożsamości zagregowane z wielu połączonych źródeł danych, zapewni jeden widok globalnej, zintegrowane wszystkich połączonych obiektów. Metaverse obiekty są tworzone w oparciu o informacje o tożsamości, które są pobierane z połączonych źródeł danych i zestaw reguł, które umożliwiają dostosowanie procesu synchronizacji.

Na poniższej ilustracji przedstawiono przestrzeni nazw z przestrzeni łącznika i przestrzeni nazw metaverse w ramach aparatu synchronizacji.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Obiekty tożsamości aparatu synchronizacji
Obiekt w aparacie synchronizacji reprezentacje albo obiektów w połączonego źródła danych lub zintegrowane widok, który aparat synchronizacji został tych obiektów. Każdy obiekt aparatu synchronizacji musi mieć unikatowy identyfikator globalny (GUID). Identyfikatory GUID zapewniają integralność danych i express relacje między obiektami.

### <a name="connector-space-objects"></a>Obiekty łączników miejsca
Gdy aparat synchronizacji komunikuje się z połączonego źródła danych, odczytuje informacje o tożsamości w połączonego źródła danych i używa tych informacji do utworzenia reprezentacji obiektu tożsamości do przestrzeni łącznika. Nie można utworzyć lub usunąć tych obiektów pojedynczo. Można jednak ręcznie usuń wszystkie obiekty w przestrzeni łącznika.

Wszystkie obiekty w przestrzeni łącznika ma dwa atrybuty:

* Unikatowy identyfikator globalny (GUID)
* Nazwa wyróżniająca (DN)

Jeśli połączonego źródła danych przypisuje unikatowy atrybut do obiektu, w obiekty w przestrzeni łącznika może również zawierać atrybut zakotwiczenia. Atrybut zakotwiczenia w sposób unikatowy identyfikuje obiekt w połączonego źródła danych. Aparat synchronizacji używa zakotwiczenia można znaleźć odpowiedniego reprezentację tego obiektu w połączonego źródła danych. Aparat synchronizacji przyjęto założenie, że zakotwiczenia obiektu nie ulega zmianie w okresie istnienia obiektu.

Znane Unikatowy identyfikator wiele łączników służy do generowania zakotwiczenie automatycznie dla każdego obiektu, gdy jest on zaimportowany. Na przykład używa łącznika usługi Active Directory **objectGUID** atrybutu zakotwiczenia. Połączonych źródeł danych, które nie udostępniają jasno określone Unikatowy identyfikator można określić generowanie zakotwiczenia w ramach konfiguracji łącznika.

W tym przypadku zakotwiczenia składa się z jednego lub więcej unikatowych atrybutach obiektu wpisz ani jakie zmiany i który unikatowo identyfikuje obiekt do przestrzeni łącznika (na przykład numer pracownika lub identyfikator użytkownika).

Obiekt miejsca łącznika może być jedną z następujących czynności:

* Obiekt przemieszczania
* Symbol zastępczy

### <a name="staging-objects"></a>Obiekty tymczasowe
Obiekt przemieszczania reprezentuje wystąpienie typów obiektów wyznaczone z połączonego źródła danych. Oprócz identyfikator GUID i nazwy wyróżniającej obiektu przemieszczania ma zawsze wartość, która wskazuje typ obiektu.

Obiekty tymczasowe, które zostały zaimportowane zawsze mieć wartość dla atrybutu zakotwiczenia. Tymczasowe obiekty, które zostały nowo udostępnione przez aparat synchronizacji i weryfikuje tworzony w połączonego źródła danych nie mają wartości dla atrybutu zakotwiczenia.

Obiekty tymczasowe również zawierać bieżące wartości atrybutów biznesowych i informacje operacyjne wymagane przez aparat synchronizacji do wykonania procesu synchronizacji. Informacje o działaniu zawiera flagi, które określają typ aktualizacji, które są umieszczane na tymczasowej obiektu. Jeśli obiekt przemieszczania otrzymał nowe informacje o tożsamości ze źródła danych połączenia, które nie zostały jeszcze przetworzone, obiekt jest oznaczony jako **importu oczekujących**. Jeśli obiekt przemieszczania zawiera nowe informacje o tożsamości, które jeszcze nie zostały wyeksportowane do połączonego źródła danych, jest oznaczone jako **oczekujących eksportu**.

Obiekt przemieszczania można obiektu importu lub eksportu. Aparat synchronizacji tworzy obiekt importu za pomocą informacji o obiekcie otrzymanego z połączonego źródła danych. Aparat synchronizacji odebrania informacji o istnieniu nowy obiekt, który pasuje do jednego z typów obiektów wybrane w łączniku tworzy obiekt importu w przestrzeni łącznika jako reprezentacji obiektu w połączonego źródła danych.

Na poniższej ilustracji przedstawiono obiektu import, który reprezentuje obiekt w połączonego źródła danych.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

Aparat synchronizacji tworzy obiekt eksportu przy użyciu informacji o obiekcie w magazynie metaverse. Eksportowanie obiektów są eksportowane do połączonego źródła danych podczas następnej sesji komunikacji. Z punktu widzenia aparatu synchronizacji eksportu obiekty nie istnieją w źródle danych połączonych jeszcze. W związku z tym atrybutu zakotwiczenia obiektu eksportu nie jest dostępna. Po otrzymaniu obiekt z aparatu synchronizacji połączonego źródła danych tworzy unikatową wartość dla atrybutu zakotwiczenia obiektu.

Na poniższej ilustracji przedstawiono sposób tworzenia obiektu eksportu przy użyciu informacji o tożsamości w magazynie metaverse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

Aparat synchronizacji potwierdza eksportu obiektu przez ponowne importowanie obiektów z połączonego źródła danych. Wyeksportuj obiekty stają się importowanie obiektów odebrania aparatu synchronizacji ich podczas importowania dalej z tego źródła danych połączenia.

### <a name="placeholders"></a>Symbole zastępcze
Aparat synchronizacji używa prosty obszar nazw do przechowywania obiektów. Jednak niektóre połączonych źródeł danych takich jak usługi Active Directory użyj hierarchicznej przestrzeni nazw. Do przekształcenia informacji z hierarchicznej przestrzeni nazw w prosty obszar nazw, aparat synchronizacji używa symbole zastępcze w celu zachowania w hierarchii.

Każdy symbol zastępczy odpowiada (na przykład jednostki organizacyjnej) część nazwy hierarchiczna obiektu nie został zaimportowany do aparatu synchronizacji, która jest wymagana do utworzenia nazwy hierarchicznej. Wypełniania luk utworzone przez odwołań w połączonego źródła danych do obiektów, które nie są przemieszczania obiekty w przestrzeni łącznika.

Aparat synchronizacji używa również symbole zastępcze do przechowywania obiektów do którego istnieje odwołanie, które nie zostały zaimportowane. Na przykład, jeśli skonfigurowano obejmują atrybutu Menedżera synchronizacji *Abbie Spencer* obiekt i odebranej wartości jest obiekt, który nie został jeszcze zaimportowany, takich jak *CN Lewandowski Sperry, CN = Użytkownicy, DC = Firma fabrikam, DC = = com*, informacje są przechowywane jako symbole zastępcze w przestrzeni łącznika. Jeśli obiekt menedżera później jest importowany, obiekt symbol zastępczy jest zastępowana przez przemieszczania obiekt, który reprezentuje menedżera.

### <a name="metaverse-objects"></a>Obiekty metaverse
Zagregowany widok zawiera obiektu metaverse tego aparat synchronizacji jest tymczasowe obiekty w przestrzeni łącznika. Aparat synchronizacji tworzy obiekty metaverse, korzystając z informacji w Importowanie obiektów. Niektóre obiekty przestrzeni łącznika może odnosić się do obiektu metaverse pojedynczego, ale obiekt miejsca łącznika nie można połączyć z więcej niż jednego obiektu metaverse.

Obiekty metaverse nie można ręcznie utworzyć ani usunąć. Aparat synchronizacji automatycznie usuwa metaverse obiektów, które nie mają łącze do dowolnego obiektu przestrzeni łącznika w przestrzeni łącznika.

Aby mapować obiektach połączonego źródła danych do odpowiedniego typu obiektu w magazynie metaverse, aparat synchronizacji zapewnia rozszerzony schemat z zestaw wstępnie zdefiniowanych typów obiektów i skojarzonych z nimi atrybutów. Można tworzyć nowe typy obiektów i atrybuty obiektów metaverse. Atrybuty mogą być jednowartościowe lub wielowartościowe, a typ atrybutu może być ciągów, odwołań, cyfry i wartościami logicznymi.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relacje między obiektami przemieszczania i metaverse
W obszarze nazw aparatu synchronizacji przepływu danych jest włączana przez łącze relacji między obiektami przemieszczania i metaverse. Obiekt przemieszczania, który jest połączony z obiektu metaverse jest wywoływana **dołączonego do obiektu** (lub **obiekt łącznika**). Przemieszczania obiektu, który nie jest połączony z obiektu metaverse jest nazywany **odłączony obiekt** (lub **obiektu izolacyjny**). Warunki przyłączone i odłączony są preferowane nie należy mylić z łączników odpowiedzialny za importowania i eksportowania danych z połączonego katalogu.

Symbole zastępcze nigdy nie są połączone z obiektu metaverse

Dołączonego do obiektu obejmuje przemieszczania obiekt i jego relacji do obiektu metaverse pojedynczego. Połączone obiekty służą do synchronizowania wartości atrybutów między obiektem przestrzeni łącznika i obiektu metaverse.

Gdy obiekt przemieszczania staje się dołączonego do obiektu podczas synchronizacji, między obiektem przemieszczania i obiektu metaverse przepływ atrybutów. Przepływ atrybutów jest dwukierunkowy i jest skonfigurowany przy użyciu reguł atrybutu importowania i eksportowania reguł atrybutu.

Obiekt miejsca pojedynczy łącznik można połączyć tylko do jednego obiektu metaverse. Jednak każdego obiektu metaverse można powiązać wiele obiektów miejsca łącznika, w tym samym lub różnych łącznika spacji, jak pokazano na poniższej ilustracji.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

Połączone relacji między przemieszczania obiektu i obiektu metaverse jest trwałe i mogą być usunięte tylko przez zasady, które określisz.

Obiekt odłączonym jest przemieszczania obiektu, który nie jest połączony z dowolnego obiektu metaverse. Atrybut, który wartości odłączonym obiektu nie są przetwarzane wszelkie dodatkowe w magazynie metaverse. Wartości atrybutów odpowiedni obiekt w połączonego źródła danych nie są aktualizowane przez aparat synchronizacji.

Za pomocą obiektów odłączonym, można przechowywać informacje o tożsamości w aparacie synchronizacji i przetworzyć go później. Utrzymywanie przemieszczania obiektu jako obiektu odłączonym do przestrzeni łącznika ma wiele zalet. Ponieważ system został już przygotowane wymaganych informacji dotyczących tego obiektu, nie jest konieczne tworzenie reprezentację tego obiektu ponownie podczas następnej importu z połączonego źródła danych. W ten sposób aparat synchronizacji zawsze ma pełnej migawki połączonego źródła danych, nawet jeśli brak bieżącego połączenia do połączonego źródła danych. Obiekty odłączonym może zostać przekonwertowany na połączone obiekty i odwrotnie, w zależności od reguł, które określisz.

Obiekt importu jest tworzony jako odłączonym obiektu. Obiekt eksportu musi być przyłączony do obiektu. Logiki systemu wymusza tę regułę i usuwa każdy obiekt eksportu, który nie jest przyłączony do obiektu.

## <a name="sync-engine-identity-management-process"></a>Proces zarządzania tożsamościami aparatu synchronizacji
Proces zarządzania tożsamościami określa sposób aktualizowania informacji o tożsamości między różnych połączonych źródeł danych. Zarządzanie tożsamościami występuje w trzech procesów:

* Import
* Synchronizacja
* Eksportowanie

Podczas procesu importowania aparatu synchronizacji ocenia przychodzące informacje o tożsamości z połączonego źródła danych. Po wykryciu zmiany jego tworzy nowe obiekty tymczasowe albo aktualizuje istniejące obiekty tymczasowego do przestrzeni łącznika do synchronizacji.

Podczas procesu synchronizacji aparatu synchronizacji aktualizacji metaverse w celu odzwierciedlenia zmian, które wystąpiły w przestrzeni łącznika i aktualizuje przestrzeni łącznika, aby uwzględnić zmiany, które wystąpiły w magazynie metaverse.

Podczas procesu eksportowania aparatu synchronizacji wypycha zmiany, które są umieszczane na tymczasowej obiektów i które są oznaczone jako oczekujące eksportu.

Na poniższej ilustracji pokazano gdzie każdego procesu występuje jako tożsamość przepływ informacji z jednego połączonego źródła danych na inny.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Proces importowania
Podczas procesu importowania aparatu synchronizacji ocenia aktualizacje do informacji o tożsamości. Aparat synchronizacji porównuje informacje o tożsamości otrzymanego z połączonego źródła danych z informacjami o tożsamości o tymczasowych obiektach i określa, czy obiekt przemieszczania wymaga aktualizacji. Jeśli jest to niezbędne do aktualizacji obiektu przemieszczania nowymi danymi importu oczekujących oflagowane przemieszczania obiektu.

Dzięki obiekty w przestrzeni łącznika przed synchronizacją, aparat synchronizacji może przetwarzać tylko zmienione informacje tożsamości. Ten proces zapewnia następujące korzyści:

* **Wydajność synchronizacji**. Ilość danych przetworzone podczas synchronizacji jest zminimalizowany.
* **Ponowna synchronizacja wydajne**. Możesz zmienić sposób aparatu synchronizacji przetwarza informacje o tożsamości bez ponowne nawiązywanie połączenia ze źródłem danych aparatu synchronizacji.
* **Możliwość synchronizacji w wersji preview**. Można wyświetlić podgląd synchronizacji, aby sprawdzić, czy założeń dotyczących procesu zarządzania tożsamościami są poprawne.

Dla każdego obiektu określony w łączniku aparatu synchronizacji najpierw próbuje zlokalizować reprezentację obiektu w przestrzeni łącznika łącznika. Aparat synchronizacji sprawdza wszystkie tymczasowe obiekty w przestrzeni łącznika i próbuje odnaleźć odpowiedniego obiektu przemieszczania, który ma pasującego atrybutu zakotwiczenia. Jeśli nie istniejący obiekt przemieszczania ma pasującego atrybut zakotwiczenia, aparat synchronizacji próbuje odnaleźć odpowiedniego obiektu przemieszczania o takiej samej nazwie wyróżniającej.

Jeśli aparat synchronizacji znajdzie przemieszczania obiekt, który odpowiada nazwie wyróżniającej, ale nie zakotwiczenia, następujące specjalne zachowanie:

* Z obiektem znajduje się w przestrzeni łącznika nie jest brak zakotwiczenia, a następnie aparatu synchronizacji usuwa ten obiekt z przestrzeni łącznika i oznacza obiektu metaverse jest połączony jako **ponów inicjowania obsługi administracyjnej na uruchomienie w ramach następnej synchronizacji**. Następnie tworzy nowy obiekt importu.
* Jeśli obiekt znajduje się w przestrzeni łącznika ma kotwicy, aparat synchronizacji założono, że tego obiektu albo został przeniesiony lub usunięty z połączonego katalogu. Przypisuje tymczasowego, nowe nazwy wyróżniającej obiektu przestrzeni łącznika, dzięki czemu można umieścić przychodzące obiektu. Staje się stary obiekt **przejściowa**, oczekiwanie łącznika do importowania zmiany nazwy lub usunięcia, aby rozwiązać problem.

Jeśli aparat synchronizacji lokalizuje przemieszczania obiekt, który odpowiada obiektu określonego w łączniku, określa jakiego rodzaju zmiany zaczęły obowiązywać. Na przykład aparatu synchronizacji może zmienić nazwy lub Usuń obiekt połączonego źródła danych lub może aktualizować tylko wartości atrybutu obiektu.

Obiekty tymczasowe o zaktualizowanych danych są oznaczone jako oczekujące na import. Dostępne są różne typy oczekującego importowania. W zależności od wyniku procesu importowania obiektu tymczasowego do przestrzeni łącznika ma jeden z następujących oczekujących typów importu:

* **Brak**. Żadne zmiany dla każdego z atrybutów obiektu przemieszczania są dostępne. Aparat synchronizacji nie Flaga ten typ importu oczekujących.
* **Dodaj**. Obiekt przemieszczania jest nowy obiekt importu w przestrzeni łącznika. Aparat synchronizacji flagi tego typu jako oczekujące importu dla dodatkowego przetwarzania w magazynie metaverse.
* **Aktualizacja**. Aparat synchronizacji umożliwia znalezienie odpowiedniego obiektu przemieszczania do przestrzeni łącznika i flagi tego typu jako importu oczekujących, tak, aby aktualizacje atrybuty mogą być przetwarzane w magazynie metaverse. Aktualizacje obejmują, zmiana nazwy obiektu.
* **Usuń**. Aparat synchronizacji znajduje odpowiedni obiekt tymczasowego do przestrzeni łącznika i flagi tego typu jako importu oczekujących, tak aby można było usunąć obiektu dołączonego do.
* **Dodaj/Usuń**. Aparat synchronizacji znajdzie odpowiedni obiekt przemieszczania do przestrzeni łącznika, ale nie są zgodne typy obiektów. W takim przypadku usuń dodawania są przygotowywane modyfikacji. A delete dodawania modyfikacji wskazuje aparatu synchronizacji, że pełnej ponownej synchronizacji tego obiektu musi wystąpić, ponieważ zastosować różne zestawy reguł dla tego obiektu, podczas zmiany typu obiektu.

Ustawiając stan oczekującego importowania obiektu przemieszczania jest można znacznie zmniejszyć ilość danych przetworzone podczas synchronizacji, ponieważ to pozwala systemu do przetwarzania tylko te obiekty, które zostały zaktualizowane dane.

### <a name="synchronization-process"></a>Proces synchronizacji
Synchronizacja składa się z dwóch powiązanych z nim procesów:

* Synchronizacji ruchu przychodzącego, po zaktualizowaniu zawartości metaverse przy użyciu danych do przestrzeni łącznika.
* Synchronizacji ruchu wychodzącego, gdy zawartość obszaru łącznika zostanie zaktualizowany przy użyciu danych w magazynie metaverse.

Przy użyciu informacji umieszczone w przestrzeni łącznika, proces synchronizacji ruchu przychodzącego tworzy w magazynie metaverse zintegrowane widoku danych, które są przechowywane w połączonych źródeł danych. Wszystkie obiekty tymczasowe lub tylko te informacje oczekującego importowania są agregowane w zależności od tego, jak zasady są skonfigurowane.

Aktualizacje procesu synchronizacji ruchu wychodzącego wyeksportować obiekty, obiekty metaverse zmiany.

Synchronizacji ruchu przychodzącego tworzy widok zintegrowane w magazynie metaverse programu informacji o tożsamości, które zostały odebrane z połączonych źródeł danych. Aparat synchronizacji można przetwarzać informacji o tożsamości w dowolnym momencie przy użyciu najnowsze informacje o tożsamości, którą ma z połączonego źródła danych.

**Synchronizacji ruchu przychodzącego**

Synchronizacji ruchu przychodzącego obejmuje następujące procesy:

* **Zainicjuj obsługę** (nazywane również **projekcji** jeśli ważne jest, aby odróżnienia tego procesu synchronizacji ruchu wychodzącego inicjowania obsługi administracyjnej). Aparat synchronizacji tworzy nowy obiekt metaverse oparte na obiekt przemieszczania i łączy je. Zainicjuj obsługę jest operacją na poziomie obiektu.
* **Dołącz**. Aparat synchronizacji łączy przemieszczania obiektu do istniejącego obiektu metaverse. Sprzężenia jest operacją na poziomie obiektu.
* **Importuj przepływ atrybutów**. Aparat synchronizacji aktualizacji wartości atrybutów o nazwie przepływ atrybutów obiektu w magazynie metaverse. Przepływ atrybutów Import jest operacją poziom atrybutu, która wymaga połączenia między obiektu przemieszczania i obiektu metaverse.

Należy to tylko procesu, który tworzy obiekty w magazynie metaverse. Zainicjuj obsługę wpływa na importu tylko te obiekty, które są obiektami odłączonym. Podczas udostępniania aparat synchronizacji tworzy obiektu metaverse, który odpowiada typowi obiektu importowanego obiektu i nawiązuje połączenie między zarówno do obiektów, w związku z tym tworzenia dołączonego do obiektu.

Proces dołączania również nawiązuje połączenie między Importowanie obiektów i obiektu metaverse. Różnica między sprzężenia i dostarczanie jest proces dołączania wymaga, aby obiekt importu, są połączone z istniejącego obiektu metaverse, gdy proces udostępniania tworzy nowy obiekt metaverse.

Aparat synchronizacji próbuje dołączyć obiekt importu do obiektu metaverse przy użyciu kryteriów, które jest określone w konfiguracji reguły synchronizacji.

Podczas procesu udostępniania i Dołącz do aparatu synchronizacji łączy odłączonym obiektu do obiektu metaverse nadawania przyłączony. Po zakończeniu tych operacji na poziomie obiektu aparatu synchronizacji można zaktualizować wartości atrybutów obiektu metaverse skojarzone. Ten proces jest nazywany importowania przepływu atrybutów.

Przepływ atrybutów Import występuje na wszystkie obiekty importu przenoszenia nowe dane, które są połączone z obiektu metaverse.

**Synchronizacji ruchu wychodzącego**

Aktualizacje synchronizacji ruchu wychodzącego wyeksportować obiekty, gdy obiektu metaverse zmiany, ale nie zostanie usunięta. Celem synchronizacji ruchu wychodzącego jest do oceny, czy zmiany metaverse obiekty wymagają aktualizacji do tymczasowych obiektów w przestrzeni łącznika. W niektórych przypadkach, zmiany mogą wymagać tego przemieszczania można zaktualizować obiektów w samych spacji łącznika. Obiekty tymczasowe, które są zmieniane są oznaczone jako oczekujące eksportu, dzięki czemu można je wyeksportować obiekty. Eksportu obiektów są później wypchnięty do połączonego źródła danych podczas procesu eksportowania.

Synchronizacji ruchu wychodzącego ma trzy procesy:

* **Aprowizacja**
* **Cofanie zastrzegania**
* **Eksportowanie przepływu atrybutów**

Alokowania i anulowania alokowania są operacjami na poziomie obiektu. Cofanie zastrzegania zależy od tego, inicjowania obsługi, ponieważ tylko inicjowania obsługi administracyjnej może inicjować ją. Cofanie zastrzegania jest wyzwalane, gdy Inicjowanie obsługi administracyjnej spowoduje usunięcie skojarzenia między obiektu metaverse i obiektu eksportu.

Inicjowanie obsługi administracyjnej jest zawsze wyzwalane, gdy zmiany są stosowane do obiektów w magazynie metaverse. Podczas wprowadzania zmian do obiektów metaverse aparatu synchronizacji można wykonać jedną z następujących czynności w trakcie procesu inicjowania obsługi administracyjnej:

* Utwórz połączone obiekty, gdzie obiektu metaverse jest połączone z obiektu eksportu nowo utworzony.
* Zmień dołączonego do obiektu.
* Odłączenie łącza między obiektu metaverse i przejściowych obiekty, tworzenie odłączonym obiektu.

Jeśli Inicjowanie obsługi administracyjnej wymaga aparatu synchronizacji, aby utworzyć nowy obiekt łącznika, przemieszczania obiektu, z którą połączony jest obiekt metaverse jest zawsze obiektu eksportu, ponieważ obiekt nie istnieje jeszcze w połączonego źródła danych.

W przypadku inicjowania obsługi administracyjnej wymaga aparatu synchronizacji, aby rozłączyć obiektu dołączonego do tworzenia obiektu odłączonym anulowania obsługi zostanie wywołany. Proces anulowania aprowizacji usuwa obiekt.

Podczas anulowania obsługi, usunięcie obiektu eksportu nie fizycznie usunięcia obiektu. Obiekt jest oznaczony jako **usunięte**, co oznacza, że operacji usuwania są przygotowywane do obiektu.

Przepływ atrybutów eksportu ma miejsce podczas procesu synchronizacji ruchu wychodzącego, podobnie jak, że przepływ atrybutów import występuje podczas synchronizacji ruchu przychodzącego. Przepływ atrybutu eksportu nastąpi tylko między metaverse i eksportowanie obiektów, które są połączone.

### <a name="export-process"></a>Proces eksportowania
Podczas procesu eksportowania aparatu synchronizacji sprawdza, czy wszystkie obiekty eksportu, które są oznaczone jako oczekujące eksportu w przestrzeni łącznika, a następnie wysyła aktualizacje do połączonego źródła danych.

Aparat synchronizacji można określić Powodzenie eksportu, ale wystarczająco nie może ustalić o ukończeniu procesu zarządzania tożsamościami. Zawsze można zmienić obiektów w połączonego źródła danych przez inne procesy. Ponieważ aparat synchronizacji nie ma trwałego połączenia do połączonego źródła danych, nie jest wystarczające do zakładają właściwości obiektu połączonego źródła danych w oparciu jedynie powiadomienia pomyślnego eksportu.

Na przykład procesu w połączonego źródła danych można zmienić atrybutów obiektu do ich oryginalnych wartości (to znaczy połączonego źródła danych można zastąpić wartości natychmiast po danych jest umieszczony przez aparat synchronizacji i pomyślnie zostały zastosowane w połączonego źródła danych).

Magazyny aparatu synchronizacji eksportować i importować informacje dotyczące każdego obiektu przemieszczania stanu. Jeśli wartości atrybutów, które są określone na liście atrybutów dołączenia zostały zmienione od czasu ostatniego eksportu, przechowywania, importowanie i eksportowanie aparatu synchronizacji umożliwia stanu do odpowiednio zareagować. Aparat synchronizacji używa procesu importowania w celu potwierdzenia wartości atrybutów, które zostały wyeksportowane do połączonego źródła danych. Porównanie zaimportowane i wyeksportowane informacje, jak pokazano na poniższej ilustracji, umożliwia aparatu synchronizacji określić, czy eksportu zakończyła się pomyślnie, lub jeśli konieczne jest powtórzenie.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Na przykład jeśli aparat synchronizacji eksportuje atrybut C, który ma wartość 5, do połączonego źródła danych, przechowuje C = 5, w jego pamięci stan eksportu. Każdy dodatkowego eksportu na wyniki tego obiektu w celu ponownie wyeksportuj C = 5 połączonego źródła danych, ponieważ aparat synchronizacji przyjęto założenie, że ta wartość nie została trwale zastosowana do obiektu (to znaczy, chyba że inną wartość została zaimportowana ostatnio z połączonego źródła danych). Pamięć eksportu zostanie wyczyszczona po odebraniu podczas operacji importowania dla obiekt C = 5.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

