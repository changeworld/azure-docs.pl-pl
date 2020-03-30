---
title: Eksportowanie do usługi Power BI z usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Zapytania analityczne mogą być wyświetlane w usłudze Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e17ca6e07ec76f0a7a1cb04f7aa13619fb9970c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664001"
---
# <a name="feed-power-bi-from-application-insights"></a>Źródło usługi Power BI z usługi Application Insights
[Usługa Power BI](https://www.powerbi.com/) to zestaw narzędzi biznesowych, które pomagają analizować dane i udostępniać szczegółowe informacje. Zaawansowane pulpity nawigacyjne są dostępne na każdym urządzeniu. Można łączyć dane z wielu źródeł, w tym zapytania Analytics z [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Istnieją trzy metody eksportowania danych usługi Application Insights do usługi Power BI:

* [**Zapytania analizy eksportu**](#export-analytics-queries). Jest to preferowana metoda. Zapisz dowolną kwerendę i wyeksportuj je do usługi Power BI. Możesz umieścić tę kwerendę na pulpicie nawigacyjnym wraz z innymi danymi.
* [**Ciągły eksport i usługa Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Ta metoda jest przydatna, jeśli chcesz przechowywać dane przez długi czas. Jeśli nie masz wymagania dotyczące przechowywania danych rozszerzonego, użyj metody zapytania analizy eksportu. Ciągły eksport i usługa Stream Analytics wymaga więcej pracy nad konfiguracją i dodatkowego obciążenia magazynem.
* **Zasilacz BI**. Zestaw wykresów jest wstępnie zdefiniowany, ale można dodać własne zapytania z innych źródeł.

> [!NOTE]
> Karta power bi jest teraz **przestarzała**. Wstępnie zdefiniowane wykresy dla tego rozwiązania są wypełniane statycznymi kwerendami nieedytalnymi. Nie masz możliwości edytowania tych zapytań i w zależności od niektórych właściwości danych możliwe jest pomyślne połączenie z usługą Power BI, ale żadne dane nie są wypełniane. Jest to spowodowane kryteria wykluczenia, które są ustawione w ramach kwerendy hardcode. Chociaż to rozwiązanie może nadal działać dla niektórych klientów, ze względu na brak elastyczności karty zalecane rozwiązanie jest użycie funkcji [**zapytań analizy eksportu.**](#export-analytics-queries)

## <a name="export-analytics-queries"></a>Zapytania usługi Export Analytics
Ta trasa umożliwia zapisanie dowolnej kwerendy Analytics lub wyeksportowanie z ścieżek użycia, a następnie wyeksportowanie jej do pulpitu nawigacyjnego usługi Power BI. (Można dodać do pulpitu nawigacyjnego utworzonego przez kartę).

### <a name="one-time-install-power-bi-desktop"></a>Jednorazowo: instalowanie programu Power BI Desktop
Aby zaimportować zapytanie usługi Application Insights, należy użyć klasycznej wersji usługi Power BI. Następnie można opublikować go w sieci Web lub w obszarze roboczym usługi Power BI w chmurze. 

Instalowanie [programu Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Eksportowanie zapytania Analytics
1. [Otwórz Analytics i napisz zapytanie](../../azure-monitor/log-query/get-started-portal.md).
2. Przetestuj i zawęzić kwerendę, dopóki nie będziesz zadowolony z wyników. Upewnij się, że kwerenda działa poprawnie w Analytics przed jej wyeksportować.
3. W menu **Eksport** wybierz polecenie **Power BI (M)**. Zapisz plik tekstowy.
   
    ![Zrzut ekranu przedstawiający analizę z wyróżnionym menu Eksportuj](./media/export-power-bi/analytics-export-power-bi.png)
4. W programie Power BI Desktop wybierz pozycję Pobierz**puste zapytanie o** **dane** > . Następnie w edytorze zapytań w obszarze **Widok**wybierz pozycję **Edytor zaawansowany**.

    Wklej wyeksportowany skrypt języka M do Edytora zaawansowanego.

    ![Zrzut ekranu przedstawiający program Power BI Desktop z wyróżnionym edytorem zaawansowanym](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Aby zezwolić usłudze Power BI na dostęp do platformy Azure, może być wymagane podanie poświadczeń. Zaloguj się za pomocą konta Microsoft za pomocą **konta instytucji.**
   
    ![Zrzut ekranu przedstawiający okno dialogowe Ustawienia kwerendy usługi Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Jeśli chcesz zweryfikować poświadczenia, użyj polecenia menu **Ustawienia źródła danych** w edytorze zapytań. Należy określić poświadczenia używane na platformie Azure, które mogą różnić się od poświadczeń dla usługi Power BI.
6. Wybierz wizualizację kwerendy i wybierz pola dla wymiaru osi x, osi y i segmentacji.
   
    ![Zrzut ekranu przedstawiający opcje wizualizacji programu Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publikowanie raportu w obszarze roboczym usługi Power BI w chmurze. Stamtąd można osadzić zsynchronizowana wersja na innych stronach internetowych.
   
    ![Zrzut ekranu przedstawiający program Power BI Desktop z wyróżnionym przyciskiem Publikowania](./media/export-power-bi/publish-power-bi.png)
8. Odśwież raport ręcznie w odstępach czasu lub skonfiguruj zaplanowane odświeżanie na stronie opcji.

### <a name="export-a-funnel"></a>Eksportowanie lejka
1. [Zrób swój lejek](../../azure-monitor/app/usage-funnels.md).
2. Wybierz pozycję **Power BI**.

   ![Zrzut ekranu przedstawiający przycisk usługi Power BI](./media/export-power-bi/button.png)

3. W programie Power BI Desktop wybierz pozycję Pobierz**puste zapytanie o** **dane** > . Następnie w edytorze zapytań w obszarze **Widok**wybierz pozycję **Edytor zaawansowany**.

   ![Zrzut ekranu przedstawiający pulpit usługi Power BI z wyróżnionym przyciskiem Puste zapytanie](./media/export-power-bi/blankquery.png)

   Wklej wyeksportowany skrypt języka M do Edytora zaawansowanego. 

   ![Zrzut ekranu przedstawiający program Power BI Desktop z wyróżnionym edytorem zaawansowanym](./media/export-power-bi/advancedquery.png)

4. Wybierz elementy z kwerendy i wybierz wizualizację ścieżki.

   ![Zrzut ekranu przedstawiający opcje wizualizacji programu Power BI Desktop](./media/export-power-bi/selectsequence.png)

5. Zmień tytuł, aby miał znaczenie, i opublikuj raport w obszarze roboczym usługi Power BI w chmurze. 

   ![Zrzut ekranu przedstawiający program Power BI Desktop z wyróżnioną zmianą tytułu](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Mogą wystąpić błędy dotyczące poświadczeń lub rozmiaru zestawu danych. Oto kilka informacji o tym, co zrobić z tymi błędami.

### <a name="unauthorized-401-or-403"></a>Nieautoryzowane (401 lub 403)
Może się tak zdarzyć, jeśli token odświeżania nie został zaktualizowany. Spróbuj wykonać następujące czynności, aby upewnić się, że nadal masz dostęp:

1. Zaloguj się do witryny Azure portal i upewnij się, że można uzyskać dostęp do zasobu.
2. Spróbuj odświeżyć poświadczenia dla pulpitu nawigacyjnego.
3. Spróbuj wyczyścić pamięć podręczną z pulpitu programu PowerBI.


   Jeśli masz dostęp i odświeżanie poświadczeń nie działa, otwórz bilet pomocy technicznej.

### <a name="bad-gateway-502"></a>Zła brama (502)
Jest to zwykle spowodowane przez zapytanie Analytics, które zwraca zbyt dużo danych. Spróbuj użyć mniejszego zakresu czasu dla kwerendy. 

Jeśli zmniejszenie zestawu danych pochodzących z zapytania Analytics nie spełnia wymagań, należy rozważyć użycie [interfejsu API](https://dev.applicationinsights.io/documentation/overview) do ściągania większego zestawu danych. Oto jak przekonwertować eksport M-Query do korzystania z interfejsu API.

1. Utwórz [klucz interfejsu API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Zaktualizuj skrypt usługi Power BI M eksportowany z analytics, zastępując adres URL usługi Azure Resource Manager interfejsem API usługi Application Insights.
   * Zamień **https:\//management.azure.com/subscriptions/...**
   * z, **https:\//api.applicationinsights.io/beta/apps/...**
3. Na koniec zaktualizuj poświadczenia do podstawowego i użyj klucza interfejsu API.

**Istniejący skrypt**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Zaktualizowany skrypt**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Informacje o pobieraniu próbek
W zależności od ilości danych wysyłanych przez aplikację można użyć adaptacyjnej funkcji próbkowania, która wysyła tylko procent danych telemetrycznych. To samo dotyczy ręcznego ustawiania próbkowania w zestawie SDK lub po spożyciu. [Dowiedz się więcej o próbkowaniu](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Zasilacz POWER BI (przestarzały)
Ta metoda tworzy kompletny pulpit nawigacyjny danych telemetrycznych dla Ciebie. Początkowy zestaw danych jest wstępnie zdefiniowany, ale można dodać do niego więcej danych.

### <a name="get-the-adapter"></a>Pobierz adapter
1. Zaloguj się do usługi [Power BI](https://app.powerbi.com/).
2. Otwórz **Get Data** ![zrzut ekranu z ikoną GetData w lewym dolnym rogu](./media/export-power-bi/001.png), **Usługi**.

    ![Zrzuty ekranu źródła danych usługi Get from Application Insights](./media/export-power-bi/002.png)

3. Wybierz **pozycję Pobierz teraz** w obszarze Usługa Application Insights.

   ![Zrzuty ekranu źródła danych usługi Get from Application Insights](./media/export-power-bi/003.png)
4. Podaj szczegóły zasobu usługi Application Insights, a następnie **zaloguj się**.

    ![Zrzut ekranu przedstawiający źródło danych usługi Get from Application Insights](./media/export-power-bi/005.png)

     Te informacje można znaleźć w okienku Przegląd usługi Application Insights:

     ![Zrzut ekranu przedstawiający źródło danych usługi Get from Application Insights](./media/export-power-bi/004.png)

5. Otwórz nowo utworzoną aplikację Usługi Power BI aplikacji Usługi Insights.

6. Zaczekaj minutę lub dwie, aby dane zostały zaimportowane.

    ![Zrzut ekranu przedstawiający kartę power bi](./media/export-power-bi/010.png)

Możesz edytować pulpit nawigacyjny, łącząc wykresy usługi Application Insights z wykresami innych źródeł i zapytaniami Analytics. Więcej wykresów można uzyskać w galerii wizualizacji, a każdy wykres ma parametry, które można ustawić.

Po pierwszym imporcie pulpit nawigacyjny i raporty są aktualizowane codziennie. Można kontrolować harmonogram odświeżania w zestawie danych.

## <a name="next-steps"></a>Następne kroki
* [Power BI — dowiedz się](https://www.powerbi.com/learning/)
* [Samouczek analizy](../../azure-monitor/log-query/get-started-portal.md)

