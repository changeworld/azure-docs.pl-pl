---
title: Wdrażanie usługi Kubernetes na użycie usług Active Directory Sfederowana (AD FS) w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć rozwiązanie Kubernetes do usługi Azure Stack, użycie usług Active Directory Sfederowana (AD FS).
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
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: bdb46a5e11b26f04d008160fa5a782050211b56e
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484377"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Wdrażanie rozwiązania Kubernetes do usługi Azure Stack przy użyciu usługi Active Directory Federation Services

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej. Odłączony platformę Azure Stack nie jest obecnie obsługiwane przez wersję zapoznawczą.

Możesz wykonać kroki opisane w tym artykule, aby wdrożyć i skonfigurować zasoby dla rozwiązania Kubernetes. Wykonaj następujące kroki federacyjnych są usługi Active Directory (AD FS) usługi zarządzania tożsamościami.

## <a name="prerequisites"></a>Wymagania wstępne 

Aby rozpocząć pracę, upewnij się, że masz odpowiednie uprawnienia, i że usługi Azure Stack jest gotowy.

1. Generowanie publicznego i prywatnego pary kluczy SSH do logowania do maszyny Wirtualnej systemu Linux w usłudze Azure Stack. Klucz publiczny jest konieczne, podczas tworzenia klastra.

    Aby uzyskać instrukcje dotyczące generowania klucza, zobacz [generowania klucza SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Sprawdź, czy masz ważnej subskrypcji w portalu dzierżawcy usługi Azure Stack, i że masz wystarczająco dużo publiczny adres IP adresów dostępnych do dodania nowych aplikacji.

    Nie można wdrożyć klaster do usługi Azure Stack **administratora** subskrypcji. Należy użyć **użytkownika** subskrypcji. 

1. Konieczne będzie usługi Key Vault w ramach subskrypcji usługi Azure Stack.

1. Klaster Kubernetes, należy w portalu marketplace. 

Jeśli brakuje usługi Key Vault i elementu portalu marketplace klastra Kubernetes, skontaktuj się z administratorem usługi Azure Stack.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Musisz pracować z administratorem usługi Azure Stack, aby skonfigurować jednostki usługi, korzystając z usług AD FS jako rozwiązania tożsamości. Nazwa główna usługi daje aplikacji dostęp do zasobów usługi Azure Stack.

1. Administrator usługi Azure Stack umożliwia certyfikatu i informacje dotyczące nazwy głównej usługi.

   - Informacji o jednostce usługi powinien wyglądać podobnie jak:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : azs-ercs01
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

   - Twój certyfikat będzie plik z rozszerzeniem `.pfx`. Twój certyfikat w magazynie keyvault zapisze jako klucz tajny.

2. Przypisz nową nazwę główną usługi rolę współautora do subskrypcji. Aby uzyskać instrukcje, zobacz [przypisania roli](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

3. Tworzenie magazynu kluczy do przechowywania certyfikatu do wdrożenia. Za pomocą następujących skryptów programu PowerShell, a nie w portalu.

   - Potrzebujesz następujących rodzajów informacji:

       | Wartość | Opis |
       | ---   | ---         |
       | Punkt końcowy usługi Azure Resource Manager | Menedżer zasobów platformy Azure to platforma zarządzania, która umożliwia administratorom wdrażanie, zarządzanie i monitorowanie zasobów platformy Azure. Usługa Azure Resource Manager może obsługiwać te zadania jako grupę, a nie indywidualnie, w ramach jednej operacji.<br>Punkt końcowy w usłudze Azure Stack Development Kit (ASDK) to: `https://management.local.azurestack.external/`<br>Punktu końcowego w systemach zintegrowanych jest: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Identyfikator subskrypcji | [Identyfikator subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack. |
       | Nazwa użytkownika | Użyj po prostu swoją nazwę użytkownika, a nie nazwę domeny i nazwę użytkownika, takie jak `username` zamiast `azurestack\username`. |
       | Nazwa grupy zasobów  | Nazwa nowej grupy zasobów lub wybierz istniejącą grupę zasobów. Nazwa zasobu musi być alfanumeryczne i małe litery. |
       | Nazwa elementu Keyvault | Nazwa magazynu.<br> Wzorzec wyrażenia regularnego: `^[a-zA-Z0-9-]{3,24}$` |
       | Lokalizacja grupy zasobów | Lokalizacja grupy zasobów. Jest to region, w wybranym dla instalacji programu Azure Stack. |

   - Otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza i [nawiązać połączenie z usługi Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Uruchom poniższy skrypt z parametrami zaktualizowany do wartości:

   ```powershell  
       $armEndpoint="<Azure Resource Manager Endpoint>"
       $subscriptionId="<Your Subscription ID>"
       $username="<your user name >"
       $resource_group_name = "<the resource group name >"
       $key_vault_name = "<keyvault name>"
       $resource_group_location="<resource group location>"
        
       # Login Azure Stack Environment
       Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
       $mycreds = Get-Credential
       Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
       # Create new Resource group and key vault
       New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
       # Note, Do not omit -EnabledForTemplateDeployment flag
       New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
       # Obtain the security identifier(SID) of the active directory user
       $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
       $objectSID = $adUser.SID.Value
       # Set the key vault access policy
       Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
     ```

4. Przekaż certyfikat do magazynu kluczy.

   - Potrzebujesz następujących rodzajów informacji:

       | Wartość | Opis |
       | ---   | ---         |
       | Ścieżka do certyfikatu | Ścieżka pliku lub nazwa FQDN certyfikatu. |
       | Hasło certyfikatu | Hasło certyfikatu. |
       | Nazwa wpisu tajnego | Nazwa wpisu tajnego, używany do odwoływania certyfikatów przechowywanych w magazynie. |
       | Nazwa magazynu kluczy | Nazwa magazynu kluczy, utworzony w poprzednim kroku. |
       | Punkt końcowy usługi Azure Resource Manager | Punkt końcowy w usłudze Azure Stack Development Kit (ASDK) to: `https://management.local.azurestack.external/`<br>Punktu końcowego w systemach zintegrowanych jest: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | Identyfikator subskrypcji | [Identyfikator subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack. |

   - Otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza i [nawiązać połączenie z usługi Azure Stack](azure-stack-powershell-configure-user.md#connect-with-ad-fs). Uruchom poniższy skrypt z parametrami zaktualizowany do wartości:

    ```powershell
        
     # upload the pfx to key vault
     $tempPFXFilePath = "<certificate path>"
     $password = "<certificate password>"
     $keyVaultSecretName = "<secret name>"
     $keyVaultName = "<key vault name>"
     $armEndpoint="<Azure Resource Manager Endpoint>"
     $subscriptionId="<Your Subscription ID>"
     # Login Azure Stack Environment
     Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
     $mycreds = Get-Credential
     Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
     $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
     $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
     $jsonObject = @"
     {
     "data": "$pfxAsBase64EncodedString",
     "dataType" :"pfx",
     "password": "$password"
     }
     "@
     $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
     $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
     $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
     $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
     ```

## <a name="deploy-kubernetes"></a>Wdrażanie rozwiązania Kubernetes

1. Otwórz [portalu Azure Stack](https://portal.local.azurestack.external).

1. Wybierz **+ Utwórz zasób** > **obliczenia** > **klastra Kubernetes**. Kliknij pozycję **Utwórz**.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Podstawy

1. Wybierz **podstawy** w tworzenie klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Wybierz swoje **subskrypcji** identyfikatora.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz istniejącą grupę zasobów. Nazwa zasobu musi być alfanumeryczne i małe litery.

1. Wybierz **lokalizacji** grupy zasobów. Jest to region, w wybranym dla instalacji programu Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Ustawienia klastra Kubernetes

1. Wybierz **ustawienia klastra Kubernetes** w tworzenie klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Wprowadź **nazwa użytkownika administratora maszyny Wirtualnej systemu Linux**. Nazwa użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.

1. Wprowadź **klucz publiczny SSH** używane na potrzeby autoryzacji do wszystkich utworzonych jako część klastra Kubernetes i Menedżer DVM maszyn z systemem Linux.

1. Wprowadź **prefiks DNS profilu wzorzec** jest unikatowa w regionie. Musi to być region unikatową nazwę, taką jak `k8s-12345`. Spróbuj wybrać jej nazwę takie same jak grupy zasobów jako najlepsze rozwiązanie.

    > [!Note]  
    > Dla każdego klastra użyj prefiks DNS nowych i unikatowych głównym profilu.

1. Wybierz **liczba profil puli wzorców Kubernetes**. Liczba zawiera liczbę węzłów w puli wzorca. Może być z zakresu od 1 do 7. Ta wartość powinna być nieparzysta.

1. Wybierz **VMSize głównych maszyn wirtualnych Kubernetes**.

1. Wybierz **liczba profil puli węzłów rozwiązania Kubernetes**. Liczba zawiera liczbę agentów w klastrze. 

1. Wybierz **profil magazynu**. Możesz wybrać **obiektu Blob dysku** lub **dysku zarządzanego**. To ustawienie określa maszyn wirtualnych węzła rozmiar maszyny Wirtualnej z platformy Kubernetes. 

1. Wybierz **usług AD FS** dla **systemu tożsamości w usłudze Azure Stack** instalacji usługi Azure Stack.

1. Wprowadź **ClientId nazwy głównej usługi** jest on używany przez dostawcę chmury Kubernetes Azure. Identyfikator klienta zidentyfikowane jako identyfikator aplikacji, utworzenia jednostki usługi, administrator usługi Azure Stack.

1. Wprowadź **grupy zasobów usługi Key Vault** źródeł, magazynu kluczy, który zawiera certyfikat.

1. Wprowadź **nazwę usługi Key Vault** nazwę magazynu kluczy, który zawiera certyfikat jako wpis tajny. 

1. Wprowadź **wpisu tajnego usługi Key Vault**. Nazwa wpisu tajnego odwołuje się do certyfikatu.

1. Wprowadź **wersja dostawcy chmury Kubernetes Azure**. To jest wersja dostawcy usługi Kubernetes Azure. Usługa Azure Stack zwalnia niestandardowej kompilacji rozwiązania Kubernetes dla każdej wersji usługi Azure Stack.

### <a name="3-summary"></a>3. Podsumowanie

1. Wybierz pozycję podsumowanie. Blok wyświetla komunikat sprawdzania poprawności ustawień konfiguracji klastra Kubernetes.

    ![Wdrażanie szablonu rozwiązań](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Przejrzyj ustawienia.

3. Wybierz **OK** do wdrożenia klastra.

> [!TIP]  
>  Jeśli masz pytania dotyczące wdrożenia, możesz Opublikuj swoje pytanie lub zobaczyć, jeśli ktoś ma już odpowiedzi na pytania w [forum poświęcone systemowi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Kolejne kroki

[Połącz z klastrem](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Włącz pulpit nawigacyjny platformy Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)