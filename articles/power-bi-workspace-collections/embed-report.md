---
title: Osadzanie raportu w kolekcji obszarów roboczych usługi Azure Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak możesz osadzić raport, który znajduje się w kolekcji obszarów roboczych usługi Power BI w aplikacji.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: bdfaaf3ecd5beb7cea56f017006921a86ecfb3f6
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519265"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Osadzanie raportu w kolekcji obszarów roboczych usługi Power BI

Dowiedz się, jak możesz osadzić raport, który znajduje się w kolekcji obszarów roboczych usługi Power BI w aplikacji.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Przedstawiony zostanie sposób faktycznie osadzanie raportu w aplikacji. Zakłada się, że masz już raport, który istnieje w obszarze roboczym w kolekcji obszarów roboczych. Jeśli jeszcze nie zrobiono tego kroku, zobacz [wprowadzenie do kolekcji obszarów roboczych usługi Power BI](get-started.md).

.NET (C#) lub zestawu SDK środowiska Node.js oraz JavaScript, umożliwia łatwe tworzenie aplikacji za pomocą kolekcji obszarów roboczych usługi Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Za pomocą kluczy dostępu do używania interfejsów API REST

Aby wywołać interfejs API REST, należy przekazać klucz dostępu, którą możesz pobrać z witryny Azure portal dla kolekcji danego obszaru roboczego. Aby uzyskać więcej informacji, zobacz [wprowadzenie do kolekcji obszarów roboczych usługi Power BI](get-started.md).

## <a name="get-a-report-id"></a>Pobierz identyfikator raportu

Każdy token dostępu jest oparty na raporcie. Należy uzyskać identyfikator raportu dla raportu, który ma zostać osadzony. Można to zrobić na podstawie wywołania [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) interfejsu API REST. Spowoduje to zwrócenie identyfikatora raportu i adres url osadzania. Można to zrobić przy użyciu zestawu SDK .NET usługi Power BI lub bezpośredniego wywoływania interfejsu API REST.

### <a name="using-the-power-bi-net-sdk"></a>Przy użyciu zestawu SDK .NET usługi Power BI

Korzystając z zestawu .NET SDK, musisz utworzyć token poświadczenie, które opiera się na klucz dostępu, który jest pobierany z witryny Azure portal. Wymaga to, że instalujesz program [pakiet NuGet interfejsu API usługi Power BI](https://www.nuget.org/profiles/powerbi).

**Instalacja pakietu NuGet**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**Kod C#**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Bezpośrednie wywoływanie interfejsu API REST

```csharp
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>Tworzenie tokenu dostępu

Użyj kolekcji obszarów roboczych usługi Power BI zasilania osadzonych tokenów, które HMAC zalogowano tokenów sieci Web JSON. Tokeny są podpisane za pomocą klucza dostępu z kolekcji obszarów roboczych usługi Power BI. Osadzonych tokenów, domyślnie, są używane w celu zapewnienia dostępu tylko do odczytu do raportów w celu osadzenia w aplikacji. Osadzanie tokeny wystawione dla konkretnego raportu i powinny być skojarzone z adresem URL osadzania.

Tokeny dostępu należy utworzyć na serwerze, klucze dostępu są używane do logowania/szyfrowanie tokenów. Aby uzyskać informacje na temat tworzenia tokenu dostępu, zobacz [uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI](app-token-flow.md). Możesz również przejrzeć [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metody. Oto przykład to będzie wyglądać podobnie jak przy użyciu zestawu SDK .NET dla usługi Power BI.

Użyjesz tego Identyfikatora raportu, który pobranym wcześniej. Po utworzeniu tokenu osadzania następnie użyje klucza dostępu do generowania tokenu, który można użyć z punktu widzenia javascript. *Klasy PowerBIToken* wymaga zainstalowania [usługi Power BI Core NuGut pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalacja pakietu NuGet**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**Kod C#**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Dodawanie zakresów uprawnień do tokenów osadzania

Korzystając z tokenów osadzania, można ograniczyć użycie zasobów, które zapewniają dostęp do. Z tego powodu można wygenerować token z uprawnieniami o określonym zakresie. Aby uzyskać więcej informacji, zobacz [zakresów](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Osadzanie przy użyciu języka JavaScript

Po utworzeniu tokenu dostępu i identyfikator raportu możemy osadzić raport przy użyciu języka JavaScript. Wymaga to, że instalujesz program NuGet [pakietu usługi Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Po prostu będzie embedUrl https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Możesz użyć [przykład osadzania raportu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) w celu przetestowania funkcjonalności. Daje ona również przykłady kodu dla różnych operacji, które są dostępne.

**Instalacja pakietu NuGet**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**Kod JavaScript**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>Ustaw rozmiar osadzone elementy

Raport zostanie automatycznie osadzony na podstawie rozmiaru jego kontenera. Aby zastąpić domyślny rozmiar osadzonego elementu, po prostu Dodaj klasy atrybutu lub wbudowane style CSS dla szerokości i wysokości.

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Usługa Power BI JavaScript pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Pakiet NuGet interfejsu API usługi BI zasilania](https://www.nuget.org/profiles/powerbi)
[usługi Power BI Core NuGut pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Repozytorium Git języka CSharp usługi Power BI](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git w węźle usługi Power BI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
