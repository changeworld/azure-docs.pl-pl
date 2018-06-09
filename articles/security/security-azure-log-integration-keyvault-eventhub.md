---
title: Integracja dzienniki z usługi Azure Key Vault za pomocą usługi Event Hubs | Dokumentacja firmy Microsoft
description: Samouczek, który zawiera kroki niezbędne do udostępnienia dzienników usługi Key Vault SIEM za pomocą integracji dziennika Azure
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 06/07/2018
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 3de876a8d06a52412bbbfd3ad922c2c4c6d8ec37
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235961"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Samouczek usługi Azure integracji dziennika: zdarzeń procesu usługi Azure Key Vault za pomocą usługi Event Hubs

>[!IMPORTANT]
> Funkcja integracji Azure dziennika zostaną wycofane przez 2019-06/01. Pliki do pobrania AzLog zostanie wyłączone przez 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu post [monitor Azure używana do integracji z narzędzia SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integracja dziennika Azure służy do pobierania zarejestrowane zdarzenia i udostępnić je do systemu zarządzania (SIEM) informacje i zdarzeń zabezpieczeń. W tym samouczku przedstawiono sposób integracji dziennika Azure może służyć do przetworzenia dzienniki, które zostały zakupione w ramach usługi Azure Event Hubs.

Preferowaną metodą integrowanie dzienników Azure jest za pomocą łącznika Azure Monitor dostawcą SIEM i wykonując te [instrukcje](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Jednak jeśli dostawcą SIEM nie łącznika do monitorowania Azure, można korzystać z usługi Azure dziennika integracji jako rozwiązanie tymczasowe (Jeśli system SIEM jest obsługiwana przez integrację dziennika Azure) do czasu udostępnienia łącznika programu.

 
Użyj tego samouczka, aby poznać sposób integracji dziennika Azure i usługi Event Hubs współpracują ze sobą następujące przykładowe kroki i zrozumienie, jak każdy krok obsługuje rozwiązania. Następnie może potrwać co kiedy znasz już w tym miejscu można utworzyć własne kroki, aby obsługiwać unikatowe wymagania firmy.

>[!WARNING]
Kroków i poleceń, w tym samouczku nie mają zostać skopiowane i wklejone. Są one tylko przykładowe. Nie należy używać poleceń programu PowerShell "w jakim jest" w środowisku na żywo. Należy dostosować oparte na środowisku unikatowy.


Ten samouczek przeprowadzi Cię przez proces podejmowania działania usługi Azure Key Vault zalogowany do Centrum zdarzeń i udostępniając ją jako pliki w formacie JSON SIEM system. Następnie można skonfigurować system SIEM do przetworzenia plików JSON.

>[!NOTE]
>Większość czynności w tym samouczku obejmują konfigurowanie magazynów kluczy konta magazynu i usługi event hubs. Kolejne kroki integracji dziennika Azure znajdują się na końcu tego samouczka. Nie należy wykonywać następujące czynności w środowisku produkcyjnym. Są one przeznaczone dla środowiska laboratoryjnego. Należy dostosować kroki przed ich użyciem w środowisku produkcyjnym.

Informacje podane na bieżąco pomaga w zrozumieniu przyczynami każdego kroku. Łącza do innych artykułów nadaj bardziej szczegółowo w pewnych tematów.

Aby uzyskać więcej informacji dotyczących usług, które wymienia tego samouczka zobacz: 

- [Usługa Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integracja z dzienników Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Początkowej konfiguracji

Aby móc zakończyć kroki opisane w tym artykule, potrzebne są następujące elementy:

1. Subskrypcja platformy Azure i konta dla tej subskrypcji z uprawnieniami administratora. Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).
 
2. System z dostępem do Internetu, który spełnia wymagania dotyczące instalowania integracji dziennika Azure. System może znajdować się na usługi w chmurze lub obsługiwanego lokalnie.

3. [Integracja z usługą Azure dziennika](https://www.microsoft.com/download/details.aspx?id=53324) zainstalowane. Aby zainstalować go:

   a. Podłącz do sieci wspomnianego w kroku 2 za pomocą pulpitu zdalnego.   
   b. Skopiuj Instalatora integracji dziennika Azure do systemu. Możesz [pobierane pliki instalacyjne](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Uruchamianie Instalatora i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.   
   d. Jeśli będzie zawierał informacje telemetryczne, pozostaw zaznaczone pole wyboru. Jeśli użytkownik nie wysłać informacje o użyciu do firmy Microsoft, wyczyść pole wyboru.
   
   Aby uzyskać więcej informacji na temat integracji dziennika Azure i sposobu jego instalacji, zobacz [Azure dziennika Integracja z usługą rejestrowania diagnostyki Azure i funkcji przekazywania zdarzeń systemu Windows](security-azure-log-integration-get-started.md).

4. Najnowsza wersja programu PowerShell.
 
   Jeśli masz zainstalowany program Windows Server 2016, musisz mieć co najmniej PowerShell 5.0. Jeśli używasz wersji systemu Windows Server może być starsza wersja programu PowerShell jest zainstalowane. Sprawdź wersję, wprowadzając ```get-host``` w oknie programu PowerShell. Jeśli nie masz programu PowerShell 5.0 zainstalowany, możesz [go pobrać](https://www.microsoft.com/download/details.aspx?id=50395).

   Po utworzeniu co najmniej PowerShell 5.0, możesz przejść do zainstalowania najnowszej wersji:
   
   a. W oknie programu PowerShell, wprowadź ```Install-Module Azure``` polecenia. Wykonaj kroki instalacji.    
   b. Wprowadź ```Install-Module AzureRM``` polecenia. Wykonaj kroki instalacji.

   Aby uzyskać więcej informacji, zobacz [instalacji programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Utwórz elementy infrastruktury obsługi

1. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień i przejdź do **C:\Program Files\Microsoft Azure dziennika integracji**.
2. Zaimportuj polecenia cmdlet AzLog przez uruchomienie skryptu LoadAzLogModule.ps1. Wprowadź `.\LoadAzLogModule.ps1` polecenia. (Uwaga ". \" w tym poleceniu.) Powinny zostać wyświetlone informacje podobne do następujących:</br>

   ![Listę załadowanych modułów](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Wprowadź `Connect-AzureRmAccount` polecenia. W oknie logowania wprowadź informacji o poświadczeniach dla subskrypcji, która będzie używana w tym samouczku.

   >[!NOTE]
   >Jeśli po raz pierwszy jest logowanie do platformy Azure z tego komputera, pojawi się komunikat o Microsoft do zbierania danych użycia programu PowerShell. Firma Microsoft zaleca włączyć tej kolekcji danych, ponieważ będzie służyć do poprawienia programu Azure PowerShell.

4. Po pomyślnym uwierzytelnieniu użytkownik zalogował się i pojawi się informacja na poniższym zrzucie ekranu. Zanotuj identyfikator subskrypcji i Nazwa subskrypcji, ponieważ konieczne będzie ich zakończenie kolejnych krokach.

   ![Okno programu PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Utwórz zmienne do przechowywania wartości, które będą używane później. Wprowadź każdy z poniższych wierszy programu PowerShell. Może być konieczne dostosowanie wartości do danego środowiska.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (Nazwa Twojej subskrypcji mogą się różnić. Można to sprawdzić w ramach danych wyjściowych poprzednie polecenie.)
    - ```$location = 'West US'``` (Ta zmienna będzie służyć do przekazywania lokalizacji, w którym ma zostać utworzony zasobów. Można zmienić tej zmiennej można w dowolnej lokalizacji wybrane.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Nazwa może być dowolna, ale powinien zawierać tylko małe litery i cyfry).
    - ``` $storageName = $name``` (Ta zmienna będzie można używać nazwy konta magazynu).
    - ```$rgname = $name ``` (Ta zmienna będzie służyć do nazwy grupy zasobów.)
    - ``` $eventHubNameSpaceName = $name``` (To jest nazwa przestrzeni nazw Centrum zdarzeń).
6. Określ subskrypcję, która będzie działać z:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Utwórz grupę zasobów:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Po wprowadzeniu `$rg` w tym momencie powinny być widoczne dane wyjściowe podobne do tego zrzutu ekranu:

   ![Dane wyjściowe po utworzeniu grupy zasobów](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Utwórz konto magazynu, która będzie służyć do śledzenia informacji o stanie:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Tworzenie przestrzeni nazw Centrum zdarzeń. Jest to wymagane do tworzenia Centrum zdarzeń.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Pobierz identyfikator reguły, który będzie używany z dostawcą insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Pobierz wszystkie możliwe lokalizacji platformy Azure i Dodaj nazwy do zmiennej używanej w kolejnym kroku:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Po wprowadzeniu `$locations` w tym momencie wyświetlić nazwy lokalizacji bez dodatkowych informacji zwrócony przez Get-AzureRmLocation.
12. Utwórz profil usługi Azure Resource Manager dziennika: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Aby uzyskać więcej informacji o profilu dzienników Azure, zobacz [Omówienie dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> Podczas próby utworzenia profilu dziennika, może być wyświetlony komunikat o błędzie. Następnie zapoznanie się z dokumentacją Get AzureRmLogProfile i Usuń AzureRmLogProfile. Po uruchomieniu Get AzureRmLogProfile zobaczysz informacje o profilu dziennika. Można usunąć istniejącego profilu dziennika wprowadzając ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` polecenia.
>
>![Błąd profilu usługi Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Tworzenie magazynu kluczy:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Konfiguruj rejestrowanie dla magazynu kluczy:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Wygenerować dziennik aktywności

Żądania należy wysłać do magazynu kluczy można wygenerować dziennik aktywności. Generowania kluczy, przechowywanie kluczy tajnych, takie jak akcje lub odczytu klucze tajne z magazynu kluczy zostanie utworzona wpisy dziennika.

1. Wyświetl bieżące klucze magazynu:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Generuj nową **klucz2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Wyświetlaj ponownie klucze i zobacz, który **klucz2** zawiera inną wartość:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Ustaw i klucz tajny, aby wygenerować wpisy dziennika dodatkowych do odczytu:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Zwrócony tajny](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurowanie integracji dzienników Azure

Teraz, gdy skonfigurowano wszystkie wymagane elementy mają Key Vault logowania do Centrum zdarzeń, należy skonfigurować integrację dziennika Azure:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Uruchom polecenie AzLog dla każdego Centrum zdarzeń:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Po minucie lub sposób uruchamiania poleceń, ostatnie dwa powinna zostać wyświetlona generowaną pliki w formacie JSON. Możesz sprawdzić, który przez monitorowanie katalogu **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Kolejne kroki

- [Integracja dzienników Azure — często zadawane pytania](security-azure-log-integration-faq.md)
- [Wprowadzenie do integracji dziennika Azure](security-azure-log-integration-get-started.md)
- [Integrowanie dzienników z zasobów platformy Azure z systemów SIEM](security-azure-log-integration-overview.md)
