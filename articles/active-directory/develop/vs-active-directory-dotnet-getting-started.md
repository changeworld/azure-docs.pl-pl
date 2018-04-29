---
title: Rozpoczynanie pracy z usługą Azure AD w projektach Visual Studio .NET MVC
description: Jak rozpocząć pracę przy użyciu usługi Azure Active Directory w projektach .NET MVC po nawiązywania połączenia lub utworzenie usługi Azure AD przy użyciu programu Visual Studio połączenia usługi
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: d521b8c85fb66b6c50d1b9f07e5f4d653e9e57b9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Wprowadzenie do korzystania z usługi Azure Active Directory (projekty składnika ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu usługi Active Directory do projektu programu ASP.NET MVC za pomocą **projektu > usług połączonych** polecenia programu Visual Studio. Jeśli usługa nie jest już zostały dodane do projektu, możesz to zrobić w dowolnym momencie.

Zobacz [co się stało z projektu MVC?](vs-active-directory-dotnet-what-happened.md) , aby zmiany wprowadzone do projektu podczas dodawania podłączonej usługi.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do kontrolerów dostępu

Wszystkie kontrolery w projekcie zostały adorned z `[Authorize]` atrybutu. Ten atrybut wymaga od użytkownika mają być uwierzytelniani przed uzyskaniem dostępu do tych kontrolerów. Aby umożliwić kontrolerowi można uzyskać dostępu do anonimowo, Usuń ten atrybut z kontrolera. Aby ustawić uprawnienia na poziomie bardziej szczegółowego, należy zastosować atrybut do każdej metody, która wymaga uwierzytelnienia, zamiast stosować go do klasy kontrolera.

## <a name="adding-signin--signout-controls"></a>Dodawanie SignIn / SignOut kontrolki

Aby dodać formanty SignIn/SignOut do widoku, można użyć `_LoginPartial.cshtml` widoku częściowego do dodawania funkcji do jednego z widoków. Oto przykład funkcje dodane do standardowego `_Layout.cshtml` widoku. (Uwaga: ostatni element div z klasy pasek nawigacyjny zwinięte):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania usługi Azure Active Directory](active-directory-authentication-scenarios.md)
- [Dodaj logowanie z firmy Microsoft do aplikacji sieci web platformy ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
