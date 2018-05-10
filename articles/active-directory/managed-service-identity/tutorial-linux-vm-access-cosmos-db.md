---
title: Umożliwia dostęp do bazy danych Azure rozwiązania Cosmos MSI maszyny Wirtualnej systemu Linux
description: Samouczek, który przeprowadzi Cię przez proces przy użyciu System-Assigned zarządzane usługi tożsamości (MSI) na maszynie Wirtualnej systemu Linux, można uzyskać dostępu do bazy danych Azure rozwiązania Cosmos.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: 145cfac02db5aa73e92da8bb281b88b28dc0e22e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>Umożliwia dostęp do bazy danych Azure rozwiązania Cosmos MSI maszyny Wirtualnej systemu Linux 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


W tym samouczku przedstawiono sposób tworzenia i używania MSI maszyny Wirtualnej systemu Linux. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz Maszynę wirtualną systemu Linux msi włączone
> * Tworzenie konta usługi Cosmos DB
> * Tworzenie kolekcji w ramach konta bazy danych rozwiązania Cosmos
> * Udziel dostępu do pliku MSI na wystąpienie bazy danych Azure rozwiązania Cosmos
> * Pobrać `principalID` z pliku msi maszyny Wirtualnej systemu Linux
> * Uzyskaj token dostępu i użyć go do wywołania usługi Azure Resource Manager
> * Uzyskaj klucze dostępu z usługi Azure Resource Manager w celu wykonywania wywołań DB rozwiązania Cosmos

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz jeszcze konta platformy Azure, [Załóż bezpłatne konto](https://azure.microsoft.com) przed kontynuowaniem.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, w tym samouczku, masz dwie opcje:

- Użyj [powłoki chmury Azure](~/articles/cloud-shell/overview.md) w portalu Azure lub za pośrednictwem **spróbuj on** przycisk znajdujący się w prawym górnym rogu każdej blok kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Utwórz maszynę wirtualną systemu Linux w nowej grupy zasobów

W tym samouczku, Utwórz nową MSI włączone maszyny Wirtualnej systemu Linux.

Aby utworzyć maszynę Wirtualną z włączoną MSI:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz wdrożyć maszyny Wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania maszyny Wirtualnej i powiązanych zasobów, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Ten krok można pominąć, jeśli masz już grupę zasobów, które chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz maszynę Wirtualną przy użyciu [tworzenia maszyny wirtualnej az](/cli/azure/vm/#az_vm_create). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVM* msi, zgodnie z żądaniem `--assign-identity` parametru. `--admin-username` i `--admin-password` parametry Określ konto użytkownika administracyjnego nazwę i hasło do logowania w maszynie wirtualnej. Zaktualizować te wartości jako odpowiednie dla danego środowiska: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12

## Create a Cosmos DB account 

If you don't already have one, create a Cosmos DB account. You can skip this step and use an existing Cosmos DB account. 

1. Click the **+/Create new service** button found on the upper left-hand corner of the Azure portal.
2. Click **Databases**, then **Azure Cosmos DB**, and a new "New account" panel  displays.
3. Enter an **ID** for the Cosmos DB account, which you use later.  
4. **API** should be set to "SQL." The approach described in this tutorial can be used with the other available API types, but the steps in this tutorial are for the SQL API.
5. Ensure the **Subscription** and **Resource Group** match the ones you specified when you created your VM in the previous step.  Select a **Location** where Cosmos DB is available.
6. Click **Create**.

## Create a collection in the Cosmos DB account

Next, add a data collection in the Cosmos DB account that you can query in later steps.

1. Navigate to your newly created Cosmos DB account.
2. On the **Overview** tab click the **+/Add Collection** button, and an "Add Collection" panel slides out.
3. Give the collection a database ID, collection ID, select a storage capacity, enter a partition key, enter a throughput value, then click **OK**.  For this tutorial, it is sufficient to use "Test" as the database ID and collection ID, select a fixed storage capacity and lowest throughput (400 RU/s).  

## Retrieve the `principalID` of the Linux VM's MSI

To gain access to the Cosmos DB account access keys from the Resource Manager in the following section, you need to retrieve the `principalID` of the Linux VM's MSI.  Be sure to replace the `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (resource group in which you VM resides), and `<VM NAME>` parameter values with your own values.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Odpowiedź zawiera szczegóły MSI przypisane systemu (Uwaga principalID, ponieważ jest używany w następnej sekcji):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Przyznać uprawnienia MSI maszyny Wirtualnej systemu Linux do klucze dostępu do konta DB rozwiązania Cosmos

Rozwiązania cosmos bazy danych nie obsługuje natywnie uwierzytelniania usługi Azure AD. Jednak można użyć Instalatora MSI można pobrać klucz dostępu DB rozwiązania Cosmos z Menedżera zasobów, a następnie za pomocą klawisza dostępu DB rozwiązania Cosmos. W tym kroku należy przyznać uprawnienia MSI kluczy do konta DB rozwiązania Cosmos.

Aby udzielić dostępu tożsamości MSI na koncie DB rozwiązania Cosmos w usłudze Azure Resource Manager przy użyciu wiersza polecenia platformy Azure, zaktualizuj wartości dla `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<COSMOS DB ACCOUNT NAME>` dla danego środowiska. Zastąp `<MSI PRINCIPALID>` z `principalId` właściwości zwróconej przez `az resource show` w [pobrać principalID msi maszyny Wirtualnej systemu Linux](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Rozwiązania cosmos bazy danych obsługuje dwa poziomy szczegółowości klawiszy dostępu: odczytu/zapisu dostępu do konta, a także dostęp tylko do odczytu do konta.  Przypisz `DocumentDB Account Contributor` roli, jeśli chcesz pobrać klucze odczytu/zapisu dla konta lub przypisać `Cosmos DB Account Reader Role` roli, jeśli chcesz pobrać klucze tylko do odczytu dla konta:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Odpowiedź zawiera szczegóły dotyczące przypisania roli, utworzyć:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Uzyskaj token dostępu za pomocą Instalatora MSI maszyny Wirtualnej systemu Linux i użyć go do wywołania usługi Azure Resource Manager

W pozostałej części tego samouczka Praca z maszyny Wirtualnej utworzone wcześniej.

Aby wykonać te kroki, należy klient SSH. Jeśli korzystasz z systemu Windows, możesz użyć klienta SSH w [podsystemu systemu Windows dla systemu Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Jeśli potrzebujesz pomocy w konfigurowaniu kluczy klienta SSH, zobacz [sposobu użycia SSH kluczy systemu Windows Azure](../../virtual-machines/linux/ssh-from-windows.md), lub [sposobu tworzenia i używania SSH publiczne i prywatne parę kluczy dla maszyn wirtualnych systemu Linux na platformie Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. W portalu Azure, przejdź do **maszyn wirtualnych**, przejdź do maszyny wirtualnej systemu Linux, następnie z **omówienie** kliknij **Connect** u góry. Skopiuj ciąg, aby nawiązać połączenie z maszyną Wirtualną. 
2. Nawiązać połączenie z maszyną Wirtualną przy użyciu klienta SSH.  
3. Następnie zostanie wyświetlony monit o wprowadź w Twojej **hasło** dodane podczas tworzenia **maszyny Wirtualnej systemu Linux**. Użytkownik powinien następnie można pomyślnie zarejestrowany.  
4. Umożliwia ZWINIĘCIE Uzyskaj token dostępu usługi Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > W żądaniu poprzedniej wartości parametru "zasobu" musi być dokładnego dopasowania dla oczekiwano przez usługę Azure AD. Podczas korzystania z usługi Azure Resource Manager identyfikator zasobu, należy dołączyć końcowy ukośnik w identyfikatorze URI.
    > W poniższych odpowiedzi elementu ' access_token ', jak została skrócona do skrócenia.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Uzyskaj klucze dostępu z usługi Azure Resource Manager w celu wykonywania wywołań DB rozwiązania Cosmos  

Teraz można użyć CURL do wywołania usługi Resource Manager można pobrać klucz dostępu do konta rozwiązania Cosmos DB przy użyciu tokenu dostępu pobrany w poprzedniej sekcji. Gdy będziemy mieć klucz dostępu, możemy zapytania DB rozwiązania Cosmos. Pamiętaj zastąpić `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, i `<COSMOS DB ACCOUNT NAME>` wartości parametrów z własne wartości. Zastąp `<ACCESS TOKEN>` wartość przy użyciu tokenu dostępu został wcześniej pobrany.  Jeśli chcesz pobrać klucze odczytu/zapisu, użyj operacji klucza typu `listKeys`.  Jeśli chcesz pobrać klucze tylko do odczytu, użyj typu klucza operacji `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Tekst w adresie URL wcześniejsze jest rozróżniana wielkość liter, dlatego upewnij się, jeśli używasz górna małe dla grupy zasobów, uwzględnienie w związku z tym. Ponadto jest ważne dowiedzieć się, że jest to żądanie POST nie żądanie GET i upewnij się, że należy przekazać wartość do przechwytywania limit długości - d, który może mieć wartości NULL.  

Odpowiedzi CURL zawiera listę kluczy.  Na przykład, jeśli zostanie wyświetlony tylko do odczytu klucze:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Teraz, po klucz dostępu dla konta rozwiązania Cosmos bazy danych, możesz przekazać go do SDK DB rozwiązania Cosmos i wywoływać w celu uzyskania dostępu do konta.  Na przykład szybki można przekazać klucz dostępu do wiersza polecenia platformy Azure.  Możesz uzyskać <COSMOS DB CONNECTION URL> z **omówienie** kartę w bloku konta DB rozwiązania Cosmos w portalu Azure.  Zastąp <ACCESS KEY> o wartości otrzymanych powyżej:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

To polecenie interfejsu wiersza polecenia zwraca szczegółowe informacje o kolekcji:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

- Omówienie MSI, zobacz [zarządzane usługi tożsamości (MSI) dla zasobów Azure](overview.md).

