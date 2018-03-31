---
title: Dodawanie usługi Azure Active Directory przy użyciu usług połączonych w programie Visual Studio | Dokumentacja firmy Microsoft
description: Dodawanie przy użyciu okna dialogowego programu Visual Studio Dodaj połączone usługi Azure Active Directory
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: 882ba1c7ea8ef6889bc9ad20031070cd54100026
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Dodawanie usługi Azure Active Directory przy użyciu usług połączonych w programie Visual Studio

Za pomocą usługi Azure Active Directory (Azure AD), może obsługiwać pojedynczego logowania jednokrotnego (SSO) dla aplikacji sieci web platformy ASP.NET MVC ani uwierzytelnianie usługi Active Directory w usługach interfejsu API sieci Web. Uwierzytelniania usługi Azure AD użytkownicy można za pomocą ich kont z usługi Azure Active Directory do nawiązania połączenia aplikacji sieci web. Zalety uwierzytelniania usługi Azure AD za pomocą interfejsu API sieci Web obejmują zwiększone bezpieczeństwo danych podczas udostępnianie interfejsu API z aplikacji sieci web. Z usługą Azure AD nie masz do zarządzania systemem oddzielne z zarządzaniem konta i użytkownika.

W tym artykule i jej artykułów pomocnika Podaj szczegóły programu za pomocą funkcji Visual Studio podłączonej usługi dla usługi Active Directory. Ta funkcja jest dostępna w Visual Studio 2017 i programu Visual Studio 2015.

Obecnie w usłudze Active Directory połączone nie obsługuje aplikacje platformy ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure: Jeśli nie masz konta platformy Azure, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) lub [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Nawiązać połączenia z usługą Azure Active Directory za pomocą okna dialogowego połączenia usługi

1. W programie Visual Studio Utwórz lub Otwórz projekt platformy ASP.NET MVC lub projekt interfejsu API sieci Web platformy ASP.NET. Można użyć MVC, interfejsu API sieci Web, pojedynczą aplikacją strony aplikacji interfejsu API platformy Azure, aplikacji mobilnej Azure i szablony usługi mobilnej Azure.

1. Wybierz **projektu > dodać podłączonej usługi...**  polecenie menu, kliknij dwukrotnie **usług połączonych** nie znaleziono węzła w projekcie w Eksploratorze rozwiązań.

1. Na **usług połączonych** wybierz pozycję **uwierzytelniania w usłudze Azure Active Directory**.

    ![Strona usług połączonych](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na **wprowadzenie** wybierz pozycję **dalej**. Jeśli na tej stronie znajdują się błędy, zajrzyj do [diagnozowanie błędów przy użyciu usługi Azure Active Directory połączone](vs-active-directory-error.md).

    ![Strona wprowadzenia](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na **jednokrotnego na** stronie, wybierz domenę z **domeny** listy rozwijanej. Lista zawiera wszystkie domeny dostępny przez konta na liście w oknie dialogowym Ustawienia konta programu Visual Studio (**Plik > Ustawienia konta...** ). Alternatywnie, można wprowadzić nazwę domeny, jeśli nie możesz znaleźć to, czego szukasz, takich jak `mydomain.onmicrosoft.com`. Można wybrać opcję, aby utworzyć aplikację usługi Azure Active Directory lub użyć ustawień z istniejącej aplikacji usługi Azure Active Directory. Wybierz **dalej** po zakończeniu.

    ![Jednokrotnego na stronie](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na **dostęp do katalogu** wybierz pozycję **odczytuj dane katalogu** opcję zgodnie z potrzebami. Deweloperzy zazwyczaj uwzględnia tę opcję.

    ![Strona dostępu do katalogu](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Wybierz **Zakończ** można uruchomić zmiany do projektu, aby włączyć uwierzytelnianie usługi Azure AD. Visual Studio przedstawia postęp w tym czasie:

    ![Usługi Active Directory połączone usługi postępów](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po zakończeniu procesu Visual Studio otworzy w przeglądarce jednego z poniższych artykułów, odpowiednio do typu projektu:

    - [Rozpoczynanie pracy z projektami .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Wprowadzenie do projektów WebAPI](vs-active-directory-webapi-getting-started.md)

1. Można również wyświetlić domeny usługi Active Directory na [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Jak zmienić jest projektu

Podczas dodawania podłączonej usługi kreatora programu Visual Studio dodaje usługi Azure Active Directory i skojarzone odwołania do projektu. Pliki konfiguracji i plików kodu w projekcie są również zmodyfikowany w celu dodania pomocy technicznej dla usługi Azure AD. Określone zmiany, które powoduje, że program Visual Studio są zależne od typu projektu. Zobacz następujące artykuły, aby uzyskać szczegółowe informacje:

- [Co się stało z moim projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co się stało z projektu interfejsu API sieci Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania usługi Azure Active Directory](active-directory-authentication-scenarios.md)
- [Dodaj logowanie z firmy Microsoft do aplikacji sieci web platformy ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
