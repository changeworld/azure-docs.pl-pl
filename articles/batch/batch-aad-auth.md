---
title: Usługa Azure Active Directory do uwierzytelniania usługi Azure Batch service solutions | Dokumentacja firmy Microsoft
description: Usługa Batch obsługuje usługi Azure AD do uwierzytelniania z poziomu usługi Batch.
services: batch
documentationcenter: .net
author: laurenhughes
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
ms.author: lahugh
ms.openlocfilehash: 0ca22cfe99e77cd2ed3c5a966fb2412444103d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922440"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Uwierzytelnianie rozwiązań usługi Batch za pomocą usługi Active Directory

Usługa Azure Batch obsługuje uwierzytelnianie przy użyciu [usługi Azure Active Directory] [ aad_about] (Azure AD). Usługa Azure AD jest katalog oparte na chmurze z wieloma dzierżawami firmy Microsoft i Usługa zarządzania tożsamościami. Azure sam używa usługi Azure AD do uwierzytelniania swoich klientów, Administratorzy usługi i użytkowników w organizacji.

Korzystając z uwierzytelniania usługi Azure AD z usługą Azure Batch, można uwierzytelniać się w jeden z dwóch sposobów:

- Za pomocą **zintegrowane uwierzytelnianie** do uwierzytelniania użytkownika, który prowadzi interakcję z aplikacją. Aplikacji przy użyciu uwierzytelniania zintegrowanego zbierane poświadczeń użytkownika i używa tych poświadczeń do uwierzytelniania dostępu do zasobów usługi Batch.
- Za pomocą **nazwy głównej usługi** do uwierzytelniania nienadzorowanej aplikacji. Jednostki usługi definiuje zasady i uprawnienia dla aplikacji w celu reprezentowania aplikacji podczas uzyskiwania dostępu do zasobów w czasie wykonywania.

Aby dowiedzieć się więcej na temat usługi Azure AD, zobacz [usługi Azure Active Directory dokumentacji](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Punkty końcowe uwierzytelniania

Do uwierzytelniania aplikacji usługi Batch za pomocą usługi Azure AD, konieczne jest uwzględnienie niektóre znane punktów końcowych w kodzie.

### <a name="azure-ad-endpoint"></a>Punkt końcowy usługi Azure AD

Podstawowy punkt końcowy urzędu usługi Azure AD jest:

`https://login.microsoftonline.com/`

Aby uwierzytelnić się za pomocą usługi Azure AD, należy użyć ten punkt końcowy wraz z Identyfikatorem dzierżawy (identyfikator katalogu). Identyfikator dzierżawy określa dzierżawy usługi Azure AD do uwierzytelniania. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Podczas uwierzytelniania za pomocą nazwy głównej usługi, wymagane będzie punktem końcowym specyficznym dla dzierżawy. 
> 
> Punktem końcowym specyficznym dla dzierżawy jest opcjonalne, podczas uwierzytelniania przy użyciu uwierzytelniania zintegrowanego, ale zalecane. Jednak można również użyć wspólnego punktu końcowego usługi Azure AD. Endpoint wspólnego dostarcza poświadczenia rodzajowe zbierania interfejsu, gdy określonej dzierżawy nie została podana. Typowe punkt końcowy jest `https://login.microsoftonline.com/common`.
>
>

Aby uzyskać więcej informacji na temat punktów końcowych usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Punkt końcowy zasobu usługi Batch

Użyj **punkt końcowy zasobu usługi Azure Batch** do uzyskania tokenu na potrzeby uwierzytelniania żądań do usługi Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Zarejestruj swoją aplikację z dzierżawą

Pierwszym krokiem przy użyciu usługi Azure AD do uwierzytelniania jest zarejestrowanie aplikacji w dzierżawie usługi Azure AD. Rejestrowanie aplikacji umożliwia wywoływanie Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) w kodzie. Biblioteki ADAL zapewnia interfejs API do uwierzytelniania za pomocą usługi Azure AD z poziomu aplikacji. Rejestrowanie aplikacji jest wymagana czy planujesz używać zintegrowanego uwierzytelniania lub ta jednostka usługi.

Możesz zarejestrować swoją aplikację, należy podać informacje o aplikacji do usługi Azure AD. Następnie usługa Azure AD zawiera identyfikator aplikacji (nazywane również *identyfikator klienta*) umożliwia kojarzenie aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o identyfikatorze aplikacji, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować aplikacji usługi Batch, wykonaj kroki opisane w [dodawania aplikacji](../active-directory/develop/quickstart-register-app.md) sekcji [Integrowanie aplikacji z usługą Azure Active Directory][aad_integrate]. Jeśli zarejestrujesz aplikację jako natywną aplikację, można określić dowolny prawidłowy identyfikator URI dla **identyfikator URI przekierowania**. Nie musi odpowiadać rzeczywistemu punktowi końcowemu.

Po zarejestrowaniu aplikacji zostanie wyświetlony identyfikator aplikacji:

![Rejestrowanie aplikacji usługi Batch w usłudze Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Pobieranie Identyfikatora dzierżawy dla usługi Active Directory

Identyfikator dzierżawy określa dzierżawy usługi Azure AD, które świadczy usługi uwierzytelniania do aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz usługi Active Directory.
2. Kliknij pozycję **Właściwości**.
3. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Korzystania ze zintegrowanego uwierzytelniania

Aby uwierzytelniać się przy użyciu uwierzytelniania zintegrowanego, musisz udzielić uprawnień aplikacji do łączenie z interfejsem API usługi Batch. Ten krok powoduje włączenie aplikacji do uwierzytelniania wywołań interfejsu API usługi Batch za pomocą usługi Azure AD.

Po zarejestrowaniu aplikacji, wykonaj następujące kroki w witrynie Azure portal, aby przyznać jej dostęp do usługi Batch:

1. W okienku nawigacji po lewej stronie w witrynie Azure portal wybierz **wszystkich usług**. Kliknij przycisk **rejestracje aplikacji**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę swojej aplikacji](./media/batch-aad-auth/search-app-registration.png)

3. Kliknij aplikację, a następnie kliknij przycisk **ustawienia**. W **dostęp do interfejsu API** zaznacz **wymagane uprawnienia**.
4. W **wymagane uprawnienia** bloku kliknij **Dodaj** przycisku.
5. W **wybierz interfejs API**, wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. W przypadku nowszych dzierżaw usługi Azure AD może być używana ta nazwa.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch. 
6. Po odnalezieniu interfejsu API usługi Batch, zaznacz go i kliknij **wybierz**.
7. W **wybierz uprawnienia**, zaznacz pole wyboru obok pozycji **dostępu do usługi Azure Batch Service** i kliknij przycisk **wybierz**.
8. Kliknij przycisk **Gotowe**.

**Wymagane uprawnienia** windows teraz pokazuje, że aplikacja usługi Azure AD ma dostęp do biblioteki ADAL i partii usługi interfejsu API. Uprawnienia są przyznawane ADAL automatycznie podczas rejestrowania aplikacji z usługą Azure AD.

![Interfejs API Udziel uprawnień](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Użyj nazwy głównej usługi 

Aby uwierzytelnić aplikację, która działa w instalacji nienadzorowanej, należy użyć nazwy głównej usługi. Po zarejestrowaniu aplikacji, wykonaj następujące kroki w witrynie Azure portal, aby skonfigurować nazwę główną usługi:

1. Żądanie klucz tajny aplikacji.
2. Przypisz rolę RBAC do aplikacji.

### <a name="request-a-secret-key-for-your-application"></a>Żądanie klucz tajny aplikacji

Gdy aplikacja uwierzytelnia się za pomocą jednostki usługi, wysyła identyfikator aplikacji i klucza tajnego do usługi Azure AD. Należy utworzyć i skopiować klucza tajnego w kodzie.

Wykonaj następujące kroki w witrynie Azure portal:

1. W okienku nawigacji po lewej stronie w witrynie Azure portal wybierz **wszystkich usług**. Kliknij przycisk **rejestracje aplikacji**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji.
3. Kliknij aplikację, a następnie kliknij przycisk **ustawienia**. W **dostęp do interfejsu API** zaznacz **klucze**.
4. Aby utworzyć klucz, wprowadź opis klucza. Następnie wybierz czas trwania dla klucza jeden lub dwa lata. 
5. Kliknij przycisk **Zapisz** przycisk, aby utworzyć i wyświetlić klucz. Skopiuj wartość klucza w bezpiecznym miejscu, ponieważ nie będzie można uzyskać do niego dostęp ponownie po opuszczeniu bloku. 

    ![Utwórz klucz tajny](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Przypisz rolę RBAC do aplikacji

Aby uwierzytelniać się przy użyciu jednostki usługi, musisz Przypisz rolę RBAC do aplikacji. Wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do konta usługi Batch używanych przez aplikację.
2. W **ustawienia** bloku konta usługi Batch wybierz **kontrola dostępu (IAM)** .
3. Kliknij przycisk **przypisań ról** kartę.
4. Kliknij przycisk **Dodaj przypisanie roli** przycisku. 
5. Z **roli** listę rozwijaną, wybierz opcję _Współautor_ lub _czytnika_ roli aplikacji. Aby uzyskać więcej informacji na temat tych ról, zobacz [wprowadzenie opartej na rolach kontrola dostępu w witrynie Azure portal](../role-based-access-control/overview.md).  
6. W **wybierz** wprowadź nazwę aplikacji. Wybierz swoją aplikację z listy, a następnie kliknij przycisk **Zapisz**.

Aplikacja powinien zostać wyświetlony na ustawienia kontroli dostępu z przypisaną rolę RBAC. 

![Przypisz rolę RBAC do aplikacji](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Pobieranie Identyfikatora dzierżawy usługi Azure Active Directory

Identyfikator dzierżawy określa dzierżawy usługi Azure AD, które świadczy usługi uwierzytelniania do aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz usługi Active Directory.
2. Kliknij pozycję **Właściwości**.
3. Skopiuj wartość identyfikatora GUID **identyfikator katalogu**. Ta wartość jest również określany jako identyfikator dzierżawy.

![Skopiuj identyfikator katalogu](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Przykłady kodu

Przykłady kodu w tej sekcji przedstawiają sposób uwierzytelniania przy użyciu usługi Azure AD przy użyciu zintegrowanego uwierzytelniania i przy użyciu jednostki usługi. Większość z tych przykładów kodu przy użyciu programu .NET, ale podstawowe koncepcje są podobne dla innych języków.

> [!NOTE]
> Token uwierzytelniania usługi Azure AD wygasa po upływie godziny. Korzystając z długotrwałe **BatchClient** obiektu, zaleca się pobrać token z biblioteki ADAL na każde żądanie, aby upewnić się, zawsze mieć prawidłowy token. 
>
>
> Aby to osiągnąć na platformie .NET, napisanie metody, która pobiera token z usługi Azure AD i przekazać tej metody do **BatchTokenCredentials** obiektu jako pełnomocnik. Na każde żądanie do usługi Batch, aby upewnić się, że podano prawidłowy token, wywoływana jest metoda delegata. Domyślnie biblioteki ADAL buforuje tokenów, aby uzyskać nowy token jest pobierany z usługi Azure AD tylko wtedy, gdy jest to konieczne. Aby uzyskać więcej informacji dotyczących tokenów w usłudze Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Przykład kodu: Używanie programu Azure AD zintegrowane uwierzytelnianie przy użyciu platformy .NET usługi Batch

Aby uwierzytelniać się przy użyciu uwierzytelniania zintegrowanego z platformy .NET usługi Batch, należy odwołać [usługi Azure Batch dla środowiska .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) pakietu i [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pakietu.

Obejmują następujące elementy `using` instrukcji w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwoływać się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikator dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołanie do punktu końcowego zasobu usługi Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołać się do konta usługi Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) na potrzeby aplikacji. Identyfikator aplikacji jest dostępna z rejestracji aplikacji w witrynie Azure portal:

```csharp
private const string ClientId = "<application-id>";
```

Także skopiować identyfikator URI, który określiłeś, przekierowania, jeśli aplikacja została zarejestrowana jako natywną aplikację. Przekierowania URI określona w kodzie musi odpowiadać przekierowania URI, podane podczas rejestrowania aplikacji:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napisanie metody wywołania zwrotnego, można uzyskać tokenu uwierzytelniania z usługi Azure AD. **GetAuthenticationTokenAsync** metody wywołania zwrotnego pokazane tutaj wywołań biblioteki ADAL do uwierzytelniania użytkownika, który prowadzi interakcję z aplikacją. **AcquireTokenAsync** metody dostarczone przez bibliotekę ADAL monituje użytkownika o swoich poświadczeń i przychody aplikacji po użytkownik udostępnia je (o ile nie ma już zostały zbuforowane poświadczenia):

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

Konstruowania **BatchTokenCredentials** obiekt, który przyjmuje delegata jako parametr. Użyj tych poświadczeń, aby otworzyć **BatchClient** obiektu. Można użyć **BatchClient** obiekt kolejne operacje względem usługi Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Przykład kodu: Za pomocą jednostki usługi Azure AD przy użyciu platformy .NET usługi Batch

Aby uwierzytelniać się przy użyciu jednostki usługi, z platformy .NET usługi Batch, należy odwołać [usługi Azure Batch dla środowiska .NET](https://www.nuget.org/packages/Azure.Batch/) pakietu i [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pakietu.

Obejmują następujące elementy `using` instrukcji w kodzie:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwoływać się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikator dzierżawy. Korzystając z nazwy głównej usługi, musisz podać punktem końcowym specyficznym dla dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołanie do punktu końcowego zasobu usługi Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołać się do konta usługi Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) na potrzeby aplikacji. Identyfikator aplikacji jest dostępna z rejestracji aplikacji w witrynie Azure portal:

```csharp
private const string ClientId = "<application-id>";
```

Określ klucz tajny, który został skopiowany z witryny Azure portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Napisanie metody wywołania zwrotnego, można uzyskać tokenu uwierzytelniania z usługi Azure AD. **GetAuthenticationTokenAsync** metody wywołania zwrotnego pokazane tutaj wywołań biblioteki ADAL do uwierzytelniania instalacji nienadzorowanej:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Konstruowania **BatchTokenCredentials** obiekt, który przyjmuje delegata jako parametr. Użyj tych poświadczeń, aby otworzyć **BatchClient** obiektu. Użyj tego **BatchClient** obiekt kolejne operacje względem usługi Batch:

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
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Przykład kodu: Za pomocą jednostki usługi Azure AD za pomocą języka Python usługi Batch

Aby uwierzytelniać się przy użyciu jednostki usługi, za pomocą języka Python usługi Batch, zainstalowania i odwołania [usługi azure batch](https://pypi.org/project/azure-batch/) i [azure common](https://pypi.org/project/azure-common/) modułów.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Korzystając z nazwy głównej usługi, należy podać identyfikator dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj czynności opisane w temacie [uzyskanie Identyfikatora dzierżawy usługi Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Odwołanie do punktu końcowego zasobu usługi Batch:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Odwołać się do konta usługi Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) na potrzeby aplikacji. Identyfikator aplikacji jest dostępna z rejestracji aplikacji w witrynie Azure portal:

```python
CLIENT_ID = "<application-id>";
```

Określ klucz tajny, który został skopiowany z witryny Azure portal:

```python
SECRET = "<secret-key>";
```

Tworzenie **ServicePrincipalCredentials** obiektu:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Użyj poświadczeń jednostki usługi, aby otworzyć **BatchServiceClient** obiektu. Użyj tego **BatchServiceClient** obiekt kolejne operacje względem usługi Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat usługi Azure AD, zobacz [usługi Azure Active Directory dokumentacji](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady, przedstawiający sposób użycia biblioteki ADAL, są dostępne w [przykłady kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteki.

* Aby dowiedzieć się więcej na temat nazw głównych usług, zobacz [aplikacji i obiektów nazw głównych usług w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Aby utworzyć nazwę główną usługi za pomocą witryny Azure portal, zobacz [w obsłudze portalu do tworzenia aplikacji i usługi jednostki, które mogą uzyskiwać dostęp do zasobów usługi Active Directory](../active-directory/develop/howto-create-service-principal-portal.md). Można również utworzyć nazwę główną usługi za pomocą programu PowerShell lub wiersza polecenia platformy Azure.

* Do uwierzytelniania aplikacji Batch Management przy użyciu usługi Azure AD, zobacz [rozwiązania uwierzytelniania Batch Management z usługą Active Directory](batch-aad-auth-management.md).

* Aby uzyskać instrukcje tworzenia klienta usługi Batch, uwierzytelniony przy użyciu tokenu usługi Azure AD, na przykład języka Python, zobacz [wdrażania usługi Azure Batch obraz niestandardowy przy użyciu skryptu języka Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) próbki.

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[azure_portal]: https://portal.azure.com
