---
title: Uwierzytelniania za pomocą usługi Azure Active Directory dostępu do obiektów blob i kolejek danych z aplikacji klienta
description: Usługa Azure Active Directory do uwierzytelniania z aplikacji klienckiej, uzyskanie tokenu OAuth 2.0 i autoryzacji żądania do usługi Azure Blob storage i Queue storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/05/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 30ebfec88182684f8e852808e978a51854389898
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073416"
---
# <a name="authenticate-with-azure-active-directory-from-an-application-for-access-to-blobs-and-queues"></a>Uwierzytelnianie za pomocą usługi Azure Active Directory z aplikacji do dostępu do obiektów blob i kolejek

Zaletą przy użyciu usługi Azure Active Directory (Azure AD) przy użyciu usługi Azure Blob storage i Queue storage jest, że poświadczenia nie są już potrzebne, mają być przechowywane w kodzie. Zamiast tego tokenu dostępu OAuth 2.0 możesz poprosić platforma tożsamości firmy Microsoft (dawniej Azure AD). Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupy lub jednostki usługi) uruchomiona jest aplikacja. Jeśli uwierzytelnianie zakończy się powodzeniem, usługa Azure AD zwraca token dostępu do aplikacji i aplikacji można następnie użyć tokenu dostępu do autoryzacji żądania do usługi Azure Blob storage i Queue storage.

W tym artykule przedstawiono sposób skonfiguruj natywną aplikację lub aplikację sieci web dla uwierzytelniania z platformą tożsamości Microsoft w wersji 2.0. Podobne metody, z których można użyć .NET funkcji przykładowy kod, ale w innych językach. Aby uzyskać więcej informacji o platformie tożsamości firmy Microsoft w wersji 2.0, zobacz [Przegląd platformy (w wersji 2.0) programu Microsoft identity](../../active-directory/develop/v2-overview.md).

Aby uzyskać omówienie przepływie przyznawania kodu OAuth 2.0, zobacz [Autoryzuj dostęp do aplikacji sieci web usługi Azure Active Directory przy użyciu kodu uwierzytelniania OAuth 2.0 udzielić przepływ](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Przypisywanie roli do podmiotu zabezpieczeń usługi Azure AD

Na potrzeby uwierzytelniania podmiotu zabezpieczeń z poziomu aplikacji usługi Azure Storage, należy najpierw skonfigurować ustawienia kontroli (RBAC) dostępu opartej na rolach dla tego podmiotu zabezpieczeń. Usługa Azure Storage definiuje wbudowane role kontroli RBAC, które obejmują uprawnienia do kontenerów i kolejek. Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń, tego podmiotu zabezpieczeń ma uprawnienia do tego zasobu. Aby uzyskać więcej informacji, zobacz [Zarządzaj praw dostępu do danych obiektów Blob platformy Azure i kolejek przy użyciu RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Zarejestruj swoją aplikację z dzierżawy usługi Azure AD

Pierwszym krokiem przy użyciu usługi Azure AD do autoryzowania dostępu do zasobów magazynu jest zarejestrowanie aplikacji klienckiej z dzierżawy usługi Azure AD z [witryny Azure portal](https://portal.azure.com). Po zarejestrowaniu aplikacji klienckiej, należy podać informacje o aplikacji do usługi Azure AD. Następnie usługa Azure AD zawiera identyfikator klienta (nazywanych również *identyfikator aplikacji*) umożliwia kojarzenie aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o identyfikatorze klienta, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować swoją aplikację usługi Azure Storage, wykonaj kroki opisane w [Szybki Start: Rejestrowanie aplikacji z platformą tożsamości Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). Na poniższej ilustracji przedstawiono typowe ustawienia rejestracji aplikacji sieci web:

![Zrzut ekranu przedstawiający sposób zarejestrować aplikację magazynu z usługą Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Jeśli zarejestrujesz aplikację jako natywną aplikację, można określić dowolny prawidłowy identyfikator URI dla **identyfikator URI przekierowania**. Dla natywnych aplikacji ta wartość nie musi być prawdziwy adres URL. Dla aplikacji sieci web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, do której podano tokenów.

Po zarejestrowaniu aplikacji zobaczysz identyfikator aplikacji (lub identyfikator klienta) w obszarze **ustawienia**:

![Zrzut ekranu przedstawiający identyfikator klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Udzielanie uprawnień zarejestrowana aplikacja do usługi Azure Storage

Następnie udziel aplikacji uprawnień do wywoływania interfejsów API usługi Azure Storage. Ten krok powoduje włączenie aplikacji w taki sposób autoryzować żądania do usługi Azure Storage z usługą Azure AD.

1. Na **Przegląd** strony zarejestrowanej aplikacji, wybierz opcję **wyświetlanie uprawnień interfejsu API**.
1. W **uprawnienia do interfejsu API** zaznacz **Dodaj uprawnienia** i wybierz polecenie **Moja organizacja korzysta z interfejsów API**.
1. W obszarze **Moja organizacja korzysta z interfejsów API** sekcji, wyszukaj frazę "Usługi Azure Storage" i wybierz **usługi Azure Storage** z listy wyników do wyświetlenia **uprawnienia do żądania interfejsu API** okienko.

    ![Zrzut ekranu przedstawiający uprawnienia do magazynu](media/storage-auth-aad-app/registered-app-permissions-1.png)

1. W obszarze **jakiego rodzaju uprawnień aplikacji wymaga?** , sprawdź, czy uprawnienia dostępne jest **delegowane uprawnienia**. Ta opcja jest wybrana domyślnie.
1. W **wybierz uprawnienia** części **uprawnienia do żądania interfejsu API** okienku zaznacz pole wyboru obok pozycji **user_impersonation**, następnie kliknij przycisk **Dodaj uprawnienia**.
1. **Uprawnienia do interfejsu API** okienka teraz wskazuje, że aplikacja usługi Azure AD ma dostęp do programu Microsoft Graph i Azure Storage. Uprawnienia są przyznawane w programie Microsoft Graph automatycznie podczas rejestrowania aplikacji z usługą Azure AD.

    ![Zrzut ekranu przedstawiający zarejestrować uprawnień aplikacji](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Utwórz klucz tajny klienta

Aplikacja musi klucz tajny klienta, aby potwierdzić swoją tożsamość, gdy żądania tokenu. Aby dodać wpis tajny klienta, wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. Wybierz **certyfikaty i klucze tajne** ustawienie.
1. W obszarze **wpisów tajnych klienta**, kliknij przycisk **nowy wpis tajny klienta** Aby utworzyć nowy klucz tajny.
1. Podaj opis klucza tajnego, a następnie wybierz interwał wygasania żądaną.
1. Natychmiast skopiuj wartość nowy wpis tajny do bezpiecznej lokalizacji. Pełna wartość jest wyświetlany tylko raz.

    ![Zrzut ekranu przedstawiający klucz tajny](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Biblioteki klienckie dla tokenu

Po rejestracji aplikacji i przyznał uprawnienia dostępu do danych w usłudze Azure Blob storage i Queue storage, można dodać kod do aplikacji uwierzytelniania podmiotu zabezpieczeń i uzyskania tokenu OAuth 2.0. Do uwierzytelniania i uzyskania tokenu, można użyć jednej z [bibliotek uwierzytelniania usługi Microsoft identity platformy](../../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki typu open source, który obsługuje OpenID Connect 1.0. Aplikacja może następnie używać tokenu dostępu do autoryzowania żądanie względem usługi Azure Blob storage i Queue storage.

Aby uzyskać listę scenariuszy, dla których jest obsługiwana uzyskiwania tokenów, zobacz [scenariuszy](https://aka.ms/msal-net-scenarios) części [Microsoft Authentication Library (MSAL) dla platformy .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repozytorium GitHub.

## <a name="net-code-example-create-a-block-blob"></a>Przykładowy kod platformy .NET: Utwórz blokowy obiekt blob

Przykład kodu pokazuje, jak uzyskiwanie dostępu do tokenu z usługi Azure AD. Token dostępu jest używany do uwierzytelniania określonego użytkownika, a następnie Autoryzuj żądanie, aby utworzyć blokowego obiektu blob. Aby to działało próbki, najpierw wykonaj kroki opisane w poprzednich sekcjach.

Do żądania tokenu, potrzebne będą następujące wartości z rejestracji aplikacji:

- Nazwa Twojej domeny usługi Azure AD. Pobierania tej wartości z **Przegląd** strony usługi Azure Active Directory.
- Identyfikator dzierżawy (lub katalogu). Pobierania tej wartości z **Przegląd** strony rejestracji aplikacji.
- Identyfikator klienta (lub aplikacji). Pobierania tej wartości z **Przegląd** strony rejestracji aplikacji.
- Klient identyfikatora URI przekierowania. Pobierania tej wartości z **uwierzytelniania** ustawienia rejestracji aplikacji.
- Wartość wpisu tajnego klienta. Tę wartość należy pobrać z lokalizacji, do której został wcześniej skopiowany.

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Dobrze znane wartości do uwierzytelniania przy użyciu usługi Azure AD

Na potrzeby uwierzytelniania podmiotu zabezpieczeń w usłudze Azure AD, należy niektóre znane wartości mają zostać uwzględnione w kodzie.

#### <a name="azure-ad-authority"></a>Urząd usługi Azure AD

Dla chmury publicznej firmy Microsoft, Azure podstawowy urząd usługi AD jest następująca, gdzie *identyfikator dzierżawy* jest identyfikator dzierżawy usługi Active Directory (lub identyfikator katalogu):

`https://login.microsoftonline.com/<tenant-id>/`

Identyfikator dzierżawy określa dzierżawy usługi Azure AD do uwierzytelniania. Jest ona również określana do jako identyfikator katalogu. Aby pobrać identyfikator dzierżawy, przejdź do **Przegląd** stronie rejestracji aplikacji w witrynie Azure portal, a następnie skopiuj wartość w tym miejscu.

#### <a name="storage-resource-id"></a>Identyfikator zasobu magazynu

Identyfikator zasobu usługi Azure Storage umożliwia uzyskanie tokenu autoryzacji żądania do usługi Azure Storage:

`https://storage.azure.com/`

### <a name="create-a-storage-account-and-container"></a>Utwórz konto magazynu i kontener

Aby uruchomić przykładowy kod, Utwórz konto magazynu w ramach tej samej subskrypcji co usługi Azure Active Directory. Następnie należy utworzyć kontener w ramach tego konta magazynu. Przykładowy kod utworzy blokowych obiektów blob w tym kontenerze.

Następnie jawnie przypisać **Współautor danych obiektu Blob magazynu** roli do konta użytkownika, na którym będzie uruchamiana w przykładowym kodzie. Aby uzyskać instrukcje dotyczące sposobu przypisywania tej roli w witrynie Azure portal, zobacz [udzielić dostępu do obiektów blob i kolejek danych Azure przy użyciu funkcji RBAC w witrynie Azure portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Podczas tworzenia konta usługi Azure Storage, możesz nie są automatycznie przypisane uprawnienia dostępu do danych za pomocą usługi Azure AD. Należy jawnie przypisać sobie rolę RBAC dla usługi Azure Storage. Można ją przypisać na poziomie subskrypcji, grupy zasobów, konto magazynu lub kontenera lub kolejki.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Tworzenie aplikacji sieci web, które autoryzują dostęp do usługi Blob storage z usługą Azure AD

Gdy aplikacja uzyskuje dostęp do usługi Azure Storage, tak dalej w imieniu użytkownika, co oznacza, że zasoby obiektów blob lub kolejki są dostępne przy użyciu uprawnień użytkownika, który jest zalogowany. Aby wypróbować ten przykład kodu, musisz mieć aplikacji sieci web, który monituje użytkownika o zalogowanie się przy użyciu tożsamości usługi Azure AD. Możesz utworzyć własne lub za pomocą aplikacji przykładowej, obsługiwane przez firmę Microsoft.

Ukończone przykładowej aplikacji sieci web, który uzyskuje token i używa jej do utworzenia obiektu blob w usłudze Azure Storage jest dostępna na [GitHub](http://aka.ms/aadstorage). Recenzowanie i uruchamianie przykładowych ukończone mogą być pomocne w zrozumieniu przykłady kodu. Instrukcje dotyczące sposobu uruchamiania przykładu ukończone, zobacz sekcję pod tytułem [widoku i uruchom przykładowy ukończone](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Dodaj odwołania i przy użyciu instrukcji  

W programie Visual Studio zainstaluj bibliotekę klienta usługi Azure Storage. Z **narzędzia** menu, wybierz opcję **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**. Wpisz następujące polecenia w oknie konsoli aby zainstalować wymagane pakiety z biblioteki klienta usługi Azure Storage dla platformy .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Następnie dodaj następujące instrukcje using do pliku HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Utwórz blokowy obiekt blob

Dodaj poniższy fragment kodu, aby utworzyć blokowego obiektu blob:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Aby autoryzować obiektów blob i kolejek operacji przy użyciu tokenu OAuth 2.0, musi używać protokołu HTTPS.

W powyższym przykładzie biblioteki klienta .NET obsługuje autoryzacji żądania, aby utworzyć blokowego obiektu blob. Usługa Azure bibliotek klienckich magazynu dla innych języków również obsługiwać autoryzacji żądania dla Ciebie. Jednak w przypadku wywołania operacji usługi Azure Storage przy użyciu tokenu OAuth za pomocą interfejsu API REST, następnie należy autoryzować żądania przy użyciu tokenu OAuth.

Aby wywoływać operacje usługi obiektów Blob i kolejek, przy użyciu tokenów dostępu protokołu OAuth, należy przekazać token dostępu w **autoryzacji** przy użyciu nagłówka **elementu nośnego** schemat, a następnie określ usługę w wersji 2017-11-09 wyższa, jako pokazano w poniższym przykładzie:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Pobierz OAuth token z usługi Azure AD

Następnie dodaj metodę, która żąda tokenu z usługi Azure AD. Token, który w przypadku żądania będą w imieniu użytkownika, a firma Microsoft użyje metody GetTokenOnBehalfOfUser.

Należy pamiętać, że jeśli niedawno logowali się i chcesz uzyskać token dla `storage.azure.com` zasobu, należy przedstawić użytkownikowi za pomocą interfejsu użytkownika, gdy użytkownik mogą wyrazić zgodę na takie działanie w ich imieniu. Ułatwianie trzeba przechwycić `MsalUiRequiredException` i Dodaj funkcje w celu zażądania zgody użytkownika, jak pokazano w poniższym przykładzie:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties = BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Zgoda jest proces udzielania autoryzacji do aplikacji w celu dostępu do chronionych zasobów w imieniu użytkownika. Platforma tożsamości firmy Microsoft w wersji 2.0 obsługuje przyrostową zgody, co oznacza, że podmiot zabezpieczeń można początkowo żądania minimalny zestaw uprawnień i Dodaj uprawnienia wraz z upływem czasu, zgodnie z potrzebami. Gdy kod żąda tokenu dostępu, należy określić zakres uprawnień, które Twoja aplikacja wymaga w dowolnym momencie, w `scope` parametru. Aby uzyskać więcej informacji na temat przyrostowej zgody, zobacz sekcję pod tytułem **zgody przyrostowych i dynamiczne** w [Dlaczego aktualizacji do platforma tożsamości firmy Microsoft (w wersji 2.0)?](../../active-directory/develop/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Metoda następujące konstrukcje właściwości uwierzytelniania żądania przyrostowe zgody:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes, MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET / MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope, scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Wyświetlanie i uruchamianie aplikacji przykładowej ukończone

Uruchom przykładową aplikację, najpierw sklonuj lub Pobierz go z [GitHub](http://aka.ms/aadstorage). Następnie należy zaktualizować aplikację, zgodnie z opisem w poniższych sekcjach.

### <a name="provide-values-in-the-settings-file"></a>Podaj wartości w pliku ustawień

Następnie zaktualizuj *appsettings.json* plików przy użyciu własnych wartości w następujący sposób:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Zaktualizuj nazwę konta i kontenera magazynu

W *HomeController.cs* plików, zaktualizować identyfikatora URI, który odwołuje się do blokowych obiektów blob, aby używał nazwy konta magazynu i kontener:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Włącz przepływ przyznawanie niejawne

Aby uruchomić przykład, może być konieczne skonfigurowanie przepływ przyznawanie niejawne dla Twojej rejestracji aplikacji. Wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. W sekcji Zarządzanie wybierz **uwierzytelniania** ustawienie.
1. W obszarze **Zaawansowane ustawienia**w **przyznawanie niejawne** sekcji, zaznacz pola wyboru, aby włączyć tokenami dostępu i tokeny Identyfikatora, jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu przedstawiający sposób włączyć ustawienia usługi flow przyznawanie niejawne](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Zaktualizuj port używany przez hosta lokalnego

Po uruchomieniu przykładu, może się okazać konieczne aktualizowanie przekierowania URI określonego w rejestrację aplikacji w taki sposób, aby użyć *localhost* portów przypisane w czasie wykonywania. Aby zaktualizować identyfikatora URI przekierowania do korzystania z portu przypisanego, wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. W sekcji Zarządzanie wybierz **uwierzytelniania** ustawienie.
1. W obszarze **identyfikatory URI przekierowań**, zmodyfikuj port, aby dopasować używanej przez przykładową aplikację, jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu przedstawiający identyfikatory URI przekierowania dla rejestracji aplikacji](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o platformie tożsamości firmy Microsoft, zobacz [platforma tożsamości usługi Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Aby dowiedzieć się więcej na temat ról RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych przy użyciu RBAC](storage-auth-aad-rbac.md).
- Aby dowiedzieć się więcej o korzystaniu z zarządzanych tożsamości dla zasobów platformy Azure z usługą Azure Storage, zobacz [uwierzytelniania dostępu do obiektów blob i kolejek usługi Azure Active Directory i zarządzanych tożsamości dla zasobów platformy Azure](storage-auth-aad-msi.md).
