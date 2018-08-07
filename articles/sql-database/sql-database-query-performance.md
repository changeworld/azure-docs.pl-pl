---
title: Wyszukiwać szczegółowe informacje o wydajności usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Monitorowanie wydajności zapytań identyfikuje większość zapytań korzystających z procesora CPU dla usługi Azure SQL Database.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 5069b4e69c53ed93e9018cef2517f6125b838d12
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524169"
---
# <a name="azure-sql-database-query-performance-insight"></a>Usługi Azure SQL Database Query Performance Insight
Zarządzanie i dostosowywanie wydajności relacyjnych baz danych jest trudniejszym zadaniem, która wymaga znaczących wiedzę i czas inwestycji. Query Performance Insight pozwala poświęcać mniej czasu na rozwiązywanie problemów z wydajnością bazy danych, zapewniając następujące czynności:

* Lepszy wgląd w swoje użycie zasobów (DTU) baz danych. 
* Najpopularniejsze zapytania według liczby Procesor/czas trwania/wykonywania, które potencjalnie mogą być dostosowane w celu zwiększenia wydajności.
* Przechodzenie do szczegółów do szczegółów zapytania, wyświetlanie historii wykorzystania zasobów i tekstem. 
* Adnotacje, które pokazują akcje wykonywane przez dostrajania wydajności [Doradcę bazy danych Azure SQL](sql-database-advisor.md)  



## <a name="prerequisites"></a>Wymagania wstępne
* Wymaga szczegółowych informacji o wydajności zapytań [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) jest aktywny w bazie danych. Jeśli Query Store nie jest uruchomiona, w portalu zostanie wyświetlony monit o ją włączyć.

## <a name="permissions"></a>Uprawnienia
Następujące [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) wymagane są uprawnienia do użycia Query Performance Insight: 

* **Czytnik**, **właściciela**, **Współautor**, **Współautor bazy danych SQL**, lub **Współautor serwera SQL Server** nie są wymagane uprawnienia Aby wyświetlić najważniejsze zasób korzystający z zapytania i wykresy. 
* **Właściciel**, **Współautor**, **Współautor bazy danych SQL**, lub **Współautor serwera SQL Server** są wymagane uprawnienia, aby wyświetlić tekst zapytania.

## <a name="using-query-performance-insight"></a>Za pomocą Query Performance Insight
Query Performance Insight jest łatwa w użyciu:

* Otwórz [witryny Azure portal](https://portal.azure.com/) i Znajdź bazy danych, który chcesz zbadać. 
  * Z menu po lewej stronie, w ramach pomocy technicznej i rozwiązywania problemów wybierz "Query Performance Insight".
* Na pierwszej karcie przejrzyj listę najpopularniejsze zapytania korzystające z zasobów.
* Wybierz określone zapytanie, aby wyświetlić jego szczegóły.
* Otwórz [Doradcę bazy danych Azure SQL](sql-database-advisor.md) i sprawdź, czy wszystkie zalecenia są dostępne.
* Użyj suwaków lub Powiększ, aby zmienić interwał obserwowanych ikon.
  
    ![pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Po kilku godzinach danych musi być przechwytywane przez Store kwerendy dla bazy danych SQL zapewnić szczegółowe informacje o wydajności zapytań. Jeśli baza danych nie ma żadnych działań lub Query Store nie było aktywne przez pewien okres czasu, wykresy będzie pusta, podczas wyświetlania tego okresu. Query Store może włączyć w dowolnym momencie, jeśli nie jest uruchomiona.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Przeglądanie zapytań zużywających najwięcej zasobów procesora CPU
W [portal](http://portal.azure.com) wykonaj następujące czynności:

1. Przejdź do bazy danych SQL, a następnie kliknij przycisk **wszystkie ustawienia** > **pomoc techniczna i rozwiązywanie problemów** > **szczegółowych informacji o wydajności zapytań**. 
   
    ![Szczegółowe informacje o wydajności zapytań][1]
   
    Zostanie otwarty widok najpopularniejsze zapytania, i wymieniono najpopularniejsze zapytania zużywające procesora CPU.
2. Kliknij na wykresie, aby uzyskać szczegółowe informacje.<br>Górna linia pokazuje ogólną % jednostek DTU bazy danych, gdy słupki pokazują procent użycia procesora CPU, używane przez wybrane zapytanie wybrany przedział czasu (na przykład, jeśli **ostatni tydzień** wybrano Każdy słupek reprezentuje jeden dzień).
   
    ![Najpopularniejsze zapytania][2]
   
    Siatka dolnej reprezentuje zagregowane informacje widoczne dla zapytań.
   
   * Identyfikator zapytania — Unikatowy identyfikator zapytania w bazie danych.
   * Procesor zapytań dostrzegalnych przedział czasu (w zależności od funkcji agregacji).
   * Czas trwania jednego zapytania (w zależności od funkcji agregacji).
   * Łączna liczba wykonań dla określonego zapytania.
     
     Zaznacz lub wyczyść pojedynczych zapytań do dołączania lub wykluczania ich z wykresu za pomocą pola wyboru.
3. Jeśli dane się starzeją, kliknij przycisk **Odśwież** przycisku.
4. Można za pomocą suwaków i powiększenia przycisków, aby zmienić interwał obserwacji i badanie wartości graniczne: ![ustawienia](./media/sql-database-query-performance/zoom.png)
5. Opcjonalnie, jeśli chcesz, aby inny widok, możesz wybrać **niestandardowe** kartę i ustawić:
   
   * Metryki (procesora CPU lub czas trwania, liczba wykonań)
   * Przedział czasu (ostatni tydzień w zeszłym miesiącu ostatnie 24 godziny). 
   * Liczba zapytań.
   * Funkcją agregacji.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Wyświetlanie szczegółów poszczególnych zapytań
Aby wyświetlić szczegóły kwerendy:

1. Kliknij dowolną kwerendę na liście najpopularniejsze zapytania.
   
    ![szczegóły](./media/sql-database-query-performance/details.png)
2. Zostanie otwarty w widoku szczegółów i licznik użycia/czas trwania/wykonywania procesora CPU zapytania dzieli się wraz z upływem czasu.
3. Kliknij na wykresie, aby uzyskać szczegółowe informacje.
   
   * Górny wykres przedstawia wiersz z ogólną % jednostek DTU bazy danych i słupki są procent użycia procesora CPU wykorzystany przez wybrane zapytanie.
   * Drugi wykres przedstawia całkowity czas trwania przez wybrane zapytanie.
   * Dolny wykres pokazuje łącznej liczby wykonań przez wybrane zapytanie.
     
     ![Szczegóły zapytania][3]
4. Opcjonalnie użyj suwaki, przyciski powiększania lub kliknij **ustawienia** dostosować sposób wyświetlania zapytania o dane lub wybierz w innym czasie.

## <a name="review-top-queries-per-duration"></a>Przejrzeć najpopularniejsze zapytania na czas trwania
W najnowszej aktualizacji Query Performance Insight, wprowadziliśmy dwie nowe metryki, które mogą pomóc w identyfikacji wąskich gardeł: czas trwania i wykonywania licznik.<br>

Długotrwałe zapytania mają największe możliwości blokowania zasobów dłużej blokowania innym użytkownikom oraz ograniczenie skalowalności. Są one również wybrać najlepszych kandydatów do optymalizacji.<br>

Aby zidentyfikować długotrwałe zapytania:

1. Otwórz **niestandardowe** karcie Query Performance Insight dla wybranej bazy danych
2. Zmiana metryki, aby być **czas trwania**
3. Wybierz liczbę zapytań i obserwowanie interwału
4. Wybierz funkcję agregacji
   
   * **Suma** dodaje przedział czasu obserwacji całego cały czas wykonywania zapytania.
   * **Maksymalna liczba** znajdzie zapytań, których czas wykonywania był maksymalna interwałem całego obserwacji.
   * **Średnia liczba** odnajduje Średni czas wykonania dla wszystkich wykonań zapytania i dowiesz się u góry poza te wartości średnie. 
     
     ![Czas trwania zapytania][4]

## <a name="review-top-queries-per-execution-count"></a>Przejrzeć najpopularniejsze zapytania według liczby wykonań
Dużą liczbę wykonań może nie mieć wpływ na samej bazy danych i użycie zasobów może być niski, ale cała aplikacja może uzyskać wolne.

W niektórych przypadkach wykonywanie dużej liczby może prowadzić do liczby rund sieciowych. Dwustronne znacznie wpłynąć na wydajność. Są one ulec opóźnienia sieci i na czas oczekiwania serwera podrzędnego. 

Na przykład wiele witryn sieci Web opartych na danych intensywnie dostęp do bazy danych dla każdego żądania użytkownika. Gdy połączenie buforowanie pomaga, ruch sieciowy zwiększone i obciążenie serwera bazy danych może niekorzystnie wpłynąć na wydajność.  Ogólne porady dotyczące jest zapewnienie rund do minimum.

Aby zidentyfikować często wykonywane zapytania ("dużą liczbą") zapytania:

1. Otwórz **niestandardowe** karcie Query Performance Insight dla wybranej bazy danych
2. Zmiana metryki, aby być **Liczba wykonań**
3. Wybierz liczbę zapytań i obserwowanie interwału
   
    ![Liczba wykonań zapytania][5]

## <a name="understanding-performance-tuning-annotations"></a>Opis adnotacje dotyczące dostrajania wydajności
Podczas eksplorowania obciążenie w Query Performance Insight, można zauważyć ikony z pionowym wierszem na podstawie wykresu.<br>

Te ikony są adnotacje; reprezentują one mające wpływ na akcje wykonywane przez wydajność [Doradcę bazy danych Azure SQL](sql-database-advisor.md). Dzięki aktywowania adnotacji możesz uzyskać podstawowe informacje dotyczące akcji:

![Adnotacja zapytania][6]

Jeśli chcesz dowiedzieć się więcej lub zastosować Doradca, zalecenia, kliknij ikonę. Zostanie otwarty szczegóły akcji. Jeśli jest aktywna Rekomendacja można zastosować razu za pomocą polecenia.

![Szczegóły adnotacji zapytania][7]

### <a name="multiple-annotations"></a>Wiele adnotacji.
Jest to możliwe, że ze względu na poziom powiększenia adnotacji, które znajdują się blisko siebie będzie pobrać zwinięte do jednego. To będą reprezentowane przez specjalną ikonę, klikając go zostanie otwarty nowy blok, gdzie lista pogrupowanych adnotacje będą wyświetlane.
Korelowanie zapytań i akcji dostrajania wydajności może pomóc lepiej zrozumieć obciążenia. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optymalizacja konfiguracji Query Store Query Performance Insight
Podczas używania Query Performance Insight mogą wystąpić następujące komunikaty Query Store:

* "Query Store nie jest prawidłowo skonfigurowany na tej bazy danych. "Kliknij tutaj, aby dowiedzieć się więcej".
* "Query Store nie jest prawidłowo skonfigurowany na tej bazy danych. "Kliknij tutaj, aby zmienić ustawienia". 

Te komunikaty są zwykle wyświetlane, gdy Query Store nie jest w stanie do zbierania nowych danych. 

Pierwszy przypadek występuje, gdy Query Store jest w stanie tylko do odczytu, a parametry są ustawione optymalnie. Można to naprawić przez zwiększenie rozmiaru Query Store lub wyczyszczenie Query Store.

![przycisk qds][8]

Drugi przypadek występuje, gdy Store zapytań jest wyłączony lub parametry nie są ustawione optymalnie. <br>Można zmienić zasady przechowywania i przechwytywania i włączyć Query Store, wykonując poniższe polecenia lub bezpośrednio z poziomu portalu:

![przycisk qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Zalecane zasady dotyczące przechowywania i przechwytywania
Istnieją dwa typy zasad przechowywania:

* Rozmiar na podstawie — Jeśli wartość AUTO go spowoduje wyczyszczenie danych automatycznie, gdy zostanie osiągnięty w pobliżu maksymalnego rozmiaru.
* Oparte na godzinie — domyślnie których firma Microsoft ustawi do 30 dni, co oznacza, że jeśli Query Store wyczerpania miejsca, spowoduje to usunięcie informacji o zapytania starsze niż 30 dni

Przechwytywanie można ustawić zasady:

* **Wszystkie** — rejestruje wszystkie zapytania.
* **Automatyczne** -rzadkie zapytań i zapytania z nieznaczące czas kompilacji i wykonania są ignorowane. Progi dla czasu trwania liczby, kompilacji i środowiska uruchomieniowego wykonywania wewnętrznie są określane. Jest to opcja domyślna.
* **Brak** -Query Store zatrzymuje przechwytywanie nowych zapytań, jednak nadal zbieranymi statystyki czasu wykonywania zapytań już przechwycony.

Firma Microsoft zaleca ustawienie wszystkie zasady na automatyczny, a zasady czystego do 30 dni:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Zwiększ rozmiar Query Store. To może zostać wykonana przez połączenie z bazą danych i wydawanie następujące zapytanie:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Stosowania tych ustawień spowoduje ostatecznie Query Store zbieranie nowych zapytań, jednak jeśli nie chcesz czekać można wyczyścić Query Store. 

> [!NOTE]
> Wykonywanie zapytania następujące spowoduje usunięcie wszystkich bieżących informacji w Store zapytania. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Podsumowanie
Query Performance Insight pomaga zrozumieć wpływ na sposób obciążenie zapytań i jego znaczenia zużycie zasobów baz danych. Dzięki tej funkcji będą informacje o pierwszych zapytań zużywających najwięcej zasobów i łatwo zidentyfikować te, aby rozwiązać problem, zanim staną się problem.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać dodatkowe zalecenia dotyczące poprawy wydajności bazy danych SQL kliknij [zalecenia](sql-database-advisor.md) na **Query Performance Insight** bloku.

![Doradca wydajności](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

