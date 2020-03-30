---
title: Szczegółowe informacje o wydajności zapytań
description: Monitorowanie wydajności zapytań identyfikuje najbardziej zużywające procesor CPU i długotrwałe zapytania dla pojedynczych i puli baz danych w bazie danych SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214057"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Usługa Query Performance Insight dla bazy danych SQL usługi Azure

Usługa Query Performance Insight zapewnia inteligentną analizę zapytań dla pojedynczych i pulowanych baz danych. Pomaga zidentyfikować najważniejsze zasoby zużywające i długotrwałe zapytania w obciążeniu. Pomaga to znaleźć zapytania do optymalizacji w celu zwiększenia ogólnej wydajności obciążenia i efektywnego korzystania z zasobów, które płacisz. Query Performance Insight pomaga spędzać mniej czasu na rozwiązywaniu problemów z wydajnością bazy danych, zapewniając:

* Lepszy wgląd w zużycie zasobów baz danych (DTU)
* Szczegółowe informacje na temat najlepszych zapytań do bazy danych według procesora CPU, czasu trwania i liczby wykonania (potencjalnych kandydatów do strojenia dla poprawy wydajności)
* Możliwość przechodzenia do szczegółów kwerendy w celu wyświetlenia tekstu kwerendy i historii wykorzystania zasobów
* Adnotacje, które pokazują zalecenia dotyczące wydajności od [doradców bazy danych](sql-database-advisor.md)

![Szczegółowe informacje o wydajności zapytań](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Wymagania wstępne

Query Performance Insight wymaga, aby [Magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx) był aktywny w bazie danych. Jest domyślnie włączana automatycznie dla wszystkich baz danych SQL platformy Azure. Jeśli Query Store nie jest uruchomiony, portal Azure wyświetli monit o jego włączenie.

> [!NOTE]
> Jeśli w portalu pojawi się komunikat "Magazyn zapytań nie jest poprawnie skonfigurowany w tej bazie danych", zobacz [Optymalizacja konfiguracji Magazynu zapytań](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Uprawnienia

Do korzystania z usługi Query Performance Insight potrzebne są następujące uprawnienia [kontroli dostępu oparte na rolach:](../role-based-access-control/overview.md)

* **Uprawnienia czytnika**, **właściciela,** **współautora,** **współautora bazy danych SQL**lub **sql server contributor** są wymagane do wyświetlania najlepszych zapytań i wykresów zużywających zasoby.
* **Do**wyświetlania tekstu kwerendy wymagane są uprawnienia właściciela , **współautora** **bazy danych SQL**lub sql server **contributor.**

## <a name="use-query-performance-insight"></a>Korzystanie ze szczegółowych informacji o wydajności zapytań

Query Performance Insight jest łatwy w użyciu:

1. Otwórz [witrynę Azure portal](https://portal.azure.com/) i znajdź bazę danych, którą chcesz sprawdzić.
2. Z menu po lewej stronie otwórz inteligentny wgląd**w wydajność zapytania** **wydajności.** > 
  
   ![Wgląd w wydajność kwerendy w menu](./media/sql-database-query-performance/tile.png)

3. Na pierwszej karcie przejrzyj listę najlepszych zapytań zużywających zasoby.
4. Wybierz pojedynczą kwerendę, aby wyświetlić jej szczegóły.
5. Otwórz**zalecenia dotyczące inteligentnej** **wydajności** > i sprawdź, czy są dostępne zalecenia dotyczące wydajności. Aby uzyskać więcej informacji na temat wbudowanych zaleceń dotyczących wydajności, zobacz [Doradca bazy danych SQL](sql-database-advisor.md).
6. Użyj suwaków lub ikon powiększenia, aby zmienić obserwowany interwał.

   ![Panel wydajności](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Aby baza danych SQL renderować informacje w ucho. Jeśli baza danych nie ma aktywności lub magazyn zapytań nie był aktywny w określonym okresie, wykresy będą puste, gdy query Performance Insight wyświetla ten zakres czasu. Magazyn zapytań można włączyć w dowolnym momencie, jeśli nie jest uruchomiony. Aby uzyskać więcej informacji, zobacz [Najważniejsze wskazówki dotyczące magazynu zapytań](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Aby uzyskać zalecenia dotyczące wydajności bazy danych, wybierz [zalecenia w](sql-database-advisor.md) bloku nawigacyjnym usługi Query Performance Insight.

![Karta Zalecenia](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Przejrzyj najlepsze zapytania zużywające procesor

Domyślnie query Performance Insight pokazuje pięć pierwszych zapytań korzystających z procesora CPU przy pierwszym otwarciu.

1. Zaznacz lub wyczyść poszczególne kwerendy, aby uwzględnić je lub wykluczyć z wykresu za pomocą pól wyboru.

   Górna linia pokazuje ogólny procent DTU dla bazy danych. Słupki pokazują procent procesora CPU, który wybrane kwerendy używane w wybranym przedziale czasu. Na przykład, jeśli **wybrano ostatni tydzień,** każdy pasek reprezentuje jeden dzień.

   ![Najpopularniejsze zapytania](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > Pokazany wiersz DTU jest agregowany do maksymalnej wartości zużycia w okresach jednogodzinnych. Jest przeznaczony do porównania wysokiego poziomu tylko ze statystykami wykonywania zapytań. W niektórych przypadkach wykorzystanie DTU może wydawać się zbyt wysokie w porównaniu do wykonywanych zapytań, ale może to nie być przypadek.
   >
   > Na przykład jeśli kwerenda maxed dtu do 100% tylko przez kilka minut, wiersz DTU w query Performance Insight pokaże całą godzinę zużycia jako 100% (konsekwencją maksymalnej wartości zagregowane).
   >
   > Aby uzyskać lepsze porównanie (do jednej minuty), należy rozważyć utworzenie niestandardowego wykresu wykorzystania identyfikatora DTU:
   >
   > 1. W witrynie Azure portal wybierz pozycję **Azure SQL Database** > **Monitoring**.
   > 2. Wybierz pozycję **Metryki**.
   > 3. Wybierz **+Dodaj wykres**.
   > 4. Wybierz procent DTU na wykresie.
   > 5. Ponadto wybierz **opcję Ostatnie 24 godziny** w lewym górnym menu i zmień je na minutę.
   >
   > Użyj niestandardowego wykresu DTU z dokładniejszym poziomem szczegółów, aby porównać go z wykresem wykonywania kwerendy.

   Dolna siatka pokazuje zagregowane informacje dla widocznych zapytań:

   * Identyfikator kwerendy, który jest unikatowym identyfikatorem kwerendy w bazie danych.
   * Procesor CPU na kwerendę podczas obserwowalnego interwału, który zależy od funkcji agregacji.
   * Czas trwania na kwerendę, który również zależy od funkcji agregacji.
   * Całkowita liczba wykonań dla określonej kwerendy.

2. Jeśli dane staną się przestarzałe, wybierz przycisk **Odśwież.**

3. Użyj suwaków i przycisków powiększenia, aby zmienić interwał obserwacji i zbadać skoki zużycia:

   ![Suwaki i przyciski powiększenia służące do zmiany interwału](./media/sql-database-query-performance/zoom.png)

4. Opcjonalnie można wybrać kartę **Niestandardowe,** aby dostosować widok dla:

   * Metryczny (procesor, czas trwania, liczba wykonania).
   * Przedział czasu (ostatnie 24 godziny, ostatni tydzień lub ostatni miesiąc).
   * Liczba zapytań.
   * Agregacja.
  
   ![Karta Niestandardowa](./media/sql-database-query-performance/custom-tab.png)
  
5. Wybierz przycisk **Przejdź >,** aby wyświetlić dostosowany widok.

   > [!IMPORTANT]
   > Query Performance Insight jest ograniczona do wyświetlania najlepszych 5-20 kwerend zużywających, w zależności od wyboru. Baza danych może uruchamiać wiele innych zapytań poza wyświetlanymi głównymi, a te zapytania nie zostaną uwzględnione na wykresie.
   >
   > Może istnieć typ obciążenia bazy danych, w którym wiele mniejszych zapytań, poza głównymi pokazano, często uruchamiać i używać większości DTU. Te zapytania nie są wyświetlane na wykresie wydajności.
   >
   > Na przykład kwerenda mogła zużywać znaczną ilość DTU przez pewien czas, chociaż jego całkowite zużycie w obserwowanym okresie jest mniejsze niż inne kwerendy zużywające. W takim przypadku wykorzystanie zasobów tej kwerendy nie pojawi się na wykresie.
   >
   > Jeśli chcesz zrozumieć najlepsze wykonania zapytań poza ograniczeniami usługi Query Performance Insight, należy rozważyć użycie [usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) do zaawansowanego monitorowania wydajności bazy danych i rozwiązywania problemów.
   >

## <a name="view-individual-query-details"></a>Wyświetlanie szczegółów poszczególnych kwerend

Aby wyświetlić szczegóły kwerendy:

1. Wybierz dowolną kwerendę na liście najpopularniejszych zapytań.

    ![Lista najpopularniejszych zapytań](./media/sql-database-query-performance/details.png)

   Zostanie otwarty widok szczegółowy. Pokazuje zużycie procesora CPU, czas trwania i liczbę wykonań w czasie.

2. Wybierz operacje wykresu, aby uzyskać szczegółowe informacje.

   * Górny wykres przedstawia wiersz z ogólnym procentem DTU bazy danych. Paski są procentem procesora CPU, który został wykorzystany przez wybrane zapytanie.
   * Drugi wykres przedstawia całkowity czas trwania wybranej kwerendy.
   * Dolny wykres przedstawia całkowitą liczbę wykonań przez wybrane zapytanie.

   ![Szczegóły kwerendy](./media/sql-database-query-performance/query-details.png)

3. Opcjonalnie użyj suwaków, użyj przycisków powiększenia lub wybierz **ustawienia,** aby dostosować sposób wyświetlania danych kwerendy lub wybrać inny zakres czasu.

   > [!IMPORTANT]
   > Query Performance Insight nie przechwytuje żadnych zapytań DDL. W niektórych przypadkach może nie przechwytywać wszystkich zapytań ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Przeglądanie najważniejszych zapytań na czas trwania

Dwie metryki w ujaźnik wydajności kwerendy może pomóc w znalezieniu potencjalnych wąskich gardeł: czas trwania i liczba wykonania.

Długotrwałe zapytania mają największy potencjał do blokowania zasobów dłużej, blokowanie innych użytkowników i ograniczanie skalowalności. Są również najlepszymi kandydatami do optymalizacji.

Aby zidentyfikować długotrwałe zapytania:

1. Otwórz kartę **Niestandardowe** w ucho wydajności kwerendy dla wybranej bazy danych.
2. Zmień dane na **czas trwania**.
3. Wybierz liczbę zapytań i interwał obserwacji.
4. Wybierz funkcję agregacji:

   * **Suma** sumuje cały czas wykonywania kwerendy dla całego interwału obserwacji.
   * **Max** znajduje zapytania, w których czas wykonywania był maksymalny dla całego interwału obserwacji.
   * **Avg** znajduje średni czas wykonywania wszystkich wykonań kwerend i pokazuje najlepsze dla tych średnich.

   ![Czas trwania kwerendy](./media/sql-database-query-performance/top-duration.png)

5. Wybierz przycisk **Przejdź >,** aby wyświetlić dostosowany widok.

   > [!IMPORTANT]
   > Dostosowanie widoku kwerendy nie powoduje aktualizacji linii DTU. Wiersz DTU zawsze pokazuje maksymalną wartość zużycia dla interwału.
   >
   > Aby zrozumieć zużycie DTU bazy danych z bardziej szczegółowymi szczegółami (do jednej minuty), należy rozważyć utworzenie niestandardowego wykresu w witrynie Azure portal:
   >
   > 1. Wybierz **pozycję Monitorowanie bazy danych** > **Monitoring**SQL platformy Azure .
   > 2. Wybierz pozycję **Metryki**.
   > 3. Wybierz **+Dodaj wykres**.
   > 4. Wybierz procent DTU na wykresie.
   > 5. Ponadto wybierz **opcję Ostatnie 24 godziny** w lewym górnym menu i zmień je na minutę.
   >
   > Zaleca się używanie niestandardowego wykresu DTU do porównywania z wykresem wydajności kwerendy.
   >

## <a name="review-top-queries-per-execution-count"></a>Przejrzyj liczbę najważniejszych zapytań na wykonanie

Aplikacja użytkownika, która używa bazy danych może uzyskać powolne, nawet jeśli duża liczba wykonań nie może mieć wpływu na samą bazę danych i użycie zasobów jest niska.

W niektórych przypadkach wysoka liczba wykonań może prowadzić do większej liczby rund sieciowych. Podróże w obie strony wpływają na wydajność. Podlegają one opóźnieniu sieci i opóźnieniu serwera podrzędnego.

Na przykład wiele witryn sieci Web opartych na danych w dużym stopniu uzyskać dostęp do bazy danych dla każdego żądania użytkownika. Mimo że buforowanie połączeń pomaga, zwiększony ruch sieciowy i obciążenie przetwarzania na serwerze bazy danych może spowolnić wydajność. Ogólnie rzecz biorąc, należy do minimum kontynuować podróże w obie strony.

Aby zidentyfikować często wykonywane zapytania ("chatty"):

1. Otwórz kartę **Niestandardowe** w ucho wydajności kwerendy dla wybranej bazy danych.
2. Zmień metryki na **liczbę wykonań**.
3. Wybierz liczbę zapytań i interwał obserwacji.
4. Wybierz przycisk **Przejdź >,** aby wyświetlić dostosowany widok.

   ![Liczba wykonania kwerendy](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Opis adnotacji dostrajania wydajności

Podczas eksplorowania obciążenia w programie Query Performance Insight można zauważyć ikony z pionową linią u góry wykresu.

Te ikony są adnotacjami. Pokazują one zalecenia dotyczące wydajności z [PROGRAMU SQL Database Advisor](sql-database-advisor.md). Najeżdżając kursorem na adnotację, można uzyskać podsumowane informacje na temat zaleceń dotyczących wydajności.

   ![Adnotacja kwerendy](./media/sql-database-query-performance/annotation.png)

Jeśli chcesz zrozumieć więcej lub zastosować zalecenie doradcy, wybierz ikonę, aby otworzyć szczegóły zalecanej akcji. Jeśli jest to aktywne zalecenie, można zastosować go od razu z portalu.

   ![Szczegóły adnotacji kwerendy](./media/sql-database-query-performance/annotation-details.png)

W niektórych przypadkach, ze względu na poziom powiększenia, istnieje możliwość, że adnotacje blisko siebie są zwinięte w jedną adnotację. Query Performance Insight reprezentuje to jako ikonę adnotacji grupy. Wybranie ikony adnotacji grupy powoduje otwarcie nowego bloku zawierającego listę adnotacji.

Skorelowanie zapytań i akcji dostrajania wydajności może pomóc lepiej zrozumieć obciążenie.

## <a name="optimize-the-query-store-configuration"></a>Optymalizuj konfigurację Sklepu zapytań

Podczas korzystania z usługi Query Performance Insight mogą zostać wyświetlene następujące komunikaty o błędach magazynu zapytań:

* "Query Store nie jest poprawnie skonfigurowany w tej bazie danych. Kliknij tutaj, aby dowiedzieć się więcej."
* "Query Store nie jest poprawnie skonfigurowany w tej bazie danych. Kliknij tutaj, aby zmienić ustawienia."

Te komunikaty są zwykle wyświetlane, gdy Query Store nie może zbierać nowych danych.

Pierwszy przypadek ma miejsce, gdy Query Store jest w stanie tylko do odczytu i parametry są ustawione optymalnie. Można to naprawić, zwiększając rozmiar magazynu danych lub czyszcząc Query Store. (Jeśli wyczyścisz Magazyn zapytań, wszystkie wcześniej zebrane dane telemetryczne zostaną utracone).

   ![Szczegóły Magazynu zapytań](./media/sql-database-query-performance/qds-off.png)

Drugi przypadek ma miejsce, gdy Query Store nie jest włączony lub parametry nie są ustawione optymalnie. Można zmienić zasady przechowywania i przechwytywania, a także włączyć Magazyn zapytań, uruchamiając następujące polecenia dostarczone z [programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub witryny Azure portal.

### <a name="recommended-retention-and-capture-policy"></a>Zalecane zasady przechowywania i przechwytywania

Istnieją dwa typy zasad przechowywania:

* **Rozmiar na podstawie:** Jeśli ta zasada jest ustawiona na **AUTO,** będzie automatycznie czyścić dane po osiągnięciu bliskiego maksymalnego rozmiaru.
* **Czas na podstawie:** Domyślnie ta zasada jest ustawiona na 30 dni. Jeśli w Magazynie zapytań zabraknie miejsca, usunie informacje o kwerendach starszych niż 30 dni.

Zasady przechwytywania można ustawić na:

* **Wszystkie:** Query Store przechwytuje wszystkie zapytania.
* **Auto:** Query Store ignoruje rzadkie zapytania i zapytania z nieznacznym czasem kompilacji i wykonywania. Progi dla liczby wykonania, czas trwania kompilacji i czas trwania środowiska uruchomieniowego są określane wewnętrznie. Jest to domyślne ustawienie opcji.
* **Brak:** Magazyn zapytań zatrzymuje przechwytywanie nowych zapytań, ale statystyki środowiska uruchomieniowego dla już przechwyconych zapytań są nadal zbierane.

Firma Microsoft zaleca ustawienie wszystkich zasad **auto** i zasady czyszczenia do 30 dni, wykonując następujące polecenia z [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub witryny Azure portal. (Zamień `YourDB` na nazwę bazy danych).

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Zwiększ rozmiar Magazynu zapytań, łącząc się z bazą danych za pośrednictwem [usługi SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) lub witryny Azure portal i uruchamiając następującą kwerendę. (Zamień `YourDB` na nazwę bazy danych).

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Zastosowanie tych ustawień spowoduje, że magazyn zapytań będzie zbierał dane telemetryczne dla nowych zapytań. Jeśli potrzebujesz Query Store do działania od razu, można opcjonalnie wyczyścić Query Store, uruchamiając następujące zapytanie za pośrednictwem usługi SSMS lub witryny Azure portal. (Zamień `YourDB` na nazwę bazy danych).

> [!NOTE]
> Uruchomienie następującej kwerendy spowoduje usunięcie wszystkich wcześniej zebranych monitorowanych danych telemetrycznych w Magazynie zapytań.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Następne kroki

Rozważ użycie [usługi Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) do zaawansowanego monitorowania wydajności dużej floty pojedynczych i pul, pul elastycznych, wystąpień zarządzanych i baz danych wystąpień.
