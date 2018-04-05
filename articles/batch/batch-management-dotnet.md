---
title: Zarządzanie zasobami konta usługi partia zadań za pomocą biblioteki klienta dla platformy .NET - Azure | Dokumentacja firmy Microsoft
description: Tworzenie, usuwanie i modyfikowanie zasobów konta partii zadań Azure przy użyciu biblioteki zarządzania partiami platformy .NET.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2d0b261a991dc02b670488c14392697ea984ec
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Zarządzanie kontami partii i przydziały z biblioteki klienta usługi partia zadań zarządzania dla platformy .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Można obniżyć konserwacji nakładów pracy w aplikacji partii zadań Azure za pomocą [zarządzania partiami platformy .NET] [ api_mgmt_net] biblioteki można zautomatyzować tworzenie konta usługi partia zadań, usuwanie, zarządzania kluczami i odnajdywania przydziału.

* **Tworzenie i usuwanie konta usługi partia zadań** w dowolnym regionie. Jeśli niezależnego dostawcy oprogramowania (ISV) na przykład można udostępnić usługi dla klientów, w których każdy jest przypisywana oddzielne konta usługi partia zadań do celów rozliczeń, możliwości tworzenia i usuwania konta można dodać do portalu klienta.
* **Pobierz i ponownie wygenerować kluczy konta** programowo dla każdego konta usługi partia zadań. Może to pomóc w przestrzegania zasad zabezpieczeń, które wymuszają okresowe przerzucania lub wygaśnięcia klucze konta. Jeśli masz kilka kont usługi partia zadań w różnych regionach platformy Azure, automatyzacji tego procesu przerzucania zwiększa wydajność rozwiązania.
* **Sprawdź konto przydziały** i wykonać czynności prób i błędów poza określania kont usługi partia zadań, które mają jakie limity. Sprawdzając przydziałami konta przed uruchomieniem zadania, tworzenia pul, lub dodawanie węzłów obliczeniowych, gdzie można dostosować aktywnego lub gdy obliczeniowe te zasoby są tworzone. Można określić konta, które wymagają przydziału zwiększa przed przydzielania dodatkowych zasobów w tych kont.
* **Łączenie funkcji z innymi usługami Azure** środowisko oferujący wszystkie funkcje zarządzania — za pomocą zarządzania partiami platformy .NET, [usługi Azure Active Directory][aad_about]i [Azure Menedżer zasobów] [ resman_overview] razem w tej samej aplikacji. Korzystając z tych funkcji i ich interfejsów API, można zapewnić środowisko frictionless uwierzytelniania, możliwość tworzenia i usuwania grup zasobów i możliwości, które są opisane powyżej rozwiązania do zarządzania end-to-end.

> [!NOTE]
> Chociaż ten artykuł dotyczy programowe zarządzanie konta wsadowego, kluczy i przydziały, mogą wykonywać wiele z tych działań za pomocą [portalu Azure][azure_portal]. Aby uzyskać więcej informacji, zobacz [utworzyć konto partii zadań Azure za pomocą portalu Azure](batch-account-create-portal.md) i [przydziały i limity dla usługi partia zadań Azure](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Tworzenie i usuwanie konta usługi partia zadań
Jak wspomniano, co główne funkcje interfejsu API zarządzania partii jest do tworzenia i usuwania konta usługi partia zadań w regionie platformy Azure. Aby to zrobić, użyj [BatchManagementClient.Account.CreateAsync] [ net_create] i [DeleteAsync][net_delete], lub ich odpowiedniki synchronicznego.

Poniższy fragment kodu tworzy konto, uzyskuje nowo utworzonych kont z usługi partia zadań i usuwa go. Ta Wstawka kodu i innych użytkowników, w tym artykule `batchManagementClient` jest całkowicie zainicjowany wystąpieniem [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Aplikacje korzystające z biblioteki zarządzania partiami platformy .NET i jego klasa BatchManagementClient wymagają **administratora usługi** lub **coadministrator** dostępu do subskrypcji, która jest właścicielem konta usługi partia zadań, które mają być zarządzane. Aby uzyskać więcej informacji, zobacz [usługi Azure Active Directory](#azure-active-directory) sekcji i [AccountManagement] [ acct_mgmt_sample] przykładowy kod.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Pobierz i ponownie wygenerować kluczy konta
Uzyskaj klucze podstawowe i pomocnicze konta z dowolnego konta usługi partia zadań w ramach subskrypcji, za pomocą [ListKeysAsync][net_list_keys]. Można ponownie wygenerować te klucze za pomocą [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Można utworzyć połączenia prostsze przepływu pracy dla aplikacji do zarządzania. Najpierw uzyskać klucz konta dla konta usługi partia zadań, mają być zarządzane za pomocą [ListKeysAsync][net_list_keys]. Następnie należy użyć tego klucza podczas inicjowania biblioteki partiami platformy .NET [BatchSharedKeyCredentials] [ net_sharedkeycred] klasy, która jest używana podczas inicjowania [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Sprawdź subskrypcję platformy Azure i przydziały konta usługi partia zadań
Subskrypcje platformy Azure i poszczególnych usług Azure, takich jak partii wszystkie mają przydziałów domyślnych, które ograniczają liczbę niektórych jednostek w nich. Aby uzyskać przydziałów domyślnych dla subskrypcji platformy Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md). Aby przydziałów domyślnych usługa partia zadań, zobacz [przydziały i limity dla usługi partia zadań Azure](batch-quota-limit.md). Przy użyciu biblioteki zarządzania partiami platformy .NET, te przydziały można sprawdzić w aplikacji. Umożliwia podjęcie decyzji alokacji, przed dodać konta lub zasobów, takich jak pule obliczeniowe i węzły obliczeniowe.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Sprawdź subskrypcję platformy Azure dla przydziały konta usługi partia zadań
Przed utworzeniem konta usługi partia zadań w regionie, można sprawdzić subskrypcji platformy Azure, aby zobaczyć, czy jesteś w stanie w celu dodania konta w tym regionie.

W poniższym fragmencie kodu, najpierw używamy [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] uzyskać zbiór wszystkich kont usługi partia zadań, które są w ramach subskrypcji. Gdy mamy już tej kolekcji, możemy określić liczbę kont region docelowy. Następnie użyjemy [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] uzyskać limit przydziału kont usługi partia zadań i określić, ile kont (jeśli istnieją) można tworzyć w tym regionie.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

We fragmencie powyżej `creds` jest wystąpieniem [TokenCloudCredentials][azure_tokencreds]. Aby zapoznać się z przykładem tworzenia tego obiektu, zobacz [AccountManagement] [ acct_mgmt_sample] przykładowy kod w witrynie GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Sprawdź konto usługi partia zadań dla przydziały zasobów obliczeniowych
Przed zwiększeniem zasobów obliczeniowych w rozwiązaniu partii, można sprawdzić, upewnij się, zasoby, które mają zostać przydzielone nie przekracza limity przydziału dla konta. We fragmencie kodu poniżej, firma Microsoft wydrukować informacje o limicie przydziału dla konta usługi partia zadań o nazwie `mybatchaccount`. W aplikacji można użyć tych informacji do określenia, czy konto może obsłużyć dodatkowe zasoby do utworzenia.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Gdy istnieją przydziałów domyślnych dla subskrypcji platformy Azure i usługi, wiele z tych limitów może być zgłaszany przez żądania w [portalu Azure][azure_portal]. Na przykład, zobacz [przydziały i limity dla usługi partia zadań Azure](batch-quota-limit.md) instrukcje dotyczące zwiększenie przydziałami konta usługi partia zadań.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Usługi Azure AD za pomocą zarządzania partii .NET

Biblioteka zarządzania partiami platformy .NET jest klientem dostawcy zasobów platformy Azure i jest używany razem z [usługi Azure Resource Manager] [ resman_overview] programowo zarządzać zasobami konta. Usługi Azure AD jest wymagany do uwierzytelniania żądań wysyłanych za pomocą dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki zarządzania partiami platformy .NET i za pośrednictwem [usługi Azure Resource Manager][resman_overview]. Aby uzyskać informacje o korzystaniu z usługi Azure AD z biblioteki zarządzania partiami platformy .NET, zobacz [użycia usługi Azure Active Directory do uwierzytelniania rozwiązań partii](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Przykładowy projekt w witrynie GitHub

Aby wyświetlić zarządzania partiami platformy .NET w akcji, zapoznaj się [AccountManagment] [ acct_mgmt_sample] przykładowy projekt w witrynie GitHub. Przykładowa aplikacja AccountManagment przedstawiono następujące operacje:

1. Uzyskanie tokenu zabezpieczającego z usługi Azure AD przy użyciu [ADAL][aad_adal]. Jeśli użytkownik nie jest już zalogowany, są monitowani o podanie poświadczeń platformy Azure.
2. Token zabezpieczający uzyskane z usługi Azure AD, tworzenie [SubscriptionClient] [ resman_subclient] zapytania Azure listę subskrypcji skojarzonych z kontem. Użytkownik może wybrać subskrypcję z listy, jeśli zawiera więcej niż jedną subskrypcję.
3. Uzyskiwanie poświadczeń skojarzonych z wybraną subskrypcję.
4. Utwórz [element ResourceManagementClient] [ resman_client] obiektu przy użyciu poświadczeń.
5. Użyj [element ResourceManagementClient] [ resman_client] obiekt, aby utworzyć grupę zasobów.
6. Użyj [BatchManagementClient] [ net_mgmt_client] obiektu w celu wykonania kilku operacji konto usługi partia zadań:
   * Utwórz konto wsadowe w nowej grupy zasobów.
   * Pobierz nowo utworzonych kont z usługi partia zadań.
   * Drukowanie klucze konta dla nowego konta.
   * Ponowne generowanie klucza podstawowego dla konta.
   * Drukowanie informacje o limicie przydziału dla konta.
   * Drukowanie informacje o limicie przydziału dla subskrypcji.
   * Wydrukowanie wszystkich kont w ramach subskrypcji.
   * Usuń nowo utworzonego konta.
7. Usuń grupę zasobów.

Przed usunięciem nowo utworzona grupa kont i zasobów usługi partia zadań, można wyświetlić je w [portalu Azure][azure_portal]:

Aby pomyślnie uruchomić przykładową aplikację, najpierw musisz zarejestrować go z dzierżawy usługi Azure AD w portalu Azure i udzielić uprawnień do interfejsu API Azure Resource Manager. Postępuj zgodnie z krokami opisanymi w [rozwiązań do zarządzania partii uwierzytelniania z usługi Active Directory](batch-aad-auth-management.md).


[aad_about]: ../active-directory/active-directory-whatis.md "Co to jest usługa Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrowanie aplikacji z usługą Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
