---
title: Rozpoczynanie pracy z usługą Azure AD w projektach programu Visual Studio .NET MVC
description: Jak rozpocząć pracę, przy użyciu usługi Azure Active Directory w projektach .NET MVC po nawiązywania połączenia lub tworzenia usługi Azure AD przy użyciu programu Visual Studio podłączone usługi
services: active-directory
author: ghogen
manager: douge
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bbbef3f48eb55c863fdd286113297d79f9b9e02
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60296847"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Wprowadzenie do usługi Azure Active Directory (w projektach ASP.NET MVC)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki, po dodaniu usługi Active Directory do projektu programu ASP.NET MVC za pomocą **Projekt > usług połączonych** polecenia programu Visual Studio. Jeśli jeszcze nie dodano usługę do projektu, możesz to zrobić w dowolnym momencie.

Zobacz [co się stało z moim projektem MVC?](vs-active-directory-dotnet-what-happened.md) zmian wprowadzonych do projektu, podczas dodawania usługi połączonej.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do dostępu do kontrolerów

Wszystkie kontrolery w projekcie zostały powiązany z `[Authorize]` atrybutu. Ten atrybut wymaga od użytkownika uwierzytelniali się przed uzyskaniem dostępu do tych kontrolerów. Aby umożliwić kontrolerowi można uzyskiwać dostęp anonimowo, Usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na bardziej szczegółowym poziomie, należy zastosować atrybut do każdej metody, która wymaga autoryzacji zamiast zastosowanie go do klasy kontrolera.

## <a name="adding-signin--signout-controls"></a>Dodawanie SignIn / SignOut kontrolki

Aby dodać formanty SignIn/SignOut do widoku, można użyć `_LoginPartial.cshtml` widoku częściowego, aby dodać funkcje do jednego z widoków. Oto przykład funkcje dodane do standardowego `_Layout.cshtml` widoku. (Uwaga: po ostatnim elemencie w div z klasy pasek nawigacyjny / Zwiń):

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

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET](quickstart-v1-aspnet-webapp.md)
