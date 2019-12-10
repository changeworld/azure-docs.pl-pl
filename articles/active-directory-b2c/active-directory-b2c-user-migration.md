---
title: Podejście do migracji użytkowników
titleSuffix: Azure AD B2C
description: W tym artykule omówiono podstawowe i zaawansowane koncepcje dotyczące migracji użytkowników przy użyciu usługi Azure AD interfejs API programu Graph i opcjonalnie można używać Azure AD B2C zasad niestandardowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5b7d477b0704db3f23919281fd61328be114dae
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950225"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>Migrowanie użytkowników do Azure Active Directory B2C

Podczas migrowania dostawcy tożsamości do Azure Active Directory B2C (Azure AD B2C) może być również konieczne przeprowadzenie migracji kont użytkowników. W tym artykule wyjaśniono, jak przeprowadzić migrację istniejących kont użytkowników z dowolnego dostawcy tożsamości do Azure AD B2C. Artykuł nie ma zastosowania, ale jest to opis kilku scenariuszy. Deweloper jest odpowiedzialny za przydatność poszczególnych rozwiązań.

## <a name="user-migration-flows"></a>Przepływy migracji użytkowników

Za pomocą Azure AD B2C można migrować użytkowników za pomocą [interfejs API programu Graph usługi Azure AD][B2C-GraphQuickStart]. Proces migracji użytkowników odbywa się w dwóch przepływach:

- **Przed migracją**: ten przepływ ma zastosowanie, gdy masz czysty dostęp do poświadczeń użytkownika (nazwa użytkownika i hasło) lub poświadczenia są szyfrowane, ale można je odszyfrować. Proces poprzedzający migrację obejmuje odczytywanie użytkowników ze starego dostawcy tożsamości i tworzenie nowych kont w katalogu Azure AD B2C.

- **Przed migracją i resetowaniem hasła**: ten przepływ ma zastosowanie, gdy hasło użytkownika jest niedostępne. Na przykład:
  - Hasło jest przechowywane w formacie skrótu.
  - Hasło jest przechowywane w dostawcy tożsamości, do którego nie można uzyskać dostępu. Stary dostawca tożsamości sprawdza poprawność poświadczeń użytkownika przez wywołanie usługi sieci Web.

W obu przepływach najpierw należy uruchomić proces poprzedzający migrację, odczytać użytkowników ze starego dostawcy tożsamości i utworzyć nowe konta w katalogu Azure AD B2C. Jeśli nie masz hasła, możesz utworzyć konto przy użyciu hasła, które zostało wygenerowane losowo. Następnie poprosisz użytkownika o zmianę hasła lub, gdy użytkownik loguje się po raz pierwszy, Azure AD B2C prosi użytkownika o jego zresetowanie.

## <a name="password-policy"></a>Zasady dotyczące haseł

Zasady haseł Azure AD B2C (dla kont lokalnych) są oparte na zasadach usługi Azure AD. Zasady rejestrowania i logowania Azure AD B2C i resetowania haseł używają silnego siły hasła i nie tracą hasła. Aby uzyskać więcej informacji, zobacz [zasady haseł usługi Azure AD][AD-PasswordPolicies].

Jeśli konta, które mają zostać poddane migracji, korzystają z słabszego siły hasła niż [silne hasła wymuszone przez Azure AD B2C][AD-PasswordPolicies], można wyłączyć wymaganie silnego hasła. Aby zmienić domyślne zasady haseł, ustaw właściwość `passwordPolicies` na `DisableStrongPassword`. Na przykład można zmodyfikować polecenie Utwórz żądanie użytkownika w następujący sposób:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Krok 1. Korzystanie z usługi Azure AD interfejs API programu Graph do migrowania użytkowników

Można utworzyć konto użytkownika Azure AD B2C za pośrednictwem interfejs API programu Graph (z hasłem lub hasłem losowym). W tej sekcji opisano proces tworzenia kont użytkowników w katalogu Azure AD B2C przy użyciu interfejs API programu Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Krok 1,1: rejestrowanie aplikacji w dzierżawie

Aby móc komunikować się z interfejs API programu Graph, musisz najpierw mieć konto usługi z uprawnieniami administracyjnymi. W usłudze Azure AD rejestrujesz aplikację i włączasz dostęp do zapisu w katalogu. Poświadczenia aplikacji to **Identyfikator aplikacji** i **klucz tajny aplikacji**. Aplikacja działa jako sama, a nie jako użytkownik, aby wywołać interfejs API programu Graph.

Najpierw Zarejestruj aplikację, której można użyć do zadań zarządzania, takich jak migracja użytkownika.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>Krok 1,2: Przyznawanie uprawnień administracyjnych do aplikacji

Następnie udziel aplikacji uprawnienia interfejs API programu Graph usługi Azure AD wymagane do zapisu w katalogu.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>Krok 1,3: tworzenie wpisu tajnego aplikacji

Utwórz wpis tajny klienta (klucz) do użycia przez aplikację do migracji użytkowników, która została skonfigurowana w późniejszym kroku.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Teraz masz aplikację z uprawnieniami do tworzenia, odczytywania i aktualizowania użytkowników w dzierżawie Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Krok 1,4: (opcjonalnie) oczyszczanie środowiska

Uprawnienie *Odczyt i zapis danych katalogu* *nie obejmuje prawa* do usuwania użytkowników. Aby umożliwić aplikacji usuwanie użytkowników (czyszczenie środowiska), należy wykonać dodatkowy krok, który polega na uruchomieniu programu PowerShell w celu ustawienia uprawnień administratora konta użytkownika. W przeciwnym razie możesz przejść do następnej sekcji.

> [!IMPORTANT]
> Musisz użyć konta administratora dzierżawy B2C, które jest *lokalne* dla dzierżawy B2C. Składnia nazwy konta to *admin\@contosob2c.onmicrosoft.com*.

W tym skrypcie programu PowerShell, który wymaga [modułu Azure AD PowerShell V2][AD-Powershell], wykonaj następujące czynności:

1. Nawiąż połączenie z usługą online. Aby to zrobić, uruchom polecenie cmdlet `Connect-AzureAD` w wierszu polecenia programu Windows PowerShell i podaj swoje poświadczenia.

1. Użyj **identyfikatora aplikacji** , aby przypisać aplikację do roli administratora konta użytkownika. Role te mają dobrze znane identyfikatory, więc wystarczy wprowadzić **Identyfikator aplikacji** w skrypcie.

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

Zmień wartość `$AppId` za pomocą **identyfikatora aplikacji**usługi Azure AD.

## <a name="step-2-pre-migration-application-sample"></a>Krok 2. przykład aplikacji przed migracją

Przykład kodu sprzed migracji znajduje się w repozytorium `azure-ad-b2c/user-migration` GitHub obsługiwanego przez społeczność:

[Azure-AD-B2C/User-Migration/premigration][UserMigrationSample-code] (GitHub)

### <a name="step-21-edit-the-migration-data-file"></a>Krok 2,1: edytowanie pliku danych migracji

Przykładowa aplikacja używa pliku JSON, który zawiera fikcyjne dane użytkownika. Po pomyślnym uruchomieniu przykładu można zmienić kod w taki sposób, aby korzystał z danych z własnej bazy danych. Można też wyeksportować profil użytkownika do pliku JSON, a następnie skonfigurować aplikację do korzystania z tego pliku.

Aby edytować plik JSON, Otwórz rozwiązanie `AADB2C.UserMigration.sln` Visual Studio. W projekcie `AADB2C.UserMigration` Otwórz plik `UsersData.json`.

![Część pliku UsersData. JSON przedstawiająca bloki JSON dwóch użytkowników](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Jak widać, plik zawiera listę jednostek użytkownika. Każda jednostka użytkownika ma następujące właściwości:

- e-mail
- displayName
- firstName
- lastName
- hasło (może być puste)

> [!NOTE]
> W czasie kompilacji program Visual Studio kopiuje plik do katalogu `bin`.

### <a name="step-22-configure-the-application-settings"></a>Krok 2,2: Konfigurowanie ustawień aplikacji

W projekcie `AADB2C.UserMigration` Otwórz plik *App. config* . Zastąp następujące ustawienia aplikacji własnymi wartościami:

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - Użycie parametrów połączenia z tabelą platformy Azure jest opisane w następnych sekcjach.
> - Nazwa dzierżawy B2C jest domeną wprowadzoną podczas tworzenia dzierżawy i jest wyświetlana w Azure Portal. Nazwa dzierżawy zwykle jest zakończona sufiksem *. onmicrosoft.com* (na przykład *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Krok 2,3: uruchamianie procesu przed migracją

Kliknij prawym przyciskiem myszy rozwiązanie `AADB2C.UserMigration`, a następnie Skompiluj ponownie przykład. Jeśli się powiedzie, powinieneś mieć teraz plik wykonywalny `UserMigration.exe` znajdujący się w `AADB2C.UserMigration\bin\Debug\net461`. Aby uruchomić proces migracji, użyj jednego z następujących parametrów wiersza polecenia:

- Aby **migrować użytkowników przy użyciu hasła**, użyj polecenia `UserMigration.exe 1`.

- Aby **migrować użytkowników przy użyciu hasła losowego**, użyj polecenia `UserMigration.exe 2`. Ta operacja powoduje także utworzenie jednostki tabeli platformy Azure. Później można skonfigurować zasady, aby wywoływać usługę interfejsu API REST. Usługa używa tabeli platformy Azure do śledzenia procesu migracji i zarządzania nim.

![Okno wiersza polecenia przedstawiające dane wyjściowe polecenia UserMigration. exe](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Krok 2,4: sprawdzanie procesu przed migracją

Aby sprawdzić poprawność migracji, użyj jednej z następujących dwóch metod:

- Aby wyszukać użytkownika według nazwy wyświetlanej, użyj Azure Portal:

   1. Otwórz **Azure AD B2C**a następnie wybierz pozycję **Użytkownicy**.
   1. W polu wyszukiwania wpisz nazwę wyświetlaną użytkownika, a następnie Wyświetl profil użytkownika.

- Aby pobrać użytkownika przy użyciu adresu e-mail logowania, użyj przykładowej aplikacji:

   1. Uruchom następujące polecenie:

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > Możesz również pobrać użytkownika według nazwy wyświetlanej za pomocą następującego polecenia: `UserMigration.exe 4 "<Display name>"`.

   1. Otwórz plik UserProfile. JSON w edytorze JSON, aby wyświetlić informacje o użytkowniku.

      ![Plik UserProfile. JSON jest otwarty w edytorze Visual Studio Code](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>Krok 2,5: (opcjonalnie) oczyszczanie środowiska

Jeśli chcesz wyczyścić dzierżawę usługi Azure AD i usunąć użytkowników z katalogu usługi Azure AD, uruchom polecenie `UserMigration.exe 5`.

> [!NOTE]
> * Aby oczyścić dzierżawcę, Skonfiguruj uprawnienia administratora konta użytkownika dla aplikacji.
> * Przykładowa aplikacja do migracji czyści wszystkich użytkowników, którzy znajdują się na liście w pliku JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Krok 2,6: zalogowanie się przy użyciu zmigrowanych użytkowników (z hasłem)

Po uruchomieniu procesu sprzed migracji z hasłami użytkowników konta są gotowe do użycia, a użytkownicy mogą logować się do aplikacji przy użyciu Azure AD B2C. Jeśli nie masz dostępu do hasła użytkownika, przejdź do następnej sekcji.

## <a name="step-3-help-users-reset-their-password"></a>Krok 3. Pomóż użytkownikom zresetować swoje hasło

W przypadku migrowania użytkowników przy użyciu hasła losowego należy zresetować swoje hasło. Aby pomóc im zresetować hasło, Wyślij powitalną wiadomość e-mail z linkiem umożliwiającym zresetowanie hasła.

Aby uzyskać link do zasad resetowania hasła, wykonaj następujące kroki. W tej procedurze przyjęto założenie, że wcześniej utworzono [zasady niestandardowe](active-directory-b2c-get-started-custom.md)resetowania hasła.

1. Wybierz katalog zawierający dzierżawcę Azure AD B2C przy użyciu filtru **Directory + Subscription** w prawej górnej części [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Azure AD B2C** w menu po lewej stronie (lub w obszarze **wszystkie usługi**).
1. W obszarze **zasady**wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz Zasady resetowania hasła. Na przykład *B2C_1A_PasswordReset*.
1. Wybierz aplikację z listy rozwijanej **Wybierz aplikację** .

    > [!NOTE]
    > **Uruchomienie teraz** wymaga, aby co najmniej jedna aplikacja była zarejestrowana w dzierżawie. Aby dowiedzieć się, jak zarejestrować aplikacje, zobacz [Samouczek: rejestrowanie aplikacji w Azure Active Directory B2C][B2C-AppRegister].

1. Skopiuj adres URL wyświetlany w polu tekstowym **Uruchom teraz punkt końcowy** , a następnie wyślij go do użytkowników.

    ![Strona zasad resetowania hasła z wyróżnionym punktem końcowym Uruchom teraz](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Krok 4: (opcjonalnie) Zmień zasady, aby sprawdzić i ustawić stan migracji użytkownika

> [!NOTE]
> Aby sprawdzić i zmienić stan migracji użytkowników, należy użyć zasad niestandardowych. Instrukcje Set-up od [początku do zasad niestandardowych][B2C-GetStartedCustom] muszą zostać ukończone.

Gdy użytkownicy próbują zalogować się bez wcześniejszego resetowania hasła, zasady powinny zwrócić przyjazny komunikat o błędzie. Na przykład:

> *Twoje hasło wygasło. Aby zresetować ją, wybierz link resetowania hasła.*

Ten opcjonalny krok wymaga zastosowania zasad niestandardowych Azure AD B2C, zgodnie z opisem w artykule [wprowadzenie do zasad niestandardowych][B2C-GetStartedCustom] .

W tej sekcji zmienisz zasady, aby sprawdzić stan migracji użytkownika podczas logowania. Jeśli użytkownik nie zmienił hasła, należy zwrócić komunikat o błędzie HTTP 409, który prosi użytkownika o wybranie **zapomnianego hasła?**

Aby śledzić zmianę hasła, należy użyć tabeli platformy Azure. Po uruchomieniu procesu sprzed migracji z parametrem wiersza polecenia `2`można utworzyć jednostkę użytkownika w tabeli platformy Azure. Usługa wykonuje następujące czynności:

- Podczas logowania zasady Azure AD B2C wywoła usługę RESTful migracji, wysyłając wiadomość e-mail jako dane wejściowe. Usługa wyszukuje adres e-mail w tabeli platformy Azure. Jeśli adres istnieje, usługa zgłasza komunikat o błędzie: *należy zmienić hasło*.

- Po pomyślnym zmianie hasła przez użytkownika Usuń jednostkę z tabeli platformy Azure.

> [!NOTE]
> Używamy tabeli platformy Azure do uproszczenia przykładu. Stan migracji można zapisać w dowolnej bazie danych lub jako właściwość niestandardowa na koncie Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4,1: Zaktualizuj ustawienie aplikacji

1. Aby przetestować demonstrację interfejsu API RESTful, Otwórz `AADB2C.UserMigration.sln` w programie Visual Studio.
1. W projekcie `AADB2C.UserMigration.API` Otwórz plik *Web. config* . Zastąp ustawienie atrybutem skonfigurowanym w [kroku 2,2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Krok 4,2. wdrażanie aplikacji sieci Web do Azure App Service

W Eksplorator rozwiązań kliknij prawym przyciskiem myszy `AADB2C.UserMigration.API`, wybierz pozycję "Opublikuj...". Postępuj zgodnie z instrukcjami, aby publikować w Azure App Service. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji do Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Krok 4,3: Dodawanie profilu technicznego i sprawdzanie poprawności profilu technicznego do zasad

1. W Eksplorator rozwiązań rozwiń węzeł "elementy rozwiązania" i Otwórz plik zasad *TrustFrameworkExtensions. XML* .
1. Zmień pola `TenantId`, `PublicPolicyUri` i `<TenantId>` z `yourtenant.onmicrosoft.com` na nazwę dzierżawy.
1. W obszarze `<TechnicalProfile Id="login-NonInteractive">` Zastąp wszystkie wystąpienia `ProxyIdentityExperienceFrameworkAppId` i `IdentityExperienceFrameworkAppId` identyfikatorami aplikacji skonfigurowanymi w temacie [wprowadzenie do zasad niestandardowych][B2C-GetStartedCustom].
1. W węźle `<ClaimsProviders>` znajdź następujący fragment kodu XML. Zmień wartość `ServiceUrl`, aby wskazywała adres URL Azure App Service.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Poprzedni profil techniczny definiuje jedno z argumentów wejściowych: `signInName` (Wyślij jako adres e-mail). W przypadku logowania, to jest wysyłane do punktu końcowego RESTful.

Po zdefiniowaniu profilu technicznego dla interfejsu API RESTful należy skonfigurować istniejący profil techniczny `SelfAsserted-LocalAccountSignin-Email`, aby dodatkowo wywołać profil techniczny interfejsu API REST, zastępując go w pliku *TrustFrameworkExtensions. XML* :

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

Następnie zmień `Id` profilu technicznego `LocalAccountSignIn` na `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Krok 4,4: przekazywanie zasad do dzierżawy

1. W [Azure Portal][Portal]przejdź do [kontekstu Azure AD B2C dzierżawy][B2C-NavContext], a następnie wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **wszystkie zasady**.
1. Wybierz pozycję **Przekaż zasady**.
1. Zaznacz pole wyboru **Zastąp zasady, jeśli istnieje** .
1. Przekaż plik *TrustFrameworkExtensions. XML* i upewnij się, że przeszedł sprawdzanie poprawności.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Krok 4,5: testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz

1. Wybierz pozycję **Azure AD B2C**, a następnie wybierz pozycję **platforma obsługi tożsamości**.
1. Otwórz *B2C_1A_signup_signin*, załadowane zasady niestandardowe jednostki uzależnionej (RP), a następnie wybierz pozycję **Uruchom teraz**.
1. Wprowadź poświadczenia jednego z zmigrowanych użytkowników, a następnie wybierz pozycję **Zaloguj się**. Interfejs API REST powinien zgłosić następujący komunikat o błędzie:

    ![Strona rejestracji logowania z komunikatem o błędzie zmiany hasła](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Krok 4,6: (opcjonalnie) Rozwiązywanie problemów z interfejsem API REST

Informacje o rejestrowaniu można wyświetlać i monitorować w czasie niemal rzeczywistym.

1. W menu Ustawienia aplikacji RESTful, w obszarze **monitorowanie**wybierz pozycję **dzienniki diagnostyczne**.
1. Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.
1. Ustaw **poziom** na **pełne**.
1. Wybierz pozycję **Zapisz**.

    ![Strona konfiguracji dzienników diagnostycznych w Azure Portal](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. W menu **Ustawienia** wybierz pozycję **strumień dzienników**.
1. Sprawdź dane wyjściowe interfejsu API RESTful.

> [!IMPORTANT]
> Dzienników diagnostycznych należy używać tylko podczas tworzenia i testowania. Dane wyjściowe interfejsu API RESTful mogą zawierać poufne informacje, które nie powinny być ujawnione w środowisku produkcyjnym.

## <a name="optional-download-the-complete-policy-files"></a>Obowiązkowe Pobieranie kompletnych plików zasad

Po wykonaniu instruktażu wprowadzenie do [zasad niestandardowych][B2C-GetStartedCustom] zalecamy utworzenie scenariusza przy użyciu własnych niestandardowych plików zasad. W odniesieniu do Twojej dokumentacji udostępniono [przykładowe pliki zasad][UserMigrationSample-policy].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
