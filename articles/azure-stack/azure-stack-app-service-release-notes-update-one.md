---
title: Usługa App Service w usłudze Azure Stack update — informacje o wersji 1 | Dokumentacja firmy Microsoft
description: Więcej informacji na temat nowości w aktualizacji, jeden dla usługi App Service w usłudze Azure Stack, znanych problemów i umożliwiające pobranie aktualizacji.
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
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 3d75467da01f0672bb735e01cbd6d7634cdf843e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445241"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Usługa App Service w usłudze Azure Stack update — informacje o wersji 1

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Tych informacjach o wersji opisano ulepszeń i poprawek w usłudze Azure App Service, Azure Stack Update 1 i znanych problemach. Znane problemy są podzielone na problemy z bezpośrednio do wdrożenia, proces aktualizacji i problemy z kompilacją (po instalacji).

> [!IMPORTANT]
> Dotyczą aktualizacji 1802 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem usługi Azure App Service.
>
>

## <a name="build-reference"></a>Dokumentacja kompilowania

App Service na numer kompilacji programu Azure Stack aktualizacji 1 to **69.0.13698.9**

### <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Teraz wymagają nowych wdrożeń usługi Azure App Service w usłudze Azure Stack [certyfikat uniwersalny tematu z trzema](azure-stack-app-service-before-you-get-started.md#get-certificates) ze względu na ulepszenia w taki sposób, w jaki logowanie Jednokrotne do Kudu jest teraz obsługiwane w usłudze Azure App Service. Nowy temat jest  **\*. sso.appservice.\< region\>.\< nazwa_domeny\>.\< rozszerzenie\>**
>
>

Zapoznaj się [dokumentacji przed rozpoczęciem](azure-stack-app-service-before-you-get-started.md) przed rozpoczęciem wdrażania.

### <a name="new-features-and-fixes"></a>Nowe funkcje i poprawki

Usługa Azure App Service w usłudze Azure Stack Update 1 obejmuje następujące ulepszenia i poprawki:

- **Wysoka dostępność usług Azure App Service** — można wdrożyć w różnych obciążeń włączone aktualizacje usługi Azure Stack w wersji 1802 domeny błędów. W związku z tym infrastruktury usługi App Service jest w stanie zapewnić odporność na uszkodzenia, ponieważ zostanie ono wdrożone w domenach błędów. Domyślnie wszystkich nowych wdrożeń usługi Azure App Service ma tę możliwość, jednak wdrożenia zakończone przed Azure Stack w wersji 1802 Trwa stosowanie aktualizacji można znaleźć [dokumentacji domeny błędów usługi App Service](azure-stack-app-service-fault-domain-update.md)

- **Wdrażanie w istniejącej sieci wirtualnej** — klienci mogą teraz wdrażać usługi App Service w usłudze Azure Stack w ramach istniejącej sieci wirtualnej. Wdrażanie w istniejącej sieci wirtualnej umożliwia klientom łączenie się z programu SQL Server i serwer plików, wymagane dla usługi Azure App Service za pośrednictwem portów prywatnych. Podczas wdrażania, klienci mogą wybrać do wdrożenia w istniejącej sieci wirtualnej, jednak [utworzyć podsieci do użycia przez usługę App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) przed ich wdrożeniem.

- Aktualizacje **dzierżawy usługi aplikacji, administrator, portali funkcje i narzędzia Kudu**. Zgodne z wersją zestawu SDK usługi Azure Stack Portal.

- Aktualizacje **środowisko uruchomieniowe usługi Azure Functions** do **v1.0.11388**.

- **Aktualizacje do narzędzi i struktur aplikacji na następujących**:
    - Dodano **.NET Core 2.0** pomocy technicznej
    - Dodano **Node.JS** wersji:
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
    - Dodano **NPM** wersji:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Dodano **PHP** aktualizacji:
        - 5.6.32
        - 7.0.26 (x86 i x64)
        - 7.1.12 (x86 i x64)
    - Zaktualizowano **Git dla Windows** v 2.14.1
    - Zaktualizowano **Mercurial** do v4.5.0

  - Dodano obsługę **tylko HTTPS** funkcji w ramach funkcji domena niestandardowa w portalu dzierżawcy usługi App Service. 

  - Dodano sprawdzanie poprawności połączenia magazynu w selektorze magazynu niestandardowego dla usługi Azure Functions 

#### <a name="fixes"></a>Poprawki

- Podczas tworzenia pakietu wdrożeniowego w trybie offline, klienci nie będą już przekazywane odmowa dostępu komunikat o błędzie podczas otwierania folderu z poziomu Instalatora usługi App Service

- Rozwiązane problemy podczas pracy w funkcji domen niestandardowych w portalu dzierżawcy usługi App Service.

- Pozwolić użytkownikom przy użyciu nazw zastrzeżonych administratora podczas instalacji

- Wdrożenie usługi App Service z włączoną **przyłączonych do domeny** serwera plików

- Ulepszone pobieranie głównego usługi Azure Stack certyfikatu w skrypcie, a teraz sprawdzić poprawność certyfikatu głównego, w oknie Instalatora usługi App Service.

- Nieprawidłowy stan ustalony zwracane do usługi Azure Resource Manager, gdy subskrypcja jest usunąć ten zawarte w niej zasoby w przestrzeni nazw Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Znane problemy związane z procesu wdrażania

- Błędy sprawdzania poprawności certyfikatu

Niektórzy klienci wystąpić problemy podczas dostarczania certyfikatów do Instalatora usługi App Service, wdrażając na system zintegrowany z powodu zbyt restrykcyjne sprawdzanie poprawności w Instalatorze. Instalator usługi App Service opublikowano ponownie, jeżeli klienci [pobrać zaktualizowany Instalator](https://aka.ms/appsvconmasinstaller). Jeśli nadal występują problemy, sprawdzanie poprawności certyfikatów za pomocą zaktualizowany Instalator, skontaktuj się z pomocą techniczną.

- Wystąpił problem podczas pobierania certyfikatu głównego usługi Azure Stack z zintegrowanego systemu.

Błąd w Get AzureStackRootCert.ps1 spowodował klientów nie można pobrać certyfikatu głównego usługi Azure Stack, podczas wykonywania skryptu na maszynie nie zainstalowano certyfikat główny. Skrypt teraz również opublikowano ponownie, rozwiązywania tego problemu i żądania klientów [Pobierz skrypty pomocnika zaktualizowane](https://aka.ms/appsvconmashelpers). Jeśli nadal występują problemy podczas pobierania certyfikatu głównego z zaktualizowanego skryptu, skontaktuj się z pomocą techniczną.

### <a name="known-issues-with-the-update-process"></a>Znane problemy związane z procesem aktualizacji

- Nie istnieją żadne znane problemy w aktualizacji usługi Azure App Service w usłudze Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Znane problemy (po instalacji)

- Zamiany nie działa.

Zamiany gniazda witryny jest przerywana w tej wersji. Aby przywrócić funkcjonalność, wykonaj następujące kroki:

1. Modyfikowanie ControllersNSG sieciowej grupy zabezpieczeń w celu **Zezwalaj** połączeń pulpitu zdalnego wystąpienia kontrolera usługi App Service. Zastąp nazwę grupy zasobów, w której wdrożono usługi App Service w AppService.local.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

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

2. Przejdź do **maszyny Wirtualnej CN0** w ramach maszyn wirtualnych w portalu administratora usługi Azure Stack i **kliknij przycisk Połącz** otworzyć sesji usług pulpitu zdalnego przy użyciu wystąpienia kontrolera. Użyj poświadczeń określonych podczas wdrażania usługi App Service.
3. Rozpocznij **program PowerShell jako Administrator** i uruchom następujący skrypt

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

4. Zamknij sesję pulpitu zdalnego.
5. Przywróć ControllersNSG sieciowej grupy zabezpieczeń w celu **Odmów** połączeń pulpitu zdalnego wystąpienia kontrolera usługi App Service. Zastąp nazwę grupy zasobów, w której wdrożono usługi App Service w AppService.local.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

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

6. Pracownicy są nie można nawiązać połączenia z serwerem plików po wdrożeniu usługi App Service w istniejącej sieci wirtualnej i serwer plików jest dostępna tylko w sieci prywatnej.

Jeśli wybierzesz do wdrożenia w istniejącej sieci wirtualnej i wewnętrzny adres IP, aby nawiązać połączenie z serwerem plików, należy dodać regułę zabezpieczeń dla ruchu wychodzącego włączanie ruchu SMB między podsieci procesów roboczych i serwera plików. Aby to zrobić, przejdź do WorkersNsg w portalu administracyjnym i dodawanie reguły zabezpieczeń dla ruchu wychodzącego z następującymi właściwościami:

- Źródło: Dowolne
- Zakres portów źródłowych: *
- Miejsce docelowe: Adresy IP
- Docelowy zakres adresów IP: Zakres adresów IP dla serwera plików
- Zakres portów docelowych: 445
- Protokół: TCP
- Akcja: Zezwalaj
- Priorytet: 700
- Nazwa: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Znane problemy dotyczące administratorów chmury, działania usługi Azure App Service w usłudze Azure Stack

Zapoznaj się z dokumentacją w [informacje o wersji usługi Azure Stack w wersji 1802](azure-stack-update-1802.md)

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure App Service, zobacz [usługi Azure App Service w usłudze Azure Stack — omówienie](azure-stack-app-service-overview.md).
- Aby uzyskać więcej informacji na temat przygotowywania do wdrożenia usługi App Service w usłudze Azure Stack, zobacz [przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](azure-stack-app-service-before-you-get-started.md).
