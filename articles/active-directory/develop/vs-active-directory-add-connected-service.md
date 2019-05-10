---
title: Dodawanie usługi Azure Active Directory przy użyciu usług połączonych programu Visual Studio
description: Dodawanie usługi Azure Active Directory za pomocą okna dialogowego programu Visual Studio Dodaj połączone usługi
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bea0a870a6ef0685f4f4bce5ad3b0d1ff1f616a
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414006"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Dodawanie usługi Azure Active Directory przy użyciu usług połączonych programu Visual Studio

Za pomocą usługi Azure Active Directory (Azure AD), może obsługiwać pojedynczego logowania jednokrotnego (SSO) dla aplikacji sieci web platformy ASP.NET MVC lub uwierzytelniania usługi Active Directory, w usługach interfejsu API sieci Web. Dzięki uwierzytelnianiu Azure AD Twoje użytkownicy mogą używać swoich kont w usłudze Azure Active Directory, połączyć się z aplikacji sieci web. Zalety uwierzytelnianie usługi Azure AD za pomocą interfejsu API sieci Web obejmują zwiększone bezpieczeństwo danych podczas udostępniania interfejsu API z aplikacji sieci web. Z usługą Azure AD nie trzeba zarządzać oddzielnym systemem uwierzytelniania zawierającym swoje własne zarządzania kontami i użytkownikami.

W tym artykule i umieszczanych tam artykułach pomocnika zapewniają szczegółowe informacje o za pomocą funkcji Visual Studio usługi połączonej dla usługi Active Directory. Ta funkcja jest dostępne w programie Visual Studio 2015 i nowszych wersjach.

Obecnie usługa Active Directory połączone nie obsługuje aplikacje platformy ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure: Jeśli nie masz konta platformy Azure, możesz to zrobić [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) lub [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Program Visual Studio 2015** lub nowszej. [Pobierz program Visual Studio teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Nawiązać połączenie z usługi Azure Active Directory za pomocą okna dialogowego podłączone usługi

1. W programie Visual Studio Utwórz lub Otwórz projekt składnika ASP.NET MVC lub projekt interfejsu API sieci Web platformy ASP.NET. Można użyć MVC, interfejsu API sieci Web, aplikacji jednostronicowej aplikacji interfejsu API platformy Azure, aplikacji mobilnej platformy Azure i szablonów usług Azure Mobile.

1. Wybierz **Projekt > Dodaj podłączoną usługę...**  polecenie menu lub kliknij dwukrotnie plik **podłączone usługi** nie znaleziono węzła w projekcie w Eksploratorze rozwiązań.

1. Na **podłączone usługi** wybierz opcję **uwierzytelniania za pomocą usługi Azure Active Directory**.

    ![Strona usług połączonych](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na **wprowadzenie** wybierz opcję **dalej**. Jeśli na tej stronie zostaną wyświetlone błędy, zajrzyj do [diagnozowanie błędów przy użyciu usługi Azure Active Directory połączone](vs-active-directory-error.md).

    ![Strona wprowadzenia](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na **logowania jednokrotnego na** wybierz domenę z **domeny** listy rozwijanej. Lista zawiera wszystkie domeny jest dostępny za pomocą kont wymienionych w oknie dialogowym Ustawienia konta programu Visual Studio (**Plik > Ustawienia konta...** ). Alternatywnie, można wprowadzić nazwę domeny, jeśli nie widzisz jeden, czego szukasz, takich jak `mydomain.onmicrosoft.com`. Można wybrać opcję, aby utworzyć aplikację usługi Azure Active Directory lub użyć ustawień z istniejącej aplikacji usługi Azure Active Directory. Wybierz **dalej** po zakończeniu.

    ![Pojedynczy znak na stronie](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na **dostęp do katalogu** wybierz opcję **Odczyt danych katalogu** opcji zgodnie z potrzebami. Deweloperzy zazwyczaj obejmują tę opcję.

    ![Strona dostępu do katalogu](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Wybierz **Zakończ** można uruchomić zmiany do projektu w celu włączenia uwierzytelniania usługi Azure AD. Visual Studio Wyświetla postęp, w tym czasie:

    ![Usługi Active Directory połączone usługi postępu](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po zakończeniu procesu programu Visual Studio Otwiera przeglądarkę do jednej z następujących artykułów, odpowiednio do danego typu projektu:

    - [Rozpoczynanie pracy z projektami .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Wprowadzenie do projektów WebAPI](vs-active-directory-webapi-getting-started.md)

1. Można również wyświetlić domeny usługi Active Directory na [witryny Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Jak jest modyfikowana projektu

Po dodaniu usługi połączonej kreatora program Visual Studio dodaje Azure Active Directory i skojarzone odwołania do projektu. Pliki konfiguracyjne i pliki kodu w projekcie są również zmodyfikować, aby dodać obsługę usługi Azure AD. Określone zmiany, które sprawia, że program Visual Studio, zależą od typu projektu. Zobacz następujące artykuły, aby uzyskać szczegółowe informacje:

- [Co się stało z moim projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co się stało z moim projektem interfejsu API sieci Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Kolejne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET](quickstart-v1-aspnet-webapp.md)
