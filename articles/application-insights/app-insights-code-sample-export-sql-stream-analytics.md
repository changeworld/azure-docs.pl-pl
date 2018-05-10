---
title: Eksportowanie do bazy danych SQL z usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Stale eksportować dane usługi Application Insights do bazy danych SQL za pomocą usługi Stream Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: e410bb87d017a7659c3eaffbedef378aaf7f5716
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Wskazówki: Eksportowanie do bazy danych SQL z usługi Application Insights przy użyciu usługi analiza strumienia
W tym artykule pokazano, jak przenieść dane telemetryczne z [Azure Application Insights] [ start] do bazy danych Azure SQL za pomocą [eksportu ciągłego] [ export] i [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Eksport ciągły przenosi dane telemetryczne do usługi Azure Storage w formacie JSON. Firma Microsoft będzie analizować obiektów JSON przy użyciu usługi Azure Stream Analytics i utworzyć wiersze w tabeli bazy danych.

(Więcej ogólnie rzecz biorąc, eksportu ciągłego jest właśnie własnej analizy telemetrii aplikacji wysyłania do usługi Application Insights. Można dostosować ten przykładowy kod, aby wykonać inne czynności z wyeksportowane dane telemetryczne, takich jak agregacji danych.)

Zaczniemy od założenia już masz aplikację, którą chcesz monitorować.

W tym przykładzie użyjemy danych widoku strony, ale tego samego wzorca można z łatwością rozszerzyć na inne typy danych, takich jak niestandardowe zdarzenia i wyjątki. 

## <a name="add-application-insights-to-your-application"></a>Dodawanie usługi Application Insights do aplikacji
Aby rozpocząć:

1. [Skonfiguruj usługę Application Insights dla stron sieci web](app-insights-javascript.md). 
   
    (W tym przykładzie firma Microsoft będzie skupić się na przetwarzanie danych widoku strony z przeglądarki klienta, ale można również skonfigurować usługi Application Insights dla po stronie serwera z [Java](app-insights-java-get-started.md) lub [ASP.NET](app-insights-asp-net.md) żądania aplikacji i procesów, zależności i inne dane telemetryczne serwera.)
2. Publikowanie aplikacji i obserwować dane telemetryczne w zasobu usługi Application Insights.

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Eksport ciągły zawsze generuje dane do konta usługi Azure Storage, należy najpierw utworzyć magazyn.

1. Utwórz konto magazynu w ramach subskrypcji w [portalu Azure][portal].
   
    ![W portalu Azure wybierz nowy, danych, magazynu. Zaznacz klasycznym, wybierz polecenie Utwórz. Podaj nazwę magazynu.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. Tworzenie kontenera
   
    ![W nowym magazynie wybierz kontenery, kliknij Kafelek kontenerów, a następnie dodaj](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. Skopiuj klucz dostępu do magazynu
   
    Należy ją szybko, aby skonfigurować dane wejściowe usługi analiza strumienia.
   
    ![W magazynie Otwórz ustawienia kluczy i wykonać kopię podstawowego klucza dostępu](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpocząć eksport ciągły w celu magazynu Azure
1. W portalu Azure przejdź do zasobu usługi Application Insights, utworzone dla aplikacji.
   
    ![Kliknij przycisk Przeglądaj, usługi Application Insights aplikacji](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. Utwórz eksport ciągły.
   
    ![Wybierz ustawienia, Eksport ciągły](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    Wybierz utworzone wcześniej konto magazynu:

    ![Skonfiguruj docelowego eksportu](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    Ustaw typy zdarzeń, które chcesz wyświetlić:

    ![Wybierz typy zdarzeń](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. Let niektóre dane gromadzone. Zaczekaj i innym użytkownikom za pomocą aplikacji przez pewien czas. Dane telemetryczne wejdzie i zobaczysz statystyczne wykresów w [Eksploratora metryk](app-insights-metrics-explorer.md) i zdarzeń z [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md). 
   
    A także będzie eksportować dane do usługi magazynu. 
2. Sprawdź wyeksportowane dane, albo w portalu — wybierz **Przeglądaj**, wybierz konto magazynu, a następnie **kontenery** — lub w programie Visual Studio. W programie Visual Studio, wybierz **wyświetlić / w chmurze Explorer**i otwórz Azure / magazynu. (Jeśli nie masz tej opcji menu, należy zainstalować zestaw Azure SDK: Otwórz okno dialogowe nowego projektu i Otwórz program Visual C# / w chmurze / Get Microsoft Azure SDK dla platformy .NET.)
   
    ![W programie Visual Studio Otwórz przeglądarkę serwera, Azure, Magazyn](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Zanotuj części wspólnej nazwy ścieżki, która jest pochodną klucz nazwy i instrumentacji aplikacji. 

Zdarzenia są zapisywane do obiektu blob pliki w formacie JSON. Każdy plik może zawierać co najmniej jednego zdarzenia. Dlatego chcemy odczytuje dane zdarzenia i filtrować polami, którą chcemy udostępnić. Wszystkie rodzaje czynności, które firma Microsoft może wykonywać z danymi, ale naszego planu dzisiaj jest użycie usługi analiza strumienia może przenieść dane do bazy danych SQL. Która ułatwi późniejszą do uruchomienia partii zapytań interesujące.

## <a name="create-an-azure-sql-database"></a>Utwórz bazę danych Azure SQL
Ponownie uruchamianie z subskrypcją w [portalu Azure][portal], utworzyć bazę danych (i nowy serwer, chyba że zostały już został) której będzie zapisywać dane.

![Nowe dane, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

Upewnij się, że serwer bazy danych zezwala na dostęp do usług platformy Azure:

![Przeglądaj, serwery, z serwerem, ustawienia zapory, Zezwalaj na dostęp do platformy Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Utwórz tabelę w bazie danych SQL Azure
Połączenia z bazą danych utworzony w poprzedniej sekcji z narzędzia do zarządzania preferowany. W ramach tego przewodnika użyjemy [narzędzia do zarządzania serwerem SQL](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Utwórz nowe zapytanie i wykonaj T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

W tym przykładzie użyto dane wyświetleń strony. Aby wyświetlić dostępne dane, sprawdź dane wyjściowe JSON, a następnie zobacz [wyeksportować modelu danych](app-insights-export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Utwórz wystąpienie usługi Azure Stream Analytics
Z [portalu Azure](https://portal.azure.com/), wybierz usługę Azure Stream Analytics i utworzyć nowe zadanie usługi Stream Analytics:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA001.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA002.png)

Po utworzeniu nowego zadania, wybierz **przejdź do zasobu**.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Dodaj nowe dane wejściowe

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA004.png)

Ustaw, aby pobrać dane wejściowe z obiektu blob z eksportu ciągłego:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA005.png)

Teraz musisz podstawowy klucz dostępu z konta magazynu, który wcześniej zapisany. Ustaw jako klucz konta magazynu.

#### <a name="set-path-prefix-pattern"></a>Wzorzec prefiksu ścieżki zestawu

**Pamiętaj ustawienie formatu RRRR-MM-DD (Łączniki).**

Wzorzec prefiksu ścieżki Określa sposób odnajdowania usługi Stream Analytics plików wejściowych w magazynie. Należy ustawić odpowiadają jak eksportu ciągłego przechowuje dane. Ustaw go następująco:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

W tym przykładzie:

* `webapplication27` jest to nazwa zasobu usługi Application Insights **w przypadku małych**. 
* `1234...` jest to klucz Instrumentacji zasobu usługi Application Insights **z kreskami usunięte**. 
* `PageViews` to typ danych, którą chcemy udostępnić analizować. Dostępne typy są zależne od filtr ustawionych w eksportu ciągłego. Sprawdź wyeksportowane dane na temat dostępnych typów oraz temat [wyeksportować modelu danych](app-insights-export-data-model.md).
* `/{date}/{time}` wzorzec są zapisywane jako literału.

Aby uzyskać nazwę i iKey zasobu usługi Application Insights, otwórz Essentials na stronie Przegląd, lub Otwórz ustawienia.

> [!TIP]
> Funkcja próbki do sprawdzenia poprawnie ustawiona ścieżka wejściowa. Jeśli działanie nie powiodło się: Sprawdź, czy dane w magazynie dla wybrano zakresu czasu próbki. Dokonaj edycji definicji wejściowego i sprawdź ustawić konto magazynu, ścieżkę prefiks i Data formacie poprawnie.
> 
> 
## <a name="set-query"></a>Zapytanie zestawu
Otwórz sekcję zapytania:

Zastąp domyślne zapytanie z:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Zwróć uwagę, że pierwsze kilka właściwości są specyficzne dla danych widoku strony. Eksporty inne typy telemetrii ma inne właściwości. Zobacz [szczegółowe odwołania do modelu danych dla typów właściwości i wartości.](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>Konfigurowanie danych wyjściowych do bazy danych
Wybierz SQL jako dane wyjściowe.

![Analiza strumienia wybierz dane wyjściowe](./media/app-insights-code-sample-export-sql-stream-analytics/SA006.png)

Określ bazę danych SQL.

![Uzupełnij informacje dotyczące bazy danych](./media/app-insights-code-sample-export-sql-stream-analytics/SA007.png)

Zamknij kreatora i zaczekaj na powiadomienie, które skonfigurowano dane wyjściowe.

## <a name="start-processing"></a>Rozpocznij przetwarzanie
Uruchom zadanie na pasku akcji:

![W module analiz strumienia kliknij przycisk Start](./media/app-insights-code-sample-export-sql-stream-analytics/SA008.png)

Można wybrać, czy można uruchomić przetwarzania danych, zaczynając od teraz, lub do uruchomienia z wcześniejszych danych. Drugie polecenie jest przydatne, jeśli masz już uruchomione na chwilę eksportu ciągłego.

Po kilku minutach Przejdź wstecz do narzędzia do zarządzania serwerem SQL i obserwować dane przepływające w. Na przykład użyć zapytania następująco:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Pokrewne artykuły:
* [Eksportowanie do usługi Power BI przy użyciu usługi analiza strumienia](app-insights-export-power-bi.md)
* [Szczegółowe dane modelu odwołania dla typów właściwości i wartości.](app-insights-export-data-model.md)
* [Eksport ciągły w usłudze Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

