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
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: a57393918992019844e2ff4ccc13d671f0b90ed5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900397"
---
# <a name="feed-power-bi-from-application-insights"></a>Źródło danych usługi Power BI z usługi Application Insights
[Usługa Power BI](https://www.powerbi.com/) jest zestaw narzędzi biznesowej, która pomaga analizować dane i dziel się spostrzeżeniami. Zaawansowane pulpity nawigacyjne są dostępne na każdym urządzeniu. Można połączyć dane z wielu źródeł, takich jak zapytania usługi Analytics z [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Istnieją trzy metody eksportowania danych usługi Application Insights do usługi Power BI:

* [**Eksportuj zapytań analitycznych**](#export-analytics-queries). Jest to preferowana metoda. Pisać zapytania ma i eksportowania ich do usługi Power BI. Na pulpicie nawigacyjnym, wraz z wszelkimi innymi danymi, można umieścić tego zapytania.
* [**Eksport ciągły i Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Ta metoda jest przydatna, jeśli chcesz przechowywać dane przez długi czas. Jeśli użytkownik nie ma to wymaganie dotyczące przechowywania danych rozszerzonych, użyj metody zapytania usługi analytics eksportu. Eksport ciągły i Stream Analytics obejmuje więcej pracy, aby skonfigurować i narzut na przechowywanie dodatkowych.
* **Usługa Power BI karty**. Wstępnie zdefiniowane zbiór wykresy, ale możesz dodawać własne zapytania z innych źródeł.

> [!NOTE]
> Karta usługi Power BI jest teraz **przestarzałe**. Wstępnie zdefiniowane wykresy dla tego rozwiązania są wypełniane przez statyczne nieedytowalne zapytania. Nie masz możliwość edytowania tych zapytań, w zależności od niektóre właściwości danych jest możliwe połączenie usługi Power BI zakończy się powodzeniem, a żadne dane nie jest wypełnione. Jest to spowodowane kryteria wykluczenia, które są ustawione w ramach zapytania zapisane na stałe. Chociaż to rozwiązanie mogą nadal działać w przypadku niektórych klientów, ze względu na brak elastyczność karty zalecanym rozwiązaniem jest użycie [ **wyeksportować zapytanie analizy** ](#export-analytics-queries) funkcji.

## <a name="export-analytics-queries"></a>Eksportuj zapytań analitycznych
Ta trasa pozwala na zapis wszelkie zapytania usługi Analytics, takie jak, lub wyeksportuj go z użycia Lejki, a następnie wyeksportować, do pulpitu nawigacyjnego usługi Power BI. (Możesz dodać do pulpitu nawigacyjnego, utworzone przez adapter.)

### <a name="one-time-install-power-bi-desktop"></a>Jeden raz: Instalowanie programu Power BI Desktop
Aby zaimportować zapytanie usługi Application Insights, należy użyć klasycznej wersji programu Power BI. Następnie można opublikować ją w sieci web lub do obszaru roboczego usługi Power BI chmury. 

Zainstaluj [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Eksportuj zapytania usługi Analytics
1. [Otwórz analizę i zapisać zapytania](../../azure-monitor/log-query/get-started-portal.md).
2. Przetestuj i uściślenie kwerendy, dopóki nie jesteś zadowolony z wyników. Upewnij się, że zapytanie działa prawidłowo w usłudze Analytics przed wykonaniem eksportu.
3. Na **wyeksportować** menu, wybierz **usługi Power BI (M)** . Zapisz plik tekstowy.
   
    ![Zrzut ekranu usługi Analytics, za pomocą menu eksportu wyróżniony](./media/export-power-bi/analytics-export-power-bi.png)
4. W programie Power BI Desktop, wybierz **Pobierz dane** > **puste zapytanie**. Następnie w edytorze zapytań w ramach **widoku**, wybierz opcję **edytora zaawansowanego**.

    Wklej wyeksportowanego skryptu języka M do edytora zaawansowanego.

    ![Zrzut ekranu programu Power BI Desktop, za pomocą edytora zaawansowanego wyróżniony](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Aby zezwolić na dostęp do platformy Azure w usłudze Power BI, trzeba podać poświadczenia. Użyj **konta organizacyjnego** zalogować się przy użyciu konta Microsoft.
   
    ![Zrzut ekranu z zapytania ustawień usługi Power BI, okno dialogowe](./media/export-power-bi/power-bi-import-sign-in.png)

    Jeśli zachodzi potrzeba zweryfikowania poświadczeń, użyj **ustawienia źródła danych** polecenia menu w edytorze zapytań. Pamiętaj określić poświadczenia, których używasz na platformie Azure, która może się różnić od swoje poświadczenia dla usługi Power BI.
6. Wybierz wizualizację dla zapytania, a następnie wybierz pola dla osi x, y i segmentacji wymiaru.
   
    ![Zrzut ekranu usługi Power BI Desktop Opcje wizualizacji](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publikowanie raportów do obszaru roboczego usługi Power BI chmury. Z tego miejsca można osadzić zsynchronizowanej wersji do innych stron sieci web.
   
    ![Zrzut ekranu programu Power BI Desktop z wyróżnionym przyciskiem publikowania](./media/export-power-bi/publish-power-bi.png)
8. Ręcznie odświeżyć raport w odstępach czasu lub skonfigurować zaplanowane odświeżanie na stronie opcji.

### <a name="export-a-funnel"></a>Wyeksportować lejka
1. [Wprowadź lejku](../../azure-monitor/app/usage-funnels.md).
2. Wybierz **usługa Power BI**.

   ![Przycisk zrzut ekranu usługi Power BI](./media/export-power-bi/button.png)

3. W programie Power BI Desktop, wybierz **Pobierz dane** > **puste zapytanie**. Następnie w edytorze zapytań w ramach **widoku**, wybierz opcję **edytora zaawansowanego**.

   ![Zrzut ekranu programu Power BI Desktop z wyróżnionym przyciskiem puste zapytanie](./media/export-power-bi/blankquery.png)

   Wklej wyeksportowanego skryptu języka M do edytora zaawansowanego. 

   ![Zrzut ekranu programu Power BI Desktop, za pomocą edytora zaawansowanego wyróżniony](./media/export-power-bi/advancedquery.png)

4. Wybierz elementy z zapytania, a następnie wybierz wizualizację lejka.

   ![Zrzut ekranu usługi Power BI Desktop Opcje wizualizacji](./media/export-power-bi/selectsequence.png)

5. Zmień tytuł na dowolnie zrozumiałe i opublikować raport do obszaru roboczego usługi Power BI chmury. 

   ![Zrzut ekranu programu Power BI Desktop, zmiana tytułu wyróżniony](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Mogą wystąpić błędy dotyczące poświadczeń lub rozmiaru zestawu danych. Poniżej przedstawiono niektóre informacje o tym, co robić w przypadku tych błędów.

### <a name="unauthorized-401-or-403"></a>Nieautoryzowany (401 lub 403)
Może to nastąpić, jeśli token odświeżania nie został zaktualizowany. Spróbuj wykonać następujące kroki, aby upewnić się, że nadal masz dostęp:

1. Zaloguj się do witryny Azure portal i upewnij się, że można uzyskać dostępu do zasobu.
2. Spróbuj odświeżyć poświadczenia dla pulpitu nawigacyjnego.

   Jeśli masz dostęp, i odświeżyć poświadczenia nie działa, otwórz bilet pomocy technicznej.

### <a name="bad-gateway-502"></a>Nieprawidłową bramą (502)
Jest to zazwyczaj spowodowane zapytania usługi Analytics, która zwraca zbyt dużej ilości danych. Spróbuj użyć mniejszego zakresu czasu dla zapytania. 

Jeśli zmniejszyć zestaw danych pochodzących z zapytania analizy nie spełnia wymagań, należy wziąć pod uwagę przy użyciu [API](https://dev.applicationinsights.io/documentation/overview) ściągnąć większy zestaw danych. Poniżej przedstawiono sposób konwertowania eksportowania zapytań M do korzystania z interfejsu API.

1. Tworzenie [klucz interfejsu API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Zaktualizuj skrypt usługi Power BI M, który został wyeksportowany z analizy, zamieniając adres URL usługi Azure Resource Manager przy użyciu interfejsu API usługi Application Insights.
   * Zastąp **https:\//management.azure.com/subscriptions/...**
   * za pomocą **https:\//api.applicationinsights.io/beta/apps/...**
3. Na koniec Zaktualizuj poświadczenia do warstwy podstawowa, a następnie użyj swój klucz interfejsu API.

**Istniejący skrypt**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Zaktualizowanego skryptu**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Temat próbkowania
W zależności od ilości danych wysyłanych przez aplikację możesz chcieć użyć funkcja adaptacyjnego próbkowania, który wysyła tylko ułamka telemetrii. Jest taka sama wartość true, jeśli ręcznie ustawiono próbkowania w zestawie SDK lub podczas pozyskiwania danych. [Dowiedz się więcej na temat próbkowania](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Usługa Power BI karty (przestarzałe)
Ta metoda tworzy pełny pulpit nawigacyjny danych telemetrycznych. Początkowy zestaw danych jest wstępnie zdefiniowane, ale większej ilości danych można dodać do niego.

### <a name="get-the-adapter"></a>Pobierz karty
1. Zaloguj się do [usługa Power BI](https://app.powerbi.com/).
2. Otwórz **Pobierz dane** ![zrzut ekranu GetData ikony w lewym dolnym rogu](./media/export-power-bi/001.png), **usług**.

    ![Zrzuty ekranu z uzyskiwanie źródła danych usługi Application Insights](./media/export-power-bi/002.png)

3. Wybierz **Pobierz teraz** w ramach usługi Application Insights.

   ![Zrzuty ekranu z uzyskiwanie źródła danych usługi Application Insights](./media/export-power-bi/003.png)
4. Podaj szczegóły zasobu usługi Application Insights, a następnie **logowania**.

    ![Zrzut ekranu z uzyskiwanie źródła danych usługi Application Insights](./media/export-power-bi/005.png)

     Te informacje można znaleźć w okienku Omówienie usługi Application Insights:

     ![Zrzut ekranu z uzyskiwanie źródła danych usługi Application Insights](./media/export-power-bi/004.png)

5. Otwórz nowo utworzony Application Insights aplikację Power BI.

6. Poczekaj minutę lub dwie dane do zaimportowania.

    ![Zrzut ekranu usługi Power BI karty](./media/export-power-bi/010.png)

Możesz edytować pulpit nawigacyjny, łącząc wykresy usługi Application Insights z tymi z innych źródeł, a za pomocą zapytań usługi Analytics. Uzyskasz wykresy więcej w galerii wizualizacji, a każdy wykres ma parametry, które można ustawić.

Po imporcie początkowym pulpit nawigacyjny i raporty będą aktualizowane codziennie. Możesz kontrolować harmonogram odświeżania zestawu danych.

## <a name="next-steps"></a>Kolejne kroki
* [Usługa Power BI — informacje](https://www.powerbi.com/learning/)
* [Samouczek analiz](../../azure-monitor/log-query/get-started-portal.md)

