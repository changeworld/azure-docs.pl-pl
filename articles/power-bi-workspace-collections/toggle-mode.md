---
title: Przełączanie między trybami wyświetlania i edycji raportów
description: Dowiedz się, jak przełączać się między trybami wyświetlania i edytowania raportów w obszarze Power BI kolekcje obszarów roboczych.
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b2696560b5d5013fe337b51ec61cbfac9e512610
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357668"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Przełączanie między trybami wyświetlania i edycji raportów w Power BI kolekcje obszarów roboczych

Dowiedz się, jak przełączać się między trybami wyświetlania i edytowania raportów w obszarze Power BI kolekcje obszarów roboczych.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Tworzenie tokenu dostępu

Należy utworzyć token dostępu, który umożliwia wyświetlanie i edytowanie raportu. Do edytowania i zapisywania raportu potrzebne jest uprawnienie **Zgłoś. ReadWrite** . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie i autoryzacja w Power BI kolekcje obszarów roboczych](app-token-flow.md).

> [!NOTE]
> Pozwala to edytować i zapisywać zmiany w istniejącym raporcie. W przypadku użycia również funkcji **Zapisz jako**, należy podać dodatkowe uprawnienia. Aby uzyskać więcej informacji, zobacz [zakresy](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Osadź konfigurację

Aby wyświetlić przycisk Zapisz w trybie edycji, należy podać uprawnienia i element ViewMode. Aby uzyskać więcej informacji, zobacz [Osadź szczegóły konfiguracji](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details).

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
    </script>
```

Wskazuje to na osadzenie raportu w trybie widoku w oparciu o zestaw **ViewMode** ustawiony na **models. ViewMode. View**.

## <a name="view-mode"></a>Tryb widoku

Aby przełączyć się do trybu widoku, można użyć poniższego kodu JavaScript.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Tryb edycji

Aby przełączyć się do trybu edycji, można użyć poniższego kodu JavaScript.

```javascript
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
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI — CSharp repozytorium git](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git w węźle usługi PowerBI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
