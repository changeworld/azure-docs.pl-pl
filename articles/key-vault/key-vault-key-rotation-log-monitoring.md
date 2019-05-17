---
title: Konfigurowanie usługi Azure Key Vault przy użyciu end-to-end rotacji i inspekcji kluczy | Dokumentacja firmy Microsoft
description: Użyj tego przewodnika instrukcje ułatwiające konfigurowanie rotacji kluczy i Monitoruj dzienniki magazynu kluczy.
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 20a170963ff4a8ff9cb69d3397e66e12c1047d16
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561187"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurowanie usługi Azure Key Vault o rotacji i inspekcji kluczy

## <a name="introduction"></a>Wprowadzenie

Po utworzeniu magazynu kluczy, można uruchomić, używany do przechowywania kluczy i wpisów tajnych. Aplikacje nie są już potrzebne zachować swoje klucze i wpisy tajne, ale zażądać ich z magazynu zgodnie z potrzebami. Magazyn kluczy umożliwia zaktualizowanie klucze i wpisy tajne bez wywierania wpływu na działanie aplikacji, która otwiera szerokie możliwości zarządzania wpisami tajnymi i klucz.

>[!IMPORTANT]
> W przykładach w tym artykule znajdują się tylko w celach ilustracyjnych. Nie są one przeznaczone do użytku produkcyjnego. 

Ten artykuł przeprowadzi:

- Przykład użycia usługi Azure Key Vault do przechowywania klucza tajnego. W tym artykule przechowywany klucz tajny jest klucz konta magazynu platformy Azure, które są używane przez aplikację. 
- Jak zaimplementować zaplanowane obrót tego klucza konta magazynu.
- Jak monitorować klucza magazynu dzienników inspekcji i zgłaszać alerty w przypadku nieoczekiwanych żądań.

> [!NOTE]
> W tym artykule nie opisano szczegółowo początkowej konfiguracji magazynu kluczy. Aby uzyskać te informacje, zobacz [co to jest usługa Azure Key Vault?](key-vault-overview.md). Aby uzyskać instrukcje dotyczące wieloplatformowego interfejsu wiersza polecenia, zobacz [Zarządzanie Key Vault przy użyciu wiersza polecenia platformy Azure](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Konfigurowanie usługi Key Vault

Aby umożliwić aplikacji do pobrania wpisu tajnego z usługi Key Vault, należy najpierw utworzyć wpis tajny i przekaż go do magazynu.

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:

```powershell
Connect-AzAccount
```

W oknie podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem. Pierwsza z nich domyślnie korzysta z programu PowerShell.

Jeśli masz wiele subskrypcji, trzeba będzie określić ten, który został użyty do utworzenia magazynu kluczy. Wprowadź następujące polecenie, aby zobaczyć subskrypcje dla konta:

```powershell
Get-AzSubscription
```

Aby określić subskrypcję, która jest skojarzona z magazynem kluczy, który będziesz rejestrować, wpisz:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Ponieważ w tym artykule przedstawiono przechowywania klucza konta magazynu jako klucz tajny, należy pobrać ten klucz konta magazynu.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po pobraniu klucz tajny (w tym przypadku klucz konta magazynu), należy przekonwertować tego klucza na bezpieczny ciąg i następnie utworzyć wpis tajny z tej wartości w magazynie kluczy.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Następnie uzyskać identyfikator URI klucza tajnego, który został utworzony. Ten identyfikator URI w późniejszym kroku, aby wywołać usługi key vault i pobrać klucz tajny jest konieczne. Uruchom następujące polecenie programu PowerShell i zanotuj wartość Identyfikatora, który jest identyfikatorem URI klucza tajnego:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurowanie aplikacji

Teraz, gdy wpis tajny przechowywane, można użyć kodu, pobrać i użyć go po wykonaniu kilku dodatkowych czynności.

Najpierw należy zarejestrować aplikację w usłudze Azure Active Directory. Następnie poinformuj usługi Key Vault informacje o aplikacji, dzięki czemu umożliwia ona żądań z aplikacji.

> [!NOTE]
> Aplikacja musi zostać utworzona w tej samej dzierżawy usługi Azure Active Directory jako magazynu kluczy.

1. Otwórz **usługi Azure Active Directory**.
2. Wybierz pozycję **Rejestracje aplikacji**. 
3. Wybierz **rejestrowanie nowej aplikacji** Aby dodać aplikację do usługi Azure Active Directory.

    ![Otwieranie aplikacji w usłudze Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. W obszarze **Utwórz**, pozostaw typ aplikacji jako **aplikacji sieci Web / interfejs API** i nadaj aplikacji nazwę. Nadaj aplikacji **adres URL logowania**. Ten adres URL może być coś, czego potrzebujesz dla tej wersji demonstracyjnej.

    ![Utwórz rejestrowanie aplikacji](./media/keyvault-keyrotation/create-app.png)

5. Po dodaniu aplikacji do usługi Azure Active Directory zostanie otwarta strona aplikacji. Wybierz **ustawienia**, a następnie wybierz pozycję **właściwości**. Kopiuj **identyfikator aplikacji** wartość. Będzie potrzebny w kolejnych krokach.

Kolejny krok to generowanie klucza dla aplikacji, dzięki czemu może współdziałać z usługą Azure Active Directory. Aby utworzyć klucz, wybierz **klucze** w obszarze **ustawienia**. Zanotuj klucz nowo wygenerowane w aplikacji usługi Azure Active Directory. Będą one potrzebne w kolejnym kroku. Klucz nie będzie dostępny, gdy opuścisz w tej sekcji. 

![Klucze aplikacji w usłudze Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Przed nawiązaniem wszelkie wywołania z aplikacji do magazynu kluczy, musisz poinformować usługi key vault o aplikacji i ich uprawnienia. Następujące polecenie używa nazwy magazynu i identyfikator aplikacji z poziomu aplikacji usługi Azure Active Directory, aby przyznać aplikacji **uzyskać** dostępu do magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Teraz możesz rozpocząć tworzenie wywołaniami aplikacji. W aplikacji należy zainstalować pakiety NuGet, które są wymagane do interakcji z usługi Azure Key Vault i Azure Active Directory. Z poziomu konsoli Menedżera pakietów Visual Studio wprowadź następujące polecenia. Na zapisywanie w tym artykule bieżącą wersję pakietu usługi Azure Active Directory 3.10.305231913, dlatego upewnij się, najnowszej wersji i update zgodnie z potrzebami.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

W kodzie aplikacji należy utworzyć klasę, aby pomieścić metodę uwierzytelniania usługi Azure Active Directory. W tym przykładzie jest wywoływana tej klasy **Utils**. Dodaj następujący kod `using` instrukcji:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Następnie dodaj następującą metodę do pobierania tokenu JWT z usługi Azure Active Directory. Łatwość utrzymania można przenieść wartości ciągu ustalonych do konfiguracji sieci web lub aplikacji.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Dodać niezbędny kod do wywołania usługi Key Vault i pobrać wartość wpisu tajnego. Najpierw należy dodać następujące `using` instrukcji:

```csharp
using Microsoft.Azure.KeyVault;
```

Dodaj wywołania metody do wywołania usługi Key Vault i pobrać klucz tajny. W przypadku tej metody należy podać klucz tajny identyfikator URI, który został zapisany w poprzednim kroku. Zwróć uwagę na użycie **GetToken** metody z **Utils** klasy, który został wcześniej utworzony.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Po uruchomieniu aplikacji powinno być teraz uwierzytelniania w usłudze Azure Active Directory, a następnie pobieranie wartość wpisu tajnego z usługi Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Wymiana kluczy przy użyciu usługi Azure Automation

> [!IMPORTANT]
> Elementy runbook usługi Azure Automation nadal korzystają z `AzureRM` modułu.

Teraz można przystąpić do konfigurowania strategii obrotu dla wartości, które są przechowywane jako wpisy tajne usługi Key Vault. Wpisy tajne można obracać na kilka sposobów:

- W ramach procesu ręczne
- Programowe, przy użyciu wywołań interfejsu API
- Za pomocą skryptu programu Azure Automation

Na potrzeby tego artykułu będziesz zmienić klucz dostępu konta usługi Azure storage za pomocą programu PowerShell, w połączeniu z usługą Azure Automation. Następnie zaktualizujesz wpisu tajnego usługi key vault za pomocą tego nowego klucza.

Aby umożliwić usłudze Azure Automation do ustawiania wartości klucza tajnego w magazynie kluczy, należy uzyskać identyfikator klienta dla połączenia o nazwie **AzureRunAsConnection**. To połączenie zostało utworzone, gdy ustanowiona swojego wystąpienia usługi Azure Automation. Aby znaleźć ten identyfikator, wybierz **zasoby** z wystąpienia usługi Azure Automation. Z tego miejsca wybierz **połączeń**, a następnie wybierz pozycję **AzureRunAsConnection** nazwy głównej usługi. Zanotuj **ApplicationId** wartość.

![Identyfikator klienta usługi Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

W **zasoby**, wybierz opcję **modułów**. Wybierz **galerii**, a następnie wyszukaj i zaimportuj zaktualizowane wersje każdego z następujących modułów:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na zapisywanie w tym artykule tylko moduły wspomniano wcześniej wymagane do aktualizacji poniższy skrypt. Zadania automatyzacji nie powiedzie się, upewnij się, że zaimportowane wszystkie niezbędne moduły oraz ich zależności.

Po pobraniu już Identyfikatora aplikacji połączenia usługi Azure Automation, musisz poinformować magazynu kluczy, ta aplikacja ma uprawnienia do zaktualizowania wpisów tajnych w magazynie. Użyj następującego polecenia programu PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Następnie wybierz pozycję **elementów Runbook** w ramach wystąpienia usługi Azure Automation, a następnie wybierz **Dodawanie elementu Runbook**. Wybierz pozycję **Szybkie tworzenie**. Nazwa elementu runbook, a następnie wybierz pozycję **PowerShell** jako typ elementu runbook. Można dodać opis. Na koniec wybierz pozycję **Utwórz**.

![Tworzenie elementu runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Wklej poniższy skrypt programu PowerShell, w okienku edytora, aby uzyskać nowy element runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

W okienku Edytora wybierz **okienko testowania** Aby przetestować skrypt. Po uruchomieniu skryptu bez błędów, można wybrać **Publikuj**, a następnie zostanie zastosowana harmonogram dla elementu runbook w okienku Konfiguracja elementu runbook.

## <a name="key-vault-auditing-pipeline"></a>Potok inspekcji usługi Key Vault

Po skonfigurowaniu magazynu kluczy można włączyć inspekcję do zbierania dzienników dla żądań dostępu do magazynu kluczy. Te dzienniki są przechowywane na koncie magazynu platformy Azure w wyznaczonym i mogą być ściągane, monitorowane i analizowane. Poniższy scenariusz używa usługi Azure functions, Azure logic apps i dzienników inspekcji usługi key vault umożliwia tworzenie potoku, który wysyła wiadomość e-mail, gdy aplikacja, która nie pasuje do Identyfikatora aplikacji w aplikacji sieci web pobiera wpisy tajne z magazynu.

Najpierw należy włączyć rejestrowanie w magazynie kluczy. Użyj następujących poleceń programu PowerShell. (Można zobaczyć wszystkie szczegóły w [ten artykuł key vault rejestrowania](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Po włączeniu rejestrowania dzienników inspekcji start, są przechowywane na koncie magazynu wyznaczonego. Te dzienniki zawierają zdarzenia dotyczące sposobu i czasu Twoich magazynów kluczy są dostępne i przez kogo.

> [!NOTE]
> Informacji rejestrowania będzie można się do 10 minut, po zakończeniu operacji magazynu kluczy. Często będzie dostępna wcześniej niż.

Następnym krokiem jest [Tworzenie kolejki usługi Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Kolejka ta jest, gdy są przekazywane dzienniki inspekcji usługi key vault. Komunikaty dziennika inspekcji znajdują się w kolejce, aplikacja logiki wybierze je i działa na nich. Utwórz wystąpienie usługi Service Bus wykonując następujące kroki:

1. Tworzenie przestrzeni nazw usługi Service Bus (Jeśli już masz, którą chcesz używać, przejdź do kroku 2).
2. Przejdź do wystąpienia usługi Service Bus w witrynie Azure portal i wybierz przestrzeń nazw, aby utworzyć kolejkę.
3. Wybierz **Utwórz zasób** > **integracji dla przedsiębiorstw** > **usługi Service Bus**, a następnie wprowadź wymagane szczegóły.
4. Znajdź informacje o połączeniu usługi Service Bus przez wybranie obszaru nazw, a następnie wybierając **informacje o połączeniu**. Te informacje należy do następnej sekcji.

Następnie [utworzyć funkcję platformy Azure](../azure-functions/functions-create-first-azure-function.md) sondowania dzienników usługi key vault w ramach konta magazynu i odebrania nowych zdarzeń. Ta funkcja zostanie wyzwolone zgodnie z harmonogramem.

Aby utworzyć aplikację funkcji platformy Azure, wybierz **Utwórz zasób**, portalu marketplace wyszukaj hasło **aplikacji funkcji**, a następnie wybierz pozycję **Utwórz**. Podczas tworzenia możesz użyć istniejącego planu hostingu lub Utwórz nową. Można również zdecydować się do hostowania dynamicznego. Aby uzyskać więcej informacji na temat opcji hostingu dla usługi Azure Functions, zobacz [jak skalować usługę Azure Functions](../azure-functions/functions-scale.md).

Po utworzeniu aplikacji funkcji platformy Azure, przejdź do niego i wybierz **czasomierza** scenariusza i **C\#**  dla języka. Następnie wybierz pozycję **Utwórz tę funkcję**.

![Blok usługi Azure Functions Start](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na **programowanie** kartę, Zastąp kod run.csx poniższym kodem:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Umożliwia modyfikację zmiennych w poprzednim kodzie, aby wskazywały do swojego konta magazynu, w którym są zapisywane dzienniki magazynu kluczy, wystąpienie usługi Service Bus, która została utworzona wcześniej i określonej ścieżki do dzienników usługi key vault storage.

Funkcja pobiera najnowszy plik dziennika z konta magazynu gdzie są zapisywane dzienniki magazynu kluczy, bierze najnowszych zdarzeń z tego pliku i wypycha je do kolejki usługi Service Bus. 

Ponieważ pojedynczy plik może mieć wiele zdarzeń, należy utworzyć plik sync.txt, funkcja również sposobom, w celu określenia sygnatura czasowa ostatniego zdarzenia, które zostało odebrane. Przy użyciu tego pliku gwarantuje, że nie wypchniesz te same zdarzenia wiele razy. 

Plik sync.txt zawiera sygnaturę czasową dla napotkano ostatniego zdarzenia. Po załadowaniu dzienniki one muszą być sortowane na podstawie ich sygnatur czasowych, aby upewnić się, że są one uporządkowane poprawnie.

Dla tej funkcji odwołujemy się kilka dodatkowych bibliotek, które nie są dostępne gotowe w usłudze Azure Functions. Aby uwzględnić te biblioteki, potrzebujemy wyciągnij je za pomocą pakietu NuGet usługi Azure Functions. W obszarze **kodu** wybierz opcję **Wyświetl pliki**.

![Opcja "Wyświetl pliki"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Dodaj plik o nazwie pliku project.json o następującej zawartości:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Po wybraniu **Zapisz**, usługi Azure Functions pobierze wymagane pliki binarne.

Przełącz się do **integracja** kartę i podać parametr czasomierza znaczącą nazwę, aby użyć wewnątrz funkcji. W poprzednim kodzie funkcja oczekuje czasomierz ma zostać wywołana *myTimer*. Określ [wyrażenie CRON](../app-service/webjobs-create.md#CreateScheduledCRON) dla czasomierza w następujący sposób: `0 * * * * *`. To wyrażenie spowoduje, że funkcja do uruchamiania raz na minutę.

W tym samym **integracja** kartę, Dodaj wejście typ **usługi Azure Blob storage**. Te dane wejściowe będzie wskazywać plik sync.txt, który zawiera sygnatura czasowa ostatniego zdarzenia przyjrzano się przez funkcję. Te dane wejściowe będzie można uzyskiwać dostępu do funkcji, używając nazwy parametru. W poprzednim kodzie dane wejściowe z magazynu obiektów Blob platformy Azure oczekuje, że nazwa parametru, który ma zostać *inputBlob*. Wybierz konto magazynu, w której zostaną umieszczone w pliku sync.txt (może być taka sama lub innego konta magazynu). W polu Ścieżka podaj ścieżkę do pliku w formacie `{container-name}/path/to/sync.txt`.

Dodaj dane wyjściowe tego typu **usługi Azure Blob storage**. Te dane wyjściowe będą wskazywać plik sync.txt, zdefiniowanych w danych wejściowych. Te dane wyjściowe używany przez funkcję do zapisywania sygnatura czasowa ostatniego zdarzenia analizujemy. Powyższy kod oczekuje, że ten parametr można wywołać *Blob_wyj*.

Funkcja jest teraz gotowy. Upewnij się wrócić do **programowanie** kartę i zapisać kod. Sprawdź okno danych wyjściowych jakichkolwiek błędów kompilacji i popraw je stosownie do potrzeb. Jeśli kod zostanie skompilowany, następnie kod powinien teraz być sprawdzanie dzienników usługi key vault co minutę i wypychanie wszystkie nowe zdarzenia do zdefiniowanych kolejki usługi Service Bus. Informacje o rejestrowaniu zapisać w oknie dziennika za każdym razem, gdy funkcja jest wyzwalana, powinny być widoczne.

### <a name="azure-logic-app"></a>Aplikacji logiki platformy Azure

Następnie należy utworzyć aplikację logiki platformy Azure, która zbiera zdarzenia, że funkcja jest Wypychanie do kolejki usługi Service Bus, analizuje zawartości i wysyła wiadomość e-mail na podstawie warunku są dopasowywane.

[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) , wybierając **Utwórz zasób** > **integracji** > **aplikacji logiki**.

Po utworzeniu aplikacji logiki, przejdź do niego i wybierz pozycję **Edytuj**. W edytorze aplikacji logiki wybierz pozycję **kolejki usługi Service Bus** i wprowadź swoje poświadczenia usługi Service Bus, aby połączyć go z kolejki.

![Usługi Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Wybierz **Dodaj warunek**. W warunku przejdź do zaawansowanego edytora, a następnie wprowadź poniższy kod. Zastąp *APP_ID* identyfikatorem rzeczywistych aplikacji w aplikacji sieci web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

To wyrażenie zasadniczo zwraca **false** Jeśli *appid* ze zdarzenia przychodzące, (czyli treści wiadomości usługi Service Bus) nie jest *appid* aplikacji.

Teraz Utwórz akcję w obszarze **Jeśli nie, nic nie RÓB**.

![Usługa Azure Logic Apps wybierz akcję](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

W przypadku akcji wybierz **usługi Office 365 — Wyślij wiadomość e-mail**. Wypełnij pola, aby utworzyć wiadomość e-mail do wysłania, gdy zwracanych przez warunek zdefiniowanych **false**. Jeśli nie masz usługi Office 365, Wyszukaj alternatywy osiągnąć takie same wyniki.

Masz teraz potok end-to-end, który wyszukuje nowe dzienniki inspekcji usługi key vault raz na minutę. Umieszcza ono nowe dzienniki, które znajdzie do kolejki usługi Service Bus. Aplikacja logiki jest wyzwalana, gdy nowa wiadomość znajdzie się w kolejce. Jeśli *appid* w ramach zdarzenia nie pasuje do Identyfikatora aplikacji w aplikacji wywołującej, przepływ wysyła wiadomość e-mail.
