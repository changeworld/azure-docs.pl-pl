---
title: Uwierzytelniania za pomocą usługi Azure AD z aplikację Storage (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Uwierzytelnianie w usłudze Azure AD z aplikacji usługi Azure Storage (wersja zapoznawcza).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 1bf4a8bba3b93c16f67d46f65292709ef2a1bba2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660876"
---
# <a name="authenticate-with-azure-ad-from-an-azure-storage-application-preview"></a>Uwierzytelniania za pomocą usługi Azure AD z aplikacji usługi Azure Storage (wersja zapoznawcza)

Zaletą przy użyciu usługi Azure Active Directory (Azure AD) z usługą Azure Storage jest, że poświadczenia nie mają być przechowywane w kodzie. Zamiast tego możesz poprosić token dostępu protokołu OAuth 2.0 z usługi Azure AD. Obsługuje uwierzytelnianie podmiotu zabezpieczeń (użytkownika, grupy lub nazwy głównej usługi), usługi Azure AD działania aplikacji. Jeśli uwierzytelnianie się powiedzie, usługa Azure AD zwraca token dostępu do aplikacji i aplikacji można następnie użyć tokenu dostępu można autoryzować żądania do usługi Azure Storage.

W tym artykule przedstawiono sposób konfigurowania aplikacji do uwierzytelniania w usłudze Azure AD. .NET funkcje przykładowy kod, ale inne języki Użyj podejście podobne.

Przed podmiot zabezpieczeń może uwierzytelnić aplikację usługi Azure Storage, można skonfigurować ustawień kontroli dostępu opartej na rolach dla tego podmiotu zabezpieczeń. Usługa Azure Storage definiuje role RBAC, które obejmują uprawnienia do kontenerów i kolejek. Gdy rola RBAC jest przypisywana do podmiotu zabezpieczeń, że podmiot zabezpieczeń uzyskuje dostęp do tego zasobu. Aby uzyskać więcej informacji, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

Omówienie przepływu grant kodu OAuth 2.0, zobacz [Autoryzuj dostęp do aplikacji sieci web usługi Azure Active Directory przy użyciu kodu OAuth 2.0 przyznać przepływu](../../active-directory/develop/active-directory-protocols-oauth-code.md).

> [!IMPORTANT]
> Ta wersja zapoznawcza jest przeznaczony tylko do użytku z systemem innym niż produkcji. Produkcyjnej umowy poziomu usług (SLA) nie będą dostępne, dopóki integracji z usługą Azure AD dla usługi Azure Storage jest zadeklarowany jako ogólnie dostępna. Jeśli dla danego scenariusza integracji z usługą Azure AD nie jest jeszcze obsługiwana, nadal używać klucza wspólnego autoryzacji lub tokeny sygnatury dostępu Współdzielonego w aplikacji. Aby uzyskać dodatkowe informacje o wersji zapoznawczej, zobacz [uwierzytelniania dostępu do usługi Azure Storage za pomocą usługi Azure Active Directory (wersja zapoznawcza)](storage-auth-aad.md).
>
> W wersji zapoznawczej przypisań ról RBAC może potrwać maksymalnie pięć minut propagacji.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Zarejestrować aplikację w dzierżawie usługi Azure AD

Pierwszym krokiem przy użyciu usługi Azure AD, do autoryzacji dostępu do zasobów magazynu jest zarejestrowanie aplikacji klienckiej w dzierżawie usługi Azure AD. Rejestrowanie aplikacji umożliwia wywoływanie platformy Azure [biblioteki uwierzytelniania usługi Active Directory](../../active-directory/active-directory-authentication-libraries.md) (ADAL) w kodzie. ADAL udostępnia interfejs API w celu uwierzytelniania w usłudze Azure AD z aplikacji. Rejestrowanie aplikacji umożliwia także autoryzowania połączeń z tej aplikacji do interfejsów API magazynu Azure z tokenem dostępu.

Podczas rejestrowania aplikacji, podanych informacji o aplikacji do usługi Azure AD. Następnie usługi Azure AD zapewnia Identyfikatora klienta (nazywanych również *identyfikator aplikacji*) umożliwia kojarzenie aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej na temat tego Identyfikatora klienta, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md).

Aby zarejestrować aplikację usługi Azure Storage, postępuj zgodnie z instrukcjami [Dodawanie aplikacji](../../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) sekcji [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). Jeśli zarejestrujesz aplikacji jako aplikacji natywnej, można określić dowolny prawidłowy identyfikator URI dla **identyfikator URI przekierowania**. Wartości muszą być prawdziwe punktu końcowego.

![Zrzut ekranu pokazujący sposób zarejestrować aplikację magazynu z usługą Azure AD](./media/storage-auth-aad-app/app-registration.png)

Po aplikacji został zarejestrowany, zostanie wyświetlony identyfikator aplikacji (lub identyfikator klienta) w obszarze **ustawienia**:

![Zrzut ekranu: identyfikator klienta](./media/storage-auth-aad-app/app-registration-client-id.png)

Aby uzyskać więcej informacji o rejestrowaniu aplikacji z usługą Azure AD, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Udziel uprawnienia zarejestrowanych aplikacji do magazynu Azure

Następnie należy przyznać uprawnienia aplikacji do wywoływania interfejsów API magazynu Azure. Ten krok pozwala aplikacji na autoryzowanie wywołania do magazynu Azure z usługą Azure AD.

1. W okienku nawigacji po lewej stronie portalu Azure wybierz **wszystkie usługi**i wyszukaj **rejestracji aplikacji**.
2. Wyszukaj nazwę zarejestrowanej aplikacji utworzonej w poprzednim kroku.
3. Wybierz zarejestrowane aplikacji i kliknij przycisk **ustawienia**. W **dostępu do interfejsu API** zaznacz **wymagane uprawnienia**.
4. W **wymagane uprawnienia** bloku, kliknij przycisk **Dodaj** przycisku.
5. W obszarze **wybierz interfejs API**, wyszukaj "Magazyn Azure" i wybierz **usługi Azure Storage** z listy wyników.

    ![Zrzut ekranu przedstawiający uprawnienia do magazynu](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. W obszarze **wybierz uprawnienia**, zaznacz pole wyboru obok pozycji **dostęp do magazynu Azure**i kliknij przycisk **wybierz**.
7. Kliknij przycisk **Gotowe**.

**Wymagane uprawnienia** windows teraz pokazuje, czy aplikacja usługi Azure AD ma dostęp do usługi Azure Active Directory i usługi Azure Storage. Przyznano uprawnienia do usługi Azure AD automatycznie po należy najpierw zarejestrować aplikację w usłudze Azure AD.

![Zrzut ekranu przedstawiający zarejestrować uprawnienia aplikacji](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Przykład kodu .NET: tworzenie blokowych obiektów blob

Przykład kodu pokazuje, jak można uzyskać dostępu do tokenu z usługi Azure AD. Token dostępu jest używany do uwierzytelniania określonego użytkownika, a następnie Autoryzuj żądanie utworzenia blokowych obiektów blob. Aby rozpocząć pracę tej próbki, najpierw wykonaj czynności opisane w poprzednich sekcjach.

> [!NOTE]
> Jako właściciel konta magazynu Azure użytkownik nie jest automatycznie przypisywana uprawnienia dostępu do danych. Należy jawnie przypisać samodzielnie roli RBAC dla usługi Azure Storage. Należy przypisać mu na poziomie subskrypcji, grupy zasobów, konta magazynu lub kontener lub kolejki. 
>
> Na przykład uruchamiać kod przykładowy, na którym jesteś właścicielem konta magazynu, a w obszarze identyfikator użytkownika, należy przypisać rolę RBAC dla obiekt Blob danych współautora do siebie. W przeciwnym razie wywołanie w celu utworzenia obiektu blob zakończy się niepowodzeniem z kodem stanu HTTP 403 (Dostęp zabroniony). Aby uzyskać więcej informacji, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Znane wartości dla uwierzytelniania za pomocą usługi Azure AD

Na potrzeby uwierzytelniania podmiotu zabezpieczeń z usługą Azure AD, należy uwzględnić niektóre dobrze znane wartości w kodzie.

#### <a name="azure-ad-oauth-endpoint"></a>Azure AD OAuth punktu końcowego

Podstawowym usługi Azure AD urzędu punkt końcowy protokołu OAuth 2.0 jest następująca, gdzie *identyfikator dzierżawcy* Identyfikatora dzierżawy usługi Active Directory (lub identyfikator katalogu):

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

Identyfikator dzierżawy określa dzierżawy usługi Azure AD na potrzeby uwierzytelniania. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie **uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory**.

#### <a name="storage-resource-id"></a>Identyfikator zasobu magazynu

Identyfikator zasobu usługi Magazyn Azure można używać w celu pobrania tokenu do uwierzytelniania żądań do usługi Azure Storage:

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory

Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W portalu Azure wybierz usługi Active Directory.
2. Kliknij pozycję **Właściwości**.
3. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawcy.

![Zrzut ekranu przedstawiający sposób kopiowania Identyfikatora dzierżawcy](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Dodaj odwołania i instrukcje using  

W programie Visual Studio Zainstaluj wersję zapoznawczą biblioteki klienta magazynu Azure. Z **narzędzia** menu, wybierz opcję **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**. W konsoli, wpisz następujące polecenie:

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Następnie dodaj następujące instrukcje using do kodu:

```dotnet
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Pobierz OAuth token z usługi Azure AD

Następnie dodaj metodę żądania tokenu z usługi Azure AD. Aby poprosić o tokenie, wywołaj [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync) metody.

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/"; // Storage resource endpoint
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token"; // Azure AD OAuth endpoint
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Utwórz blokowych obiektów blob

Na koniec Użyj tokenu dostępu, aby utworzyć nowe poświadczenia magazynu, a te poświadczenia umożliwiają utworzenie obiektu blob:

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Integracja usługi Azure AD z usługą Azure Storage wymaga używania protokołu HTTPS dla operacji usługi Azure Storage.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o rolach RBAC dla usługi Azure storage, zobacz [Zarządzaj praw dostępu do magazynu danych z RBAC (wersja zapoznawcza)](storage-auth-aad-rbac.md).
- Aby zapoznać się z usługą Azure Storage za pomocą zarządzanych tożsamość usługi, zobacz [uwierzytelniony przez usługi Azure AD z tożsamości usługi zarządzane Azure (wersja zapoznawcza)](storage-auth-aad-msi.md).
- Aby dowiedzieć się, jak zalogować się do wiersza polecenia platformy Azure i programu PowerShell przy użyciu tożsamości usługi Azure AD, zobacz [korzystania z tożsamości usługi Azure AD dostęp do magazynu Azure z interfejsu wiersza polecenia lub środowiska PowerShell (wersja zapoznawcza)](storage-auth-aad-script.md).
- Aby uzyskać dodatkowe informacje na temat integracji z usługą Azure AD dla kolejek i obiektów blob Azure, zobacz w usłudze Azure Storage team na blogu, [Announcing uwierzytelniania Podgląd Azure AD dla usługi Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).



