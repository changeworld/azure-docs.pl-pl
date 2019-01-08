---
title: Konfigurowanie usługi Azure Key Vault z rotacji kluczy end-to-end i przeprowadzanie inspekcji — usługa Azure Key Vault | Dokumentacja firmy Microsoft
description: Niniejszy instruktaż umożliwia pomocy można skonfigurować za pomocą monitorowania dzienniki magazynu kluczy i wymiany kluczy.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 4dbfd993a8464c569d30f11e305d4bae000a778f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077712"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurowanie usługi Azure Key Vault o rotacji i inspekcji kluczy

## <a name="introduction"></a>Wprowadzenie

Po utworzeniu magazynu kluczy, można uruchomić, używany do przechowywania kluczy i wpisów tajnych. Aplikacje nie są już potrzebne zachować swoje klucze i wpisy tajne, ale zażądać ich z magazynu zgodnie z potrzebami. Dzięki temu można zaktualizować kluczy i wpisów tajnych, bez wywierania wpływu na działanie aplikacji, która otwiera szerokie możliwości dotyczące Twojego klucza i zarządzanie wpisami tajnymi.

>[!IMPORTANT]
> W przykładach w tym artykule znajdują się tylko w celach ilustracyjnych. Nie są przeznaczone do użytku produkcyjnego. 

Ten artykuł przeprowadzi:

- Przykład użycia usługi Azure Key Vault do przechowywania klucza tajnego. W tym samouczku przechowywany klucz tajny jest klucz konta usługi Azure Storage, uzyskiwał dostęp do aplikacji. 
- Ilustruje też implementacji zaplanowane obrotu tego klucza konta magazynu.
- Pokazuje sposób monitorowania dzienników inspekcji usługi key vault i zgłaszać alerty w przypadku nieoczekiwanych żądań.

> [!NOTE]
> W tym samouczku nie jest przeznaczona do szczegółowo wyjaśniono wszystkie początkowej konfiguracji magazynu kluczy. Te informacje można znaleźć w temacie [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md). Aby uzyskać instrukcje dotyczące Wieloplatformowego interfejsu wiersza polecenia, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Konfigurowanie usługi Key Vault

Aby umożliwić aplikacji do pobrania wpisu tajnego z usługi Key Vault, należy najpierw utworzyć wpis tajny i przekaż go do magazynu. Można to zrobić, uruchamiając sesję programu Azure PowerShell i logowania do konta platformy Azure za pomocą następującego polecenia:

```powershell
Connect-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem. Pierwsza z nich domyślnie korzysta z programu PowerShell.

Jeśli masz wiele subskrypcji, trzeba będzie określić ten, który został użyty do utworzenia magazynu kluczy. Wprowadź następujące polecenie, aby zobaczyć subskrypcje dla konta:

```powershell
Get-AzureRmSubscription
```

Aby określić subskrypcję, która jest skojarzona z magazynem kluczy, który będziesz rejestrować, wpisz:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Ponieważ w tym artykule przedstawiono przechowywania klucza konta magazynu jako klucz tajny, należy pobrać ten klucz konta magazynu.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po pobraniu klucz tajny (w tym przypadku klucz konta magazynu), należy przekonwertować, bezpieczny ciąg i następnie utworzyć wpis tajny z tej wartości w magazynie kluczy.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Następnie uzyskać identyfikator URI klucza tajnego, który został utworzony. Służy to w późniejszym kroku podczas wywoływania magazynu kluczy, aby pobrać klucz tajny. Uruchom następujące polecenie programu PowerShell i zanotuj wartość Identyfikatora, czyli identyfikator URI klucza tajnego:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurowanie aplikacji

Teraz, gdy wpis tajny przechowywane, można użyć kodu, można pobrać i używać go. Istnieje kilka kroków wymaganych do osiągnięcia tego. Pierwszy i Najważniejszy krok jest rejestrowanie aplikacji w usłudze Azure Active Directory i następnie informuje usługi Key Vault informacje o aplikacji, dzięki czemu umożliwia żądania od aplikacji.

> [!NOTE]
> Aplikacja musi zostać utworzona w tej samej dzierżawy usługi Azure Active Directory jako magazynu kluczy.
>
>

1. Przejdź do usługi Azure Active Directory.
2. Wybierz **rejestracje aplikacji** 
3. Wybierz **rejestrowanie nowej aplikacji** Aby dodać aplikację do usługi Azure Active Directory.

    ![Otwieranie aplikacji w usłudze Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. W **Utwórz** sekcji pozostawić typ aplikacji jako **interfejsu API sieci WEB i/lub usługi sieci WEB APPLICATION** i nadaj aplikacji nazwę. Nadaj aplikacji **adres URL logowania**. Może to być dowolna, które mają dla tej wersji demonstracyjnej.

    ![Utwórz rejestrowanie aplikacji](./media/keyvault-keyrotation/create-app.png)

5. Po dodaniu aplikacji do usługi Azure Active Directory, zostanie wyświetlona na stronie aplikacji. Wybierz **ustawienia** i wybierz polecenie Właściwości. Kopiuj **identyfikator aplikacji** wartość. Będzie ona potrzebna w kolejnych krokach.

Kolejny krok to generowanie klucza dla aplikacji, dzięki czemu może współdziałać z usługi Azure Active Directory. Można utworzyć klucza pod, przechodząc do **klucze** sekcji **ustawienia**. Zanotuj klucz nowo wygenerowane z poziomu aplikacji usługi Azure Active Directory do użycia na późniejszym etapie. Należy zauważyć, że klucz nie będą dostępne po przejściu z tej sekcji. 

![Klucze aplikacji usługi Azure Active Directory](./media/keyvault-keyrotation/create-key.png)

Przed nawiązaniem wszelkie wywołania z aplikacji do magazynu kluczy, musisz poinformować usługi key vault o aplikacji i ich uprawnienia. Następujące polecenie przyjmuje nazwę magazynu i identyfikator aplikacji z poziomu aplikacji usługi Azure Active Directory i przyznaje **uzyskać** dostępu do magazynu kluczy dla aplikacji.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

W tym momencie możesz gotowe do rozpoczęcia tworzenia wywołaniami aplikacji. W aplikacji należy zainstalować pakiety NuGet, musieli korzystać z usługi Azure Key Vault i Azure Active Directory. Z poziomu konsoli Menedżera pakietów Visual Studio wprowadź następujące polecenia. Na zapisywanie w tym artykule bieżącą wersję pakietu usługi Azure Active Directory jest 3.10.305231913, dzięki czemu możesz chcieć sprawdzić najnowsza wersja, a następnie odpowiednio zaktualizować.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

W kodzie aplikacji należy utworzyć klasę, aby pomieścić metodę uwierzytelniania usługi Azure Active Directory. W tym przykładzie jest wywoływana tej klasy **Utils**. Dodaj następującą instrukcję using:

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

Dodać niezbędny kod do wywołania usługi Key Vault i pobrać wartość wpisu tajnego. Najpierw należy dodać następujące instrukcję using:

```csharp
using Microsoft.Azure.KeyVault;
```

Dodaj wywołania metody do wywołania usługi Key Vault i pobrać klucz tajny. W przypadku tej metody należy podać klucz tajny identyfikator URI, który został zapisany w poprzednim kroku. Zwróć uwagę na użycie **GetToken** metody z **Utils** klasy został wcześniej utworzony.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Po uruchomieniu aplikacji powinno być teraz uwierzytelniania w usłudze Azure Active Directory, a następnie pobieranie wartość wpisu tajnego z usługi Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Wymiana kluczy przy użyciu usługi Azure Automation

Istnieją różne opcje dotyczące implementowania strategii obrotu dla wartości, które są przechowywane jako wpisy tajne w usłudze Azure Key Vault. Wpisy tajne można obracać jako część ręczny proces, ich może zostać obrócona programowo przy użyciu wywołań interfejsu API lub może zostać obrócona za pomocą skryptów automatyzacji. Na potrzeby tego artykułu jest będzie używać programu Azure PowerShell w połączeniu z usługą Azure Automation można zmienić klucza dostępu konta usługi Azure Storage. Następnie zaktualizuje wpisu tajnego usługi key vault za pomocą tego nowego klucza.

Aby umożliwić usłudze Azure Automation do ustawiania wartości klucza tajnego w magazynie kluczy, należy uzyskać identyfikator klienta dla połączenia o nazwie AzureRunAsConnection, który został utworzony, gdy ustanowiona swojego wystąpienia usługi Azure Automation. Ten identyfikator można znaleźć, wybierając **zasoby** z wystąpienia usługi Azure Automation. Z tego miejsca możesz wybrać **połączeń** , a następnie wybierz **AzureRunAsConnection** usługi zasady. Zwróć uwagę na **identyfikator aplikacji**.

![Identyfikator klienta usługi Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

W **zasoby**, wybierz **modułów**. Z **modułów**, wybierz opcję **galerii**, a następnie wyszukaj i **importu** zaktualizowane wersje każdego z następujących modułów:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Na zapisywanie w tym artykule tylko moduły wspomniano wcześniej wymagane do aktualizacji poniższy skrypt. Jeśli okaże się, że zadania automatyzacji kończy się niepowodzeniem, upewnij się, że został zaimportowany wszystkie niezbędne moduły oraz ich zależności.
>
>

Po pobraniu Identyfikatora aplikacji połączenia usługi Azure Automation, musisz poinformować magazynu kluczy czy ta aplikacja ma dostęp do zaktualizowania wpisów tajnych w magazynie. Można to zrobić za pomocą następującego polecenia programu PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Następnie wybierz pozycję **elementów Runbook** w ramach wystąpienia usługi Azure Automation, a następnie wybierz **Dodaj element Runbook**. Wybierz pozycję **Szybkie tworzenie**. Nazwa elementu runbook, a następnie wybierz pozycję **PowerShell** jako typ elementu runbook. Masz opcję, aby dodać opis. Na koniec kliknij **Utwórz**.

![Tworzenie elementu runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Wklej poniższy skrypt programu PowerShell, w okienku edytora, aby uzyskać nowy element runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
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

#Optionally you may set the following as parameters
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

W okienku Edytora wybierz **okienko testowania** Aby przetestować skrypt. Gdy skrypt jest uruchamiany bez błędów, można wybrać **Publikuj**, a następnie zostanie zastosowana harmonogram dla elementu runbook w okienku Konfiguracja elementu runbook.

## <a name="key-vault-auditing-pipeline"></a>Potok inspekcji usługi Key Vault
Po skonfigurowaniu magazynu kluczy można włączyć inspekcję do zbierania dzienników dla żądań dostępu do magazynu kluczy. Te dzienniki są przechowywane w wyznaczonego konta usługi Azure Storage i mogą być ściągane, monitorowane i analizowane. Poniższy scenariusz używa usługi Azure functions, Azure logic apps i dzienników inspekcji usługi key vault umożliwia tworzenie potoku, aby wysłać wiadomość e-mail, gdy aplikacja, która jest zgodna z Identyfikatorem aplikacji w aplikacji sieci web pobiera wpisy tajne z magazynu.

Najpierw należy włączyć rejestrowanie w magazynie kluczy. Można to zrobić za pomocą następujących poleceń programu PowerShell (szczegółowe informacje są widoczne w [key vault rejestrowania](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Po włączeniu to rozpoczęcia zbierania do konta magazynu wyznaczonego dzienników inspekcji. Te dzienniki zawierają zdarzenia dotyczące sposobu i czasu Twoich magazynów kluczy są dostępne i przez kogo.

> [!NOTE]
> Informacji rejestrowania będzie można się do 10 minut, po zakończeniu operacji magazynu kluczy. Zwykle jest krótszy.
>
>

Następnym krokiem jest [Tworzenie kolejki usługi Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Jest to, gdzie są wypychane dzienników inspekcji usługi key vault. Komunikaty dziennika inspekcji znajdują się w kolejce, aplikacja logiki wybierze je i działa na nich. Tworzenie usługi Service bus wykonując następujące kroki:

1. Tworzenie przestrzeni nazw usługi Service Bus (Jeśli masz już takie, którego chcesz użyć dla tego, przejdź do kroku 2).
2. Przejdź do usługi service bus w witrynie Azure portal, a następnie wybierz przestrzeń nazw, aby utworzyć kolejkę.
3. Wybierz **Utwórz zasób**, **integracji dla przedsiębiorstw**, **usługi Service Bus**, a następnie wprowadź wymagane szczegóły.
4. Wybierz informacje o połączeniu usługi Service Bus, wybierając przestrzeni nazw, a następnie klikając polecenie **informacje o połączeniu**. Ta informacja będzie potrzebna do następnej sekcji.

Następnie [utworzyć funkcję platformy Azure](../azure-functions/functions-create-first-azure-function.md) sondowania dzienników usługi key vault w ramach konta magazynu i odebrania nowych zdarzeń. Będzie to funkcja, która jest wyzwalana zgodnie z harmonogramem.

Aby utworzyć funkcję platformy Azure, wybierz **Utwórz zasób**, portalu marketplace wyszukaj hasło _aplikacji funkcji_i kliknij przycisk **Utwórz**. Podczas tworzenia możesz użyć istniejącego planu hostingu lub Utwórz nową. Można również zdecydować się do hostowania dynamicznego. Szczegółowe informacje na temat opcji hostingu funkcji znajduje się w temacie [jak skalować usługę Azure Functions](../azure-functions/functions-scale.md).

Podczas tworzenia funkcji platformy Azure, przejdź do niego i wybierz pozycję czasomierz, funkcji i C\#. Następnie kliknij przycisk **Utwórz tę funkcję**.

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

            //required to order by time as they may not be in the file
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
> Upewnij się zastąpić zmiennych w poprzednim kodzie, aby wskazywały do swojego konta magazynu, gdzie są zapisywane dzienniki magazynu kluczy, usługi service bus, która została utworzona wcześniej i określonej ścieżki do dzienników magazynu kluczy.
>
>

Funkcja pobiera najnowszy plik dziennika z konta magazynu gdzie są zapisywane dzienniki magazynu kluczy, bierze najnowszych zdarzeń z tego pliku i wypycha je do kolejki usługi Service Bus. Ponieważ pojedynczy plik może mieć wiele zdarzeń, należy utworzyć plik sync.txt, funkcja również sposobom, w celu określenia sygnatura czasowa ostatniego zdarzenia, które zostało odebrane. Daje to gwarancję, że nie wypchniesz te same zdarzenia wiele razy. Ten plik sync.txt zawiera sygnaturę czasową dla ostatniego zdarzenia wystąpił. Dzienniki, gdy ładowany, muszą być sortowane w oparciu o sygnaturę czasową, aby upewnić się, że są one uporządkowane poprawnie.

Dla tej funkcji odwołujemy się kilka dodatkowych bibliotek, które nie są dostępne gotowe w usłudze Azure Functions. Aby je uwzględnić, musimy usługi Azure Functions, aby ściągnąć je za pomocą narzędzia NuGet. Wybierz **Wyświetl pliki** opcji.

![Opcja Widok do plików](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

I Dodaj plik o nazwie pliku project.json o następującej zawartości:

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

Po **Zapisz**, usługi Azure Functions pobierze wymagane pliki binarne.

Przełącz się do **integracja** kartę i podać parametr czasomierza znaczącą nazwę, aby użyć wewnątrz funkcji. W poprzednim kodzie oczekuje, że Czasomierz ma zostać wywołana *myTimer*. Określ [wyrażenie CRON](../app-service/webjobs-create.md#CreateScheduledCRON) w następujący sposób: 0 \* \* \* \* \* dla czasomierza, który spowoduje, że funkcja do uruchamiania raz na minutę.

W tym samym **integracja** kartę, Dodaj wejście typ **usługi Azure Blob Storage**. Będzie to wskazywać plik sync.txt, który zawiera sygnatura czasowa ostatniego zdarzenia przyjrzano się przez funkcję. Będzie dostępna w ramach funkcji, nazwy parametru. W poprzednim kodzie danych wejściowych usługi Azure Blob Storage oczekuje, że nazwa parametru, który ma zostać *inputBlob*. Wybierz konto magazynu, w której będą znajdować się plik sync.txt (może być taka sama lub innego konta magazynu). W polu Ścieżka podaj ścieżkę lokalizacji pliku w formacie {container-name}/path/to/sync.txt.

Dodaj dane wyjściowe tego typu *usługi Azure Blob Storage* danych wyjściowych. Będzie to wskazywać plik sync.txt, które zostały zdefiniowane w danych wejściowych. Za pomocą funkcji służy do zapisywania sygnatura czasowa ostatniego zdarzenia przyjrzano się. Powyższy kod oczekuje, że ten parametr można wywołać *Blob_wyj*.

W tym momencie funkcja jest gotowa. Upewnij się wrócić do **programowanie** kartę i zapisać kod. Sprawdź okno danych wyjściowych jakichkolwiek błędów kompilacji i odpowiednio je poprawić. Jeśli kod zostanie skompilowany, następnie kod powinien teraz być sprawdzanie dzienników usługi key vault co minutę i wypychanie wszystkie nowe zdarzenia do zdefiniowanych kolejki usługi Service Bus. Informacje o rejestrowaniu zapisać w oknie dziennika za każdym razem, gdy funkcja jest wyzwalana, powinny być widoczne.

### <a name="azure-logic-app"></a>Aplikacji logiki platformy Azure

Następnie należy utworzyć aplikację logiki platformy Azure, która zbiera zdarzenia, że funkcja jest Wypychanie do kolejki usługi Service Bus, analizuje zawartości i wysyła wiadomość e-mail na podstawie warunku są dopasowywane.

[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) , przechodząc do **nowy > Aplikacja logiki**.

Po utworzeniu aplikacji logiki, przejdź do niego i wybierz pozycję **Edytuj**. W edytorze aplikacji logiki wybierz pozycję **kolejki usługi Service Bus** i wprowadź swoje poświadczenia usługi Service Bus, aby połączyć go z kolejki.

![Usługi Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Następnie wybierz **Dodaj warunek**. W tym stanie przejdź do zaawansowanego edytora, a następnie wprowadź następujący kod, zastępując APP_ID rzeczywiste APP_ID aplikacji sieci web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

To wyrażenie zasadniczo zwraca **false** Jeśli *appid* ze zdarzenia przychodzące, (czyli treści wiadomości usługi Service Bus) nie jest *appid* aplikacji.

Teraz Utwórz akcję w obszarze **Jeśli nie, nic nie rób**.

![Aplikacja logiki platformy Azure wybierz akcję](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

W przypadku akcji wybierz **usługi Office 365 — Wyślij wiadomość e-mail**. Wypełnij pola, aby utworzyć wiadomość e-mail do wysłania, gdy zwracanych przez warunek zdefiniowanych **false**. Jeśli nie masz usługi Office 365, może przyjrzyj rozwiązań alternatywnych, aby osiągnąć takie same wyniki.

W tym momencie masz potok końcowy, który wyszukuje nowe dzienniki inspekcji usługi key vault raz na minutę. Umieszcza ono nowe dzienniki, które znajdzie do kolejki usługi service bus. Aplikacja logiki jest wyzwalana, gdy nowa wiadomość znajdzie się w kolejce. Jeśli *appid* w ramach zdarzenia jest niezgodny z Identyfikatorem aplikacji w aplikacji wywołującej, przepływ wysyła wiadomość e-mail.
