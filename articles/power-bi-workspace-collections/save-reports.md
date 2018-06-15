---
title: Zapisywanie raportów w kolekcjach obszaru roboczego programu Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zapisywanie raportów w kolekcjach obszaru roboczego programu Power BI. Wymaga odpowiednich uprawnień, aby działało poprawnie.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: c5512584531c9f5c8a13e9a50161eb6b5a1f8a7b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31411220"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Zapisywanie raportów w kolekcjach obszaru roboczego programu Power BI

Dowiedz się, jak zapisywanie raportów w kolekcjach obszaru roboczego programu Power BI. Zapisywanie raportów wymaga odpowiednich uprawnień, aby działało poprawnie.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

W kolekcjach obszaru roboczego programu Power BI możesz edytować istniejących raportów i zapisz je. Można również utworzyć nowy raport i zapisać jako nowy raport, aby go utworzyć.

Aby zapisać raport, należy najpierw utworzyć token dla określonego raportu z prawej zakresów:

* Aby włączyć Zapisz Report.ReadWrite zakres jest wymagany
* Aby włączyć Zapisz jako, Report.Read i Workspace.Report.Copy zakresy są wymagane
* Aby włączyć Zapisz, a następnie Zapisz jako, Report.ReadWrite i Workspace.Report.Copy są wymagane

Odpowiednio w celu umożliwienia uprawnienie do zapisywania/Zapisz jako przyciski w menu Plik, należy zapewnić odpowiednie uprawnienie w konfiguracji osadzania osadzenia raportu:

* modele. Permissions.ReadWrite
* modele. Permissions.Copy
* modele. Permissions.All

> [!NOTE]
> Token dostępu musi również odpowiedniego zakresów. Aby uzyskać więcej informacji, zobacz [zakresy](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Osadzanie raportu w trybie edycji

Załóżmy, że aby osadzić raport w trybie edycji wewnątrz aplikacji, więc wystarczy przekazać właściwości prawa w konfiguracji osadzania i wywołać powerbi.embed(). Należy podać uprawnienia i viewMode, aby wyświetlić zapisywania i Zapisz jako przyciski w trybie edycji. Aby uzyskać więcej informacji, zobacz [osadzić szczegóły konfiguracji](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

Na przykład w języku JavaScript:

```
   <div id="reportContainer"></div>

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
```

Teraz raport zostanie osadzony w aplikacji w trybie edycji.

## <a name="save-report"></a>Zapisywanie raportu

Po osadzanie raportu w trybie edycji z prawego token i uprawnień, można zapisać raport, w menu Plik lub z poziomu języka javascript:

```
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Zapisz jako

```
// Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Tylko po *Zapisz jako* jest utworzony nowy raport. Po zapisywanie obszar roboczy jest nadal wyświetlany starego raportu w trybie edycji, a nie nowy raport. Osadzić nowy raport, który został utworzony. Osadzanie nowy raport wymaga nowy token dostępu tworzonych na raport.

Następnie należy załadować nowy raport po *Zapisz jako*. Trwa ładowanie nowego raportu jest podobny do osadzania żadnych raportów.

```
<div id="reportContainer"></div>
  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
```

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Tworzenie nowego raportu przy użyciu zestawu danych](create-report-from-dataset.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Program Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)

