---
title: Wprowadzenie do usługi Azure AD w projektach MVC platformy .NET | Azure
description: Jak rozpocząć korzystanie z usługi Azure Active Directory w projektach mvc platformy .NET po nawiązaniu połączenia z usługą Azure AD lub utworzeniu usługi Azure AD przy użyciu połączonych usług programu Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: fe408e965c528db1d82b73ee7b20bbe3b3933657
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886130"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Wprowadzenie do usługi Azure Active Directory (ASP.NET projektów MVC)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-dotnet-getting-started.md)
> - [Co się stało](vs-active-directory-dotnet-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu usługi Active Directory do ASP.NET projektu MVC za pośrednictwem polecenia **Programu Project > Connected Services** programu Visual Studio. Jeśli usługa nie została jeszcze dodana do projektu, możesz to zrobić w dowolnym momencie.

Zobacz [Co się stało z moim projektem MVC?](vs-active-directory-dotnet-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania w celu uzyskania dostępu do kontrolerów

Wszystkie kontrolery w projekcie zostały ozdobione atrybutem. `[Authorize]` Ten atrybut wymaga, aby użytkownik został uwierzytelniony przed dostępem do tych kontrolerów. Aby umożliwić dostęp do kontrolera anonimowo, usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na poziomie bardziej szczegółowy, zastosuj atrybut do każdej metody, która wymaga autoryzacji zamiast stosowania go do klasy kontrolera.

## <a name="adding-signin--signout-controls"></a>Dodawanie kontrolek SignIn / SignOut

Aby dodać kontrolki SignIn/SignOut do widoku, można użyć widoku `_LoginPartial.cshtml` częściowego, aby dodać funkcje do jednego z widoków. Oto przykład funkcji dodanych do widoku `_Layout.cshtml` standardowego. (Zwróć uwagę na ostatni element w div z podziałem nawigacyjny klasy):

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

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)
