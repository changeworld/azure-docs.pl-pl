---
title: Integracja pulpitu nawigacyjnego usługi Power BI z usługą Azure Stream Analytics
description: W tym artykule opisano, jak używać pulpitu nawigacyjnego usługi Power BI w czasie rzeczywistym do wizualizacji danych z zadania usługi Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: 8466fbcb4325dc244551a3b84fc20581366b7071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851151"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Usługa Stream Analytics i Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym do przesyłania strumieniowego danych

Usługa Azure Stream Analytics umożliwia korzystanie z jednego z wiodących narzędzi analizy biznesowej, [Microsoft Power BI.](https://powerbi.com/) W tym artykule dowiesz się, jak tworzyć narzędzia analizy biznesowej przy użyciu usługi Power BI jako dane wyjściowe dla zadań usługi Azure Stream Analytics. Dowiesz się również, jak tworzyć pulpit nawigacyjny w czasie rzeczywistym i korzystać z niego.

Ten artykuł jest kontynuowany z samouczka [wykrywania oszustw](stream-analytics-real-time-fraud-detection.md) w czasie rzeczywistym usługi Stream Analytics. Opiera się na przepływie pracy utworzonym w tym samouczku i dodaje dane wyjściowe usługi Power BI, dzięki czemu można wizualizować fałszywe połączenia telefoniczne, które są wykrywane przez zadanie usługi Analytics przesyłania strumieniowego. 

Możesz obejrzeć [film,](https://www.youtube.com/watch?v=SGUpT-a99MA) który ilustruje ten scenariusz.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Konto platformy Azure.
* Konto usługi Power BI Pro. Możesz użyć konta służbowego lub konta szkolnego.
* Ukończona wersja samouczka wykrywania oszustw w [czasie rzeczywistym.](stream-analytics-real-time-fraud-detection.md) Samouczek zawiera aplikację, która generuje fikcyjne metadane połączeń telefonicznych. W samouczku utworzysz centrum zdarzeń i wyślesz dane połączeń telefonicznych przesyłania strumieniowego do centrum zdarzeń. Piszesz kwerendę, która wykrywa fałszywe połączenia (połączenia z tego samego numeru w tym samym czasie w różnych lokalizacjach). 


## <a name="add-power-bi-output"></a>Dodawanie danych wyjściowych usługi Power BI
W samouczku wykrywania oszustw w czasie rzeczywistym dane wyjściowe są wysyłane do magazynu obiektów Blob platformy Azure. W tej sekcji należy dodać dane wyjściowe, które wysyła informacje do usługi Power BI.

1. W witrynie Azure portal otwórz zadanie usługi Analizy przesyłania strumieniowego utworzone wcześniej. Jeśli użyto sugerowanej nazwy, zadanie `sa_frauddetection_job_demo`nosi nazwę .

2. W menu po lewej stronie wybierz pozycję **Dane wyjściowe** w obszarze **Topologia zadań**. Następnie wybierz **polecenie + Dodaj** i wybierz pozycję Power **BI** z menu rozwijanego.

3. Wybierz **+ Dodaj** > **power bi**. Następnie wypełnij formularz przy użyciu poniższych wartości i wybierz pozycję **Autoryzuj**:

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|
   |Alias danych wyjściowych  |  CallStream-PowerBI  |
   |Nazwa zestawu danych  |   zestaw danych sa  |
   |Nazwa tabeli |  oszukańcze połączenia  |

   ![Konfigurowanie danych wyjściowych usługi Stream Analytics](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Jeśli usługa Power BI ma zestaw danych i tabelę, które mają takie same nazwy jak te, które określisz w zadaniu usługi Stream Analytics, istniejące są zastępowane.
   > Zaleca się, aby nie tworzyć jawnie tego zestawu danych i tabeli na koncie usługi Power BI. Są one tworzone automatycznie po uruchomieniu zadania usługi Stream Analytics, a zadanie rozpocznie pompowanie danych wyjściowych do usługi Power BI. Jeśli kwerenda zadania nie zwraca żadnych wyników, zestaw danych i tabela nie są tworzone.
   >

4. Po wybraniu pozycji **Autoryzuj** zostanie otwarte okno podręczne i zostanie wyświetlona prośba o podanie poświadczeń w celu uwierzytelnienia na koncie usługi Power BI. Kiedy autoryzacja zakończy się pomyślnie, **zapisz** ustawienia.

8. Kliknij przycisk **Utwórz**.

Zestaw danych jest tworzony z następującymi ustawieniami:

* **defaultRetentionPolicy: BasicFIFO** - Dane to FIFO, z maksymalnie 200 000 wierszy.
* **defaultMode: pushStreaming** — zestaw danych obsługuje zarówno kafelki przesyłania strumieniowego, jak i tradycyjne wizualizacje oparte na raporcie (znane również jako wypychanie).

Obecnie nie można tworzyć zestawów danych z innymi flagami.

Aby uzyskać więcej informacji na temat zestawów danych usługi Power BI, zobacz odwołanie do [interfejsu API REST usługi Power BI.](https://msdn.microsoft.com/library/mt203562.aspx)


## <a name="write-the-query"></a>Napisz zapytanie

1. Zamknij **wyjścia** bloku i powrócić do bloku zadania.

2. Kliknij pole **Kwerenda.** 

3. Wprowadź następującą kwerendę. Ta kwerenda jest podobna do kwerendy samoskładnia utworzonej w samouczku wykrywania oszustw. Różnica polega na tym, że ta kwerenda`CallStream-PowerBI`wysyła wyniki do nowego utworzonego wyjścia ( ). 

    >[!NOTE]
    >Jeśli nie nazwa danych `CallStream` wejściowych w samouczku wykrywania `CallStream` oszustw, zastąp swoją nazwę w **klauzulach FROM** i **JOIN** w kwerendzie.

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

4. Kliknij przycisk **Zapisz**.


## <a name="test-the-query"></a>Testowanie zapytania

Ta sekcja jest opcjonalna, ale zalecana. 

1. Jeśli aplikacja TelcoStreaming nie jest aktualnie uruchomiona, uruchom ją, wykonując następujące czynności:

    * Otwórz wiersz polecenia.
    * Przejdź do folderu, w którym znajdują się pliki telcogenerator.exe i zmodyfikowane pliki telcodatagen.exe.config.
    * Uruchom następujące polecenie:

       `telcodatagen.exe 1000 .2 2`

2. Na stronie **Zapytanie** dla zadania usługi Stream Analytics `CallStream` kliknij kropki obok danych wejściowych, a następnie wybierz **pozycję Przykładowe dane z danych wejściowych**.

3. Określ, czy chcesz dane warte trzy minuty i kliknij **przycisk OK**. Poczekaj, aż otrzymasz powiadomienie, że próbka danych została przygotowana.

4. Kliknij **przycisk Testuj** i przejrzyj wyniki.

## <a name="run-the-job"></a>Uruchamianie zadania

1. Upewnij się, że aplikacja TelcoStreaming jest uruchomiona.

2. Przejdź do strony **Przegląd** zadania usługi Stream Analytics i wybierz pozycję **Start**.

    ![Uruchamianie zadania usługi Stream Analytics](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Zadanie Analizy przesyłania strumieniowego zaczyna wyszukywać fałszywe połączenia w strumieniu przychodzącym. Zadanie tworzy również zestaw danych i tabelę w usłudze Power BI i rozpoczyna wysyłanie danych o fałszywych połączeniach do nich.


## <a name="create-the-dashboard-in-power-bi"></a>Tworzenie pulpitu nawigacyjnego w usłudze Power BI

1. Przejdź do [Powerbi.com](https://powerbi.com) i zaloguj się za pomocą konta służbowego. Jeśli kwerenda zadania usługi Stream Analytics wyprowadza wyniki, zobaczysz, że zestaw danych jest już utworzony:

    ![Przesyłanie strumieniowe lokalizacji zestawu danych w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. W obszarze roboczym kliknij pozycję ** + &nbsp;Utwórz**.

    ![Przycisk Utwórz w obszarze roboczym usługi Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Utwórz nowy pulpit `Fraudulent Calls`nawigacyjny i nadaj jego nazwę .

    ![Tworzenie pulpitu nawigacyjnego i nadawanie mu nazwy w obszarze roboczym usługi Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. W górnej części okna kliknij pozycję **Dodaj kafelek**, wybierz pozycję **NIESTANDARDOWE DANE PRZESYŁANIA STRUMIENIOWEGO**, a następnie kliknij przycisk **Dalej**.

    ![Kafelek niestandardowego zestawu danych przesyłania strumieniowego w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. W **obszarze DATSETS**wybierz swój zestaw danych, a następnie kliknij przycisk **Dalej**.

    ![Zestaw danych przesyłania strumieniowego w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. W obszarze **Typ wizualizacji**wybierz pozycję **Karta**, a następnie na liście **Pola** wybierz pozycję **fałszywe zawołania**.

    ![Szczegóły wizualizacji nowego kafelka](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Kliknij przycisk **alej**.

8. Wypełnij szczegóły kafelków, takie jak tytuł i podtytuł.

    ![Tytuł i podtytuł dla nowego kafelka](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kliknij przycisk **Zastosuj**.

    Teraz masz licznik oszustw!

    ![Licznik oszustw na pulpicie nawigacyjnym usługi Power BI](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Wykonaj ponownie kroki, aby dodać kafelek (zaczynając od kroku 4). Tym razem wykonaj następujące czynności:

    * Po dojściu do **wizualizacji typ**wybierz **opcję Wykres liniowy**. 
    * Dodaj oś i wybierz pozycję **windowend**. 
    * Dodaj wartość i wybierz pozycję **fraudulentcalls**.
    * W ustawieniu **Okno czasowe do wyświetlenia** wybierz ostatnie 10 minut.

      ![Tworzenie kafelka dla wykresu liniowego w usłudze Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kliknij **przycisk Dalej**, dodaj tytuł i napisy, a następnie kliknij przycisk **Zastosuj**.

     Pulpit nawigacyjny usługi Power BI udostępnia teraz dwa widoki danych o fałszywych połączeniach wykrytych w danych przesyłania strumieniowego.

     ![Gotowy pulpit nawigacyjny usługi Power BI z dwoma kafelkami dla fałszywych połączeń](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Dowiedz się więcej o usłudze Power BI

W tym samouczku pokazano, jak utworzyć tylko kilka rodzajów wizualizacji dla zestawu danych. Usługa Power BI może pomóc w tworzeniu innych narzędzi analizy biznesowej klientów dla twojej organizacji. Aby uzyskać więcej pomysłów, zobacz następujące zasoby:

* Inny przykład pulpitu nawigacyjnego usługi Power BI można obejrzeć [klip wideo wprowadzenie do usługi Power BI.](https://youtu.be/L-Z_6P56aas?t=1m58s)
* Aby uzyskać więcej informacji na temat konfigurowania danych wyjściowych usługi Power BI i korzystania z grup usługi Power BI, zapoznaj się z sekcją [Usługi Power BI](stream-analytics-define-outputs.md#power-bi) w [artykule dane wyjściowe usługi Stream Analytics.](stream-analytics-define-outputs.md) 
* Aby uzyskać informacje dotyczące ogólnie korzystania z usługi Power BI, zobacz [Pulpity nawigacyjne w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Dowiedz się więcej o ograniczeniach i najlepszych praktykach
Obecnie usługa Power BI może być wywoływana mniej więcej raz na sekundę. Przesyłanie strumieniowe wizualizacji obsługuje pakiety o rozmiarze 15 KB. Poza tym, przesyłanie strumieniowe wizualizacje nie (ale push nadal działa). Z powodu tych ograniczeń usługa Power BI nadaje się najbardziej naturalnie do przypadków, w których usługa Azure Stream Analytics znacznie zmniejsza obciążenie danych. Zaleca się użycie okna Tumbling lub Hopping okna, aby upewnić się, że wypychanie danych jest co najwyżej jeden wypychanie na sekundę i że zapytanie ląduje w ramach wymagań przepływności.

Do obliczenia wartości w ciągu kilku sekund można użyć następującego równania:

![Równanie do obliczania wartości, aby dać okno w sekundach](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Przykład:

* Masz 1000 urządzeń wysyłających dane w odstępach jednosekundowych.
* Używasz jednostki SKU usługi Power BI Pro, która obsługuje 1 000 000 wierszy na godzinę.
* Chcesz opublikować w usłudze Power BI ilość średnich danych na urządzenie.

W rezultacie równanie staje się:

![Równanie oparte na przykładowych kryteriach](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Biorąc pod uwagę tę konfigurację, można zmienić oryginalną kwerendę na następującą:

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

### <a name="renew-authorization"></a>Odnawianie autoryzacji
Jeśli hasło uległo zmianie od czasu utworzenia lub ostatnio uwierzytelnionego zadania, należy ponownie uwierzytelnić konto usługi Power BI. Jeśli uwierzytelnianie wieloskładnikowe platformy Azure jest skonfigurowane w dzierżawie usługi Azure Active Directory (Azure AD), należy również odnawiać autoryzację usługi Power BI co dwa tygodnie. Jeśli nie odnowisz, mogą być widoczne symptomy, takie jak `Authenticate user error` brak danych wyjściowych zadania lub w dziennikach operacji.

Podobnie jeśli zadanie rozpoczyna się po wygaśnięciu tokenu, występuje błąd i zadanie kończy się niepowodzeniem. Aby rozwiązać ten problem, zatrzymaj uruchomione zadanie i przejdź do danych wyjściowych usługi Power BI. Aby uniknąć utraty danych, wybierz łącze **Odnów autoryzację,** a następnie uruchom ponownie zadanie z **pozycji Czas ostatniego zatrzymania**.

Po odświeżeniu autoryzacji za pomocą usługi Power BI w obszarze autoryzacji pojawi się zielony alert, aby odzwierciedlić, że problem został rozwiązany.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Odwołanie do języka kwerendy usługi Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odwołanie interfejsu API REST usługi Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
