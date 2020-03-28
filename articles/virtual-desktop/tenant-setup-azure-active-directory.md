---
title: Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows — Azure
description: W tym artykule opisano sposób konfigurowania dzierżaw pulpitu wirtualnego systemu Windows w usłudze Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9f9f7f709d31967e892900ccb25657a5963c0379
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370223"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Samouczek: Tworzenie dzierżawy na pulpicie wirtualnym systemu Windows

Utworzenie dzierżawy w programie Windows Virtual Desktop jest pierwszym krokiem w kierunku utworzenia rozwiązania do wirtualizacji pulpitu. Dzierżawca to grupa co najmniej jednej puli hostów. Każda pula hostów składa się z wielu hostów sesji, działających jako maszyny wirtualne na platformie Azure i zarejestrowanych w usłudze pulpitu wirtualnego systemu Windows. Każda pula hostów składa się również z jednej lub więcej grup aplikacji, które są używane do publikowania zasobów pulpitu zdalnego i aplikacji zdalnych dla użytkowników. Za pośrednictwem dzierżawy można tworzyć pule hostów, tworzyć grupy aplikacji, przypisywać użytkowników i nawiązywać połączenia za pośrednictwem usługi.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udziel uprawnień usługi Azure Active Directory do usługi pulpitu wirtualnego systemu Windows.
> * Przypisz rolę aplikacji TenantCreator do użytkownika w dzierżawie usługi Azure Active Directory.
> * Tworzenie dzierżawy pulpitu wirtualnego systemu Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>Co jest potrzebne do skonfigurowania dzierżawy

Przed rozpoczęciem konfigurowania dzierżawy pulpitu wirtualnego systemu Windows upewnij się, że masz następujące czynności:

* Identyfikator [dzierżawy usługi Azure Active Directory](https://azure.microsoft.com/services/active-directory/) dla użytkowników pulpitu wirtualnego systemu Windows.
* Konto administratora globalnego w dzierżawie usługi Azure Active Directory.
   * Dotyczy to również organizacji dostawcy rozwiązań w chmurze (CSP), które tworzą dzierżawę pulpitu wirtualnego systemu Windows dla swoich klientów. Jeśli jesteś w organizacji CSP, musisz mieć możliwość logowania się jako administrator globalny wystąpienia usługi Azure Active Directory klienta.
   * Konto administratora musi pochodzić z dzierżawy usługi Azure Active Directory, w której próbujesz utworzyć dzierżawę pulpitu wirtualnego systemu Windows. Ten proces nie obsługuje kont usługi Azure Active Directory B2B (gość).
   * Konto administratora musi być kontem służbowym.
* Subskrypcja platformy Azure.

Musisz mieć gotowy identyfikator dzierżawy, globalne konto administratora i subskrypcję platformy Azure, aby proces opisany w tym samouczku mógł działać poprawnie.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Udzielanie uprawnień pulpitowi wirtualnemu systemu Windows

Jeśli masz już przyznane uprawnienia do pulpitu wirtualnego systemu Windows dla tego wystąpienia usługi Azure Active Directory, pomiń tę sekcję.

Udzielanie uprawnień do usługi pulpitu wirtualnego systemu Windows umożliwia wykonywanie zapytań o usługę Azure Active Directory w celu wykonania zadań administracyjnych i użytkowników końcowych.

Aby udzielić uprawnień usługi:

1. Otwórz przeglądarkę i rozpocznij przepływ zgody administratora do [aplikacji Serwera pulpitu wirtualnego systemu Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Jeśli zarządzasz klientem i musisz udzielić zgody administratora dla katalogu klienta, wprowadź następujący adres URL w przeglądarce i zastąp {tenant} nazwą domeny usługi Azure AD klienta. Jeśli na przykład organizacja klienta zarejestrowała nazwę domeny usługi Azure AD contoso.onmicrosoft.com, zamień {tenant} na contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Zaloguj się na stronie zgody pulpitu wirtualnego systemu Windows przy za pomocą konta administratora globalnego. Na przykład, jeśli byłeś w organizacji Contoso, twoje konto może być admin@contoso.com lub admin@contoso.onmicrosoft.com.
3. Wybierz pozycję **Zaakceptuj**.
4. Poczekaj minutę, aby usługa Azure AD mogła rejestrować zgodę.
5. Otwórz przeglądarkę i rozpocznij przepływ zgody administratora do [aplikacji klienta pulpitu wirtualnego systemu Windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Jeśli zarządzasz klientem i musisz udzielić zgody administratora dla katalogu klienta, wprowadź następujący adres URL w przeglądarce i zastąp {tenant} nazwą domeny usługi Azure AD klienta. Jeśli na przykład organizacja klienta zarejestrowała nazwę domeny usługi Azure AD contoso.onmicrosoft.com, zamień {tenant} na contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Zaloguj się na stronie zgody pulpitu wirtualnego systemu Windows jako administrator globalny, tak jak w kroku 2.
7. Wybierz pozycję **Zaakceptuj**.

## <a name="assign-the-tenantcreator-application-role"></a>Przypisywanie roli aplikacji TenantCreator

Przypisywanie użytkownika usługi Azure Active Directory roli aplikacji TenantCreator umożliwia użytkownikowi utworzenie dzierżawy pulpitu wirtualnego systemu Windows skojarzonej z wystąpieniem usługi Azure Active Directory. Aby przypisać rolę TenantCreator, musisz użyć konta administratora globalnego.

Aby przypisać rolę aplikacji TenantCreator:

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać rolą aplikacji TenantCreator. Wyszukaj i wybierz **aplikacje enterprise**. Jeśli pracujesz z wieloma dzierżawami usługi Azure Active Directory, najlepszym rozwiązaniem jest otwarcie prywatnej sesji przeglądarki oraz skopiowanie i wklejenie adresów URL do paska adresu.

   ![Zrzut ekranu przedstawiający wyszukiwanie aplikacji dla przedsiębiorstw w witrynie Azure portal](media/azure-portal-enterprise-applications.png)
2. W **aplikacjach przedsiębiorstwa**wyszukaj **pulpit wirtualny systemu Windows**. Zobaczysz dwie aplikacje, na które wyraziłeś zgodę w poprzedniej sekcji. Spośród tych dwóch aplikacji wybierz pozycję **Pulpit wirtualny systemu Windows**.
   
   ![Zrzut ekranu przedstawiający wyniki wyszukiwania podczas wyszukiwania hasła "Pulpit wirtualny systemu Windows" w polu "Aplikacje dla przedsiębiorstw". Wyróżniona jest aplikacja o nazwie "Pulpit wirtualny systemu Windows".](media/tenant-enterprise-app.png)
3. Wybierz **pozycję Użytkownicy i grupy**. Może się okazać, że administrator, który udzielił zgody na aplikację, jest już wymieniony z przypisaną rolą **dostępu domyślnego.** Nie jest to wystarczające do utworzenia dzierżawy pulpitu wirtualnego systemu Windows. Kontynuuj przestrzeganie tych instrukcji, aby dodać rolę **TenantCreator** do użytkownika.
   
   ![Zrzut ekranu przedstawiający użytkowników i grupy przypisane do zarządzania aplikacją enterprise "Pulpit wirtualny systemu Windows". Zrzut ekranu pokazuje tylko jedno przypisanie, które jest dla "Domyślny dostęp".](media/tenant-default-access.png)
4. Wybierz pozycję **Dodaj użytkownika,** a następnie wybierz pozycję **Użytkownicy i grupy** na karcie Dodawanie **przydziału.**
5. Wyszukaj konto użytkownika, które utworzy dzierżawę pulpitu wirtualnego systemu Windows. Dla uproszczenia może to być konto administratora globalnego.
   - Jeśli używasz dostawcy tożsamości firmy contosoadmin@live.com contosoadmin@outlook.comMicrosoft, takiego jak lub możesz nie być w stanie zalogować się do pulpitu wirtualnego systemu Windows. Zalecamy używanie konta specyficznego admin@contoso.com admin@contoso.onmicrosoft.com dla domeny, takiego jak lub zamiast tego.

   ![Zrzut ekranu przedstawiający wybranie użytkownika do dodania jako "TenantCreator".](media/tenant-assign-user.png)
   > [!NOTE]
   > Należy wybrać użytkownika (lub grupę zawierającą użytkownika), która pochodzi z tego wystąpienia usługi Azure Active Directory. Nie można wybrać użytkownika gościa (B2B) ani jednostki usługi.

6. Wybierz konto użytkownika, wybierz przycisk **Wybierz,** a następnie wybierz pozycję **Przypisz**.
7. Na stronie **Pulpit wirtualny systemu Windows — Użytkownicy i grupy** sprawdź, czy zostanie wyświetlony nowy wpis z rolą **TenantCreator** przypisaną do użytkownika, który utworzy dzierżawę pulpitu wirtualnego systemu Windows.

   ![Zrzut ekranu przedstawiający użytkowników i grupy przypisane do zarządzania aplikacją enterprise "Pulpit wirtualny systemu Windows". Zrzut ekranu zawiera teraz drugi wpis użytkownika przypisanego do roli "TenantCreator".](media/tenant-tenant-creator-added.png)

Przed kontynuowaniem tworzenia dzierżawy pulpitu wirtualnego systemu Windows potrzebne są dwie informacje:

   - Identyfikator dzierżawy usługi Azure Active Directory (lub **identyfikator katalogu)**
   - Identyfikator subskrypcji platformy Azure

Aby znaleźć identyfikator dzierżawy usługi Azure Active Directory (lub **identyfikator katalogu):**
1. W tej samej sesji [portalu Platformy Azure](https://portal.azure.com) wyszukaj i wybierz pozycję Usługa Azure Active **Directory**.

   ![Zrzut ekranu przedstawiający wyniki wyszukiwania dla usługi "Azure Active Directory" w witrynie Azure portal. Wyróżniony wynik wyszukiwania w obszarze "Usługi".](media/tenant-search-azure-active-directory.png)
2. Przewiń w dół, aż znajdziesz **polecenie Właściwości**, a następnie wybierz je.
3. **Poszukaj identyfikatora katalogu,** a następnie wybierz ikonę schowka. Wklej go w poręcznej lokalizacji, aby można było go później użyć jako wartość **AadTenantId.**

   ![Zrzut ekranu właściwości usługi Azure Active Directory. Mysz jest najechanie kursorem na ikonę schowka dla "Identyfikator katalogu", aby skopiować i wkleić.](media/tenant-directory-id.png)

Aby znaleźć identyfikator subskrypcji platformy Azure:
1. W tej samej sesji [portalu Platformy Azure](https://portal.azure.com) wyszukaj i wybierz **subskrypcje**.
   
   ![Zrzut ekranu przedstawiający wyniki wyszukiwania dla usługi "Azure Active Directory" w witrynie Azure portal. Wyróżniony wynik wyszukiwania w obszarze "Usługi".](media/tenant-search-subscription.png)
2. Wybierz subskrypcję platformy Azure, której chcesz użyć do otrzymywania powiadomień o usłudze pulpitu wirtualnego systemu Windows.
3. **Poszukaj identyfikatora subskrypcji,** a następnie umieść wskaźnik myszy na wartości, aż pojawi się ikona schowka. Wybierz ikonę schowka i wklej ją w poręcznej lokalizacji, aby można było jej później użyć jako wartości **AzureSubscriptionId.**
   
   ![Zrzut ekranu właściwości subskrypcji platformy Azure. Mysz jest najechanie kursorem na ikonę schowka dla "Identyfikator subskrypcji", aby skopiować i wkleić.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Tworzenie dzierżawy pulpitu wirtualnego systemu Windows

Teraz, gdy przyznano uprawnienia usługi pulpitu wirtualnego systemu Windows do wykonywania kwerend usługi Azure Active Directory i przypisano rolę TenantCreator do konta użytkownika, można utworzyć dzierżawę pulpitu wirtualnego systemu Windows.

Najpierw [pobierz i zaimportuj moduł pulpitu wirtualnego systemu Windows](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś.

Zaloguj się do pulpitu wirtualnego systemu Windows przy użyciu konta użytkownika TenantCreator przy użyciu tego polecenia cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie utwórz nową dzierżawę pulpitu wirtualnego systemu Windows skojarzoną z dzierżawą usługi Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Zastąp wartości w nawiasach wartościami odpowiednimi dla organizacji i dzierżawy. Nazwa wybrana dla nowej dzierżawy pulpitu wirtualnego systemu Windows powinna być unikatowa globalnie. Załóżmy na przykład, że jesteś dzierżawcą pulpitu wirtualnego systemu Windows dla organizacji Contoso. Polecenie cmdlet, które chcesz uruchomić będzie wyglądać następująco:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Dobrym pomysłem jest przypisanie dostępu administracyjnego drugiemu użytkownikowi w przypadku, gdy kiedykolwiek znajdziesz się zablokowanym na swoim koncie lub jedziesz na wakacje i potrzebujesz kogoś, kto będzie pełnił rolę administratora dzierżawy pod Twoją nieobecność. Aby przypisać dostęp administratora drugiemu użytkownikowi, uruchom `<TenantName>` `<Upn>` następujące polecenie cmdlet z nazwą dzierżawy i nazwą UPN drugiego użytkownika.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu dzierżawy należy utworzyć jednostkę usługi w usłudze Azure Active Directory i przypisać mu rolę na pulpicie wirtualnym systemu Windows. Podmiot usługi umożliwi pomyślne wdrożenie oferty portalu Windows Virtual Desktop Azure Marketplace w celu utworzenia puli hostów. Aby dowiedzieć się więcej o pulach hostów, przejdź do samouczka tworzenia puli hostów na pulpicie wirtualnym systemu Windows.

> [!div class="nextstepaction"]
> [Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell](./create-service-principal-role-powershell.md)
