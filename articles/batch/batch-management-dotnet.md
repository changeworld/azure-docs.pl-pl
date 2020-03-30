---
title: Zarządzanie zasobami konta za pomocą biblioteki Zarządzanie wsadami .NET
description: Tworzenie, usuwanie i modyfikowanie zasobów kont usługi Azure Batch za pomocą biblioteki .NET usługi Zarządzanie wsadowe.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 79916d769ad8a7228aec8db965c29506ccd78ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023688"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Zarządzanie kontami i przydziałami usługi Batch za pomocą biblioteki klienta usługi Zarządzanie wsadem dla platformy .NET

> [!div class="op_single_selector"]
> * [Portal Azure](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Można zmniejszyć obciążenie związane z konserwacją w aplikacjach usługi Azure Batch przy użyciu biblioteki [.NET usługi zarządzania wsadem][api_mgmt_net] do automatyzacji tworzenia konta usługi Batch, usuwania, zarządzania kluczami i odnajdowania przydziałów.

* **Tworzenie i usuwanie kont usługi Batch** w dowolnym regionie. Jeśli na przykład jako niezależny dostawca oprogramowania (ISV) świadczysz usługę dla klientów, w których każdemu z nich przypisano osobne konto usługi Batch do celów rozliczeniowych, można dodać funkcje tworzenia i usuwania kont do portalu klienta.
* **Pobieranie i ponowne generowanie kluczy kont** programowo dla dowolnego konta usługi Batch. Może to pomóc w przestrzeganiu zasad zabezpieczeń, które wymuszają okresowe rolowanie lub wygaśnięcie kluczy konta. Jeśli masz kilka kont usługi Batch w różnych regionach platformy Azure, automatyzacja tego procesu rolowania zwiększa wydajność rozwiązania.
* **Sprawdź przydziały kont** i wyjmij zgadywanie prób i błędów przy określaniu, które konta usługi Batch mają jakie limity. Sprawdzając przydziały kont przed uruchomieniem zadań, utworzeniem pul lub dodaniem węzłów obliczeniowych, można proaktywnie dostosować miejsce i czas tworzenia tych zasobów obliczeniowych. Przed przydzieleniem dodatkowych zasobów na tych kontach można określić, które konta wymagają zwiększenia przydziału.
* **Połącz funkcje innych usług platformy Azure** w celu uzyskania w pełni funkcjonowego środowiska zarządzania — przy użyciu usługi .NET, usługi Azure Active [Directory][aad_about]i [usługi Azure Resource Manager][resman_overview] w tej samej aplikacji. Korzystając z tych funkcji i ich interfejsów API, można zapewnić bezproblemowe uwierzytelnianie, możliwość tworzenia i usuwania grup zasobów oraz możliwości, które są opisane powyżej dla kompleksowego rozwiązania do zarządzania.

> [!NOTE]
> W tym artykule skupiono się na programowym zarządzaniu kontami, kluczami i przydziałami usługi Batch, ale wiele z tych działań można wykonać za pomocą [witryny Azure portal.][azure_portal] Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Batch przy użyciu portalu Azure](batch-account-create-portal.md) i [przydziałów i limitów dla usługi Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Tworzenie i usuwanie kont usługi Batch
Jak wspomniano, jedną z podstawowych funkcji interfejsu API zarządzania wsadem jest tworzenie i usuwanie kont usługi Batch w regionie platformy Azure. Aby to zrobić, należy użyć [BatchManagementClient.Account.CreateAsync][net_create] i [DeleteAsync][net_delete]lub ich synchroniczne odpowiedniki.

Poniższy fragment kodu tworzy konto, uzyskuje nowo utworzone konto z usługi Batch, a następnie usuwa je. W tym urywek i innych `batchManagementClient` w tym artykule, jest w pełni zainicjowane wystąpienie [BatchManagementClient][net_mgmt_client].

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
> Aplikacje korzystające z biblioteki .NET zarządzania wsadem i jej klasy BatchManagementClient wymagają administratora **usługi** lub **współadministratora** dostępu do subskrypcji, która jest właścicielem konta batch, które mają być zarządzane. Aby uzyskać więcej informacji, zobacz sekcję Usługi Azure Active Directory i przykład kodu [egoisminowania konta.][acct_mgmt_sample]
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Pobieranie i ponowne generowanie kluczy kont
Uzyskaj podstawowe i dodatkowe klucze kont z dowolnego konta usługi Batch w ramach subskrypcji przy użyciu [programu ListKeysAsync][net_list_keys]. Można ponownie wygenerować te klucze za pomocą [RegenerateKeyAsync][net_regenerate_keys].

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
> Można utworzyć usprawniony przepływ pracy połączenia dla aplikacji do zarządzania. Najpierw uzyskaj klucz konta dla konta usługi Batch, którym chcesz zarządzać za pomocą [programu ListKeysAsync][net_list_keys]. Następnie użyj tego klucza podczas inicjowania klasy [BatchSharedKeyCredentials][net_sharedkeycred] biblioteki .NET, która jest używana podczas inicjowania [pliku BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Sprawdzanie przydziałów kont subskrypcji platformy Azure i usługi Batch
Subskrypcje platformy Azure i poszczególne usługi platformy Azure, takie jak Batch, mają domyślne przydziały, które ograniczają liczbę niektórych jednostek w nich. Aby uzyskać domyślne przydziały dla subskrypcji platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md). Aby uzyskać domyślne przydziały usługi Batch, zobacz [Przydziały i limity dla usługi Azure Batch](batch-quota-limit.md). Korzystając z biblioteki .NET zarządzania wsadami, można sprawdzić te przydziały w aplikacjach. Dzięki temu można podejmować decyzje o alokacji przed dodaniem kont lub zasobów obliczeniowych, takich jak pule i węzły obliczeniowe.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Sprawdzanie subskrypcji platformy Azure pod kątem przydziałów kont usługi Batch
Przed utworzeniem konta usługi Batch w regionie, można sprawdzić subskrypcji platformy Azure, aby zobaczyć, czy można dodać konto w tym regionie.

We urywek kodu poniżej najpierw używamy [BatchManagementClient.Account.ListAsync,][net_mgmt_listaccounts] aby uzyskać kolekcję wszystkich kont usługi Batch, które znajdują się w ramach subskrypcji. Po uzyskaniu tej kolekcji określamy, ile kont znajduje się w regionie docelowym. Następnie używamy [BatchManagementClient.Subscriptions,][net_mgmt_subscriptions] aby uzyskać przydział konta usługi Batch i określić, ile kont (jeśli istnieją) można utworzyć w tym regionie.

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

We wtorkowym urywek `creds` jest wystąpieniem [TokenCloudCredentials][azure_tokencreds]. Aby zobaczyć przykład tworzenia tego obiektu, zobacz przykład kodu [AccountManagement][acct_mgmt_sample] w usłudze GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Sprawdzanie konta usługi Batch pod kątem przydziałów zasobów obliczeniowych
Przed zwiększeniem zasobów obliczeniowych w rozwiązaniu usługi Batch można sprawdzić, czy zasoby, które chcesz przydzielić, nie przekroczą przydziałów konta. We wetemencie kodu poniżej drukujemy informacje o `mybatchaccount`przydziałach dla konta usługi Batch o nazwie . W aplikacji można użyć takich informacji, aby ustalić, czy konto może obsługiwać dodatkowe zasoby, które mają zostać utworzone.

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
> Chociaż istnieją domyślne przydziały dla subskrypcji i usług platformy Azure, wiele z tych limitów można podnieść, wystawiając żądanie w [witrynie Azure portal.][azure_portal] Na przykład zobacz [przydziały i limity dla usługi Azure Batch,](batch-quota-limit.md) aby uzyskać instrukcje dotyczące zwiększania przydziałów kont usługi Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Używanie usługi Azure AD z usługą Azure Management .NET

Biblioteka .NET usługi Zarządzanie wsadami jest klientem dostawcy zasobów platformy Azure i jest używana razem z [usługą Azure Resource Manager][resman_overview] do programowego zarządzania zasobami kont. Usługa Azure AD jest wymagana do uwierzytelniania żądań złożonych za pośrednictwem dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki .NET usługi Zarządzanie wsadowe oraz za pośrednictwem [usługi Azure Resource Manager.][resman_overview] Aby uzyskać informacje dotyczące korzystania z usługi Azure AD z biblioteką .NET usługi Zarządzanie wsadowe, zobacz [Uwierzytelnianie rozwiązań usługi Azure Active Directory za pomocą usługi Azure Active Directory](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Przykładowy projekt w usłudze GitHub

Aby wyświetlić zarządzanie wsadowymi .NET w akcji, zapoznaj się z przykładowym projektem [AccountManagement][acct_mgmt_sample] w usłudze GitHub. Przykładowa aplikacja AccountManagement pokazuje następujące operacje:

1. Uzyskaj token zabezpieczający z usługi Azure AD przy użyciu [usługi ADAL][aad_adal]. Jeśli użytkownik nie jest jeszcze zalogowany, są monitowani o ich poświadczenia platformy Azure.
2. Za pomocą tokenu zabezpieczającego uzyskanego z usługi Azure AD utwórz [SubscriptionClient,][resman_subclient] aby zbadać platformę Azure w celu uzyskania listy subskrypcji skojarzonych z kontem. Użytkownik może wybrać subskrypcję z listy, jeśli zawiera więcej niż jedną subskrypcję.
3. Pobierz poświadczenia skojarzone z wybraną subskrypcją.
4. Utwórz [obiekt ResourceManagementClient][resman_client] przy użyciu poświadczeń.
5. Użyj [obiektu ResourceManagementClient,][resman_client] aby utworzyć grupę zasobów.
6. Użyj [obiektu BatchManagementClient,][net_mgmt_client] aby wykonać kilka operacji konta usługi Batch:
   * Utwórz konto usługi Batch w nowej grupie zasobów.
   * Pobierz nowo utworzone konto z usługi Batch.
   * Drukowanie kluczy konta dla nowego konta.
   * Ponownie wygeneruj nowy klucz podstawowy dla konta.
   * Wydrukuj informacje o przydziałach dla konta.
   * Drukowanie informacji o przydziałach dla subskrypcji.
   * Wydrukuj wszystkie konta w ramach subskrypcji.
   * Usuń nowo utworzone konto.
7. Usuń grupę zasobów.

Przed usunięciem nowo utworzonego konta i grupy zasobów usługi Batch można je wyświetlić w [witrynie Azure portal:][azure_portal]

Aby pomyślnie uruchomić przykładową aplikację, należy najpierw zarejestrować ją w dzierżawie usługi Azure AD w witrynie Azure portal i udzielić uprawnień do interfejsu API usługi Azure Resource Manager. Wykonaj kroki opisane w [rozwiązaniu Uwierzytelniania zarządzania wsadowego za pomocą usługi Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest usługa Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Scenariusze uwierzytelniania dla usługi Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrowanie aplikacji z usługą Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
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
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
