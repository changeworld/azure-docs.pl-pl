---
title: Tworzenie dzierżawy w Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: W tym artykule opisano sposób konfigurowania dzierżaw Windows wirtualnego pulpitu (wersja zapoznawcza) w usłudze Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 88b3ffa38eb42eef42c98920b2c3193661b1c0f5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236158"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Samouczek: Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego Windows

Tworzenie dzierżawy w wersji zapoznawczej pulpitu wirtualnego Windows jest pierwszy krok w kierunku zbudowaniu rozwiązania do wirtualizacji pulpitu. Dzierżawa to grupy co najmniej jedna pula hosta. Każda pula hosta składa się z wielu hostów sesji, uruchomione jako maszyny wirtualne na platformie Azure i zarejestrowane w usłudze Windows pulpitu wirtualnego. Każda pula hosta również składa się z co najmniej jedną grupę aplikacji, które są używane do publikowania zasobów zdalnego pulpitu i zdalnej aplikacji dla użytkowników. Z dzierżawą można budować pule hosta, tworzenie grupy aplikacji, przypisywanie użytkowników i nawiązywać połączenia za pośrednictwem usługi.

W tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Uprawnienia GRANT usługi Azure Active Directory z usługą Windows pulpitu wirtualnego.
> * Przypisz rolę aplikacji TenantCreator użytkownikowi w swojej dzierżawie usługi Azure Active Directory.
> * Utwórz dzierżawę Windows pulpitu wirtualnego.

Oto, co jest potrzebne do skonfigurowania dzierżawy pulpitu wirtualnego Windows:

* [Usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) identyfikator dla Windows pulpitu wirtualnego użytkowników dzierżawy.
* Konto administratora globalnego dzierżawy usługi Azure Active Directory.
   * Dotyczy to również organizacjom Cloud Solution Provider (CSP), tworzenia dzierżawy pulpitu wirtualnego Windows dla swoich klientów. Jeśli w organizacji dostawcy usług Kryptograficznych, należy zalogować się jako administrator globalny przez klienta usługi Azure Active Directory.
   * Konta administratora należy ustalić źródło, z dzierżawy usługi Azure Active Directory, w którym próbujesz utworzyć dzierżawę pulpitu wirtualnego Windows. Ten proces nie obsługuje usługi Azure Active Directory B2B (Gość) konta.
   * Konto administratora musi być konta firmowego lub szkolnego.
* Subskrypcja platformy Azure

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
7. Przejdź do **opcja zgody** > **aplikację kliencką**, wprowadź ten sam nazwa dzierżawy usługi Azure Active Directory lub identyfikator katalogu, a następnie wybierz **przesyłania**.
8. Zaloguj się Strona zgody użytkownika pulpitu wirtualnego Windows jako administrator globalny, jak zrobiono to w kroku 3.
9. Wybierz pozycję **Zaakceptuj**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Przypisanie roli aplikacji TenantCreator do użytkownika w dzierżawie usługi Azure Active Directory

Przypisanie użytkownika usługi Azure Active Directory TenantCreator roli aplikacji umożliwia użytkownikowi tworzenie dzierżawy Windows pulpit wirtualny skojarzony z usługą Azure Active Directory. Należy użyć konta administratora globalnego usługi, aby przypisać rolę TenantCreator.

Aby przypisać rolę TenantCreator aplikacji przy użyciu konta administratora globalnego:

1. Otwórz przeglądarkę i połącz się z [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
   - Jeśli pracujesz z wieloma dzierżawami usługi Azure Active Directory, jest najlepszym rozwiązaniem, aby otworzyć sesję przeglądarki prywatnej i skopiuj i Wklej adresy URL na pasku adresu.
2. Na pasku wyszukiwania w witrynie Azure portal Wyszukaj **aplikacje dla przedsiębiorstw** i wybierz wpis, który pojawia się w obszarze **usług** kategorii.
3. W ramach **aplikacje dla przedsiębiorstw**, wyszukaj **pulpitu wirtualnego Windows**. Zostaną wyświetlone dwie aplikacje, które podałeś zgodę dotyczącą w poprzedniej sekcji. Te dwie aplikacje, wybierz **pulpitu wirtualnego Windows**.
        ![Zrzut ekranu przedstawiający wyniki wyszukiwania podczas wyszukiwania "Pulpitu wirtualnego Windows" w "aplikacje przedsiębiorstwa". Aplikacja o nazwie "Windows pulpit wirtualny" zostanie wyróżniona.](media/tenant-enterprise-app.png)
4. Wybierz pozycję **Użytkownicy i grupy**. Zobaczysz, że administrator, który uzyska zatwierdzenie do aplikacji jest już wyświetlany na liście **domyślnego dostępu** przypisanej roli. To nie jest wystarczająco, aby utworzyć dzierżawę Windows pulpitu wirtualnego. Postępuj zgodnie z tymi instrukcjami, aby dodać **TenantCreator** roli do użytkownika.
        ![Zrzut ekranu użytkowników i grup przypisanych do zarządzania aplikacją przedsiębiorstwa "Pulpitu wirtualnego Windows". Zrzut ekranu przedstawia tylko jedno przypisanie, które jest "Domyślnego dostępu".](media/tenant-default-access.png)
5. Wybierz **Dodaj użytkownika**, następnie **użytkowników i grup** w **Dodaj przydziału** bloku.
6. Wyszukiwanie konta użytkownika, który spowoduje utworzenie dzierżawy Windows pulpitu wirtualnego. Dla uproszczenia może to być konto administratora globalnego.

    ![Zrzut ekranu przedstawiający wybór użytkownika do dodania jako "TenantCreator".](media/tenant-assign-user.png)

   > [!NOTE]
   > Należy wybrać użytkownika (lub grupy zawierającej użytkowników), wartości są uzyskiwane z tej usługi Azure Active Directory. Nie możesz wybrać użytkownika-gościa (B2B) lub ta jednostka usługi.

7. Wybierz konto użytkownika, wybierz pozycję **wybierz** przycisk, a następnie wybierz **przypisać**.
8. Na **pulpitu wirtualnego Windows - użytkownicy i grupy** upewnij się, że zostanie wyświetlony nowy wpis o **TenantCreator** roli przypisanej do użytkownika, który utworzy dzierżawę Windows pulpitu wirtualnego.
        ![Zrzut ekranu użytkowników i grup przypisanych do zarządzania aplikacją przedsiębiorstwa "Pulpitu wirtualnego Windows". Zrzut ekranu zawiera teraz drugi wpis użytkownika przypisanego do roli "TenantCreator".](media/tenant-tenant-creator-added.png)

Przed kontynuowaniem na utworzyć swoją dzierżawę Windows pulpitu wirtualnego, potrzebne są dwa rodzaje informacji:
- Identyfikator dzierżawy usługi Azure Active Directory (lub **identyfikator katalogu**)
- Identyfikator subskrypcji platformy Azure

Aby znaleźć usługi Active Directory identyfikator dzierżawy usługi Azure (lub **identyfikator katalogu**):
1. W tej samej sesji portalu Azure, wyszukaj **usługi Azure Active Directory** w pasku wyszukiwania i wybierz wpis, który pojawia się w obszarze **usług** kategorii.
        ![Zrzut ekranu przedstawiający wyniki wyszukiwania dla "Azure Active Directory" w witrynie Azure portal. Wynik wyszukiwania w obszarze "Usługi" zostanie wyróżniona.](media/tenant-search-azure-active-directory.png)
2. Przewiń w dół, aż znajdziesz **właściwości**, wybierz ją.
3. Wyszukaj **identyfikator katalogu**, następnie wybierz ikonę Schowka. Wklej ją w poręczne lokalizacji, aby można było używać nowszego jako **AadTenantId**.
        ![Zrzut ekranu przedstawiający właściwości usługi Azure Active Directory. Po umieszczeniu wskaźnika myszy na ikonie Schowka "Identyfikator katalogu" w celu kopiowania i wklejania.](media/tenant-directory-id.png)

Aby znaleźć swój identyfikator subskrypcji platformy Azure:
1. W tej samej sesji portalu Azure, wyszukaj **subskrypcje** w pasku wyszukiwania i wybierz wpis, który pojawia się w obszarze **usług** kategorii.
        ![Zrzut ekranu przedstawiający wyniki wyszukiwania dla "Azure Active Directory" w witrynie Azure portal. Wynik wyszukiwania w obszarze "Usługi" zostanie wyróżniona.](media/tenant-search-subscription.png)
2. Wybierz subskrypcję platformy Azure, którego chcesz użyć do odbierania powiadomień dotyczących usług Windows pulpitu wirtualnego.
3. Wyszukaj **identyfikator subskrypcji**, następnie umieść kursor nad wartością, aż pojawi się ikona Schowka. Wybierz ikonę Schowka i wkleić ją w poręczne miejsce, aby można było używać nowszego jako **AzureSubscriptionId**.
        ![Zrzut ekranu przedstawiający właściwości subskrypcji platformy Azure. Po umieszczeniu wskaźnika myszy na ikonie Schowka "Identyfikator subskrypcji" w celu kopiowania i wklejania.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Tworzenie dzierżawy Windows wirtualnego pulpitu (wersja zapoznawcza)

Skoro już przyznane uprawnienia usługi pulpitu wirtualnego Windows do wykonywania zapytań w usłudze Azure Active Directory i przypisano rolę TenantCreator do konta użytkownika, możesz utworzyć dzierżawę Windows pulpitu wirtualnego.

Po pierwsze, [Pobierz i zaimportuj moduł programu Windows pulpitu wirtualnego](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Zaloguj się do Windows pulpitu wirtualnego przy użyciu konta użytkownika TenantCreator za pomocą tego polecenia cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
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

Po utworzeniu dzierżawy należy utworzyć nazwę główną usługi w usłudze Azure Active Directory i przypisać ją w roli w ramach pulpitu wirtualnego Windows. Nazwa główna usługi pozwala pomyślne wdrożenie Windows wirtualnego pulpitu portalu Azure Marketplace oferty utworzyć pulę hosta. Aby dowiedzieć się więcej o pulach hosta, przejdź do samouczka związane z tworzeniem puli hosta w Windows pulpitu wirtualnego.

> [!div class="nextstepaction"]
> [Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell](./create-service-principal-role-powershell.md)
