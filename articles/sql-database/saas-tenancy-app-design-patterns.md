---
title: Wzorce SaaS dla wielu dzierżawców
description: Dowiedz się więcej o wymaganiach i typowych wzorcach architektury danych oprogramowania wielodostępnego jako usługi (SaaS) aplikacji bazy danych, które działają w środowisku chmury platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 956d74467c69d9924d26f9cae8d902a6ddd84496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067502"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Wzorce dzierżawy bazy danych SaaS dla wielu dzierżaw

W tym artykule opisano różne modele najmu dostępne dla aplikacji SaaS z wieloma dzierżawami.

Podczas projektowania aplikacji SaaS z wieloma dzierżawami należy starannie wybrać model najmu, który najlepiej odpowiada potrzebom aplikacji.  Model najmu określa, jak dane każdej dzierżawy są mapowane do magazynu.  Wybór modelu najmu ma wpływ na projektowanie aplikacji i zarządzanie nimi.  Późniejsze przejście na inny model jest czasami kosztowne.

## <a name="a-saas-concepts-and-terminology"></a>A. Koncepcje i terminologia SaaS

W modelu Oprogramowanie jako usługa (SaaS) firma nie sprzedaje *licencji na* oprogramowanie. Zamiast tego każdy klient dokonuje płatności czynszu na rzecz firmy, dzięki czemu każdy klient jest *najemcą* twojej firmy.

W zamian za płacenie czynszu każda dzierżawa otrzymuje dostęp do składników aplikacji SaaS i przechowuje swoje dane w systemie SaaS.

Termin *model najmu* odnosi się do sposobu organizowania przechowywanych danych dzierżawców:

- *Pojedynczy dzierżawa:* &nbsp; każda baza danych przechowuje dane tylko z jednej dzierżawy.
- *Multi-najem:* &nbsp; Każda baza danych przechowuje dane z wielu oddzielnych dzierżaw (z mechanizmami ochrony prywatności danych).
- Dostępne są również hybrydowe modele najmu.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak wybrać odpowiedni model najmu

Ogólnie rzecz biorąc model najmu nie ma wpływu na funkcję aplikacji, ale prawdopodobnie ma wpływ na inne aspekty ogólnego rozwiązania.  Do oceny każdego z modeli stosuje się następujące kryteria:

- **Skalowalność:**
    - Liczba najemców.
    - Magazyn na dzierżawcę.
    - Przechowywanie w ujęciu zbiorczym.
    - Obciążenia.

- **Izolacja dzierżawy:** &nbsp; izolacja danych i wydajność (niezależnie od tego, czy obciążenie jednej dzierżawy ma wpływ na inne osoby).

- **Koszt dzierżawy:** &nbsp; koszty bazy danych.

- **Złożoność rozwoju:**
    - Zmiany w schemacie.
    - Zmiany w kwerendach (wymagane przez wzorzec).

- **Złożoność operacyjna:**
    - Monitorowanie wydajności i zarządzanie nimi.
    - Zarządzanie schematem.
    - Przywracanie dzierżawy.
    - Odzyskiwanie sprawności systemu po awarii.

- **Możliwość dostosowania:** &nbsp; Łatwość obsługi dostosowań schematu, które są specyficzne dla dzierżawy lub specyficzne dla klasy dzierżawy.

Dyskusja na temat najmu koncentruje się na warstwie *danych.*  Ale rozważ przez chwilę warstwę *aplikacji.*  Warstwa aplikacji jest traktowana jako monolityczny element.  Jeśli aplikacja zostanie podzielona na wiele małych składników, wybór modelu najmu może ulec zmianie.  Niektóre komponenty można traktować inaczej niż inne, zarówno pod względem najmu, jak i zastosowanej technologii pamięci masowej lub platformy.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Autonomiczna aplikacja z jedną dzierżawą z bazą danych z jedną dzierżawą

#### <a name="application-level-isolation"></a>Izolacja poziomu aplikacji

W tym modelu cała aplikacja jest instalowana wielokrotnie, raz dla każdej dzierżawy.  Każde wystąpienie aplikacji jest autonomicznym wystąpieniem, więc nigdy nie wchodzi w interakcję z żadnym innym wystąpieniem autonomicznym.  Każde wystąpienie aplikacji ma tylko jedną dzierżawę i dlatego wymaga tylko jednej bazy danych.  Dzierżawca ma bazę danych dla siebie.

![Projektowanie autonomicznej aplikacji z dokładnie jedną bazą danych z jedną dzierżawą.][image-standalone-app-st-db-111a]

Każde wystąpienie aplikacji jest instalowane w oddzielnej grupie zasobów platformy Azure.  Grupa zasobów może należeć do subskrypcji, która jest własnością dostawcy oprogramowania lub dzierżawy.  W obu przypadkach dostawca może zarządzać oprogramowaniem dla dzierżawy.  Każde wystąpienie aplikacji jest skonfigurowane do łączenia się z odpowiednią bazą danych.

Każda baza danych dzierżawy jest wdrażana jako pojedyncza baza danych.  Ten model zapewnia największą izolację bazy danych.  Ale izolacja wymaga, aby wystarczające zasoby zostały przydzielone do każdej bazy danych do obsługi jego szczytowe obciążenia.  W tym miejscu ma znaczenie, że pule elastyczne nie mogą być używane dla baz danych wdrożonych w różnych grupach zasobów lub do różnych subskrypcji.  To ograniczenie sprawia, że ten samodzielny model aplikacji z jedną dzierżawą jest najdroższym rozwiązaniem z punktu widzenia kosztów bazy danych.

#### <a name="vendor-management"></a>Zarządzanie dostawcami

Dostawca może uzyskać dostęp do wszystkich baz danych we wszystkich autonomicznych wystąpień aplikacji, nawet jeśli wystąpienia aplikacji są zainstalowane w różnych subskrypcji dzierżawy.  Dostęp jest osiągany za pośrednictwem połączeń SQL.  Ten dostęp między wystąpieniami może umożliwić dostawcy scentralizowanie zarządzania schematem i kwerendy między bazami danych do celów raportowania lub analizy.  Jeśli ten rodzaj scentralizowanego zarządzania jest pożądany, należy wdrożyć katalog, który mapuje identyfikatory dzierżawy do identyfikatorów URI bazy danych.  Usługa Azure SQL Database udostępnia bibliotekę dzielenia na fragmenty, która jest używana razem z bazą danych SQL w celu zapewnienia katalogu.  Biblioteka dzielenia na fragmenty jest formalnie nazywana [biblioteką klienta elastycznej bazy danych.][docu-elastic-db-client-library-536r]

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplikacja dla wielu dzierżawców z bazą danych na dzierżawcę

Ten następny wzorzec używa aplikacji wielodostępnych z wieloma bazami danych, wszystkie są bazy danych z jedną dzierżawą.  Nowa baza danych jest aprowizowana dla każdej nowej dzierżawy.  Warstwa aplikacji jest skalowana w górę w *pionie* przez dodanie większej liczby zasobów na węzeł.  Lub aplikacja jest skalowana *w poziomie* przez dodanie większej liczby węzłów.  Skalowanie jest oparte na obciążeniu i jest niezależne od liczby lub skali poszczególnych baz danych.

![Projektowanie aplikacji wielodostępnej z bazą danych na dzierżawcę.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Dostosowywanie dla dzierżawy

Podobnie jak wzorzec aplikacji autonomicznej, użycie baz danych z jedną dzierżawą zapewnia silną izolację dzierżawy.  W dowolnej aplikacji, której model określa tylko bazy danych z jedną dzierżawą, schemat dla dowolnej danej bazy danych można dostosować i zoptymalizować pod kątem dzierżawy.  To dostosowanie nie wpływa na innych dzierżaw w aplikacji. Być może dzierżawca może potrzebować danych poza podstawowymi polami danych, których potrzebują wszyscy dzierżawcy.  Ponadto dodatkowe pole danych może wymagać indeksu.

Z bazy danych na dzierżawy dostosowywanie schematu dla jednego lub więcej poszczególnych dzierżaw jest proste do osiągnięcia.  Dostawca aplikacji musi zaprojektować procedury, aby starannie zarządzać dostosowaniami schematu na dużą skalę.

#### <a name="elastic-pools"></a>Pule elastyczne

Gdy bazy danych są wdrażane w tej samej grupie zasobów, mogą być pogrupowane w pulę elastyczną.  Pule zapewniają opłacalny sposób udostępniania zasobów w wielu bazach danych.  Ta opcja puli jest tańsza niż wymaganie, aby każda baza danych była wystarczająco duża, aby pomieścić szczyty użycia, które występują.  Mimo że buforowane bazy danych współużytkują dostęp do zasobów, nadal mogą osiągnąć wysoki stopień izolacji wydajności.

![Projektowanie aplikacji wielodostępnej z bazą danych na dzierżawcę przy użyciu puli elastycznej.][image-mt-app-db-per-tenant-pool-153p]

Usługa Azure SQL Database udostępnia narzędzia niezbędne do konfigurowania, monitorowania i zarządzania udostępnianiem.  Metryki wydajności na poziomie puli i bazy danych są dostępne w witrynie Azure portal i za pośrednictwem dzienników usługi Azure Monitor.  Metryki mogą dać doskonały wgląd w wydajność agregacji i specyficzne dla dzierżawy.  Poszczególne bazy danych mogą być przenoszone między pulami, aby zapewnić zarezerwowane zasoby do określonej dzierżawy.  Narzędzia te umożliwiają zapewnienie dobrej wydajności w opłacalny sposób.

#### <a name="operations-scale-for-database-per-tenant"></a>Skala operacji dla bazy danych na dzierżawcę

Platforma Azure SQL Database ma wiele funkcji zarządzania zaprojektowanych do zarządzania dużą liczbą baz danych na dużą skalę, takich jak ponad 100 000 baz danych.  Te funkcje sprawiają, że wzorzec bazy danych na dzierżawcę jest prawdopodobny.

Załóżmy na przykład, że system ma bazę danych 1000 dzierżaw jako jedyną bazę danych.  Baza danych może mieć 20 indeksów.  Jeśli system konwertuje do posiadania 1000 pojedynczych dzierżaw baz danych, ilość indeksów wzrasta do 20 000.  W bazie danych SQL w ramach [automatycznego dostrajania][docu-sql-db-automatic-tuning-771a]funkcje automatycznego indeksowania są domyślnie włączone.  Automatyczne indeksowanie zarządza dla Ciebie wszystkie 20 000 indeksów i ich ciągłe optymalizacje tworzenia i upuszczania.  Te zautomatyzowane akcje występują w obrębie pojedynczej bazy danych i nie są koordynowane ani ograniczone przez podobne akcje w innych bazach danych.  Automatyczne indeksowanie traktuje indeksy inaczej w zajętej bazie danych niż w mniej ruchliwej bazie danych.  Ten typ dostosowywania zarządzania indeksami byłoby niepraktyczne w skali bazy danych na dzierżawcę, jeśli to ogromne zadanie zarządzania trzeba było wykonać ręcznie.

Inne funkcje zarządzania, które dobrze skalują się, obejmują następujące elementy:

- Wbudowane kopie zapasowe.
- Wysoka dostępność.
- Szyfrowanie na dysku.
- Telemetria wydajności.

#### <a name="automation"></a>Automatyzacja

Operacje zarządzania mogą być skryptowane i oferowane za pośrednictwem modelu [devops.][http-visual-studio-devops-485m]  Operacje mogą być nawet zautomatyzowane i widoczne w aplikacji.

Na przykład można zautomatyzować odzyskiwanie pojedynczej dzierżawy do wcześniejszego punktu w czasie.  Odzyskiwanie musi tylko przywrócić jedną bazę danych z jedną dzierżawą, która przechowuje dzierżawcę.  To przywracanie nie ma wpływu na innych dzierżawców, co potwierdza, że operacje zarządzania są na poziomie precyzyjnie granulowany każdego dzierżawy poszczególnych.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplikacja wielodostępna z wielodostępowymi bazami danych

Innym dostępnym wzorcem jest przechowywanie wielu dzierżawców w bazie danych z wieloma dzierżawcami.  Wystąpienie aplikacji może mieć dowolną liczbę wielodostępnych baz danych.  Schemat bazy danych z wieloma dzierżawcami musi mieć jedną lub więcej kolumn identyfikatora dzierżawy, aby dane z dowolnej dzierżawy mogły być selektywnie pobierane.  Ponadto schemat może wymagać kilku tabel lub kolumn, które są używane tylko przez podzbiór dzierżawców.  Jednak kod statyczny i dane referencyjne są przechowywane tylko raz i są współużytkowane przez wszystkich dzierżawców.

#### <a name="tenant-isolation-is-sacrificed"></a>Izolacja dzierżawy jest poświęcana

*Dane:* &nbsp; wielodostępna baza danych musi poświęcać izolacji dzierżawy.  Dane wielu dzierżawców są przechowywane razem w jednej bazie danych.  Podczas tworzenia upewnij się, że kwerendy nigdy nie ujawniają danych z więcej niż jednej dzierżawy.  Baza danych SQL obsługuje [zabezpieczenia na poziomie wiersza,][docu-sql-svr-db-row-level-security-947w]które mogą wymuszać, że dane zwrócone z kwerendy mają zakres do pojedynczej dzierżawy.

*Przetwarzanie:* &nbsp; wielodostępna baza danych współużytkuje zasoby obliczeniowe i magazynowe we wszystkich dzierżawach.  Baza danych jako całość może być monitorowana, aby upewnić się, że działa akceptowalnie.  Jednak system platformy Azure nie ma wbudowanego sposobu monitorowania lub zarządzania wykorzystaniem tych zasobów przez indywidualną dzierżawę.  W związku z tym wielodostępnej bazy danych niesie ze sobą zwiększone ryzyko napotkania hałaśliwych sąsiadów, gdzie obciążenie jednego nadaktywnego dzierżawy wpływa na wydajność innych dzierżaw w tej samej bazie danych.  Dodatkowe monitorowanie na poziomie aplikacji może monitorować wydajność na poziomie dzierżawy.

#### <a name="lower-cost"></a>Niższy koszt

Ogólnie rzecz biorąc, wielodostępne bazy danych mają najniższy koszt na dzierżawę.  Koszty zasobów dla pojedynczej bazy danych są niższe niż dla puli elastycznej o równoważnym rozmiarze.  Ponadto w scenariuszach, w których dzierżawcy potrzebują tylko ograniczonego magazynu, potencjalnie miliony dzierżawców mogą być przechowywane w jednej bazie danych.  Żadna pula elastyczna nie może zawierać milionów baz danych.  Jednak rozwiązanie zawierające 1000 baz danych na pulę, z pulami 1000, może osiągnąć skalę milionów na ryzyko, że stanie się nieporęczne do zarządzania.

Dwie odmiany modelu bazy danych z wieloma dzierżawami są omówione w co następuje, z podzielonym modelem wielodostępnym jest najbardziej elastyczne i skalowalne.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplikacja dla wielu dzierżawców z pojedynczą bazą danych z wieloma dzierżawcami

Najprostszy wzorzec bazy danych z wieloma dzierżawami używa pojedynczej bazy danych do hostowania danych dla wszystkich dzierżawców.  W miarę dodawania większej liczby dzierżaw baz danych jest skalowana z większą liczą pamięcią masową i zasobami obliczeniowymi.  Ta skala w górę może być wszystko, co jest potrzebne, chociaż zawsze istnieje ostateczny limit skali.  Jednak na długo przed osiągnięciem tego limitu baza danych staje się niewygodna do zarządzania.

Operacje zarządzania, które koncentrują się na poszczególnych dzierżaw są bardziej złożone do zaimplementowania w bazie danych z wieloma dzierżawami.  I na dużą skalę operacje te mogą stać się niedopuszczalnie powolne.  Jednym z przykładów jest przywracanie danych w czasie dla tylko jednej dzierżawy.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplikacja dla wielu dzierżawców z podzielonymi na fragmenty bazami danych z wieloma dzierżawcami

Większość aplikacji SaaS uzyskać dostęp do danych tylko jednej dzierżawy naraz.  Ten wzorzec dostępu umożliwia dystrybucji danych dzierżawy w wielu bazach danych lub fragmentach, gdzie wszystkie dane dla jednej dzierżawy są zawarte w jednym niezależnuzyku.  W połączeniu ze wzorcem bazy danych z wieloma dzierżawcami model podzielony na fragmenty umożliwia niemal nieograniczoną skalę.

![Projektowanie aplikacji wielodostępnej z podzielonymi na fragmenty bazami danych z wieloma dzierżawami.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Zarządzanie fragmentami

Dzielenia na fragmenty zwiększa złożoność zarówno do projektowania i zarządzania operacyjnego.  Katalog jest wymagany do obsługi mapowania między dzierżawców i baz danych.  Ponadto procedury zarządzania są wymagane do zarządzania fragmentami i populacji dzierżawy.  Na przykład procedury muszą być zaprojektowane w celu dodawania i usuwania fragmentów oraz przenoszenia danych dzierżawy między fragmentami.  Jednym ze sposobów skalowania jest dodanie nowego fragmentu i zapełnienie go nowymi dzierżawcami.  W innych przypadkach można podzielić gęsto zaludniony fragment na dwa mniej gęsto zaludnione fragmenty.  Po kilku dzierżaw zostały przeniesione lub przerwane, można scalić słabo zaludnione fragmenty razem.  Scalanie spowodowałoby bardziej oszczędne wykorzystanie zasobów.  Dzierżawcy mogą również być przenoszone między fragmentami do równoważenia obciążeń.

Baza danych SQL udostępnia narzędzie podziału/scalania, które działa w połączeniu z biblioteką dzielenia na fragmenty i bazą danych katalogu.  Podana aplikacja może dzielić i scalać fragmenty i może przenosić dane dzierżawy między fragmentami.  Aplikacja przechowuje również katalog podczas tych operacji, oznaczanie dotkniętych dzierżaw jako w trybie offline przed przeniesieniem ich.  Po przeprowadzce aplikacja ponownie aktualizuje katalog za pomocą nowego mapowania i oznacza dzierżawę jako z powrotem w trybie online.

#### <a name="smaller-databases-more-easily-managed"></a>Mniejsze bazy danych łatwiej zarządzane

Poprzez dystrybucję dzierżaw w wielu bazach danych, podzielone rozwiązanie wielodostępne powoduje mniejsze bazy danych, które są łatwiej zarządzane.  Na przykład przywracanie określonej dzierżawy do poprzedniego punktu w czasie teraz obejmuje przywracanie pojedynczej mniejszej bazy danych z kopii zapasowej, a nie większej bazy danych, która zawiera wszystkie dzierżawy. Rozmiar bazy danych i liczba dzierżaw na bazę danych można wybrać, aby zrównoważyć obciążenie pracą i zadaniami zarządzania.

#### <a name="tenant-identifier-in-the-schema"></a>Identyfikator dzierżawy w schemacie

W zależności od zastosowanego podejścia dzielenia na fragmenty mogą zostać nałożone dodatkowe ograniczenia na schemat bazy danych.  Aplikacja podziału/scalania bazy danych SQL wymaga, aby schemat zawiera klucz dzielenia na fragmenty, który zazwyczaj jest identyfikatorem dzierżawy.  Identyfikator dzierżawy jest wiodącym elementem w kluczu podstawowym wszystkich tabel podzielonych na fragmenty.  Identyfikator dzierżawy umożliwia aplikacji podziału/scalania, aby szybko zlokalizować i przenieść dane skojarzone z określoną dzierżawą.

#### <a name="elastic-pool-for-shards"></a>Elastyczna pula odłamków

Podzielona wielodostępna baza danych może być umieszczona w pulach elastycznych.  Ogólnie rzecz biorąc posiadanie wielu baz danych z jedną dzierżawą w puli jest tak samo opłacalne, jak posiadanie wielu dzierżaw w kilku bazach danych z wieloma dzierżawcami.  Wielodostępne bazy danych są korzystne, gdy istnieje duża liczba stosunkowo nieaktywnych dzierżawców.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybrydowy model bazy danych z podzielonymi fragmentami wielodostępnym

W modelu hybrydowym wszystkie bazy danych mają identyfikator dzierżawy w ich schemacie.  Bazy danych są w stanie przechowywać więcej niż jeden dzierżawy i baz danych mogą być podzielona na fragmenty.  Tak więc w sensie schematu są one wszystkie wielodostępne bazy danych.  Jednak w praktyce niektóre z tych baz danych zawierają tylko jedną dzierżawę.  Niezależnie od tego ilość dzierżawców przechowywanych w danej bazie danych nie ma wpływu na schemat bazy danych.

#### <a name="move-tenants-around"></a>Przenoszenie najemców

W dowolnym momencie można przenieść określonego dzierżawcy do własnej bazy danych z wieloma dzierżawami.  W dowolnym momencie można zmienić zdanie i przenieść dzierżawcę z powrotem do bazy danych zawierającej wielu dzierżawców.  Można również przypisać dzierżawy do nowej bazy danych z jedną dzierżawą podczas inicjowania obsługi administracyjnej nowej bazy danych.

Model hybrydowy świeci, gdy istnieją duże różnice między potrzebami zasobów identyfikowalnych grup dzierżawców.  Załóżmy na przykład, że dzierżawcy uczestniczący w bezpłatnej wersji próbnej nie są gwarantowane ten sam wysoki poziom wydajności, które subskrybowania dzierżaw są.  Zasady mogą być dla dzierżawców w fazie bezpłatnej wersji próbnej, które mają być przechowywane w bazie danych z wieloma dzierżawami, która jest współużytkowana przez wszystkich dzierżaw wersji próbnej.  Gdy dzierżawa bezpłatna wersja próbna subskrybuje podstawową warstwę usług, dzierżawa może zostać przeniesiona do innej bazy danych z wieloma dzierżawami, która może mieć mniej dzierżawców.  Subskrybent, który płaci za warstwę usług premium może zostać przeniesiony do własnej nowej bazy danych z jedną dzierżawą.

#### <a name="pools"></a>Pule

W tym modelu hybrydowym bazy danych z jedną dzierżawą dla dzierżawców subskrybentów można umieścić w pulach zasobów w celu zmniejszenia kosztów bazy danych na dzierżawę.  Odbywa się to również w modelu bazy danych na dzierżawcę.

## <a name="i-tenancy-models-compared"></a>I. Porównywano modele najmu

W poniższej tabeli podsumowano różnice między głównymi modelami najmu.

| Miara | Aplikacja autonomiczna | Baza danych na dzierżawcę | Podzielony na fragmenty wielodostępny |
| :---------- | :------------- | :------------------ | :------------------- |
| Skalowanie | Medium<br />1-100s | Bardzo wysoka<br />1-100 000 | Unlimited (nieograniczony)<br />1-1 000 000 |
| Izolacja dzierżawy | Bardzo wysoka | Wysoka | Niski; z wyjątkiem dowolnego pojedynczego dzierżawy (który jest sam w db MT). |
| Koszt bazy danych na dzierżawcę | Wysoki; jest do wielkości dla szczytów. | Niski; używanych basenów. | Najniższy, dla małych najemców w MT DBs. |
| Monitorowanie wydajności i zarządzanie nimi | Tylko dla dzierżawcy | Agregacja + na dzierżawcę | Agregacja; chociaż jest na dzierżawę tylko dla singli. |
| Złożoność rozwoju | Małe | Małe | Średni; z powodu dzielenia na fragmenty. |
| Złożoność operacyjna | Niski-Wysoki. Indywidualnie proste, złożone na dużą skalę. | Niski-Średni. Wzorce adres złożoności na dużą skalę. | Niski-Wysoki. Indywidualne zarządzanie najemcą jest złożone. |
| &nbsp; ||||

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie i eksplorowanie aplikacji Wingtip z wieloma dzierżawami, która używa modelu SaaS bazy danych na dzierżawę — usługa Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Przykładowe bilety Wingtip SaaS Azure SQL Database][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Projektowanie autonomicznej aplikacji z dokładnie jedną bazą danych z jedną dzierżawą."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Projektowanie aplikacji wielodostępnej z bazą danych na dzierżawcę."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Projektowanie aplikacji wielodostępnej z bazą danych na dzierżawcę przy użyciu puli elastycznej."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Projektowanie aplikacji wielodostępnej z podzielonymi na fragmenty bazami danych z wieloma dzierżawami."

