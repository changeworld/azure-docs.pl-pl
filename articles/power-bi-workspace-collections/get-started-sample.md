---
title: Rozpoczynanie pracy przy użyciu przykładu
description: W tym artykule firma Microsoft będzie przedstawiono przykładowe uruchomiono get Power BI obszaru roboczego kolekcji.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: d8a9ef78-ad4e-4bc7-9711-89172dc5c548
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/25/2017
ms.author: maghan
ms.openlocfilehash: 4daa675092bed1788025e7e3f7e7e11d02ef1671
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414885"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Rozpoczęcie pracy z przykładem Power BI obszaru roboczego kolekcje

Z **Microsoft Power BI obszaru roboczego kolekcje**, można zintegrować raportów usługi Power BI bezpośrednio z sieci web lub aplikacji dla urządzeń przenośnych. W tym artykule na wprowadzeniu **Power BI obszaru roboczego kolekcje** próbki uruchomiono get.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Przed rozszerzana wszelkie dodatkowe, którym chcesz zapisać następujące zasoby: pomagają składników podczas integrowania raportów usługi Power BI za do przykładowej aplikacji i własnych aplikacji.

* [Przykładową aplikację sieci web dla obszaru roboczego](http://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI obszaru roboczego kolekcje API odwołania](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK ](http://go.microsoft.com/fwlink/?LinkId=746472) (dostępnych za pośrednictwem pakietu NuGet)
* [Przykładowe osadzić raport JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Zanim można skonfigurować i działanie Pobierz kolekcje obszaru roboczego Power BI rozpoczęte próbki, należy utworzyć co najmniej jeden **kolekcji obszarów roboczych** w Twojej subskrypcji platformy Azure. Aby dowiedzieć się, jak utworzyć **kolekcji obszarów roboczych** w portalu Azure, zobacz [wprowadzenie do korzystania z programu Power BI obszaru roboczego kolekcje](get-started.md).

## <a name="configure-the-sample-app"></a>Konfigurowanie przykładowej aplikacji

Przejdźmy przez proces konfigurowania środowiska deweloperskiego Visual Studio dostęp do składników wymaganych do uruchomienia aplikacji przykładowej.

1. Pobierz i Rozpakuj [Power BI obszaru roboczego kolekcje — integracji raportu w aplikacji sieci web](http://go.microsoft.com/fwlink/?LinkId=761493) w witrynie GitHub.
2. Otwórz **PowerBI embedded.sln** w programie Visual Studio. Może być konieczne wykonanie **pakiet aktualizacji** w konsoli Menedżera pakietów NuGet, aby można było zaktualizować pakietów używanych w tym rozwiązaniu.
3. Skompiluj rozwiązanie.
4. Uruchom **ProvisionSample** aplikacji konsoli. W przykładowej aplikacji konsoli aprowizować obszaru roboczego i importowanie pliku PBIX.
5. Do udostępnienia nowej **obszaru roboczego**, wybierz opcję 1, **zarządzania kolekcji**, a następnie wybierz opcję 6, **udostępnić nowy obszar roboczy**
6. Aby zaimportować nowy **raportu**, wybierz opcję 2, **raport zarządzania**, a następnie wybierz opcję 3, **pliku pulpitu zaimportować plik PBIX do obszaru roboczego**.

7. Wprowadź użytkownika **kolekcji obszarów roboczych** nazwa, i **klucz dostępu**. Aby pobrać te **portalu Azure**. Aby dowiedzieć się więcej na temat sposobu uzyskania Twojej **klucz dostępu**, zobacz [kluczy dostępu interfejsu API widoku Power BI](get-started.md#view-power-bi-api-access-keys) w wprowadzenie do programu Microsoft Power BI Embedded.

    ![Klawisze dostępu w portalu Azure](media/get-started-sample/azure-portal.png)
8. Skopiuj i Zapisz nowo utworzony **identyfikator obszaru roboczego** do użycia w dalszej części tego artykułu. Po **identyfikator obszaru roboczego** jest utworzony, można go znaleźć **portalu Azure**.

    ![Identyfikator obszaru roboczego w portalu Azure](media/get-started-sample/workspace-id.png)
9. Importowanie pliku PBIX do Twojej **obszaru roboczego**, wybierz opcję **6. Importowanie pliku PBIX pulpitu do istniejący obszar roboczy**. Jeśli nie masz PBIX pliku przydatną, możesz pobrać [Retail Analysis próbki PBIX](http://go.microsoft.com/fwlink/?LinkID=780547).
10. Jeśli zostanie wyświetlony monit, wprowadź przyjazną nazwę dla Twojego **zestawu danych**.

Powinna zostać wyświetlona odpowiedź, takich jak:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Jeśli plik PBIX zawiera wszystkie połączenia zapytania bezpośredniego, należy uruchomić opcję 7, aby zaktualizować ciągi połączenia.

W tym momencie masz raport programu Power BI PBIX zaimportowane do programu **obszaru roboczego**. Teraz zobaczmy, jak uruchomić **Power BI obszaru roboczego kolekcje** Pobierz wprowadzenie przykładową aplikację sieci web.

## <a name="run-the-sample-web-app"></a>Uruchamianie przykładowej aplikacji internetowej

Przykładowej aplikacji sieci web, to przykładowa aplikacja renderujący raporty zaimportowane do programu **obszaru roboczego**. Poniżej przedstawiono sposób konfigurowania przykładowej aplikacji sieci web.

1. W **osadzonych PowerBI** rozwiązania Visual Studio, kliknij prawym przyciskiem myszy **EmbedSample** aplikacji sieci web, a następnie wybierz pozycję **Ustaw jako projekt startowy**.
2. W **web.config**w **EmbedSample** aplikacji sieci web, Edytuj **appSettings**: **AccessKey**, **WorkspaceCollection** nazwa, i **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Uruchom **EmbedSample** aplikacji sieci web.

Po uruchomieniu **EmbedSample** aplikacji sieci web powinna zawierać lewym panelu nawigacyjnym **raporty** menu. Aby wyświetlić raport, należy zaimportować, rozwiń węzeł **raporty**i kliknij przycisk raport. Po zaimportowaniu [Retail Analysis próbki PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), przykładową aplikację sieci web będzie wyglądać następująco:

![Przykładowe nawigacji po lewej stronie w przykładowej aplikacji](media/get-started-sample/sample-left-nav.png)

Po kliknięciu raportu, **EmbedSample** aplikacji sieci web powinien wyglądać to:

![Przykładowy raport wyświetlania w aplikacji](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Eksploruj przykładowy kod

**Microsoft Power BI obszaru roboczego kolekcje** próbka jest przykładowej aplikacji sieci web, który pokazuje, jak zintegrować **usługi Power BI** raportów w aplikacji. Wzorzec projektowy Model-widok-kontroler (MVC) używa do zaprezentowania najlepszych rozwiązań. W tej sekcji przedstawiono przykładowy kod, który można sprawdzić w części **osadzonych usługi Power BI** rozwiązania aplikacji w sieci web. Wzorzec Model-widok-kontroler (MVC) oddziela modelowania domeny, prezentacji i akcji, w oparciu o dane wejściowe użytkownika do trzech osobnych klas: Model, widok i kontroli. Aby dowiedzieć się więcej na temat MVC, zobacz [Dowiedz się więcej o ASP.NET](http://www.asp.net/mvc).

**Microsoft Power BI obszaru roboczego kolekcje** przykładowy kod jest oddzielona w następujący sposób. Każda sekcja zawiera nazwę pliku w rozwiązaniu embedded.sln usługi Power BI, dzięki czemu można łatwo znaleźć kod w próbce.

> [!NOTE]
> W tej sekcji znajduje się podsumowanie przykładowy kod, który pokazuje, jak kod został zapisany. Aby wyświetlić kompletnego przykładu, załaduj rozwiązania embedded.sln usługi Power BI w programie Visual Studio.

### <a name="model"></a>Model

Próbki **ReportsViewModel** i **ReportViewModel**.

**ReportsViewModel.cs**: reprezentuje raportami programu Power BI.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: reprezentuje raportu Power BI.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### <a name="connection-string"></a>Parametry połączenia

Parametry połączenia muszą mieć następujący format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Przy użyciu wspólnej serwera i bazy danych atrybutów kończyć się niepowodzeniem. Na przykład: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Widok

**Widoku** zarządza wyświetlanie usługi Power BI **raporty** i usługi Power BI **raport**.

**Reports.cshtml**: iteracja **Model.Reports** utworzyć **ActionLink**. **ActionLink** składa się w następujący sposób:

| Część | Opis |
| --- | --- |
| Stanowisko |Nazwa raportu. |
| Ciąg zapytania |Łącze do raportu identyfikatora. |

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Ustaw **Model.AccessToken**, a wyrażenie Lambda **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### <a name="controller"></a>Kontroler

**DashboardController.cs**: tworzy przekazywanie PowerBIClient **tokenu aplikacji**. JSON tokenów sieci Web (JWT) są generowane na podstawie **klucza podpisywania** uzyskanie **poświadczenia**. **Poświadczenia** są używane do tworzenia wystąpienia **PowerBIClient**. Po utworzeniu wystąpienia **PowerBIClient**, można wywołać GetReports() i GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Zadanie<ActionResult> raportu (ciąg reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### <a name="integrate-a-report-into-your-app"></a>Integracji raportu w swojej aplikacji

Po utworzeniu **raport**, możesz użyć **IFrame** do osadzenia usługi Power BI **raport**. Oto fragment kodu z powerbi.js w **Microsoft Power BI obszaru roboczego kolekcje** próbki.

```
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<igrame style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Raporty filtru osadzonego w aplikacji

Osadzony raport można filtrować przy użyciu składni adresu URL. Aby to zrobić, należy dodać **$filter** parametr ciągu zapytania o **eq** operator do adresu url źródła iFrame z odpowiednim filtrem. Oto składnia zapytania filtru:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} nie może zawierać spacji ani znaków specjalnych. {fieldValue} akceptuje pojedyncze wartości kategorii.  

## <a name="see-also"></a>Zobacz także

[Typowe scenariusze kolekcji obszarów roboczych programu Microsoft Power BI](scenarios.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Tworzenie nowego raportu przy użyciu zestawu danych](create-report-from-dataset.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)
