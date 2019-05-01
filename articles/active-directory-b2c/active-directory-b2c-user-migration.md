---
title: Zbliża się do migracji użytkowników w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Omówiono podstawowe i zaawansowane pojęcia dotyczące migracji użytkownika przy użyciu interfejsu API programu Graph i opcjonalnie za pomocą niestandardowych zasad usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a4195d7c292100712e6d68831443369ab793bb95
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726122"
---
# <a name="azure-active-directory-b2c-user-migration"></a>Azure Active Directory B2C: Migracja użytkowników
W przypadku migracji dostawcy tożsamości do usługi Azure Active Directory B2C (Azure AD B2C), może być również konieczne migracji konta użytkownika. W tym artykule wyjaśniono, jak przeprowadzić migrację istniejących kont użytkowników z dowolnego dostawcy tożsamości do usługi Azure AD B2C. Artykuł jest nie należy traktować jako przetestowanego rozwiązania ze szczegółami, ale raczej opisano w nim kilka scenariuszy. Deweloper jest odpowiedzialny za przydatności każde podejście.

## <a name="user-migration-flows"></a>Przepływy migracji użytkowników
Za pomocą usługi Azure AD B2C można przeprowadzić migrację użytkowników za pomocą [interfejsu API usługi Azure AD Graph][B2C-GraphQuickStart]. Proces migracji użytkownika należy do dwóch przepływów:

- **Przed migracją**: Ten przepływ ma zastosowanie, gdy masz albo wyczyść dostęp do poświadczeń użytkownika (nazwy użytkownika i hasło) lub poświadczenia są szyfrowane, ale można je odszyfrować. Proces migracji wstępnej obejmuje odczytywania użytkowników u starego dostawcy tożsamości i tworzenia nowych kont w katalogu usługi Azure AD B2C.

- **Przed migracją i resetowaniem hasła**: Ten przepływ ma zastosowanie, gdy hasło tego użytkownika nie jest dostępny. Na przykład:
   - Hasło jest przechowywane w formacie wyznaczania wartości skrótu.
   - Hasło jest przechowywane w dostawcy tożsamości, którego nie można uzyskać dostęp. Stary dostawca tożsamości weryfikuje poświadczenia użytkownika, przez wywołanie usługi sieci web.

W obu przepływów możesz najpierw uruchom proces migracji wstępnej, przeczytaj użytkowników ze starego dostawcy tożsamości i tworzenia nowych kont w katalogu usługi Azure AD B2C. Jeśli nie masz hasła, możesz utworzyć konto przy użyciu hasła, który jest generowany losowo. Następnie poproś użytkownika o zmianę hasła, lub gdy użytkownik loguje się po raz pierwszy, usługi Azure AD B2C z monitem o zresetowanie go.

## <a name="password-policy"></a>Zasady haseł
Zasady haseł usługi Azure AD B2C (dla kont lokalnych) jest oparta na zasadach usługi Azure AD. Usługa Azure AD B2C rejestracji lub logowania i hasło Resetuj zasady, użyj siły "silnego" hasła i wygaśnięcia hasła. Aby uzyskać więcej informacji, zobacz [zasad haseł usługi Azure AD][AD-PasswordPolicies].

Jeśli konta, które mają zostać zmigrowane, użyj słabszy siły hasła, niż [siły silne hasło, wymuszane przez usługę Azure AD B2C][AD-PasswordPolicies], możesz wyłączyć wymaganie silne hasło. Aby zmienić domyślne zasady haseł, należy ustawić `passwordPolicies` właściwość `DisableStrongPassword`. Na przykład utwórz żądanie użytkownika można zmodyfikować w następujący sposób:

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>Krok 1: Migrowanie użytkowników za pomocą interfejsu API programu Graph usługi Azure AD
Utworzysz konto użytkownika usługi Azure AD B2C za pośrednictwem interfejsu API programu Graph (przy użyciu hasła lub przy użyciu losowego hasła). W tej sekcji opisano proces tworzenia kont użytkowników w katalogu usługi Azure AD B2C przy użyciu interfejsu API programu Graph.

### <a name="step-11-register-your-application-in-your-tenant"></a>Krok 1.1: Zarejestruj swoją aplikację w dzierżawie
Aby komunikować się za pomocą interfejsu API programu Graph, należy najpierw musi mieć konto usługi z uprawnieniami administracyjnymi. W usłudze Azure AD należy zarejestrować aplikację i uwierzytelniania usługi Azure AD. Poświadczenia aplikacji są **identyfikator aplikacji** i **klucz tajny aplikacji**. Aplikacja działa w swoim imieniu, nie jako użytkownik, do wywołania interfejsu API programu Graph.

Najpierw zarejestrować aplikację migracji w usłudze Azure AD. Utwórz klucz aplikacji (klucz tajny aplikacji) i ustaw ją z uprawnieniami do zapisu.

1. Zaloguj się w witrynie [Azure Portal][Portal].
   
1. Wybierz usługę Azure AD **B2C** dzierżawy, wybierając swoje konto w prawym górnym rogu okna.
   
1. W okienku po lewej stronie wybierz **usługi Azure Active Directory** (nie usługi Azure AD B2C). Aby ją znaleźć, konieczne może być wybierz **więcej usług**.
   
1. Wybierz pozycję **Rejestracje aplikacji**.
   
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
   
   ![Rejestrowanie nowej aplikacji](media/active-directory-b2c-user-migration/pre-migration-app-registration.png)
   
1. Utwórz nową aplikację, wykonując następujące czynności:
   - Dla **nazwa**, użyj **B2CUserMigration** lub dowolnej innej nazwy, które chcesz.
   - Aby uzyskać **typ aplikacji**, użyj **aplikacji/interfejs API sieci Web**.
   - Aby uzyskać **adres URL logowania**, użyj `https://localhost` (co nie jest odpowiednie dla tej aplikacji).
   - Wybierz pozycję **Utwórz**.
   
1. Po utworzeniu aplikacji w **aplikacje** wybierz nowo utworzony **B2CUserMigration** aplikacji.
   
1. Wybierz **właściwości**, kopia **identyfikator aplikacji**i zapisz go na później.

### <a name="step-12-create-the-application-secret"></a>Krok 1.2: Utwórz klucz tajny aplikacji
1. W witrynie Azure portal **zarejestrowana aplikacja** wybierz **klucze**.
   
1. Dodaj nowy klucz (znany także jako klucz tajny klienta), a następnie skopiuj klucz do późniejszego użycia.
   
   ![Identyfikator aplikacji i klucze](media/active-directory-b2c-user-migration/pre-migration-app-id-and-key.png)
   
### <a name="step-13-grant-administrative-permission-to-your-application"></a>Krok 1.3. Przyznanie uprawnień administracyjnych do aplikacji
1. W witrynie Azure portal **zarejestrowana aplikacja** wybierz **wymagane uprawnienia**.

1. Wybierz **Windows Azure Active Directory**.
   
1. W **Włącz dostęp za pomocą** okienko, w obszarze **uprawnienia aplikacji**, wybierz opcję **odczytu i zapisu danych katalogu**, a następnie wybierz pozycję **Zapisz**.
   
1. W **wymagane uprawnienia** okienku wybierz **Udziel uprawnień**.
   
   ![Uprawnienia aplikacji](media/active-directory-b2c-user-migration/pre-migration-app-registration-permissions.png)
   
Teraz gdy masz już aplikację z uprawnieniami do tworzenia, odczytywania i aktualizowania użytkowników w dzierżawie usługi Azure AD B2C.

### <a name="step-14-optional-environment-cleanup"></a>Krok 1.4. (Opcjonalnie) Czyszczenie środowiska
Odczyt i zapis, czy uprawnienia do danych katalogu *nie* obejmują prawo do usuwania użytkowników. Aby zapewnić aplikacji możliwość usuwania użytkowników (czyszczenie środowiska), należy wykonać dodatkowe czynności, które polega na uruchomieniu programu PowerShell, aby ustawić uprawnienia Administrator kont użytkowników. W przeciwnym razie możesz przejść do następnej sekcji.

> [!IMPORTANT]
> Należy użyć konta administratora dzierżawy B2C, który jest *lokalnego* do dzierżawy usługi B2C. Składnia nazwy konta jest *administratora\@contosob2c.onmicrosoft.com*.

>[!NOTE]
> Poniższy skrypt programu PowerShell wymaga [usługi Azure Active Directory PowerShell w wersji 2][AD-Powershell].

Ten skrypt programu PowerShell wykonaj następujące czynności:
1. Łączenie z usługą online. Aby to zrobić, uruchom `Connect-AzureAD` polecenia cmdlet w programie Windows PowerShell, wiersz polecenia i podaj swoje poświadczenia.
   
1. Użyj **identyfikator aplikacji** przypisania aplikacji z roli użytkownika konta administratora. Te role mają dobrze znane identyfikatory, więc wszystko, czego potrzebujesz, aby zrobić to, wprowadź swoje **identyfikator aplikacji** w skrypcie.
   
```powershell
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

Zmiana `$AppId` wartości za pomocą usługi Azure AD **identyfikator aplikacji**.

## <a name="step-2-pre-migration-application-sample"></a>Krok 2: Przykład aplikacji przed migracją
[Pobieranie i uruchamianie przykładowego kodu][UserMigrationSample]. Możesz ją pobrać jako plik .zip.

### <a name="step-21-edit-the-migration-data-file"></a>Krok 2.1. Edytuj plik danych migracji
Przykładowa aplikacja używa pliku JSON, który zawiera dane użytkownika fikcyjnego z rolą. Po pomyślnym uruchomieniu przykładu możesz zmienić kod do pracy z danymi z własną bazę danych. Lub można wyeksportować profilu użytkownika do pliku JSON, a następnie ustaw aplikację, aby użyć tego pliku.

Aby edytować plik JSON, otwórz `AADB2C.UserMigration.sln` rozwiązania Visual Studio. W `AADB2C.UserMigration` otwarty projekt `UsersData.json` pliku.

![Plik danych użytkownika](media/active-directory-b2c-user-migration/pre-migration-data-file.png)

Jak widać, plik zawiera listę jednostek użytkownika. Każda jednostka użytkownika ma następujące właściwości:
- email
- displayName
- firstName
- lastName
- hasło (może być puste)

> [!NOTE]
> W czasie kompilacji program Visual Studio kopiuje plik do `bin` katalogu.

### <a name="step-22-configure-the-application-settings"></a>Krok 2.2. Konfigurowanie ustawień aplikacji
W obszarze `AADB2C.UserMigration` otwarty projekt *App.config* pliku. Zastąp następujące ustawienia aplikacji przy użyciu własnych wartości:

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
> - Korzystanie z parametrów połączenia usługi Azure table jest opisane w kolejnych sekcjach.
> - Nazwa dzierżawy B2C jest domeną, która wprowadzona podczas tworzenia dzierżawy i jest wyświetlany w witrynie Azure portal. Nazwa dzierżawy kończy się zazwyczaj z sufiksem *. onmicrosoft.com* (na przykład *contosob2c.onmicrosoft.com*).

### <a name="step-23-run-the-pre-migration-process"></a>Krok 2.3 Uruchom proces migracji wstępnej
Kliknij prawym przyciskiem myszy `AADB2C.UserMigration` rozwiązania, a następnie ponownej kompilacji próbki. Po pomyślnym wykonaniu `UserMigration.exe` plik wykonywalny znajduje się w `AADB2C.UserMigration\bin\Debug\net461`. Aby uruchomić proces migracji, użyj jednej z poniższych parametrów wiersza polecenia:

- Aby **migrację użytkowników za pomocą hasła**, użyj `UserMigration.exe 1` polecenia.

- Aby **migrację użytkowników za pomocą hasła losowego**, użyj `UserMigration.exe 2` polecenia. Ta operacja tworzy również jednostkę tabeli platformy Azure. Następnie należy skonfigurować zasady aby wywołać usługę interfejsu API REST. Usługa używa tabeli platformy Azure do śledzenia i zarządzania procesem migracji.

![Pokaz procesu migracji](media/active-directory-b2c-user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>Krok 2.4 Sprawdź proces migracji wstępnej
Aby sprawdzić poprawność migracji, należy użyć jednej z następujących dwóch metod:

- Aby wyszukać konkretnego użytkownika według nazwy wyświetlanej, należy użyć witryny Azure portal:
   
   1. Otwórz **usługi Azure AD B2C**, a następnie wybierz pozycję **użytkowników i grup**.
   
   1. W polu wyszukiwania wpisz nazwę wyświetlaną tego użytkownika, a następnie Wyświetl profil użytkownika.
   
- Aby pobrać użytkownika według adresu e-mail logowania, należy użyć tej aplikacji przykładowej:
   
   1. Uruchom następujące polecenie:
   
      ```Console
          UserMigration.exe 3 {email address}
      ```
      
      > [!TIP]
      > Możesz również pobrać użytkownika według nazwy wyświetlanej, za pomocą następującego polecenia: `UserMigration.exe 4 "<Display name>"`.
      
   1. Otwórz plik UserProfile.json w edytorze JSON, aby wyświetlić informacje o użytkowniku.
   
      ![Plik UserProfile.json](media/active-directory-b2c-user-migration/pre-migration-get-by-email2.png)
      
### <a name="step-25-optional-environment-cleanup"></a>Krok 2.5 (Opcjonalnie) Czyszczenie środowiska
Jeśli chcesz wyczyścić dzierżawy usługi Azure AD Konfigurowanie i usuwanie użytkowników z katalogu usługi Azure AD, uruchom `UserMigration.exe 5` polecenia.

> [!NOTE]
> * Aby wyczyścić swoją dzierżawę, należy skonfigurować uprawnienia Administrator kont użytkowników dla aplikacji.
> * Przykładowa aplikacja migracji czyści wszyscy użytkownicy, którzy są wymienione w pliku JSON.

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>Krok 2.6: Zaloguj się przy użyciu migrowanych użytkowników (przy użyciu hasła)
Po uruchomieniu proces migracji wstępnej przy użyciu haseł użytkowników kont są gotowe do użycia, a użytkownicy mogą się logować do swojej aplikacji za pomocą usługi Azure AD B2C. Jeśli nie mają dostępu do hasła użytkowników, przejdź do następnej sekcji.

## <a name="step-3-help-users-reset-their-password"></a>Krok 3: Pomaganie użytkownikom zresetowanie hasła
W przypadku migrowania użytkowników za pomocą hasła losowego one należy zresetować swoje hasło. Aby pomóc im zresetować hasło, należy wysłać powitalną wiadomość e-mail z linkiem do resetowania hasła.

Aby uzyskać link do zasad resetowania hasła, wykonaj następujące czynności:

1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie wybierz pozycję **Resetuj hasło** właściwości zasad.

1. Wybierz swoją aplikację.

    > [!NOTE]
    > Uruchom teraz wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie] [ B2C-GetStarted] artykułu lub [rejestracji aplikacji] [ B2C-AppRegister] artykułu.

1. Wybierz **Uruchom teraz**, a następnie sprawdź zasady.

1. W **Uruchom punkt końcowy do natychmiastowego** polu, skopiuj adres URL i wysłać go do użytkowników.

    ![Dzienniki diagnostyczne zestawu](media/active-directory-b2c-user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>Krok 4: (Opcjonalnie) Zmień zasady, aby sprawdzić i ustawić stan migracji użytkowników

> [!NOTE]
> Aby sprawdzić i zmienić stan migracji użytkownika, należy użyć zasad niestandardowych. Instrukcje dotyczące konfiguracji od [wprowadzenie do zasad niestandardowych] [ B2C-GetStartedCustom] muszą zostać wykonane.
>

Gdy użytkownicy próbują zalogować bez resetowania hasła, najpierw, zasad powinien zwrócić przyjazny komunikat o błędzie. Na przykład:
>*Twoje hasło wygasło. Aby ją zresetować, wybierz link resetowania hasła.*

Ten opcjonalny etap wymagane jest użycie niestandardowych zasad usługi Azure AD B2C, zgodnie z opisem w [wprowadzenie do zasad niestandardowych] [ B2C-GetStartedCustom] artykułu.

W tej sekcji możesz zmienić zasady Aby sprawdzić stan migracji użytkownika zalogowaniu. Jeśli użytkownik nie zmienił hasło, zwraca komunikat o błędzie HTTP 409, która prosi użytkownika o wybranie **nie pamiętasz hasła?** łącza.

Aby śledzić zmiany hasła, należy użyć tabeli platformy Azure. Po uruchomieniu proces migracji wstępnej za pomocą parametru wiersza polecenia `2`, tworzyć jednostki użytkownika w tabeli platformy Azure. Usługa zapewnia następujące funkcje:

- Na logowanie zasady usługi Azure AD B2C wywołuje migracji do usługi RESTful, wysyłając wiadomość e-mail jako danych wejściowych roszczenia. Usługa wyszukuje adres e-mail w tabeli platformy Azure. Jeśli istnieje adres, Usługa generuje komunikat o błędzie: *Musisz zmienić hasło*.

- Po użytkownik pomyślnie zmienia hasło, należy usunąć jednostki z tabeli platformy Azure.

>[!NOTE]
>Używamy tabeli platformy Azure, aby uprościć przykład. Stan migracji można przechowywać w dowolnej bazie danych lub jako właściwości niestandardowych w ramach konta usługi Azure AD B2C.

### <a name="41-update-your-application-setting"></a>4.1: Zaktualizuj ustawienie aplikacji
1. Aby przetestować wersji demonstracyjnej interfejsu API RESTful, otwórz `AADB2C.UserMigration.sln` w programie Visual Studio.

1. W `AADB2C.UserMigration.API` otwarty projekt *Web.config* pliku. Zastąp ustawienia skonfigurowane w [krok 2.2](#step-22-configure-the-application-settings):

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>Krok 4.2 Wdrażanie aplikacji sieci web w usłudze Azure App Service
W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy `AADB2C.UserMigration.API`, wybierz pozycję "Opublikuj...". Postępuj zgodnie z instrukcjami, aby opublikować w usłudze Azure App Service. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji w usłudze Azure App Service][AppService-Deploy].

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>Krok 4.3: Dodaj do swoich zasad profilu technicznego i weryfikacji profilu technicznego
1. W Eksploratorze rozwiązań rozwiń "Elementy rozwiązania", a następnie otwórz *TrustFrameworkExtensions.xml* plik zasad.
1. Zmiana `TenantId`, `PublicPolicyUri` i `<TenantId>` pola z `yourtenant.onmicrosoft.com` na nazwę Twojej dzierżawy.
1. W obszarze `<TechnicalProfile Id="login-NonInteractive">` element, zastąpić wszystkie wystąpienia `ProxyIdentityExperienceFrameworkAppId` i `IdentityExperienceFrameworkAppId` o identyfikatorach aplikacji skonfigurowanych w [wprowadzenie do zasad niestandardowych][B2C-GetStartedCustom].
1. W obszarze `<ClaimsProviders>` węzła, znajdź następujący fragment kodu XML. Zmień wartość właściwości `ServiceUrl` wskaż adres URL usługi aplikacji Azure.

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

Poprzedni profilu technicznego definiuje jedno oświadczenie wejściowe: `signInName` (Wyślij jako adres e-mail). Na logowanie oświadczenia są wysyłane do punktu końcowego usługi RESTful.

Po zdefiniowaniu profilu technicznego dla interfejsu API RESTful, poinformuj zasady usługi Azure AD B2C, aby wywołać profilu technicznego. Fragment kodu XML zastępuje `SelfAsserted-LocalAccountSignin-Email`, która została zdefiniowana w zasadach podstawowych. Fragment kodu XML również dodaje `ValidationTechnicalProfile`, za pomocą ReferenceId, wskazując polecenie usługi profilu technicznego `LocalAccountUserMigration`.

### <a name="step-44-upload-the-policy-to-your-tenant"></a>Krok 4.4 Przekazywanie zasad dla Twojej dzierżawy
1. W [witryny Azure portal][Portal], przełącz się do [kontekstu dzierżawy usługi Azure AD B2C][B2C-NavContext], a następnie wybierz pozycję **usługi Azure AD B2C**.

1. Wybierz **struktura środowiska tożsamości**.

1. Wybierz **wszystkie zasady**.

1. Wybierz **przekazywać zasady**.

1. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.

1. Przekaż *TrustFrameworkExtensions.xml* pliku i upewnij się, że przekazuje sprawdzania poprawności.

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>Krok 4.5: Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie przejdź do **struktura środowiska tożsamości**.

1. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, które przekazane, a następnie wybierz **Uruchom teraz**.

1. Spróbuj zalogować się przy użyciu jednego z migrowanych użytkowników poświadczenia, a następnie wybierz **Sign In**. Interfejs API REST powinien zgłaszać następujący komunikat o błędzie:

    ![Dzienniki diagnostyczne zestawu](media/active-directory-b2c-user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>Krok 4.6: (Opcjonalnie) Rozwiązywanie problemów z interfejsu API REST
Możesz wyświetlić i monitorować informacje rejestrowania w czasie niemal rzeczywistym.

1. W menu Ustawienia aplikacji RESTful w obszarze **monitorowanie**, wybierz opcję **dzienniki diagnostyczne**.

1. Ustaw **rejestrowanie aplikacji (system plików)** do **na**.

1. Ustaw **poziom** do **pełne**.

1. Wybierz **Zapisz**

    ![Dzienniki diagnostyczne zestawu](media/active-directory-b2c-user-migration/pre-migration-diagnostic-logs.png)

1. Na **ustawienia** menu, wybierz opcję **strumień dziennika**.

1. Sprawdź dane wyjściowe interfejsu API RESTful.

> [!IMPORTANT]
> Dzienniki diagnostyczne należy używać tylko podczas tworzenia i testowania aplikacji. Interfejs API RESTful danych wyjściowych może zawierać informacje poufne, które nie powinny zostać ujawnione w środowisku produkcyjnym.
>

## <a name="optional-download-the-complete-policy-files"></a>(Opcjonalnie) Pobierz pliki pełną zasad
Po ukończeniu [wprowadzenie do zasad niestandardowych] [ B2C-GetStartedCustom] wskazówki, firma Microsoft zaleca tworzenie scenariusza za pomocą plików zasad niestandardowych. Dla Twojej informacji udostępniliśmy [przykładowe pliki zasad][UserMigrationSample].

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample]: https://github.com/yoelhor/Azure-AD-B2C-UserMigration
