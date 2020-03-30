---
title: 'Synchronizacja usługi Azure AD Connect: opis architektury — Platforma Azure'
description: W tym temacie opisano architekturę synchronizacji usługi Azure AD Connect i wyjaśniono używane terminy.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fac0f9143918d3f273812e53abfb88d6a56f7a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261621"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Synchronizacja usługi Azure AD Connect: opis architektury
W tym temacie omówiono podstawową architekturę synchronizacji usługi Azure AD Connect. Pod wieloma względami jest podobny do swoich poprzedników MIIS 2003, ILM 2007 i FIM 2010. Synchronizacja usługi Azure AD Connect to ewolucja tych technologii. Jeśli znasz którąkolwiek z tych wcześniejszych technologii, treść tego tematu będzie również znana. Jeśli jesteś nowy w synchronizacji, to ten temat jest dla Ciebie. Nie jest jednak wymaganiem, aby poznać szczegóły tego tematu, aby odnieść sukces w tworzeniu dostosowań do synchronizacji usługi Azure AD Connect (o nazwie aparat synchronizacji w tym temacie).

## <a name="architecture"></a>Architektura
Aparat synchronizacji tworzy zintegrowany widok obiektów, które są przechowywane w wielu połączonych źródłach danych i zarządza informacjami o tożsamości w tych źródłach danych. Ten zintegrowany widok jest określany przez informacje o tożsamości pobrane z połączonych źródeł danych i zestaw reguł, które określają sposób przetwarzania tych informacji.

### <a name="connected-data-sources-and-connectors"></a>Połączone źródła danych i łączniki
Aparat synchronizacji przetwarza informacje o tożsamości z różnych repozytoriów danych, takich jak usługa Active Directory lub baza danych programu SQL Server. Każde repozytorium danych, które organizuje swoje dane w formacie przypominającym bazę danych i które zapewnia standardowe metody dostępu do danych, jest potencjalnym kandydatem źródła danych dla aparatu synchronizacji. Repozytoria danych, które są synchronizowane przez aparat synchronizacji są nazywane **połączonymi źródłami danych** lub **połączonymi katalogami** (CD).

Aparat synchronizacji hermetyzuje interakcję z połączonym źródłem danych w module o nazwie **Connector**. Każdy typ połączonego źródła danych ma określony łącznik. Łącznik tłumaczy wymaganą operację na format, który rozumie połączone źródło danych.

Łączniki wywołania interfejsu API do wymiany informacji o tożsamości (zarówno odczytu i zapisu) z połączonym źródłem danych. Istnieje również możliwość dodania łącznika niestandardowego przy użyciu rozszerzalnej struktury łączności. Na poniższej ilustracji pokazano, jak łącznik łączy połączone źródło danych z aparatem synchronizacji.

![Łuk 1](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Dane mogą przepływać w obu kierunkach, ale nie mogą przepływać jednocześnie w obu kierunkach. Innymi słowy łącznik można skonfigurować tak, aby umożliwić przepływ danych z połączonego źródła danych w celu zsynchronizowania aparatu lub z aparatu synchronizacji do połączonego źródła danych, ale tylko jedna z tych operacji może wystąpić w dowolnym momencie dla jednego obiektu i atrybutu. Kierunek może być różny dla różnych obiektów i dla różnych atrybutów.

Aby skonfigurować łącznik, należy określić typy obiektów, które mają zostać zsynchronizowane. Określenie typów obiektów definiuje zakres obiektów, które są uwzględnione w procesie synchronizacji. Następnym krokiem jest wybranie atrybutów do synchronizacji, który jest znany jako lista dołączania atrybutów. Te ustawienia można zmienić w dowolnym momencie w odpowiedzi na zmiany w regułach biznesowych. Korzystając z kreatora instalacji usługi Azure AD Connect, te ustawienia są skonfigurowane dla Ciebie.

Aby wyeksportować obiekty do połączonego źródła danych, lista dołączania atrybutów musi zawierać co najmniej minimalne atrybuty wymagane do utworzenia określonego typu obiektu w połączonym źródle danych. Na przykład atrybut **sAMAccountName** musi zostać uwzględniony na liście dołączania atrybutów, aby wyeksportować obiekt użytkownika do usługi Active Directory, ponieważ wszystkie obiekty użytkownika w usłudze Active Directory muszą mieć zdefiniowany atrybut **sAMAccountName.** Ponownie kreator instalacji wykonuje tę konfigurację za Ciebie.

Jeśli połączone źródło danych używa składników konstrukcyjnych, takich jak partycje lub kontenery do organizowania obiektów, można ograniczyć obszary w połączonym źródle danych, które są używane dla danego rozwiązania.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Wewnętrzna struktura obszaru nazw aparatu synchronizacji
Cały obszar nazw aparatu synchronizacji składa się z dwóch obszarów nazw, które przechowują informacje o tożsamości. Dwie przestrzenie nazw to:

* Przestrzeń łącznika (CS)
* Metaverse (MV)

**Przestrzeń łącznika** jest obszarem przejściowym, który zawiera reprezentacje wyznaczonych obiektów z połączonego źródła danych i atrybuty określone na liście dołączania atrybutów. Aparat synchronizacji używa przestrzeni łącznika, aby określić, co zmieniło się w połączonym źródle danych i na etapie zmian przychodzących. Aparat synchronizacji używa również miejsca łącznika do wystawiania zmian wychodzących do eksportu do połączonego źródła danych. Aparat synchronizacji zachowuje różne przestrzeni łącznika jako obszar przemieszczania dla każdego łącznika.

Korzystając z obszaru przemieszczania, aparat synchronizacji pozostaje niezależny od połączonych źródeł danych i nie ma na nie wpływu ich dostępność i dostępność. W rezultacie można przetwarzać informacje o tożsamości w dowolnym momencie przy użyciu danych w obszarze przemieszczania. Aparat synchronizacji może żądać tylko zmian wprowadzonych wewnątrz połączonego źródła danych od czasu zakończenia ostatniej sesji komunikacyjnej lub wypchnąć tylko zmiany informacji o tożsamości, których podłączone źródło danych nie zostało jeszcze odebrane, co zmniejsza sieć między aparatem synchronizacji a podłączonym źródłem danych.

Ponadto aparat synchronizacji przechowuje informacje o stanie wszystkich obiektów, które etapy w przestrzeni łącznika. Po odebraniu nowych danych aparat synchronizacji zawsze ocenia, czy dane zostały już zsynchronizowane.

**Metaverse** to obszar magazynu, który zawiera zagregowane informacje o tożsamości z wielu połączonych źródeł danych, zapewniając jeden globalny, zintegrowany widok wszystkich połączonych obiektów. Obiekty Metaverse są tworzone na podstawie informacji o tożsamości pobieranych z połączonych źródeł danych oraz zestawu reguł, które umożliwiają dostosowanie procesu synchronizacji.

Na poniższej ilustracji przedstawiono obszar nazw obszaru łącznika i obszar nazw metaverse w silniku synchronizacji.

![Łuk2](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Synchronizowanie obiektów tożsamości aparatu
Obiekty w aparatie synchronizacji są reprezentacjami obiektów w połączonym źródle danych lub zintegrowanego widoku, który aparat synchronizacji ma z tych obiektów. Każdy obiekt aparatu synchronizacji musi mieć unikatowy identyfikator (GUID). Identyfikatory GUID zapewniają integralność danych i ekspresowe relacje między obiektami.

### <a name="connector-space-objects"></a>Obiekty przestrzeni łączników
Gdy aparat synchronizacji komunikuje się z połączonym źródłem danych, odczytuje informacje o tożsamości w połączonym źródle danych i używa tych informacji do utworzenia reprezentacji obiektu tożsamości w przestrzeni łącznika. Nie można tworzyć ani usuwać tych obiektów indywidualnie. Można jednak ręcznie usunąć wszystkie obiekty w przestrzeni łącznika.

Wszystkie obiekty w przestrzeni łącznika mają dwa atrybuty:

* Unikatowy identyfikator globalny (GUID)
* Nazwa wyróżniająca (znana również jako DN)

Jeśli połączone źródło danych przypisuje do obiektu unikatowy atrybut, obiekty w przestrzeni łącznika mogą mieć również atrybut zakotwiczenia. Atrybut zakotwiczenia jednoznacznie identyfikuje obiekt w połączonym źródle danych. Aparat synchronizacji używa zakotwiczenia, aby zlokalizować odpowiednią reprezentację tego obiektu w połączonym źródle danych. Aparat synchronizacji zakłada, że zakotwiczenie obiektu nigdy nie zmienia się w okresie istnienia obiektu.

Wiele łączników używa znanego unikatowego identyfikatora do automatycznego generowania zakotwiczenia dla każdego obiektu podczas importowania. Na przykład łącznik usługi Active Directory używa atrybutu **objectGUID** dla zakotwiczenia. W przypadku połączonych źródeł danych, które nie zawierają jasno zdefiniowanego unikatowego identyfikatora, można określić generowanie zakotwiczenia jako część konfiguracji łącznika.

W takim przypadku zakotwiczenie jest zbudowany z jednego lub więcej unikatowych atrybutów typu obiektu, z których żaden nie zmienia się i który jednoznacznie identyfikuje obiekt w przestrzeni łącznika (na przykład numer pracownika lub identyfikator użytkownika).

Obiekt spacji łącznika może być jedną z następujących czynności:

* Obiekt przemieszczania
* Symbol zastępczy

### <a name="staging-objects"></a>Obiekty przemieszczania
Obiekt przemieszczania reprezentuje wystąpienie typów obiektów wyznaczonych z połączonego źródła danych. Oprócz identyfikatora GUID i nazwy wyróżniającej obiekt przemieszczania zawsze ma wartość, która wskazuje typ obiektu.

Obiekty przemieszczania, które zostały zaimportowane zawsze mają wartość dla atrybutu anchor. Obiekty przemieszczania, które zostały niedawno aprowizowany przez aparat synchronizacji i są w trakcie tworzenia w połączonym źródle danych nie mają wartości dla atrybutu anchor.

Obiekty przemieszczania również nosić bieżące wartości atrybutów biznesowych i informacji operacyjnych potrzebnych przez aparat synchronizacji do wykonywania procesu synchronizacji. Informacje operacyjne obejmują flagi wskazujące typ aktualizacji, które są przemieszczane na obiekcie przemieszczania. Jeśli obiekt przemieszczania otrzymał nowe informacje o tożsamości z połączonego źródła danych, które nie zostało jeszcze przetworzone, obiekt zostanie oznaczony jako **oczekujący na import**. Jeśli obiekt przemieszczania zawiera nowe informacje o tożsamości, które nie zostały jeszcze wyeksportowane do połączonego źródła danych, jest oflagowany jako **oczekujący na eksport**.

Obiekt przemieszczania może być obiektem importu lub obiektem eksportu. Aparat synchronizacji tworzy obiekt importu przy użyciu informacji o obiektach otrzymanych z podłączonego źródła danych. Gdy aparat synchronizacji odbiera informacje o istnieniu nowego obiektu, który pasuje do jednego z typów obiektów wybranych w łączniku, tworzy obiekt importu w przestrzeni łącznika jako reprezentację obiektu w połączonym źródle danych.

Na poniższej ilustracji przedstawiono obiekt importu reprezentujący obiekt w połączonym źródle danych.

![Łuk 3](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Aparat synchronizacji tworzy obiekt eksportu przy użyciu informacji o obiekcie w metaverse. Obiekty eksportu są eksportowane do połączonego źródła danych podczas następnej sesji komunikacyjnej. Z punktu widzenia aparatu synchronizacji obiekty eksportu nie istnieją jeszcze w połączonym źródle danych. W związku z tym atrybut zakotwiczenia dla obiektu eksportu nie jest dostępny. Po odebraniu obiektu z aparatu synchronizacji połączone źródło danych tworzy unikatową wartość dla atrybutu zakotwiczenia obiektu.

Na poniższej ilustracji pokazano, jak obiekt eksportu jest tworzony przy użyciu informacji o tożsamości w metaverse.

![Łuk4](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Aparat synchronizacji potwierdza eksport obiektu przez ponowneimportowanie obiektu z podłączonego źródła danych. Obiekty eksportu stają się obiektami importu, gdy aparat synchronizacji odbiera je podczas następnego importowania z tego połączonego źródła danych.

### <a name="placeholders"></a>Symbole zastępcze
Aparat synchronizacji używa płaskiej przestrzeni nazw do przechowywania obiektów. Jednak niektóre połączone źródła danych, takie jak usługa Active Directory, używają hierarchicznego obszaru nazw. Aby przekształcić informacje z hierarchicznego obszaru nazw w płaską przestrzeń nazw, aparat synchronizacji używa symboli zastępczych do zachowania hierarchii.

Każdy symbol zastępczy reprezentuje składnik (na przykład jednostkę organizacyjną) nazwy hierarchicznej obiektu, który nie został zaimportowany do aparatu synchronizacji, ale jest wymagany do konstruowania nazwy hierarchicznej. Wypełniają one przerwy utworzone przez odwołania w połączonym źródle danych do obiektów, które nie są obiektami przemieszczania w przestrzeni łącznika.

Aparat synchronizacji używa również symboli zastępczych do przechowywania obiektów, do których istnieją odwołania, które nie zostały jeszcze zaimportowane. Na przykład, jeśli synchronizacja jest skonfigurowana tak, aby zawierała atrybut menedżera dla obiektu *Abbie Spencer,* a odebrana wartość jest obiektem, który nie został jeszcze zaimportowany, takim jak *CN=Lee Sperry,CN=Users,DC=fabrikam,DC=com*, informacje menedżera są przechowywane jako symbole zastępcze w przestrzeni łącznika. Jeśli obiekt menedżera zostanie później zaimportowany, obiekt zastępczy jest zastępowany przez obiekt przemieszczania reprezentujący menedżera.

### <a name="metaverse-objects"></a>Obiekty Metaverse
Obiekt metaverse zawiera widok zagregowany, który aparat synchronizacji ma obiektów przemieszczania w przestrzeni łącznika. Aparat synchronizacji tworzy obiekty metaverse przy użyciu informacji w obiektach importu. Kilka obiektów przestrzeni łącznika może być połączonych z pojedynczym obiektem metaverse, ale obiekt przestrzeni łącznika nie może być połączony z więcej niż jednym obiektem metaverse.

Nie można ręcznie tworzyć ani usuwać obiektów metaverse. Aparat synchronizacji automatycznie usuwa obiekty metaverse, które nie mają łącza do żadnego obiektu przestrzeni łącznika w przestrzeni łącznika.

Aby mapować obiekty w połączonym źródle danych na odpowiedni typ obiektu w metaverse, aparat synchronizacji udostępnia rozszerzalny schemat ze wstępnie zdefiniowanym zestawem typów obiektów i skojarzonymi atrybutami. Można tworzyć nowe typy obiektów i atrybuty dla obiektów metaverse. Atrybuty mogą być jednowącjonowane lub wielowartościowe, a typy atrybutów mogą być ciągami znaków, odwołań, liczb i wartości logicznych.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relacje między obiektami przemieszczania i obiektów metaverse
W obszarze nazw aparatu synchronizacji przepływ danych jest włączony przez relację łącza między obiektami przejściowymi a obiektami metaverse. Obiekt przemieszczania połączony z obiektem metaverse jest nazywany **połączonym obiektem** (lub **obiektem łącznika).** Obiekt przemieszczania, który nie jest połączony z obiektem metaverse, jest nazywany **obiektem rozłącznym** (lub **obiektem rozłączacza).** Terminy połączone i rozłączone są preferowane, aby nie mylić z łącznikami odpowiedzialnymi za importowanie i eksportowanie danych z połączonego katalogu.

Symbole zastępcze nigdy nie są połączone z obiektem metaverse

Połączony obiekt składa się z obiektu przemieszczania i jego połączonej relacji z pojedynczym obiektem metaverse. Połączone obiekty służą do synchronizowania wartości atrybutów między obiektem przestrzeni łącznika a obiektem metaverse.

Gdy obiekt przemieszczania staje się połączony obiekt podczas synchronizacji, atrybuty mogą przepływać między obiektem przemieszczania i obiektu metaverse. Przepływ atrybutów jest dwukierunkowy i jest konfigurowany przy użyciu reguł atrybutu importu i reguł atrybutów eksportu.

Pojedynczy obiekt spacji łącznika może być połączony tylko z jednym obiektem metaverse. Jednak każdy obiekt metaverse może być połączony z wieloma obiektami przestrzeni łączników w tym samym lub w różnych przestrzeniach łączników, jak pokazano na poniższej ilustracji.

![Łuk 5](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Połączona relacja między obiektem przemieszczania a obiektem metaverse jest trwała i może zostać usunięta tylko przez określone reguły.

Odłączony obiekt jest obiektem przemieszczania, który nie jest połączony z żadnym obiektem metaverse. Wartości atrybutów odłączony obiekt nie są przetwarzane dalej w metaverse. Wartości atrybutów odpowiedniego obiektu w połączonym źródle danych nie są aktualizowane przez aparat synchronizacji.

Za pomocą odłączonych obiektów, można przechowywać informacje o tożsamości w silniku synchronizacji i przetwarzać go później. Utrzymywanie obiektu przemieszczania jako odłączony obiekt w przestrzeni łącznika ma wiele zalet. Ponieważ system już wystawił wymagane informacje o tym obiekcie, nie jest konieczne ponowne utworzenie reprezentacji tego obiektu podczas następnego importowania z połączonego źródła danych. W ten sposób aparat synchronizacji zawsze ma pełną migawkę połączonego źródła danych, nawet jeśli nie ma bieżącego połączenia z połączonym źródłem danych. Odłączone obiekty mogą być konwertowane na połączone obiekty i odwrotnie, w zależności od reguł, które określisz.

Obiekt importu jest tworzony jako obiekt rozłączny. Obiekt eksportu musi być obiektem sprzężonym. Logika systemowa wymusza tę regułę i usuwa każdy obiekt eksportu, który nie jest obiektem sprzężonym.

## <a name="sync-engine-identity-management-process"></a>Proces zarządzania tożsamościami aparatu synchronizacji
Proces zarządzania tożsamościami kontroluje sposób aktualizowanie informacji o tożsamości między różnymi połączonymi źródłami danych. Zarządzanie tożsamościami odbywa się w trzech procesach:

* Import
* Synchronizacja
* Eksportowanie

Podczas procesu importowania aparat synchronizacji ocenia przychodzące informacje o tożsamości z połączonego źródła danych. Po wykryciu zmian tworzy nowe obiekty tymczasowe lub aktualizuje istniejące obiekty tymczasowe w przestrzeni łącznika do synchronizacji.

Podczas procesu synchronizacji aparat synchronizacji aktualizuje metaverse, aby odzwierciedlić zmiany, które wystąpiły w przestrzeni łącznika i aktualizuje przestrzeń łącznika, aby odzwierciedlić zmiany, które wystąpiły w metaverse.

Podczas procesu eksportowania aparat synchronizacji wypycha zmiany, które są przemieszczane w obiektach przejściowych i które są oflagowane jako oczekujące na eksport.

Na poniższej ilustracji pokazano, gdzie każdy z procesów występuje jako przepływy informacji o tożsamości z jednego połączonego źródła danych do innego.

![Łuk6](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>Proces importowania
Podczas procesu importowania aparat synchronizacji ocenia aktualizacje informacji o tożsamości. Aparat synchronizacji porównuje informacje o tożsamości otrzymane z połączonego źródła danych z informacjami o tożsamości obiektu przemieszczania i określa, czy obiekt przemieszczania wymaga aktualizacji. Jeśli konieczne jest zaktualizowanie obiektu przemieszczania o nowe dane, obiekt przemieszczania jest oflagowany jako oczekujący import.

Przez obiekty przemieszczania w przestrzeni łącznika przed synchronizacją, aparat synchronizacji może przetwarzać tylko informacje o tożsamości, które uległy zmianie. Proces ten zapewnia następujące korzyści:

* **Wydajna synchronizacja**. Ilość danych przetwarzanych podczas synchronizacji jest zminimalizowana.
* **Efektywna ponowna synchronizacja**. Można zmienić sposób, w jaki aparat synchronizacji przetwarza informacje o tożsamości bez ponownego połączenia aparatu synchronizacji ze źródłem danych.
* **Możliwość podglądu synchronizacji**. Można wyświetlić podgląd synchronizacji, aby sprawdzić, czy założenia dotyczące procesu zarządzania tożsamościami są poprawne.

Dla każdego obiektu określonego w łączniku aparat synchronizacji najpierw próbuje zlokalizować reprezentację obiektu w przestrzeni łącznika łącznika. Aparat synchronizacji sprawdza wszystkie obiekty tymczasowe w przestrzeni łącznika i próbuje znaleźć odpowiedni obiekt przemieszczania, który ma pasujący atrybut zakotwiczenia. Jeśli żaden istniejący obiekt przemieszczania nie ma pasującego atrybutu zakotwiczenia, aparat synchronizacji próbuje znaleźć odpowiedni obiekt przemieszczania o tej samej nazwie wyróżniającej.

Gdy aparat synchronizacji znajdzie obiekt przemieszczania, który pasuje do nazwy wyróżniającej, ale nie przez kotwicę, występuje następujące zachowanie specjalne:

* Jeśli obiekt znajdujący się w przestrzeni łącznika nie ma kotwicy, aparat synchronizacji usuwa ten obiekt z przestrzeni łącznika i oznacza obiekt metaverse, z który jest połączony jako **ponowną inicjowanie obsługi administracyjnej przy następnym uruchomieniu synchronizacji**. Następnie tworzy nowy obiekt importu.
* Jeśli obiekt znajdujący się w przestrzeni łącznika ma kotwicę, aparat synchronizacji zakłada, że nazwa tego obiektu została zmieniona lub usunięta w połączonym katalogu. Przypisuje tymczasową, nową nazwę wyróżniającą dla obiektu przestrzeni łącznika, dzięki czemu może on wystawiać obiekt przychodzący. Stary obiekt staje się następnie **przejściowy,** czekając na łącznika, aby zaimportować zmianę nazwy lub usunięcie, aby rozwiązać sytuację.

Jeśli aparat synchronizacji lokalizuje obiekt przemieszczania, który odpowiada obiektowi określonemu w łączniku, określa, jakiego rodzaju zmiany do zastosowania. Na przykład aparat synchronizacji może zmienić nazwę lub usunąć obiekt w połączonym źródle danych lub może tylko zaktualizować wartości atrybutów obiektu.

Obiekty przemieszczania ze zaktualizowanymi danymi są oznaczone jako oczekujące na import. Dostępne są różne typy oczekujących importów. W zależności od wyniku procesu importowania obiekt przemieszczania w przestrzeni łącznika ma jeden z następujących typów importu oczekujących:

* **Brak**. Żadne zmiany do żadnego atrybutów obiektu przemieszczania są dostępne. Aparat synchronizacji nie oznacza tego typu jako oczekującego importu.
* **Dodaj**. Obiekt przemieszczania jest nowym obiektem importu w przestrzeni łącznika. Aparat synchronizacji oznacza ten typ jako oczekujący import do dodatkowego przetwarzania w metaverse.
* **Aktualizacja**. Aparat synchronizacji znajduje odpowiedni obiekt przemieszczania w przestrzeni łącznika i oznacza ten typ jako oczekujący import, dzięki czemu aktualizacje atrybutów mogą być przetwarzane w metaverse. Aktualizacje obejmują zmianę nazwy obiektu.
* **Usuń**plik . Aparat synchronizacji znajduje odpowiedni obiekt przemieszczania w przestrzeni łącznika i oznacza ten typ jako oczekujący import, dzięki czemu połączony obiekt może zostać usunięty.
* **Usuń/Dodaj**. Aparat synchronizacji znajduje odpowiedni obiekt przemieszczania w przestrzeni łącznika, ale typy obiektów nie są zgodne. W takim przypadku modyfikacja usuwania i dodawania jest przemieszczana. Modyfikacja delete-add wskazuje aparatowi synchronizacji, że musi nastąpić pełna ponowna synchronizacja tego obiektu, ponieważ różne zestawy reguł mają zastosowanie do tego obiektu po zmianie typu obiektu.

Ustawiając oczekujący stan importu obiektu przemieszczania, można znacznie zmniejszyć ilość danych przetwarzanych podczas synchronizacji, ponieważ w ten sposób system może przetwarzać tylko te obiekty, które mają zaktualizowane dane.

### <a name="synchronization-process"></a>Proces synchronizacji
Synchronizacja składa się z dwóch powiązanych procesów:

* Synchronizacja przychodząca, gdy zawartość metaverse jest aktualizowana przy użyciu danych w przestrzeni łącznika.
* Synchronizacja wychodząca, gdy zawartość przestrzeni łącznika jest aktualizowana przy użyciu danych w metaverse.

Korzystając z informacji przemieszczanych w przestrzeni łącznika, proces synchronizacji przychodzącej tworzy w metawerecie zintegrowany widok danych przechowywanych w połączonych źródłach danych. Wszystkie obiekty przejściowe lub tylko te z oczekującymi informacjami o imporcie są agregowane, w zależności od konfiguracji reguł.

Proces synchronizacji ruchu wychodzącego aktualizuje obiekty eksportujące po zmianie obiektów metaverse.

Synchronizacja przychodząca tworzy widok zintegrowany w metawersum informacji o tożsamości, które są odbierane z połączonych źródeł danych. Aparat synchronizacji może przetwarzać informacje o tożsamości w dowolnym momencie przy użyciu najnowszych informacji o tożsamości, które ma z podłączonego źródła danych.

**Synchronizacja ruchu przychodzącego**

Synchronizacja przychodząca obejmuje następujące procesy:

* **Provision (nazywany** również **projekcji,** jeśli jest ważne, aby odróżnić ten proces od inicjowania obsługi administracyjnej synchronizacji wychodzącej). Aparat synchronizacji tworzy nowy obiekt metaverse na podstawie obiektu przemieszczania i łączy je. Inicjacja jest operacją na poziomie obiektu.
* **Dołącz**. Aparat synchronizacji łączy obiekt przemieszczania z istniejącym obiektem metaverse. Sprzężenie jest operacją na poziomie obiektu.
* **Importuj przepływ atrybutów**. Aparat synchronizacji aktualizuje wartości atrybutów, zwane przepływem atrybutów, obiektu w metaverse. Importuj przepływ atrybutów jest operacją na poziomie atrybutu, która wymaga łącza między obiektem przemieszczania a obiektem metaverse.

Provision provision jest jedynym procesem, który tworzy obiekty w metaverse. Aprower dotyczy tylko obiektów importu, które są odłączone obiekty. Podczas inicjowania obsługi administracyjnej aparat synchronizacji tworzy obiekt metaverse, który odpowiada typowi obiektu importu i ustanawia łącze między obu obiektami, tworząc w ten sposób połączony obiekt.

Proces sprzężenia ustanawia również łącze między obiektami importu i obiektem metaverse. Różnica między sprzężenia i aprowizysk jest, że proces sprzężenia wymaga, aby obiekt importu są połączone z istniejącym obiektem metaverse, gdzie proces inicjowania tworzy nowy obiekt metaverse.

Aparat synchronizacji próbuje połączyć obiekt importu z obiektem metaverse przy użyciu kryteriów określonych w konfiguracji reguły synchronizacji.

Podczas inicjowania obsługi administracyjnej i sprzężenia procesów aparat synchronizacji łączy odłączony obiekt do obiektu metaverse, dzięki czemu są połączone. Po zakończeniu tych operacji na poziomie obiektu aparat synchronizacji może zaktualizować wartości atrybutów skojarzonego obiektu metaverse. Ten proces jest nazywany przepływem atrybutów importu.

Import przepływu atrybutów występuje na wszystkich obiektach importu, które zawierają nowe dane i są połączone z obiektem metaverse.

**Synchronizacja ruchu wychodzącego**

Synchronizacja wychodząca aktualizuje obiekty eksportujące, gdy obiekt metaverse zmienia się, ale nie jest usuwany. Celem synchronizacji wychodzącej jest ocena, czy zmiany obiektów metaverse wymagają aktualizacji obiektów przemieszczania w przestrzeni łącznika. W niektórych przypadkach zmiany mogą wymagać aktualizacji obiektów przemieszczania we wszystkich przestrzeniach łączników. Obiekty przemieszczania, które zostały zmienione są oflagowane jako oczekujące eksportu, dzięki czemu ich eksportowania obiektów. Te obiekty eksportu są później wypychane do połączonego źródła danych podczas procesu eksportowania.

Synchronizacja wychodząca ma trzy procesy:

* **Inicjowanie obsługi**
* **Anulowanie obsługi administracyjnej**
* **Eksportowanie przepływu atrybutów**

Inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej są zarówno operacji na poziomie obiektu. Anulowanie obsługi administracyjnej zależy od inicjowania obsługi administracyjnej, ponieważ tylko inicjowanie obsługi administracyjnej może go zainicjować. Anulowanie obsługi administracyjnej jest wyzwalane, gdy inicjowanie obsługi administracyjnej usuwa łącze między obiektem metaverse a obiektem eksportu.

Inicjowanie obsługi administracyjnej jest zawsze wyzwalane, gdy zmiany są stosowane do obiektów w metaverse. Po wprowadzeniu zmian w obiektach metaverse aparat synchronizacji może wykonywać dowolne z następujących zadań w ramach procesu inicjowania obsługi administracyjnej:

* Utwórz połączone obiekty, w których obiekt metaverse jest połączony z nowo utworzonym obiektem eksportu.
* Zmień nazwę połączonego obiektu.
* Rozłączyć łącza między obiektem metaverse i obiektami przemieszczania, tworząc obiekt rozłączony.

Jeśli inicjowanie obsługi administracyjnej wymaga aparatu synchronizacji, aby utworzyć nowy obiekt łącznika, obiekt przemieszczania, do którego jest połączony obiekt metaverse jest zawsze obiektem eksportu, ponieważ obiekt nie istnieje jeszcze w połączonym źródle danych.

Jeśli inicjowanie obsługi administracyjnej wymaga aparatu synchronizacji do odłączenia sprzężone obiektu, tworząc obiekt rozłączony, deprovisioning jest wyzwalany. Proces anulowania obsługi administracyjnej usuwa obiekt.

Podczas anulowania obsługi administracyjnej usunięcie obiektu eksportu nie powoduje fizycznego usunięcia obiektu. Obiekt jest oflagowany jako **usunięty**, co oznacza, że operacja usuwania jest przemieszczana na obiekcie.

Przepływ atrybutów eksportu występuje również podczas procesu synchronizacji wychodzącej, podobnie jak przepływ atrybutów importu podczas synchronizacji przychodzącej. Przepływ atrybutów Eksportu występuje tylko między obiektami metaverse i eksportu, które są połączone.

### <a name="export-process"></a>Proces eksportowania
Podczas procesu eksportowania aparat synchronizacji sprawdza wszystkie obiekty eksportu, które są oflagowane jako oczekujące na eksport w przestrzeni łącznika, a następnie wysyła aktualizacje do połączonego źródła danych.

Aparat synchronizacji można określić powodzenie eksportu, ale nie może wystarczająco określić, że proces zarządzania tożsamościami jest zakończony. Obiekty w połączonym źródle danych zawsze mogą być zmieniane przez inne procesy. Ponieważ aparat synchronizacji nie ma trwałego połączenia z połączonym źródłem danych, nie jest wystarczające, aby przyjąć założenia dotyczące właściwości obiektu w połączonym źródle danych tylko na podstawie pomyślnego powiadomienia o wyeksportu.

Na przykład proces w połączonym źródle danych może zmienić atrybuty obiektu z powrotem na ich oryginalne wartości (oznacza to, że połączone źródło danych może zastąpić wartości natychmiast po wypchnięciu danych przez aparat synchronizacji i pomyślnym zastosowaniu w podłączonego źródła danych).

Aparat synchronizacji przechowuje informacje o stanie eksportu i importu dla każdego obiektu przemieszczania. Jeśli wartości atrybutów, które są określone na liście dołączania atrybutów uległy zmianie od czasu ostatniego eksportu, magazyn stanu importu i eksportu umożliwia aparatowi synchronizacji odpowiednie reagowanie. Aparat synchronizacji używa procesu importowania do potwierdzenia wartości atrybutów, które zostały wyeksportowane do połączonego źródła danych. Porównanie importowanych i wyeksportowanych informacji, jak pokazano na poniższej ilustracji, umożliwia aparatowi synchronizacji ustalenie, czy eksport zakończył się pomyślnie, czy też musi zostać powtórzony.

![Łuk 7](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Na przykład jeśli aparat synchronizacji eksportuje atrybut C, który ma wartość 5, do połączonego źródła danych, przechowuje C = 5 w pamięci stanu eksportu. Każdy dodatkowy eksport tego obiektu powoduje próbę ponownego wyeksportowania C=5 do połączonego źródła danych, ponieważ aparat synchronizacji zakłada, że ta wartość nie została uporczywie zastosowana do obiektu (to jest, chyba że ostatnio zaimportowano inną wartość z obiektu połączonego źródła danych). Pamięć eksportu jest czyszczona po odebraniu C=5 podczas operacji importowania obiektu.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

