---
title: Eksportowanie do usługi Power BI z usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zapytania analityczne mogą być wyświetlane w usłudze Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: dee3313082fbe75d76bf27105979cf7e869fafad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294126"
---
# <a name="feed-power-bi-from-application-insights"></a>Źródła danych usługi Power BI z usługi Application Insights
[Power BI](http://www.powerbi.com/) jest to zestaw narzędzi biznesowych, która pomaga analizować dane i udostępniać informacji. Pulpity nawigacyjne sformatowanego są dostępne na każdym urządzeniu. Można połączyć dane z wielu źródeł, takich jak analiza zapytania z [Azure Application Insights](app-insights-overview.md).

Istnieją trzy metody zalecane eksportowania danych usługi Application Insights do usługi Power BI. Można ich używać oddzielnie lub razem.

* [**Power BI karty**](#power-pi-adapter). Konfigurowanie pełny pulpit nawigacyjny dane telemetryczne z aplikacji. Wstępnie zdefiniowane zbiór wykresy, ale można dodać własne zapytania z innych źródeł.
* [**Eksportuj zapytania analityczne**](#export-analytics-queries). Pisać zapytania mają i eksportowania ich do usługi Power BI. Napisz zapytanie, używając Analytics lub zapisywania ich z Lejki użycia. To zapytanie można umieścić na pulpicie nawigacyjnym, wraz z innymi danymi.
* [**Eksport ciągły i usługi Azure Stream Analytics**](app-insights-export-stream-analytics.md). Ta metoda jest przydatna, jeśli chcesz zachować dane przez dłuższy czas. Jeśli nie, użyj jednej z innych metod, ponieważ ta obejmuje więcej pracy, aby skonfigurować.

## <a name="power-bi-adapter"></a>Power BI karty
Ta metoda tworzy pełne pulpitu nawigacyjnego dane telemetryczne dla Ciebie. Wstępnie zdefiniowane początkowego zestawu danych, ale można dodać więcej danych do niego.

### <a name="get-the-adapter"></a>Pobierz karty
1. Zaloguj się do [Power BI](https://app.powerbi.com/).
2. Otwórz **Pobierz dane**, **usług**, a następnie **usługi Application Insights**.
   
    ![Zrzuty ekranu z Pobierz ze źródła danych usługi Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Podaj szczegóły zasobu usługi Application Insights.
   
    ![Zrzut ekranu z Pobierz ze źródła danych usługi Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Zaczekaj minutę lub dwie na dane do zaimportowania.
   
    ![Zrzut ekranu z usługi Power BI karty](./media/app-insights-export-power-bi/010.png)

Pulpit nawigacyjny, można edytować połączenie wykresów usługi Application Insights z systemami innych źródeł oraz z zapytaniami Analytics. Możesz uzyskać więcej wykresów w galerii wizualizacji, a każdy wykres ma parametry, które można ustawić.

Po zaimportowaniu początkowej pulpit nawigacyjny i raporty nadal aktualizowane codziennie. Można kontrolować harmonogram odświeżania w zestawie danych.

## <a name="export-analytics-queries"></a>Zapytania analityczne eksportu
Tej trasy pozwala na każde zapytanie Analytics, takich jak lub wyeksportować z użycia Lejki zapisu, a następnie wyeksportować który do pulpitu nawigacyjnego usługi Power BI. (Możesz dodać do pulpitu nawigacyjnego utworzone przez adapter.)

### <a name="one-time-install-power-bi-desktop"></a>Jeden raz: Zainstaluj Power BI Desktop
Aby zaimportować zapytanie usługi Application Insights, należy użyć tej wersji usługi Power BI. Następnie można opublikować go w sieci Web lub do swojego obszaru roboczego chmury usługi Power BI. 

Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Eksportuj zapytania analityka
1. [Otwórz Analytics i zapisać zapytanie](app-insights-analytics-tour.md).
2. Testowanie i uściślenie kwerendy, dopóki zakończeniu modyfikowania wyniki. Upewnij się, że zapytanie działa poprawnie w module analiz przed wyeksportowaniem.
3. Na **wyeksportować** menu, wybierz **Power BI (M)**. Zapisz plik tekstowy.
   
    ![Zrzut ekranu Analytics, z wyróżnioną pozycją menu eksportu](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. W programie Power BI Desktop, wybierz **Pobierz dane** > **puste zapytania**. Następnie w edytorze zapytań w obszarze **widoku**, wybierz pozycję **Zaawansowany edytor**.

    Wklej wyeksportowanego skryptu języka M do Zaawansowany edytor.

    ![Zrzut ekranu programu Power BI Desktop, z Zaawansowany edytor wyróżnione](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Aby zezwolić usłudze Power BI na dostęp do platformy Azure, może być konieczne podanie poświadczeń. Użyj **konta organizacyjnego** się zalogować za pomocą konta Microsoft.
   
    ![Zrzut ekranu programu Power BI zapytania ustawienia — okno dialogowe](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Jeśli musisz zweryfikować poświadczenia, użyj **ustawienia źródła danych** polecenia menu w edytorze zapytań. Pamiętaj określić poświadczenia, używanego dla platformy Azure, która może być inny niż poświadczenia dla usługi Power BI.
2. Wybierz wizualizacji zapytania i wybierz pola dla osi x, y i podzielenie wymiaru.
   
    ![Opcje vizualization zrzut ekranu Power BI Desktop](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publikowanie raportu do obszaru roboczego chmury usługi Power BI. Z tego miejsca można osadzić zsynchronizowanej wersji do innych stron sieci web.
   
    ![Zrzut ekranu programu Power BI Desktop, z wyróżnioną pozycją przycisk Publikuj](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Ręcznie odświeżyć raport w odstępach czasu, lub skonfigurowania zaplanowanego odświeżania na stronie opcji.

### <a name="export-a-funnel"></a>Eksportuj rozdzielacz
1. [Należy z lejka](usage-funnels.md).
2. Wybierz **Power BI**. 

   ![Przycisk zrzut ekranu usługi Power BI](./media/app-insights-export-power-bi/button.png)
   
3. W programie Power BI Desktop, wybierz **Pobierz dane** > **puste zapytania**. Następnie w edytorze zapytań w obszarze **widoku**, wybierz pozycję **Zaawansowany edytor**.

   ![Zrzut ekranu programu Power BI Desktop, z wyróżnionym przyciskiem puste zapytania](./media/app-insights-export-power-bi/blankquery.png)

   Wklej wyeksportowanego skryptu języka M do Zaawansowany edytor. 

   ![Zrzut ekranu programu Power BI Desktop, z Zaawansowany edytor wyróżnione](./media/app-insights-export-power-bi/advancedquery.png)

4. Wybierz elementy z zapytania, a następnie wybierz pozycję wizualizacji lejka.

   ![Opcje vizualization zrzut ekranu Power BI Desktop](./media/app-insights-export-power-bi/selectsequence.png)

5. Zmień tytuł, aby był łatwy do rozpoznania i opublikować raportu do obszaru roboczego chmury usługi Power BI. 

   ![Zrzut ekranu programu Power BI Desktop, zmiana tytułu wyróżnione](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Mogą wystąpić błędy dotyczące poświadczeń lub rozmiaru zestawu danych. Oto niektóre informacje o tym, jak te błędy.

### <a name="unauthorized-401-or-403"></a>Nieautoryzowane (401 lub 403)
Może to nastąpić, jeśli token odświeżania nie został zaktualizowany. Spróbuj wykonać następujące kroki, aby upewnić się, że nadal mieć dostęp:

1. Zaloguj się do portalu Azure i upewnij się, że ma dostęp do zasobu.
2. Spróbuj odświeżyć poświadczenia dla pulpitu nawigacyjnego.

 Jeśli masz dostęp i odświeżyć poświadczenia nie działa, otwórz bilet pomocy technicznej.

### <a name="bad-gateway-502"></a>Zła brama (502)
Jest to zazwyczaj spowodowane Analytics kwerendę, która zwraca za dużo danych. Spróbuj użyć mniejszego zakresu czasu dla zapytania. 

Jeśli zmniejszenie zestawu danych przesyłanych przez zapytanie Analytics nie spełniają wymagań, należy rozważyć użycie [interfejsu API](https://dev.applicationinsights.io/documentation/overview) ściągania większy zestaw danych. Poniżej przedstawiono sposób konwertowania Eksport M zapytania do za pomocą interfejsu API.

1. Utwórz [klucz interfejsu API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Zaktualizuj skrypt Power BI M wyeksportowany z usługi Analytics zamieniając adres URL Menedżera zasobów Azure przy użyciu interfejsu API Insights aplikacji.
   * Zastąp  **https://management.azure.com/subscriptions/...**
   * **https://api.applicationinsights.io/beta/apps/...**
3. Na koniec Zaktualizuj poświadczenia do warstwy podstawowa i korzystania z klucza interfejsu API.
  

**Istniejący skrypt**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Zaktualizowany skrypt**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>O pobierania próbek
Aplikacja wysyła dużą ilość danych, można użyć funkcji adaptacyjną próbkowania, która wysyła tylko procent telemetrii. Jest taka sama wartość true, jeśli ręcznie ustawiono próbkowania w zestawie SDK lub na wprowadzanie. [Dowiedz się więcej o próbkowania](app-insights-sampling.md).


## <a name="next-steps"></a>Kolejne kroki
* [Power BI — informacje](http://www.powerbi.com/learning/)
* [Samouczek analityka](app-insights-analytics-tour.md)

