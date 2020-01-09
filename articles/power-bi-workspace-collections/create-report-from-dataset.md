---
title: Tworzenie raportu z kolekcji obszarów roboczych Power BI
description: Raporty kolekcji obszarów roboczych Power BI można teraz tworzyć z zestawu danych w aplikacji.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bcc6044d0f0f5270f81a619e4d1ad71ea35cc170
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427053"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Tworzenie nowego raportu z zestawu danych w Power BI kolekcje obszarów roboczych

Raporty kolekcji obszarów roboczych Power BI można teraz tworzyć z zestawu danych w aplikacji.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Metoda uwierzytelniania jest podobna do osadzania raportu. Jest on oparty na tokenach dostępu, które są specyficzne dla zestawu danych. Tokeny używane na potrzeby PowerBI.com są wystawiane przez Azure Active Directory (AAD). Tokeny kolekcji obszarów roboczych Power BI są wystawiane przez własną aplikację.

W przypadku tworzenia osadzonego raportu tokeny wystawione dotyczą określonego zestawu danych. Tokeny powinny być skojarzone z adresem URL osadzania w tym samym elemencie, aby upewnić się, że każdy z nich ma unikatowy token. Aby można było utworzyć osadzony raport, należy podać w tokenie dostępu *zestaw zakresów DataSet. Read i Workspace. Create* .

## <a name="create-access-token-needed-to-create-new-report"></a>Utwórz token dostępu wymagany do utworzenia nowego raportu

Power BI kolekcje obszarów roboczych używają tokenu osadzania, który jest tokenami sieci Web JSON podpisanymi przez HMAC. Tokeny są podpisane przy użyciu klucza dostępu z kolekcji obszarów roboczych Power BI. Domyślnie tokeny osadzania są używane do zapewnienia dostępu tylko do odczytu do raportu w celu osadzenia w aplikacji. Tokeny osadzania są wystawiane dla określonego raportu i powinny być skojarzone z adresem URL osadzania.

Tokeny dostępu należy tworzyć na serwerze, ponieważ klucze dostępu są używane do podpisywania/szyfrowania tokenów. Aby uzyskać informacje na temat sposobu tworzenia tokenu dostępu, zobacz [uwierzytelnianie i autoryzacja przy użyciu kolekcji obszarów roboczych Power BI](app-token-flow.md). Możesz również przejrzeć metodę [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) . Oto przykład tego, jak będzie wyglądać tak jak w przypadku Power BI.

W tym przykładzie mamy identyfikator zestawu danych, dla którego chcemy utworzyć nowy raport. Konieczne jest również dodanie zakresów dla *elementu DataSet. Read i Workspace. Report. Create*.

*Klasa PowerBIToken* wymaga zainstalowania [pakietu Power BI Core NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalacja pakietu NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#kodu**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Utwórz nowy pusty raport

Aby można było utworzyć nowy raport, należy podać konfigurację tworzenia. Powinno to obejmować token dostępu, embedURL i datasetID, dla których chcemy utworzyć raport. Wymaga to zainstalowania [pakietu nuget Power BI języka JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). EmbedUrl będzie po prostu https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> W celu przetestowania funkcjonalności można użyć [przykładu Osadź raport JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) . Zawiera również przykłady kodu dla różnych dostępnych operacji.

**Instalacja pakietu NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Kod JavaScript**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Wywołanie metody *PowerBI. GetReport ()* powoduje, że w trybie edycji pojawia się pusta Kanwa w obrębie elementu *DIV* .

![Nowy pusty raport](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Zapisz nowe raporty

Raport nie zostanie utworzony do momentu wywołania operacji **Zapisz jako** . Można to zrobić z menu plik lub JavaScript.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Nowy raport jest tworzony dopiero po wywołaniu metody **Save as** . Po zapisaniu Kanwa nadal będzie wyświetlać zestaw danych w trybie edycji, a nie raport. Należy ponownie załadować nowy raport, taki jak każdy inny raport.

![Menu plik — Zapisz jako](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Załaduj nowy raport

Aby można było korzystać z nowego raportu, należy go osadzić w taki sam sposób, w jaki aplikacja domyślnie osadza raport zwykły, co oznacza, że nowy token musi zostać wystawiony dla nowego raportu, a następnie wywołać metodę osadzania.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatyzowanie zapisywania i ładowania nowego raportu przy użyciu zdarzenia "zapisane"

Aby zautomatyzować proces operacji "Zapisz jako", a następnie załadować nowy raport, możesz użyć zdarzenia "zapisane". To zdarzenie jest wywoływane, gdy operacja zapisywania zostanie ukończona i zwraca obiekt JSON zawierający nową identyfikatorem ReportID, nazwę raportu, stary identyfikatorem ReportID (jeśli taki istnieje) i jeśli operacja została zapisana lub zapisywana.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Aby zautomatyzować proces, można nasłuchiwać "zapisanego" zdarzenia, pobrać nowe identyfikatorem ReportID, utworzyć nowy token i osadzić w nim nowy raport.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Zapisywanie raportów](save-reports.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI podstawowy pakiet NuGut](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI pakiet JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
