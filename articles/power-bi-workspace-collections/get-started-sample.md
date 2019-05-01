---
title: Rozpoczynanie pracy przy użyciu przykładu
description: W tym artykule wprowadzimy należy do przykładu wprowadzenie get kolekcji obszarów roboczych usługi Power BI.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 6cf78bf9c83df0a1255c14499bbd2036a2be5ed6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714504"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Rozpoczęcie pracy z przykładem kolekcji obszarów roboczych usługi Power BI

Za pomocą **kolekcji obszarów roboczych pakietu Microsoft Power BI**, można zintegrować raporty usługi Power BI bezpośrednio z aplikacjami internetowymi lub mobilnymi. W tym artykule będziemy wprowadzenia do **kolekcji obszarów roboczych usługi Power BI** przykładowe wprowadzenie get.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Zanim przejdziemy dalej, którym chcesz zapisać następujące zasoby: Ułatwiają one podczas integrowanie raportów usługi Power BI przykładową aplikację i własne aplikacje za.

* [Przykładowa aplikacja internetowa obszaru roboczego](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Dokumentacja usługi Power BI obszar roboczy kolekcji z interfejsu API](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Usługa Power BI .NET SDK](https://go.microsoft.com/fwlink/?LinkId=746472) (dostępne za pośrednictwem NuGet)
* [Przykład osadzania przy użyciu języka JavaScript raport](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Zanim będzie można skonfigurować i uruchom kolekcje obszarów roboczych usługi Power BI uzyskać pracę próbki, należy utworzyć co najmniej jeden **kolekcji obszarów roboczych** w subskrypcji platformy Azure. Aby dowiedzieć się, jak utworzyć **kolekcji obszarów roboczych** w witrynie Azure portal, zobacz [wprowadzenie do kolekcji obszarów roboczych usługi Power BI](get-started.md).

## <a name="configure-the-sample-app"></a>Konfigurowanie przykładowej aplikacji

Przejdźmy teraz przez proces konfigurowania środowiska deweloperskiego Visual Studio, dostęp do składników potrzebnych do uruchomienia aplikacji przykładowej.

1. Pobierz i Rozpakuj [kolekcji obszarów roboczych usługi Power BI — integrowanie raportu w aplikacji sieci web](https://go.microsoft.com/fwlink/?LinkId=761493) próbki w witrynie GitHub.
2. Otwórz **embedded.sln usługi Power BI** w programie Visual Studio. Może być konieczne wykonanie **pakiet aktualizacji** polecenia w konsoli Menedżera pakietów NuGet, aby można było zaktualizować pakiety wykorzystanych w tym rozwiązaniu.
3. Skompiluj rozwiązanie.
4. Uruchom **ProvisionSample** aplikację konsoli. Przykładowa aplikacja konsoli możesz aprowizować obszaru roboczego i importowanie pliku PBIX.
5. Aby zainicjować obsługę nowej **obszaru roboczego**, wybierz opcję 1, **zarządzanie kolekcją**, a następnie wybierz opcję 6, **Aprowizacja nowego obszaru roboczego**
6. Aby zaimportować nowy **raportu**, wybierz opcję 2, **raport zarządzania**, a następnie wybierz opcję 3, **pliku pulpitu zaimportować plik PBIX do obszaru roboczego**.

7. Wprowadź swoje **kolekcji obszarów roboczych** nazwy i **klucz dostępu**. Możesz je pobrać w **witryny Azure portal**. Aby dowiedzieć się więcej na temat sposobu uzyskania usługi **klucz dostępu**, zobacz [kluczy dostępu interfejsu API dla usługi Power BI, widok](get-started.md#view-power-bi-api-access-keys) w rozpocząć korzystanie z usługi Microsoft Power BI Embedded.

    ![Klucze dostępu w witrynie Azure portal](media/get-started-sample/azure-portal.png)
8. Skopiuj i Zapisz nowo utworzony **identyfikator obszaru roboczego** w dalszej części tego artykułu. Po **identyfikator obszaru roboczego** jest utworzone, możesz go znaleźć **witryny Azure portal**.

    ![Identyfikator obszaru roboczego w witrynie Azure portal](media/get-started-sample/workspace-id.png)
9. Aby zaimportować plik PBIX do usługi **obszaru roboczego**, wybierz opcję **6. Importowanie pliku PBIX pulpitu do istniejącego obszaru roboczego**. Jeśli nie masz PBIX z pliku pod ręką, możesz pobrać [przykładem analizy sprzedaży PBIX](https://go.microsoft.com/fwlink/?LinkID=780547).
10. Jeśli zostanie wyświetlony monit, wprowadź przyjazną nazwę dla Twojej **zestawu danych**.

Powinny pojawić się odpowiedź podobna:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Jeśli plik PBIX zawiera wszystkie połączenia zapytania bezpośredniego, należy uruchomić opcję 7, aby zaktualizować parametry połączenia.

W tym momencie masz raport usługi Power BI PBIX zaimportowane do Twojej **obszaru roboczego**. Teraz Przyjrzyjmy się sposobu uruchamiania **kolekcji obszarów roboczych usługi Power BI** uzyskać uruchomiono przykładową aplikację sieci web.

## <a name="run-the-sample-web-app"></a>Uruchamianie przykładowej aplikacji internetowej

Przykład aplikacji sieci web jest przykładowej aplikacji, która renderuje raporty zaimportowane do Twojej **obszaru roboczego**. Poniżej przedstawiono sposób konfigurowania przykładowej aplikacji sieci web.

1. W **osadzonych w usłudze Power BI** rozwiązania Visual Studio kliknij prawym przyciskiem myszy **EmbedSample** aplikacji sieci web, a następnie wybierz **Ustaw jako projekt startowy**.
2. W **web.config**w **EmbedSample** aplikacji sieci web, Edytuj **appSettings**: **AccessKey**, **WorkspaceCollection** nazwy i **WorkspaceId**.

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Uruchom **EmbedSample** aplikacji sieci web.

Po uruchomieniu **EmbedSample** aplikacji sieci web powinien zawierać w lewym panelu nawigacyjnym **raporty** menu. Aby wyświetlić raport zaimportowany, rozwiń węzeł **raporty**i kliknij raport. Jeśli został zaimportowany [przykładem analizy sprzedaży PBIX](https://go.microsoft.com/fwlink/?LinkID=780547), przykładową aplikację sieci web będzie wyglądać następująco:

![Przykładowe nawigacji po lewej stronie w przykładowej aplikacji](media/get-started-sample/sample-left-nav.png)

Po kliknięciu raportu, **EmbedSample** aplikacji sieci web powinien wyglądać mniej więcej to:

![Przykładowy raport, wyświetlanie w aplikacji](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Poznawanie przykładowego kodu

**Kolekcji obszarów roboczych pakietu Microsoft Power BI** próbka jest przykładowa aplikacja sieci web, pokazujący, jak zintegrować **usługi Power BI** raporty w swojej aplikacji. Wzorzec projektowy Model-View-Controller (MVC) używa do zademonstrowania najlepszych rozwiązań. W tej sekcji przedstawiono przykładowy kod, który umożliwia zapoznanie się w części **osadzonych w usłudze Power BI** rozwiązania aplikacji w sieci web. Wzorzec Model-View-Controller (MVC) oddziela modelowanie domeny, prezentacji i akcji, w oparciu o dane wejściowe użytkownika w trzech osobnych klas: Model, widok i kontroli. Aby dowiedzieć się więcej na temat MVC, zobacz [Dowiedz się więcej o programie ASP.NET](https://www.asp.net/mvc).

**Kolekcji obszarów roboczych pakietu Microsoft Power BI** przykładowy kod jest oddzielona w następujący sposób. Każda sekcja zawiera nazwę pliku w rozwiązaniu embedded.sln usługi Power BI, aby mógł łatwo znaleźć kod w przykładzie.

> [!NOTE]
> W tej sekcji przedstawiono podsumowanie elementów, przykładowy kod, który pokazuje, jak kod został zapisany. Aby wyświetlić pełny przykład, załaduj rozwiązanie embedded.sln usługi Power BI w programie Visual Studio.

### <a name="model"></a>Modelowanie

Przykład obejmuje **ReportsViewModel** i **ReportViewModel**.

**ReportsViewModel.cs**: Reprezentuje raporty usługi Power BI.

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs**: Reprezentuje raportu usługi Power BI.

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>Parametry połączenia

Parametry połączenia muszą być w następującym formacie:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Za pomocą wspólnego serwera i bazy danych atrybutów kończyć się niepowodzeniem. Na przykład: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Widok

**Widoku** zarządza wyświetlania usługi Power BI **raporty** i usługi Power BI **raportu**.

**Reports.cshtml**: Iteracja **Model.Reports** utworzyć **ActionLink**. **ActionLink** składa się w następujący sposób:

| Część | Opis |
| --- | --- |
| Tytuł |Nazwa raportu. |
| Ciąg zapytania |Link do raportu identyfikatora. |
```cshtml
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
```
Report.cshtml: Ustaw **Model.AccessToken**i wyrażenie Lambda **PowerBIReportFor**.

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>Kontroler

**DashboardController.cs**: Tworzy przekazywanie PowerBIClient **tokenu aplikacji**. JSON Web Token (token JWT) jest generowany na podstawie **klucza podpisywania** można pobrać **poświadczenia**. **Poświadczenia** są używane do tworzenia instancji **PowerBIClient**. Po utworzeniu wystąpienia **PowerBIClient**, można wywołać GetReports() i GetReportsAsync().


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
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
```

Zadanie\<ActionResult > Raport (reportId ciągu)

```csharp
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
```

### <a name="integrate-a-report-into-your-app"></a>Integrowanie raportu w aplikacji

Po utworzeniu **raportu**, możesz użyć **IFrame** do osadzania usługi Power BI **raportu**. Poniżej przedstawiono fragment kodu z powerbi.js w **kolekcji obszarów roboczych pakietu Microsoft Power BI** próbki.

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtrować raporty osadzone w aplikacji

Osadzony raport można filtrować przy użyciu składni adresu URL. Aby to zrobić, należy dodać **$filter** parametr ciągu przy użyciu zapytania **eq** operatora do adresu url elementu iFrame src przy użyciu określonego filtru. Oto składnia zapytania filtru:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} nie może zawierać spacji ani znaków specjalnych. {fieldValue} akceptuje pojedyncze wartości kategorii.  

## <a name="see-also"></a>Zobacz także

[Typowe scenariusze kolekcji obszarów roboczych pakietu Microsoft Power BI](scenarios.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Tworzenie nowego raportu przy użyciu zestawu danych](create-report-from-dataset.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
