---
title: Usługa Azure Analysis Services alias serwerów nazw | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia i używania aliasów nazwy serwera.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 66e6b4713591f099769543a75dcddec34f3d2e2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534321"
---
# <a name="alias-server-names"></a>Alias serwera nazw

Za pomocą aliasu nazwy serwera, użytkownicy mogą nawiązywać połączenie z serwerem usług Azure Analysis Services przy użyciu krótszy *alias* zamiast nazwy serwera. W przypadku łączenia się z aplikacji klienckiej, alias jest określony jako punktu końcowego za pomocą **łącze: / /** formatu protokołu. Punktu końcowego następnie zwraca nazwę prawdziwy serwer w celu nawiązania połączenia.

Alias serwera nazw dla zastosowań dobre są:

- Migrowanie modele między serwerami, nie wpływając na użytkowników. 
- Serwer przyjaznej nazwy są łatwiejsze do zapamiętania przez użytkowników. 
- Bezpośrednie użytkowników na innych serwerach o różnych porach dnia. 
- Bezpośrednie użytkowników w różnych regionach z wystąpieniami, które są geograficznie bliżej, np. przy użyciu usługi Azure Traffic Manager. 

Punkt końcowy HTTPS, która zwraca prawidłową nazwę serwera usług Azure Analysis Services może służyć jako alias. Punkt końcowy musi obsługiwać protokół HTTPS za pośrednictwem portu 443 i port nie może być określony w identyfikatorze URI.

![Alias przy użyciu formatu linku](media/analysis-services-alias/aas-alias-browser.png)

Podczas nawiązywania połączenia z klientem, nazwę serwera aliasu jest wprowadzane przy użyciu **łącze: / /** formatu protokołu. Na przykład w programie Power BI Desktop:

![Połączenie programu Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Utwórz alias

Aby utworzyć punkt końcowy aliasu, można użyć dowolnej metody, która zwraca prawidłową nazwę serwera usług Azure Analysis Services. Na przykład odwołanie do pliku w usłudze Azure Storage Blob zawierają rzeczywiste server name, lub Utwórz i Opublikuj aplikację formularzy sieci Web ASP.NET.

W tym przykładzie aplikacja formularzy sieci Web ASP.NET jest tworzony w programie Visual Studio. Strona wzorcowa odwołania i użytkownika kontroli są usuwane ze strony Default.aspx. Zawartość Default.aspx jest po prostu następująca dyrektywa strony:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

W zdarzeniu Page_Load w Default.aspx.cs używa metody Response.Write(), aby zwrócić nazwę serwera usług Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Zobacz także

[Biblioteki klienckie](analysis-services-data-providers.md)   
[Łączenie z programu Power BI Desktop](analysis-services-connect-pbi.md)
