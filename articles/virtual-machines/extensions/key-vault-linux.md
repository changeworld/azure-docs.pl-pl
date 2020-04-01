---
title: Rozszerzenie maszyny Wirtualnej usługi Azure Key Vault dla systemu Linux
description: Wdrażanie agenta wykonującego automatyczne odświeżanie certyfikatów usługi Key Vault na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: add2d515e4f8e8c56a98a7292e137e601332d10c
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410863"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej Key Vault dla systemu Linux

Rozszerzenie maszyny Wirtualnej usługi Key Vault zapewnia automatyczne odświeżanie certyfikatów przechowywanych w magazynie kluczy platformy Azure. W szczególności rozszerzenie monitoruje listę obserwowanych certyfikatów przechowywanych w magazynach kluczy.  Po wykryciu zmiany rozszerzenie pobiera i instaluje odpowiednie certyfikaty. Rozszerzenie maszyny Wirtualnej usługi Key Vault jest publikowane i obsługiwane przez firmę Microsoft, obecnie na maszynach wirtualnych z systemem Linux. Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform, konfiguracji i opcji wdrażania rozszerzenia maszyny Wirtualnej usługi Key Vault dla systemu Linux. 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny Wirtualnej Usługi Key Vault obsługuje następujące dystrybucje systemu Linux:

- Ubuntu-1604
- Ubuntu-1804
- Debian-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>Obsługiwane typy zawartości certyfikatu

- #12 PKCS
- Pem

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia maszyny Wirtualnej magazynu kluczy. Rozszerzenie nie wymaga chronionych ustawień - wszystkie jego ustawienia są uważane za informacje bez wpływu na bezpieczeństwo. Rozszerzenie wymaga listy monitorowanych wpisów tajnych, częstotliwości sondowania i magazynu certyfikatów docelowych. Są to:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Adresy URL obserwowanych certyfikatów `https://myVaultName.vault.azure.net/secrets/myCertName`powinny mieć formę .
> 
> Dzieje się `/secrets` tak, ponieważ ścieżka zwraca pełny certyfikat, `/certificates` w tym klucz prywatny, podczas gdy ścieżka nie. Więcej informacji na temat certyfikatów można znaleźć tutaj: [Certyfikaty magazynu kluczy](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| wydawca | Microsoft.Azure.KeyVault | ciąg |
| type | KeyVaultForLinux (KeyVaultForLinux) | ciąg |
| typHandlerVersion | 1.0 | int |
| sondowanieIntervalInS | 3600 | ciąg |
| certificateStoreName | MY | ciąg |
| linkOnRenewal | false | wartość logiczna |
| certificateStoreLocation (Alokacja magazynu)  | Localmachine | ciąg |
| wymaganeInitialSync | true | wartość logiczna |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | tablica ciągów


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Szablony są idealne podczas wdrażania co najmniej jednej maszyny wirtualnej, które wymagają odświeżenia certyfikatów po wdrożeniu. Rozszerzenie można wdrożyć na poszczególnych maszynach wirtualnych lub zestawach skalowania maszyny wirtualnej. Schemat i konfiguracja są wspólne dla obu typów szablonów. 

Konfiguracja JSON dla rozszerzenia maszyny wirtualnej musi być zagnieżdżona `"resources": []` wewnątrz fragmentu zasobu maszyny wirtualnej szablonu, w szczególności obiektu dla szablonu maszyny wirtualnej i w przypadku skalowania maszyny wirtualnej ustawionej w obiekcie. `"virtualMachineProfile":"extensionProfile":{"extensions" :[]`

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Wdrożenie programu Azure PowerShell

Program Azure PowerShell może służyć do wdrażania rozszerzenia maszyny Wirtualnej usługi Key Vault na istniejącej maszynie wirtualnej lub zestawie skalowania maszyny wirtualnej. 

* Aby wdrożyć rozszerzenie na maszynie Wirtualnej:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyny wirtualnej:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia maszyny Wirtualnej usługi Key Vault do istniejącego zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej. 
 
* Aby wdrożyć rozszerzenie na maszynie Wirtualnej:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyny wirtualnej:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Należy pamiętać o następujących ograniczeniach/wymaganiach:
- Ograniczenia przechowalni kluczy:
  - Musi istnieć w momencie 
  - Zasady dostępu do magazynu kluczy są ustawione dla tożsamości maszyn wirtualnych/VMSS przy użyciu systemu MSI


## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu programu Azure PowerShell. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu programu Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
