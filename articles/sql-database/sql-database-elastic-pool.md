---
title: Zarządzanie wielu baz danych przy użyciu elastycznej puli platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie i skalowanie wielu baz danych SQL — setki i tysięcy - wykorzystujących pule elastyczne. Jedna cena dla zasobów, którą można dystrybuować, w razie potrzeby.
keywords: wiele baz danych, bazy danych zasobów, wydajność bazy danych
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.openlocfilehash: 5ef32b231a77906a6840ad3550e81b631ddc0c13
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309658"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastyczne pule pozwalają na zarządzanie i skalowania wielu baz danych Azure SQL

Pule elastyczne bazy danych SQL są proste i ekonomiczne rozwiązanie do zarządzania i skalowanie wielu baz danych, które mają wymagania użycia różnych i nieprzewidywalnych. Bazy danych w puli elastycznej znajdują się na jednym serwerze bazy danych SQL Azure i udostępnianie jest określona liczba zasobów w cenie zestawu. Pule elastyczne pule w usłudze Azure SQL Database umożliwiają deweloperom SaaS optymalizację stosunku ceny do wydajności dla grupy baz danych w ramach określonego budżetu, zapewniając jednocześnie elastyczność wydajności dla każdej bazy danych.

## <a name="what-are-sql-elastic-pools"></a>Co to są pule elastyczne SQL?

Deweloperzy SaaS tworzą aplikacje w oparciu o warstwy danych w dużej skali składające się z wielu baz danych. Typowym wzorcem aplikacji jest udostępnianie jednej bazy danych dla każdego klienta. Jednak różni klienci często mają różne i nieprzewidywalne wzorce użycia i trudno przewidzieć wymagania dotyczące zasobów dla każdego indywidualnego użytkownika bazy danych. Tradycyjnie masz dwie opcje:

- Nadmiernego udostępniania zasobów opartych na szczytowe użycie i za pośrednictwem płatności, lub
- Niepełną należy zapisać koszt kosztem wydajności i odbiorcy zadowolenia użytkowników podczas pików.

Pule elastyczne rozwiązać ten problem, zapewniając, że bazy danych uzyskać zasobów wydajności, które są im potrzebne, gdy są one wymagane. Udostępniają one prosty mechanizm alokacji zasobów w ramach przewidywalnego budżetu. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Pule elastyczne Włączanie deweloperowi zakupu zasobów dla puli współużytkowane przez wiele baz danych do uwzględnienia nieprzewidywalne okresów użycia przez pojedyncze bazy danych. Można skonfigurować zasoby puli albo na podstawie [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) lub [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md). Wymaganie zasobów w puli jest określany przez łączny wykorzystania jej baz danych. Liczba dostępnych do puli zasobów jest kontrolowana przez budżet developer. Deweloper po prostu dodaje baz danych do puli, ustawia zasobów minimalną i maksymalną dla baz danych (minimalna i maksymalna liczba jednostek Dtu lub minimalna lub maksymalna vCores w zależności od wybranych resourcing modelu), a następnie ustawia na podstawie puli zasobów ich budżetu. Korzystając z pul, deweloper może bezproblemowo rozwijać swoją usługę od niewielkiego startupu do dojrzałego biznesu w coraz większej skali.

Poszczególne bazy danych w ramach puli mają możliwość elastycznego skalowania automatycznego w określonym zakresie parametrów. Duże obciążenie bazy danych mogą zużywać więcej zasobów, aby spełnić wymagania. Bazy danych w ramach lekkie ładunki zużywają mniej i baz danych w ramach obciążenia korzystać z żadnych zasobów. Aprowizacja zasobów dla całej puli zamiast pojedynczych baz danych upraszcza zadania związane z zarządzaniem. Ponadto należy przewidywalną budżetu puli. Dodatkowe zasoby można dodać do istniejącej puli bez przestojów bazy danych, z wyjątkiem tego, że bazy danych może być konieczne do przeniesienia do zapewnienia zasoby obliczeniowe dodatkowe nowe zastrzeżenie eDTU. Podobnie jeśli dodatkowe zasoby nie są już potrzebne one można usunąć z istniejącej puli w dowolnym momencie w czasie. Można również dodawać bazy danych do puli lub odejmować je z niej. Jeśli baza danych przewidywalnie niewystarczająco wykorzystuje zasoby, należy ją przenieść.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kiedy należy rozważyć puli elastycznej bazy danych SQL

Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się niskim średnim wykorzystaniem oraz stosunkowo rzadkimi okresami zwiększonego użycia.

Im więcej baz danych możesz dodać do puli, tym większe uzyskujesz oszczędności. W zależności od wzorca użycia aplikacji możliwe jest uzyskanie oszczędności nawet już przy dwóch bazach danych S3.

Poniższe sekcje ułatwiają zrozumienie, jak ocenić, czy dodanie konkretnej kolekcji baz danych do puli może być korzystne. W przykładach użyto pul warstwy Standardowa, ale te same zasady mają zastosowanie także do pul w warstwach Podstawowa i Premium.

### <a name="assessing-database-utilization-patterns"></a>Ocena wzorców użycia bazy danych

Na poniższej ilustracji pokazano przykład bazy danych, która przez większość czasu pozostaje bezczynna, ale okresowo doświadcza dużego wzrostu aktywności. Jest to wzorzec użycia, który jest odpowiedni dla puli:

   ![pojedyncza baza danych odpowiednia dla puli](./media/sql-database-elastic-pool/one-database.png)

W przedstawionym okresie pięciu minut baza danych DB1 odnotowuje wzrost użycia do 90 DTU, ale jej całkowite średnie użycie jest mniejsze niż pięć DTU. Do obsługi tego obciążenia w jednej bazie danych wymagany jest poziom wydajności S3, ale spowoduje to, że większość zasobów pozostanie nieużywana w okresach niskiej aktywności.

Pula umożliwia udostępnienie tych nieużywanych jednostek DTU wielu bazom danych, a tym samym zmniejsza liczbę potrzebnych jednostek DTU i obniża koszty ogólne.

Rozwijając poprzedni przykład, załóżmy, że istnieją dodatkowe bazy danych o podobnych wzorcach użycia jak baza danych DB1. W polu dwa poniższe rysunki wykorzystania 20 i cztery bazy danych są warstwowego na tym samym wykresie w celu zilustrowania charakteru nienakładający ich użycia wraz z upływem czasu za pomocą model kupna jednostek dtu w warstwie:

   ![cztery bazy danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/four-databases.png)

   ![dwadzieścia baz danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/twenty-databases.png)

Zagregowane użycie jednostek DTU we wszystkich 20 bazach danych przedstawia czarna linia na poprzednim rysunku. Pokazuje ona, że zagregowane użycie jednostek DTU nigdy nie przekracza 100 DTU i wskazuje, że te 20 baz danych może współużytkować 100 jednostek eDTU w tym okresie. Skutkuje to dwudziestokrotnym zmniejszeniem liczby DTU i trzynastokrotnie niższą ceną w porównaniu do umieszczenia każdej z baz danych na poziomie wydajności S3 dla pojedynczej bazy danych.

Ten przykład jest idealny z następujących przyczyn:

* Istnieją duże różnice między użyciem szczytowym a średnim użyciem na bazę danych.
* Użycie szczytowe dla poszczególnych baz danych występuje w różnych punktach w czasie.
* Jednostki eDTU są współdzielone przez wiele baz danych.

Cena puli jest funkcją jednostek eDTU puli. Chociaż cena jednostki eDTU dla puli jest 1,5 raza większa niż cena jednostki DTU dla pojedynczej bazy danych, **jednostki eDTU puli mogą być współdzielone przez wiele baz danych, a tym samym potrzebna jest mniejsza całkowita liczba jednostek eDTU**. Te różnice w cenie i współużytkowanie jednostek eDTU są podstawą potencjalnych oszczędności, które mogą zapewnić pule.

Następujące reguły akceptacji powiązane z liczbą baz danych i użyciem baz danych pomagają upewnić się, że pula zapewnia mniejsze koszty w porównaniu z użyciem poziomów wydajności dla pojedynczych baz danych.

### <a name="minimum-number-of-databases"></a>Minimalna liczba baz danych

Jeśli łączna ilość zasobów dla pojedynczej bazy danych jest więcej niż 1,5 x zasobów niezbędnych do puli, pula elastyczna jest tańsze.

***Na podstawie jednostek dtu w warstwie zakupów przykład modelu***<br>
Aby pula 100 eDTU była bardziej opłacalna niż korzystanie z poziomów wydajności dla pojedynczych baz danych, potrzebne są co najmniej dwie baz danych S3 lub co najmniej 15 baz danych S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maksymalna liczba baz danych z równoczesnymi szczytami użycia

Dzięki udostępnieniu zasobów nie wszystkie bazy danych w puli można jednocześnie używać zasobów w granicach dostępne dla pojedynczej bazy danych. Mniej baz danych, która jednocześnie szczytu, tym niższy puli zasobów można ustawić i bardziej ekonomiczne rozwiązanie staje się puli. Ogólnie nie więcej niż 2/3 (lub 67%) w puli baz danych powinien jednocześnie szczytowa ich limit zasobów.

***Na podstawie jednostek dtu w warstwie zakupów przykład modelu***<br>
Aby zmniejszyć koszty dla trzech baz danych S3 w puli 200 eDTU, co najwyżej dwie z tych baz danych mogą jednocześnie osiągać szczytowe użycie. W przeciwnym razie, jeśli więcej niż dwie z tych czterech baz danych S3 jednocześnie osiągają szczytowe użycie, rozmiar puli musiałby zostać zwiększony do ponad 200 eDTU. Jeśli rozmiar puli będzie większy niż 200 eDTU, konieczne będzie dodanie większej liczby baz danych S3, aby koszty puli pozostały niższe niż w przypadku poziomów wydajności dla pojedynczych baz danych.

Należy zauważyć, że w tym przykładzie nie jest rozważane użycie innych baz danych w puli. Jeśli wszystkie bazy danych mają jakiś poziom użycia w dowolnym danym momencie czasu, wówczas mniej niż 2/3 (lub 67%) baz danych może jednocześnie osiągać szczytowe użycie.

### <a name="resource-utilization-per-database"></a>Użycie zasobów dla jednej bazy danych
Duża różnica między szczytowym i średnim użyciem bazy danych wskazuje na dłuższe okresy niewielkiego wykorzystania i krótkie okresy wysokiego wykorzystania. Ten wzorzec użycia jest idealny dla współużytkowania zasobów między bazami danych. Bazę danych należy rozważyć do umieszczenia w puli, jeśli jej szczytowe użycie jest ponad 1,5 raza większe niż jej średnie użycie.

***Na podstawie jednostek dtu w warstwie zakupów przykład modelu***<br>
Baza danych S3, która osiąga szczytowe użycie do 100 DTU, a przeciętnie używa 67 DTU lub mniej, jest dobrym kandydatem do współużytkowania jednostek eDTU w puli. Również baza danych S1, która osiąga szczytowe użycie 20 DTU, a przeciętnie używa 13 DTU lub mniej, jest dobrym kandydatem do umieszczenia w puli.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak wybrać rozmiar puli poprawna?

Najlepszy rozmiar puli zależy od łącznej zasobów niezbędnych do wszystkich baz danych w puli. Obejmuje to określenie następujących czynności:

* Maksymalna zasoby wykorzystywane przez wszystkie bazy danych w puli (maksymalna Dtu lub maksymalna vCores w zależności od wybranych resourcing modelu).
* Maksymalna liczba bajtów magazynu wykorzystana przez wszystkie bazy danych w puli.

W warstwach usług dostępne dla każdego modelu zasobu, zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) lub [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md).

Usługa SQL Database automatycznie ocenia historyczne użycie zasobów przez bazy danych na istniejącym serwerze usługi SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure Portal. Oprócz zaleceń, wbudowane środowisko szacuje użycie jednostek eDTU dla niestandardowej grupy baz danych na serwerze. Umożliwia to przeprowadzenie analizy warunkowej przez interaktywne dodawanie baz danych do puli i usuwanie ich w celu uzyskania analizy użycia zasobów i porady dotyczącej rozmiaru przed zatwierdzeniem zmian. Aby uzyskać instrukcje, zobacz [Monitorowanie puli elastycznej, zmienianie jej rozmiaru i zarządzanie nią](#monitor-an-elastic-pool-and-its-databases).

W przypadkach, gdy nie można użyć narzędzi, skorzystanie z następujących instrukcji krok po kroku może ułatwić oszacowanie, czy pula jest bardziej opłacalna niż pojedyncza baza danych:

1. Szacowanie Edtu lub vCores potrzebne dla puli w następujący sposób:

   Na podstawie jednostek dtu w warstwie model kupna: Maksymalna liczba (<*łączna liczba baz danych* X *średnie użycie jednostek DTU na bazę danych*>,<br>
   <*liczba baz danych jednocześnie osiągających szczyt użycia* X *użycie szczytowe jednostek DTU na bazę danych*)

   Na podstawie vCore model kupna (wersja zapoznawcza): Maksymalna liczba (<*łączna liczba baz danych* X *średnie wykorzystanie vCore na bazę danych*>,<br>
   <*Liczba jednocześnie peaking bazami danych* X *szczytowego wykorzystania vCore na bazę danych*)

2. Oszacuj miejsce do magazynowania wymagane dla puli przez dodanie liczby bajtów potrzebnych dla wszystkich baz danych w puli. Następnie określ rozmiar puli (w jednostkach eDTU), który zapewni tę ilość miejsca przechowywania.
3. Na podstawie jednostek dtu w warstwie model kupna potrwać dłuższy szacuje eDTU z kroku 1 i 2. Na podstawie vCore model kupna (wersja zapoznawcza) należy wykonać szacowania vCore z kroku 1.
4. Zobacz [bazy danych SQL cennikiem](https://azure.microsoft.com/pricing/details/sql-database/) i Znajdź rozmiaru najmniejszą puli, która jest większa niż szacowania w kroku 3.
5. Porównaj cenę puli z kroku 5 z ceną za używanie odpowiednich poziomów wydajności dla pojedynczych baz danych.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Używanie innych funkcji bazy danych SQL z pul elastycznych

### <a name="elastic-jobs-and-elastic-pools"></a>Zadania elastyczne i pul elastycznych

W przypadku puli zadania zarządzania są uproszczone dzięki uruchamianiu skryptów w **[zadaniach elastycznych](sql-database-elastic-jobs-overview.md)**. Zadanie elastyczne eliminuje większość monotonnych czynności związanych z dużą liczbą baz danych. Aby rozpocząć, zobacz [Wprowadzenie do zadań elastycznych](sql-database-elastic-jobs-getting-started.md).

Aby uzyskać więcej informacji na temat innych narzędzi do pracy z wieloma bazami danych, zobacz artykuł dotyczący [skalowania w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opcje ciągłości działania dla baz danych w puli elastycznej
Bazy danych w puli obsługują te same [funkcje ciągłości działalności biznesowej](sql-database-business-continuity.md), które są dostępne dla pojedynczych baz danych.

- **W momencie przywracania**: w momencie przywracania używa kopii zapasowych bazy danych automatyczne odzyskać bazę danych w puli do określonego punktu w czasie. Zobacz [Przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Geograficzne**: geograficzne zapewnia domyślna opcja odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie, w którym hostowana jest baza danych. Zobacz [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Przywracanie usługi Azure SQL Database lub przełączanie w trybie failover do pomocniczej bazy danych).

- **Aktywna replikacja geograficzna**: w przypadku aplikacji, które są bardziej agresywną wymagania odzyskiwania niż geograficzne mogą oferować, skonfigurować [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Tworzenie nowej puli elastycznej bazy danych SQL przy użyciu portalu Azure

Istnieją dwa sposoby, w portalu Azure można utworzyć puli elastycznej.
1. Można utworzyć puli elastycznej, wyszukując **puli elastycznej SQL** w **Marketplace** lub klikając **+ Dodaj** w puli elastycznej SQL Przeglądaj bloku. Jesteś w stanie umożliwia określenie nowego lub istniejącego serwera do obsługi przepływu pracy w tej puli.
2. Lub można utworzyć puli elastycznej, przechodząc do istniejącego serwera SQL i klikając **tworzenie puli** Aby utworzyć pulę bezpośrednio do tego serwera. Jedyną różnicą jest pominąć krok gdzie Określ serwer podczas obsługi przepływu pracy puli.

> [!NOTE]
> Można utworzyć wiele pul na serwerze, ale nie można dodać bazy danych z różnych serwerów do tej samej puli.

Warstwa usług puli Określa funkcje dostępne dla elastics w puli, a maksymalna ilość zasobów dostępnych dla każdej bazy danych. Aby uzyskać więcej informacji, zobacz limitów zasobów dla pul elastycznych w [modelu jednostek dtu w warstwie](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels). Dla zasobów na podstawie vCore limity pul elastycznych, zobacz [limity zasobów na podstawie vCore - pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Aby skonfigurować zasoby i cenowych w puli, kliknij przycisk **Konfigurowanie puli**. Następnie wybierz warstwy usług, dodać bazy danych do puli i skonfigurować limity zasobów dla puli i jej baz danych.

Po zakończeniu konfigurowania puli można kliknij przycisk "Zastosuj", nazwa puli i kliknij przycisk OK, aby utworzyć pulę.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorowanie puli elastycznej i jej baz danych

W portalu Azure można monitorować użycie puli elastycznej i baz danych w tej puli. Można również utworzyć zestaw zmian do puli elastycznej i Prześlij wszystkie zmiany w tym samym czasie. Te zmiany obejmują dodawanie lub usuwanie baz danych, zmiana ustawień puli elastycznej lub zmiany ustawień bazy danych.

Aby rozpocząć monitorowanie z puli elastycznej, Znajdź i otwórz elastycznej puli w portalu. Najpierw zostanie wyświetlony ekran, który zawiera przegląd stanu puli elastycznej. Obejmuje to:

* Monitorowanie wykresy przedstawiający użycie zasobów w puli elastycznej
* Ostatnie alerty i zalecenia, jeśli jest dostępna dla puli elastycznej

Na poniższym rysunku przedstawiono przykład puli elastycznej:

![Widok puli](./media/sql-database-elastic-pool-manage-portal/basic.png)

Jeśli chcesz uzyskać więcej informacji na temat puli możesz kliknąć na żadnym z dostępnych informacji w tym omówieniu. Kliknięcie **wykorzystania zasobów** wykresu spowoduje przejście do widoku monitorowania Azure gdzie można dostosować, metryki i przedziału czasowego wyświetlane na wykresie. Kliknięcie dowolnej dostępnych powiadomień spowoduje przejście do bloku, który zawiera szczegółowe informacje dotyczące tego alertu lub zalecenia.

Jeśli chcesz monitorować baz danych w puli użytkownika, możesz kliknąć **bazy danych wykorzystania zasobów** w **monitorowanie** części zasobów menu po lewej stronie.

![Strona wykorzystania zasobów bazy danych](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Aby dostosować wyświetlanie wykresu

Wykres i metryki strony, aby wyświetlić innych metryk, takich jak procent, procent we/wy danych i dziennika we/wy procent wykorzystania procesora CPU można edytować.

Na **Edytuj wykres** formularza, możesz wybrać stały czas należeć do zakresu, lub kliknij przycisk **niestandardowych** wybierz wszystkie okna 24-godzinnym, w ciągu ostatnich dwóch tygodni, a następnie wybierz zasoby do monitorowania.

### <a name="to-select-databases-to-monitor"></a>Aby wybrać baz danych do monitorowania

Domyślnie wykresu w **wykorzystania zasobów bazy danych** bloku wyświetli baz danych pierwsze 5 DTU lub procesora CPU (w zależności od warstwę usługi). Wybierając i unselecting baz danych z listy poniżej wykresu za pomocą pola wyboru po lewej stronie, można przełączać zapasową baz danych na tym wykresie.

Można również wybrać więcej metryk do widoku obok siebie w tej tabeli bazy danych, aby uzyskać bardziej szczegółowy widok wydajności bazy danych.

Aby uzyskać więcej informacji, zobacz [tworzyć alerty bazy danych SQL w portalu Azure](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby skalować pule elastyczne, zobacz [pule elastyczne skalowanie](sql-database-elastic-pool.md) i [skalowania puli elastycznej — przykładowy kod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
* Aby obejrzeć wideo, zobacz [Microsoft Virtual Academy kursu wideo na możliwości elastycznej bazy danych SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
* Samouczek SaaS wykorzystujących pule elastyczne, zobacz [wprowadzenie do aplikacji Wingtip SaaS](sql-database-wtp-overview.md).
