---
title: Tworzenie dzierżawy w Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: W tym artykule opisano sposób konfigurowania dzierżaw Windows wirtualnego pulpitu (wersja zapoznawcza) w usłudze Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: b8b5c2cef1db5018ce0d61e1950f49a3bd215ac2
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402902"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Samouczek: Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego Windows

Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego Windows jest pierwszy krok w kierunku zbudowaniu rozwiązania do wirtualizacji pulpitu. Dzierżawa to grupy co najmniej jedna pula hosta. Każda pula hosta składa się z wielu hostów sesji, uruchomione jako maszyny wirtualne na platformie Azure i zarejestrowane w usłudze Windows pulpitu wirtualnego. Każda pula hosta również składa się z co najmniej jedną grupę aplikacji, które są używane do publikowania zasobów zdalnego pulpitu i zdalnej aplikacji dla użytkowników. Z dzierżawą można budować pule hosta, tworzenie grupy aplikacji, przypisywanie użytkowników i nawiązywać połączenia za pośrednictwem usługi.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uprawnienia GRANT usługi Azure Active Directory z usługą Windows pulpitu wirtualnego.
> * Przypisz rolę aplikacji TenantCreator użytkownikowi w swojej dzierżawie usługi Azure Active Directory.
> * Utwórz dzierżawę Windows pulpitu wirtualnego.

Oto, co jest potrzebne do skonfigurowania dzierżawy pulpitu wirtualnego Windows:

* [Usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) identyfikator dla Windows pulpitu wirtualnego użytkowników dzierżawy.
* Konto administratora globalnego dzierżawy usługi Azure Active Directory.
   * Dotyczy to również organizacjom Cloud Solution Provider (CSP), tworzenia dzierżawy pulpitu wirtualnego Windows dla swoich klientów. Jeśli w organizacji dostawcy usług Kryptograficznych, należy zalogować się jako administrator globalny przez klienta usługi Azure Active Directory.
* Identyfikator subskrypcji platformy Azure

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Uprawnienia GRANT usługi Azure Active Directory z usługą Windows wirtualnego pulpitu (wersja zapoznawcza)

Jeśli masz już uprawnień do pulpitu wirtualnego Windows dla tej usługi Azure Active Directory, należy pominąć tę sekcję.

Udzielanie uprawnień w usłudze Windows pulpitu wirtualnego umożliwia mu zapytań usługi Azure Active Directory dla administracyjnych i zadaniach użytkownika końcowego.

Aby udzielić uprawnień usługi:

1. Otwórz przeglądarkę i połącz się z [strona zgody użytkownika pulpitu wirtualnego Windows](https://rdweb.wvd.microsoft.com).
2. Dla **opcja zgody** > **aplikacja serwera**, wprowadź nazwę dzierżawy usługi Azure Active Directory lub identyfikator katalogu, a następnie wybierz **przesyłania**.
        W przypadku klientów programu Cloud Solution Provider identyfikator jest identyfikator firmy Microsoft przez klienta z portalu dla partnerów. Dla klientów korporacyjnych identyfikator znajduje się w folderze **usługi Azure Active Directory** > **właściwości** > **identyfikator katalogu**.
3. Zaloguj się Strona zgody użytkownika Windows pulpitu wirtualnego przy użyciu konta administratora globalnego. Na przykład, gdyby w organizacji Contoso Twoje konto może być admin@contoso.com lub admin@contoso.onmicrosoft.com.  
4. Wybierz pozycję **Zaakceptuj**.
5. Oczekiwanie na jedną minutę.
6. Przejdź z powrotem do [strona zgody użytkownika pulpitu wirtualnego Windows](https://rdweb.wvd.microsoft.com).
7. Przejdź do **opcja zgody** > **aplikację kliencką**, wprowadź ten sam nazwa dzierżawy usługi Azure AD lub identyfikator katalogu, a następnie wybierz **przesyłania**.
8. Zaloguj się Strona zgody użytkownika pulpitu wirtualnego Windows jako administrator globalny, jak zrobiono to w kroku 3.
9. Wybierz pozycję **Zaakceptuj**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Przypisanie roli aplikacji TenantCreator do użytkownika w dzierżawie usługi Azure Active Directory

Przypisanie użytkownika usługi Azure Active Directory TenantCreator roli aplikacji umożliwia użytkownikowi tworzenie dzierżawy Windows pulpit wirtualny skojarzony z usługą Azure Active Directory. Należy użyć konta administratora globalnego usługi, aby przypisać rolę TenantCreator.

Aby przypisać rolę TenantCreator aplikacji przy użyciu konta administratora globalnego:

1. Otwórz przeglądarkę i połącz się z [portalu Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta administratora globalnego.
   - Jeśli pracujesz z wieloma dzierżawami usługi Azure AD, jest najlepszym rozwiązaniem, aby otworzyć sesję przeglądarki prywatnej i skopiuj i Wklej adresy URL do adresu.
2. Wybierz **aplikacje dla przedsiębiorstw**, wyszukaj **pulpitu wirtualnego Windows**. Zostaną wyświetlone dwie aplikacje, które podałeś zgodę dotyczącą w poprzedniej sekcji. Te dwie aplikacje, wybierz **pulpitu wirtualnego Windows**.
3. Wybierz **użytkowników i grup**, a następnie wybierz **Dodaj użytkownika**.
4. Wybierz użytkowników i grup w **Dodaj przydziału** bloku
5. Wyszukiwanie konta użytkownika, który spowoduje utworzenie dzierżawy Windows pulpitu wirtualnego.
   - Dla uproszczenia może to być konto administratora globalnego.
6. Wybierz konto użytkownika, kliknij przycisk **wybierz** przycisk, a następnie wybierz **przypisać**.

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Tworzenie dzierżawy Windows wirtualnego pulpitu (wersja zapoznawcza)

Skoro już przyznane uprawnienia usługi pulpitu wirtualnego Windows do wykonywania zapytań w usłudze Azure Active Directory i przypisano rolę TenantCreator do konta użytkownika, możesz utworzyć dzierżawę Windows pulpitu wirtualnego.

Po pierwsze, [Pobierz i zaimportuj moduł programu Windows pulpitu wirtualnego](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Zaloguj się do Windows pulpitu wirtualnego przy użyciu konta użytkownika TenantCreator za pomocą tego polecenia cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
```

Po tym należy utworzyć nową dzierżawę Windows pulpit wirtualny skojarzony z dzierżawą usługi Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Przy użyciu wartości odpowiednich dla Twojej firmy i dzierżawy należy zastąpić wartości w nawiasach kwadratowych. Załóżmy na przykład, że jesteś Windows TenantCreator pulpitu wirtualnego w organizacji Contoso. Polecenia cmdlet, które należy uruchomić następujące polecenie będzie wyglądać następująco:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu dzierżawy należy utworzyć pulę hosta. Aby dowiedzieć się więcej o pulach hosta, przejdź do samouczka związane z tworzeniem puli hosta w Windows pulpitu wirtualnego.

> [!div class="nextstepaction"]
> [Samouczek puli hosta Windows pulpitu wirtualnego](./create-host-pools-azure-marketplace.md)
