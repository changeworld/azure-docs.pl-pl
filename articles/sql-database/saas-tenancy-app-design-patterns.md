---
title: Wielodostępne wzorców SaaS — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Informacje na temat wymagań i wspólnych danych wzorce architektury wielodostępnej oprogramowania jako usługi (SaaS) aplikacji baz danych, które działają w środowisku chmury systemu Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6332555c1a176a06004ddfeee513844ad5875c30
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59260548"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Wielodostępne SaaS baza danych dzierżawy wzorców

W tym artykule opisano różne modele dzierżawy, dostępne dla aplikacji SaaS z wieloma dzierżawami.

Podczas projektowania aplikacji SaaS z wieloma dzierżawami, należy starannie wybrać model dzierżawy, który najlepiej pasuje do wymagań aplikacji.  Model dzierżawy określa, jak dane każdej dzierżawy jest mapowany do magazynu.  Wybór modelu dzierżawy ma wpływ na projekt aplikacji i zarządzania.  Przełączanie do innego modelu później czasami jest kosztowne.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS pojęcia i terminologia

W oprogramowaniu jako model usługi (SaaS), Twoja firma nie sprzedaje *licencji* z oprogramowaniem. Zamiast tego każdy klient sprawia, że wynajmować płatności do Twojej firmy, dzięki czemu każdy klient *dzierżawy* swojej firmy.

Poinformowanie płacenia nadrzędne, każda dzierżawa uzyskuje dostęp do składników aplikacji SaaS, a ma swoje dane przechowywane w systemie SaaS.

Termin *modelu dzierżawy* odwołuje się do organizowania dzierżawców przechowywanych danych:

- *Jednym dzierżawy:*&nbsp; Każda baza danych przechowuje dane z tylko jedną dzierżawą.
- *Wielodostępność:*&nbsp; Każda baza danych przechowuje dane z wielu oddzielnych dzierżaw (z mechanizmy ochrony prywatności danych).
- Dostępne są również modele dzierżawy hybrydowej.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak wybrać model odpowiednich dzierżawców

Ogólnie rzecz biorąc model dzierżawy nie ma wpływu na funkcji aplikacji, ale prawdopodobnie ma wpływ na inne aspekty ogólnego rozwiązania.  Poniższe kryteria są używane do oceny wszystkich modeli:

- **Skalowalność:**
    - Liczby dzierżawców.
    - Magazyn dla dzierżawcy.
    - Magazyn w agregacji.
    - Obciążenie.

- **Izolacja dzierżawy:**&nbsp; Izolacja danych i wydajności (czy obciążenie jednej dzierżawy ma wpływ na inne osoby).

- **Koszt dla dzierżawcy:**&nbsp; Koszty bazy danych.

- **Złożoność programowania:**
    - Zmiany schematu.
    - Zmiany do zapytań (wymagane przez wzorzec).

- **Złożoność operacyjną:**
    - Monitorowanie i zarządzanie wydajnością.
    - Zarządzanie schematami.
    - Przywracanie dzierżawy.
    - Odzyskiwanie sprawności systemu po awarii.

- **Dostosowywalności:**&nbsp; Łatwość obsługi dostosowania schematu, które są specyficzne dla dzierżawy lub swoiste dla klas dzierżawy.

Omówienie dzierżawy koncentruje się na *danych* warstwy.  Jednak należy wziąć pod uwagę na chwilę *aplikacji* warstwy.  Warstwa aplikacji jest traktowane jako monolityczny jednostki.  Jeśli dzielisz aplikacji na wiele małych składników wybranych przez siebie model dzierżawy mogą ulec zmianie.  Niektóre składniki można traktować inaczej niż inne, zarówno dzierżawy, jak i technologii magazynowania lub platforma używana.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Autonomiczna aplikacja jednej dzierżawy z jedną dzierżawą bazy danych

#### <a name="application-level-isolation"></a>Poziom izolacji aplikacji

W tym modelu cała aplikacja zostanie zainstalowana wielokrotnie, raz dla każdego dzierżawcy.  Każde wystąpienie aplikacji jest wystąpienia autonomicznego, więc nigdy nie wchodzi w interakcję inne wystąpienia autonomicznego.  Każde wystąpienie aplikacji ma tylko jedną dzierżawą i dlatego wymaga tylko jedną bazę danych.  Dzierżawa istnieje baza danych, wszystkie do samego siebie.

![Projekt aplikacji autonomicznych przy użyciu dokładnie jeden pojedynczej dzierżawy bazy danych.][image-standalone-app-st-db-111a]

Każde wystąpienie aplikacji jest zainstalowany w grupie oddzielne zasoby platformy Azure.  Grupa zasobów może należeć do subskrypcji, który jest własnością dostawcy oprogramowania lub dzierżawcy.  W obu przypadkach dostawcy można zarządzać oprogramowania dla dzierżawy.  Każde wystąpienie aplikacji jest skonfigurowany do łączenia się z jego odpowiedniego bazą danych.

Każda baza danych dzierżawy jest wdrażany jako pojedynczą bazę danych.  Ten model zapewnia największą izolację bazy danych.  Ale izolację wymaga, że wystarczające zasoby można przydzielić do każdej bazy danych, aby obsłużyć jej szczytowe obciążenie.  W tym miejscu ma znaczenia, czy pule elastyczne nie można użyć dla baz danych, wdrożonych w różnych grupach zasobów lub różnych subskrypcji.  To ograniczenie sprawia, że ta aplikacja jednej dzierżawy autonomiczny model najbardziej kosztowne rozwiązanie z perspektywy ogólny koszt bazy danych.

#### <a name="vendor-management"></a>Zarządzanie dostawcami

Dostawcy dostęp do wszystkich baz danych wszystkich autonomicznych wystąpień aplikacji, nawet jeśli wystąpień aplikacji są instalowane w ramach subskrypcji do innej dzierżawy.  Dostęp odbywa się za pośrednictwem połączeń z serwerem SQL.  Ten dostęp dla wielu wystąpień można włączyć dostawcy, można scentralizować zarządzania schematami i zapytań między bazami danych dla celów raportowania lub analizy.  W razie potrzeby tego rodzaju scentralizowanego zarządzania wykazem musi zostać wdrożony mapująca identyfikatorów dzierżawy na bazę danych, identyfikatory URI.  Usługa Azure SQL Database udostępnia bibliotekę fragmentowania, która jest używana wraz z bazy danych SQL do udostępniania katalogu.  Formalnie nosi nazwę biblioteki fragmentowania [Biblioteka kliencka Elastic Database][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplikacji wielodostępnej za pomocą bazy danych dla dzierżawy

Ten wzorzec dalej korzysta z aplikacji z wieloma dzierżawami z wieloma bazami danych, są wszystkie bazy danych z jedną dzierżawą.  Nowa baza danych jest aprowizowany dla każdej nowej dzierżawy.  Warstwa aplikacji jest skalowana *się* w pionie, dodając więcej zasobów w każdym węźle.  Lub aplikacja jest skalowana *się* w poziomie, dodając więcej węzłów.  Skalowanie jest oparty na obciążeniu, a nie zależy od liczby lub skalowania poszczególnych baz danych.

![Projekt aplikacji wielodostępnej za pomocą bazy danych dla dzierżawy.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Dostosowywanie dla dzierżawy

Like wzór aplikacji autonomicznej korzystanie z baz danych z jedną dzierżawą zapewnia izolacji dzierżawcy silne.  We wszystkich aplikacjach, w której model określa tylko jedną dzierżawą bazy danych można dostosować i zoptymalizowane pod kątem swojej dzierżawy schematu dla jednej danej bazy danych.  To dostosowanie nie ma wpływu na innych dzierżaw w aplikacji. Być może dzierżawy może być konieczne po osiągnięciu pola danych podstawowych, które muszą wszystkich dzierżaw.  Ponadto pola dodatkowe dane, może być konieczne indeksu.

Z bazy danych dla dzierżawcy Dostosowywanie schematu dla co najmniej jednego dzierżawcy poszczególnych jest proste do osiągnięcia.  Z dostawcą aplikacji, należy zaprojektować procedury uważna Obsługa schematu dostosowań na dużą skalę.

#### <a name="elastic-pools"></a>Pule elastyczne

Bazy danych są wdrażane w tej samej grupie zasobów, mogą być grupowane w pulach elastycznych.  Pule oferują ekonomiczny sposób udostępniania zasobów między bazami danych.  Ta opcja puli jest tańsze niż wymaganie każdej bazy danych do być wystarczająco duży, aby obsłużyć szczytowe użycie, które napotyka go.  Mimo że bazy danych współużytkują dostęp do zasobów można nadal osiągnąć wysokiego stopnia izolacji wydajności.

![Projekt aplikacji wielodostępnej za pomocą bazy danych — dla dzierżawcy, używając puli elastycznej.][image-mt-app-db-per-tenant-pool-153p]

Usługa Azure SQL Database udostępnia narzędzia niezbędne do konfigurowania, monitorowania i zarządzania udostępnianiem.  Zarówno metryk wydajności puli i na poziomie bazy danych są dostępne w witrynie Azure portal i za pomocą dzienników usługi Azure Monitor.  Metryki można nadać bardzo szczegółowych informacji o wydajności agregacji i specyficznym dla dzierżawy.  Pojedyncze bazy danych można przenosić między pulami, aby zapewnić zarezerwowanych zasobów do określonej dzierżawy.  Te narzędzia pozwalają zapewnić dobrą wydajność w sposób niskie koszty.

#### <a name="operations-scale-for-database-per-tenant"></a>Operacje skalowania bazy danych dla dzierżawy

Platforma Azure SQL Database oferuje wiele funkcji zarządzania, przeznaczona do zarządzania dużą liczbą baz danych na dużą skalę, takich jak bazy danych oraz ponad 100 000.  Te funkcje ułatwiające wiarygodne wzorca bazy danych dla dzierżawcy.

Na przykład załóżmy, że system ma dzierżawy 1000 bazę danych jako jej tylko jedną bazę danych.  Baza danych może być 20 indeksów.  Jeśli system jest konwertowany na o 1000 baz danych z pojedynczą dzierżawą, liczba indeksów wzrasta do 20 000.  W bazie danych SQL jako część [dostrajania automatycznego][docu-sql-db-automatic-tuning-771a], automatycznego indeksowania funkcji są domyślnie włączone.  Automatyczne indeksowanie zarządza dla Ciebie, wszystkie indeksy 20 000 i ich bieżących optymalizacje tworzenie i upuszczanie.  Te zautomatyzowane akcje występują w ramach poszczególnych baz danych, a nie są one koordynowany lub ograniczone przez podobnych działań w innych bazach danych.  Automatyczne indeksowanie traktuje indeksy inaczej w zajętej bazy danych niż mniejszego obciążenia bazy danych.  Tego rodzaju dostosowanie zarządzania indeksem byłaby to niepraktyczne na dużą skalę bazy danych dla dzierżawcy to zadanie zarządzania ogromna musiały być wykonywane ręcznie.

Inne funkcje zarządzania, które jest dobrze skalowalna, obejmują:

- Wbudowaną funkcję kopii zapasowych.
- Wysoka dostępność.
- Szyfrowanie na dysku.
- Danych telemetrycznych wydajności.

#### <a name="automation"></a>Automatyzacja

Operacje zarządzania może być uwzględnione w skryptach i oferowana za pośrednictwem [devops] [ http-visual-studio-devops-485m] modelu.  Operacje mogą nawet zautomatyzowane i widoczne w aplikacji.

Na przykład można zautomatyzować odzyskiwanie pojedynczej dzierżawy do wcześniejszego punktu w czasie.  Odzyskiwanie musi tylko przywracanie jednej bazy danych pojedynczej dzierżawy, która przechowuje dzierżawy.  Przywracanie nie ma wpływu na innych dzierżaw potwierdzenie, czy operacje zarządzania na poziomie precyzyjnie szczegółową każdego pojedynczego dzierżawcy.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplikacji wielodostępnej za pomocą bazy danych z wieloma dzierżawcami

Inny wzorzec dostępne jest przechowywanie wielu dzierżaw w wielodostępnej bazie danych.  Wystąpienie aplikacji może mieć dowolną liczbę baz danych z wieloma dzierżawami.  Schemat z wielodostępną bazą danych musi mieć co najmniej jedną kolumnę identyfikatora dzierżawy, dzięki czemu można selektywnie pobrać dane z dowolnego danej dzierżawy.  Ponadto schematu mogą wymagać kilku tabel lub kolumn, które są używane przez tylko podzbiór dzierżaw.  Jednak statyczne kodu i danych referencyjnych jest zapisywane tylko raz i jest współużytkowany przez wszystkich dzierżaw.

#### <a name="tenant-isolation-is-sacrificed"></a>Jest to konieczne izolacji dzierżawcy

*Dane:*&nbsp; Wielodostępną bazą danych zawsze zwiększa izolacji dzierżawy.  Dane z wielu dzierżaw jest przechowywane razem w jednej bazie danych.  Podczas tworzenia aplikacji upewnij się, że zapytania nigdy nie uwidocznić dane z więcej niż jednej dzierżawy.  Usługa SQL Database obsługuje [zabezpieczenia][docu-sql-svr-db-row-level-security-947w], które mogą zostać wymuszone te dane zwrócone przez zapytanie zakresu do pojedynczej dzierżawy.

*Przetwarzanie:*&nbsp; Wielodostępną bazą danych współużytkuje zasobów obliczeniowych i magazynu dla wszystkich swoich dzierżaw.  Baza danych jako całość można monitorować w taki sposób, aby upewnić się, że jest akceptowalne.  Jednak Azure system nie ma wbudowanej możliwości monitorowania lub zarządzania wykorzystaniem tych zasobów przez pojedynczą dzierżawę.  W związku z tym wielodostępną bazą danych niesie ze sobą zwiększone ryzyko napotkania sąsiadów generujące dużo alertów, gdy obciążenie jednej dzierżawy overactive ma wpływ na środowisko wydajność innych dzierżawców w tej samej bazy danych.  Dodatkowe monitorowanie na poziomie aplikacji można monitorować wydajność na poziomie dzierżawy.

#### <a name="lower-cost"></a>Niższy koszt

Ogólnie rzecz biorąc wielodostępnych baz danych ma najniższą dla dzierżawcy kosztów.  Koszty zasobów dla pojedynczej bazy danych są niższe niż ekwiwalentnie wielkości puli elastycznej.  Ponadto w przypadku scenariuszy, w której dzierżawy muszą tylko ograniczony magazyn potencjalnie milionów dzierżaw mogą być przechowywane w jednej bazie danych.  Brak elastycznej puli może zawierać miliony baz danych.  Jednak rozwiązanie zawierające 1000 baz danych w jednej puli z pulami 1000 można skontaktować się z skalowania milionów ryzyko stają się one nieporęczne za zarządzanie.

W poniżej, za pomocą modelu wielodostępnym podzielonej na fragmenty, elastyczna i skalowalna omówiono dwie odmiany modelu wielodostępną bazą danych.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplikacji wielodostępnej za pomocą jednej wielodostępnej bazie danych

Najprostszy wzorzec wielodostępną bazą danych używa pojedynczej bazy danych do przechowywania danych dla wszystkich dzierżaw.  Po dodaniu więcej dzierżaw bazy danych jest skalowany w górę więcej zasobów magazynu i mocy obliczeniowej.  Skalowanie w górę może być wszystko, co jest potrzebne, mimo że zawsze jest limit skalowania ultimate.  Jednak długo przed osiągnięciu tego limitu bazy danych staje się one nieporęczne za zarządzanie.

Operacje zarządzania, które koncentrują się na poszczególnych dzierżaw są bardziej złożone, aby zaimplementować w wielodostępnej bazie danych.  I na dużą skalę te operacje mogą stać się zbyt wolno.  Przykładem jest w momencie przywracania danych dla tylko jednej dzierżawy.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplikacji wielodostępnej za pomocą podzielonej na fragmenty wielodostępnych baz danych.

Większość aplikacji SaaS dostęp do danych tylko jednej dzierżawy w danym momencie.  Ten wzorzec dostępu pozwala na być rozproszone między wieloma bazami danych przy użyciu danych dzierżawy lub fragmentów, w którym wszystkie dane dla każdego dzierżawcy znajduje się w jednym fragmencie.  W połączeniu z wzorcem wielodostępną bazą danych podzielonych na fragmenty modelu umożliwia niemal nieograniczonej skali.

![Projekt aplikacji wielodostępnej za pomocą podzielonej na fragmenty wielodostępnych baz danych.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Zarządzanie fragmentami

Dzielenie na fragmenty zwiększa złożoność zarówno do projektowania i zarządzanie operacyjne.  Wykaz jest wymagany w której ma zostać Obsługa mapowania między dzierżawami i baz danych.  Ponadto procedury zarządzania są wymagane do zarządzania fragmentami i wypełniania dzierżawy.  Na przykład procedury muszą być zaprojektowane, aby dodawać i usuwać fragmenty i przenoszenie danych dzierżawy między fragmentami.  Jednym ze sposobów skalowania jest przez dodanie nowych fragmentów i zapełnianie nowych dzierżaw.  W pozostałych godzinach zagęszczenie fragmentów może podzielić dwóch mniej-zagęszczenie fragmentów.  Po kilka dzierżaw zostały przeniesione lub wycofane, słabo wypełnionych fragmentów może być scalone razem.  Scalanie mogłoby spowodować więcej wykorzystanie zasobów ekonomiczny sposób.  Dzierżawcy mogą także przenosić między fragmentami w celu równoważenia obciążenia.

SQL Database udostępnia narzędzie dzielenia i scalania, które działa w połączeniu z biblioteką dzielenia na fragmenty i baza danych wykazu.  Podana aplikacja można podzielić i scalania fragmentów i przenosić dane dzierżawy między fragmentami.  Aplikacja udostępnia również, że katalogu podczas tych czynności, oznaczanie wpływ dzierżaw jako trybu offline przed przeniesienie ich.  Po przeniesieniu aplikacji aktualizacji katalogu ponownie przy użyciu nowego mapowania i znakowania dzierżawy jako wróci do trybu online.

#### <a name="smaller-databases-more-easily-managed"></a>Mniejszych baz danych więcej łatwe zarządzanie

Dzięki rozłożeniu dzierżaw w wielu bazach danych, rozwiązanie dla wielu dzierżawców podzielonej na fragmenty skutkuje mniejszych baz danych, aby łatwiej zarządzanych.  Na przykład przywracanie z określoną dzierżawą do wcześniejszego punktu w czasie teraz obejmuje Przywracanie mniejszych pojedynczej bazy danych z kopii zapasowej, a nie większą bazę danych, która zawiera wszystkich dzierżaw. Rozmiar bazy danych i liczby dzierżawców na bazę danych, można wybrać do równoważenia obciążenia i wysiłki zarządzania.

#### <a name="tenant-identifier-in-the-schema"></a>Identyfikator dzierżawy w schemacie

W zależności od użytego podejścia fragmentowania dodatkowe ograniczenia mogą być nałożone na schemat bazy danych.  Aplikacja dzielenia i scalania SQL Database wymaga, że schemat zawiera klucz fragmentowania, który zazwyczaj jest to identyfikator dzierżawy.  Identyfikator dzierżawy jest wiodącym elementu w kluczu podstawowym wszystkich tabel podzielonej na fragmenty.  Identyfikator dzierżawy umożliwia aplikacji dzielenia i scalania szybko znaleźć i przenoszenie danych skojarzonych z określoną dzierżawą.

#### <a name="elastic-pool-for-shards"></a>Puli elastycznej dla fragmentów

Podzielonej na fragmenty wielodostępnych baz danych można umieścić w elastycznej puli.  Ogólnie rzecz biorąc wiele pojedynczej dzierżawy baz danych w puli jest jako opłacalne jako mające wiele dzierżaw w kilku wielodostępnych baz danych.  Wielodostępnych baz danych są korzystne w przypadku dużej liczby stosunkowo mało aktywnych dzierżaw.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybrydowe podzielonej na fragmenty wielodostępną bazą danych modelu

W modelu hybrydowego wszystkich baz danych ma identyfikator dzierżawy w ich schematu.  Bazy danych znajdują się wszystkie można przechowywać więcej niż jednej dzierżawy i baz danych może być podzielonej na fragmenty.  Tak, aby w tym sensie schematu wszystkich wielodostępnych baz danych.  Jeszcze w praktyce niektóre z tych baz danych zawiera tylko jedną dzierżawą.  Niezależnie od tego ilość dzierżaw przechowywanych w danej bazie danych nie ma wpływu na schemat bazy danych.

#### <a name="move-tenants-around"></a>Poruszanie się dzierżaw

W dowolnym momencie można przenieść danego dzierżawy do własną bazę danych z wieloma dzierżawami.  I w dowolnym momencie możesz zmienić zdanie i przenieść dzierżawy z powrotem do bazy danych, który zawiera wiele dzierżaw.  Można także przypisać dzierżawy do nowej bazy danych z pojedynczą dzierżawą, podczas aprowizacji nowej bazy danych.

Hybrydowego modelu świeci, gdy istnieją duże różnice między zapotrzebowanie na zasoby do zidentyfikowania grup dzierżaw.  Na przykład załóżmy, że dzierżaw udział w bezpłatnej wersji próbnej nie są gwarantowane tego samego wysoki poziom wydajności, które są subskrypcji dzierżawcy.  Zasady może być dla dzierżaw w bezpłatnej wersji próbnej fazie mają być przechowywane w wielodostępnej bazie danych jest współużytkowana przez wszystkie dzierżawy bezpłatnej wersji próbnej.  Gdy bezpłatnej wersji próbnej dzierżawca subskrybuje warstwa podstawowa usług, dzierżawcy można przenieść do innej bazy danych wielu dzierżawców, która może być mniej dzierżaw.  Na subskrybencie, który płaci za warstwę premium można przenieść do jego własnej nowej bazy danych jedną dzierżawą.

#### <a name="pools"></a>Pule

W tym modelu hybrydowego pojedynczej dzierżawy baz danych dzierżaw subskrybenta można umieścić w pulach zasobów w celu zmniejszenia kosztów bazy danych na dzierżawę.  To również odbywa się w modelu bazy danych dla dzierżawcy.

## <a name="i-tenancy-models-compared"></a>I. Modele dzierżawców w porównaniu

Poniższa tabela zawiera podsumowanie różnic między modelami głównej dzierżawy.

| Miara | Aplikacja autonomiczna | Bazy danych dla dzierżawcy | Podzielonej na fragmenty wielodostępnych |
| :---------- | :------------- | :------------------ | :------------------- |
| Skalowanie | Medium<br />1 100s | Bardzo wysokie<br />1 100,000s | Nieograniczona liczba<br />1-1 000 000 |
| Izolacji dzierżawcy | Bardzo wysokie | Wysoka | Niska; z wyjątkiem wszelkich pojedynczej dzierżawy (dotyczy to tylko w bazie danych MT). |
| Koszt bazy danych na dzierżawę | Wysoki; rozmiar jest szczytów wyrażonych w godzinach. | Niska; pul użytych. | LOWEST, małych dzierżaw w MT baz danych. |
| Zarządzania i monitorowania wydajności | Dla dzierżawcy tylko | Wartość zagregowana + dla dzierżawcy | Wartość zagregowana; Mimo że jest dla dzierżawy tylko w przypadku wybiera. |
| Złożoność rozwoju | Małe | Małe | Średnia; ze względu na dzielenie na fragmenty. |
| Złożoność operacyjną | Niska wysoka. Indywidualnie prosty, złożonych na dużą skalę. | Low-Medium. Wzorce dotyczą złożoności na dużą skalę. | Niska wysoka. Zarządzanie dzierżawą poszczególnych jest złożony. |
| &nbsp; ||||

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie i eksplorowanie aplikacji Wingtip z wieloma dzierżawami, która używa modelu SaaS bazy danych dla dzierżawcy — usługi Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Aplikacja Wtp przykładowe SaaS usługi Azure SQL Database dzierżawców — Zapraszamy!][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Projekt aplikacji autonomicznych przy użyciu dokładnie jeden pojedynczej dzierżawy bazy danych."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Projekt aplikacji wielodostępnej za pomocą bazy danych dla dzierżawy."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Projekt aplikacji wielodostępnej za pomocą bazy danych — dla dzierżawcy, używając puli elastycznej."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Projekt aplikacji wielodostępnej za pomocą podzielonej na fragmenty wielodostępnych baz danych."

