---
title: Zarządzanie wielu baz danych SQL za pomocą elastycznych pul platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie i skalowania wielu baz danych SQL — setki oraz tysięcy - wykorzystujących pule elastyczne. Jedna cena zasoby, które można rozpowszechniać w razie potrzeby.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 02/28/2019
ms.openlocfilehash: c1db16475224cc3c91a5353ead0aabd091098e14
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240359"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Pul elastycznych pozwalają na zarządzanie i skalowania wielu baz danych Azure SQL

Pule elastyczne usługi SQL Database to proste i niedrogie rozwiązanie do zarządzania i skalowania wielu baz danych, które mają różne i nieprzewidywalne wymagania dotyczące użycia. Bazy danych w puli elastycznej znajdują się na jednym serwerze usługi Azure SQL Database i udostępniaj wiele zestaw zasobów w cenie zestawu. Pule elastyczne pule w usłudze Azure SQL Database umożliwiają deweloperom SaaS optymalizację stosunku ceny do wydajności dla grupy baz danych w ramach określonego budżetu, zapewniając jednocześnie elastyczność wydajności dla każdej bazy danych.

## <a name="what-are-sql-elastic-pools"></a>Co to są pule elastyczne SQL

Deweloperzy SaaS tworzą aplikacje w oparciu o warstwy danych w dużej skali składające się z wielu baz danych. Typowym wzorcem aplikacji jest udostępnianie jednej bazy danych dla każdego klienta. Jednak różni klienci często mają różne i nieprzewidywalne wzorce użycia i trudno przewidzieć wymagania dotyczące zasobów dla każdego indywidualnego użytkownika bazy danych. Tradycyjnie dostępne były dwie opcje:

- Nadmiernej aprowizacji zasobów na podstawie szczytowego użycia i za pośrednictwem płatności, lub
- Niedostatecznym poziomie w celu obniżenia opłat, kosztem wydajności i zadowolenia klientów w okresach szczytowego.

Pule elastyczne rozwiązują ten problem przez zapewnienie, że bazy danych uzyskują zasobów wydajności, które są im potrzebne, kiedy ich potrzebują. Udostępniają one prosty mechanizm alokacji zasobów w ramach przewidywalnego budżetu. Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Brak opłat za bazę danych dla pul elastycznych. Opłaty są naliczane za każdą godzinę istnienia puli o najwyższej liczby jednostek eDTU lub rdzeni wirtualnych, niezależnie od użycia lub tego, czy pula był aktywny przez mniej niż godzinę.

Elastyczne pule umożliwiają deweloperowi zakupienie zasobów dla puli współdzielonej przez wiele baz danych, aby obsługiwać nieprzewidywalne okresy użycia pojedynczych bazy danych. Można skonfigurować zasoby puli albo na podstawie [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md). Zapotrzebowanie na zasoby dla puli jest określana na podstawie zagregowanego użycia jej baz danych. Ilość zasobów dostępnych dla puli jest kontrolowana przez budżet dewelopera. Deweloper po prostu dodaje bazy danych do puli, ustawia minimum i maksimum zasobów dla baz danych (minimalnej i maksymalnej liczby jednostek Dtu lub minimalna lub maksymalna rdzeni wirtualnych, w zależności od wybranego personalne modelu), a następnie ustawia zasobów puli na podstawie ich Budżet. Korzystając z pul, deweloper może bezproblemowo rozwijać swoją usługę od niewielkiego startupu do dojrzałego biznesu w coraz większej skali.

Poszczególne bazy danych w ramach puli mają możliwość elastycznego skalowania automatycznego w określonym zakresie parametrów. Pod dużym obciążeniem bazy danych może zużywać więcej zasobów do spełnienia określonych wymagań. Bazy danych pod niskim obciążeniem wykorzystują mniej, a bazy danych bez obciążenia korzysta z żadnych zasobów. Aprowizacja zasobów dla całej puli zamiast pojedynczych baz danych upraszcza zadania związane z zarządzaniem. Ponadto możesz pule mają przewidywalny budżet dla puli. Dodatkowe zasoby można dodać do istniejącej puli bez przestoju bazy danych, z tą różnicą, że bazy danych może być konieczne do przeniesienia w celu zapewnienia dodatkowych zasobów obliczeniowych dla nowej rezerwacji jednostek eDTU. Podobnie jeśli nie są już potrzebne są dodatkowe zasoby są można je usunąć z istniejącej puli w dowolnym momencie w czasie. Można również dodawać bazy danych do puli lub odejmować je z niej. Jeśli baza danych przewidywalnie niewystarczająco wykorzystuje zasoby, należy ją przenieść.

> [!NOTE]
> Podczas przenoszenia baz danych do lub z puli elastycznej, nie jest bez przestojów, z wyjątkiem krótki czas (rzędu kilku sekund) po zakończeniu operacji po upuszczeniu połączenia z bazą danych.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kiedy należy rozważyć pulę elastyczną SQL Database

Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się niskim średnim wykorzystaniem oraz stosunkowo rzadkimi okresami zwiększonego użycia.

Im więcej baz danych możesz dodać do puli, tym większe uzyskujesz oszczędności. W zależności od wzorca użycia aplikacji możliwe jest uzyskanie oszczędności nawet już przy dwóch bazach danych S3.

Poniższe sekcje ułatwiają zrozumienie, jak ocenić, czy dodanie konkretnej kolekcji baz danych do puli może być korzystne. W przykładach użyto pul warstwy Standardowa, ale te same zasady mają zastosowanie także do pul w warstwach Podstawowa i Premium.

### <a name="assessing-database-utilization-patterns"></a>Ocena wzorców użycia bazy danych

Na poniższej ilustracji pokazano przykład bazy danych, która przez większość czasu pozostaje bezczynna, ale okresowo doświadcza dużego wzrostu aktywności. Jest to wzorzec użycia, który jest odpowiedni dla puli:

   ![pojedyncza baza danych odpowiednia dla puli](./media/sql-database-elastic-pool/one-database.png)

W przedstawionym okresie pięciu minut baza danych DB1 odnotowuje wzrost użycia do 90 DTU, ale jej całkowite średnie użycie jest mniejsze niż pięć DTU. Rozmiar obliczeń S3 jest wymagany do obsługi tego obciążenia w jednej bazie danych, ale spowoduje to, że większość zasobów nieużywane w okresach niskiej aktywności.

Pula umożliwia udostępnienie tych nieużywanych jednostek DTU wielu bazom danych, a tym samym zmniejsza liczbę potrzebnych jednostek DTU i obniża koszty ogólne.

Rozwijając poprzedni przykład, załóżmy, że istnieją dodatkowe bazy danych o podobnych wzorcach użycia jak baza danych DB1. W następnych dwóch ilustracjach poniżej użycie czterech i 20 baz danych przedstawiono nałożone na tym samym wykresie, aby zilustrować nienakładający charakter ich użycia wraz z upływem czasu przy użyciu modelu zakupu opartego na jednostkach DTU:

   ![cztery bazy danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/four-databases.png)

   ![dwadzieścia baz danych z wzorcem użycia odpowiednim dla puli](./media/sql-database-elastic-pool/twenty-databases.png)

Zagregowane użycie jednostek DTU we wszystkich 20 bazach danych przedstawia czarna linia na poprzednim rysunku. Pokazuje ona, że zagregowane użycie jednostek DTU nigdy nie przekracza 100 DTU i wskazuje, że te 20 baz danych może współużytkować 100 jednostek eDTU w tym okresie. Skutkuje to 20-krotne zmniejszenie liczby jednostek Dtu i 13 x obniżki cen w porównaniu do umieszczenia każdej z baz danych w trybie S3 obliczenia rozmiarów dla pojedynczych baz danych.

Ten przykład jest idealny z następujących przyczyn:

- Istnieją duże różnice między użyciem szczytowym a średnim użyciem na bazę danych.
- Użycie szczytowe dla poszczególnych baz danych występuje w różnych punktach w czasie.
- Jednostki eDTU są współdzielone przez wiele baz danych.

Cena puli jest funkcją jednostek eDTU puli. Chociaż cena jednostki eDTU dla puli jest 1,5 raza większa niż cena jednostki DTU dla pojedynczej bazy danych, **jednostki eDTU puli mogą być współdzielone przez wiele baz danych, a tym samym potrzebna jest mniejsza całkowita liczba jednostek eDTU**. Te różnice w cenie i współużytkowanie jednostek eDTU są podstawą potencjalnych oszczędności, które mogą zapewnić pule.

Następujące reguły akceptacji powiązane z liczbą baz danych i użyciem baz danych pomóc upewnić się, że pula zapewnia mniejsze koszty w porównaniu z użyciem rozmiarów wystąpień obliczeniowych dla pojedynczych baz danych.

### <a name="minimum-number-of-databases"></a>Minimalna liczba baz danych

Jeśli łączna ilość zasobów dla pojedynczych baz danych jest większa niż 1,5 x zasoby wymagane dla puli, pula elastyczna jest bardziej opłacalna.

***Oparte na jednostkach DTU przykładowy model zakupu***<br>
Aby pula 100 eDTU była bardziej opłacalna niż korzystanie z rozmiarów wystąpień obliczeniowych dla pojedynczych baz danych potrzebne co najmniej dwie baz danych S3 lub co najmniej 15 baz danych S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maksymalna liczba baz danych z równoczesnymi szczytami użycia

Dzięki udostępnieniu zasobów, nie wszystkie bazy danych w puli mogą jednocześnie używać zasobów do limitu dostępne dla pojedynczych baz danych. Mniejsza liczba baz danych, które jednocześnie odnotowują szczytowe użycie, tym niższy można ustawić zasobów w puli i tym bardziej opłacalne staje się puli. Ogólne, nie więcej niż 2/3 (lub 67%) baz danych w puli powinno jednocześnie doświadczać ich limitu zasobów.

***Oparte na jednostkach DTU przykładowy model zakupu***

Aby zmniejszyć koszty dla trzech baz danych S3 w puli 200 eDTU, co najwyżej dwie z tych baz danych mogą jednocześnie osiągać szczytowe użycie. W przeciwnym razie, jeśli więcej niż dwie z tych czterech baz danych S3 jednocześnie osiągają szczytowe użycie, rozmiar puli musiałby zostać zwiększony do ponad 200 eDTU. Jeśli rozmiar puli będzie większy niż 200 Edtu, większej liczby baz danych S3 musi zostać dodane do aby koszty puli pozostały niższe niż obliczenia rozmiarów dla pojedynczych baz danych.

Należy zauważyć, że w tym przykładzie nie jest rozważane użycie innych baz danych w puli. Jeśli wszystkie bazy danych mają jakiś poziom użycia w dowolnym danym momencie czasu, wówczas mniej niż 2/3 (lub 67%) baz danych może jednocześnie osiągać szczytowe użycie.

### <a name="resource-utilization-per-database"></a>Wykorzystanie zasobów na bazę danych

Duża różnica między szczytowym i średnim użyciem bazy danych wskazuje na dłuższe okresy niewielkiego wykorzystania i krótkie okresy wysokiego wykorzystania. Ten wzorzec użycia jest idealny dla współużytkowania zasobów między bazami danych. Bazę danych należy rozważyć do umieszczenia w puli, jeśli jej szczytowe użycie jest ponad 1,5 raza większe niż jej średnie użycie.

**Oparte na jednostkach DTU przykładowy model zakupu**: Baza danych S3, która osiąga szczytowe użycie do 100 DTU, a przeciętnie używa 67 DTU lub mniej, jest dobrym kandydatem do współużytkowania jednostek eDTU w puli. Również baza danych S1, która osiąga szczytowe użycie 20 DTU, a przeciętnie używa 13 DTU lub mniej, jest dobrym kandydatem do umieszczenia w puli.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak wybrać rozmiar puli poprawne

Najlepszy rozmiar puli zależy od łącznej zasoby wymagane dla wszystkich baz danych w puli. Obejmuje to określenie następujących czynności:

- Maksymalna zasoby wykorzystana przez wszystkie bazy danych w puli (maksymalna liczba jednostek Dtu lub maksymalna rdzeni wirtualnych, w zależności od wybranego personalne modelu).
- Maksymalna liczba bajtów magazynu wykorzystana przez wszystkie bazy danych w puli.

Dla warstwy usług dostępnych dla każdego modelu zasobów, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) lub [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

W przypadkach, gdy nie można użyć narzędzi, skorzystanie z następujących instrukcji krok po kroku może ułatwić oszacowanie, czy pula jest bardziej opłacalna niż pojedyncza baza danych:

1. Oszacuj liczbę jednostek Edtu lub rdzeni wirtualnych potrzebnych dla puli w następujący sposób:

   Dla modelu zakupu opartego na jednostkach DTU: MAX(<*łączna liczba baz danych* X *średnie użycie jednostek DTU na bazę danych*>,<br>  
   <*liczba baz danych jednocześnie osiągających szczyt użycia* X *użycie szczytowe jednostek DTU na bazę danych*)

   Dla modelu zakupu opartego na rdzeniach wirtualnych: MAX (<*łączna liczba baz danych* X *średnie użycie pamięci rdzeń wirtualny na bazę danych*>,<br>  
   <*Liczba jednocześnie osiągających Szczyt użycia baz danych* X *użycie pamięci rdzeń wirtualny szczytowe na bazę danych*)

2. Oszacuj miejsce do magazynowania wymagane dla puli przez dodanie liczby bajtów potrzebnych dla wszystkich baz danych w puli. Następnie określ rozmiar puli (w jednostkach eDTU), który zapewni tę ilość miejsca przechowywania.
3. W przypadku opartego na jednostkach DTU modelu zakupu weź większą z wartości szacunkowych eDTU, z kroku 1 i 2. Dla opartych na rdzeniach wirtualnych model zakupu należy wykonać szacowania — rdzeń wirtualny z kroku 1.
4. Zobacz [SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/) i Znajdź najmniejszy puli rozmiar jest większy niż szacunkowa wartość z kroku 3.
5. Porównaj cenę puli z kroku 5 z ceną za używanie rozmiarów obliczeniowych odpowiednie dla pojedynczych baz danych.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Przy użyciu innych funkcji usługi SQL Database z pulami elastycznymi

### <a name="elastic-jobs-and-elastic-pools"></a>Zadań elastycznych i elastycznych pul

W przypadku puli zadania zarządzania są uproszczone dzięki uruchamianiu skryptów w **[zadaniach elastycznych](elastic-jobs-overview.md)** . Zadanie elastyczne eliminuje większość monotonnych czynności związanych z dużą liczbą baz danych.

Aby uzyskać więcej informacji na temat innych narzędzi do pracy z wieloma bazami danych, zobacz artykuł dotyczący [skalowania w poziomie za pomocą usługi Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opcje ciągłości działania dla baz danych w puli elastycznej

Bazy danych w puli obsługują te same [funkcje ciągłości działalności biznesowej](sql-database-business-continuity.md), które są dostępne dla pojedynczych baz danych.

- **W momencie przywracania**

  Przywracanie do punktu w czasie używa automatycznych kopiach zapasowych, aby odzyskać bazę danych w puli z określonego punktu w czasie. Zobacz [Przywracanie do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Funkcja przywracania geograficznego**

  Przywracanie geograficzne zapewnia domyślną opcję odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie, w którym hostowana jest baza danych. Zobacz [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Przywracanie usługi Azure SQL Database lub przełączanie w trybie failover do pomocniczej bazy danych).

- **Aktywna replikacja geograficzna**

  W przypadku aplikacji, które mają bardziej agresywnego odzyskiwania niż oferowane przez funkcję przywracania geograficznego, skonfigurować [aktywnej replikacji geograficznej](sql-database-active-geo-replication.md) lub [automatyczny tryb failover grupy](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Tworzenie nowej puli elastycznej bazy danych SQL przy użyciu witryny Azure portal

Istnieją dwa sposoby, można utworzyć pulę elastyczną w witrynie Azure portal.

1. Można utworzyć puli elastycznej, wyszukując **elastycznej puli SQL** w **Marketplace** lub klikając **+ Dodaj** pule elastyczne SQL bloku przeglądania. Jesteś w stanie określić nowy lub istniejący serwer za pomocą tego przepływu pracy aprowizacji puli.
2. Lub można utworzyć puli elastycznej, przechodząc do istniejącego serwera SQL, a następnie klikając polecenie **tworzenie puli** Aby utworzyć pulę bezpośrednio do tego serwera. Jedyną różnicą jest pominąć krok gdzie określić serwera podczas przepływu pracy aprowizacji puli.

> [!NOTE]
> Można utworzyć wiele pul na serwerze, ale nie można dodać bazy danych z różnych serwerów do tej samej puli.

Warstwa usług puli Określa funkcje dostępne dla elastycznych w puli, a maksymalna ilość zasobów dostępnych dla każdej bazy danych. Aby uzyskać szczegółowe informacje, zobacz limity zasobów dla elastycznych pul w [modelu jednostek DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Zasób oparty na rdzeniach wirtualnych limity dla pul elastycznych, zobacz [limity zasobów opartych na rdzeniach wirtualnych - pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).

Aby skonfigurować zasoby i ceny puli, kliknij przycisk **skonfigurować pulę**. Następnie wybierz warstwę usługi, dodać bazy danych do puli i skonfigurować limity zasobów dla puli i jego bazami danych.

Po zakończeniu konfigurowania puli można kliknij przycisk "Zastosuj", nazwa puli i kliknij przycisk OK, aby utworzyć pulę.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorowanie puli elastycznej i jego baz danych

W witrynie Azure portal można monitorować wykorzystanie puli elastycznej i baz danych w tej puli. Można również wprowadzić serię zmian do elastycznej puli i Prześlij wszystkie zmiany w tym samym czasie. Te zmiany obejmują dodawanie lub usuwanie baz danych, zmieniając ustawienia w puli elastycznej i zmienianie ustawienia bazy danych.

Aby rozpocząć monitorowanie puli elastycznej, Znajdź i otwórz elastycznej puli w portalu. Najpierw zostanie wyświetlony ekran, który zawiera przegląd stanu puli elastycznej. Obejmuje to:

- Wykresy monitorowania przedstawiający wykorzystanie zasobów puli elastycznej
- Ostatnie alerty i zalecenia, jeśli są dostępne dla puli elastycznej

Na poniższym rysunku przedstawiono przykład elastycznej puli:

![Widok puli](./media/sql-database-elastic-pool-manage-portal/basic.png)

Jeśli chcesz, aby dowiedzieć się więcej o puli kliknięcie na żadnym z dostępnych informacji w tym omówieniu. Kliknięcie **wykorzystanie zasobów** wykresu spowoduje przejście do widoku monitorowania platformy Azure możesz zmodyfikować metryki i przedziału czasowego wyświetlane na wykresie. Kliknięcie dowolnego dostępnych powiadomień spowoduje przejście do bloku, pokazujący pełne szczegóły tego alertu lub zalecenia.

Jeśli chcesz monitorować bazy danych w puli, możesz kliknąć **bazy danych wykorzystania zasobów** w **monitorowanie** części zasobu menu po lewej stronie.

![Strona wykorzystanie zasobów bazy danych](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Aby dostosować wyświetlanie wykresu

Możesz edytować wykres i metryk strony, aby wyświetlić inne metryki, takie jak procent procesora CPU, procent użycia operacji We/Wy danych i operacji We/Wy dziennika w ujęciu procentowym.

Na **Edytuj wykres** formularza, możesz wybrać stały czas zakresu lub kliknij przycisk **niestandardowe** można wybrać dowolnego 24-godzinnego przedziału w ciągu ostatnich dwóch tygodni, a następnie wybierz zasoby do monitorowania.

### <a name="to-select-databases-to-monitor"></a>Aby wybrać baz danych do monitorowania

Domyślnie wykresu w **wykorzystanie zasobów bazy danych** bloku pokaże baz danych pierwsze 5 jednostek DTU lub procesora CPU (w zależności od swojej warstwy usług). Możesz przełączać zapasowych baz danych na tym wykresie, wybierając i usunięcie zaznaczenia baz danych z listy poniżej wykresu za pomocą pola wyboru po lewej stronie.

Możesz również wybrać więcej metryk, aby wyświetlić obok siebie w tej tabeli bazy danych, aby uzyskać bardziej szczegółowy widok wydajności bazy danych.

Aby uzyskać więcej informacji, zobacz [tworzyć alerty bazy danych SQL w witrynie Azure portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Analizy przypadków klientów

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  Firma SnelStart umożliwia pul elastycznych za pomocą usługi Azure SQL Database szybko rozszerzyć swoje usługi biznesowe w wysokości 1000 nowe usługi Azure SQL bazy danych na miesiąc.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco używa pul elastycznych za pomocą usługi Azure SQL Database do szybkiego aprowizację i skalowanie usług dla tysięcy dzierżaw w chmurze.

- [Daxko/CSI](https://customers.microsoft.com/story/csi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services)

  Daxko/CSI używa pul elastycznych za pomocą usługi Azure SQL Database, przyspieszyć cykl tworzenia i zwiększyć jej działu pomocy technicznej i wydajność.

## <a name="next-steps"></a>Kolejne kroki

- Aby skalować pul elastycznych, zobacz [skalowanie pul elastycznych](sql-database-elastic-pool.md) i [skalowanie elastycznej puli — przykładowy kod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Aby obejrzeć wideo, zobacz [Microsoft Virtual Academy kurs wideo w serwisie elastycznym możliwościom usługi Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
- Samouczek SaaS wykorzystujących pule elastyczne, zobacz [wprowadzenie do aplikacji SaaS o nazwie Wingtip](sql-database-wtp-overview.md).
