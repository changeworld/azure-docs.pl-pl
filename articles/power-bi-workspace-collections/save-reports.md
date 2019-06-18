---
title: Zapisywanie raportów w kolekcji obszarów roboczych usługi Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapisywanie raportów w ramach kolekcji obszarów roboczych usługi Power BI. Wymaga to odpowiednie uprawnienia, aby móc pracować pomyślnie.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b61abee3382697d50b9a18de763c8a4d01e1ccba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701865"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Zapisywanie raportów w kolekcji obszarów roboczych usługi Power BI

Dowiedz się, jak zapisywanie raportów w ramach kolekcji obszarów roboczych usługi Power BI. Zapisywanie raportów wymaga odpowiednich uprawnień, aby działała poprawnie.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

W ramach kolekcji obszarów roboczych usługi Power BI możesz edytować istniejące raporty i zapisywać je. Można również utworzyć nowy raport i zapisać jako nowy raport, aby go utworzyć.

Aby zapisać raport, najpierw musisz utworzyć token dla określonego raportu z zakresami prawa:

* Aby włączyć Zapisz Report.ReadWrite zakres jest wymagany
* Aby włączyć Zapisz jako, Report.Read i Workspace.Report.Copy zakresy są wymagane
* Aby włączyć Zapisz, a następnie Zapisz jako, Report.ReadWrite i Workspace.Report.Copy są wymagane

Odpowiednio w celu umożliwienia po prawej stronie save/Zapisywanie jako przyciski w menu Plik, konieczne będzie podanie odpowiednich uprawnień w konfiguracji osadzania po osadzeniu raportu:

* models.Permissions.ReadWrite
* modele. Permissions.Copy
* models.Permissions.All

> [!NOTE]
> Token dostępu wymaga także odpowiednich zakresach. Aby uzyskać więcej informacji, zobacz [zakresy](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Osadzanie raportu w trybie edycji

Załóżmy, że chcesz osadzić raport w trybie edycji w aplikacji, więc po prostu przekazać odpowiednie właściwości w konfiguracji osadzania, i wywoływać powerbi.embed(). Podaj uprawnienia i viewMode, aby można było zobaczyć zapisywania i Zapisz jako przyciski w trybie edycji. Aby uzyskać więcej informacji, zobacz [osadzić szczegóły konfiguracji](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Na przykład w języku JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Teraz raport zostanie osadzony w aplikacji w trybie edycji.

## <a name="save-report"></a>Zapisz raport

Po osadzanie raportu w trybie edycji, za pomocą prawidłowy token i uprawnienia, można zapisać raport, w menu Plik lub z poziomu języka javascript:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Zapisz jako

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
> Tylko po *Zapisz jako* jest utworzony nowy raport. Po zapisywanie obszar roboczy jest nadal wyświetlana stare raportów w trybie edycji, a nie nowy raport. Osadzić nowy raport, który został utworzony. Osadzanie nowy raport wymaga nowy token dostępu, utworzonemu na raport.

Następnie należy załadować nowy raport po *Zapisz jako*. Trwa ładowanie nowego raportu jest podobny do osadzania każdego raportu.

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

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Tworzenie nowego raportu przy użyciu zestawu danych](create-report-from-dataset.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)

