---
title: Używanie przechwytywania pakietów do proaktywnego monitorowania sieci przy użyciu alertów i usługi Azure Functions | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia przechwytywania pakietów wyzwolonych alertów za pomocą usługi Azure Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 37e42b05046be27254d2ceb15a59fbdb931ae161
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711912"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Proaktywne monitorowanie sieci za pomocą alertów i usługi Azure Functions na użytek przechwycenie pakietu

Przechwytywanie pakietów obserwatora sieci tworzy sesji przechwytywania, aby śledzić ruch do i z maszyn wirtualnych. Plik przechwytywania może mieć filtru, który jest zdefiniowane w celu śledzenia tylko ruch, którego chcesz monitorować. Te dane są następnie przechowywane w obiekcie blob magazynu lokalnie i na maszynie gościa.

Ta funkcja może być uruchomiona zdalnie innych scenariuszy usługi automation, takich jak Azure Functions. Przechwytywanie pakietów zapewnia możliwość uruchomienia aktywnego przechwytywania oparte na sieci definiowanej przez anomalii. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji o sieci włamań i debugowania komunikacja klient serwer.

Zasoby, które są wdrażane na platformie Azure uruchomić 24/7. Możesz i działu aktywnie nie można monitorować stan wszystkich zasobów 24/7. Na przykład co się stanie po wystąpieniu problemu o 2: 00?

Dzięki użyciu usługi Network Watcher, alerty i functions z poziomu systemu Azure możesz aktywnie reagować z wyprzedzeniem przy użyciu danych i narzędzia do rozwiązywania problemów w sieci.

![Scenariusz][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-Az-ps).
* Istniejącego wystąpienia usługi Network Watcher. Jeśli nie masz jeszcze jeden, [Utwórz wystąpienie usługi Network Watcher](network-watcher-create.md).
* W tym samym regionie, co Usługa Network Watcher z istniejącej maszyny wirtualnej [rozszerzenia Windows](../virtual-machines/windows/extensions-nwa.md) lub [rozszerzenia maszyny wirtualnej systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenariusz

W tym przykładzie maszyna wirtualna wysyła więcej segmentów TCP niż zwykle, a chcesz otrzymywać alerty. Segmentów TCP jest używany tutaj jako przykładu, ale można użyć dowolnego warunek alertu.

Po wyświetleniu powiadomienia, chcesz otrzymywać poziomie pakietów danych, aby zrozumieć, dlaczego został zwiększony komunikacji. Możesz wykonać kroki, aby wrócić maszyny wirtualnej do regularnego komunikacji.

W tym scenariuszu przyjęto założenie, iż istniejącego wystąpienia usługi Network Watcher i grupę zasobów z prawidłową maszyną wirtualną.

Poniżej przedstawiono omówienie przepływu pracy, który ma miejsce:

1. Alert zostanie wywołany na maszynie Wirtualnej.
1. Alert wywołuje funkcję platformy Azure za pomocą elementu webhook.
1. Funkcji platformy Azure przetwarza alertu i uruchamia sesję przechwytywania pakietów usługi Network Watcher.
1. Przechwytywanie pakietów działa na maszynie Wirtualnej i umożliwia zbieranie informacji o ruchu.
1. Plik przechwytywania pakietów jest przekazywany do konta magazynu do przeglądu i diagnostyki.

Aby zautomatyzować ten proces, utworzymy i połączyć z alertu w naszej maszyny Wirtualnej, aby wyzwolić po wystąpieniu zdarzenia. Musimy również utworzyć funkcję, aby wywoływać usługi Network Watcher.

W tym scenariuszu wykonuje następujące czynności:

* Tworzy funkcję platformy Azure, który rozpoczyna się przechwytywania pakietów.
* Tworzy regułę alertu na maszynie wirtualnej i konfiguruje regułę alertu do wywoływania funkcji platformy Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Pierwszym krokiem jest tworzenie funkcji platformy Azure do przetwarzania alertu i utworzyć przechwytywania pakietów.

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **Utwórz zasób** > **obliczenia** > **aplikacji funkcji**.

    ![Utworzenie aplikacji funkcji][1-1]

2. Na **aplikacji funkcji** bloku, wprowadź następujące wartości, a następnie wybierz **OK** do utworzenia aplikacji:

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Nazwa aplikacji**|PacketCaptureExample|Nazwa aplikacji funkcji.|
    |**Subskrypcja**|[Twoja subskrypcja] Subskrypcja, dla której ma zostać utworzona aplikacja funkcji.||
    |**Grupa zasobów**|PacketCaptureRG|Grupę zasobów zawierającą aplikację funkcji.|
    |**Plan hostingu**|Plan zużycia| Typ planu używany przez aplikację funkcji. Opcje to zużycie i plan usługi Azure App Service. |
    |**Lokalizacja**|Środkowe stany USA| Region, w której chcesz utworzyć aplikację funkcji.|
    |**Konto magazynu**|{automatycznie wygenerowany}| Konto magazynu, które wymaga usługi Azure Functions na potrzeby magazynu ogólnego przeznaczenia.|

3. Na **aplikacje funkcji PacketCaptureExample** bloku wybierz **funkcje** > **funkcja niestandardowa**  >  **+**.

4. Wybierz **HttpTrigger-Powershell**, a następnie wprowadź pozostałe informacje. Na koniec, aby utworzyć funkcję, wybierz **Utwórz**.

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Scenariusz**|Eksperymentalne|Typ scenariusza|
    |**Nazwa funkcji**|AlertPacketCapturePowerShell|Nazwa funkcji|
    |**Poziom autoryzacji**|Funkcja|Poziom autoryzacji dla funkcji|

![Przykład funkcji][functions1]

> [!NOTE]
> Szablon programu PowerShell jest eksperymentalny i nie ma pełną pomoc techniczną.

Dostosowania są wymagane dla tego przykładu i zostały wyjaśnione w poniższych krokach.

### <a name="add-modules"></a>Dodaj moduły

Za pomocą poleceń cmdlet programu PowerShell obserwatora sieci, należy przekazać najnowszy moduł programu PowerShell do aplikacji funkcji.

1. Na komputerze lokalnym przy użyciu zainstalowane najnowsze moduły programu Azure PowerShell uruchom następujące polecenie programu PowerShell:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    W tym przykładzie zapewnia ścieżkę lokalną moduły programu Azure PowerShell. Te foldery są używane w kolejnym kroku. Moduły, które są używane w tym scenariuszu są:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Foldery programu PowerShell][functions5]

1. Wybierz **ustawień aplikacji funkcji** > **przejdź do Edytor usługi App Service**.

    ![Ustawienia aplikacji funkcji][functions2]

1. Kliknij prawym przyciskiem myszy **AlertPacketCapturePowershell** folder, a następnie utwórz folder o nazwie **azuremodules**. 

4. Utwórz podfolder dla każdego modułu, które są potrzebne.

    ![Folder i podfoldery][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Kliknij prawym przyciskiem myszy **Az.Network** podfolder, a następnie wybierz **Przekaż pliki**. 

6. Przejdź do swojej moduły platformy Azure. W lokalnej **Az.Network** folderu, zaznacz wszystkie pliki w folderze. Następnie wybierz przycisk **OK**. 

7. Powtórz te kroki dla **Az.Accounts** i **Az.Resources**.

    ![Przekazywanie plików][functions6]

1. Po zakończeniu, każdy folder powinien mieć pliki modułu programu PowerShell na maszynie lokalnej.

    ![Pliki interpretatora PowerShell][functions7]

### <a name="authentication"></a>Authentication

Aby korzystać z poleceń cmdlet programu PowerShell, należy uwierzytelnić. Możesz skonfigurować uwierzytelnianie w aplikacji funkcji. Aby skonfigurować uwierzytelnianie, należy skonfigurować zmienne środowiskowe i przekazywanie zaszyfrowanego pliku klucza do aplikacji funkcji.

> [!NOTE]
> Ten scenariusz zawiera tylko jeden przykład jak implementować uwierzytelniania za pomocą usługi Azure Functions. Istnieją inne sposoby, aby to zrobić.

#### <a name="encrypted-credentials"></a>Zaszyfrowane poświadczenia

Poniższy skrypt programu PowerShell tworzy plik klucza o nazwie **PassEncryptKey.key**. Umożliwia także zaszyfrowana wersja hasła, która jest dostarczana. To hasło jest tego samego hasła, która jest zdefiniowana dla aplikacji usługi Azure Active Directory, który jest używany do uwierzytelniania.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

W edytorze usługi App Service dla aplikacji funkcji, Utwórz folder o nazwie **klucze** w obszarze **AlertPacketCapturePowerShell**. Następnie przekaż **PassEncryptKey.key** pliku, który został utworzony w poprzednim przykładzie programu PowerShell.

![Klucz funkcji][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Pobierz wartości zmiennych środowiskowych

Wymaganie końcowego jest Ustawianie zmiennych środowiskowych, które są niezbędne uzyskać dostęp do wartości do uwierzytelniania. Na poniższej liście przedstawiono zmienne środowiskowe, które zostały utworzone:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Identyfikator klienta jest identyfikator aplikacji dla aplikacji w usłudze Azure Active Directory.

1. Jeśli nie masz jeszcze aplikacji do użycia, z poniższego przykładu, aby utworzyć aplikację.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Hasło używane podczas tworzenia aplikacji przez powinny być tego samego hasła, który został utworzony wcześniej, podczas zapisywania pliku klucza.

1. W witrynie Azure portal wybierz **subskrypcje**. Wybierz subskrypcję, a następnie wybierz pozycję **kontrola dostępu (IAM)**.

    ![Funkcje zarządzania tożsamościami i Dostępem][functions9]

1. Wybierz konto, a następnie wybierz pozycję **właściwości**. Skopiuj identyfikator aplikacji.

    ![Identyfikator aplikacji funkcji][functions10]

#### <a name="azuretenant"></a>AzureTenant

Uzyskaj identyfikator dzierżawy, uruchamiając poniższy przykład programu PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Wartość zmiennej środowiskowej AzureCredPassword jest wartością, który można pobrać z systemem w poniższym przykładzie programu PowerShell. W tym przykładzie jest taki sam, która jest wyświetlana w poprzednim **zaszyfrowane poświadczenia** sekcji. Wartość, która jest potrzebna jest dane wyjściowe `$Encryptedpassword` zmiennej.  To hasło jednostki usługi, który został zaszyfrowany za pomocą skryptu programu PowerShell.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Store zmiennych środowiskowych

1. Przejdź do aplikacji funkcji. Następnie wybierz pozycję **ustawień aplikacji funkcji** > **Konfigurowanie ustawień aplikacji**.

    ![Konfigurowanie ustawień aplikacji][functions11]

1. Dodaj zmienne środowiskowe i ich wartości ustawień aplikacji, a następnie wybierz **Zapisz**.

    ![Ustawienia aplikacji][functions12]

### <a name="add-powershell-to-the-function"></a>Dodaj programu PowerShell do funkcji

Teraz nadszedł czas na wykonywanie wywołań do usługi Network Watcher z w ramach funkcji platformy Azure. W zależności od wymagań implementacja tej funkcji może się różnić. Jednak ogólny przepływ kodu jest następująca:

1. Parametry wejściowe procesu.
2. Przechwytuje pakietów istniejące zapytanie w celu sprawdzenia limitów i rozwiązać konflikty nazw.
3. Utwórz przechwytywania pakietów za pomocą odpowiednich parametrów.
4. Przechwytywanie pakietów sondowania, okresowo do czasu jego zakończenia.
5. Powiadamia użytkownika, że sesja przechwytywania pakietów została zakończona.

Poniższy przykład jest kod programu PowerShell, który może służyć do funkcji. Istnieją wartości, które muszą zostać zastąpione dla **subscriptionId**, **resourceGroupName**, i **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Pobierz adres URL funkcji 
1. Po utworzeniu funkcji należy skonfigurować alert do wywołania adresu URL, który jest skojarzony z funkcją. Aby uzyskać tę wartość, skopiuj adres URL funkcji z aplikacji funkcji.

    ![Znajdowanie adres URL funkcji][functions13]

2. Skopiuj adres URL funkcji dla aplikacji funkcji.

    ![Kopiowanie adresu URL funkcji][2]

Jeśli potrzebujesz właściwości niestandardowe w ładunku żądania POST elementu webhook, zobacz [Konfigurowanie elementu webhook dla alertu dotyczącego metryki Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurowanie alertu dla maszyny Wirtualnej

Alerty można skonfigurować do powiadamiania osób określonych Metryka przekracza próg, który nie jest do niej przypisany. W tym przykładzie alertu znajduje się na segmentów TCP, które są wysyłane, ale można wyzwolić alert dla innych metryk. W tym przykładzie alertu jest skonfigurowana do wywoływania elementu webhook w wywołaniu funkcji.

### <a name="create-the-alert-rule"></a>Utwórz regułę alertu

Przejdź do istniejącej maszyny wirtualnej, a następnie dodaj regułę alertu. Bardziej szczegółowa dokumentacja na temat konfigurowania alertów znajduje się w temacie [Tworzenie alertów w usłudze Azure Monitor dla usług platformy Azure — witryna Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Wprowadź następujące wartości w **reguły alertu** bloku, a następnie wybierz **OK**.

  |**Ustawienie** | **Wartość** | **Szczegóły** |
  |---|---|---|
  |**Nazwa**|TCP_Segments_Sent_Exceeded|Nazwa reguły alertu.|
  |**Opis**|Segmentów TCP wysyłane przekroczyła próg|Opis reguły alertu.|
  |**Metryka**|Wysłane segmenty protokołu TCP| Metryki służące do wyzwolenia alertu. |
  |**Warunek**|Większe niż| Warunek, który chcesz użyć podczas obliczania metryki.|
  |**Próg**|100| Wartość metryki, które wyzwala alert. Ta wartość powinna być równa prawidłową wartość dla danego środowiska.|
  |**Okres**|W ciągu ostatnich pięciu minut| Określa okres, w którym należy szukać próg dla metryki.|
  |**Webhook**|[adres URL elementu webhook z funkcji aplikacji]| Adres URL elementu webhook z poziomu aplikacji funkcji, który został utworzony w poprzednich krokach.|

> [!NOTE]
> Metryka segmentów TCP nie jest włączona domyślnie. Dowiedz się więcej o sposobie włączania dodatkowe metryki, odwiedzając [Włączanie monitorowania i diagnostyki](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Sprawdzanie wyników

Po kryteria alertu wyzwalaczy przechwytywania pakietów jest tworzony. Przejdź do usługi Network Watcher, a następnie wybierz **przechwytywania pakietów**. Na tej stronie możesz wybrać link pliku przechwytywania pakietów, aby pobrać przechwytywania pakietów.

![Wyświetl przechwycenie pakietu][functions14]

Jeśli plik przechwytywania jest przechowywany lokalnie, można je pobrać, logując się do maszyny wirtualnej.

Aby uzyskać instrukcje dotyczące pobierania plików z konta usługi Azure storage, zobacz [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Inne narzędzie, można użyć jest [Eksploratora usługi Storage](https://storageexplorer.com/).

Po pobraniu swoje przechwytywania można je wyświetlić, przy użyciu dowolnego narzędzia, który może odczytać **.cap** pliku. Poniżej podano linki do dwóch spośród tych narzędzi:

- [Narzędzie Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wyświetlać swoje przechwytywania pakietów, odwiedzając [analizy przechwytywania pakietów za pomocą programu Wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
