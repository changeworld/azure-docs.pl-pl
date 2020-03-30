---
title: Przesyłanie strumieniowe danych przy użyciu integracji usługi Stream Analytics (wersja zapoznawcza)
description: Usługa Azure Stream Analytics umożliwia przesyłanie strumieniowe danych do bazy danych SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820840"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Przesyłanie strumieniowe danych przy użyciu integracji usługi Azure SQL Database Stream Analytics (wersja zapoznawcza)

Użytkownicy mogą teraz pozyskiwania, przetwarzania, wyświetlania i analizowania danych przesyłania strumieniowego w czasie rzeczywistym do tabeli bezpośrednio z bazy danych SQL w witrynie Azure portal przy użyciu [usługi Azure Stream Analytics.](../stream-analytics/stream-analytics-introduction.md) To środowisko umożliwia szeroką gamę scenariuszy, takich jak podłączony samochód, zdalne monitorowanie, wykrywanie oszustw i wiele innych. W witrynie Azure portal można wybrać źródło zdarzeń (Centrum zdarzeń/Centrum IoT), wyświetlić przychodzące zdarzenia w czasie rzeczywistym i wybrać tabelę do przechowywania zdarzeń. Można również napisać kwerendy języka zapytań usługi Stream Analytics w portalu, aby przekształcić zdarzenia przychodzące i zapisać je w wybranej tabeli. Ten nowy punkt wejścia jest dodatkiem do środowiska tworzenia i konfiguracji, które już istnieją w usłudze Stream Analytics. To środowisko rozpoczyna się od kontekstu bazy danych, co pozwala szybko skonfigurować zadanie usługi Stream Analytics i bezproblemowo nawigować między usługami Azure SQL Database i środowiskami usługi Stream Analytics.

![Przepływ analizy strumienia](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

- Przełączanie kontekstu minimalnego: Można rozpocząć od bazy danych SQL w portalu i rozpocząć pozyskiwania danych w czasie rzeczywistym do tabeli bez przełączania się do innej usługi. 
- Zmniejszona liczba kroków: kontekst bazy danych i tabeli jest używany do wstępnej konfiguracji zadania usługi Stream Analytics.
- Dodatkowa łatwość użycia z danymi podglądu: podgląd danych przychodzących ze źródła zdarzeń (Centrum zdarzeń/Usługi IoT) w kontekście wybranej tabeli 


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące zasoby:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/). 
- Baza danych SQL. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie pojedynczej bazy danych w bazie danych SQL Azure](sql-database-single-database-get-started.md).
- Reguła zapory umożliwiająca komputerowi łączenie się z serwerem SQL platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie reguły zapory na poziomie serwera](sql-database-server-level-firewall-rule.md).


## <a name="configure-stream-analytics-integration"></a>Konfigurowanie integracji analizy strumienia

1. Zaloguj się do Portalu Azure. 
2. Przejdź do bazy danych SQL, w której chcesz pozyskiwania danych przesyłania strumieniowego. Wybierz **opcję Analiza strumienia (wersja zapoznawcza)**. 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. Aby rozpocząć pozyskiwania danych przesyłania strumieniowego do tej bazy danych SQL, wybierz pozycję **Utwórz** i nadaj nazwę zadania przesyłania strumieniowego, a następnie wybierz pozycję **Dalej: Wprowadzanie**. 

    ![tworzenie zadania usługi Stream Analytics](media/sql-database-stream-analytics/create-job.png)

4. Wprowadź szczegóły źródła zdarzeń, a następnie wybierz pozycję **Dalej: Dane wyjściowe**.

   - **Typ danych wejściowych:** Centrum zdarzeń/Centrum IoT
   - **Alias wejściowy**: Wprowadź nazwę, aby zidentyfikować źródło zdarzeń 
   - **Subskrypcja**: tak samo jak subskrypcja bazy danych SQL 
   - **Obszar nazw Centrum zdarzeń**: Nazwa obszaru nazw 
   - **Nazwa Centrum zdarzeń**: Nazwa centrum zdarzeń w wybranej przestrzeni nazw 
   - **Nazwa zasad Centrum zdarzeń** (domyślnie, aby utworzyć nową): Nadaj nazwę zasad 
   - **Grupa odbiorców Centrum zdarzeń** (Domyślnie, aby utworzyć nowy): Nadaj nazwę grupy odbiorców  
     - Zaleca się utworzenie grupy konsumentów i zasad dla każdego nowego zadania usługi Azure Stream Analytics, które tworzysz w tym miejscu. Grupy konsumentów zezwalają tylko na pięć równoczesnych czytników, więc zapewnienie dedykowanej grupy odbiorców dla każdego zadania pozwoli uniknąć błędów, które mogą wyniknąć z przekroczenia tego limitu. Dedykowane zasady umożliwia obracanie klucza lub odwoływanie uprawnień bez wpływu na inne zasoby.

    ![tworzenie zadania usługi Stream Analytics](media/sql-database-stream-analytics/create-job-output.png)

5. Wybierz tabelę, do której chcesz pozyskiwania dane przesyłane strumieniowo. Po zakończeniu wybierz pozycję **Utwórz**.
   - **Nazwa użytkownika**, **Hasło:** Wprowadź poświadczenia do uwierzytelniania serwera SQL. Wybierz przycisk **Weryfikuj**.
   - **Tabela**: Wybierz **pozycję Utwórz nowe** lub **Użyj istniejącego**. W tym przepływie wybierzmy pozycję **Utwórz**. Spowoduje to utworzenie nowej tabeli po uruchomieniu zadania analizy strumienia.

    ![tworzenie zadania usługi Stream Analytics](media/sql-database-stream-analytics/create.png)

6. Zostanie otwarta strona kwerendy z następującymi szczegółami:

   - Twoje **dane wejściowe** (źródło zdarzeń wejściowych), z którego będą pozyskiwane dane  
   - Twoje **dane wyjściowe** (tabela danych wyjściowych), które będą przechowywane przekształcone dane 
   - Przykładowa [kwerenda SAQL](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) z instrukcją SELECT. 
   - **Podgląd danych wejściowych**: Pokazuje migawkę najnowszych danych przychodzących ze źródła zdarzeń wejściowych.  
     - Typ serializacji w danych jest wykrywany automatycznie (JSON/CSV). Można go ręcznie zmienić również na JSON / CSV / AVRO. 
     - Można wyświetlić podgląd danych przychodzących w formacie Tabela lub Raw. 
     - Jeśli wyświetlane dane nie są aktualne, wybierz **odśwież,** aby wyświetlić najnowsze zdarzenia. 
     - Wybierz **wybierz wybierz zakres czasu,** aby przetestować kwerendę względem określonego zakresu czasu zdarzeń przychodzących. 
     - Wybierz **opcję Przekaż przykładowe dane wejściowe,** aby przetestować zapytanie, przesyłając przykładowy plik JSON/CSV. Aby uzyskać więcej informacji na temat testowania kwerendy SAQL, zobacz [Testowanie zadania usługi Azure Stream Analytics z przykładowymi danymi.](../stream-analytics/stream-analytics-test-query.md) 

    ![kwerenda testna](media/sql-database-stream-analytics/test-query.png)


   - **Wyniki testów**: Wybierz **zapytanie testowe,** aby zobaczyć wyniki zapytania przesyłania strumieniowego 

    ![wyniki testu](media/sql-database-stream-analytics/test-results.png)

   - **Schemat wyników testów:** pokazuje schemat wyników zapytania przesyłania strumieniowego po przetestowaniu. Upewnij się, że schemat wyników testu jest zgodny ze schematem danych wyjściowych. 

    ![schemat wyników testów](media/sql-database-stream-analytics/test-results-schema.png)


   - **Schemat wyjściowy**: Zawiera schemat tabeli wybranej w kroku 5 (nowy lub istniejący).
     - Utwórz nowy: Jeśli ta opcja została wybrana w kroku 5, schemat nie będzie jeszcze widoczny, dopóki nie rozpoczniesz zadania przesyłania strumieniowego. Podczas tworzenia nowej tabeli wybierz odpowiedni indeks tabeli. Aby uzyskać więcej informacji na temat indeksowania tabel, zobacz [Indeksy klastrowane i nieklastrowane opisane](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
    - Użyj istniejącego: Jeśli ta opcja została wybrana w kroku 5, zobaczysz schemat wybranej tabeli. 
 
7. Po zakończeniu tworzenia & testowaniu kwerendy wybierz pozycję **Zapisz kwerendę**. Wybierz **zadanie Rozpocznij analizę strumienia,** aby rozpocząć pozyskiwania przekształconych danych do tabeli SQL. Po zakończeniu następujących pól **uruchom** zadanie. 
   - **Godzina rozpoczęcia danych wyjściowych:** Określa czas pierwszego wyjścia zadania.  
     - Teraz: Zadanie rozpocznie się teraz i będzie przetwarzać nowe przychodzące dane.
     - Niestandardowe: Zadanie rozpocznie się teraz, ale będzie przetwarzać dane z określonego punktu w czasie (które mogą być w przeszłości lub w przyszłości). Aby uzyskać więcej informacji, zobacz [Jak uruchomić zadanie usługi Azure Stream Analytics](../stream-analytics/start-job.md).
   - **Jednostki przesyłania strumieniowego:** Usługa Azure Stream Analytics jest wyceniana przez liczbę jednostek przesyłania strumieniowego wymaganych do przetworzenia danych do usługi. Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/). 
   - **Obsługa błędów danych wyjściowych:**  
     - Ponów próbę: Po wystąpieniu błędu usługa Azure Stream Analytics ponawia próby zapisywania zdarzenia przez czas nieokreślony, dopóki zapis nie powiedzie się. Nie ma limitu czasu dla ponownych prób. Po pewnym czasie wszystkie kolejne zdarzenia są blokowane z przetwarzania przez zdarzenie, które jest ponawiane. Ta opcja jest domyślną zasadą obsługi błędów danych wyjściowych. 
     - Upuść: usługa Azure Stream Analytics spowoduje upuszczenie dowolnego zdarzenia wyjściowego powodującego błąd konwersji danych. Porzuconych zdarzeń nie można odzyskać na potrzeby ponownego przetwarzania później. Wszystkie błędy przejściowe (na przykład błędy sieciowe) są ponawiane niezależnie od konfiguracji zasad obsługi błędów wyjściowych. 
   - **Ustawienia wyjściowe bazy danych SQL:** Opcja dziedziczenia schematu partycjonowania poprzedniego kroku kwerendy, aby włączyć w pełni równoległą topologię z wieloma modułami zapisującymi do tabeli. Aby uzyskać więcej informacji, zobacz [dane wyjściowe usługi Azure Stream Analytics do usługi Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Maksymalna liczba partii**: Zalecany górny limit liczby rekordów wysłanych z każdą transakcją wstawiania zbiorczego.  
    Aby uzyskać więcej informacji na temat obsługi błędów danych [wyjściowych, zobacz Zasady błędów wyjściowych w usłudze Azure Stream Analytics](../stream-analytics/stream-analytics-output-error-policy.md).  

    ![rozpoczynanie zadania](media/sql-database-stream-analytics/start-job.png)

8. Po uruchomieniu zadania zostanie wyświetlona praca Uruchomiona na liście i możesz podjąć następujące czynności: 
   - **Rozpocznij/zatrzymaj zadanie**: Jeśli zadanie jest uruchomione, można zatrzymać zadanie. Jeśli zadanie zostanie zatrzymane, można uruchomić zadanie. 
   - **Zadanie edycji**: Kwerendę można edytować. Jeśli chcesz zrobić więcej zmian w zadaniu ex, dodaj więcej danych wejściowych/wyjściowych, a następnie otwórz zadanie w usłudze Stream Analytics. Opcja edycji jest wyłączona, gdy zadanie jest uruchomione. 
   - **Podgląd tabeli wyjściowej:** Można wyświetlić podgląd tabeli w edytorze zapytań SQL. 
   - **Otwórz w usłudze Stream Analytics:** Otwórz zadanie w usłudze Usługi Stream Analytics, aby wyświetlić monitorowanie, debugowanie szczegółów zadania. 


    ![przesyłanie strumieniowe zadań analitycznych](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>Następne kroki

- [Dokumentacja usługi Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)
- [Wzorce rozwiązań usługi Azure Stream Analytics](../stream-analytics/stream-analytics-solution-patterns.md)
