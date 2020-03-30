---
title: Nazwy serwerów aliasów usług Azure Analysis Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć aliasy nazw serwerów usług Azure Analysis Services. Użytkownicy mogą następnie połączyć się z serwerem z krótszą nazwą aliasu zamiast nazwy serwera.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572292"
---
# <a name="alias-server-names"></a>Nazwy serwerów aliasów

Za pomocą aliasu nazwy serwera użytkownicy mogą łączyć się z serwerem usług Azure Analysis Services z krótszym *aliasem* zamiast nazwy serwera. Podczas łączenia się z aplikacji klienckiej alias jest określony jako punkt końcowy przy użyciu formatu protokołu **link://.** Punkt końcowy następnie zwraca rzeczywistą nazwę serwera w celu nawiązania połączenia.

Nazwy serwerów aliasów są dobre dla:

- Migrowanie modeli między serwerami bez wpływu na użytkowników. 
- Przyjazne nazwy serwerów są łatwiejsze do zapamiętania dla użytkowników. 
- Kieruj użytkowników na różne serwery o różnych porach dnia. 
- Kierować użytkowników w różnych regionach do wystąpień, które są geograficznie bliżej, na przykład podczas korzystania z usługi Azure Traffic Manager. 

Każdy punkt końcowy HTTPS, który zwraca prawidłową nazwę serwera usług Azure Analysis Services może służyć jako alias. Punkt końcowy musi obsługiwać protokół HTTPS za pośrednictwem portu 443 i nie można określić portu w identyfikatorze URI.

![Alias przy użyciu formatu łącza](media/analysis-services-alias/aas-alias-browser.png)

Podczas łączenia się z klientem nazwa serwera aliasów jest wprowadzana przy użyciu formatu protokołu **link://.** Na przykład w programie Power BI Desktop:

![Połączenie z programem Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Tworzenie aliasu

Aby utworzyć alias punktu końcowego, można użyć dowolnej metody, która zwraca prawidłową nazwę serwera usług Azure Analysis Services. Na przykład odwołanie do pliku w usłudze Azure Blob Storage zawierającego rzeczywistą nazwę serwera lub utworzyć i opublikować ASP.NET aplikacji formularzy sieci Web.

W tym przykładzie w programie Visual Studio zostanie utworzona ASP.NET aplikacji formularzy sieci Web. Odwołanie do strony wzorcowej i formant użytkownika są usuwane ze strony Default.aspx. Zawartość Default.aspx są po prostu następujące dyrektywy strony:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Zdarzenie Page_Load w Default.aspx.cs używa metody Response.Write() do zwrócenia nazwy serwera usług Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Zobacz też

[Biblioteki klientów](analysis-services-data-providers.md)   
[Łączenie z programu Power BI Desktop](analysis-services-connect-pbi.md)
