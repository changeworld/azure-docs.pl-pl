---
title: Integracja pulpitu nawigacyjnego usługi Power BI z usługą Azure Stream Analytics
description: W tym artykule opisano, jak wizualizować dane z zadania usługi Azure Stream Analytics przy użyciu pulpitu nawigacyjnego usługi Power BI w czasie rzeczywistym.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 487c142400dc2bfa6f44e17963535051af017196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818021"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Samouczek: Stream Analytics i usługa Power BI: Pulpit nawigacyjny analizy w czasie rzeczywistym dla danych przesyłanych strumieniowo
Usługa Azure Stream Analytics umożliwia korzystanie z zalet jednej z wiodących narzędzi analizy biznesowej, [Microsoft Power BI](https://powerbi.com/). W tym artykule dowiesz się, jak utworzyć narzędzia do analizy biznesowej za pomocą usługi Power BI jako dane wyjściowe dla zadań usługi Azure Stream Analytics. Poznasz również sposób tworzenia i używania pulpitu nawigacyjnego w czasie rzeczywistym.

W tym artykule jest kontynuowane od usługi Stream Analytics [wykrywanie oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) samouczka. Do niej opiera się na przepływ pracy utworzone w tym samouczku, usługa Power BI, dane wyjściowe, dzięki czemu możesz wizualizować fałszywych połączeń telefonicznych, które są wykrywane przez zadanie usługi Stream Analytics. 

Możesz obejrzeć [wideo](https://www.youtube.com/watch?v=SGUpT-a99MA) który ilustruje ten scenariusz.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Konto platformy Azure.
* Konto usługi dla usługi Power BI. Można użyć konta służbowego lub konta służbowego.
* Pełną wersję [wykrywanie oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) samouczka. Samouczek obejmuje aplikację, która generuje metadanych fikcyjne połączeń telefonicznych. W tym samouczku Tworzenie Centrum zdarzeń i Wyślij dane przesyłane strumieniowo połączeń telefonicznych do Centrum zdarzeń. Można napisać zapytanie, które wykrywa fałszywe wywołania (wywołań z tego samego numeru, w tym samym czasie w różnych lokalizacjach). 


## <a name="add-power-bi-output"></a>Dodaj dane wyjściowe usługi Power BI
W tym samouczku wykrywanie oszustw w czasie rzeczywistym dane wyjściowe są wysyłane do usługi Azure Blob storage. W tej sekcji możesz dodać dane wyjściowe, które wysyła informacje do usługi Power BI.

1. W witrynie Azure portal Otwórz zadanie usługi Stream Analytics, które została utworzona wcześniej. Jeśli używasz sugerowanej nazwy, zadanie o nazwie `sa_frauddetection_job_demo`.

2. Wybierz **dane wyjściowe** polu w środku pulpitu nawigacyjnego zadań, a następnie wybierz pozycję **+ Dodaj**.

3. Aby uzyskać **Alias wyjściowy**, wprowadź `CallStream-PowerBI`. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj je, ponieważ nazwa będą potrzebne później. 

4. W obszarze **ujścia**, wybierz opcję **usługi Power BI**.

   ![Tworzenie danych wyjściowych dla usługi Power BI](./media/stream-analytics-power-bi-dashboard/create-power-bi-ouptut.png)

5. Kliknij przycisk **autoryzować**.

    Zostanie otwarte okno, którym można podać swoje poświadczenia platformy Azure dla konta firmowego lub szkolnego. 

    ![Wprowadź poświadczenia dla dostępu do usługi Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-authorization-credentials.png)

6. Wprowadź swoje poświadczenia. Należy pamiętać, a następnie po wprowadzeniu poświadczeń, również przygotowujesz uprawnienia do zadania usługi Stream Analytics do dostępu do obszaru usługi Power BI.

7. Gdy następuje powrót do **nowe dane wyjściowe** bloku, wprowadź następujące informacje:

   * **Obszar roboczy grupy**: Wybierz obszar roboczy w dzierżawie usługi Power BI, w której chcesz utworzyć zestaw danych.
   * **Nazwa zestawu danych**:  Wprowadź polecenie `sa-dataset`. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj go na później.
   * **Nazwa tabeli**: Wprowadź polecenie `fraudulent-calls`. Obecnie usługa Power BI dane wyjściowe zadania usługi Stream Analytics może mieć tylko jedną tabelę w zestawie danych.

     ![Zestaw danych do programu Power BI obszaru roboczego i tabeli](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

     > [!WARNING]
     > Jeśli usługa Power BI zawiera zestaw danych i tabelę, która ma takie same nazwy te, które określisz w zadaniu Stream Analytics, istniejące zostaną zastąpione.
     > Zaleca się, że nie jawnie utworzysz ten zestaw danych i tabeli na koncie usługi Power BI. Są one tworzone automatycznie podczas uruchamiania zadania usługi Stream Analytics, a zadanie zostanie uruchomione przekazujący dane wyjściowe do usługi Power BI. Jeśli zapytanie dotyczące zadań nie zwraca żadnych wyników, zestaw danych i tabeli nie są tworzone.
     >

8. Kliknij pozycję **Utwórz**.

Zestaw danych jest tworzony z następującymi ustawieniami:

* **defaultRetentionPolicy: BasicFIFO**: Dane są FIFO, z maksymalnie 200 000 wierszy.
* **defaultMode: pushStreaming**: Zestaw danych obsługuje zarówno Kafelki przesyłania strumieniowego i tradycyjne wizualizacje na podstawie raportu (zwane) Wypychanie).

Obecnie nie można utworzyć zestawy danych przy użyciu innych flag.

Aby uzyskać więcej informacji na temat zestawów danych usługi Power BI, zobacz [interfejsu API REST usługi Power BI](https://msdn.microsoft.com/library/mt203562.aspx) odwołania.


## <a name="write-the-query"></a>Napisać zapytanie

1. Zamknij **dane wyjściowe** blok i wróć do bloku zadania.

2. Kliknij przycisk **zapytania** pole. 

3. Wprowadź następujące zapytanie. To zapytanie jest podobne do zapytania samosprzężenie utworzonych w tym samouczku wykrywanie oszustw. Różnica polega na tym tego zapytania i wysyła wyniki do nowe dane wyjściowe, utworzony (`CallStream-PowerBI`). 

    >[!NOTE]
    >Jeśli dane wejściowe, nie nazwy `CallStream` w tym samouczku wykrywanie oszustw, zastąp nazwę `CallStream` w **FROM** i **Dołącz** klauzule w zapytaniu.

        ```SQL
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
        ```

4. Kliknij pozycję **Zapisz**.


## <a name="test-the-query"></a>Testovat dotaz
Ta sekcja jest opcjonalna, ale zalecana. 

1. Jeżeli aplikacja TelcoStreaming nie jest obecnie uruchomiona, należy ją uruchomić, wykonując następujące czynności:

    * Otwórz okno polecenia.
    * Przejdź do folderu, w których telcogenerator.exe i telcodatagen.exe.config zmodyfikowanych plików.
    * Uruchom następujące polecenie:

       `telcodatagen.exe 1000 .2 2`

2. W **zapytania** bloku kliknij kropki obok `CallStream` dane wejściowe, a następnie wybierz pozycję **przykładowe dane z danych wejściowych**.

3. Określ, czy program przechowuje trzy minuty danych i kliknij przycisk **OK**. Poczekaj, aż otrzymasz powiadomienie, że próbka danych została przygotowana.

4. Kliknij przycisk **Testuj** i upewnij się, że wyświetlane są wyniki.


## <a name="run-the-job"></a>Uruchamianie zadania

1. Upewnij się, że aplikacja TelcoStreaming jest uruchomiona.

2. Zamknij **zapytania** bloku.

3. W bloku zadania kliknij **Start**.

    ![Uruchamianie zadania usługi Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Zadanie usługi Stream Analytics uruchamia wyszukiwanie fałszywych połączeń w strumienia przychodzącego. Zadanie również tworzy zestaw danych i tabeli w usłudze Power BI i rozpoczyna wysyłanie danych dotyczących fałszywych połączeń do nich.


## <a name="create-the-dashboard-in-power-bi"></a>Tworzenie pulpitu nawigacyjnego w usłudze Power BI

1. Przejdź do [Powerbi.com](https://powerbi.com) i zaloguj się przy użyciu swojego konta firmowego lub szkolnego. Jeśli zapytanie zadania usługi Stream Analytics dane wyjściowe, zostanie wyświetlony zestaw danych jest już utworzony:

    ![Przesyłanie strumieniowe lokalizacji zestawu danych w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. W obszarze roboczym, kliknij przycisk  **+ &nbsp;Utwórz**.

    ![Przycisk Utwórz, w obszarze roboczym usługi Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Utwórz nowy pulpit nawigacyjny i nadaj mu `Fraudulent Calls`.

    ![Utwórz pulpit nawigacyjny i nadaj jej nazwę w obszarze roboczym usługi Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. W górnej części okna kliknij **Dodaj Kafelek**, wybierz opcję **niestandardowe dane przesyłane STRUMIENIOWO**, a następnie kliknij przycisk **dalej**.

    ![Niestandardowe przesyłania strumieniowego kafelków zestawu danych w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. W obszarze **YOUR DATSETS**, wybierz zestaw danych, a następnie kliknij przycisk **dalej**.

    ![Zestaw danych przesyłania strumieniowego w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. W obszarze **typ wizualizacji**, wybierz opcję **karty**, a następnie w polu **pola** listy wybierz **fraudulentcalls**.

    ![Wizualizacja szczegółów dotyczących nowego kafelka](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Kliknij przycisk **Dalej**.

8. Wypełnij szczegóły kafelka, takie jak tytuł i podtytuł.

    ![Tytuł i podtytuł dotyczących nowego kafelka](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kliknij przycisk **Zastosuj**.

    Teraz masz licznika oszustwa.

    ![Licznik oszustwa na pulpicie nawigacyjnym usługi Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Wykonaj kroki ponownie, aby dodać tabliczkę (począwszy od kroku 4). Tym razem, wykonaj następujące czynności:

    * Po przejściu do **typ wizualizacji**, wybierz opcję **wykres liniowy**. 
    * Dodaj oś i wybierz pozycję **windowend**. 
    * Dodaj wartość i wybierz pozycję **fraudulentcalls**.
    * W ustawieniu **Okno czasowe do wyświetlenia** wybierz ostatnie 10 minut.

      ![Tworzenie kafelka wykresu liniowego w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kliknij przycisk **dalej**, Dodaj tytuł i podtytuł, a kliknij **Zastosuj**.

     Pulpit nawigacyjny usługi Power BI teraz oferuje dwa widoki danych o fałszywych połączeń wykryciu danych przesyłanych strumieniowo.

     ![Zakończono pulpitu nawigacyjnego usługi Power BI, przedstawiający dwa Kafelki dla fałszywych połączeń](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Dowiedz się więcej o usłudze Power BI

Ten samouczek przedstawia sposób tworzenia tylko kilka rodzajów wizualizacji dla zestawu danych. Usługa Power BI może pomóc utworzyć innymi narzędziami analizy biznesowej klientów dla Twojej organizacji. Więcej pomysłów na ten temat można znaleźć w następujących zasobach:

* Inny przykład pulpitu nawigacyjnego usługi Power BI, obejrzyj [wprowadzenie do usługi Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) wideo.
* Aby uzyskać więcej informacji o konfigurowaniu usługi Stream Analytics zadanie danych wyjściowych do usługi Power BI i przy użyciu grup usługi Power BI, przejrzyj [usługi Power BI](stream-analytics-define-outputs.md#power-bi) części [danych wyjściowych usługi Stream Analytics](stream-analytics-define-outputs.md) artykułu. 
* Aby dowiedzieć się, jak zwykle przy użyciu usługi Power BI, zobacz [pulpitów nawigacyjnych w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Dowiedz się więcej o ograniczeniach i najlepszych rozwiązaniach
Obecnie usługa Power BI może być wywoływana około raz na sekundę. Wizualizacje przesyłane strumieniowo obsługuje pakiety 15 KB. Poza tym wizualizacje przesyłane strumieniowo się nie powieść (ale wypychania w dalszym ciągu działać). Ze względu na ograniczenia te usługi Power BI jest przydatna w najbardziej naturalnie przypadki, w którym usługi Azure Stream Analytics jest zmniejszenie obciążenia duża ilość danych. Zalecamy używanie okna wirowania lub Hopping, aby upewnić się, wypychanie danych jest co najwyżej jeden wypychanych na sekundę i czy zapytania umieszczać swoje dokumenty w ramach wymagań w zakresie przepływności.

Można użyć poniższego równania, aby obliczyć wartość do nadania okna w ciągu kilku sekund:

![Równania, aby obliczyć wartość do nadania okna w ciągu kilku sekund](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Na przykład:

* Masz 1000 urządzeń, które wysyłają dane w jednosekundowych odstępach czasu.
* Używasz usługi Power BI Pro jednostkę SKU obsługującą 1 000 000 wierszy na godzinę.
* Chcesz opublikować ilość uśrednianie danych na urządzenie do usługi Power BI.

Co w efekcie staje się równania:

![Na podstawie kryteriów przykład równania](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

W takiej konfiguracji można zmienić oryginalne zapytanie do następujących:

```SQL
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
```

### <a name="renew-authorization"></a>Odnów autoryzację
Jeśli hasło uległ zmianie od czasu utworzenia lub ostatniego uwierzytelnienia zadania, musisz ponownie uwierzytelniać konta usługi Power BI. Jeśli usługi Azure Multi-Factor Authentication jest skonfigurowany w dzierżawie usługi Azure Active Directory (Azure AD), należy odnowić autoryzację usługi Power BI, co dwa tygodnie. Jeśli nie zostanie odnowiony, można uzyskać objawy, takich jak brakujące dane wyjściowe zadania lub `Authenticate user error` w dziennikach operacji.

Podobnie jeśli zadanie uruchamia się po wygaśnięciu tokenu, wystąpi błąd, i zadanie zakończy się niepowodzeniem. Aby rozwiązać ten problem, Zatrzymaj zadanie, które jest uruchomiona i przejdź do danych wyjściowych usługi Power BI. Aby uniknąć utraty danych, wybierz pozycję **Odnów autoryzację** połączyć, a następnie uruchom ponownie zadanie z **ostatniego zatrzymana**.

Po autoryzacji zostały odświeżone przy użyciu usługi Power BI, zielony alert pojawia się w obszarze zezwolenia, aby odzwierciedlić, że problem został rozwiązany.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań w usłudze Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Dokumentacja usługi Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
