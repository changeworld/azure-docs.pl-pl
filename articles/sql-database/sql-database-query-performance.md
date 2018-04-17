---
title: Zapytanie szczegółowe informacje o wydajności dla usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Monitorowanie wydajności zapytania identyfikuje większość używające Procesora zapytań dla bazy danych SQL Azure.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c0cff73e8164bd493554b663d4ffbbf20f5155cb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-sql-database-query-performance-insight"></a>Wgląd wydajności kwerendy bazy danych Azure SQL
Zarządzanie i dostrajania wydajności relacyjnych baz danych jest trudne zadanie, które wymaga znaczących wiedzę i czas inwestycji. Szczegółowe informacje o wydajności zapytań umożliwia poświęcają mniej czasu Rozwiązywanie problemów z bazy danych wydajności przez korzystanie z następujących:

* Bardziej szczegółowe informacje na temat użycia zasobów (bazy danych DTU) bazy danych. 
* Najważniejszych zapytań przez Procesor/czas trwania/wykonywania licznik, który może potencjalnie dostroić zwiększonej wydajności.
* Umożliwia przechodzenie do szczegółów zapytania, Wyświetl tekst i historii wykorzystania zasobów. 
* Adnotacje, które zawierają akcje wykonywane przez dostrajania wydajności [doradcy bazy danych SQL Azure](sql-database-advisor.md)  



## <a name="prerequisites"></a>Wymagania wstępne
* Szczegółowe informacje o wydajności wymaga, aby zapytania [magazyn zapytań](https://msdn.microsoft.com/library/dn817826.aspx) jest aktywny w bazie danych. Jeśli Magazyn zapytań nie jest uruchomiona, monituje o portalu, możesz ją włączyć.

## <a name="permissions"></a>Uprawnienia
Następujące [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) wymagane są uprawnienia do używania szczegółowe informacje o wydajności zapytań: 

* **Czytnik**, **właściciela**, **współautora**, **Współautor bazy danych SQL**, lub **programu SQL Server współautora** uprawnienia są wymagane Aby wyświetlić zasobu najwyższego wykorzystywanie kwerend i wykresy. 
* **Właściciel**, **współautora**, **Współautor bazy danych SQL**, lub **programu SQL Server współautora** wymagane są uprawnienia do wyświetlania tekstu zapytania.

## <a name="using-query-performance-insight"></a>Przy użyciu szczegółowe informacje o wydajności zapytań
Szczegółowe informacje o wydajności zapytań jest łatwy w użyciu:

* Otwórz [portalu Azure](https://portal.azure.com/) i Znajdź bazy danych, które chcesz sprawdzić. 
  * Z menu po lewej stronie, w ramach pomocy technicznej i rozwiązywania problemów wybierz opcję "Szybkie szczegółowe informacje o wydajności zapytań".
* Na pierwszej karcie zapoznaj się z listą najważniejszych zapytań korzystanie z zasobów.
* Wybierz poszczególne zapytania, aby wyświetlić jego szczegóły.
* Otwórz [doradcy bazy danych SQL Azure](sql-database-advisor.md) i sprawdź, czy wszystkie zalecenia są dostępne.
* Za pomocą suwaków lub Powiększ, aby zmienić interwał obserwowanych ikon.
  
    ![pulpit nawigacyjny wydajności](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Po kilku godzinach danych musi być przechwytywane przez Magazyn zapytań dla bazy danych SQL w celu zapewnienia szczegółowe informacje o wydajności zapytań. Jeśli bazy danych nie ma aktywności lub magazyn zapytań był nieaktywny w przedziale czasu, wykresy będzie pusta podczas wyświetlania tego okresu. Mogą włączyć magazyn zapytań w dowolnym momencie, jeśli nie jest uruchomiona.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Przejrzyj górny Procesora korzystanie z zapytań
W [portal](http://portal.azure.com) wykonaj następujące czynności:

1. Przejdź do bazy danych SQL, a następnie kliknij przycisk **wszystkie ustawienia** > **pomocy technicznej i rozwiązywania problemów** > **szczegółowe informacje o wydajności zapytań**. 
   
    ![Szczegółowe informacje o wydajności zapytań][1]
   
    Otwiera widok najważniejszych zapytań i najważniejszych Procesora zapytań odbierająca komunikaty są wyświetlane.
2. Kliknij przycisk wokół wykresu, aby uzyskać szczegółowe informacje.<br>Pierwszego wiersza zawiera ogólną % jednostek DTU dla bazy danych, podczas pasków Pokaż procesora CPU Wynoszące % używane przez wybrane zapytania wybrany przedział czasu (na przykład, jeśli **ostatni tydzień** wybrano każdy pasek reprezentuje jeden dzień).
   
    ![najważniejszych zapytań][2]
   
    Siatka dolnej reprezentuje zagregowane informacje widoczne zapytań.
   
   * Identyfikator zapytania — Unikatowy identyfikator zapytania w bazie danych.
   * Procesor zapytań interwale zauważalne (zależy od funkcji agregacji tabeli).
   * Czas trwania jednego zapytania (zależy od funkcji agregacji tabeli).
   * Całkowita liczba wykonaniami dla określonego zapytania.
     
     Wybierz lub wyczyść poszczególnych kwerend do dołączania lub wykluczania ich z wykresu za pomocą pola wyboru.
3. W przypadku danych starych, kliknij przycisk **Odśwież** przycisku.
4. Można za pomocą suwaków i powiększenia przycisków, aby zmienić interwał obserwacji i zbadaj nagłego: ![ustawienia](./media/sql-database-query-performance/zoom.png)
5. Opcjonalnie, jeśli chcesz inny widok, możesz zaznaczyć **niestandardowy** kartę i ustawić:
   
   * Metryka (CPU, czas trwania, liczba wykonań)
   * Interwał czasu (ostatni tydzień ostatni miesiąc ostatnich 24 godzin). 
   * Liczba zapytań.
   * Funkcją agregacji.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Wyświetlanie szczegółów pojedynczych zapytań
Aby wyświetlić szczegóły kwerendy:

1. Kliknij każde zapytanie na liście najważniejszych zapytań.
   
    ![szczegóły](./media/sql-database-query-performance/details.png)
2. Otwiera widok szczegółów i liczba zużycie/czas trwania/wykonania Procesora kwerend dzieli się wraz z upływem czasu.
3. Kliknij przycisk wokół wykresu, aby uzyskać szczegółowe informacje.
   
   * Górny wykres pokazuje wiersza z ogólną % jednostek dtu w warstwie bazy danych i paski są używane przez wybrane zapytanie procent użycia procesora CPU.
   * Drugi wykres przedstawia całkowity czas trwania przez wybrane zapytanie.
   * Wykres dolnej pokazuje łączna liczba wykonań przez wybrane zapytanie.
     
     ![Szczegóły kwerendy][3]
4. Opcjonalnie, za pomocą suwaków, przyciski powiększania lub kliknij przycisk **ustawienia** można dostosować sposób wyświetlania danych zapytania lub do pobrania w innym czasie.

## <a name="review-top-queries-per-duration"></a>Przejrzyj najważniejszych zapytań na czas trwania
W najnowszych aktualizacji szczegółowe informacje o wydajności zapytań, wprowadzono dwie nowe metryki, które mogą ułatwić identyfikację wąskich gardeł: liczba czas trwania i wykonywanie.<br>

Kwerend mają potencjalnie największy blokowanie zasobów dłużej, blokowania innych użytkowników i ograniczanie skalowalności. Są to najbardziej odpowiednich do optymalizacji.<br>

Aby zidentyfikować długo działające kwerendy:

1. Otwórz **niestandardowy** karcie szczegółowe informacje o wydajności zapytań dla wybranej bazy danych
2. Zmień metryki za **czas trwania**
3. Wybierz liczbę zapytań i przedziałach obserwacji
4. Wybierz funkcję agregacji
   
   * **Suma** dodaje wszystkie czasu wykonywania zapytania interwału obserwacji całego.
   * **Maksymalna liczba** znajduje zapytań, które czas wykonywania był maksymalna w przedziałach obserwacji całego.
   * **Średni** znajduje Średni czas wykonywania wszystkich wykonania zapytania i wyświetlić górnej poza tych wartości. 
     
     ![czas trwania kwerendy][4]

## <a name="review-top-queries-per-execution-count"></a>Przejrzyj najważniejszych zapytań na liczba wykonania
Wysoka liczba wykonań nie może mieć wpływ sama baza danych i użycia zasobów może być niski, ale może spowodować, że aplikacja ogólna powolne.

W niektórych przypadkach wykonanie bardzo dużej liczby może spowodować wzrost sieci rund. Rund znacznie wpłynąć na wydajność. Są one może ulec opóźnienia sieci i serwer podrzędny opóźnienia. 

Na przykład wiele witryn sieci Web opartych na danych silnie dostęp do bazy danych dla każdego żądania użytkownika. Podczas połączenia buforowanie pomaga, ruch sieciowy zwiększona i przetwarzania obciążenia na serwerze bazy danych może niekorzystnie wpłynąć na wydajność.  Ogólne porady się zachować rund do minimum.

Aby zidentyfikować często wykonywane zapytania zapytań ("chatty"):

1. Otwórz **niestandardowy** karcie szczegółowe informacje o wydajności zapytań dla wybranej bazy danych
2. Zmień metryki za **liczba wykonania**
3. Wybierz liczbę zapytań i przedziałach obserwacji
   
    ![Liczba wykonania zapytania][5]

## <a name="understanding-performance-tuning-annotations"></a>Opis adnotacje dostrajania wydajności
Podczas eksploracji obciążenie w szczegółowe informacje o wydajności zapytań, można zauważyć ikon z pionowym wierszem na wykresie.<br>

Te ikony są adnotacje; reprezentują wydajności wpływających na akcje wykonywane przez [doradcy bazy danych SQL Azure](sql-database-advisor.md). Dzięki aktywowania adnotacji możesz uzyskać podstawowe informacje dotyczące akcji:

![Adnotacja zapytania][6]

Jeśli chcesz dowiedzieć się więcej lub zastosować zalecenia usługi advisor, kliknij ikonę. Zostanie otwarty szczegóły akcji. Jeśli jest aktywny zalecenia można zastosować razu za pomocą polecenia.

![Szczegóły adnotacji kwerendy][7]

### <a name="multiple-annotations"></a>Wiele adnotacji.
Jest to możliwe, że z powodu poziomu powiększenia adnotacje zbliża się wzajemnie będzie pobrać zwinięte do jednego. To będą reprezentowane przez specjalnej ikony, klikając go zostanie otwarty nowy blok, gdy lista pogrupowanych adnotacje będą wyświetlane.
Korelowanie zapytań i akcji dostrajania wydajności może pomóc lepiej zrozumieć obciążenie. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optymalizowanie konfiguracji magazynu zapytań, aby uzyskać szczegółowe informacje o wydajności zapytań
Podczas korzystania z szczegółowe informacje o wydajności zapytań mogą wystąpić następujące komunikaty magazynu zapytań:

* "Magazyn zapytań nie jest prawidłowo skonfigurowany na tę bazę danych. "Kliknij tutaj, aby dowiedzieć się więcej".
* "Magazyn zapytań nie jest prawidłowo skonfigurowany na tę bazę danych. "Kliknij tutaj, aby zmienić ustawienia". 

Komunikaty te są zwykle widoczne, gdy magazyn zapytań nie będzie mógł zbierać dane nowego. 

Pierwszym przypadku się stanie, jeśli magazyn zapytań jest w stanie tylko do odczytu i parametry zostały ustawione optymalnie. Można temu zaradzić przez zwiększenie rozmiaru magazynu zapytań lub wyczyszczenie magazynu zapytań.

![przycisk qds][8]

Drugim przypadku się stanie, jeśli magazyn zapytań jest wyłączony lub parametry nie są ustawione optymalnie. <br>Możesz zmienić zasady przechowywania i przechwytywania i Włącz magazyn zapytań, wykonując poniższe polecenia lub bezpośrednio z portalu:

![przycisk qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Zalecane zasady przechowywania i przechwytywania
Istnieją dwa typy zasad przechowywania:

* Rozmiar na podstawie — Jeśli wartość AUTO go wyczyści danych automatycznie, gdy zostanie osiągnięty maksymalny rozmiar w pobliżu.
* Oparte na godzinie — domyślnie możemy ustawi do 30 dni, co oznacza, jeśli magazyn zapytań zabraknie miejsca, spowoduje usunięcie informacji dotyczących zapytań starsze niż 30 dni

Przechwyć można ustawić zasady:

* **Wszystkie** -przechwytuje wszystkie zapytania.
* **Automatycznie** -rzadkim zapytania i zapytania z nieznaczne czas trwania kompilacji i wykonywanie zostaną zignorowane. Wewnętrznie zależą progi czas wykonywania liczby, kompilacji i środowiska wykonawczego. Jest to opcja domyślna.
* **Brak** — magazyn zapytań zatrzymuje przechwytywanie nowego zapytania, jednak Statystyka środowiska uruchomieniowego już przechwyconych zapytania są nadal zbierane.

Zaleca się ustawiania zasad wszystkie AUTOMATYCZNIE i wyczyść zasad do 30 dni:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Zwiększ rozmiar magazynu zapytań. Ten proces można wykonać przy połączenia z bazą danych i wystawianie następujące zapytanie:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Stosowanie tych ustawień spowoduje po pewnym czasie magazynu zapytań zbieranie nowego zapytania, jednak jeśli nie chcesz czekać można wyczyścić magazyn zapytań. 

> [!NOTE]
> Wykonywanie zapytania następujące spowoduje usunięcie wszystkich bieżących informacji w magazynie zapytań. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Podsumowanie
Szczegółowe informacje o wydajności zapytań ułatwia zrozumienie wpływu obciążenia zapytania i jego znaczenia zużycie zasobów w bazie danych. W przypadku tej funkcji będzie informacje o zapytania zużywające najwięcej zasobów i łatwo zidentyfikować te, aby rozwiązać problem, zanim staną się problem.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać dodatkowe zalecenia dotyczące poprawy wydajności bazy danych SQL, kliknij [zalecenia](sql-database-advisor.md) na **szczegółowe informacje o wydajności zapytań** bloku.

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

