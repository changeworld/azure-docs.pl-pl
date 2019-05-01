---
title: Tworzenie nowego raportu z zestawu danych w kolekcji obszarów roboczych usługi Power BI | Dokumentacja firmy Microsoft
description: Teraz można tworzyć raporty usługi Power BI kolekcji obszarów roboczych z zestawu danych w swojej aplikacji.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: e7499345f03e3deedb8972b0d51e8e676cb6c982
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683452"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Tworzenie nowego raportu z zestawu danych w kolekcji obszarów roboczych usługi Power BI

Teraz można tworzyć raporty usługi Power BI kolekcji obszarów roboczych z zestawu danych w swojej aplikacji.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Metoda uwierzytelniania jest podobny do osadzania raportu. Jest ona oparta na tokeny dostępu, które są specyficzne dla zestawu danych. Tokeny używane dla witryny PowerBI.com są wydawane przez usługę Azure Active Directory (AAD). Tokeny kolekcji obszarów roboczych usługi Power BI zasilania są wydawane przez własną aplikację.

Podczas tworzenia raportu osadzone tokeny wystawione dotyczą konkretnego zestawu danych. Tokeny powinna być skojarzona z adres URL osadzania w tym samym elemencie, aby upewnić się, że każde z nich ma unikatowy token. Aby utworzyć raport osadzone *Dataset.Read i Workspace.Report.Create* zakresy musi być podana w tokenie dostępu.

## <a name="create-access-token-needed-to-create-new-report"></a>Tworzenie tokenu dostępu wymagane do utworzenia nowego raportu

Kolekcje obszarów roboczych usługi Power BI Użyj osadzania tokenu, który jest HMAC podpisany tokenów sieci Web JSON. Tokeny są podpisane za pomocą klucza dostępu z kolekcji obszarów roboczych usługi Power BI. Osadzonych tokenów, domyślnie, są używane w celu zapewnienia dostępu tylko do odczytu do raportów w celu osadzenia w aplikacji. Osadzanie tokeny wystawione dla konkretnego raportu i powinny być skojarzone z adresem URL osadzania.

Tokeny dostępu należy utworzyć na serwerze, klucze dostępu są używane do logowania/szyfrowanie tokenów. Aby uzyskać informacje na temat tworzenia tokenu dostępu, zobacz [uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI](app-token-flow.md). Możesz również przejrzeć [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metody. Oto przykład to będzie wyglądać podobnie jak przy użyciu zestawu SDK .NET dla usługi Power BI.

W tym przykładzie mamy nasz identyfikator zestawu danych, które chcemy utworzyć nowy raport na. Należy również dodać zakresy dla *Dataset.Read i Workspace.Report.Create*.

*Klasy PowerBIToken* wymaga zainstalowania [usługi Power BI Core NuGut pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalacja pakietu NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**Kod C#**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Tworzenie nowego pustego raportu

Aby utworzyć nowy raport, należy podać Utwórz konfigurację. Powinno to obejmować token dostępu i embedURL datasetID, który chcemy, aby utworzyć raport względem. Wymaga to, że instalujesz program nuget [pakietu usługi Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Po prostu będzie embedUrl https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Możesz użyć [przykład osadzania raportu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) w celu przetestowania funkcjonalności. Daje ona również przykłady kodu dla różnych operacji, które są dostępne.

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

Wywoływanie *powerbi.createReport()* sprawia, że pojawiają się w pustej kanwy w trybie edycji *div* elementu.

![Nowy pusty raport](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Zapisz nowe raporty

Raport nie zostanie utworzony, dopóki nie zostanie wywołana **Zapisz jako** operacji. Można to zrobić z menu Plik lub JavaScript.

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
> Nowy raport jest tworzony dopiero po **Zapisz jako** jest wywoływana. Po zapisaniu, obszar roboczy w dalszym ciągu będzie zestawu danych w trybie edycji i nie raportu. Należy ponownie załadować nowy raport, tak jak w przypadku dowolnego innego raportu.

![Menu — Zapisz plik jako](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Załaduj nowy raport

W celu interakcji z nowy raport, który należy ją osadzić w taki sam sposób, w aplikacji osadza zwykłego raportu, co oznacza, nowego tokenu musi zostać wystawiony dla nowego raportu, a następnie wywołaj metodę osadzania.

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

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Nie można załadować nowy raport za pomocą "zapisane" zdarzenia i zautomatyzować Zapisz

Aby zautomatyzować proces "Zapisz jako" i następnie ładowania nowy raport, można skorzystać "zapisane" zdarzenia. To zdarzenie jest wywoływane podczas zapisywania operacja została zakończona i zwraca obiektem Json zawierającym nowe reportId, nazwę raportu, stare reportId (jeśli jest to była jedna) i jeśli operacja zakończyła się zapisywanie jako lub zapisać.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Aby zautomatyzować proces może nasłuchiwać zdarzeń "zapisane", podjąć nowe reportId, Utwórz nowy token i osadzić nowy raport z nim.

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
[Usługa Power BI Core NuGut pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Usługa Power BI JavaScript pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
