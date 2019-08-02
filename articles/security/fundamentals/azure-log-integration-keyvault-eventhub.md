---
title: Integrowanie dzienników z Azure Key Vault przy użyciu Event Hubs | Microsoft Docs
description: Samouczek, który zawiera kroki niezbędne do udostępnienia dzienników Key Vault SIEM za pomocą Azure Log Integration
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 01ab4f6f0535f137c7ffeb99c36ecd1e831de6f7
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727664"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Samouczek Azure Log Integration: Przetwarzanie Azure Key Vault zdarzeń przy użyciu Event Hubs

>[!IMPORTANT]
> Funkcja integracja z usługą Azure log zostanie zaniechana przez 06/15/2019. Pliki do pobrania AzLog zostały wyłączone w dniu 27 czerwca 2018. Aby uzyskać wskazówki na temat tego, co należy zrobić, przejrzyj temat publikowanie [za pomocą usługi Azure monitor w celu integracji z narzędziami Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Za pomocą Azure Log Integration można pobrać zarejestrowane zdarzenia i udostępnić je w systemie informacji zabezpieczeń i zarządzania zdarzeniami (SIEM). W tym samouczku przedstawiono przykład sposobu użycia Azure Log Integration do przetwarzania dzienników uzyskanych za pomocą usługi Azure Event Hubs.

Preferowaną metodą integrowania dzienników platformy Azure jest użycie łącznika Azure Monitor dostawcy SIEM i postępując zgodnie z tymi [instrukcjami](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Jeśli jednak dostawca SIEM nie udostępnia łącznika do Azure Monitor, można użyć Azure Log Integration jako rozwiązania tymczasowego (jeśli SIEM jest obsługiwany przez Azure Log Integration) do momentu udostępnienia takiego łącznika.

 
Skorzystaj z tego samouczka, aby zapoznać się ze sposobem, w jaki Azure Log Integration i Event Hubs współdziałać, wykonując przykładowe kroki i zrozumieć, jak każdy krok obsługuje rozwiązanie. Następnie możesz skorzystać z informacji podanych tutaj, aby utworzyć własne kroki do obsługi unikatowych wymagań firmy.

> [!WARNING]
> Kroki i polecenia w tym samouczku nie są przeznaczone do kopiowania i wklejania. Są to tylko przykłady. Nie należy używać poleceń programu PowerShell "AS IS" w środowisku działającym na żywo. Należy je dostosować w oparciu o unikatowe środowisko.


Ten samouczek przeprowadzi Cię przez proces podejmowania Azure Key Vault działania zarejestrowanego w centrum zdarzeń i udostępnienia go jako plików JSON w systemie SIEM. Następnie można skonfigurować system SIEM do przetwarzania plików JSON.

>[!NOTE]
>Większość kroków opisanych w tym samouczku obejmuje konfigurowanie magazynów kluczy, kont magazynu i centrów zdarzeń. Konkretne Azure Log Integration kroki znajdują się na końcu tego samouczka. Nie wykonuj tych kroków w środowisku produkcyjnym. Są one przeznaczone tylko do środowiska laboratoryjnego. Kroki należy dostosować przed ich użyciem w środowisku produkcyjnym.

Informacje zawarte w tym sposobie ułatwiają zrozumienie powodów związanych z każdym krokiem. Linki do innych artykułów dają więcej szczegółowych informacji na temat niektórych tematów.

Aby uzyskać więcej informacji na temat usług, które zawiera ten samouczek, zobacz: 

- [Usługa Azure Key Vault](/azure/key-vault/key-vault-whatis)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Azure Log Integration](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Konfiguracja początkowa

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w tym artykule potrzebne są następujące elementy:

* Subskrypcja i konto platformy Azure dla tej subskrypcji z uprawnieniami administratora. Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).
 
* System z dostępem do Internetu, który spełnia wymagania dotyczące instalowania Azure Log Integration. System może znajdować się w usłudze w chmurze lub hostowanej lokalnie.

* Azure Log Integration zainstalowane. Aby go zainstalować:

   a. Użyj Pulpit zdalny, aby nawiązać połączenie z systemem wymienionym w kroku 2.   
   b. Skopiuj Instalatora Azure Log Integration do systemu. c. Uruchom Instalatora i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.

* Jeśli postanowisz informacje telemetryczne, pozostaw zaznaczone pole wyboru. Jeśli nie wysyłasz informacji o użyciu do firmy Microsoft, wyczyść to pole wyboru.

   Aby uzyskać więcej informacji na temat Azure Log Integration i sposobu ich instalowania, zobacz [Azure log Integration z rejestrowaniem Diagnostyka Azure i przekazywaniem zdarzeń systemu Windows](azure-log-integration-get-started.md).

* Najnowsza wersja programu PowerShell.

   Jeśli masz zainstalowany system Windows Server 2016, masz co najmniej program PowerShell 5,0. Jeśli używasz innej wersji systemu Windows Server, być może masz zainstalowaną wcześniejszą wersję programu PowerShell. Możesz sprawdzić wersję, wprowadzając ```get-host``` okno programu PowerShell. Jeśli nie masz zainstalowanego programu PowerShell 5,0, możesz [go pobrać](https://www.microsoft.com/download/details.aspx?id=50395).

   Po zainstalowaniu co najmniej programu PowerShell w wersji 5,0 możesz wykonać czynności opisane w temacie [instalowanie Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Tworzenie elementów infrastruktury pomocniczej

1. Otwórz okno programu PowerShell z podwyższonym poziomem uprawnień i przejdź do **folderu C:\Program Files\Microsoft Azure log Integration**.
1. Zaimportuj polecenia cmdlet AzLog, uruchamiając skrypt LoadAzLogModule. ps1. `.\LoadAzLogModule.ps1` Wprowadź polecenie. (Zwróć uwagę na ". \" w tym poleceniu). Powinny zostać wyświetlone informacje podobne do następujących:</br>

   ![Lista załadowanych modułów](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. `Connect-AzAccount` Wprowadź polecenie. W oknie logowania wprowadź informacje o poświadczeniach dla subskrypcji, która będzie używana w tym samouczku.

   >[!NOTE]
   >Jeśli po raz pierwszy logujesz się do platformy Azure z tej maszyny, zobaczysz komunikat o umożliwieniu firmie Microsoft zbierać dane dotyczące użycia programu PowerShell. Zalecamy włączenie tej kolekcji danych, ponieważ będzie ona używana do ulepszania Azure PowerShell.

1. Po pomyślnym uwierzytelnieniu użytkownik jest zalogowany. Zanotuj identyfikator subskrypcji i nazwę subskrypcji, ponieważ będą one potrzebne do wykonania dalszych kroków.

1. Utwórz zmienne do przechowywania wartości, które będą używane później. Wprowadź każdy z poniższych wierszy programu PowerShell. Może być konieczne dostosowanie wartości, aby odpowiadały danemu środowisku.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(Nazwa subskrypcji może się różnić. Można go zobaczyć jako część danych wyjściowych poprzedniego polecenia.
    - ```$location = 'West US'```(Ta zmienna zostanie użyta do przekazania lokalizacji, w której powinny zostać utworzone zasoby. Tę zmienną można zmienić na dowolną dowolnie wybraną lokalizację.
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(Nazwa może być dowolna, ale powinna zawierać tylko małe litery i cyfry).
    - ```$storageName = $name```(Ta zmienna zostanie użyta dla nazwy konta magazynu).
    - ```$rgname = $name```(Ta zmienna zostanie użyta dla nazwy grupy zasobów).
    - ```$eventHubNameSpaceName = $name```(Jest to nazwa przestrzeni nazw centrum zdarzeń).
1. Określ subskrypcję, z którą będziesz pracować:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Utwórz grupę zasobów:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Jeśli wprowadzisz `$rg` w tym momencie, powinny pojawić się dane wyjściowe podobne do tego zrzutu ekranu:

   ![Dane wyjściowe po utworzeniu grupy zasobów](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Utwórz konto magazynu, które będzie używane do śledzenia informacji o stanie:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Utwórz przestrzeń nazw centrum zdarzeń. Jest to wymagane do utworzenia centrum zdarzeń.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Pobierz identyfikator reguły, który będzie używany z dostawcą usługi Insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Pobierz wszystkie możliwe lokalizacje platformy Azure i Dodaj nazwy do zmiennej, której można użyć w późniejszym kroku:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Jeśli wprowadzisz `$locations` w tym momencie, zobaczysz nazwy lokalizacji bez dodatkowych informacji zwracanych przez polecenie Get-AzLocation.
1. Utwórz profil dziennika Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Aby uzyskać więcej informacji na temat profilu dziennika platformy Azure, zobacz [Omówienie dziennika aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Podczas próby utworzenia profilu dziennika może zostać wyświetlony komunikat o błędzie. Następnie można zapoznać się z dokumentacją dotyczącą Get-AzLogProfile i Remove-AzLogProfile. Jeśli uruchomisz polecenie Get-AzLogProfile, zobaczysz informacje o profilu dziennika. Istniejący profil dziennika można usunąć, wprowadzając ```Remove-AzLogProfile -name 'Log Profile Name'``` polecenie.
>
>![Błąd profilu Menedżer zasobów](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Utwórz magazyn kluczy:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Skonfiguruj rejestrowanie dla magazynu kluczy:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Generowanie dziennika aktywności

Żądania należy wysłać do Key Vault w celu wygenerowania działania dziennika. Akcje, takie jak generacja kluczy, przechowywanie wpisów tajnych lub odczytywanie kluczy tajnych z Key Vault, utworzy wpisy dziennika.

1. Wyświetl bieżące klucze magazynu:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generuj nowy **klucz2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Ponownie Wyświetl klucze i sprawdź, czy **klucz2** ma inną wartość:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Ustawianie i odczytywanie wpisu tajnego w celu wygenerowania dodatkowych wpisów dziennika:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Zwrócony klucz tajny](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurowanie Azure Log Integration

Po skonfigurowaniu wszystkich elementów wymaganych do Key Vault rejestrowania w centrum zdarzeń należy skonfigurować Azure Log Integration:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Uruchom polecenie AzLog dla każdego centrum zdarzeń:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Po minucie lub w celu uruchomienia ostatnich dwóch poleceń powinny zostać wyświetlone pliki JSON, które są generowane. Można potwierdzić, że przez monitorowanie katalogu **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Kolejne kroki

- [Azure Log Integration często zadawane pytania](azure-log-integration-faq.md)
- [Wprowadzenie do Azure Log Integration](azure-log-integration-get-started.md)
- [Integrowanie dzienników z zasobów platformy Azure z systemami SIEM](azure-log-integration-overview.md)
