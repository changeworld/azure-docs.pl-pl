---
title: Użyj usługi Azure Active Directory do uwierzytelniania rozwiązań usługi partia zadań Azure | Dokumentacja firmy Microsoft
description: Wsadowe obsługuje usługi Azure AD do uwierzytelniania z usługi partia zadań.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: danlep
ms.openlocfilehash: c28af5a9773cc362663831346b58f599aed6ea9a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Uwierzytelnianie partii rozwiązań usług w usłudze Active Directory

Partia zadań Azure obsługuje uwierzytelnianie za pomocą [usługi Azure Active Directory] [ aad_about] (Azure AD). Usługi Azure AD jest katalog wielu dzierżawców w chmurze firmy Microsoft i tożsamość usługi zarządzania. Azure sam używa usługi Azure AD do uwierzytelniania swoich klientów, Administratorzy usługi i użytkowników w organizacji.

Jeśli używasz uwierzytelniania usługi Azure AD z partii zadań Azure, można uwierzytelniać się w jednym z dwóch sposobów:

- Za pomocą **zintegrowane uwierzytelnianie** do uwierzytelniania użytkownika, do którego prowadzi interakcję z aplikacją. Aplikacji przy użyciu zintegrowanego uwierzytelniania zbiera poświadczeń użytkownika i używa tych poświadczeń do uwierzytelniania dostępu do zasobów w partii.
- Za pomocą **nazwy głównej usługi** do uwierzytelniania aplikacji instalacji nienadzorowanej. Nazwy głównej usługi definiuje zasady i uprawnienia dla aplikacji w celu przedstawienia aplikacji podczas uzyskiwania dostępu do zasobów w czasie wykonywania.

Aby dowiedzieć się więcej na temat usługi Azure AD, zobacz [Azure Active Directory dokumentacji](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Punkty końcowe na potrzeby uwierzytelniania

Na potrzeby uwierzytelniania aplikacji partii z usługą Azure AD, konieczne jest uwzględnienie niektórych dobrze znanych punktów końcowych w kodzie.

### <a name="azure-ad-endpoint"></a>Punktu końcowego platformy Azure AD

Podstawowy punkt końcowy urzędu usługi Azure AD jest:

`https://login.microsoftonline.com/`

Do uwierzytelniania za pomocą usługi Azure AD, możesz używać tego punktu końcowego wraz z identyfikator dzierżawcy (identyfikator katalogu). Identyfikator dzierżawy określa dzierżawy usługi Azure AD na potrzeby uwierzytelniania. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Punkt końcowy specyficznego dla dzierżawy jest wymagana podczas uwierzytelniania przy użyciu nazwy głównej usługi. 
> 
> Punkt końcowy specyficznego dla dzierżawy jest opcjonalna, podczas uwierzytelniania przy użyciu zintegrowanego uwierzytelniania, ale zalecana. Jednak umożliwia także wspólnego punktu końcowego usługi Azure AD. Typowe punktu końcowego dostarcza poświadczenia ogólnego zbierania interfejsu, gdy nie podano określonych dzierżawy. Typowe punkt końcowy jest `https://login.microsoftonline.com/common`.
>
>

Aby uzyskać więcej informacji na temat punktów końcowych usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Punkt końcowy zasobów usługi partia zadań

Użyj **punktu końcowego zasobów partii zadań Azure** można uzyskać tokenu do uwierzytelniania żądań do usługi partia zadań:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Zarejestrować aplikację z dzierżawą

Pierwszy krok w celu uwierzytelniania przy użyciu usługi Azure AD jest zarejestrowanie aplikację w dzierżawie usługi Azure AD. Rejestrowanie aplikacji umożliwia wywoływanie platformy Azure [biblioteki uwierzytelniania usługi Active Directory] [ aad_adal] (ADAL) w kodzie. ADAL udostępnia interfejs API w celu uwierzytelniania w usłudze Azure AD z aplikacji. Rejestrowanie aplikacji jest wymagana czy planujesz używać zintegrowanego uwierzytelniania lub nazwy głównej usługi.

Podczas rejestrowania aplikacji, podanych informacji o aplikacji do usługi Azure AD. Następnie usługi Azure AD zapewnia identyfikator aplikacji (nazywane również *identyfikator klienta*) umożliwia kojarzenie aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej na temat identyfikator aplikacji, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](../active-directory/develop/active-directory-application-objects.md).

Aby zarejestrować aplikację partii, postępuj zgodnie z instrukcjami [Dodawanie aplikacji](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) sekcji [Integrowanie aplikacji z usługą Azure Active Directory][aad_integrate]. Jeśli zarejestrujesz aplikacji jako aplikacji natywnej, można określić dowolny prawidłowy identyfikator URI dla **identyfikator URI przekierowania**. Nie musi być rzeczywistym punktu końcowego.

Po aplikacji został zarejestrowany, zostanie wyświetlony identyfikator aplikacji:

![Rejestrowanie aplikacji partii z usługą Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Aby uzyskać więcej informacji o rejestrowaniu aplikacji z usługą Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Uzyskanie Identyfikatora dzierżawy dla usługi Active Directory

Identyfikator dzierżawy określa dzierżawy usługi Azure AD, która zapewnia usługi uwierzytelniania do aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W portalu Azure wybierz usługi Active Directory.
2. Kliknij pozycję **Właściwości**.
3. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawcy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Uwierzytelnianie zintegrowane

Do uwierzytelniania za pomocą uwierzytelniania zintegrowanego, należy przyznać uprawnienia do nawiązania połączenia interfejsu API usługi partii aplikacji. Ten krok pozwala aplikacji do uwierzytelnienia wywołania interfejsu API usługi partii z usługą Azure AD.

Po wprowadzeniu [zarejestrowana aplikacja](#register-your-application-with-an-azure-ad-tenant), wykonaj następujące kroki w portalu Azure, aby przyznać jej dostęp do usługi partia zadań:

1. W okienku nawigacji po lewej stronie portalu Azure wybierz **wszystkie usługi**. Kliknij przycisk **rejestracji aplikacji**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę aplikacji](./media/batch-aad-auth/search-app-registration.png)

3. Kliknij aplikację, a następnie kliknij przycisk **ustawienia**. W **dostępu do interfejsu API** zaznacz **wymagane uprawnienia**.
4. W **wymagane uprawnienia** bloku, kliknij przycisk **Dodaj** przycisku.
5. W **wybierz interfejs API**, wyszukaj interfejsu API partii. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. W przypadku nowszych dzierżaw usługi Azure AD może być używana ta nazwa.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch. 
6. Po znalezieniu interfejsu API partii, zaznacz go i kliknij **wybierz**.
7. W **wybierz uprawnienia**, zaznacz pole wyboru obok pozycji **usługa partia zadań Azure dostępu** i kliknij przycisk **wybierz**.
8. Kliknij przycisk **Gotowe**.

**Wymagane uprawnienia** windows teraz pokazuje, czy aplikacja usługi Azure AD ma dostęp do biblioteki ADAL oraz partii usługi interfejsu API. Przyznano uprawnienia do biblioteki ADAL automatycznie podczas rejestrowania aplikacji z usługą Azure AD.

![Interfejs API Udziel uprawnień](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Użyj nazwy głównej usługi 

Aby uwierzytelnić aplikację, która działa w instalacji nienadzorowanej, należy użyć nazwy głównej usługi. Po po zarejestrowaniu aplikacji, wykonaj następujące kroki w portalu Azure, aby skonfigurować nazwy głównej usługi:

1. Żądanie klucz tajny aplikacji.
2. Przypisz rolę RBAC do aplikacji.

### <a name="request-a-secret-key-for-your-application"></a>Żądanie klucz tajny aplikacji

Podczas uwierzytelniania aplikacji z główną usługą wysyła zarówno identyfikator aplikacji, jak i klucz tajny do usługi Azure AD. Musisz utworzyć i skopiować klucza tajnego do używania w kodzie.

Wykonaj następujące kroki w portalu Azure:

1. W okienku nawigacji po lewej stronie portalu Azure wybierz **wszystkie usługi**. Kliknij przycisk **rejestracji aplikacji**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji.
3. Kliknij aplikację, a następnie kliknij przycisk **ustawienia**. W **dostępu do interfejsu API** zaznacz **klucze**.
4. Aby utworzyć klucz, wprowadź opis klucza. Następnie wybierz czas trwania dla klucza jeden lub dwa lata. 
5. Kliknij przycisk **zapisać** przycisk, aby utworzyć i wyświetlić klucz. Skopiować wartość klucza do bezpiecznej lokalizacji, nie będzie można uzyskać do niego dostęp ponownie po zakończeniu działania bloku. 

    ![Utwórz klucz tajny](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Przypisz rolę RBAC do aplikacji

Do uwierzytelniania za pomocą nazwy głównej usługi, należy przypisać rolę RBAC do aplikacji. Wykonaj następujące kroki:

1. W portalu Azure przejdź do konta usługi partia zadań używanych przez aplikację.
2. W **ustawienia** bloku dla konta usługi partia zadań, wybierz opcję **kontroli dostępu (IAM)**.
3. Kliknij przycisk **Dodaj**. 
4. Z **roli** listy rozwijanej, wybierz opcję _współautora_ lub _czytnika_ roli aplikacji. Aby uzyskać więcej informacji o tych ról, zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure](../role-based-access-control/overview.md).  
5. W **wybierz** wprowadź nazwę aplikacji. Wybierz aplikację z listy, a następnie kliknij przycisk **zapisać**.

Aplikacja powinien zostać wyświetlony w ustawieniach kontroli dostępu z rolą RBAC przypisane. 

![Przypisz rolę RBAC do aplikacji](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory

Identyfikator dzierżawy określa dzierżawy usługi Azure AD, która zapewnia usługi uwierzytelniania do aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W portalu Azure wybierz usługi Active Directory.
2. Kliknij pozycję **Właściwości**.
3. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawcy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Przykłady kodu

Przykłady kodu w tej sekcji przedstawiają sposób uwierzytelniania z usługi Azure AD przy użyciu zintegrowanego uwierzytelniania i nazwy głównej usługi. Większość tych przykładów kodu przy użyciu .NET, ale pojęcia są podobne dla innych języków.

> [!NOTE]
> Token uwierzytelniania usługi Azure AD wygasa po upływie godziny. Korzystając z długotrwałe **BatchClient** obiektu, zaleca się pobrania tokenu z biblioteki ADAL na każde żądanie, aby upewnić się, zawsze ma nieprawidłowy token. 
>
>
> Aby to osiągnąć w środowisku .NET, napisanie metody, która pobiera token z usługi Azure AD i przekaż tej metody do **BatchTokenCredentials** obiektu jako pełnomocnik. Na każde żądanie do usługi partia zadań, aby upewnić się, że podano prawidłowy token wywoływana jest metoda obiektu delegowanego. Domyślnie ADAL buforuje tokenów, aby nowy token są pobierane z usługi Azure AD tylko wtedy, gdy jest to konieczne. Aby uzyskać więcej informacji dotyczących tokenów w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Przykład kodu: używanie programu Azure AD zintegrowane uwierzytelnianie z partiami platformy .NET

Odwoływać się do uwierzytelniania za pomocą uwierzytelniania zintegrowanego z partiami platformy .NET, [Azure partiami platformy .NET](https://www.nuget.org/packages/Azure.Batch/) pakietu i [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pakietu.

Obejmują `using` instrukcje w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołanie do punktu końcowego usługi Azure AD w kodzie, w tym identyfikator dzierżawcy. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołanie do punktu końcowego zasobów usługi partii:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołanie konta partii zadań:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) dla aplikacji. Identyfikator aplikacji jest dostępna z rejestracji aplikacji w portalu Azure:

```csharp
private const string ClientId = "<application-id>";
```

Także skopiować przekierowania URI, który jest określony, jeśli aplikacja jest zarejestrowany jako aplikacji natywnej. Określone w kodzie identyfikator URI przekierowania musi być zgodna przekierowania URI podane podczas rejestrowania aplikacji:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napisanie metody wywołania zwrotnego można uzyskać tokenu uwierzytelniania z usługi Azure AD. **GetAuthenticationTokenAsync** metody wywołania zwrotnego pokazane wywołań biblioteki ADAL do uwierzytelniania użytkownika, który prowadzi interakcję z aplikacją. **AcquireTokenAsync** metoda ADAL monituje użytkownika o swoich poświadczeń i przychody aplikacji po użytkownika zapewni im (chyba że ma już pamięci podręcznej poświadczeń):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Utworzyć **BatchTokenCredentials** obiekt, który przyjmuje delegata jako parametr. Użyj tych poświadczeń do otwierania **BatchClient** obiektu. Możesz użyć tych **BatchClient** obiektu dla kolejnych operacji względem usługi partia zadań:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Przykład kodu: przy użyciu nazwy głównej usługi Azure AD z partiami platformy .NET

Odwoływać się do uwierzytelniania za pomocą nazwy głównej usługi z partiami platformy .NET, [Azure partiami platformy .NET](https://www.nuget.org/packages/Azure.Batch/) pakietu i [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pakietu.

Obejmują `using` instrukcje w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołanie do punktu końcowego usługi Azure AD w kodzie, w tym identyfikator dzierżawcy. Przy użyciu nazwy głównej usługi, należy podać punktu końcowego specyficznego dla dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołanie do punktu końcowego zasobów usługi partii:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołanie konta partii zadań:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) dla aplikacji. Identyfikator aplikacji jest dostępna z rejestracji aplikacji w portalu Azure:

```csharp
private const string ClientId = "<application-id>";
```

Określ klucz tajny, który został skopiowany z portalu Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Napisanie metody wywołania zwrotnego można uzyskać tokenu uwierzytelniania z usługi Azure AD. **GetAuthenticationTokenAsync** metody wywołania zwrotnego pokazane wywołań biblioteki ADAL do uwierzytelniania instalacji nienadzorowanej:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Utworzyć **BatchTokenCredentials** obiekt, który przyjmuje delegata jako parametr. Użyj tych poświadczeń do otwierania **BatchClient** obiektu. Użyj tego **BatchClient** obiektu dla kolejnych operacji względem usługi partia zadań:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Przykład kodu: przy użyciu nazwy głównej usługi Azure AD z partii Python

Do uwierzytelniania za pomocą nazwy głównej usługi w języku Python partii, zainstalowania i odwołania [partii zadań azure](https://pypi.org/project/azure-batch/) i [typowe azure](https://pypi.org/project/azure-common/) modułów.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Przy użyciu nazwy głównej usługi, należy podać identyfikator dzierżawcy. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Odwołanie do punktu końcowego zasobów usługi partii:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Odwołanie konta partii zadań:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) dla aplikacji. Identyfikator aplikacji jest dostępna z rejestracji aplikacji w portalu Azure:

```python
CLIENT_ID = "<application-id>";
```

Określ klucz tajny, który został skopiowany z portalu Azure:

```python
SECRET = "<secret-key>";
```

Utwórz **ServicePrincipalCredentials** obiektu:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Umożliwia otwieranie poświadczenia główne usługi **BatchServiceClient** obiektu. Użyj tego **BatchServiceClient** obiektu dla kolejnych operacji względem usługi partia zadań.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat usługi Azure AD, zobacz [Azure Active Directory dokumentacji](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady przedstawiająca sposób używania biblioteki ADAL są dostępne w [przykłady kodu Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteki.

* Aby dowiedzieć się więcej na temat nazwy główne usług, zobacz [aplikacji i usług obiektów principal w usłudze Azure Active Directory](../active-directory/develop/active-directory-application-objects.md). Aby utworzyć nazwy głównej usługi przy użyciu portalu Azure, zobacz [użycia portalu do tworzenia aplikacji i usług podmiot zabezpieczeń, który ma dostęp do zasobów usługi Active Directory](../resource-group-create-service-principal-portal.md). Można również utworzyć nazwy głównej usługi z programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

* Na potrzeby uwierzytelniania aplikacji partii zarządzania przy użyciu usługi Azure AD, zobacz [rozwiązań do zarządzania partii uwierzytelniania z usługi Active Directory](batch-aad-auth-management.md).

* Na przykład Python sposobu tworzenia klienta uwierzytelniony przy użyciu tokenu usługi Azure AD, zobacz [wdrażanie partii niestandardowy obraz Azure przy użyciu skryptu Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) próbki.

[aad_about]: ../active-directory/active-directory-whatis.md "Co to jest usługa Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[azure_portal]: http://portal.azure.com
