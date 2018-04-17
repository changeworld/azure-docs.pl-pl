---
title: Przełącz między widokami i edytować tryb dla raportów w programie Power BI obszaru roboczego kolekcje | Dokumentacja firmy Microsoft
description: Informacje o sposobie przełączania między widoku i edytować tryb dla raportów w kolekcjach obszaru roboczego programu Power BI.
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
ms.openlocfilehash: 23a8c4f0dd626a623df56de9546258a23d549d1a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Przełącz między widokami i edytować tryb dla raportów w programie Power BI obszaru roboczego kolekcje

Informacje o sposobie przełączania między widoku i edytować tryb dla raportów w kolekcjach obszaru roboczego programu Power BI.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Tworzenie token dostępu

Należy utworzyć token dostępu, który daje możliwość zarówno wyświetlania i edytowania raportu. Aby edytować i zapisać raport, należy **Report.ReadWrite** token uprawnień. Aby uzyskać więcej informacji, zobacz [Authenticating i autoryzacji w kolekcjach obszaru roboczego programu Power BI](app-token-flow.md).

> [!NOTE]
> Dzięki temu można zapisać zmian do istniejącego raportu. Jeśli chcesz również funkcję obsługi **Zapisz jako**, należy podać dodatkowe uprawnienia. Aby uzyskać więcej informacji, zobacz [zakresy](app-token-flow.md#scopes).

```
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Osadź konfiguracji

Należy podać uprawnienia i viewMode, aby zobaczyć zapisywania przycisku w trybie edycji. Aby uzyskać więcej informacji, zobacz [osadzić szczegóły konfiguracji](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
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

Oznacza to, aby osadzić raport w trybie widoku na podstawie **viewMode** ustawiany **modeli. ViewMode.View**.

## <a name="view-mode"></a>Tryb wyświetlania

Następujący kod JavaScript umożliwia przełączanie w tryb widoku, jeśli jesteś w trybie edycji.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Tryb edycji

Następujący kod JavaScript służy do przełączania do trybu edycji, będąc w trybie.

```
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Usługa Power BI CSharp repozytorium Git](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git węzła usługi Power BI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)
