---
title: Usługi aplikacji — informacje o wersji 1 update stosu Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji, jeden dla usługi aplikacji Azure stosu, znane problemy i pobierania aktualizacji.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 9391072159f26ae8c7d3eba9f023a8e7d62a4e26
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Usługi aplikacji — informacje o wersji 1 update Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Te informacje o wersji opisano ulepszeń i poprawek w usłudze Azure App Service na Azure stosu Update 1 i znanych problemów. Znane problemy są podzielone na problemy z bezpośrednio do wdrożenia, proces aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Zastosowanie aktualizacji 1802 systemu Azure stosu zintegrowane lub wdrożyć najnowszy zestaw deweloperski stosu Azure przed wdrożeniem usługi Azure App Service.
>
>

## <a name="build-reference"></a>Tworzenie odwołania

Usługa aplikacji Azure stosu Update 1 numer kompilacji jest **69.0.13698.9**

### <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Nowe wdrożenia usługi Azure App Service na stosie Azure wymaga [certyfikat uniwersalny podmiotu trzech](azure-stack-app-service-before-you-get-started.md#get-certificates) ze względu na ulepszenia w taki sposób, w jaki usługa rejestracji Jednokrotnej dla Kudu teraz obsługiwane w usłudze Azure App Service. Nowy temat jest ** *.sso.appservice.<region>.<domainname>.<extension>**
>
>

Zapoznaj się [przed rozpoczęciem pracy dokumentacji](azure-stack-app-service-before-you-get-started.md) przed rozpoczęciem wdrażania.

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Usługa aplikacji Azure na Azure stosu Update 1 zawiera następujące ulepszenia i poprawki:

- **Wysokiej dostępności z usługi Azure App Service** -1802 stosu Azure obciążeń włączone aktualizacje do wdrożenia na fault domen. W związku z tym infrastruktury usługi aplikacji jest w stanie się odporność na uszkodzenia, co zostanie ona wdrożona w domenach awarii. Domyślnie wszystkie nowe wdrożenia usługi Azure App Service ma tę możliwość, jednak wdrożeń ukończone przed 1802 stosu Azure zastosowanych aktualizacji można znaleźć w temacie [dokumentacji domena awarii usługi aplikacji](azure-stack-app-service-fault-domain-update.md)

- **Wdrażanie w istniejącej sieci wirtualnej** — klienci mogą teraz wdrożyć usługi aplikacji na stosie Azure w ramach istniejącej sieci wirtualnej. Wdrażanie w istniejącej sieci wirtualnej umożliwia klientom łączenie się z programu SQL Server i serwer plików, wymaganych do usługi Azure App Service za pośrednictwem portów prywatnych. Podczas wdrażania klientów można wybrać do wdrożenia w istniejącej sieci wirtualnej, jednak [należy utworzyć podsieci do użycia przez usługę App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) przed ich wdrożeniem.

- Aktualizacje **dzierżawa usługi aplikacji, administrator, portali funkcje i narzędzia Kudu**. Spójne z wersją zestawu SDK usługi Azure stosu portalu.

- **Aktualizacje następujące struktury aplikacji i narzędzi**:
    - Dodaje **.Net Core 2.0** obsługuje
    - Dodaje **Node.JS** wersji:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Dodaje **NPM** wersji:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Dodaje **PHP** aktualizacji:
        - 5.6.32
        - 7.0.26 (x86 i x64)
        - 7.1.12 (x86 i x64)
    - Zaktualizowano **Git dla systemu Windows** v 2.14.1
    - Zaktualizowano **Mercurial** do v4.5.0

  - Dodano obsługę **HTTPS tylko** funkcji w funkcji domeny niestandardowe w portalu dzierżawcy usługi aplikacji. 

  - Dodano Weryfikacja połączenia z magazynem w selektorze magazynu niestandardowego dla usługi Azure Functions 

#### <a name="fixes"></a>Poprawki

- Podczas tworzenia pakietu wdrożeniowego w trybie offline, klienci nie będą już przekazywane dostępu komunikat o błędzie podczas otwierania folderu z Instalatora usługi aplikacji

- Rozwiązane problemy podczas pracy funkcji domen niestandardowych w portalu dzierżawcy usługi aplikacji.

- Zapobiegaj klientów przy użyciu nazw zastrzeżonych administratora podczas instalacji

- Wdrożenia usługi App Service z włączoną **przyłączony do domeny** serwera plików

- Ulepszone pobieranie stosu Azure głównego certyfikatu w skrypcie i teraz sprawdzić poprawność certyfikatu głównego w Instalatorze usługi aplikacji.

- Nieprawidłowy stan stały zwracanych do usługi Azure Resource Manager, gdy subskrypcja jest usuwane że zasoby zawarte w przestrzeni nazw Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Znane problemy z procesu wdrażania

- Błędy sprawdzania poprawności certyfikatu

Niektórzy klienci wystąpić problemy podczas dostarczania certyfikatów Instalatora usługi aplikacji podczas wdrażania w systemie zintegrowane z powodu zbyt restrykcyjne weryfikacji w Instalatorze. Instalator usługi aplikacji została ponownie wydana, jeżeli klienci [Pobierz zaktualizowany Instalator](https://aka.ms/appsvconmasinstaller). Jeśli nadal występują problemy dotyczące sprawdzania poprawności certyfikatów przy użyciu zaktualizowanych Instalatora, należy się z pomocą techniczną.

- Problem podczas pobierania certyfikatu głównego stosu Azure z zintegrowany system.

Błąd w Get AzureStackRootCert.ps1 spowodował klientów nie można pobrać certyfikat główny stos Azure podczas wykonywania skryptu na komputerze, na którym nie ma zainstalowany certyfikat główny. Skrypt teraz również opublikowano ponownie, rozwiązywanie tego problemu, a żądania klientów [Pobierz skrypty zaktualizowane Pomocnika](https://aka.ms/appsvconmashelpers). Jeśli nadal występują problemy podczas pobierania certyfikatu głównego z zaktualizowany skrypt, skontaktuj się z pomocą techniczną.

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Nie są znane problemy aktualizacji usługi Azure App Service na Azure stosu Update 1.

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

- Wymiany gniazd nie działa.

Wymiany gniazd lokacji są dzielone w tej wersji. Aby przywrócić funkcjonalność, wykonaj następujące kroki:

1. Modyfikowanie grupy zabezpieczeń sieci ControllersNSG do **Zezwalaj** połączeń pulpitu zdalnego do wystąpień kontrolera usługi aplikacji. Zamień na nazwę grupy zasobów, wdrożonej usługi aplikacji w AppService.local.

    ```powershell
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

2. Przejdź do **CN0-VM** w obszarze maszyn wirtualnych w portalu Azure stosu administratora i **kliknij przycisk Połącz** otworzyć sesję pulpitu zdalnego z wystąpienie kontrolera. Użyj poświadczeń określonych podczas wdrażania usługi aplikacji.
3. Uruchom **programu PowerShell jako Administrator** i uruchom następujący skrypt

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Zamykanie sesji usług pulpitu zdalnego.
5. Przywróć ControllersNSG sieciową grupę zabezpieczeń do **Odmów** połączeń pulpitu zdalnego do wystąpień kontrolera usługi aplikacji. Zamień na nazwę grupy zasobów, wdrożonej usługi aplikacji w AppService.local.

    ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
- Nie można osiągnąć serwera plików, gdy wdrażana jest usługa aplikacji w ramach istniejącej sieci wirtualnej i serwer plików jest dostępna w sieci prywatnej tylko są pracowników.
 
Jeśli wybierzesz do wdrożenia w ramach istniejącej sieci wirtualnej i wewnętrzny adres IP, aby nawiązać połączenie z plików, należy dodać reguły zabezpieczeń dla ruchu wychodzącego, włączanie ruchu SMB między podsieci procesu roboczego i serwera plików. Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:
 * Źródło: wszystkie
 * Zakres portów źródłowych: *
 * Miejsce docelowe: Adresy IP
 * Docelowy zakres adresów IP: zakres adresów IP dla serwera plików
 * Zakres portów docelowych: 445
 * Protokół: TCP
 * Akcja: Zezwalaj
 * Priorytet: 700
 * Nazwa: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Znane problemy dotyczące działania usługi Azure App Service na stosie Azure Administratorzy chmury

Zapoznaj się z dokumentacją w [1802 stosu Azure informacje o wersji](azure-stack-update-1802.md)

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure App Service, zobacz [usłudze Azure App Service na stos Azure omówienie](azure-stack-app-service-overview.md).
- Aby uzyskać więcej informacji dotyczących sposobu przygotowania do wdrożenia usługi aplikacji Azure stosu, zobacz [przed rozpoczęciem pracy z usługi aplikacji Azure stosu](azure-stack-app-service-before-you-get-started.md).
