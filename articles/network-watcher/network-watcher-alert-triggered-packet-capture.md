---
title: Przechwytywanie pakietów umożliwia proaktywne monitorowanie sieci za pomocą alertów — Usługi Azure Functions
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób tworzenia przechwytywania pakietów wyzwalanego alertem za pomocą usługi Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ea506e137d71fc3124a4f93f1e97750a08dd4284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842941"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Używanie przechwytywania pakietów do proaktywnego monitorowania sieci za pomocą alertów i funkcji platformy Azure

Przechwytywanie pakietów Obserwatora sieci tworzy sesje przechwytywania do śledzenia ruchu do i z maszyn wirtualnych. Plik przechwytywania może mieć filtr, który jest zdefiniowany do śledzenia tylko ruchu, który chcesz monitorować. Te dane są następnie przechowywane w obiekcie blob magazynu lub lokalnie na komputerze gościa.

Tę funkcję można uruchomić zdalnie z innych scenariuszy automatyzacji, takich jak usługi Azure Functions. Przechwytywanie pakietów umożliwia uruchamianie proaktywnych przechwytywani na podstawie zdefiniowanych anomalii sieciowych. Inne zastosowania obejmują zbieranie statystyk sieciowych, uzyskiwanie informacji o włamaniach do sieci, debugowanie komunikacji klient-serwer i inne.

Zasoby, które są wdrażane na platformie Azure uruchomić 24/7. Ty i Twoi pracownicy nie możecie aktywnie monitorować stanu wszystkich zasobów 24/7. Na przykład, co się stanie, jeśli problem wystąpi o godzinie 2:00?

Korzystając z funkcji Kontrola sieci, alertów i funkcji z poziomu ekosystemu platformy Azure, można aktywnie reagować za pomocą danych i narzędzi do rozwiązywania problemów w sieci.

![Scenariusz][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja [programu Azure PowerShell](/powershell/azure/install-Az-ps).
* Istniejące wystąpienie Obserwatora sieciowego. Jeśli jeszcze go nie masz, [utwórz wystąpienie Funkcji Obserwatora sieciowego](network-watcher-create.md).
* Istniejąca maszyna wirtualna w tym samym regionie co Obserwator sieci z [rozszerzeniem systemu Windows](../virtual-machines/windows/extensions-nwa.md) lub [rozszerzeniem maszyny wirtualnej systemu Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenariusz

W tym przykładzie maszyna wirtualna wysyła więcej segmentów TCP niż zwykle i chcesz otrzymywać alerty. Segmenty TCP są używane jako przykład tutaj, ale można użyć dowolnego warunku alertu.

Gdy zostaniesz powiadomiony, chcesz otrzymywać dane na poziomie pakietu, aby zrozumieć, dlaczego komunikacja wzrosła. Następnie można podjąć kroki, aby przywrócić maszynę wirtualną do regularnej komunikacji.

W tym scenariuszu założono, że masz istniejące wystąpienie Obserwatora sieciowego i grupy zasobów z prawidłową maszyną wirtualną.

Poniższa lista zawiera przegląd przepływu pracy, który ma miejsce:

1. Alert jest wyzwalany na maszynie wirtualnej.
1. Alert wywołuje funkcję platformy Azure za pośrednictwem elementu webhook.
1. Funkcja platformy Azure przetwarza alert i uruchamia sesję przechwytywania pakietów obserwatora sieci.
1. Przechwytywanie pakietów działa na maszynie Wirtualnej i zbiera ruch.
1. Plik przechwytywania pakietów jest przekazyany na konto magazynu w celu przejrzenia i zdiagnozowania.

Aby zautomatyzować ten proces, tworzymy i łączymy alert na naszej maszynie Wirtualnej, aby wyzwolić, gdy wystąpi zdarzenie. Tworzymy również funkcję do wywołania w Network Watcher.

W tym scenariuszu wykonuje się następujące czynności:

* Tworzy funkcję platformy Azure, która uruchamia przechwytywanie pakietów.
* Tworzy regułę alertu na maszynie wirtualnej i konfiguruje regułę alertu, aby wywołać funkcję platformy Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Pierwszym krokiem jest utworzenie funkcji platformy Azure do przetwarzania alertu i tworzenia przechwytywania pakietów.

1. W [portalu Azure](https://portal.azure.com)wybierz pozycję **Utwórz** > aplikację funkcji**obliczeniowych** > **Function App**zasobów .

    ![Tworzenie aplikacji funkcji][1-1]

2. W bloku **aplikacja funkcji** wprowadź następujące wartości, a następnie wybierz przycisk **OK,** aby utworzyć aplikację:

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Nazwa aplikacji**|PacketCaptureExample|Nazwa aplikacji funkcji.|
    |**Subskrypcja**|[Twoja subskrypcja] Subskrypcja, dla której ma być utworzona aplikacja funkcji.||
    |**Grupa zasobów**|PacketCaptureRG (PakietCaptureRG)|Grupa zasobów zawierająca aplikację funkcji.|
    |**Plan hostingowy**|Plan zużycia| Typ planu używanego przez aplikację funkcji. Opcje to Zużycie lub plan usługi Azure App Service. |
    |**Lokalizacja**|Środkowe stany USA| Region, w którym ma być tworzęna aplikację funkcji.|
    |**Konto magazynu**|{autogenerowane}| Konto magazynu, które usługa Azure Functions potrzebuje do magazynu ogólnego przeznaczenia.|

3. W bloku **PacketCaptureExample Function Apps** wybierz pozycję **Funkcje** > **Custom function** >**+** niestandardowe.

4. Wybierz **pozycję HttpTrigger-Powershell**, a następnie wprowadź pozostałe informacje. Na koniec, aby utworzyć funkcję, wybierz pozycję **Utwórz**.

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Scenariusz**|Eksperymentalne|Typ scenariusza|
    |**Nadawanie nazwy funkcji**|AlertPacketCapturePowerShell|Nazwa funkcji|
    |**Poziom autoryzacji**|Funkcja|Poziom autoryzacji dla funkcji|

![Przykład funkcji][functions1]

> [!NOTE]
> Szablon programu PowerShell jest eksperymentalny i nie ma pełnej obsługi.

Dostosowania są wymagane w tym przykładzie i są wyjaśnione w poniższych krokach.

### <a name="add-modules"></a>Dodaj moduły

Aby użyć poleceń cmdlet programu PowerShell programu Network Watcher, należy przekazać najnowszy moduł programu PowerShell do aplikacji funkcyjnej.

1. Na komputerze lokalnym z zainstalowanymi najnowszymi modułami programu Azure PowerShell uruchom następujące polecenie programu PowerShell:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    W tym przykładzie przedstawiono ścieżkę lokalną modułów programu Azure PowerShell. Te foldery są używane w późniejszym kroku. Moduły, które są używane w tym scenariuszu są:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Foldery programu PowerShell][functions5]

1. Wybierz **pozycję Ustawienia** > aplikacji Funkcji**Przejdź do Edytora usług aplikacji**.

    ![Ustawienia aplikacji funkcji][functions2]

1. Kliknij prawym przyciskiem myszy folder **AlertPacketCapturePowershell,** a następnie utwórz folder o nazwie **azuremodules**. 

4. Utwórz podfolder dla każdego modułu, którego potrzebujesz.

    ![Foldery i podfoldery][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Kliknij prawym przyciskiem myszy podfolder **Az.Network,** a następnie wybierz polecenie **Przekaż pliki**. 

6. Przejdź do modułów platformy Azure. W lokalnym folderze **Az.Network** zaznacz wszystkie pliki w folderze. Następnie wybierz przycisk **OK**. 

7. Powtórz te kroki dla **Az.Accounts** i **Az.Resources**.

    ![Przekazywanie plików][functions6]

1. Po zakończeniu każdego folderu powinny mieć pliki modułu programu PowerShell z komputera lokalnego.

    ![Pliki programu PowerShell][functions7]

### <a name="authentication"></a>Uwierzytelnianie

Aby użyć poleceń cmdlet programu PowerShell, należy uwierzytelnić. Uwierzytelnianie jest konfiguracyjne w aplikacji funkcji. Aby skonfigurować uwierzytelnianie, należy skonfigurować zmienne środowiskowe i przekazać zaszyfrowany plik klucza do aplikacji funkcji.

> [!NOTE]
> W tym scenariuszu przedstawiono tylko jeden przykład sposobu implementacji uwierzytelniania za pomocą usługi Azure Functions. Istnieją inne sposoby, aby to zrobić.

#### <a name="encrypted-credentials"></a>Zaszyfrowane poświadczenia

Następujący skrypt programu PowerShell tworzy plik klucza o nazwie **PassEncryptKey.key**. Zapewnia również zaszyfrowaną wersję hasła, które jest dostarczane. To hasło jest tym samym hasłem, które jest zdefiniowane dla aplikacji usługi Azure Active Directory, która jest używana do uwierzytelniania.

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

W Edytorze aplikacji usługi app utwórz folder o nazwie **klucze** w obszarze **AlertPacketCapturePowerShell**. Następnie przekaż plik **PassEncryptKey.key** utworzony w poprzednim przykładzie programu PowerShell.

![Klawisz Funkcji][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Pobieranie wartości dla zmiennych środowiskowych

Ostatecznym wymaganiem jest skonfigurowanie zmiennych środowiskowych, które są niezbędne do uzyskania dostępu do wartości uwierzytelniania. Na poniższej liście przedstawiono tworzone zmienne środowiskowe:

* Usługa AzureClientID

* AzureTenant (AzureTenant)

* AzureCredPassword


#### <a name="azureclientid"></a>Usługa AzureClientID

Identyfikator klienta jest identyfikatorem aplikacji w usłudze Azure Active Directory.

1. Jeśli nie masz jeszcze aplikacji do użycia, uruchom poniższy przykład, aby utworzyć aplikację.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Hasło używane podczas tworzenia aplikacji powinno być tym samym hasłem, które zostało utworzone wcześniej podczas zapisywania pliku klucza.

1. W witrynie Azure portal wybierz pozycję **Subskrypcje**. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz pozycję **Kontrola dostępu (IAM)**.

    ![Funkcje IAM][functions9]

1. Wybierz konto, którego chcesz użyć, a następnie wybierz pozycję **Właściwości**. Skopiuj identyfikator aplikacji.

    ![Identyfikator aplikacji funkcji][functions10]

#### <a name="azuretenant"></a>AzureTenant (AzureTenant)

Uzyskaj identyfikator dzierżawy, uruchamiając następujący przykład programu PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Wartość zmiennej środowiskowej AzureCredPassword jest wartością, którą można uzyskać z uruchamiania następującego przykładu programu PowerShell. W tym przykładzie jest ten sam, który jest wyświetlany w poprzedniej sekcji **zaszyfrowane poświadczenia.** Wartość, która jest potrzebna jest `$Encryptedpassword` dane wyjściowe zmiennej.  Jest to hasło główne usługi, które zostały zaszyfrowane przy użyciu skryptu programu PowerShell.

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

### <a name="store-the-environment-variables"></a>Przechowywanie zmiennych środowiskowych

1. Przejdź do aplikacji funkcji. Następnie wybierz pozycję **Ustawienia** > aplikacji Funkcja**Konfigurowanie ustawień aplikacji**.

    ![Konfigurowanie ustawień aplikacji][functions11]

1. Dodaj zmienne środowiskowe i ich wartości do ustawień aplikacji, a następnie wybierz pozycję **Zapisz**.

    ![Ustawienia aplikacji][functions12]

### <a name="add-powershell-to-the-function"></a>Dodawanie programu PowerShell do funkcji

Nadszedł czas, aby nawiązać połączenia do Obserwatora sieciowego z poziomu funkcji platformy Azure. W zależności od wymagań implementacja tej funkcji może się różnić. Jednak ogólny przepływ kodu jest następujący:

1. Parametry wejściowe procesu.
2. Kwerenda istniejących przechwytuje pakiet, aby zweryfikować limity i rozwiązać konflikty nazw.
3. Utwórz przechwytywanie pakietów z odpowiednimi parametrami.
4. Okresowo przechwyć pakiety sondowania, aż do jego ukończenia.
5. Powiadom użytkownika, że sesja przechwytywania pakietów została ukończona.

Poniższy przykład to kod programu PowerShell, który może być używany w funkcji. Istnieją wartości, które należy zastąpić dla **subscriptionId**, **resourceGroupName**i **storageAccountName**.

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
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

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
#### <a name="retrieve-the-function-url"></a>Pobieranie adresu URL funkcji 
1. Po utworzeniu funkcji skonfiguruj alert tak, aby wywołał adres URL skojarzony z funkcją. Aby uzyskać tę wartość, skopiuj adres URL funkcji z aplikacji funkcji.

    ![Znajdowanie adresu URL funkcji][functions13]

2. Skopiuj adres URL funkcji aplikacji funkcyjnej.

    ![Kopiowanie adresu URL funkcji][2]

Jeśli potrzebujesz właściwości niestandardowych w ładunku żądania POST elementu webhook, zapoznaj się z konfigurowaniem elementu [webhook w alertie metryki platformy Azure.](../azure-monitor/platform/alerts-webhooks.md)

## <a name="configure-an-alert-on-a-vm"></a>Konfigurowanie alertu na maszynie wirtualnej

Alerty można skonfigurować tak, aby powiadamiały osoby, gdy określona metryka przekroczy próg, który jest do niej przypisany. W tym przykładzie alert znajduje się w segmentach TCP, które są wysyłane, ale alert może być wyzwalany dla wielu innych metryk. W tym przykładzie alert jest skonfigurowany do wywoływania elementu webhook do wywołania funkcji.

### <a name="create-the-alert-rule"></a>Tworzenie reguły alertu

Przejdź do istniejącej maszyny wirtualnej, a następnie dodaj regułę alertu. Bardziej szczegółową dokumentację dotyczącą konfigurowania alertów można znaleźć w [temacie Tworzenie alertów w usłudze Azure Monitor dla usług platformy Azure — witryna Azure portal.](../monitoring-and-diagnostics/insights-alerts-portal.md) Wprowadź następujące wartości w bloku **reguły alertu,** a następnie wybierz przycisk **OK**.

  |**Ustawienie** | **Wartość** | **Szczegóły** |
  |---|---|---|
  |**Nazwa**|TCP_Segments_Sent_Exceeded|Nazwa reguły alertu.|
  |**Opis**|Wysłane segmenty TCP przekroczyły próg|Opis reguły alertu.|
  |**Metryka**|Wysłane segmenty TCP| Metryka używana do wyzwalania alertu. |
  |**Warunek**|Większe niż| Warunek do użycia podczas oceny metryki.|
  |**Próg**|100| Wartość metryki, która wyzwala alert. Ta wartość powinna być ustawiona na prawidłową wartość dla środowiska.|
  |**Okres**|W ciągu ostatnich pięciu minut| Określa okres, w którym ma być wyszukywać próg na metryki.|
  |**Webhook**|[adres URL elementu webhook z aplikacji funkcji]| Adres URL elementu webhook z aplikacji funkcji, który został utworzony w poprzednich krokach.|

> [!NOTE]
> Metryka segmentów TCP nie jest domyślnie włączona. Dowiedz się więcej o włączaniu dodatkowych metryk, odwiedzając pozycję [Włącz monitorowanie i diagnostykę.](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)

## <a name="review-the-results"></a>Sprawdzanie wyników

Po kryteriach wyzwalania alertu tworzone jest przechwytywanie pakietów. Przejdź do funkcji Obserwator sieci, a następnie wybierz pozycję **Przechwytywanie pakietów**. Na tej stronie można wybrać łącze do pliku przechwytywania pakietów, aby pobrać przechwytywanie pakietów.

![Wyświetlanie przechwytywania pakietów][functions14]

Jeśli plik przechwytywania jest przechowywany lokalnie, można go pobrać, logując się do maszyny wirtualnej.

Aby uzyskać instrukcje dotyczące pobierania plików z kont magazynu platformy Azure, zobacz [Wprowadzenie do usługi Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Innym narzędziem, którego można użyć, jest [Eksplorator magazynu](https://storageexplorer.com/).

Po pobraniu przechwytywania można go wyświetlić za pomocą dowolnego narzędzia, które może odczytać plik **.cap.** Poniżej znajdują się linki do dwóch z tych narzędzi:

- [Analizator komunikatów firmy Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
- [Wireshark](https://www.wireshark.org/)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyświetlić przechwytywanie pakietów, odwiedzając [analizę przechwytywania pakietów za pomocą narzędzia Wireshark](network-watcher-deep-packet-inspection.md).


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
