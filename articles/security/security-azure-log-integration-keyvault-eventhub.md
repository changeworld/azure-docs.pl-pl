---
title: Integrowanie dzienników z usługi Azure Key Vault przy użyciu usługi Event Hubs | Dokumentacja firmy Microsoft
description: Samouczek, który zawiera niezbędne instrukcje, aby udostępnić dzienników usługi Key Vault do rozwiązania SIEM przy użyciu usługi Azure Log Integration
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 5614cc6fa01ddd10d670fdf429051a8e024550fc
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298200"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Samouczek dotyczący usługi Azure Log Integration: Przetwarzanie zdarzeń usługi Azure Key Vault przy użyciu usługi Event Hubs

>[!IMPORTANT]
> Funkcja integracji dziennika Azure zostaną wycofane 06/15/2019 r. Pliki do pobrania AzLog zostały wyłączone w dniu 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu wpis [użycia w usłudze Azure monitor do integracji z narzędziami rozwiązania SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integracja dzienników platformy Azure umożliwia pobieranie zarejestrowanych zdarzeń i udostępnić je zabezpieczeń informacjami i zdarzeniami (SIEM) systemu zarządzania. W tym samouczku przedstawiono przykład sposobu integracji dzienników platformy Azure mogą być używane do przetwarzania dzienników, które są nabywane za pośrednictwem usługi Azure Event Hubs.

Preferowaną metodą integracji dzienników platformy Azure jest za pomocą łącznika usługi Azure Monitor z dostawcą rozwiązania SIEM, a także następujące [instrukcje](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Jednak jeśli dostawcy rozwiązania SIEM nie udostępnia łącznik do usługi Azure Monitor, może być można używać integracji dzienników platformy Azure jako rozwiązanie tymczasowe (Jeśli system SIEM jest obsługiwany przez usługi Azure Log Integration) do czasu udostępnienia łącznika programu.

 
Użyj tego samouczka, zapoznaniu się z jak Azure Log Integration i centrów zdarzeń współpracują ze sobą, zgodnie z krokami przykład i zrozumienie, jak każdy krok obsługuje rozwiązania. Następnie można podjąć, co wykorzystasz zdobyte umiejętności w tym miejscu utworzyć własne instrukcje obsługuje unikatowe wymagania Twojej firmy.

> [!WARNING]
> Kroki i polecenia w tym samouczku nie są przeznaczone do można kopiować i wklejać. Są one jedynie przykładowe. Nie należy używać poleceń programu PowerShell "w jakim jest" w środowisku na żywo. Należy dostosować je w zależności od używanego środowiska unikatowy.


Ten samouczek przeprowadzi Cię przez proces podejmowania działań usługi Azure Key Vault zalogowany do Centrum zdarzeń i udostępniając je jako pliki w formacie JSON do systemu SIEM. Następnie należy skonfigurować systemu SIEM do przetwarzania plików JSON.

>[!NOTE]
>Większość czynności w tym samouczku obejmują konfigurowanie magazynów kluczy, kont magazynu i centrów zdarzeń. Określone kroki integracji dzienników Azure znajdują się na końcu tego samouczka. Nie należy wykonywać następujące kroki w środowisku produkcyjnym. Są one przeznaczone dla środowiska laboratoryjnego. Należy dostosować kroki przed ich użyciem w środowisku produkcyjnym.

Informacje podane w drodze ułatwia zrozumienie przyczyn każdego kroku. Łącza do innych artykułów zapewniają bardziej szczegółowo określonych tematów.

Aby uzyskać więcej informacji o usługach, które przewodnim tego samouczka zobacz: 

- [Usługa Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integracja dzienników platformy Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Konfiguracja początkowa

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w tym artykule potrzebne są następujące elementy:

* Subskrypcja platformy Azure i konto dla tej subskrypcji z uprawnieniami administratora. Jeśli nie masz subskrypcji, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/).
 
* System z dostępem do Internetu, który spełnia wymagania dotyczące instalowania usługi Azure Log Integration. System może być w usłudze w chmurze lub hostowanych lokalnie.

* Integracja dzienników platformy Azure zainstalowany. Aby je zainstalować:

   a. Za pomocą pulpitu zdalnego połączenia z systemem wspomnianego w kroku 2.   
   b. Skopiuj Instalator integracji dzienników platformy Azure do systemu. c. Uruchom Instalatora i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.

* Jeśli podasz informacje telemetryczne, pozostaw zaznaczone pole wyboru. Jeśli użytkownik nie wysłać informacje o użyciu do firmy Microsoft, usuń zaznaczenie pola wyboru.

   Aby uzyskać więcej informacji o integracji dzienników platformy Azure i sposobu jego instalacji, zobacz [Azure Log Integration rejestrowania diagnostyki Azure i funkcji przekazywania zdarzeń Windows](security-azure-log-integration-get-started.md).

* Najnowsza wersja programu PowerShell.

   Jeśli masz zainstalowany program Windows Server 2016, a następnie masz co najmniej PowerShell 5.0. Jeśli używasz wersji systemu Windows Server, Niewykluczone, że starszą wersję programu PowerShell jest zainstalowane. Możesz sprawdzić wersję, wprowadzając ```get-host``` w oknie programu PowerShell. Jeśli nie masz PowerShell 5.0 zainstalowany, możesz to zrobić [ją pobrać](https://www.microsoft.com/download/details.aspx?id=50395).

   Po utworzeniu co najmniej PowerShell 5.0, możesz przejść do zainstalowania najnowszej wersji, postępując zgodnie z instrukcjami wyświetlanymi w [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Utwórz elementy infrastruktury obsługi

1. Otwórz okno programu PowerShell z podwyższonym i przejdź do **C:\Program Files\Microsoft Azure Log Integration**.
1. Zaimportuj polecenia cmdlet AzLog, uruchamiając skrypt LoadAzLogModule.ps1. Wprowadź `.\LoadAzLogModule.ps1` polecenia. (Zwróć uwagę ". \" tego polecenia.) Powinny zostać wyświetlone informacje podobne do następujących:</br>

   ![Listę załadowanych modułów](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Wprowadź `Connect-AzAccount` polecenia. W oknie logowania wprowadź informacji o poświadczeniach dla subskrypcji, która będzie używana na potrzeby tego samouczka.

   >[!NOTE]
   >Jeśli jest to, w przypadku logowania do platformy Azure z tego komputera po raz pierwszy, zostanie wyświetlony komunikat o firmy Microsoft na zbieranie danych użycia programu PowerShell. Firma Microsoft zaleca, włączyć zbierania danych, ponieważ będzie służyć do poprawy programu Azure PowerShell.

1. Po pomyślnym uwierzytelnieniu użytkownik jest zalogowany. Zanotuj identyfikator subskrypcji i nazwę subskrypcji, ponieważ będą potrzebne do wykonania dalszych krokach.

1. Utwórz zmienne do przechowywania wartości, które będą używane później. Wprowadź każdy z poniższych wierszy programu PowerShell. Może być konieczne dostosowanie wartości pod kątem danego środowiska.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (Nazwa subskrypcji usługi mogą się różnić. Można go było wyświetlić jako część danych wyjściowych poprzedniego polecenia.)
    - ```$location = 'West US'``` (Ta zmienna będzie służyć do przekazywania lokalizacji, w którym utworzone zasoby. Można zmienić tej zmiennej do dowolnej lokalizacji wybranej.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (Nazwa może zawierać wszystko, ale powinien on zawierać tylko małe litery i cyfry).
    - ```$storageName = $name``` (Ta zmienna będzie używana dla nazwy konta magazynu).
    - ```$rgname = $name``` (Ta zmienna będzie służyć jako nazwę grupy zasobów.)
    - ```$eventHubNameSpaceName = $name``` (Jest to nazwa przestrzeni nazw Centrum zdarzeń).
1. Określ subskrypcję, która będzie pracował z:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Utwórz grupę zasobów:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Jeśli wprowadzasz `$rg` w tym momencie powinien zostać wyświetlony dane wyjściowe podobne do tego zrzutu ekranu:

   ![Dane wyjściowe po utworzeniu grupy zasobów](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Utwórz konto magazynu, która będzie służyć do śledzenia informacji o stanie:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Utwórz przestrzeń nazw Centrum zdarzeń. Jest to wymagane do tworzenia Centrum zdarzeń.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Pobierz identyfikator reguły, używany przy użyciu dostawcy szczegółowe informacje:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Pobierz wszystkie możliwe lokalizacje platformy Azure i dodać nazwy do zmiennej używanej w kolejnym kroku:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Jeśli wprowadzasz `$locations` w tym momencie wyświetlić nazwy lokalizacji bez dodatkowych informacji zwracany przez Get-AzLocation.
1. Tworzenie profilu dziennika usługi Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Aby uzyskać więcej informacji o profilu dzienników platformy Azure, zobacz [Przegląd dziennika aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Możesz otrzymać komunikat o błędzie podczas próby utworzenia profilu dziennika. Następnie możesz przejrzeć dokumentację Get AzLogProfile i Usuń AzLogProfile. Jeśli uruchamiasz Get AzLogProfile, zobaczysz informacje o profilu dziennika. Możesz usunąć istniejący profil dziennika, wprowadzając ```Remove-AzLogProfile -name 'Log Profile Name'``` polecenia.
>
>![Błąd profilu usługi Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Tworzenie magazynu kluczy:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Konfigurowanie rejestrowania dla usługi key vault:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Generowanie dziennika aktywności

Żądania należy do wysłania do usługi Key Vault, aby wygenerować dziennik aktywności. Akcje, takie jak Generowanie kluczy, wpisów tajnych oraz ich przechowywania lub odczytywanie wpisów tajnych z usługi Key Vault spowoduje utworzenie wpisy dziennika.

1. Wyświetl bieżące klucze magazynu:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generuj nowy **klucz2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Ponownie wyświetlić klucze i zobacz, że **klucz2** zawiera różne wartości:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Ustawianie i odczytywanie wpisu tajnego, który można wygenerować wpisów dziennika dodatkowe:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Zwrócone wpisu tajnego](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurowanie integracji dzienników platformy Azure

Teraz, gdy skonfigurowano wszystkie wymagane elementy, aby logowanie do Centrum zdarzeń w usłudze Key Vault, należy skonfigurować integracji dzienników Azure:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Uruchom polecenie AzLog dla każdego Centrum zdarzeń:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Po około minucie uruchamiania ostatnich dwóch poleceń powinna zostać wyświetlona generowane pliki w formacie JSON. Można potwierdzić, monitorując katalogu **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Kolejne kroki

- [Integracja dzienników platformy Azure — często zadawane pytania](security-azure-log-integration-faq.md)
- [Rozpoczynanie pracy z usługą Azure Log Integration](security-azure-log-integration-get-started.md)
- [Integrowanie dzienników z zasobów platformy Azure w systemach SIEM](security-azure-log-integration-overview.md)
