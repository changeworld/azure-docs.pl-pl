---
title: Uzyskiwanie tokenu z usługi Azure AD do autoryzowania żądań z aplikacji klienckiej
titleSuffix: Azure Storage
description: Usługa Azure Active Directory służy do uwierzytelniania z poziomu aplikacji klienckiej, uzyskiwania tokenu OAuth 2.0 i autoryzowania żądań do magazynu obiektów Blob platformy Azure i magazynu kolejek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268485"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Uzyskiwanie tokenu z usługi Azure AD do autoryzowania żądań z aplikacji klienckiej

Kluczową zaletą korzystania z usługi Azure Active Directory (Azure AD) z magazynu obiektów Blob platformy Azure lub magazynu kolejek jest to, że poświadczenia nie muszą być już przechowywane w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2.0 z platformy tożsamości firmy Microsoft (dawniej Usługi Azure AD). Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupy lub jednostki usługi) z uruchomieniem aplikacji. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD zwraca token dostępu do aplikacji, a aplikacja może następnie użyć tokenu dostępu do autoryzowania żądań do magazynu obiektów Blob platformy Azure lub magazynu kolejki.

W tym artykule pokazano, jak skonfigurować aplikację macierzystą lub aplikację sieci web do uwierzytelniania za pomocą platformy tożsamości firmy Microsoft 2.0. Przykładowy kod zawiera .NET, ale w innych językach stosuje się podobne podejście. Aby uzyskać więcej informacji na temat platformy tożsamości firmy Microsoft 2.0, zobacz [omówienie platformy tożsamości firmy Microsoft (w wersji 2.0).](../../active-directory/develop/v2-overview.md)

Aby zapoznać się z omówieniem przepływu grantów kodu OAuth 2.0, zobacz [Autoryzowanie dostępu do aplikacji sieci Web usługi Azure Active Directory przy użyciu przepływu dotacji kodu OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Przypisywanie roli do podmiotu zabezpieczeń usługi Azure AD

Aby uwierzytelnić podmiot zabezpieczeń z aplikacji usługi Azure Storage, należy najpierw skonfigurować ustawienia kontroli dostępu oparte na rolach (RBAC) dla tego podmiotu zabezpieczeń. Usługa Azure Storage definiuje wbudowane role RBAC, które obejmują uprawnienia do kontenerów i kolejek. Gdy rola RBAC jest przypisana do podmiotu zabezpieczeń, ten podmiot zabezpieczeń otrzymuje dostęp do tego zasobu. Aby uzyskać więcej informacji, zobacz [Zarządzanie prawami dostępu do danych obiektów Blob i kolejki platformy Azure za pomocą pliku RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD

Pierwszym krokiem przy użyciu usługi Azure AD do autoryzowania dostępu do zasobów magazynu jest rejestrowanie aplikacji klienckiej za pomocą dzierżawy usługi Azure AD z [witryny Azure portal.](https://portal.azure.com) Podczas rejestrowania aplikacji klienckiej, należy podać informacje o aplikacji do usługi Azure AD. Usługa Azure AD udostępnia następnie identyfikator klienta (nazywany również *identyfikatorem aplikacji),* którego używasz do skojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o identyfikatorze klienta, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować aplikację usługi Azure Storage, wykonaj czynności opisane w [przewodniku Szybki start: Zarejestruj aplikację na platformie tożsamości firmy Microsoft.](../../active-directory/develop/quickstart-configure-app-access-web-apis.md) Na poniższej ilustracji przedstawiono typowe ustawienia rejestrowania aplikacji sieci web:

![Zrzut ekranu przedstawiający sposób rejestrowania aplikacji magazynu w usłudze Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Jeśli aplikacja zostanie zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI **dla identyfikatora URI przekierowania**. W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, do którego są dostarczane tokeny.

Po zarejestrowaniu aplikacji w obszarze **Ustawienia**zostanie wyświetlony identyfikator aplikacji (lub identyfikator klienta):

![Zrzut ekranu przedstawiający identyfikator klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji za pomocą usługi Azure AD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Udzielanie uprawnień zarejestrowanej aplikacji do usługi Azure Storage

Następnie przyznaj uprawnienia aplikacji do wywoływania interfejsów API usługi Azure Storage. Ten krok umożliwia aplikacji autoryzowanie żądań do usługi Azure Storage za pomocą usługi Azure AD.

1. Na stronie **Przegląd** dla zarejestrowanej aplikacji wybierz pozycję **Wyświetl uprawnienia interfejsu API**.
1. W sekcji **Uprawnienia interfejsu API** wybierz pozycję Dodaj **uprawnienie** i wybierz pozycję **Interfejsy API firmy Microsoft**.
1. Wybierz **usługę Azure Storage** z listy wyników, aby wyświetlić okienko **uprawnień interfejsu API żądania.**
1. W obszarze **Jakiego typu uprawnień wymaga aplikacja?**, należy zauważyć, że dostępnym typem uprawnień są **uprawnienia delegowane**. Ta opcja jest domyślnie zaznaczona.
1. W sekcji **Wybierz uprawnienia** okienka **Żądania uprawnień interfejsu API** zaznacz pole wyboru obok **user_impersonation**, a następnie kliknij pozycję **Dodaj uprawnienia**.

    ![Zrzut ekranu przedstawiający uprawnienia do przechowywania](media/storage-auth-aad-app/registered-app-permissions-1.png)

Okienko **uprawnień interfejsu API** pokazuje teraz, że zarejestrowana aplikacja usługi Azure AD ma dostęp zarówno do programu Microsoft Graph, jak i usługi Azure Storage. Uprawnienia są przyznawane do programu Microsoft Graph automatycznie podczas pierwszego rejestrowania aplikacji w usłudze Azure AD.

![Zrzut ekranu przedstawiający uprawnienia do rejestracji aplikacji](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta

Aplikacja potrzebuje klucza tajnego klienta, aby udowodnić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. Wybierz ustawienie **Certyfikaty & wpisy tajne.**
1. W obszarze **Wpisy tajne klienta**kliknij pozycję **Nowy klucz tajny klienta,** aby utworzyć nowy klucz tajny.
1. Podaj opis klucza tajnego i wybierz żądany interwał wygaśnięcia.
1. Natychmiast skopiuj wartość nowego klucza tajnego do bezpiecznej lokalizacji. Pełna wartość jest wyświetlana tylko raz.

    ![Zrzut ekranu przedstawiający klucz tajny klienta](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Biblioteki klientów do pozyskiwania tokenów

Po zarejestrowaniu aplikacji i przyznaniu jej uprawnień dostępu do danych w magazynie obiektów Blob platformy Azure lub magazynie kolejki można dodać kod do aplikacji w celu uwierzytelnienia podmiotu zabezpieczeń i uzyskania tokenu OAuth 2.0. Aby uwierzytelnić i uzyskać token, można użyć jednej z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki open source obsługującej openid connect 1.0. Aplikacja może następnie użyć tokenu dostępu do autoryzowania żądania względem magazynu obiektów Blob platformy Azure lub magazynu kolejki.

Aby uzyskać listę scenariuszy, dla których tokeny pozyskiwania jest obsługiwany, zobacz sekcję [przepływów uwierzytelniania](/en-us/azure/active-directory/develop/msal-authentication-flows) [zawartości biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Dobrze znane wartości uwierzytelniania za pomocą usługi Azure AD

Aby uwierzytelnić podmiot zabezpieczeń za pomocą usługi Azure AD, należy uwzględnić niektóre dobrze znane wartości w kodzie.

### <a name="azure-ad-authority"></a>Urząd usługi Azure AD

W przypadku chmury publicznej firmy Microsoft podstawowy urząd usługi Azure AD jest następujący, gdzie *identyfikator dzierżawy* jest identyfikatorem dzierżawy usługi Active Directory (lub identyfikatorem katalogu):

`https://login.microsoftonline.com/<tenant-id>/`

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD do użycia do uwierzytelniania. Jest również określany jako identyfikator katalogu. Aby pobrać identyfikator dzierżawy, przejdź do strony **Przegląd** rejestracji aplikacji w witrynie Azure portal i skopiuj tę wartość.

### <a name="azure-storage-resource-id"></a>Identyfikator zasobu usługi Azure Storage

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu .NET: Tworzenie bloku blob

Przykład kodu pokazuje, jak uzyskać token dostępu z usługi Azure AD. Token dostępu służy do uwierzytelniania określonego użytkownika, a następnie autoryzowania żądania utworzenia bloku obiektu blob. Aby uzyskać ten przykład pracy, należy najpierw wykonać kroki opisane w poprzednich sekcjach.

Aby zażądać tokenu, musisz mieć następujące wartości z rejestracji aplikacji:

- Nazwa domeny usługi Azure AD. Pobierz tę wartość ze strony **Przegląd** usługi Azure Active Directory.
- Identyfikator dzierżawy (lub katalogu). Pobierz tę wartość ze strony **Przegląd** rejestracji aplikacji.
- Identyfikator klienta (lub aplikacji). Pobierz tę wartość ze strony **Przegląd** rejestracji aplikacji.
- Identyfikator URI przekierowania klienta. Pobierz tę wartość z ustawień **uwierzytelniania** dla rejestracji aplikacji.
- Wartość klucza tajnego klienta. Pobierz tę wartość z lokalizacji, do której została wcześniej skopiowana.

### <a name="create-a-storage-account-and-container"></a>Tworzenie konta magazynu i kontenera

Aby uruchomić przykładowy kod, utwórz konto magazynu w ramach tej samej subskrypcji co usługa Azure Active Directory. Następnie utwórz kontener w ramach tego konta magazynu. Przykładowy kod utworzy blokowy obiekt blob w tym kontenerze.

Następnie jawnie przypisać **rolę współautora danych obiektu blob magazynu** do konta użytkownika, w ramach którego zostanie uruchomiony przykładowy kod. Aby uzyskać instrukcje dotyczące przypisywania tej roli w witrynie Azure portal, zobacz [Udzielanie dostępu do obiektów blob platformy Azure i danych kolejek za pomocą funkcji RBAC w witrynie Azure portal.](storage-auth-aad-rbac-portal.md)

> [!NOTE]
> Podczas tworzenia konta usługi Azure Storage nie są automatycznie przypisywane uprawnienia dostępu do danych za pośrednictwem usługi Azure AD. Należy jawnie przypisać sobie rolę RBAC dla usługi Azure Storage. Można przypisać go na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontenera lub kolejki.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Tworzenie aplikacji sieci Web, która autoryzuje dostęp do magazynu obiektów Blob za pomocą usługi Azure AD

Gdy aplikacja uzyskuje dostęp do usługi Azure Storage, robi to w imieniu użytkownika, co oznacza, że zasoby obiektu blob lub kolejki są dostępne przy użyciu uprawnień użytkownika, który jest zalogowany. Aby wypróbować ten przykład kodu, potrzebujesz aplikacji sieci web, która monituje użytkownika o zalogowanie się przy użyciu tożsamości usługi Azure AD. Można utworzyć własną lub użyć przykładowej aplikacji dostarczonej przez firmę Microsoft.

Ukończona przykładowa aplikacja sieci web, która uzyskuje token i używa go do utworzenia obiektu blob w usłudze Azure Storage, jest dostępna w [usłudze GitHub.](https://aka.ms/aadstorage) Przeglądanie i uruchamianie ukończonego przykładu może być przydatne do zrozumienia przykładów kodu. Aby uzyskać instrukcje dotyczące uruchamiania ukończonego przykładu, zobacz sekcję ["Wyświetl i uruchom ukończoną próbkę".](#view-and-run-the-completed-sample)

#### <a name="add-references-and-using-statements"></a>Dodawanie odwołań i używanie instrukcji  

W programie Visual Studio zainstaluj bibliotekę klienta usługi Azure Storage. Z menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet**, a następnie pozycję **Konsola menedżera pakietów**. Wpisz następujące polecenia w oknie konsoli, aby zainstalować niezbędne pakiety z biblioteki klienta usługi Azure Storage dla platformy .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Następnie dodaj następujące instrukcje do HomeController.cs pliku:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>Tworzenie bloku blob

Dodaj następujący fragment kodu, aby utworzyć blokowy obiekt blob:

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
> Aby autoryzować operacje obiektów blob i kolejek za pomocą tokenu OAuth 2.0, należy użyć protokołu HTTPS.

W powyższym przykładzie biblioteka klienta .NET obsługuje autoryzację żądania utworzenia bloku obiektu blob. Biblioteki klienta usługi Azure Storage dla innych języków również obsługiwać autoryzację żądania dla Ciebie. Jednak jeśli wywołujesz operację usługi Azure Storage z tokenem OAuth przy użyciu interfejsu API REST, musisz autoryzować żądanie przy użyciu tokenu OAuth.

Aby wywołać operacje usługi obiektów Blob i queue przy użyciu tokenów dostępu OAuth, przekaż token dostępu w nagłówku **Autoryzacja** przy użyciu schematu **nadźwignia** i określ wersję usługi 2017-11-09 lub nowszą, jak pokazano w poniższym przykładzie:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Uzyskaj token OAuth z usługi Azure AD

Następnie dodaj metodę, która żąda tokenu z usługi Azure AD w imieniu użytkownika. Ta metoda definiuje zakres, dla którego mają być przyznane uprawnienia. Aby uzyskać więcej informacji na temat uprawnień i zakresów, zobacz [Uprawnienia i zgoda w punkcie końcowym platformy tożsamości firmy Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Użyj identyfikatora zasobu do konstruowania zakresu, dla którego można uzyskać token. W przykładzie konstruuje zakres przy użyciu identyfikatora `user_impersonation` zasobu wraz z wbudowanym zakresem, co wskazuje, że token jest żądany w imieniu użytkownika.

Należy pamiętać, że może być konieczne przedstawienie użytkownikowi interfejsu, który umożliwia użytkownikowi wyrażenie zgody na żądanie tokenu w ich imieniu. Gdy zgoda jest konieczna, przykład łapie **MsalUiRequiredException** i wywołuje inną metodę w celu ułatwienia żądania zgody:

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
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Zgoda jest procesem udzielania przez użytkownika autoryzacji aplikacji w celu uzyskania dostępu do chronionych zasobów w ich imieniu. Platforma tożsamości firmy Microsoft 2.0 obsługuje przyrostową zgodę, co oznacza, że podmiot zabezpieczeń może początkowo zażądać minimalnego zestawu uprawnień i dodać uprawnienia wraz z czasem w razie potrzeby. Gdy kod żąda tokenu dostępu, określ zakres uprawnień, które aplikacja `scope` potrzebuje w danym momencie przez parametr. Aby uzyskać więcej informacji na temat zgody przyrostowej, zobacz sekcję **"Przyrostowa i dynamiczna zgoda"** w [sekcji Dlaczego należy aktualizować platformę tożsamości firmy Microsoft (w wersji 2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

Następująca metoda konstruuje właściwości uwierzytelniania dla żądania zgody przyrostowej:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
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

## <a name="view-and-run-the-completed-sample"></a>Wyświetlanie i uruchamianie ukończonej próbki

Aby uruchomić przykładową aplikację, najpierw sklonuj lub pobierz ją z [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Następnie zaktualizuj aplikację zgodnie z opisem w poniższych sekcjach.

### <a name="provide-values-in-the-settings-file"></a>Podaj wartości w pliku ustawień

Następnie zaktualizuj plik *appsettings.json* o własne wartości w następujący sposób:

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

### <a name="update-the-storage-account-and-container-name"></a>Aktualizowanie konta magazynu i nazwy kontenera

W pliku *HomeController.cs* zaktualizuj identyfikator URI, który odwołuje się do obiektu blob bloku, aby użyć nazwy konta magazynu i kontenera:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Włączanie niejawnego przepływu dotacji

Aby uruchomić przykład, może być konieczne skonfigurowanie niejawnego przepływu dotacji dla rejestracji aplikacji. Wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. W sekcji Zarządzanie wybierz ustawienie **Uwierzytelnianie.**
1. W obszarze **Ustawienia zaawansowane**w sekcji **Niejawne przyznanie** wybierz pola wyboru, aby włączyć tokeny dostępu i identyfikatory, jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu przedstawiający sposób włączania ustawień niejawnego przepływu dotacji](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Aktualizowanie portu używanego przez localhost

Po uruchomieniu przykładu może się okazać, że należy zaktualizować identyfikator URI przekierowania określony w rejestracji aplikacji, aby użyć portu *localhost* przypisanego w czasie wykonywania. Aby zaktualizować identyfikator URI przekierowania w celu użycia przypisanego portu, wykonaj następujące kroki:

1. Przejdź do rejestracji aplikacji w witrynie Azure portal.
1. W sekcji Zarządzanie wybierz ustawienie **Uwierzytelnianie.**
1. W obszarze **Przekierowanie identyfikatorów URI**edytuj port zgodny z portem używanym przez przykładową aplikację, jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu przedstawiający przekierowanie identyfikatorów URI do rejestracji aplikacji](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o platformie tożsamości firmy Microsoft, zobacz [Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/).
- Aby dowiedzieć się więcej o rolach RBAC dla magazynu platformy Azure, zobacz [Zarządzanie prawami dostępu do danych magazynu za pomocą funkcji RBAC](storage-auth-aad-rbac.md).
- Aby dowiedzieć się więcej na temat używania tożsamości zarządzanych dla zasobów platformy Azure za pomocą usługi Azure Storage, zobacz [Uwierzytelnij dostęp do obiektów blob i kolejek za pomocą usługi Azure Active Directory i tożsamości zarządzanych dla zasobów platformy Azure.](storage-auth-aad-msi.md)
