---
title: Uwierzytelnij usługi Azure Batch za pomocą usługi Azure Active Directory
description: Usługa Batch obsługuje usługę Azure AD w celu uwierzytelnienia z usługi Batch. Dowiedz się, jak uwierzytelniać się na dwa sposoby.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 01/28/2020
ms.author: labrenne
ms.openlocfilehash: f56c05f64086ac2e98e69d6b21fae7a0a63b5006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019523"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Uwierzytelnij rozwiązania usługi wsadowej za pomocą usługi Active Directory

Usługa Azure Batch obsługuje uwierzytelnianie za pomocą [usługi Azure Active Directory][aad_about] (Azure AD). Usługa Azure AD to usługa zarządzania katalogami i tożsamościami opartymi na chmurze z wieloma dzierżawcami firmy Microsoft. Sama platforma Azure używa usługi Azure AD do uwierzytelniania swoich klientów, administratorów usług i użytkowników organizacji.

Korzystając z uwierzytelniania usługi Azure AD przy użyciu usługi Azure Batch, można uwierzytelnić na jeden z dwóch sposobów:

- Za pomocą **zintegrowanego uwierzytelniania** do uwierzytelniania użytkownika, który wchodzi w interakcję z aplikacją. Aplikacja używająca zintegrowanego uwierzytelniania zbiera poświadczenia użytkownika i używa tych poświadczeń do uwierzytelniania dostępu do zasobów usługi Batch.
- Za pomocą **jednostki usługi** do uwierzytelniania aplikacji nienadzorowanych. Podmiot usługi definiuje zasady i uprawnienia dla aplikacji w celu reprezentowania aplikacji podczas uzyskiwania dostępu do zasobów w czasie wykonywania.

Aby dowiedzieć się więcej o usłudze Azure AD, zobacz [Dokumentację usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Punkty końcowe uwierzytelniania

Aby uwierzytelnić aplikacje usługi Batch za pomocą usługi Azure AD, należy dołączyć kilka dobrze znanych punktów końcowych w kodzie.

### <a name="azure-ad-endpoint"></a>Punkt końcowy usługi Azure AD

Podstawowym punktem końcowym urzędu usługi Azure AD jest:

`https://login.microsoftonline.com/`

Aby uwierzytelnić się za pomocą usługi Azure AD, należy użyć tego punktu końcowego wraz z identyfikatorem dzierżawy (identyfikator katalogu). Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD do użycia do uwierzytelniania. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w [pobierz identyfikator dzierżawy dla usługi Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> Punkt końcowy specyficzne dla dzierżawy jest wymagane podczas uwierzytelniania przy użyciu jednostki usługi. 
> 
> Punkt końcowy specyficzny dla dzierżawy jest opcjonalny podczas uwierzytelniania przy użyciu zintegrowanego uwierzytelniania, ale zalecane. Można jednak również użyć wspólnego punktu końcowego usługi Azure AD. Wspólny punkt końcowy zapewnia ogólny interfejs gromadzenia poświadczeń, gdy określona dzierżawca nie jest podana. Wspólnym punktem `https://login.microsoftonline.com/common`końcowym jest .
>
>

Aby uzyskać więcej informacji na temat punktów końcowych usługi Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Punkt końcowy zasobu wsadowego

Punkt **końcowy zasobu usługi Azure Batch** służy do uzyskiwania tokenu do uwierzytelniania żądań do usługi Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Zarejestruj aplikację w dzierżawie

Pierwszym krokiem przy użyciu usługi Azure AD do uwierzytelniania jest rejestrowanie aplikacji w dzierżawie usługi Azure AD. Rejestrowanie aplikacji umożliwia wywołanie [biblioteki uwierzytelniania usługi][aad_adal] Azure Active Directory (ADAL) z kodu. Usługa ADAL udostępnia interfejs API do uwierzytelniania za pomocą usługi Azure AD z aplikacji. Rejestrowanie aplikacji jest wymagane, niezależnie od tego, czy planujesz używać zintegrowanego uwierzytelniania, czy jednostki usługi.

Podczas rejestrowania aplikacji, należy podać informacje o aplikacji do usługi Azure AD. Usługa Azure AD udostępnia następnie identyfikator aplikacji (nazywany również *identyfikatorem klienta),* którego używasz do skojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o identyfikatorze aplikacji, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować aplikację batch, wykonaj kroki opisane w sekcji [Dodawanie aplikacji](../active-directory/develop/quickstart-register-app.md) w obszarze [Integrowanie aplikacji z usługą Azure Active Directory][aad_integrate]. Jeśli aplikacja zostanie zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI **dla identyfikatora URI przekierowania**. Nie musi być prawdziwym punktem końcowym.

Po zarejestrowaniu aplikacji zostanie wyświetlony identyfikator aplikacji:

![Rejestrowanie aplikacji wsadowego za pomocą usługi Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji za pomocą usługi Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Pobierz identyfikator dzierżawy usługi Active Directory

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD, która zapewnia usługi uwierzytelniania aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz usługę Active Directory.
1. Wybierz **pozycję Właściwości**.
1. Skopiuj wartość guid pod warunkiem **identyfikatora katalogu**. Ta wartość jest również nazywany identyfikatorem dzierżawy.

![Kopiowanie identyfikatora katalogu](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Korzystanie ze zintegrowanego uwierzytelniania

Aby uwierzytelnić się za pomocą zintegrowanego uwierzytelniania, należy udzielić uprawnień aplikacji, aby połączyć się z interfejsem API usługi Batch. Ten krok umożliwia aplikacji uwierzytelnienie wywołań do interfejsu API usługi wsadowej za pomocą usługi Azure AD.

Po zarejestrowaniu aplikacji wykonaj następujące kroki w witrynie Azure portal, aby udzielić jej dostępu do usługi Batch:

1. W lewym okienku nawigacji w witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Wybierz **pozycję Rejestracje aplikacji**.
1. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę aplikacji](./media/batch-aad-auth/search-app-registration.png)

1. Wybierz aplikację i wybierz **uprawnienia interfejsu API**.
1. W sekcji **Uprawnienia interfejsu API** wybierz pozycję Dodaj **uprawnienie**.
1. W **obszarze Wybierz interfejs API**wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **Partia platformy Microsoft Azure**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch.
1. Po znalezieniu interfejsu API partii zaznacz go i wybierz pozycję **Wybierz**.
1. W **obszarze Zaznacz uprawnienia**zaznacz pole wyboru obok pozycji Dostęp do usługi **wsadowej usługi Azure,** a następnie wybierz pozycję **Dodaj uprawnienia**.

Sekcja **uprawnień interfejsu API** pokazuje teraz, że aplikacja usługi Azure AD ma dostęp zarówno do programu Microsoft Graph, jak i interfejsu API usługi Batch. Uprawnienia są przyznawane do programu Microsoft Graph automatycznie podczas pierwszego rejestrowania aplikacji w usłudze Azure AD.

![Udziel uprawnień interfejsu API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Korzystanie z jednostki usługi

Aby uwierzytelnić aplikację, która działa bez nadzoru, należy użyć jednostki usługi. Po zarejestrowaniu aplikacji wykonaj następujące kroki w witrynie Azure portal, aby skonfigurować jednostkę usługi:

1. Poproś o klucz tajny dla aplikacji.
1. Przypisz do aplikacji kontrolę dostępu opartą na rolach (RBAC).

### <a name="request-a-secret-for-your-application"></a>Poproś o klucz tajny dla swojej aplikacji

Gdy aplikacja uwierzytelnia się za pomocą jednostki usługi, wysyła zarówno identyfikator aplikacji, jak i klucz tajny do usługi Azure AD. Musisz utworzyć i skopiować klucz tajny, aby użyć go z kodu.

Wykonaj następujące kroki w witrynie Azure Portal:

1. W lewym okienku nawigacji w witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Wybierz **pozycję Rejestracje aplikacji**.
1. Wybierz aplikację z listy rejestracji aplikacji.
1. Wybierz aplikację, a następnie wybierz **pozycję Certyfikaty & wpisy tajne**. W sekcji **Wpisy tajne klienta** wybierz pozycję **Nowy klucz tajny klienta**.
1. Aby utworzyć klucz tajny, wprowadź opis klucza tajnego. Następnie wybierz wygaśnięcia tajemnicy jednego roku, dwóch lat lub bez wygaśnięcia..
1. Wybierz **dodaj,** aby utworzyć i wyświetlić klucz tajny. Skopiuj wartość tajną w bezpieczne miejsce, ponieważ nie będziesz mieć do niej dostępu ponownie po opuszczeniu strony.

    ![Tworzenie klucza tajnego](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Przypisywanie RBAC do aplikacji

Aby uwierzytelnić się za pomocą jednostki usługi, należy przypisać RBAC do aplikacji. Wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do konta usługi Batch używanego przez aplikację.
1. W sekcji **Ustawienia** konta usługi Batch wybierz pozycję **Kontrola dostępu (IAM)**.
1. Wybierz kartę **Przypisania ról.**
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. Z listy rozwijanej **Rola** wybierz rolę *współautora* lub *czytelnika* dla aplikacji. Aby uzyskać więcej informacji na temat tych ról, zobacz [Wprowadzenie do kontroli dostępu opartej na rolach w witrynie Azure portal.](../role-based-access-control/overview.md)  
1. W polu **Wybierz** wprowadź nazwę aplikacji. Wybierz aplikację z listy, a następnie wybierz pozycję **Zapisz**.

Aplikacja powinna teraz pojawić się w ustawieniach kontroli dostępu z przypisaną rolą RBAC.

![Przypisywanie roli RBAC do aplikacji](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Przypisywanie roli niestandardowej

Rola niestandardowa udziela szczegółowych uprawnień użytkownikowi do przesyłania zadań, zadań i innych funkcji. Zapewnia to możliwość uniemożliwienia użytkownikom wykonywania operacji, które wpływają na koszt, takich jak tworzenie pul lub modyfikowanie węzłów.

Za pomocą roli niestandardowej można udzielić uprawnień użytkownikowi, grupie lub podmiotowi usługi Azure AD dla następujących operacji RBAC:

- Microsoft.Batch/batchKonta/pule/zapis
- Microsoft.Batch/batchKonsuny/pule/usuwanie
- Microsoft.Batch/batchKonsuny/pule/odczyt
- Microsoft.Batch/batchKonta/zadaniaSześci/zapis
- Microsoft.Batch/batchKonta/zadaniaSześci/usuń
- Microsoft.Batch/batchKonta/zadaniaSześci/odczyt
- Microsoft.Batch/batchKonta/zadania/zapis
- Microsoft.Batch/batchKonta/zadania/usuń
- Microsoft.Batch/batchKonta/zadania/odczyt
- Microsoft.Batch/batchKonta/certyfikaty/zapis
- Microsoft.Batch/batchKonsunacje/certyfikaty/usuń
- Microsoft.Batch/batchKonsuny/certyfikaty/odczyt
- Microsoft.Batch/batchKonsuny/odczyt (dla każdej operacji odczytu)
- Microsoft.Batch/batchKonta/listKeys/akcja (dla każdej operacji)

Role niestandardowe są dla użytkowników uwierzytelnionych przez usługę Azure AD, a nie poświadczenia konta usługi Batch (klucz udostępniony). Należy zauważyć, że poświadczenia konta usługi Batch dają pełne uprawnienia do konta usługi Batch. Należy również zauważyć, że zadania korzystające z autopool wymagają uprawnień na poziomie puli.

Oto przykład niestandardowej definicji roli:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Aby uzyskać bardziej ogólne informacje dotyczące tworzenia roli niestandardowej, zobacz [Role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Uzyskaj identyfikator dzierżawy usługi Azure Active Directory

Identyfikator dzierżawy identyfikuje dzierżawę usługi Azure AD, która zapewnia usługi uwierzytelniania aplikacji. Aby uzyskać identyfikator dzierżawy, wykonaj następujące kroki:

1. W witrynie Azure portal wybierz usługę Active Directory.
1. Wybierz **pozycję Właściwości**.
1. Skopiuj wartość guid pod warunkiem **identyfikatora katalogu**. Ta wartość jest również nazywany identyfikatorem dzierżawy.

![Kopiowanie identyfikatora katalogu](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Przykłady kodu

Przykłady kodu w tej sekcji pokazują, jak uwierzytelniać się za pomocą usługi Azure AD przy użyciu zintegrowanego uwierzytelniania i jednostki usługi. Większość z tych przykładów kodu używa .NET, ale pojęcia są podobne dla innych języków.

> [!NOTE]
> Token uwierzytelniania usługi Azure AD wygasa po godzinie. Podczas korzystania z obiektu **batchclient** długowieczne, zaleca się pobrać token z biblioteki ADAL na każde żądanie, aby upewnić się, że zawsze masz prawidłowy token. 
>
>
> Aby to osiągnąć w .NET, napisz metodę, która pobiera token z usługi Azure AD i przekazać tę metodę do **obiektu BatchTokenCredentials** jako pełnomocnika. Metoda delegata jest wywoływana przy każdym żądaniu do usługi Batch, aby upewnić się, że jest dostarczany prawidłowy token. Domyślnie ADAL buforuje tokeny, więc nowy token jest pobierany z usługi Azure AD tylko wtedy, gdy jest to konieczne. Aby uzyskać więcej informacji na temat tokenów w usłudze Azure AD, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Przykład kodu: używanie zintegrowanego uwierzytelniania usługi Azure AD z usługą Batch .NET

Aby uwierzytelnić się przy użyciu zintegrowanego uwierzytelniania z usługi Batch .NET, odwołaj się do pakietu [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) i pakietu [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Dołącz do `using` kodu następujące instrukcje:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołanie się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikator dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w [pobierz identyfikator dzierżawy dla usługi Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołanie się do punktu końcowego zasobu usługi wsadowej:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołaj się do swojego konta Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) dla aplikacji. Identyfikator aplikacji jest dostępny w rejestracji aplikacji w witrynie Azure portal:

```csharp
private const string ClientId = "<application-id>";
```

Również skopiować identyfikator URI przekierowania, który został określony, jeśli aplikacja została zarejestrowana jako aplikacja macierzysta. Identyfikator URI przekierowania określony w kodzie musi być zgodny z identyfikatorem URI przekierowania podanym podczas rejestracji aplikacji:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Napisz metodę wywołania zwrotnego, aby uzyskać token uwierzytelniania z usługi Azure AD. Metoda **wywołania zwrotnego GetAuthenticationTokenAsync** pokazano w tym miejscu wywołuje adal uwierzytelnić użytkownika, który wchodzi w interakcję z aplikacją. **Metoda AcquireTokenAsync dostarczona** przez adal monituje użytkownika o ich poświadczenia, a aplikacja jest kontynuowana, gdy użytkownik je udostępnia (chyba że ma już zapisane poświadczenia w pamięci podręcznej):

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

Konstruuj **BatchTokenCredentials** obiektu, który przyjmuje delegata jako parametr. Użyj tych poświadczeń, aby otworzyć **Obiekt BatchClient.** Można użyć tego **obiektu BatchClient** dla kolejnych operacji względem usługi Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Przykład kodu: używanie jednostki usługi Azure AD z usługą Batch .NET

Aby uwierzytelnić się przy użyciu jednostki usługi z usługi Batch .NET, odwołaj się do pakietu [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/) i pakietu [ADAL.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

Dołącz do `using` kodu następujące instrukcje:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Odwołanie się do punktu końcowego usługi Azure AD w kodzie, w tym identyfikator dzierżawy. Korzystając z jednostki usługi, należy podać punkt końcowy specyficzne dla dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w [pobierz identyfikator dzierżawy dla usługi Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Odwołanie się do punktu końcowego zasobu usługi wsadowej:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Odwołaj się do swojego konta Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Określ identyfikator aplikacji (identyfikator klienta) dla aplikacji. Identyfikator aplikacji jest dostępny w rejestracji aplikacji w witrynie Azure portal:

```csharp
private const string ClientId = "<application-id>";
```

Określ klucz tajny skopiowany z witryny Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Napisz metodę wywołania zwrotnego, aby uzyskać token uwierzytelniania z usługi Azure AD. Metoda **wywołania zwrotnego GetAuthenticationTokenAsync** jest wyświetlana w tym miejscu, wywołuje metodę ADAL dla uwierzytelniania nienadzorowanego:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Konstruuj **BatchTokenCredentials** obiektu, który przyjmuje delegata jako parametr. Użyj tych poświadczeń, aby otworzyć **Obiekt BatchClient.** Następnie użyj tego **obiektu BatchClient** dla kolejnych operacji względem usługi Batch:

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

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Przykład kodu: używanie jednostki usługi Azure AD z usługą Batch Python

Aby uwierzytelnić się przy użyciu jednostki usługi z usługi Batch Python, zainstaluj i odwołaj się do modułów [azure-batch](https://pypi.org/project/azure-batch/) i [azure-common.](https://pypi.org/project/azure-common/)

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Korzystając z jednostki usługi, należy podać identyfikator dzierżawy. Aby pobrać identyfikator dzierżawy, wykonaj kroki opisane w [pobierz identyfikator dzierżawy dla usługi Azure Active Directory:](#get-the-tenant-id-for-your-active-directory)

```python
TENANT_ID = "<tenant-id>"
```

Odwołanie się do punktu końcowego zasobu usługi wsadowej:  

```python
RESOURCE = "https://batch.core.windows.net/"
```

Odwołaj się do swojego konta Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Określ identyfikator aplikacji (identyfikator klienta) dla aplikacji. Identyfikator aplikacji jest dostępny w rejestracji aplikacji w witrynie Azure portal:

```python
CLIENT_ID = "<application-id>"
```

Określ klucz tajny skopiowany z witryny Azure Portal:

```python
SECRET = "<secret-key>"
```

Utwórz obiekt **ServicePrincipalCredentials:**

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Użyj poświadczeń jednostki usługi, aby otworzyć **obiekt BatchServiceClient.** Następnie użyj tego **obiektu BatchServiceClient** dla kolejnych operacji względem usługi Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o usłudze Azure AD, zobacz [Dokumentację usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Szczegółowe przykłady pokazujące, jak używać biblioteki ADAL są dostępne w [bibliotece przykładów kodu Azure.](https://azure.microsoft.com/resources/samples/?service=active-directory)

- Aby dowiedzieć się więcej o jednostkach usługi, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Aby utworzyć jednostkę usługi przy użyciu witryny Azure Portal, zobacz [Tworzenie aplikacji i jednostki usługi Active Directory, która może uzyskiwać dostęp do zasobów, umożliwia utworzenie portalu](../active-directory/develop/howto-create-service-principal-portal.md)za pomocą portalu. Można również utworzyć jednostkę usługi za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

- Aby uwierzytelnić aplikacje zarządzania partią przy użyciu usługi Azure AD, zobacz [Uwierzytelnianie rozwiązań zarządzania wsadowego za pomocą usługi Active Directory](batch-aad-auth-management.md).

- Przykład pythonu dotyczący sposobu tworzenia klienta usługi Batch uwierzytelnionego przy użyciu tokenu usługi Azure AD można znaleźć w [przykładzie wdrażania niestandardowego obrazu usługi Azure Batch za pomocą przykładu skryptu języka Python.](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md)

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest usługa Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[azure_portal]: https://portal.azure.com
