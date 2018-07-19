---
title: Uzyskiwanie dostępu do usługi Azure Cosmos DB przy użyciu tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows
description: Samouczek przedstawiający proces użycia przypisanej przez system tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: cee3a1425d7c3ad8f680394831175165203b4839
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005649"
---
# <a name="tutorial-use-a-windows-vm-msi-to-access-azure-cosmos-db"></a>Samouczek: uzyskiwanie dostępu do usługi Azure Cosmos DB przy użyciu tożsamości usługi zarządzanej na maszynie wirtualnej z systemem Windows

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku przedstawiono sposób utworzenia i użycia tożsamości usługi zarządzanej maszyny wirtualnej w celu uzyskania dostępu do usługi Cosmos DB. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows z włączoną tożsamością usługi zarządzanej 
> * Tworzenie konta usługi Cosmos DB
> * Udzielanie dostępu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows do kluczy dostępu do konta usługi Cosmos DB
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej z systemem Windows w celu wywoływania usługi Azure Resource Manager
> * Uzyskiwanie kluczy dostępu z usługi Azure Resource Manager w celu wykonywania wywołań usługi Cosmos DB

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Tworzenie maszyny wirtualnej z systemem Windows w nowej grupie zasobów

W tym samouczku utworzymy nową maszynę wirtualną z systemem Windows.  Możesz też włączyć tożsamość usługi zarządzanej na istniejącej maszynie wirtualnej.

1. Kliknij przycisk **Utwórz zasób** (+) znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. 
3. Wprowadź informacje o maszynie wirtualnej. **Nazwa użytkownika** i **Hasło** utworzone w tym miejscu są poświadczeniami używanymi do logowania do maszyny wirtualnej.
4. Wybierz odpowiednią **Subskrypcję** dla maszyny wirtualnej z listy rozwijanej.
5. Aby wybrać nową **Grupę zasobów**, w której chcesz utworzyć maszynę wirtualną, wybierz opcję **Utwórz nową**. Po zakończeniu kliknij przycisk **OK**.
6. Wybierz rozmiar maszyny wirtualnej. Aby wyświetlić więcej rozmiarów, wybierz pozycje **Wyświetl wszystkie** lub zmień filtr **Obsługiwany typ dysku**. Na stronie ustawień pozostaw ustawienia domyślne i kliknij przycisk **OK**.

   ![Alternatywny tekst obrazu](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Włączanie tożsamości usługi zarządzanej na maszynie wirtualnej 

Tożsamość usługi zarządzanej maszyny wirtualnej umożliwia uzyskanie tokenów dostępu z usługi Azure AD bez potrzeby umieszczania poświadczeń w kodzie. Włączenie tożsamości zarządzanej na maszynie wirtualnej w witrynie Azure Portal powoduje wykonanie w niewidoczny sposób dwóch czynności: zarejestrowania maszyny wirtualnej przy użyciu usługi Azure AD w celu utworzenia tożsamości zarządzanej oraz skonfigurowania tożsamości na maszynie wirtualnej.

1. Wybierz **maszynę wirtualną**, dla której chcesz włączyć tożsamość usługi zarządzanej.  
2. Na lewym pasku nawigacyjnym kliknij opcję **Konfiguracja**. 
3. Zobaczysz ekran **Tożsamość usługi zarządzanej**. Aby zarejestrować i włączyć tożsamość usługi zarządzanej, wybierz opcję **Tak**. Jeśli chcesz ją wyłączyć, wybierz opcję Nie. 
4. Pamiętaj, aby kliknąć przycisk **Zapisz** w celu zapisania konfiguracji.  
   ![Alternatywny tekst obrazu](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB 

Jeśli jeszcze nie masz konta usługi Cosmos DB, utwórz je. Możesz pominąć ten krok i użyć istniejącego konta usługi Cosmos DB. 

1. Kliknij przycisk **+/Utwórz nową usługę** znajdujący się w lewym górnym rogu witryny Azure Portal.
2. Kliknij kolejno pozycje **Bazy danych** i **Azure Cosmos DB**. Zostanie wyświetlony panel „Nowe konto”.
3. Wprowadź **identyfikator** konta usługi Cosmos DB do późniejszego użycia.  
4. **Interfejs API** należy ustawić na „SQL”. Podejście opisane w tym samouczku można stosować w przypadku innych dostępnych typów interfejsu API, ale kroki opisane w tym samouczku dotyczą interfejsu API SQL.
5. Upewnij się, że **Subskrypcja** i **Grupa zasobów** pasują do wartości określonych podczas tworzenia maszyny wirtualnej w poprzednim kroku.  Wybierz **lokalizację**, w której jest dostępna usługa Cosmos DB.
6. Kliknij przycisk **Utwórz**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Tworzenie kolekcji w ramach konta usługi Cosmos DB

Następnie na koncie usługi Cosmos DB dodaj kolekcję danych, dla której możesz utworzyć zapytania w kolejnych krokach.

1. Przejdź do nowo utworzonego konta usługi Cosmos DB.
2. Na karcie **Omówienie** kliknij przycisk **+/Dodaj kolekcję**. Zostanie wysunięty panel „Dodawanie kolekcji”.
3. Nadaj kolekcji identyfikator bazy danych i identyfikator kolekcji, wybierz pojemność magazynu, wprowadź klucz partycji, wprowadź wartość przepływności, a następnie kliknij przycisk **OK**.  W tym samouczku wystarczy użyć identyfikatora bazy danych i identyfikatora kolekcji „Test”, a następnie wybrać stałą pojemność magazynu i najniższą przepływność (400 RU/s).  

## <a name="grant-windows-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Udzielanie dostępu tożsamości usługi zarządzanej maszyny wirtualnej z systemem Windows do kluczy dostępu do konta usługi Cosmos DB

Usługa Cosmos DB nie zapewnia natywnej obsługi uwierzytelniania usługi Azure AD. Jednak możesz użyć tożsamości usługi zarządzanej, aby pobrać klucz dostępu konta usługi Cosmos DB z usługi Resource Manager, a następnie użyć klucza do uzyskania dostępu do usługi Cosmos DB. W tym kroku udzielasz tożsamości usługi zarządzanej dostępu do kluczy do konta usługi Cosmos DB.

Aby udzielić dostępu tożsamości usługi zarządzanej do konta usługi Cosmos DB w usłudze Azure Resource Manager przy użyciu programu PowerShell, zaktualizuj wartości `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<COSMOS DB ACCOUNT NAME>` dla danego środowiska. Zastąp element `<MSI PRINCIPALID>` właściwością `principalId` zwróconą przez polecenie `az resource show` w części [Pobieranie identyfikatora principalID tożsamości usługi zarządzanej maszyny wirtualnej z systemem Linux](#retrieve-the-principalID-of-the-linux-VM's-MSI).  W przypadku korzystania z kluczy dostępu usługa Cosmos DB obsługuje dwa poziomy szczegółowości: dostęp do odczytu/zapisu na koncie i dostęp tylko do odczytu do konta.  Przypisz rolę `DocumentDB Account Contributor`, jeśli chcesz uzyskać klucze odczytu/zapisu dla konta, lub przypisz rolę `Cosmos DB Account Reader Role`, jeśli chcesz uzyskać klucze tylko do odczytu dla konta:

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-msi-to-call-azure-resource-manager"></a>Uzyskiwanie tokenu dostępu przy użyciu tożsamości maszyny wirtualnej z systemem Windows w celu wywoływania usługi Azure Resource Manager

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej. 

W tej części będzie wymagane użycie poleceń cmdlet programu PowerShell w usłudze Azure Resource Manager.  Jeżeli nie jest on zainstalowany, [pobierz najnowszą wersję](https://docs.microsoft.com/powershell/azure/overview) przed kontynuowaniem.

Ponadto musisz zainstalować najnowszą wersję [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) na maszynie wirtualnej z systemem Windows.

1. W witrynie Azure Portal przejdź do pozycji **Maszyny wirtualne**, przejdź do maszyny wirtualnej z systemem Windows, a następnie na stronie **Przegląd** kliknij opcję **Połącz** u góry. 
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows. 
3. Teraz, po utworzeniu **połączenia pulpitu zdalnego** z maszyną wirtualną, otwórz program PowerShell w sesji zdalnej.
4. Używając polecenia Invoke-WebRequest programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości usługi zarządzanej, aby uzyskać token dostępu na potrzeby usługi Azure Resource Manager.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > Wartość parametru „resource” musi być dokładnie dopasowana do tego, czego oczekujemy od usługi Azure AD. W przypadku użycia identyfikatora zasobu usługi Azure Resource Manager należy uwzględnić końcowy ukośnik w identyfikatorze URI.
    
    Następnie wyodrębnij element„Content”, który jest przechowywany w ciągu w formacie JavaScript Object Notation (JSON) w obiekcie $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Następnie wyodrębnij token dostępu z odpowiedzi.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Uzyskiwanie kluczy dostępu z usługi Azure Resource Manager w celu wykonywania wywołań usługi Cosmos DB

Teraz użyj programu PowerShell, aby wywołać usługę Resource Manager przy użyciu tokenu dostępu pozyskanego w poprzedniej sekcji, aby pobrać klucz dostępu do usługi Cosmos DB. Gdy będziemy już mieć klucz dostępu, będzie można wykonać zapytanie dotyczące usługi Cosmos DB. Pamiętaj, aby zastąpić parametry `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` i `<COSMOS DB ACCOUNT NAME>` własnymi wartościami. Zastąp wartość `<ACCESS TOKEN>` tokenem dostępu pobranym wcześniej.  Jeśli chcesz pobrać klucze odczytu/zapisu, użyj typu operacji klucza `listKeys`.  Jeśli chcesz pobrać klucze tylko do odczytu, użyj typu operacji klucza `readonlykeys`:

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
Odpowiedź zapewnia listę kluczy.  Jeśli pobierasz klucze tylko do odczytu:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Teraz, gdy masz klucz dostępu do konta usługi Cosmos DB, możesz przekazać go do zestawu SDK usługi Cosmos DB i wykonywać wywołania w celu uzyskania dostępu do konta.  Szybki przykład to przekazanie klucza dostępu do wiersza polecenia platformy Azure.  Element <COSMOS DB CONNECTION URL> można uzyskać na karcie **Omówienie** karty w bloku konta usługi Cosmos DB w witrynie Azure Portal.  Zastąp element wartością <ACCESS KEY> uzyskaną powyżej:

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

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości usługi zarządzanej systemu Windows w celu uzyskania dostępu do usługi Cosmos DB.  Aby dowiedzieć się więcej o usłudze Cosmos DB, zobacz:

> [!div class="nextstepaction"]
>[Omówienie usługi Azure Cosmos DB](/azure/cosmos-db/introduction)


