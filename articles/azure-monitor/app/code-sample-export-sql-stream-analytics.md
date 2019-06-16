---
title: Eksportowanie do bazy danych SQL z usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Ciągły Eksport danych usługi Application Insights do bazy danych SQL przy użyciu usługi Stream Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: eecd2a50607fa42562a9ae6a7fb950a253655a45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65872700"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Przewodnik: Eksportowanie do bazy danych SQL z usługi Application Insights przy użyciu usługi Stream Analytics
W tym artykule pokazano, jak przenieść dane telemetryczne z [usługi Azure Application Insights] [ start] do usługi Azure SQL database przy użyciu [eksportu ciągłego] [ export] i [usługi Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Eksport ciągły przenosi dane telemetryczne do usługi Azure Storage w formacie JSON. Firma Microsoft będzie analizowanie obiektów JSON za pomocą usługi Azure Stream Analytics i utworzyć wiersze w tabeli bazy danych.

(Ogólnie rzecz biorąc, Eksport ciągły jest to sposób na wykonywanie analizy telemetrii aplikacji wysyłanie do usługi Application Insights. Można dostosować ten przykładowy kod, aby wykonać inne czynności przy użyciu wyeksportowanego telemetrii, takie jak agregacja danych.)

Rozpoczniemy pracę przy założeniu, że masz już aplikację, którą chcesz monitorować.

W tym przykładzie użyjemy dane wyświetleń strony, ale tego samego wzorca można z łatwością rozszerzyć na inne typy danych, takie jak zdarzenia niestandardowe i wyjątki. 

## <a name="add-application-insights-to-your-application"></a>Dodaj usługę Application Insights do aplikacji
Aby rozpocząć pracę:

1. [Konfigurowanie usługi Application Insights dla stron sieci web](../../azure-monitor/app/javascript.md). 
   
    (W tym przykładzie skupimy się na przetwarzaniu dane dotyczące wyświetleń stron w przeglądarkach klientów, ale można też skonfigurować usługę Application Insights po stronie serwera usługi [Java](../../azure-monitor/app/java-get-started.md) lub [ASP.NET](../../azure-monitor/app/asp-net.md) żądania aplikacji i procesów zależności i inne dane telemetryczne serwera).
2. Opublikuj swoją aplikację i obejrzyj dane telemetryczne, które pojawiają się w zasobie usługi Application Insights.

## <a name="create-storage-in-azure"></a>Tworzenie magazynu na platformie Azure
Eksport ciągły zawsze wysyła dane do konta usługi Azure Storage, dlatego musisz najpierw utworzyć magazynu.

1. Tworzenie konta magazynu w ramach subskrypcji w [witryny Azure portal][portal].
   
    ![W witrynie Azure portal wybierz pozycję Nowy, danych, magazynu. Wybierz model klasyczny, wybierz pozycję Utwórz. Podaj nazwę magazynu.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Tworzenie kontenera
   
    ![W nowym magazynie wybierz kontenery, kliknij Kafelek kontenerów, a następnie dodaj](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiowanie klucza dostępu do magazynu
   
    Będzie potrzebny wkrótce, aby skonfigurować dane wejściowe do usługi stream analytics.
   
    ![W magazynie Otwórz okno Ustawienia i klucze i utwórz jego kopię podstawowego klucza dostępu](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Rozpocząć eksport ciągły w usłudze Azure storage
1. W witrynie Azure portal przejdź do zasobu usługi Application Insights, utworzonej aplikacji.
   
    ![Wybierz kolejno opcje Przeglądaj, Application Insights aplikacji](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Utwórz eksport ciągły.
   
    ![Wybierz pozycję Dodaj ustawienia eksportu ciągłego](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Wybierz konto magazynu, która została utworzona wcześniej:

    ![Ustaw miejsce docelowe eksportu](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Ustaw typy zdarzeń, które mają być wyświetlane:

    ![Wybierz typy zdarzeń](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Pozwól, niektóre dane są gromadzone. Zaczekaj i umożliwianie użytkownikom korzystania z aplikacji przez jakiś czas. Dane telemetryczne pojawią się, a zobaczysz statystyczne wykresów w [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md) i zdarzeń z [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md). 
   
    A także dane zostaną wyeksportowane do usługi storage. 
2. Zbadaj wyeksportowane dane, zarówno w portalu — wybieranie **Przeglądaj**, wybierz konto magazynu, a następnie **kontenery** — lub w programie Visual Studio. W programie Visual Studio, wybierz **wyświetlić / w chmurze Explorer**, a następnie otwórz Azure / Storage. (Jeśli nie masz tej opcji menu, należy zainstalować zestaw Azure SDK: Otwórz okno dialogowe Nowy projekt i otwórz Visual C# / w chmurze / uzyskiwanie Microsoft Azure SDK dla platformy .NET.)
   
    ![W programie Visual Studio Otwórz przeglądarkę serwera, Azure, Magazyn](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Zanotuj części wspólnej nazwy ścieżki, która jest pochodną klucz nazwy i instrumentacji aplikacji. 

Zdarzenia są zapisywane do obiektu blob, pliki w formacie JSON. Każdy plik może zawierać jedno lub więcej zdarzeń. Dlatego chcielibyśmy odczytuje dane zdarzenia i filtrowanie pól, które chcemy. Istnieją wszelkiego rodzaju czynności, które firma Microsoft może wykonać przy użyciu danych, ale naszego planu jest już dziś przenoszenia danych do usługi SQL database za pomocą usługi Stream Analytics. Który uczyni go łatwo uruchomić wiele ciekawych zapytania.

## <a name="create-an-azure-sql-database"></a>Utwórz bazę danych Azure SQL Database
Jeszcze raz od Twojej subskrypcji w [witryny Azure portal][portal], utworzyć bazę danych (i nowego serwera, chyba, że już masz jedną), do której będziesz zapisywać dane.

![New, Data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Upewnij się, że serwer bazy danych umożliwia dostęp do usług platformy Azure:

![Przeglądaj, serwery, z serwerem, ustawienia zapory, Zezwalaj na dostęp do platformy Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Tworzenie tabeli w usłudze Azure SQL DB
Połączenia z bazą danych, utworzony w poprzedniej sekcji z narzędziem preferowane. W tym przewodniku użyjemy [narzędzi zarządzania serwerem SQL](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Utwórz nowe zapytanie i wykonać języka T-SQL:

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

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

W tym przykładzie używamy danych z wyświetleń stron. Aby wyświetlić dostępne dane, zbadaj dane wyjściowe JSON i zobacz [eksportowanie modelu danych](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Tworzenie wystąpienia usługi Azure Stream Analytics
Z [witryny Azure portal](https://portal.azure.com/), wybierz usługę Azure Stream Analytics i utworzyć nowe zadanie usługi Stream Analytics:

![Stream analytics ustawienia](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Gdy zostanie utworzone nowe zadanie, wybierz pozycję **przejdź do zasobu**.

![Stream analytics ustawienia](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Dodaj nowe dane wejściowe

![Stream analytics ustawienia](./media/code-sample-export-sql-stream-analytics/SA004.png)

Ustaw, aby pobrać dane wejściowe z obiektu blob z eksportu ciągłego:

![Stream analytics ustawienia](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Teraz będzie potrzebna podstawowy klucz dostępu z Twojego konta magazynu, możesz zauważyć, wcześniej. Ustaw jako klucz konta magazynu.

#### <a name="set-path-prefix-pattern"></a>Wzorzec prefiksu ścieżki zestawu

**Pamiętaj ustawić Format daty RRRR-MM-DD (z kreskami).**

Wzorzec prefiksu ścieżki Określa, jak usługi Stream Analytics znajduje pliki wejściowe w magazynie. Musisz ustawić jego odpowiadają jak eksportu ciągłego przechowuje dane. Ustaw ją następująco:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

W tym przykładzie:

* `webapplication27` to nazwa zasobu usługi Application Insights **w małych liter**. 
* `1234...` jest to klucz Instrumentacji zasobu usługi Application Insights **z kreskami usunięte**. 
* `PageViews` jest to typ danych, które chcemy przeanalizować. Dostępne typy są zależne od filtr, który zostało ustawiony w eksportu ciągłego. Sprawdź wyeksportowane dane, aby zobaczyć dostępne typy, a [eksportowanie modelu danych](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` wzorzec jest zapisywany bezpośrednio.

Aby uzyskać nazwę i klucz Instrumentacji zasobu usługi Application Insights, Otwórz program Essentials na stronie Przegląd lub Otwórz okno Ustawienia.

> [!TIP]
> Użyj wyświetlany kod przykładowej funkcji, aby sprawdzić, poprawnie ustawiona ścieżka wejściowa. Jeśli nie powiedzie się: Sprawdź, czy dane w magazynie dla wybranego zakresu czasu próbki. Edytuj definicję danych wejściowych i sprawdź Ustaw konto magazynu, prefiks ścieżki i Data formacie poprawnie.

 
## <a name="set-query"></a>Ustaw zapytanie
Otwórz sekcję zapytania:

Zastąp domyślne zapytanie za pomocą:

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

Należy zauważyć, że kilka pierwszych właściwości są specyficzne dla dane dotyczące wyświetleń stron. Eksporty innych typów danych telemetrycznych ma inne właściwości. Zobacz [szczegółowe dokumentacja dotycząca modeli obiektów dla typów właściwości i wartości.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Konfigurowanie danych wyjściowych do bazy danych
Wybierz SQL jako dane wyjściowe.

![W usłudze stream analytics wybierz dane wyjściowe](./media/code-sample-export-sql-stream-analytics/SA006.png)

Określ bazę danych SQL.

![Wypełnij szczegóły bazy danych](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zamknij kreatora i poczekaj, aż powiadomienie, które skonfigurowano dane wyjściowe.

## <a name="start-processing"></a>Rozpocznij przetwarzanie
Uruchamianie zadania na pasku akcji:

![W usłudze stream analytics kliknij przycisk Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

Możesz wybrać, czy do rozpoczęcia przetwarzania danych, zaczynając od teraz lub zaczynać starszych danych. Drugim jest przydatne, jeśli masz już uruchomione od pewnego czasu eksportu ciągłego.

Po kilku minutach wróć do narzędzi zarządzania serwerem SQL i obejrzyj dane przepływające w. Na przykład użyć zapytania następująco:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Pokrewne artykuły:
* [Eksportowanie do usługi Power BI przy użyciu usługi Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Szczegółowe dane modelu odwołań dla typów właściwości i wartości.](../../azure-monitor/app/export-data-model.md)
* [Eksport ciągły w usłudze Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

