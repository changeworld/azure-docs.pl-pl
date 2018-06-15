---
title: Power BI pulpitu nawigacyjnego integracji z usługą Azure Stream Analytics
description: W tym artykule opisano sposób użycia w czasie rzeczywistym pulpit nawigacyjny usługi Power BI do wizualizacji danych poza zadanie usługi analiza strumienia Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2017
ms.openlocfilehash: e84903870110091d527e870600d9a67bdc9cc6e5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418458"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Samouczek: Analiza strumienia i usługi Power BI: pulpitu nawigacyjnego analytics w czasie rzeczywistym do strumieniowego przesyłania danych
Usługa Azure Stream Analytics pozwala korzystać z jednego z początku narzędzi analizy biznesowej, [Microsoft Power BI](https://powerbi.com/). W tym artykule dowiesz się, jak utworzyć przy użyciu usługi Power BI jako dane wyjściowe dla Twojego zadania usługi analiza strumienia Azure narzędzia do analizy biznesowej. Możesz również sposób tworzenia i używania w czasie rzeczywistym pulpitu nawigacyjnego.

W tym artykule będzie kontynuowane z usługi Stream Analytics [wykrywanie oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) samouczka. Jego oparty na utworzonej w tym samouczku przepływu pracy i dodaje dane wyjściowe, dzięki czemu można zwizualizować fałszywych połączeń telefonicznych, które są wykrywane przez zadanie analizy przesyłania strumieniowego usługi Power BI. 

Możesz obserwować [wideo](https://www.youtube.com/watch?v=SGUpT-a99MA) przedstawiający tego scenariusza.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że należy dysponować następującymi elementami:

* Konto platformy Azure.
* Konto usługi Power BI. Można użyć konta służbowego lub konta służbowego.
* Ukończoną wersję [wykrywanie oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) samouczka. Samouczek obejmuje aplikację, która generuje metadanych fikcyjne połączeń telefonicznych. W samouczku tworzenia Centrum zdarzeń i wysyłać dane przesyłane strumieniowo rozmowy telefonicznej do Centrum zdarzeń. Możesz napisać zapytanie wykrywające fałszywych wywołania (wywołania z tego samego numeru w tym samym czasie w różnych lokalizacjach). 


## <a name="add-power-bi-output"></a>Dodawanie danych wyjściowych usługi Power BI
W samouczku wykrywanie oszustw w czasie rzeczywistym dane wyjściowe są wysyłane do magazynu obiektów Blob platformy Azure. W tej sekcji możesz dodać dane wyjściowe, które wysyła informacje do usługi Power BI.

1. W portalu Azure Otwórz utworzoną wcześniej zadanie analizy przesyłania strumieniowego. Jeśli używasz sugerowana nazwa nosi nazwę zadania `sa_frauddetection_job_demo`.

2. Wybierz **dane wyjściowe** polu środku pulpitu nawigacyjnego zadania, a następnie wybierz **+ Dodaj**.

3. Aby uzyskać **Alias wyjściowy**, wprowadź `CallStream-PowerBI`. Można użyć innej nazwy. Jeśli to zrobisz, zanotuj, ponieważ później potrzebne. 

4. W obszarze **Sink**, wybierz pozycję **usługi Power BI**.

   ![Utworzenie danych wyjściowych dla usługi Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Kliknij przycisk **autoryzować**.

    Otwiera okno, w którym można podać poświadczenia platformy Azure dla konta firmowego lub szkolnego. 

    ![Podaj poświadczenia, aby uzyskać dostęp do usługi Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Wprowadź swoje poświadczenia. Należy pamiętać, a następnie wprowadź swoje poświadczenia, użytkownik jest również nadanie uprawnień do zadanie analizy przesyłania strumieniowego dostęp do Twojego obszaru usługi Power BI.

7. Gdy nastąpi powrót do **nowe dane wyjściowe** bloku, wprowadź następujące informacje:

    * **Obszar roboczy grupy**: Wybierz obszar roboczy w dzierżawie usługi Power BI, na którym chcesz utworzyć zestawu danych.
    * **Nazwa zestawu danych**: wprowadź `sa-dataset`. Można użyć innej nazwy. Jeśli to zrobisz, zanotuj jej na później.
    * **Nazwa tabeli**: wprowadź `fraudulent-calls`. Obecnie usługa Power BI dane wyjściowe zadania usługi analiza strumienia może mieć tylko jedną tabelę w zestawie danych.

    ![Obszar roboczy PBI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Usługa Power BI udostępnia zestaw danych i tabeli, które mają takie same nazwy, jak te, które określają w zadaniu Stream Analytics, istniejące zostaną zastąpione.
    > Zaleca się, że nie jawnie utworzysz ten zestaw danych i tabeli na koncie usługi Power BI. Są one tworzone automatycznie podczas uruchamiania zadania usługi analiza strumienia i zadanie uruchamia pompującego dane wyjściowe do usługi Power BI. Jeśli zadania kwerenda nie zwróciła żadnych wyników, zestaw danych i tabeli nie są tworzone.
    >

8. Kliknij przycisk **Utwórz**.

Zestaw danych jest tworzony z następującymi ustawieniami:

* **defaultRetentionPolicy: BasicFIFO**: dane są FIFO, używając maksymalnie 200 000 wierszy.
* **właściwości defaultMode: pushStreaming**: zestaw danych obsługuje Kafelki przesyłania strumieniowego i tradycyjnych elementy wizualne na podstawie raportu () Push).

Obecnie nie można utworzyć zestawy danych z innych flagi.

Aby uzyskać więcej informacji na temat zestawów danych usługi Power BI, zobacz [API REST usługi Power BI](https://msdn.microsoft.com/library/mt203562.aspx) odwołania.


## <a name="write-the-query"></a>Napisz zapytanie

1. Zamknij **dane wyjściowe** blok i wróć do bloku zadania.

2. Kliknij przycisk **zapytania** pole. 

3. Wpisz poniższe zapytanie. To zapytanie jest podobny do zapytania samosprzężenie utworzony w samouczku wykrywanie oszustw. Różnica polega na tym tego zapytania i wysyła wyniki do nowych danych wyjściowych utworzony (`CallStream-PowerBI`). 

    >[!NOTE]
    >Jeśli dane wejściowe nie nazwy `CallStream` w samouczku wykrywanie oszustw zastąpić nazwę `CallStream` w **FROM** i **JOIN** klauzule w zapytaniu.

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. Kliknij pozycję **Zapisz**.


## <a name="test-the-query"></a>Testowanie zapytania
Ta sekcja jest opcjonalna, ale zalecana. 

1. Jeśli aplikacja TelcoStreaming obecnie nie działa, należy ją uruchomić, wykonaj następujące czynności:

    * Otwórz okno poleceń.
    * Przejdź do folderu, gdzie są telcogenerator.exe i telcodatagen.exe.config zmodyfikowane pliki.
    * Uruchom następujące polecenie:

            telcodatagen.exe 1000 .2 2

2. W **zapytania** bloku, kliknij punkty `CallStream` danych wejściowych, a następnie wybierz **przykładowe dane z danych wejściowych**.

3. Określ wartość trzy minuty danych i kliknij przycisk **OK**. Poczekaj, aż zostanie wyświetlone powiadomienie, że dane uzyskane podczas próbkowania.

4. Kliknij przycisk **testu** i upewnij się, że otrzymywanie wyników.


## <a name="run-the-job"></a>Uruchom zadanie

1. Upewnij się, że aplikacja TelcoStreaming działa.

2. Zamknij **zapytania** bloku.

3. W bloku zadania kliknij **Start**.

    ![Uruchom zadanie usługi analiza strumienia](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Zadanie analizy przesyłania strumieniowego uruchamia wyszukiwanie fałszywych wywołań strumienia przychodzącego. To zadanie również tworzy zestaw danych i tabeli w usłudze Power BI i rozpoczyna wysyłanie danych dotyczących fałszywych odwołania do nich.


## <a name="create-the-dashboard-in-power-bi"></a>Utwórz pulpit nawigacyjny w usłudze Power BI

1. Przejdź do [witryny Powerbi.com](https://powerbi.com) i zaloguj się przy użyciu konta służbowego. Jeśli zapytanie zadania usługi analiza strumienia zapisuje wyniki, zobacz zestawu danych jest już utworzony:

    ![Zestawu danych przesyłania strumieniowego w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. W obszarze roboczym, kliknij przycisk  **+ &nbsp;Utwórz**.

    ![Przycisk Utwórz, w obszarze roboczym usługi Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Utwórz nowy pulpit nawigacyjny i nadaj mu nazwę `Fraudulent Calls`.

    ![Utwórz pulpit nawigacyjny i nadaj mu nazwę w obszarze roboczym usługi Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. W górnej części okna kliknij **kafelka Dodaj**, wybierz pozycję **danych przesyłania STRUMIENIOWEGO NIESTANDARDOWYCH**, a następnie kliknij przycisk **dalej**.

    ![Niestandardowe przesyłania strumieniowego zestawu danych](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. W obszarze **YOUR DATSETS**, wybierz zestaw danych, a następnie kliknij przycisk **dalej**.

    ![Zestaw danych przesyłania strumieniowego](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. W obszarze **typ wizualizacji**, wybierz pozycję **karty**, a następnie w **pola** listy, wybierz **fraudulentcalls**.

    ![Wizualizacja szczegółów dotyczących nowego kafelka](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Kliknij przycisk **Dalej**.

8. Wprowadź szczegóły kafelka, takie jak tytuł i pomocniczą.

    ![Tytuł i podtytuł dotyczących nowego kafelka](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kliknij przycisk **Zastosuj**.

    Teraz masz licznika oszustw.

    ![Licznik oszustwa](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Wykonaj kroki ponownie, aby dodać kafelka (począwszy od krok 4). Teraz, wykonaj następujące czynności:

    * Po przejściu do **typ wizualizacji**, wybierz pozycję **wykres liniowy**. 
    * Dodawanie osi i wybierz **windowend**. 
    * Dodaj wartość i wybrać **fraudulentcalls**.
    * Aby uzyskać **przedział czasu, aby wyświetlić**, wybierz ostatnich 10 minut.

    ![Utwórz Kafelek wykres liniowy](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kliknij przycisk **dalej**, Dodaj tytuł i podtytuł i kliknij przycisk **Zastosuj**.

    Pulpit nawigacyjny usługi Power BI teraz oferuje dwa widoki danych o wywołaniach fałszywych wykryciu dane przesyłane strumieniowo.

    ![Zakończono pulpit nawigacyjny usługi Power BI przedstawiający dwa Kafelki dla fałszywych wywołań](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Dowiedz się więcej o usłudze Power BI

Ten samouczek przedstawia sposób tworzenia tylko kilka rodzajów wizualizacji dla zestawu danych. Usługa Power BI może pomóc tworzyć inne narzędzia analizy biznesowej odbiorcy w Twojej organizacji. Aby uzyskać więcej informacji zobacz następujące zasoby:

* Na przykład innego pulpitu nawigacyjnego usługi Power BI Obejrzyj [wprowadzenie do korzystania z usługi Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) wideo.
* Aby uzyskać więcej informacji o konfigurowaniu analizy strumienia zadania dane wyjściowe do usługi Power BI i przy użyciu usługi Power BI grup, przejrzyj [usługi Power BI](stream-analytics-define-outputs.md#power-bi) sekcji [analiza strumienia danych wyjściowych](stream-analytics-define-outputs.md) artykułu. 
* Aby dowiedzieć się, jak przy użyciu usługi Power BI, ogólnie rzecz biorąc, zobacz [pulpitów nawigacyjnych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Dowiedz się więcej o ograniczeniach oraz najlepsze rozwiązania
Obecnie usługa Power BI można wywołać około raz na sekundę. Elementy wizualne przesyłania strumieniowego obsługuje pakietów 15 KB. Ponadto niepowodzeniem przesyłania strumieniowego elementy wizualne (ale wypychania w dalszym ciągu działać). Ze względu na ograniczenia te usługi Power BI pozwalają najbardziej naturalnie do przypadków, w którym usługi Azure Stream Analytics jest zmniejszenie obciążenia dużej ilości danych. Zalecamy używanie okno wirowania lub okna Hopping zapewnienie wypychanie danych jest co najwyżej jeden wypychania na sekundę i że kwerenda wyładowuje w wymagania dotyczące przepływności.

Następujące równanie służy do obliczenia wartości umożliwiają okna w sekundach:

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Na przykład:

* Masz 1000 urządzeń wysyłanie danych na sekundę.
* Używasz Power BI Pro jednostki SKU obsługującego 1 000 000 wierszy na godzinę.
* Chcesz opublikować ilość uśrednianie danych na urządzeniu do usługi Power BI.

W związku z tym staje się równania:

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

W takiej konfiguracji można zmienić oryginalne zapytanie do następującego:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Odnów autoryzację
Jeśli hasło uległ zmianie od czasu utworzenia lub ostatniej uwierzytelniony zadania, należy ponownie uwierzytelniać konta usługi Power BI. Jeśli uwierzytelnianie wieloskładnikowe Azure został skonfigurowany w dzierżawie usługi Azure Active Directory (Azure AD), należy odnowić autoryzacji usługi Power BI co dwa tygodnie. Jeśli nie odnowisz, można zauważyć objawy, takich jak brakujące dane wyjściowe zadania lub `Authenticate user error` w dzienników operacji.

Podobnie jeśli zadanie rozpoczyna się po wygaśnięciu tokenu, występuje błąd, a zadanie nie powiedzie się. Aby rozwiązać ten problem, zatrzymać zadanie, które jest uruchomiona i przejdź do usługi Power BI dane wyjściowe. Aby uniknąć utraty danych, wybierz **odnowić autoryzacji** połączyć, a następnie uruchom ponownie zadanie z **czas ostatniego zatrzymania**.

Po odświeżeniu autoryzację przy użyciu usługi Power BI, zielony alert zostanie wyświetlony w obszarze autoryzacji, aby uwzględnić, czy problem został rozwiązany.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Odwołania API REST zarządzania usługi analiza strumienia Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
