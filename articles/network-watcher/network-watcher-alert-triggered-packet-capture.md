---
title: Za pomocą przechwytywania pakietów wykonywać sieci aktywnego monitorowania za pomocą alertów i usługi Azure Functions | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia przechwytywania alertów wyzwalanych pakietów z obserwatora sieciowego Azure
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4c96ca70b9b6a82dcccec443ac0b1e06f96a2396
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597415"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Użyj przechwytywania pakietów dla sieci aktywnego monitorowania za pomocą alertów i usługi Azure Functions

Przechwytywania pakietów obserwatora sieciowego tworzy sesji przechwytywania, aby śledzić ruch do i z maszyn wirtualnych. Plik przechwytywania może mieć filtr, który zdefiniowano w celu śledzenia ruch, który chcesz monitorować. Dane te są następnie przechowywane w obiekcie blob magazynu lub lokalnie na maszynie gościa.

Ta funkcja może być uruchomiona zdalnie na inne scenariusze automatyzacji, takich jak usługi Azure Functions. Przechwytywania pakietów daje możliwość uruchomienia aktywnego przechwytywania oparte na sieci anomalii. Innych celów obejmują zbieranie statystyk sieciowych, pobieranie informacji na temat sieci atakami, debugowania komunikacja klient serwer i inne.

Zasoby, które są wdrażane na platformie Azure Uruchom 24/7. Możesz i pracownicy działu aktywnie nie można monitorować stan wszystkich zasobów 24/7. Na przykład co się stanie, jeśli wystąpi problem w 2 AM?

Dzięki użyciu obserwatora sieciowego, alerty i funkcji w ramach ekosystemu platformy Azure można aktywnego Odpowiedz przy użyciu danych i narzędzia do rozwiązywania problemów w sieci.

![Scenariusz][scenario]

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowszą wersję [programu Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Istniejące wystąpienie obserwatora sieciowego. Jeśli nie masz już konto, [utworzyć wystąpienia obserwatora sieciowego](network-watcher-create.md).
* W tym samym regionie co obserwatora sieciowego z istniejącej maszyny wirtualnej [rozszerzenie systemu Windows](../virtual-machines/windows/extensions-nwa.md) lub [rozszerzenie maszyny wirtualnej systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenariusz

W tym przykładzie maszyna wirtualna wysyła więcej segmentów TCP niż zwykle, i chcesz otrzymywać alerty. Segmentów TCP są używane jako w tym przykładzie, ale można użyć dowolnego warunku alertu.

Po wyświetleniu powiadomienia, które chcesz otrzymywać dane poziomie pakietów, aby zrozumieć, dlaczego wzrosło komunikacji. Następnie należy wykonać czynności związane z powrót maszyny wirtualnej do regularnych komunikacji.

W tym scenariuszu założono, że istniejące wystąpienie programu obserwatora sieciowego i grupy zasobów z prawidłową maszyną wirtualną.

Poniżej przedstawiono omówienie przepływu pracy, który ma miejsce:

1. Alert zostanie wywołany na maszynie Wirtualnej.
1. Alert wywołania funkcji platformy Azure za pomocą elementu webhook.
1. Funkcja Azure przetwarza alert i uruchamia sesję przechwytywania pakietów obserwatora sieciowego.
1. Przechwytywania pakietów działa na maszynie Wirtualnej i zbiera ruchu.
1. Plik przechwytywania pakietów jest przekazywany do konta magazynu do przeglądu i diagnostyki.

Aby zautomatyzować ten proces, możemy utworzyć i połącz alert na naszych maszyny Wirtualnej można wyzwolić po wystąpieniu zdarzenia. Utworzymy również funkcję, która ma wywołują obserwatora sieciowego.

W tym scenariuszu wykonuje następujące czynności:

* Tworzy funkcję platformy Azure, która rozpoczyna się przechwytywania pakietów.
* Tworzy regułę alertu na maszynie wirtualnej i konfiguruje reguły alertów w wywołaniu funkcji platformy Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Pierwszym krokiem jest tworzenie funkcji platformy Azure do przetwarzania alertu i utworzyć przechwytywania pakietów.

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **Utwórz zasób** > **obliczeniowe** > **aplikacji funkcji**.

    ![Tworzenie aplikacji — funkcja][1-1]

2. Na **aplikacji funkcji** bloku, wprowadź następujące wartości, a następnie wybierz **OK** do utworzenia aplikacji:

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Nazwa aplikacji**|PacketCaptureExample|Nazwa funkcji aplikacji.|
    |**Subskrypcja**|[Subskrypcji] Subskrypcja do tworzenia aplikacji funkcji.||
    |**Grupa zasobów**|PacketCaptureRG|Grupy zasobów w celu uwzględnienia funkcji aplikacji.|
    |**Plan hostingu**|Plan zużycia| Typ planu używany przez funkcję aplikacji. Dostępne opcje to zużycie lub plan usługi aplikacji Azure. |
    |**Lokalizacja**|Środkowe stany USA| Region, w którym chcesz utworzyć aplikację funkcji.|
    |**Konto magazynu**|{automatycznie wygenerowaną}| Konto magazynu Azure Functions potrzeb magazynu ogólnego przeznaczenia.|

3. Na **aplikacji funkcji PacketCaptureExample** bloku, wybierz opcję **funkcje** > **Niestandardowa funkcja** >**+**.

4. Wybierz **HttpTrigger Powershell**, a następnie wprowadź pozostałe informacje. Na koniec Utwórz funkcję, wybierz **Utwórz**.

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Scenariusz**|Eksperymentalne|Scenariusz|
    |**Nazwa funkcji**|AlertPacketCapturePowerShell|Nazwa funkcji|
    |**Poziom dostępu**|Funkcja|Poziom dostępu dla funkcji|

![Przykład funkcji][functions1]

> [!NOTE]
> Szablon programu PowerShell jest eksperymentalna i nie ma pełnej obsługi.

Dostosowania są wymagane w ramach tego przykładu i opisano szczegółowo w kolejnych krokach.

### <a name="add-modules"></a>Dodawanie modułów

Aby używać poleceń cmdlet programu PowerShell obserwatora sieci, Przekaż najnowsze modułu programu PowerShell do aplikacji funkcji.

1. Na komputerze lokalnym z zainstalowane najnowsze modułów programu Azure PowerShell uruchom następujące polecenie programu PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    W tym przykładzie zapewnia ścieżki lokalnej moduły programu PowerShell systemu Azure. Te foldery są używane w kolejnym kroku. Moduły, które są używane w tym scenariuszu są:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![Foldery programu PowerShell][functions5]

1. Wybierz **funkcji ustawienia aplikacji** > **przejdź do aplikacji usługi edytora**.

    ![Ustawienia aplikacji funkcji][functions2]

1. Kliknij prawym przyciskiem myszy **AlertPacketCapturePowershell** folder, a następnie utwórz folder o nazwie **azuremodules**. 

4. Utwórz podfolder dla poszczególnych modułów, które są potrzebne.

    ![Folderze i jego podfolderach][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Kliknij prawym przyciskiem myszy **AzureRM.Network** podfolder, a następnie wybierz **Przekaż**. 

6. Przejdź do usługi Azure moduły. W lokalnej **AzureRM.Network** folderu, wybierz wszystkie pliki w folderze. Następnie wybierz przycisk **OK**. 

7. Powtórz te kroki dla **AzureRM.Profile** i **AzureRM.Resources**.

    ![Przekazywanie plików][functions6]

1. Po zakończeniu, każdy folder powinien mieć pliki modułu programu PowerShell z komputera lokalnego.

    ![Pliki środowiska PowerShell][functions7]

### <a name="authentication"></a>Authentication

Aby używać poleceń cmdlet programu PowerShell, należy uwierzytelnić. Możesz skonfigurować uwierzytelnianie w aplikacji funkcji. Aby skonfigurować uwierzytelnianie, musi skonfigurować zmienne środowiskowe i przekazać zaszyfrowany plik klucza do funkcji aplikacji.

> [!NOTE]
> Ten scenariusz zawiera tylko jeden przykład sposobu wdrażania uwierzytelniania za pomocą usługi Azure Functions. Istnieją inne sposoby, w tym celu.

#### <a name="encrypted-credentials"></a>Zaszyfrowane poświadczenia

Poniższy skrypt programu PowerShell tworzy plik klucza o nazwie **PassEncryptKey.key**. Umożliwia także zaszyfrowana wersja hasła, które jest dostarczone. To hasło jest takie samo jak hasło jest zdefiniowany dla aplikacji usługi Azure Active Directory, który jest używany do uwierzytelniania.

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

W aplikacji usługi edytorze funkcji aplikacji, Utwórz folder o nazwie **klucze** w obszarze **AlertPacketCapturePowerShell**. Następnie przekaż **PassEncryptKey.key** pliku, który został utworzony w poprzedniej próbki programu PowerShell.

![Funkcje klucza][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Pobieranie wartości zmiennych środowiskowych

Wymaganie końcowego jest ustawienie zmiennych środowiskowych, które są niezbędne do uzyskania dostępu wartości dla uwierzytelniania. Na poniższej liście przedstawiono zmiennych środowiskowych, które są tworzone:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Identyfikator klienta to identyfikator aplikacji w usłudze Azure Active Directory.

1. Jeśli nie masz już aplikację do użycia, należy uruchomić poniższy przykład, aby utworzyć aplikację.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Hasła, który jest używany podczas tworzenia aplikacji powinna być to samo hasło, który został utworzony wcześniej podczas zapisywania pliku klucza.

1. W portalu Azure wybierz **subskrypcje**. Wybierz subskrypcję, a następnie wybierz **(IAM) kontroli dostępu**.

    ![Funkcje IAM][functions9]

1. Wybierz konto do użycia, a następnie wybierz **właściwości**. Skopiuj identyfikator aplikacji.

    ![Identyfikator aplikacji funkcji][functions10]

#### <a name="azuretenant"></a>AzureTenant

Uzyskaj identyfikator dzierżawcy, uruchamiając w poniższym przykładzie programu PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Wartość zmiennej środowiskowej AzureCredPassword jest wartością, który można pobrać z z poniższego polecenia programu PowerShell. W tym przykładzie jest taki sam, która jest widoczna w poprzednim **zaszyfrowane poświadczenia** sekcji. Wartość, która jest potrzebna jest dane wyjściowe `$Encryptedpassword` zmiennej.  To jest szyfrowane przy użyciu skryptu programu PowerShell hasło głównej usługi.

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

### <a name="store-the-environment-variables"></a>Zmienne środowiskowe

1. Przejdź do aplikacji funkcji. Następnie wybierz **funkcji ustawienia aplikacji** > **Konfiguruj ustawienia aplikacji**.

    ![Konfigurowanie ustawień aplikacji][functions11]

1. Dodawanie ustawień aplikacji zmienne środowiskowe i ich wartości, a następnie wybierz **zapisać**.

    ![Ustawienia aplikacji][functions12]

### <a name="add-powershell-to-the-function"></a>Dodawanie funkcji programu PowerShell

Teraz nadszedł czas, w celu wykonywania wywołań do obserwatora sieciowego z wewnątrz funkcji platformy Azure. W zależności od wymagań stosowania tej funkcji może się różnić. Jednak ogólny przebieg kodu jest następujący:

1. Parametry wejściowe procesu.
2. Istniejący pakiet kwerendy przechwytuje Sprawdź limity i rozwiązać konflikty nazw.
3. Utwórz przechwytywania pakietów z odpowiednimi parametrami.
4. Przechwytywania pakietów sondowania okresowo czasu jego ukończenia.
5. Powiadamia użytkownika, że sesja przechwytywania pakietów została zakończona.

Poniższy przykład jest kod programu PowerShell, który może służyć do funkcji. Brak wartości, które muszą zostać zastąpione dla **subscriptionId**, **resourceGroupName**, i **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

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
            Connect-AzureRmAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Pobierz adres URL funkcji 
1. Po utworzeniu funkcji, należy skonfigurować alert do adresu URL, który został skojarzony z funkcją wywołania. Aby uzyskać tę wartość, skopiuj adres URL funkcji z aplikacji funkcji.

    ![Znajdowanie adresu URL — funkcja][functions13]

2. Skopiuj adres URL funkcji dla funkcji aplikacji.

    ![Kopiowanie adresu URL — funkcja][2]

Jeśli potrzebujesz właściwości niestandardowe w ładunku żądania POST webhook odwołują się do [skonfigurować elementu webhook na alert metryki Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurowanie alertu dla maszyny Wirtualnej

Alerty można skonfigurować do wysyłania powiadomień osoby o konkretnej metryki przekracza wartość progową, który przypisano do niej. W tym przykładzie alertu jest na segmentów TCP, które są wysyłane, ale można wywoływać alert dla innych metryk. W tym przykładzie alertu jest skonfigurowany do wywołania elementu webhook w wywołaniu funkcji.

### <a name="create-the-alert-rule"></a>Utwórz regułę alertu

Przejdź do istniejącej maszyny wirtualnej, a następnie dodaj regułę alertu. Bardziej szczegółowa dokumentacja na temat konfigurowania alertów można znaleźć w folderze [w monitorze Azure tworzyć alerty dla usług Azure - Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Wprowadź następujące wartości w **reguły alertu** bloku, a następnie wybierz **OK**.

  |**Ustawienie** | **Wartość** | **Szczegóły** |
  |---|---|---|
  |**Nazwa**|TCP_Segments_Sent_Exceeded|Nazwa reguły alertów.|
  |**Opis**|Przekroczono próg wysyłane segmentów TCP|Opis reguły alertów.||
  |**Metryka**|Wysłane segmenty protokołu TCP| Metryka wyzwalanie alertu. |
  |**Warunek**|Więcej niż| Warunek, którego chcesz użyć podczas obliczania metryki.|
  |**Próg**|100| Wartość metryki, które wyzwala alert. Ta wartość powinna być równa prawidłową wartość dla danego środowiska.|
  |**Okres**|W ciągu ostatnich pięciu minut| Określa okres, w którym ma zostać wyszukane próg na metryki.|
  |**Element Webhook**|[adres URL elementu webhook z funkcji aplikacji]| Adres URL elementu webhook z aplikacji funkcji, który został utworzony w poprzednich krokach.|

> [!NOTE]
> Metryka segmentów TCP nie jest domyślnie włączona. Dowiedz się więcej o sposobie włączania dodatkowe metryki, odwiedzając [włączania monitorowania i diagnostyki](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Sprawdzanie wyników

Po kryteria alertu wyzwalaczy przechwytywania pakietów jest tworzony. Przejdź do obserwatora sieciowego, a następnie wybierz **przechwytywania pakietów**. Na tej stronie możesz wybrać łącze pliku przechwytywania pakietów, aby pobrać przechwytywania pakietów.

![Widok przechwytywania pakietów][functions14]

Jeśli plik przechwytywania jest przechowywany lokalnie, można je pobrać, logując się do maszyny wirtualnej.

Aby uzyskać instrukcje dotyczące pobierania plików z kontami magazynu Azure, zobacz [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Kolejnym narzędziem służy jest [Eksploratora usługi Storage](http://storageexplorer.com/).

Po pobraniu programu przechwytywania można wyświetlić go przy użyciu dowolnego narzędzia, który może odczytywać **CAP** pliku. Poniżej podano linki do dwóch spośród tych narzędzi:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wyświetlić Twojej przechwytywania pakietów, odwiedzając [analizy przechwytywania pakietu z programu Wireshark](network-watcher-deep-packet-inspection.md).


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
