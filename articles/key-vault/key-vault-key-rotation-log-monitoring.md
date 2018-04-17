---
title: Konfigurowanie usługi Azure Key Vault z rotacją kluczy end-to-end i inspekcji | Dokumentacja firmy Microsoft
description: Użyj tego instrukcje, aby pomóc Ci skonfigurować z rotacją kluczy i monitorowania magazynu kluczy dzienniki.
services: key-vault
documentationcenter: ''
author: swgriffith
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: stgriffi
ms.openlocfilehash: 701edc784dcde067e8ec585a3b31d6075207fbbf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Konfigurowanie kompleksowej wymiany i inspekcji kluczy w usłudze Azure Key Vault
## <a name="introduction"></a>Wprowadzenie
Po utworzeniu magazynu kluczy, można uruchomić za pomocą tego magazynu do przechowywania kluczy i kluczy tajnych. Aplikacji nie jest już należy do utrwalenia z kluczy lub kluczy tajnych, ale raczej zażąda je z magazynu kluczy zgodnie z potrzebami. Dzięki temu można zaktualizować kluczy i kluczy tajnych bez wpływu na zachowanie aplikacji, która otwiera szerokości możliwości wokół klucza, a tajny zarządzania.

>[!IMPORTANT]
> Przykłady w tym artykule znajdują się wyłącznie dla celów ilustracyjnych. Nie są przeznaczone do użycia w środowisku produkcyjnym. 

W tym artykule przedstawiono przykład użycia usługi Azure Key Vault do przechowywania klucza tajnego, w tym przypadku klucz konta magazynu Azure, który jest dostępny przez aplikację. Ilustruje też implementacji zaplanowane obrotu tego klucza konta magazynu. Na koniec przeprowadzi przez pokaz do monitorowania dzienników inspekcji magazynu kluczy i zgłaszanie alertów po nieoczekiwany żądań.

> [!NOTE]
> W tym samouczku nie jest przeznaczona do opisano szczegółowo początkowej konfiguracji magazynu kluczy. Te informacje można znaleźć w temacie [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md). Aby uzyskać instrukcje Międzyplatformowego interfejsu wiersza polecenia, zobacz [Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Konfigurowanie usługi Key Vault
Aby umożliwić aplikacji można pobrać klucza tajnego z magazynu kluczy, należy najpierw utworzyć klucz tajny i przekaż go do magazynu. Można to zrobić przez uruchamianie sesji programu PowerShell systemu Azure i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

```powershell
Login-AzureRmAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem. Pierwsza z nich domyślnie korzysta z programu PowerShell.

Jeśli masz wiele subskrypcji może być konieczne określenie ten, który został użyty do utworzenia magazynu kluczy. Wprowadź następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```powershell
Get-AzureRmSubscription
```

Aby określić subskrypcję skojarzoną z magazynem kluczy, który będziesz rejestrować, wpisz:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Ponieważ w tym artykule przedstawiono przechowywania jako klucz tajny klucz konta magazynu, należy pobrać ten klucz konta magazynu.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po pobraniu klucz tajny (w tym przypadku klucz konta magazynu), należy przekonwertować który bezpieczny ciąg, a następnie utwórz klucz tajny z daną wartością, w magazynie kluczy.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Następnie należy uzyskać identyfikator URI klucz tajny, który został utworzony. Służy to w kolejnym kroku podczas wywoływania magazynu kluczy, aby pobrać klucz tajny. Uruchom następujące polecenie programu PowerShell i zanotuj wartość Identyfikatora jest tajny identyfikatora URI:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurowanie aplikacji
Teraz, po klucz tajny przechowywane, można użyć kodu pobrać i użyć go. Istnieje kilka czynności, aby to osiągnąć. Pierwszy i Najważniejszy krok rejestrowanie aplikacji w usłudze Azure Active Directory, a następnie informuje usługi Key Vault informacje o aplikacji, dzięki czemu można zezwolić żądań od aplikacji.

> [!NOTE]
> Aplikację należy utworzyć w tej samej dzierżawy usługi Azure Active Directory jako magazynu kluczy.
>
>

Otwórz kartę aplikacji usługi Azure Active Directory.

![Otwarte aplikacje w usłudze Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Wybierz **Dodaj** można dodać aplikację do usługi Azure Active Directory.

![Wybierz przycisk Dodaj](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Pozostaw typ aplikacji jako **interfejsu API sieci WEB i/lub aplikacji sieci WEB** i nadaj nazwę aplikacji.

![Nazwa aplikacji](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Nadaj aplikacji **adres URL logowania** i **identyfikator URI aplikacji**. Mogą to być dowolnych znaków dla tego pokazu i mogą zostać zmienione później w razie potrzeby.

![Podaj wymagane identyfikatorów URI](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Po dodaniu aplikacji do usługi Azure Active Directory, zostanie wyświetlona na stronie aplikacji. Kliknij przycisk **Konfiguruj** karcie, a następnie znajdź i skopiuj **identyfikator klienta** wartość. Zanotuj identyfikator klienta dla kolejnych krokach.

Następnie wygeneruj klucz dla aplikacji, może współdziałać z usługą Azure Active Directory. Można go utworzyć w obszarze **klucze** sekcji **konfiguracji** kartę. Zanotuj klucz wygenerowanym aplikacji usługi Azure Active Directory do użycia w kolejnym kroku.

![Klucze aplikacji usługi Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Zanim zostanie nawiązana wszystkie wywołania z aplikacji do magazynu kluczy, należy Poinformuj magazynu kluczy o aplikacji i jej uprawnienia. Poniższe polecenie pobiera nazwę magazynu i identyfikator klienta z aplikacji Azure Active Directory i przyznaje **uzyskać** dostępu do magazynu kluczy dla aplikacji.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

W tym momencie można przystąpić do rozpocząć tworzenie połączeń aplikacji. W aplikacji należy zainstalować pakiety NuGet wymagane do interakcji z usługą Azure Key Vault i Azure Active Directory. Za pomocą konsoli Menedżera pakietów programu Visual Studio wprowadź następujące polecenia. Na zapisywanie w tym artykule bieżącej wersji pakietu usługi Azure Active Directory jest 3.10.305231913, dlatego warto Potwierdź najnowszą wersję, a następnie odpowiednio zaktualizować.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

W kodzie aplikacji Utwórz klasę do przechowywania metodę uwierzytelniania usługi Azure Active Directory. W tym przykładzie tej klasy, nosi **witryny**. Dodaj następującą instrukcję using:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Następnie dodaj następującą metodę do pobrania tokenu JWT z usługi Azure Active Directory. Łatwość utrzymania można przenieść ciągi ustalony do konfiguracji sieci web lub aplikacji.

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

Dodaj kod niezbędne, aby wywołać magazyn kluczy i pobrać poufną wartość. Należy najpierw dodaj następującą instrukcję using:

```csharp
using Microsoft.Azure.KeyVault;
```

Dodaj wywołania metody do wywołania magazyn kluczy i pobrać klucz tajny. W przypadku tej metody musisz podać klucz tajny identyfikator URI, który został zapisany w poprzednim kroku. Zwróć uwagę na użycie **GetToken** metody z **witryny** klasy utworzone wcześniej.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Po uruchomieniu aplikacji powinno być teraz uwierzytelniania w usłudze Azure Active Directory, a następnie pobierając poufną wartość z usługi Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Obracanie klucza przy użyciu usługi Automatyzacja Azure
Istnieją różne opcje wdrażania strategii obrotu wartości, które są przechowywane jako klucze tajne usługi Azure Key Vault. Klucze tajne można obracać jako część ręczny proces, ich może zostać obrócona programowo przy użyciu interfejsu API lub może zostać obrócona i skryptów automatyzacji. Na potrzeby tego artykułu jest będzie używać programu Azure PowerShell połączone z usługi Automatyzacja Azure można zmienić klucza dostępu konta magazynu Azure. Następnie zaktualizuje klucza tajnego klucza magazynu z tego nowego klucza.

Aby umożliwić Automatyzacja Azure, aby ustawić wartości tajnego w magazynie kluczy, należy uzyskać identyfikator klienta dla połączenia o nazwie AzureRunAsConnection, który został utworzony podczas ustanawiania wystąpienia usługi Automatyzacja Azure. Ten identyfikator można znaleźć, wybierając **zasoby** z wystąpieniem usługi Automatyzacja Azure. Z tego miejsca, możesz wybrać **połączeń** , a następnie wybierz **AzureRunAsConnection** usługi zasady. Zwróć uwagę na **identyfikator aplikacji**.

![Identyfikator klienta usługi Automatyzacja Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

W **zasoby**, wybierz **modułów**. Z **modułów**, wybierz pozycję **galerii**, a następnie wyszukaj i **importu** zaktualizowane wersje każdego z następujących modułów:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Na zapisywanie w tym artykule wcześniej dostrzeżone modułów potrzebne do aktualizacji poniższy skrypt. Jeśli okaże się, że zadanie usługi Automatyzacja nie działa prawidłowo, upewnij się, że został zaimportowany wszystkie niezbędne moduły oraz ich zależności.
>
>

Po pobraniu Identyfikatora aplikacji dla połączenia usługi Automatyzacja Azure, należy wskazać magazynu kluczy czy tę aplikację ma dostęp do aktualizacji kluczy tajnych w magazynie. Można to zrobić za pomocą następującego polecenia programu PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Następnie wybierz pozycję **Runbook** w wystąpieniu usługi Automatyzacja Azure, a następnie wybierz **Dodaj element Runbook**. Wybierz pozycję **Szybkie tworzenie**. Nazwa elementu runbook i wybierz **PowerShell** jako typ elementu runbook. Masz opcję, aby dodać opis. Na koniec kliknij **Utwórz**.

![Tworzenie elementu runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Wklej poniższy skrypt programu PowerShell w okienku edytora dla nowy element runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
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

W okienku Edytora wybrać **okienku testu** Aby przetestować skrypt. Gdy skrypt jest uruchamiany bez błędów, można wybrać **publikowania**, a następnie można zastosować harmonogram dla elementu runbook w okienku konfiguracji elementu runbook.

## <a name="key-vault-auditing-pipeline"></a>Potok inspekcji usługi Key Vault
Po skonfigurowaniu magazynu kluczy można włączyć inspekcję do zbierania dzienników na żądania dostępu do magazynu kluczy. Te dzienniki są przechowywane w wyznaczonym konta magazynu Azure i można ściągnąć, monitorowania i analizy. Poniższy scenariusz używa usługę Azure functions aplikacje logiki platformy Azure i dzienniki inspekcji magazynu kluczy do utworzenia potoku, aby wysłać wiadomość e-mail, gdy aplikacja, która jest zgodny z Identyfikatorem aplikacji w aplikacji sieci web pobiera kluczy tajnych z magazynu.

Najpierw należy włączyć rejestrowanie w magazynie kluczy. Można to zrobić za pomocą następujących poleceń programu PowerShell (szczegółowe informacje są widoczne w [klucza magazynu rejestrowania](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Po włączeniu to rozpoczęcia zbierania do konta magazynu wyznaczonego dzienniki inspekcji. Te dzienniki zawiera zdarzenia o jak i kiedy są dostępne z magazynów kluczy i przez kogo.

> [!NOTE]
> Dostępne informacji rejestrowania 10 minut po wykonaniu operacji magazynu kluczy. Zwykle jest krótszy.
>
>

Następnym krokiem jest [Utwórz kolejkę Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Jest to, gdzie są usuwane, dzienniki inspekcji magazynu kluczy. Gdy komunikaty dziennika inspekcji znajdują się w kolejce, aplikację logiki przejmuje je i działa na nich. Tworzenie usługi service bus z następujących kroków:

1. Tworzenie przestrzeni nazw usługi Service Bus (Jeśli masz już konto, którego chcesz użyć dla tego, przejdź do kroku 2).
2. Przejdź do usługi service bus w portalu Azure i wybierz obszar nazw, aby utworzyć kolejkę w.
3. Wybierz **Utwórz zasób**, **integracji przedsiębiorstwa**, **usługi Service Bus**, a następnie wprowadź wymagane szczegóły.
4. Wybierz informacje o połączeniu usługi Service Bus, wybierając obszar nazw, a następnie klikając polecenie **informacje o połączeniu**. Informacje te będą potrzebne do następnej sekcji.

Następnie [Tworzenie funkcji platformy Azure](../azure-functions/functions-create-first-azure-function.md) sondowania dzienniki magazynu kluczy w ramach konta magazynu i odebrania nowych zdarzeń. Jest to funkcja, która zostanie wywołany zgodnie z harmonogramem.

Aby utworzyć funkcję platformy Azure, wybierz **Utwórz zasób**, wyszukiwania witryny marketplace w celu _aplikacji funkcji_i kliknij przycisk **Utwórz**. Podczas tworzenia można użyć istniejącego planu obsługi lub Utwórz nową. Można również wybrać do obsługi dynamicznej. Więcej informacji na temat funkcji hosting opcje można znaleźć w folderze [jak skalować usługę Azure Functions](../azure-functions/functions-scale.md).

Po utworzeniu funkcji platformy Azure, przejdź do niego i wybierz polecenie czasomierz, funkcji i C\#. Następnie kliknij przycisk **tworzenia tej funkcji**.

![Blok funkcji Start Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na **opracowanie** karcie, Zastąp kod run.csx następującym kodem:

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
> Upewnij się zastąpić zmiennych w powyższym kodzie do punktu do konta magazynu, w którym zapisywane są dzienniki magazynu kluczy, usługi service bus, utworzony wcześniej i określonej ścieżki do magazynu kluczy dzienniki magazynu.
>
>

Funkcja przejmuje najnowszego pliku dziennika z konta magazynu której zapisywane są dzienniki magazynu kluczy, grabs najnowsze zdarzenia z tego pliku i umieszcza je do kolejki usługi Service Bus. Ponieważ pojedynczy plik może mieć wiele zdarzeń, należy utworzyć plik sync.txt, funkcja również wygląda na ustalenie sygnaturę czasową ostatniego zdarzenia, która została pobrana. Daje to pewność, że nie naciśnięciu tego samego zdarzenia wiele razy. Ten plik sync.txt zawiera sygnaturę czasową dla ostatniego zdarzenia napotkano. Dzienniki, gdy załadowano, trzeba posortowane według sygnatury czasowej, aby upewnić się, że są one uporządkowane poprawnie.

Dla tej funkcji odwołania mamy kilka dodatkowych bibliotek, które nie są dostępne z usługi Azure Functions, w tym polu. Aby je uwzględnić, potrzebujemy usługi Azure Functions może kopiować je przy użyciu narzędzia NuGet. Wybierz **Wyświetl pliki** opcji.

![Wyświetlanie plików](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

I Dodaj plik o nazwie project.json o następującej zawartości:

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
Po **zapisać**, usługi Azure Functions pobierze wymagane pliki binarne.

Przełącz się do **integracji** karcie i podać parametr czasomierza zrozumiałą nazwę do użycia w funkcji. W powyższym kodzie oczekuje czasomierza wywoływanie *myTimer*. Określ [wyrażenie CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) w następujący sposób: 0 \* \* \* \* \* dla czasomierza, który spowoduje, że funkcja do uruchamiania raz na minutę.

W tym samym **integracji** , Dodaj dane wejściowe typu **magazyn obiektów Blob Azure**. Będzie to wskaż plik sync.txt, który zawiera sygnaturę czasową ostatniego zdarzenia przeglądał przez funkcję. Będzie dostępna w ramach funkcji przez nazwę parametru. W powyższym kodzie wprowadzania magazynu obiektów Blob Azure oczekuje, że nazwa parametru, który ma zostać *inputBlob*. Wybierz konto magazynu, w której będą znajdować się w pliku sync.txt (może być taka sama lub innego konta magazynu). W polu Ścieżka podaj ścieżkę, w którym znajduje się plik w formacie {container-name}/path/to/sync.txt.

Dodaj dane wyjściowe typu *magazyn obiektów Blob Azure* danych wyjściowych. Będzie to wskaż plik sync.txt zdefiniowanych w danych wejściowych. To jest używany przez funkcję zapisu sygnatura czasowa ostatniego zdarzenia przeglądał. Poprzedni kod oczekuje, że ten parametr ma być wywoływana *Blob_wyj*.

W tym momencie funkcji jest gotowy. Upewnij się powrócić do **opracowanie** karcie i Zapisz kod. Sprawdź okno danych wyjściowych występują błędy kompilacji i odpowiednio je poprawić. Jeśli kompiluje kod, następnie kod powinny teraz być sprawdzanie dzienniki magazynu kluczy co minutę i wypychanie żadnych nowych zdarzeń, na zdefiniowanym kolejki usługi Service Bus. Informacje o rejestrowaniu zapisał w oknie dziennika każdym wyzwoleniu funkcji powinny być widoczne.

### <a name="azure-logic-app"></a>Aplikacja logiki platformy Azure
Następnie należy utworzyć aplikację logiki platformy Azure, która przejmuje zdarzenia, że funkcja jest Wypychanie do kolejki usługi Service Bus, analizuje zawartość i wysyła wiadomość e-mail na podstawie warunku filtrowanego.

[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) , przechodząc do **nowy > aplikacji logiki**.

Po utworzeniu aplikacji logiki, przejdź do niego i wybierz polecenie **Edytuj**. W edytorze aplikacji logiki, wybierz **kolejką usługi Service Bus** i wprowadź swoje poświadczenia usługi Service Bus, aby połączyć go z kolejki.

![Magistrala usług aplikacji logiki platformy Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Następnie wybierz **Dodaj warunek**. W tym stanie Przełącz się do Zaawansowany edytor i wprowadź następujący kod, zastępując APP_ID rzeczywiste APP_ID aplikacji sieci web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

To wyrażenie zasadniczo zwraca **false** Jeśli *appid* od zdarzenia przychodzące (czyli treści wiadomości usługi Service Bus) nie jest *appid* aplikacji.

Teraz utworzymy działania na podstawie **Jeśli nie, nic nie rób**.

![Azure aplikacji logiki, wybierz akcję](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Jako akcję wybierz **usługi Office 365 — wysyłanie wiadomości e-mail**. Wypełnij pola, aby utworzyć wiadomość e-mail do wysłania w zdefiniowanym warunkiem zwraca **false**. Jeśli nie masz usługi Office 365, można przyjrzeć alternatyw do osiągnięcia takie same wyniki.

W tym momencie masz pełny potok, który wyszukuje nowe dzienniki inspekcji magazynu kluczy raz na minutę. Nowe dzienniki, który odnajdzie jej Wypychanie do kolejką usługi service bus. Aplikację logiki jest wyzwalane, gdy nowy komunikat znajdzie się w kolejce. Jeśli *appid* w zdarzenie nie jest zgodny z Identyfikatorem aplikacji z aplikacji wywołującej, wysyła wiadomość e-mail.
