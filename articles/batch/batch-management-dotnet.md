---
title: Zarządzanie zasobami konta za pomocą biblioteki klienta dla platformy .NET — Azure Batch | Dokumentacja firmy Microsoft
description: Tworzenie, usuwanie i modyfikowanie zasobów konta usługi Azure Batch przy użyciu biblioteki zarządzania partiami platformy .NET.
services: batch
documentationcenter: .net
author: laurenhughes
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
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 301a3f9a500c41cf13dfa071d3526d2128b5e131
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775138"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Zarządzanie kontami usługi Batch i przydziałów za pomocą biblioteki klienta Batch Management dla platformy .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Możesz obniżyć konserwacji obciążenie w aplikacjach usługi Azure Batch przy użyciu [Batch Management .NET] [ api_mgmt_net] biblioteki, aby zautomatyzować tworzenie konta usługi Batch, usuwanie, zarządzanie kluczami i odnajdywania limitu przydziału.

* **Tworzenie i usuwanie kont usługi Batch** w dowolnym regionie. Jeśli jako niezależny dostawca oprogramowania (ISV) na przykład można udostępniać usługi dla klientów, w których każdy jest przypisywana oddzielnego konta usługi Batch na potrzeby rozliczeń, możliwości tworzenia i usuwania konta można dodać do portalu klientów.
* **Pobieranie i ponowne generowanie kluczy konta** programowo dla każdej z kontami usługi Batch. Może to pomóc w są zgodne z zasadami zabezpieczeń, które wymuszają okresowe przerzucania lub wygaśnięcie kluczy konta. Jeśli masz kilka kont usługi Batch w różnych regionach świadczenia usługi Azure automation tego procesu przerzucania zwiększa efektywność swojego rozwiązania.
* **Sprawdź limity przydziału konta** i określanie konta usługi Batch, które mają limity jakie zgadywania prób i błędów. Sprawdzając limity przydziału konta przed rozpoczęciem zadania, tworzenie pul lub dodawanie węzłów obliczeniowych, gdzie można dostosować aktywnie lub gdy te obliczenia są tworzone zasoby. Można określić konta, które wymagają przydziału zwiększa się przed przydzieleniem dodatkowych zasobów w ramach tych kont.
* **Łączenie funkcje innych usług platformy Azure** zarządzania w pełni funkcjonalne środowisko pracy — przy użyciu usługi Batch Management .NET [usługi Azure Active Directory][aad_about]i [platformy Azure Menedżer zasobów] [ resman_overview] razem w tej samej aplikacji. Za pomocą tych funkcji i ich interfejsów API, może zapewnić środowisko uwierzytelniania bezproblemowy, możliwość tworzenia i usuwania grupy zasobów i możliwości, które są opisane powyżej rozwiązania do zarządzania end-to-end.

> [!NOTE]
> Chociaż ten artykuł koncentruje się na programowego zarządzania kontami usługi Batch, klucze i limitami przydziału, można wykonać wiele z tych działań przy użyciu [witryny Azure portal][azure_portal]. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Batch przy użyciu witryny Azure portal](batch-account-create-portal.md) i [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Tworzenie i usuwanie kont usługi Batch
Jak wspomniano wcześniej, jest jedną z podstawowych funkcji interfejsu API zarządzania usługi Batch do tworzenia i usuwania konta usługi Batch w regionie platformy Azure. Aby to zrobić, należy użyć [BatchManagementClient.Account.CreateAsync] [ net_create] i [DeleteAsync][net_delete], lub ich odpowiedników synchroniczne.

Poniższy fragment kodu tworzy konto usługi, uzyskuje nowo utworzonego konta usługi Batch, a następnie usuwa je. W tym fragmencie kodu i innych użytkowników, w tym artykule `batchManagementClient` jest w pełni zainicjowany wystąpieniem [BatchManagementClient][net_mgmt_client].

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
> Aplikacje korzystające z biblioteki Batch Management .NET i jego klasa BatchManagementClient wymagają **administratora usługi** lub **współadministrator** dostępu do subskrypcji, która jest właścicielem usługi Batch konto, które mają być zarządzane. Aby uzyskać więcej informacji, zobacz sekcję usługi Azure Active Directory i [zarządzania kontem] [ acct_mgmt_sample] przykładowy kod.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Pobieranie i ponowne generowanie kluczy konta
Uzyskaj klucze podstawowe i pomocnicze konta z dowolnego konta usługi Batch w ramach subskrypcji za pomocą [ListKeysAsync][net_list_keys]. Ponowne generowanie tych kluczy przy użyciu [RegenerateKeyAsync][net_regenerate_keys].

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
> Można utworzyć połączenia usprawnione przepływu pracy dla aplikacji do zarządzania. Po pierwsze, uzyskiwania klucza konta usługi dla konta usługi Batch, którą chcesz zarządzać za pomocą [ListKeysAsync][net_list_keys]. Następnie należy użyć tego klucza, podczas inicjowania biblioteki .NET usługi Batch [BatchSharedKeyCredentials] [ net_sharedkeycred] klasy, która jest używana podczas inicjowania [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Sprawdź subskrypcję platformy Azure i limity przydziału konta usługi Batch
Subskrypcje platformy Azure i poszczególnych usług platformy Azure, takich jak Batch, wszystkie mają domyślne limity przydziału ograniczające liczbę niektórych jednostek w nich. Aby uzyskać domyślne limity przydziału dla subskrypcji platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md). Aby uzyskać domyślne limity przydziałów usługi Batch, zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md). Za pomocą biblioteki Batch Management .NET, możesz sprawdzić te przydziały w swoich aplikacjach. Dzięki temu można podjąć decyzje alokacji przed Dodaj konta lub zasoby, takie jak pule obliczeniowe i węzły obliczeniowe.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Sprawdź subskrypcję platformy Azure dla przydziałów konta usługi Batch
Przed utworzeniem konta usługi Batch w regionie, możesz sprawdzić subskrypcji platformy Azure, aby zobaczyć, czy jesteś w stanie dodać konto w danym regionie.

W poniższym fragmencie kodu najpierw używamy [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] można pobrać kolekcję wszystkie konta usługi Batch, które znajdują się w subskrypcji. Gdy firma Microsoft została uzyskana tej kolekcji, określamy, jak wiele kont znajdują się w regionie docelowym. Następnie użyjemy [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] uzyskać limitu przydziału konta usługi Batch i określić, ile konta (jeśli istnieje) można tworzyć w tym regionie.

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

W powyższym fragmencie `creds` jest wystąpieniem [TokenCloudCredentials][azure_tokencreds]. Aby zapoznać się przykładem tworzenia tego obiektu, zobacz [zarządzania kontem] [ acct_mgmt_sample] przykładowy kod w witrynie GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Sprawdź konto usługi Batch dla limity przydziałów zasobów obliczeniowych
Przed zwiększeniem zasobów obliczeniowych w rozwiązaniu usługi Batch, możesz sprawdzić, aby upewnić się, zasoby, które mają zostać przydzielone nie przekracza limity przydziału dla konta. W poniższym fragmencie kodu firma Microsoft Drukuj informacje o limitach przydziału dla konta usługi Batch, o nazwie `mybatchaccount`. W swojej aplikacji można użyć tych informacji do określenia, czy to konto może obsłużyć dodatkowe zasoby, które ma zostać utworzony.

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
> Choć istnieją domyślne limity przydziału dla subskrypcji platformy Azure i usług, wiele z tych limitów może zostać wywołane przez żądania w [witryny Azure portal][azure_portal]. Na przykład zobacz [przydziały i limity dla usługi Azure Batch](batch-quota-limit.md) instrukcje dotyczące zwiększyć limity przydziału konta usługi Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Używaj usługi Azure AD z zarządzaniem usługą Batch .NET

Biblioteka zarządzania partiami platformy .NET jest klientem dostawcy zasobów platformy Azure i jest używany razem z [usługi Azure Resource Manager] [ resman_overview] do programowego zarządzania zasobami konta. Usługa Azure AD jest wymagane do uwierzytelnienia żądania wysyłane za pośrednictwem dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki Batch Management .NET oraz [usługi Azure Resource Manager][resman_overview]. Aby dowiedzieć się, jak za pomocą usługi Azure AD przy użyciu biblioteki zarządzania partiami platformy .NET, zobacz [użycia usługi Azure Active Directory uwierzytelnianie rozwiązań usługi Batch](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Przykładowy projekt w witrynie GitHub

Aby zobaczyć zarządzania partiami platformy .NET w akcji, obejrzyj [zarządzania kontem] [ acct_mgmt_sample] przykładowy projekt w witrynie GitHub. Przykładowa aplikacja zarządzania kontem pokazuje następujące operacje:

1. Uzyskiwanie tokenu zabezpieczającego z usługi Azure AD przy użyciu [ADAL][aad_adal]. Jeśli użytkownik nie jest już zalogowany, są monitowani o podanie poświadczeń platformy Azure.
2. Z tokenem zabezpieczającym, uzyskany z usługi Azure AD, Utwórz [SubscriptionClient] [ resman_subclient] do wykonywania zapytań usługi Azure, aby uzyskać listę subskrypcji skojarzonych z kontem. Użytkownik może wybrać subskrypcję z listy, jeśli zawiera więcej niż jedną subskrypcję.
3. Uzyskaj poświadczenia skojarzone z wybraną subskrypcję.
4. Tworzenie [ResourceManagementClient] [ resman_client] obiektu przy użyciu poświadczeń.
5. Użyj [ResourceManagementClient] [ resman_client] obiekt, aby utworzyć grupę zasobów.
6. Użyj [BatchManagementClient] [ net_mgmt_client] obiektu wykonanie kilku operacji konto usługi Batch:
   * Utwórz konto usługi Batch w nowej grupie zasobów.
   * Uzyskaj nowo utworzonego konta usługi Batch.
   * Drukowanie kluczy kont dla nowego konta.
   * Ponowne generowanie klucza podstawowego dla konta.
   * Drukuj informacje o limitach przydziału dla konta.
   * Drukuj informacje o limitach przydziału dla subskrypcji.
   * Wydrukowanie wszystkich kont w ramach subskrypcji.
   * Usuń nowo utworzonego konta.
7. Usuń grupę zasobów.

Przed usunięciem nowo utworzona grupa kont i zasobów usługi Batch, można wyświetlić je w [witryny Azure portal][azure_portal]:

Pomyślnie uruchomić przykładową aplikację, najpierw musisz zarejestrować ją w swojej dzierżawie usługi Azure AD w witrynie Azure portal i przyznać uprawnienia do interfejsu API usługi Azure Resource Manager. Postępuj zgodnie z krokami opisanymi w [rozwiązania uwierzytelniania Batch Management z usługą Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Co to jest Azure Active Directory?"
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
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
