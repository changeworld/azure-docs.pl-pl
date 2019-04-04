---
title: Dodaj rozwiązanie Kubernetes w portalu Marketplace usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać rozwiązania Kubernetes w portalu Azure Marketplace stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: cf831c6f8faad1892291794bc43dc13e6a17eba1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484852"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Dodaj rozwiązanie Kubernetes w portalu Marketplace usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej. Odłączony platformę Azure Stack nie jest obecnie obsługiwane przez wersję zapoznawczą.

Rozwiązania Kubernetes jako elementu portalu Marketplace możesz zaoferować użytkownikom. Użytkownicy mogą następnie można wdrożyć rozwiązania Kubernetes w jednej, skoordynowanej operacji.

Następujący artykuł przyjrzeć się przy użyciu szablonu usługi Azure Resource Manager, wdrażanie i Inicjowanie obsługi administracyjnej zasobów dla autonomicznego klastra usługi Kubernetes. Przed rozpoczęciem należy sprawdzić usługi Azure Stack i ustawienia globalnego dzierżawy usługi Azure. Zbieranie wymaganych informacji na temat usługi Azure Stack. Dodaj wymagane zasoby do swojej dzierżawy, a w portalu Azure Marketplace stosu. Klaster zależy od tego, serwer Ubuntu, niestandardowego skryptu i handlowa klastra Kubernetes w portalu marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Tworzenie planu, oferty i subskrypcji

Tworzenie planu, oferty i subskrypcji dla elementu portalu Marketplace platformy Kubernetes. Można także użyć istniejącego planu i oferty.

1. Zaloguj się do [portalu administratora.](https://adminportal.local.azurestack.external)

1. Utwórz plan jako podstawowy plan. Aby uzyskać instrukcje, zobacz [Tworzenie planu w usłudze Azure Stack](azure-stack-create-plan.md).

1. Utwórz ofertę. Aby uzyskać instrukcje, zobacz [Tworzenie oferty w usłudze Azure Stack](azure-stack-create-offer.md).

1. Wybierz **oferuje**i Znajdź utworzoną ofertę.

1. Wybierz **Przegląd** w bloku oferty.

1. Wybierz **zmiany stanu**. Wybierz pozycję **Publiczna**.

1. Wybierz **+ Utwórz zasób** > **oferty i plany** > **subskrypcji** można utworzyć subskrypcji.

    a. Wprowadź **nazwy wyświetlanej**.

    b. Wprowadź **użytkownika**. Użyj konta usługi Azure AD skojarzonego z dzierżawą.

    c. **Opis dostawcy**

    d. Ustaw **dzierżawy katalogu** do dzierżawy usługi Azure AD dla usługi Azure Stack. 

    e. Wybierz **oferują**. Wybierz nazwę oferty, który został utworzony. Zanotuj identyfikator subskrypcji.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Tworzenie nazwy głównej usługi i poświadczeń w usługach AD FS

Jeśli używasz Active Directory federacyjnego Services (AD FS) dla usługi zarządzania tożsamościami, należy utworzyć jednostkę dla użytkowników, wdrażaniu klastra Kubernetes usługi.

1. Tworzenie i eksportowanie certyfikatu z podpisem własnym używany do tworzenia nazwy głównej usługi. 

    - Potrzebujesz następujących rodzajów informacji:

       | Wartość | Opis |
       | ---   | ---         |
       | Hasło | Wprowadź nowe hasło dla certyfikatu. |
       | Ścieżka do lokalnego certyfikatu | Wprowadź ścieżkę i nazwę certyfikatu. Na przykład: `c:\certfilename.pfx` |
       | Nazwa certyfikatu | Wprowadź nazwę certyfikatu. |
       | Lokalizacja magazynu certyfikatów |  Na przykład: `Cert:\LocalMachine\My` |

    - Otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza. Uruchom poniższy skrypt z parametrami zaktualizowany do wartości:

        ```powershell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  Zanotuj identyfikator certyfikatu wyświetlane w sesji programu PowerShell `1C2ED76081405F14747DC3B5F76BB1D83227D824`. Ten identyfikator będzie używany podczas tworzenia nazwy głównej usługi.

    ```powershell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. Tworzenie usługi podmiotu zabezpieczeń, za pomocą certyfikatu.

    - Potrzebujesz następujących rodzajów informacji:

       | Wartość | Opis                     |
       | ---   | ---                             |
       | ERCS IP | W ASDK uprzywilejowanych punkt końcowy jest zwykle `AzS-ERCS01`. |
       | Nazwa aplikacji | Wprowadź prostą nazwę jednostki usługi aplikacji. |
       | Lokalizacja magazynu certyfikatów | Ścieżka na komputerze, na którym są przechowywane certyfikatu. Jest to wskazywane przez lokalizacji magazynu, a identyfikator certyfikatu generowane w pierwszym kroku. Na przykład: `Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       Po wyświetleniu monitu użyj poniższych poświadczeń, aby nawiązać połączenie z punktem końcowym uprawnień. 
        - Nazwa użytkownika: Określ konto CloudAdmin w formacie <Azure Stack domain>\cloudadmin. (Dla ASDK, nazwa użytkownika jest azurestack\cloudadmin).
        - Hasło: Wprowadź to samo hasło, które zostało podane podczas instalacji dla konta administratora domeny AzureStackAdmin.

    - Uruchom poniższy skrypt z parametrami zaktualizowany do wartości:

        ```powershell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - Szczegóły jednostki usługi wyglądać jak poniższy fragment kodu

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>Dodawanie obrazu systemu Ubuntu server

Dodaj poniższy obraz Ubuntu Server w portalu Marketplace:

1. Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **wszystkich usług**, a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź polecenie `Ubuntu Server`.

1. Wybierz najnowszą wersję serwera. Zapoznaj się z pełną wersją i upewnij się, że masz najnowszą wersję:
    - **Wydawca**: Canonical
    - **Oferty**: UbuntuServer
    - **Wersja**: 16.04.201806120 (lub najnowsza wersja)
    - **SKU**: 16.04-LTS

1. Wybierz **pobierania.**

## <a name="add-a-custom-script-for-linux"></a>Dodawanie niestandardowego skryptu dla systemu Linux

Dodaj usługi Kubernetes z witryny Marketplace:

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **wszystkich usług** a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź polecenie `Custom Script for Linux`.

1. Wybierz odpowiedni skrypt, za pomocą następującego profilu:
   - **Oferty**: Niestandardowego skryptu dla systemu Linux w wersji 2.0
   - **Wersja**: 2.0.6 (lub najnowsza wersja)
   - **Wydawca**: Microsoft Corp

     > [!Note]  
     > Może być wymieniona więcej niż jedna wersja niestandardowego skryptu dla systemu Linux. Należy dodać ostatniej wersji elementu.

1. Wybierz **pobierania.**


## <a name="add-kubernetes-to-the-marketplace"></a>Dodaj rozwiązanie Kubernetes w portalu Marketplace

1. Otwórz [portalu administracyjnego](https://adminportal.local.azurestack.external).

1. Wybierz **wszystkich usług** a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace**.

1. Wybierz **+ Dodaj na platformie Azure**.

1. Wprowadź polecenie `Kubernetes`.

1. Wybierz pozycję `Kubernetes Cluster`.

1. Wybierz **pobierania.**

    > [!note]  
    > Może upłynąć pięć minut przez element portalu marketplace są wyświetlane w portalu Marketplace.

    ![Kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualizowanie lub usuwanie rozwiązania Kubernetes 

Podczas aktualizowania elementu Kubernetes, usuniesz poprzedniej pozycji na rynku. Postępuj zgodnie z instrukcjami w tym artykule można dodać rozwiązania Kubernetes aktualizacji w portalu Marketplace.

Aby usunąć element Kubernetes:

1. Połącz się do usługi Azure Stack przy użyciu programu PowerShell jako operator. Aby uzyskać instrukcje, zobacz [nawiązywanie połączenia z usługi Azure Stack przy użyciu programu PowerShell jako operator](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Bieżący element klastra Kubernetes można znaleźć w galerii.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Pamiętaj, takie jak nazwa bieżącego elementu `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Aby usunąć element, należy użyć następującego polecenia cmdlet programu PowerShell:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie usługi Kubernetes do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Przegląd oferty usług w usłudze Azure Stack](azure-stack-offer-services-overview.md)
