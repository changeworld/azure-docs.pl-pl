---
title: Zarządzanie wieloma bazami danych za pomocą pul elastycznych
description: Zarządzanie wieloma bazami danych SQL — setki i tysiące — przy użyciu pul elastycznych i skalowanie ich. Jedna cena za zasoby, które można dystrybuować w razie potrzeby.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 3c476393153f6bc1d18d5c163bcd69484583eb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256278"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastyczne pule pozwalają na zarządzanie wieloma bazami danych Azure SQL oraz ich skalowanie

Pule elastyczne usługi SQL Database to proste i niedrogie rozwiązanie do zarządzania i skalowania wielu baz danych, które mają różne i nieprzewidywalne wymagania dotyczące użycia. Bazy danych w elastycznej puli znajdują się na jednym serwerze usługi Azure SQL Database i współdzielą określoną liczbę zasobów za ustaloną cenę. Pule elastyczne pule w usłudze Azure SQL Database umożliwiają deweloperom SaaS optymalizację stosunku ceny do wydajności dla grupy baz danych w ramach określonego budżetu, zapewniając jednocześnie elastyczność wydajności dla każdej bazy danych.

## <a name="what-are-sql-elastic-pools"></a>Co to są pule elastyczne SQL

Deweloperzy SaaS tworzą aplikacje w oparciu o warstwy danych w dużej skali składające się z wielu baz danych. Typowym wzorcem aplikacji jest udostępnianie jednej bazy danych dla każdego klienta. Ale różni klienci często mają różne i nieprzewidywalne wzorce użycia i trudno jest przewidzieć wymagania dotyczące zasobów każdego użytkownika poszczególnych baz danych. Tradycyjnie, masz dwie opcje:

- Zasoby nadmiernej aprowizyjno-administracyjnej oparte na szczytowym zużyciu i nad płacą, lub
- Niedostatezasiążenie, aby zaoszczędzić koszty, kosztem wydajności i satysfakcji klienta podczas szczytów.

Pule elastyczne rozwiązać ten problem, zapewniając, że bazy danych uzyskać zasoby wydajności, których potrzebują, gdy ich potrzebują. Udostępniają one prosty mechanizm alokacji zasobów w ramach przewidywalnego budżetu. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> W przypadku pule elastycznych nie pobierana jest opłata za bazę danych. Opłaty są naliczane za każdą godzinę, gdy pula istnieje przy najwyższych eDTU lub punktach wirtualnych, niezależnie od użycia lub tego, czy pula była aktywna przez mniej niż godzinę.

Pule elastyczne umożliwiają deweloperowi zakup zasobów dla puli współużytkowanych przez wiele baz danych, aby pomieścić nieprzewidywalne okresy użycia przez poszczególne bazy danych. Zasoby dla puli można skonfigurować na podstawie [modelu zakupu opartego](sql-database-service-tiers-dtu.md) na UU lub [modelu zakupu opartego na parcie wirtualnym](sql-database-service-tiers-vcore.md). Zapotrzebowanie na zasoby dla puli zależy od zagregowanego wykorzystania jego baz danych. Ilość zasobów dostępnych dla puli jest kontrolowana przez budżet dewelopera. Deweloper po prostu dodaje bazy danych do puli, ustawia minimalne i maksymalne zasoby dla baz danych (minimalne i maksymalne DTU lub minimalne lub maksymalne pole wirtualne w zależności od wybranego modelu pozyskiwania zasobów), a następnie ustawia zasoby puli na podstawie ich Budżetu. Korzystając z pul, deweloper może bezproblemowo rozwijać swoją usługę od niewielkiego startupu do dojrzałego biznesu w coraz większej skali.

Poszczególne bazy danych w ramach puli mają możliwość elastycznego skalowania automatycznego w określonym zakresie parametrów. Pod dużym obciążeniem bazy danych może zużywać więcej zasobów, aby zaspokoić zapotrzebowanie. Bazy danych przy lekkich obciążeniach zużywają mniej, a bazy danych bez obciążenia nie zużywają żadnych zasobów. Aprowizacja zasobów dla całej puli zamiast pojedynczych baz danych upraszcza zadania związane z zarządzaniem. Dodatkowo masz przewidywalny budżet na pulę. Dodatkowe zasoby można dodać do istniejącej puli bez przestojów bazy danych, z tą różnicą, że bazy danych mogą wymagać przeniesienia w celu zapewnienia dodatkowych zasobów obliczeniowych dla nowej rezerwacji jednostek eDTU. Podobnie jeśli dodatkowe zasoby nie są już potrzebne, można je usunąć z istniejącej puli w dowolnym momencie. Można również dodawać bazy danych do puli lub odejmować je z niej. Jeśli baza danych przewidywalnie niewystarczająco wykorzystuje zasoby, należy ją przenieść.

> [!NOTE]
> Podczas przenoszenia baz danych do lub z puli elastycznej, nie ma przestojów, z wyjątkiem krótkiego okresu czasu (w kolejności sekund) na końcu operacji, gdy połączenia bazy danych są odrzucane.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kiedy należy wziąć pod uwagę pulę elastyczną bazy danych SQL

Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się niskim średnim wykorzystaniem oraz stosunkowo rzadkimi okresami zwiększonego użycia.

Im więcej baz danych możesz dodać do puli, tym większe uzyskujesz oszczędności. W zależności od wzorca wykorzystania aplikacji jest możliwe, aby zobaczyć oszczędności z zaledwie dwóch baz danych S3.

Poniższe sekcje ułatwiają zrozumienie, jak ocenić, czy dodanie konkretnej kolekcji baz danych do puli może być korzystne. W przykładach użyto pul warstwy Standardowa, ale te same zasady mają zastosowanie także do pul w warstwach Podstawowa i Premium.

### <a name="assessing-database-utilization-patterns"></a>Ocena wzorców użycia bazy danych

Na poniższej ilustracji pokazano przykład bazy danych, która przez większość czasu pozostaje bezczynna, ale okresowo doświadcza dużego wzrostu aktywności. Jest to wzorzec użycia, który jest odpowiedni dla puli:

   ![pojedyncza baza danych odpowiednia dla puli](./media/sql-database-elastic-pool/one-database.png)

W przedstawionym okresie pięciu minut baza danych DB1 odnotowuje wzrost użycia do 90 DTU, ale jej całkowite średnie użycie jest mniejsze niż pięć DTU. Rozmiar obliczeń S3 jest wymagany do uruchomienia tego obciążenia w jednej bazie danych, ale pozostawia to większość zasobów nieużywane w okresach niskiej aktywności.

Pula umożliwia udostępnienie tych nieużywanych jednostek DTU wielu bazom danych, a tym samym zmniejsza liczbę potrzebnych jednostek DTU i obniża koszty ogólne.

Rozwijając poprzedni przykład, załóżmy, że istnieją dodatkowe bazy danych o podobnych wzorcach użycia jak baza danych DB1. W następnych dwóch poniższych rysunkach wykorzystanie czterech baz danych i 20 baz danych jest warstwowe na tym samym wykresie, aby zilustrować nienakładający się charakter ich wykorzystania w czasie przy użyciu modelu zakupu opartego na UTU:

   ![cztery bazy danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/four-databases.png)

   ![dwadzieścia baz danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/twenty-databases.png)

Zagregowane użycie jednostek DTU we wszystkich 20 bazach danych przedstawia czarna linia na poprzednim rysunku. Pokazuje ona, że zagregowane użycie jednostek DTU nigdy nie przekracza 100 DTU i wskazuje, że te 20 baz danych może współużytkować 100 jednostek eDTU w tym okresie. Powoduje to 20-krotne zmniejszenie jednostek DTU i 13-krotną obniżkę ceny w porównaniu z umieszczeniem każdej z baz danych w rozmiarach obliczeniowych S3 dla pojedynczych baz danych.

Ten przykład jest idealny z następujących przyczyn:

- Istnieją duże różnice między użyciem szczytowym a średnim użyciem na bazę danych.
- Użycie szczytowe dla poszczególnych baz danych występuje w różnych punktach w czasie.
- Jednostki eDTU są współdzielone przez wiele baz danych.

Cena puli jest funkcją jednostek eDTU puli. Chociaż cena jednostki eDTU dla puli jest 1,5 raza większa niż cena jednostki DTU dla pojedynczej bazy danych, **jednostki eDTU puli mogą być współdzielone przez wiele baz danych, a tym samym potrzebna jest mniejsza całkowita liczba jednostek eDTU**. Te różnice w cenie i współużytkowanie jednostek eDTU są podstawą potencjalnych oszczędności, które mogą zapewnić pule.

Następujące reguły kciuka związane z liczbą baz danych i wykorzystania bazy danych pomóc zapewnić, że pula zapewnia zmniejszenie kosztów w porównaniu do przy użyciu rozmiarów obliczeń dla pojedynczych baz danych.

### <a name="minimum-number-of-databases"></a>Minimalna liczba baz danych

Jeśli łączna ilość zasobów dla pojedynczych baz danych jest więcej niż 1,5 x zasobów potrzebnych do puli, a następnie puli elastycznej jest bardziej opłacalne.

***Przykład modelu zakupów opartego na UTU***<br>
Co najmniej dwie bazy danych S3 lub co najmniej 15 baz danych S0 są potrzebne dla puli 100 jednostek eDTU, aby była bardziej opłacalna niż przy użyciu rozmiarów obliczeniowych dla pojedynczych baz danych.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maksymalna liczba baz danych z równoczesnymi szczytami użycia

Udostępniając zasoby, nie wszystkie bazy danych w puli można jednocześnie używać zasobów do limitu dostępnego dla pojedynczych baz danych. Im mniej baz danych, które jednocześnie szczyt, tym niższe zasoby puli można ustawić i bardziej opłacalne puli staje. Ogólnie rzecz biorąc, nie więcej niż 2/3 (lub 67%) baz danych w puli powinny jednocześnie osiągnąć najwyższy poziom do limitu zasobów.

***Przykład modelu zakupów opartego na UTU***

Aby zmniejszyć koszty dla trzech baz danych S3 w puli 200 eDTU, co najwyżej dwie z tych baz danych mogą jednocześnie osiągać szczytowe użycie. W przeciwnym razie, jeśli więcej niż dwie z tych czterech baz danych S3 jednocześnie osiągają szczytowe użycie, rozmiar puli musiałby zostać zwiększony do ponad 200 eDTU. Jeśli pula zostanie przesięknięta do ponad 200 jednostek eDTU, więcej baz danych S3 musiałoby zostać dodanych do puli, aby utrzymać koszty niższe niż rozmiary obliczeń dla pojedynczych baz danych.

W tym przykładzie nie uwzględnia wykorzystania innych baz danych w puli. Jeśli wszystkie bazy danych mają jakiś poziom użycia w dowolnym danym momencie czasu, wówczas mniej niż 2/3 (lub 67%) baz danych może jednocześnie osiągać szczytowe użycie.

### <a name="resource-utilization-per-database"></a>Wykorzystanie zasobów na bazę danych

Duża różnica między szczytowym i średnim użyciem bazy danych wskazuje na dłuższe okresy niewielkiego wykorzystania i krótkie okresy wysokiego wykorzystania. Ten wzorzec użycia jest idealny dla współużytkowania zasobów między bazami danych. Bazę danych należy rozważyć do umieszczenia w puli, jeśli jej szczytowe użycie jest ponad 1,5 raza większe niż jej średnie użycie.

**Przykład modelu zakupów oparty na jednostkach DTU:** Baza danych S3, która osiąga wartość szczytową do 100 DTU i średnio używa 67 procesorów DTU lub mniej, jest dobrym kandydatem do udostępniania eDTU w puli. Również baza danych S1, która osiąga szczytowe użycie 20 DTU, a przeciętnie używa 13 DTU lub mniej, jest dobrym kandydatem do umieszczenia w puli.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak wybrać odpowiedni rozmiar basenu

Najlepszy rozmiar puli zależy od zasobów agregujących potrzebnych dla wszystkich baz danych w puli. Wiąże się to z określeniem następujących elementów:

- Maksymalna ilość zasobów wykorzystywanych przez wszystkie bazy danych w puli (maksymalne DTU lub maksymalne wartości wirtualne w zależności od wybranego modelu pozyskiwania zasobów).
- Maksymalna liczba bajtów magazynu wykorzystana przez wszystkie bazy danych w puli.

Aby uzyskać dostępne warstwy usług dla każdego modelu zasobów, zobacz [model zakupu oparty na UTU](sql-database-service-tiers-dtu.md) lub model zakupów oparty na [parcie wirtualnym](sql-database-service-tiers-vcore.md).

W przypadkach, gdy nie można użyć narzędzi, skorzystanie z następujących instrukcji krok po kroku może ułatwić oszacowanie, czy pula jest bardziej opłacalna niż pojedyncza baza danych:

1. Oszacuj eDTU lub vCore potrzebne dla puli w następujący sposób:

   W przypadku modelu zakupu opartego na jednostka DTU: MAX(<*Całkowita liczba DBs* X średnie wykorzystanie DTU na> *DB,*<br>  
   <*liczba baz danych jednocześnie osiągających szczyt użycia* X *użycie szczytowe jednostek DTU na bazę danych*)

   W przypadku modelu zakupu opartego na procesorach vCore: MAX(<*Całkowita liczba* mb X średnie wykorzystanie vCore na> *DB,*<br>  
   <*Liczba jednocześnie szczytowych gnpp* X *Szczytowe wykorzystanie vCore na DB*)

2. Oszacuj miejsce do magazynowania wymagane dla puli przez dodanie liczby bajtów potrzebnych dla wszystkich baz danych w puli. Następnie określ rozmiar puli (w jednostkach eDTU), który zapewni tę ilość miejsca przechowywania.
3. W przypadku modelu zakupów opartego na UJ należy przyjąć większą wartość szacunków eDTU z kroku 1 i 2. W przypadku modelu zakupu opartego na wynikach vCore należy wziąć oszacowanie koru wirtualnego z kroku 1.
4. Zobacz [stronę cennika bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/) i znajdź najmniejszy rozmiar puli, który jest większy niż szacowany krok 3.
5. Porównaj cenę puli z kroku 5 z ceną przy użyciu odpowiednich rozmiarów obliczeń dla pojedynczych baz danych.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Korzystanie z innych funkcji bazy danych SQL z pulami elastycznymi

### <a name="elastic-jobs-and-elastic-pools"></a>Elastyczne zadania i elastyczne baseny

W przypadku puli zadania zarządzania są uproszczone dzięki uruchamianiu skryptów w **[zadaniach elastycznych](elastic-jobs-overview.md)**. Zadanie elastyczne eliminuje większość monotonnych czynności związanych z dużą liczbą baz danych.

Aby uzyskać więcej informacji na temat innych narzędzi do pracy z wieloma bazami danych, zobacz artykuł dotyczący [skalowania w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opcje ciągłości działania baz danych w puli elastycznej

Bazy danych w puli obsługują te same [funkcje ciągłości działalności biznesowej](sql-database-business-continuity.md), które są dostępne dla pojedynczych baz danych.

- **Przywracanie do określonego momentu**

  Przywracanie w czasie używa automatycznych kopii zapasowych bazy danych w celu odzyskania bazy danych w puli do określonego punktu w czasie. Zobacz [Przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Przywracanie geograficzne**

  Przywracanie geograficzne zapewnia domyślną opcję odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie, w którym znajduje się baza danych. Zobacz [Przywracanie bazy danych SQL platformy Azure lub pracy awaryjnej do pomocniczego](sql-database-disaster-recovery.md)

- **Aktywna replikacja geograficzna**

  W przypadku aplikacji, które mają bardziej agresywne wymagania dotyczące odzyskiwania niż przywracanie geograficzne, należy skonfigurować [active replikację geograficzną](sql-database-active-geo-replication.md) lub [grupę automatycznego trybu failover](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Tworzenie nowej puli elastycznej bazy danych SQL przy użyciu portalu Azure

Istnieją dwa sposoby tworzenia puli elastycznej w witrynie Azure portal.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby utworzyć pulę elastyczną. Wyszukaj i wybierz **pozycję Azure SQL**.
2. Wybierz **+Dodaj,** aby otworzyć stronę **opcji Wybierz wdrożenie SQL.** Dodatkowe informacje o pulach elastycznych można wyświetlić, wybierając pozycję **Pokaż szczegóły** na **kafelku Bazy danych.**
3. Na **kafelku Bazy danych** wybierz pozycję **Pula elastyczna** w menu rozwijanym **Typ zasobu,** a następnie wybierz pozycję **Utwórz**:

   ![Tworzenie puli elastycznej](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Lub można utworzyć pulę elastyczną, przechodząc do istniejącego serwera SQL platformy Azure i klikając **przycisk + Nowa pula,** aby utworzyć pulę bezpośrednio do tego serwera.

> [!NOTE]
> Można utworzyć wiele pul na serwerze, ale nie można dodawać baz danych z różnych serwerów do tej samej puli.

Warstwa usług puli określa funkcje dostępne dla gumek w puli i maksymalną ilość zasobów dostępnych dla każdej bazy danych. Aby uzyskać szczegółowe informacje, zobacz Limity zasobów dla pul elastycznych w [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Aby uzyskać limity zasobów oparte na pulach elastycznych, zobacz [limity zasobów oparte na pulach opartych na parciach wirtualnych — pule elastyczne](sql-database-vcore-resource-limits-elastic-pools.md).

Aby skonfigurować zasoby i ceny puli, kliknij pozycję **Konfiguruj pulę**. Następnie wybierz warstwę usług, dodaj bazy danych do puli i skonfiguruj limity zasobów dla puli i jej baz danych.

Po zakończeniu konfigurowania puli możesz kliknąć przycisk "Zastosuj", nazwać pulę i kliknąć przycisk "OK", aby utworzyć pulę.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorowanie puli elastycznej i jej baz danych

W witrynie Azure portal można monitorować wykorzystanie puli elastycznej i baz danych w tej puli. Można również wprowadzić zestaw zmian w puli elastycznej i przesłać wszystkie zmiany w tym samym czasie. Zmiany te obejmują dodawanie lub usuwanie baz danych, zmienianie ustawień puli elastycznej lub zmienianie ustawień bazy danych.

Aby rozpocząć monitorowanie puli elastycznej, znajdź i otwórz elastyczną pulę w portalu. Najpierw zobaczysz ekran, który zawiera przegląd stanu puli elastycznej. Obejmuje to:

- Monitorowanie wykresów przedstawiających użycie zasobów w puli elastycznej
- Najnowsze alerty i zalecenia dotyczące puli elastycznej, jeśli są dostępne

Na poniższej ilustracji przedstawiono przykładową pulę elastyczną:

![Widok z basenu](./media/sql-database-elastic-pool-manage-portal/basic.png)

Jeśli chcesz uzyskać więcej informacji na temat puli, możesz kliknąć dowolną z dostępnych informacji w tym przeglądzie. Kliknięcie wykresu **wykorzystania zasobów** spowoduje wyświetlenie widoku Monitorowania platformy Azure, w którym można dostosować metryki i przedział czasu wyświetlany na wykresie. Kliknięcie wszystkich dostępnych powiadomień spowoduje, że przejdziesz do bloku, w którego zostaną wyświetlone pełne szczegóły tego alertu lub zalecenia.

Jeśli chcesz monitorować bazy danych wewnątrz puli, możesz kliknąć na **Wykorzystanie zasobów bazy danych** w sekcji **Monitorowanie** menu zasobów po lewej stronie.

![Strona wykorzystania zasobów bazy danych](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Aby dostosować wyświetlanie wykresu

Można edytować wykres i stronę metryki, aby wyświetlić inne metryki, takie jak procent procesora CPU, procent we/wy danych i procent we/wy dziennika używane.

W formularzu **Edytuj wykres** można wybrać stały zakres czasu lub kliknąć **niestandardowe,** aby wybrać dowolne okno 24-godzinne w ciągu ostatnich dwóch tygodni, a następnie wybrać zasoby do monitorowania.

### <a name="to-select-databases-to-monitor"></a>Aby wybrać bazy danych do monitorowania

Domyślnie na wykresie w bloku **Wykorzystanie zasobów bazy danych** będzie wyświetlane 5 najlepszych baz danych przez jednostki DTU lub CPU (w zależności od warstwy usług). Możesz przełączyć bazy danych na tym wykresie, zaznaczając i odznaczając bazy danych z listy poniżej wykresu za pomocą pól wyboru po lewej stronie.

Można również wybrać więcej metryk do wyświetlania obok siebie w tej tabeli bazy danych, aby uzyskać pełniejszy widok wydajności baz danych.

Aby uzyskać więcej informacji, zobacz [tworzenie alertów bazy danych SQL w witrynie Azure portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Analizy przypadków klientów

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart używane pul elastycznych z usługi Azure SQL Database, aby szybko rozszerzyć swoje usługi biznesowe w tempie 1000 nowych baz danych SQL platformy Azure miesięcznie.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco używa pul elastycznych z usługą Azure SQL Database do szybkiego udostępniania i skalowania usług dla tysięcy dzierżawców w chmurze.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Program Daxko/CSI używa pul elastycznych z usługą Azure SQL Database w celu przyspieszenia cyklu programowania i zwiększenia obsługi klienta i wydajności.   

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o cenach, zobacz [Ceny puli elastycznej](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Aby skalować pule elastyczne, zobacz [Skalowanie basenów elastycznych](sql-database-elastic-pool-scale.md) i [Skaluj pulę elastyczną — przykładowy kod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Aby uzyskać film wideo, zobacz [kurs wideo z programu Microsoft Virtual Academy na temat elastycznych funkcji usługi Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
- Aby uzyskać samouczek SaaS przy użyciu basenów elastycznych, zobacz [Wprowadzenie do aplikacji SaaS Wingtip](sql-database-wtp-overview.md).
- Aby dowiedzieć się więcej o zarządzaniu zasobami w pulach elastycznych z wieloma bazami danych, zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](sql-database-elastic-pool-resource-management.md).
