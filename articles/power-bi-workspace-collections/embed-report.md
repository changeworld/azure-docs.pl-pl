---
title: Osadzanie raportu w kolekcjach obszaru roboczego programu Azure Power BI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak osadzanie raportu, który znajduje się w kolekcji programu Power BI obszaru roboczego do aplikacji.
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
ms.openlocfilehash: b6fa46b1cf3a251d6116e7de6ef41a9e6d265c29
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31410356"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Osadzanie raportu w kolekcjach obszaru roboczego programu Power BI

Dowiedz się, jak osadzanie raportu, który znajduje się w kolekcji programu Power BI obszaru roboczego do aplikacji.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Firma Microsoft będzie wyglądać jak faktycznie osadzanie raportu w aplikacji. Jest to zakładając, że masz już raport, który istnieje w obszarze roboczym w kolekcji obszaru roboczego. Jeśli jeszcze nie zostało to jeszcze zrobione tego kroku, zobacz [wprowadzenie w usłudze Power BI obszaru roboczego kolekcje](get-started.md).

.NET (C#) lub zestaw Node.js SDK oraz JavaScript, umożliwia łatwe tworzenie aplikacji z kolekcjami obszaru roboczego programu Power BI.

## <a name="using-the-access-keys-to-use-rest-apis"></a>Przy użyciu kluczy dostępu do używania interfejsów API REST

Aby można było wywołać interfejsu API REST, można przekazać klucza dostępu, który można pobrać z portalu Azure dla kolekcji danego obszaru roboczego. Aby uzyskać więcej informacji, zobacz [wprowadzenie w usłudze Power BI obszaru roboczego kolekcje](get-started.md).

## <a name="get-a-report-id"></a>Pobierz identyfikator raportu

Każdy token dostępu jest oparty na raport. Należy uzyskać identyfikator danego raportu dla raportu, który ma zostać osadzony. Można to zrobić na podstawie wywołań [Get Reports](https://msdn.microsoft.com/library/azure/mt711510.aspx) interfejsu API REST. Zwróci identyfikator raportu i osadzania adresu url. Można to zrobić przy użyciu zestawu .NET SDK Power BI lub bezpośrednie wywoływanie interfejsu API REST.

### <a name="using-the-power-bi-net-sdk"></a>Przy użyciu zestawu .NET SDK Power BI

Podczas korzystania z zestawu .NET SDK, należy utworzyć token poświadczeniami, które jest oparte na kluczu dostępu, który można pobrać z portalu Azure. Wymaga to zainstalowania [pakietu NuGet interfejsu API usługi Power BI](https://www.nuget.org/profiles/powerbi).

**Instalacja pakietu NuGet**

```
Install-Package Microsoft.PowerBI.Api
```

**Kod w języku C#**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>Bezpośrednie wywoływanie interfejsu API REST

```
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

## <a name="create-an-access-token"></a>Utwórz token dostępu

Power BI obszaru roboczego kolekcje użycia osadzaj tokenów, które są HMAC podpisany tokenów sieci Web JSON. Tokeny są podpisane za pomocą klucza dostępu z kolekcji Power BI obszaru roboczego. Osadzaj tokenów, domyślnie, są używane w celu zapewnienia dostępu tylko do odczytu do raportu w celu osadzenia w aplikacji. Osadź tokeny są wydawane dla określonego raportu i powinna być skojarzona z adresem URL osadzania.

Tokeny dostępu należy utworzyć na serwerze jako klucze dostępu są używane do logowania/szyfrowania tokenów. Aby uzyskać informacje na temat tworzenia tokenu dostępu, zobacz [Authenticating i autoryzacji z kolekcjami obszaru roboczego programu Power BI](app-token-flow.md). Można również przejrzeć [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metody. Oto przykład jak to będzie wyglądać przy użyciu zestawu .NET SDK dla usługi Power BI.

Użycie Identyfikatora raport, który został wcześniej pobrany. Po utworzeniu token osadzania następnie użyjesz klucz dostępu do generowania tokenu, którego można używać z punktu widzenia javascript. *Klasy PowerBIToken* wymaga zainstalowania [Power BI Core NuGut pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**Instalacja pakietu NuGet**

```
Install-Package Microsoft.PowerBI.Core
```

**Kod w języku C#**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Dodawanie zakresy uprawnień do osadzenia tokenów

Osadź tokeny, można ograniczyć użycie zasobów, które zapewniają dostęp do. Z tego powodu można wygenerować token z zakresu uprawnień. Aby uzyskać więcej informacji, zobacz [zakresów](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>Osadź przy użyciu języka JavaScript

Po umieszczeniu token dostępu i identyfikator raportu, możemy osadzić raport przy użyciu języka JavaScript. Wymaga to zainstalowania NuGet [pakietu Power BI JavaScript](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/). Po prostu będzie embedUrl https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Można użyć [próbki osadzić raport JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/) Aby przetestować funkcje. Udostępnia także przykłady kodu dla różnych operacji, które są dostępne.

**Instalacja pakietu NuGet**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**Kod JavaScript**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>Ustaw rozmiar elementów osadzonych

Raport zostanie automatycznie osadzony na podstawie rozmiaru swojego kontenera. Aby zastąpić domyślny rozmiar element osadzony, po prostu Dodaj klasy atrybutu lub wbudowane style CSS szerokość i wysokość.

## <a name="see-also"></a>Zobacz także

[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Pakiet NuGet interfejsu API usługi BI zasilania](https://www.nuget.org/profiles/powerbi)
[Power BI Core NuGut pakietu](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Usługa Power BI CSharp repozytorium Git](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git węzła usługi Power BI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)
