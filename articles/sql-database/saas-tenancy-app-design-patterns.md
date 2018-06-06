---
title: Wzorce SaaS wielodostępne — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wymagań i wspólne dane wzorce architektura wielodostępnej oprogramowania jako usługa (SaaS) aplikacje baz danych, które są uruchamiane w środowisku chmury Azure.
keywords: samouczek usługi sql database
services: sql-database
author: billgib
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.reviewer: genemi
ms.author: billgib
ms.openlocfilehash: 39be48019979ceb1337cbd3008c8cf071d403310
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737684"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Wielodostępne SaaS bazy danych dzierżawców wzorców

Podczas projektowania wielodostępnych aplikacji SaaS, musisz wybrać dokładnie model dzierżawy, który najlepiej odpowiada potrzebom aplikacji.  Model dzierżawy określa odwzorowania każdego dzierżawcy danych do magazynu.  Wybór modelu dzierżawy ma wpływ na projekt aplikacji i zarządzania.  Przełączenie do innego modelu później czasami jest kosztowna.

W tym artykule opisano modeli alternatywnych dzierżawy.

## <a name="a-saas-concepts-and-terminology"></a>A. Pojęcia SaaS i terminologia

Oprogramowanie jako usługa (SaaS), firma nie sprzedaje *licencji* oprogramowania. Zamiast tego każdy klient sprawia, że wynajmować płatności w firmie, co każdy klient *dzierżawy* firmy.

W zamian za płatności dzierżawę, każdy z dzierżawców uzyskuje dostęp do składniki aplikacji SaaS, a jego danych przechowywanych w systemie SaaS.

Termin *modelu dzierżawy* odwołuje się do organizowania przechowywanych danych dzierżawców:

- *Dzierżawy jednym:* &nbsp; każda baza danych przechowuje dane z tylko jednego dzierżawcy.
- *Wielodostępność:* &nbsp; każda baza danych przechowuje dane z wieloma dzierżawcami oddzielne (za pomocą mechanizmów, aby chronić prywatność danych).
- Dostępne są również modele hybrydowe dzierżawy.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak wybrać odpowiednią dzierżawy model

Ogólnie rzecz biorąc model dzierżawy nie ma wpływu na działanie aplikacji, ale prawdopodobnie ją ma wpływ na inne aspekty ogólnego rozwiązania.  Poniższe kryteria są używane do oceny każdego modeli:

- **Skalowalność:**
    - Liczba dzierżaw.
    - Magazyn na dzierżawcy.
    - Magazyn w agregacji.
    - Obciążenie pracą.

- **Odizolowania dzierżawców:** &nbsp; izolacji danych i wydajności (czy obciążenie jednego dzierżawy ma wpływ na inne).

- **Koszt dzierżawy:** &nbsp; bazy danych kosztów.

- **Programowanie złożoność:**
    - Zmiany schematu.
    - Zmiany kwerendy (wymaganego przez wzorzec).

- **Złożoność działania:**
    - Monitorowanie i zarządzanie wydajnością.
    - Zarządzanie schematu.
    - Przywracanie dzierżawcy.
    - Odzyskiwanie sprawności systemu po awarii.

- **Dostosowywalności:** &nbsp; łatwość obsługi dostosowania schematu, które są specyficzne dla dzierżawy lub dzierżawy specyficzne dla klasy.

Omówienie dzierżawy koncentruje się na *danych* warstwy.  Jednak należy wziąć pod uwagę chwilę *aplikacji* warstwy.  Warstwa aplikacji jest traktowany jako wbudowanymi jednostki.  Dzieląc aplikację na wiele małych składniki wybór modelu dzierżawy mogą ulec zmianie.  Niektóre składniki można traktować inaczej niż inne dotyczące zarówno dzierżawy i technologii magazynowania lub użyć platformy.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Autonomiczny pojedynczej dzierżawy aplikacji za pomocą pojedynczej dzierżawy bazy danych

#### <a name="application-level-isolation"></a>Poziom izolacji aplikacji

W tym modelu całej aplikacji jest zainstalowany, jeden raz dla każdego dzierżawcy.  Każde wystąpienie aplikacji jest wystąpienia autonomicznego, więc nigdy nie interakcji z dowolnego innego wystąpienia autonomicznego.  Każde wystąpienie aplikacji dzierżawą tylko jeden i dlatego wymaga tylko jedną bazę danych.  Dzierżawca ma bazy danych wszystkich do samej siebie.

![Projekt aplikacji autonomicznych przy użyciu dokładnie jeden dzierżawy pojedynczej bazy danych.][image-standalone-app-st-db-111a]

Każde wystąpienie aplikacji jest zainstalowany w grupie oddzielnych zasobów platformy Azure.  Grupa zasobów może należeć do subskrypcji, którego właścicielem jest dostawcą oprogramowania lub dzierżawcy.  W obu przypadkach dostawcę można zarządzać oprogramowania dla dzierżawcy.  Każde wystąpienie aplikacji jest skonfigurowanej do połączenia jej z odpowiednią bazę danych.

Każda baza danych dzierżawy jest wdrażany jako autonomiczna baza danych.  Ten model zawiera największy izolacji bazy danych.  Ale izolację wymaga przydzielone wystarczające zasoby do każdej bazy danych do obsługi obciążeń jego godzinami szczytu.  W tym miejscu ma znaczenia, czy pule elastyczne nie może służyć do baz danych wdrożonych w różnych grupach zasobów lub różnych subskrypcji.  To ograniczenie sprawia, że ta aplikacja pojedynczej dzierżawy autonomiczny model najbardziej kosztowne rozwiązanie z ogólną widzenia kosztów bazy danych.

#### <a name="vendor-management"></a>Zarządzanie dostawcami

Dostawcy mają dostęp do wszystkich baz danych wszystkich aplikacji wystąpień autonomicznych, nawet jeśli wystąpienia aplikacji są instalowane w ramach dzierżawy różnych subskrypcji.  Uzyskuje się dostęp za pośrednictwem połączeń z serwerem SQL.  Dostęp do tego wystąpienia między można włączyć dostawcy zapewnić scentralizowane zarządzanie schematami i między bazami danych zapytania do celów raportowania lub analizy.  W razie potrzeby tego rodzaju scentralizowane zarządzanie wykaz należy wdrożyć mapowanego dzierżawy identyfikatorów URI bazy danych.  Baza danych SQL Azure udostępnia biblioteki dzielenia na fragmenty, która jest używana razem z bazą danych SQL w celu dostarczenia wykaz.  Formalnie nosi nazwę biblioteki dzielenia na fragmenty [elastycznej bazy danych klienta biblioteki][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Wielodostępne aplikacji z bazy danych dla dzierżawcy

Ten wzorzec dalej używa aplikacji wielodostępnych z wielu baz danych, wszystkie trwa pojedynczej dzierżawy baz danych.  Nowa baza danych jest przeznaczona dla każdej nowej dzierżawy.  Skalowania warstwy aplikacji *się* w pionie, dodając więcej zasobów w każdym węźle.  Aplikacja jest pokazany *limit* poziomo, dodając więcej węzłów.  Skalowanie jest oparta na obciążenie i zależy od liczby lub skali pojedynczych baz danych.

![Projekt aplikacji wielodostępnych z bazy danych dla dzierżawy.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Dostosowywanie dla dzierżawcy

Takich jak wzorzec aplikacji autonomicznej użyj pojedynczej dzierżawy baz danych zapewnia izolacji dzierżawców silne.  W dowolnej aplikacji, których modelu określa tylko jednego dzierżawcy bazy danych można dostosować i zoptymalizowany pod kątem jego dzierżawy schematu dla jednej danego bazy danych.  Takie dostosowanie nie ma wpływu na innych dzierżawców w aplikacji. Być może dzierżawcy może być konieczne danych po przekroczeniu pola podstawowe dane, które muszą wszystkich dzierżawców.  Ponadto w polu dodatkowych danych może być konieczne indeksu.

Z bazy danych dla dzierżawy Dostosowywanie schematu dla co najmniej jednego dzierżawcy poszczególnych jest prosta do osiągnięcia.  Dostawca aplikacji należy projektować procedur dokładnie zarządzania dostosowania schematu na dużą skalę.

#### <a name="elastic-pools"></a>Pule elastyczne

Gdy baz danych są wdrożone w tej samej grupie zasobów, mogą być pogrupowane w elastycznych pul baz danych.  Pule umożliwiają ekonomiczne funkcji udostępniania zasobów przez wiele baz danych.  Ta opcja puli jest tańsze niż wymagające każdą bazę danych do być wystarczająco duży, aby pomieścić szczytowe, które napotyka go.  Mimo że puli baz danych udzielać dostępu do zasobów można nadal osiągnąć wysoki stopień izolacji wydajności.

![Projekt aplikacji wielodostępnych z bazy danych na dzierżawy, przy użyciu puli elastycznej.][image-mt-app-db-per-tenant-pool-153p]

Baza danych SQL Azure udostępnia narzędzia niezbędne do skonfigurowania, monitorowanie i Zarządzanie udostępnianiem.  Zarówno metryk wydajności puli poziomie bazy danych i są dostępne w portalu Azure i przy użyciu analizy dzienników.  Metryki zapewnić doskonałe wgląd w działania agregacji i specyficznego dla dzierżawy.  Pojedyncze bazy danych można przenosić między pulami zapewnienie zarezerwowanych zasobów w określonym dzierżawcy.  Te narzędzia pozwalają zapewnić dobrą wydajność w ekonomicznym.

#### <a name="operations-scale-for-database-per-tenant"></a>Skalowanie operacji dla bazy danych dla dzierżawcy

Platforma Azure SQL Database oferuje wiele funkcji zarządzania przeznaczona do zarządzania dużą liczbą baz danych na dużą skalę, takich jak również ponad 100 000 baz danych.  Te funkcje, że wzorca bazy danych na dzierżawy wiarygodne.

Na przykład załóżmy, że system ma dzierżawy 1000 bazy danych jako jego tylko jedną bazę danych.  Baza danych może zawierać indeksów 20.  Jeśli system konwertuje o 1000 pojedynczego dzierżawcy z bazy danych, liczba indeksów wzrasta do 20 000.  W bazie danych SQL w ramach [automatycznego dostrajania][docu-sql-db-automatic-tuning-771a], automatycznego indeksowania funkcje są domyślnie włączone.  Automatycznego indeksowania zarządza dla Ciebie wszystkich indeksów 20 000 i ich trwającą optymalizacje tworzenie i upuść.  Te akcje automatyczne występują w ramach poszczególnych bazy danych, a nie są one koordynowane lub ograniczone przez podobnych działań w innych bazach danych.  Automatyczne indeksowanie traktuje indeksów inaczej zajęty bazy danych niż w bazie danych mniej zajęty.  Dostosowania zarządzania indeks tego typu będą niepraktyczne na dużą skalę bazy danych dla dzierżawy, jeśli to zadanie zarządzania ogromnych musiało być przeprowadzane ręcznie.

Inne funkcje zarządzania, które skalowalne są następujące:

- Wbudowane kopii zapasowych.
- Wysoka dostępność.
- Szyfrowanie na dysku.
- Dane telemetryczne wydajności.

#### <a name="automation"></a>Automatyzacja

Operacje zarządzania mogą być uwzględnione w skryptach i oferowane przez [devops] [ http-visual-studio-devops-485m] modelu.  Czynności można nawet automatycznego i widoczne w aplikacji.

Na przykład można zautomatyzować odzyskiwanie pojedynczej dzierżawy do wcześniejszego punktu w czasie.  Odzyskiwanie tylko musi przywrócić jedną bazę danych pojedynczej dzierżawy przechowujący dzierżawcy.  To przywracanie nie ma wpływu na innych dzierżawców, który potwierdza, czy operacje zarządzania na poziomie szczegółowym precyzyjne każdego pojedynczego dzierżawcy.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Wielodostępne aplikacji z bazami danych z wieloma dzierżawcami

Inny wzorzec dostępne jest przechowywać wiele dzierżaw w bazie danych wielodostępnej.  Wystąpienie aplikacji może mieć dowolną liczbę baz danych z wieloma dzierżawcami.  Schemat bazy danych z wieloma dzierżawcami musi mieć co najmniej jedną kolumnę Identyfikator dzierżawy, tak aby można selektywnie pobrać danych z żadnych danej dzierżawy.  Ponadto schematu może wymagać kilku tabel lub kolumn, które są używane przez tylko podzestaw dzierżaw.  Jednak dane statyczne kodu i odwołania są przechowywane tylko raz i jest współużytkowana przez wszystkie dzierżaw.

#### <a name="tenant-isolation-is-sacrificed"></a>Jest to konieczne izolacji dzierżawców

*Dane:* &nbsp; wielodostępne bazy danych zawsze zwiększa izolacji dzierżawców.  Dane wielu dzierżawców jest przechowywane razem w jednej bazie danych.  Podczas tworzenia upewnij się, że zapytania nigdy nie uwidacznia danych z więcej niż jednego dzierżawcy.  Baza danych SQL obsługuje [wiersza poziomu zabezpieczeń][docu-sql-svr-db-row-level-security-947w], które można wymusić te dane zwrócone w wyniku zapytania ograniczone do pojedynczej dzierżawy.

*Przetwarzanie:* &nbsp; bazy danych z wieloma dzierżawcami udostępnia zasoby obliczeniowe i magazynujące we wszystkich jego dzierżawców.  Aby upewnić się, że jest akceptowalne można monitorować bazy danych jako całość.  Jednak Azure system nie ma wbudowane możliwości monitorowania lub zarządzać wykorzystaniem tych zasobów przez poszczególne dzierżawy.  W związku z tym wielodostępne bazy danych wykonuje zwiększone ryzyko napotkania sąsiadów zakłócenia, w którym obciążenie jednego overactive dzierżawy ma wpływ na wydajność innych dzierżawców w tej samej bazy danych.  Dodatkowe monitorowanie na poziomie aplikacji można monitorować wydajność na poziomie dzierżawy.

#### <a name="lower-cost"></a>Niższe koszty

Ogólnie rzecz biorąc baz danych z wieloma dzierżawcami ma najniższą na — dzierżawcy kosztów.  Koszty zasobów autonomiczna baza danych są niższe niż dla puli elastycznej ekwiwalentnie rozmiarze.  Ponadto w scenariuszach, w którym dzierżawcy wymagają tylko ograniczone do magazynu, potencjalnie miliony dzierżawcy może być przechowywane w pojedynczej bazy danych.  Nie puli elastycznej może zawierać miliony baz danych.  Jednak rozwiązanie zawierające 1000 baz danych w jednej puli przy użyciu pul 1000 mogą osiągnąć skali miliony ryzyko staje się niewygodna do zarządzania.

Dwie odmiany model bazy danych z wieloma dzierżawcami omówiono poniżej, z podzielonej modelu wielodostępnym trwa elastyczność i skalowalność.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Wielodostępne aplikacji za pomocą pojedynczej bazy danych z wieloma dzierżawcami

Najprostsza wzorca bazy danych z wieloma dzierżawcami używa jednej autonomicznej bazy danych do przechowywania danych dla wszystkich dzierżawców.  Po dodaniu więcej dzierżaw, bazy danych jest skalowanie więcej zasobów magazynu i zasobów obliczeniowych.  Tej skali zapasowej może być są wystarczające, mimo że zawsze jest limit ultimate skali.  Jednak long przed osiągnięciem tego limitu bazy danych staje się niewygodna do zarządzania.

Operacje zarządzania, które są koncentruje się na poszczególnych dzierżawców są bardziej złożone wdrożenie w wielu dzierżawców bazy danych.  I na dużą skalę te operacje mogą stać się zbyt wolne.  Przykładem jest w momencie przywracania danych dla tylko jednego dzierżawcy.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Wielodostępne aplikacji za pomocą podzielonej wielodostępnych baz danych

Większość aplikacji SaaS uzyskać dostęp do danych tylko jeden dzierżawy naraz.  Ten wzorzec dostępu umożliwia danych dzierżawy być rozproszone na wielu baz danych lub fragmentów, w którym wszystkie dane dla każdego dzierżawcy znajduje się w jednym niezależnego fragmentu.  W połączeniu z wzorca bazy danych z wieloma dzierżawcami, podzielonej modelu pozwala niemal nieograniczonego skali.

![Projekt aplikacji wielodostępnych podzielonej wielodostępnych baz danych.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Zarządzanie odłamków

Dzielenia na fragmenty dodaje złożoności zarówno do projektowania i zarządzanie operacyjne.  Katalog jest wymagana do obsługi mapowanie między dzierżawami i baz danych.  Ponadto procedury zarządzania są wymagane do zarządzania odłamków i wypełniania dzierżawy.  Na przykład procedur musi zostać zaprojektowana do dodawania i usuwania odłamków i zmieniać danych dzierżawcy fragmentów.  Jednym ze sposobów skalowania jest przez dodanie nowych niezależnego fragmentu i zapełnianie nowi dzierżawcy.  W pozostałych godzinach zagęszczenie niezależnego fragmentu może podzielone dwa niezależne mniej gęsto wypełnione.  Po kilku dzierżaw zostały przeniesione lub wycofane, słabo wypełnione odłamków może scalić ze sobą.  Scalanie spowoduje więcej wykorzystania zasobów ekonomiczny sposób.  Dzierżawcy mogą także przenosić między niezależne równoważenia obciążenia.

Baza danych SQL zawiera narzędzie podziału/merge, które działa w połączeniu z biblioteki dzielenia na fragmenty i baza danych katalogu.  Podana aplikacji można dzielić i odłamków scalania który można przenieść dane dzierżawy między fragmentów.  Aplikacji zachowuje również, że dzierżawcy jako trybu offline przed przeniesieniem ich wpływ na katalogu podczas tych czynności, oznaczenie.  Po przeniesieniu aplikacja zostanie zaktualizowana katalogu ponownie z nowym mapowaniu i oznakowanie dzierżawy jako z powrotem do trybu online.

#### <a name="smaller-databases-more-easily-managed"></a>Mniejsze baz danych więcej łatwo zarządzać

Przekazując dzierżaw wielu baz danych, podzielonej rozwiązania wielodostępnej powoduje mniejszych baz danych, co ułatwia zarządzanych.  Na przykład Przywracanie określonych dzierżawy do wcześniejszego punktu w czasie teraz obejmuje Przywracanie pojedynczej mniejszych bazy danych z kopii zapasowej, a nie większą bazę danych zawierającą wszystkich dzierżawców. Rozmiar bazy danych i liczby dzierżawców na bazę danych, można wybrać do równoważenia obciążenia i zadaniami zarządzania.

#### <a name="tenant-identifier-in-the-schema"></a>Identyfikator dzierżawy w schemacie

W zależności od użytego podejścia dzielenia na fragmenty dodatkowe ograniczenia mogą być nałożone na schemat bazy danych.  Aplikacja podziału/merge bazy danych SQL wymaga, że schemat zawiera klucz dzielenia na fragmenty, który zazwyczaj jest to identyfikator dzierżawy.  Identyfikator dzierżawy jest element wiodące w kluczu podstawowym wszystkie tabele podzielonej.  Identyfikator dzierżawy umożliwia aplikacji podziału/merge szybko lokalizować i przenieść dane skojarzone z określonym dzierżawcy.

#### <a name="elastic-pool-for-shards"></a>Puli elastycznej dla fragmentów

Podzielonej wielodostępnych baz danych można umieścić w puli elastycznej.  Ogólnie rzecz biorąc mających wiele pojedynczej dzierżawy baz danych w puli jest koszt wydajne jako zawierający wiele dzierżaw w kilku wielodostępnych baz danych.  Wielodostępne baz danych są korzystne w przypadku, gdy dużej liczby dzierżawców stosunkowo nieaktywne.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybrydowe podzielonej wielodostępne bazy danych modelu

W modelu hybrydowych wszystkie bazy danych ma identyfikator dzierżawy w ich schematu.  Bazy danych znajdują się wszystkie można przechowywać więcej niż jednej dzierżawy i baz danych może być podzielony na niezależne fragmenty.  Tak, aby w tym sensie schematu, wszystkie bazy danych z wieloma dzierżawcami.  Jeszcze w praktyce niektóre z tych baz danych zawierają tylko jednego dzierżawcy.  Niezależnie od tego ilość dzierżaw przechowywane w określonej bazie danych nie ma wpływu na schemat bazy danych.

#### <a name="move-tenants-around"></a>Poruszanie się dzierżawcy

W dowolnym momencie można przenieść określonego dzierżawy do własną bazę danych z wieloma dzierżawcami.  I w dowolnym momencie możesz zmienić zdanie i przenieść dzierżawcy z powrotem do bazy danych zawierającej wiele dzierżaw.  Można także przypisać dzierżawcy nowej dzierżawy pojedynczej bazy danych podczas obsługi administracyjnej nowej bazy danych.

Modelu hybrydowych możliwości, gdy istnieją duże różnice między zapotrzebowania na zasoby do zidentyfikowania grup dzierżawców.  Załóżmy na przykład, dzierżaw uczestniczących w ramach bezpłatnej wersji próbnej nie ma gwarancji poziomu wysokiej wydajności, które subskrypcji dzierżawcy są.  Zasady mogą być dla dzierżawców w bezpłatnej wersji próbnej fazy do zapisania w bazie danych wielodostępne współużytkowany bezpłatnej wersji próbnej dzierżaw.  Gdy bezpłatnej wersji próbnej dzierżawca subskrybuje poziomu usług podstawowa, dzierżawcy można przenieść do innej bazy danych wielu dzierżawców, która może być mniej dzierżaw.  Subskrybent płaci poziomu usług premium można jej przenieść do jego własnej nową bazę danych pojedynczej dzierżawy.

#### <a name="pools"></a>Pule

W tym modelu hybrydowego pojedynczej dzierżawy baz danych w przypadku dzierżaw subskrybenta można umieścić w pulach zasobów w celu zredukowania kosztów bazy danych dla każdego dzierżawcy.  To również odbywa się w modelu bazy danych dla dzierżawy.

## <a name="i-tenancy-models-compared"></a>I. Modele dzierżawy porównaniu

W poniższej tabeli przedstawiono różnice między modelami główny dzierżawy.

| Miara | Aplikacja autonomiczna | Bazy danych dla dzierżawcy | Podzielonej wieloma dzierżawcami |
| :---------- | :------------- | :------------------ | :------------------- |
| Skalowanie | Medium<br />1 100s | Bardzo wysokie<br />1 100,000s | Nieograniczona liczba<br />1 1,000,000s |
| Izolacji dzierżawców | Bardzo wysokie | Wysoka | Niski; z wyjątkiem dowolnej dzierżawy pojedyncze (która jest tylko do bazy danych MT). |
| Koszt bazy danych dla każdego dzierżawcy | Wysoki; rozmiar jest szczytów. | Niski; pule używane. | Najniższa, w przypadku małych dzierżaw w MT bazami danych. |
| Monitorowanie wydajności i zarządzanie | Dla dzierżawcy tylko | Agregacja + na dzierżawy | Agregacja; Mimo że jest na dzierżawy tylko dla pojedynczych wystąpień. |
| Programowanie złożoności | Małe | Małe | Średnia liczba godzin; z powodu dzielenia na fragmenty. |
| Złożoność działania | Wysoki niski. Proste pojedynczo, złożone na dużą skalę. | Low-Medium. Wzorce adresów złożoność na dużą skalę. | Wysoki niski. Zarządzanie pojedynczego dzierżawcy jest złożone. |
| &nbsp; ||||

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie i Eksploruj aplikacji Wingtip wielodostępne, która używa modelu SaaS bazy danych dla dzierżawcy — baza danych SQL Azure][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Bilety Wingtip przykładowej bazy danych SQL Azure SaaS dzierżawy aplikacji — Zapraszamy!][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Projekt aplikacji autonomicznych przy użyciu dokładnie jeden dzierżawy pojedynczej bazy danych."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Projekt aplikacji wielodostępnych z bazy danych dla dzierżawy."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Projekt aplikacji wielodostępnych z bazy danych na dzierżawy, przy użyciu puli elastycznej."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Projekt aplikacji wielodostępnych podzielonej wielodostępnych baz danych."

