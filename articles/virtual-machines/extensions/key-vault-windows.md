---
title: Rozszerzenie maszyny Wirtualnej usługi Azure Key Vault dla systemu Windows
description: Wdrażanie agenta wykonującego automatyczne odświeżanie wpisów tajnych usługi Key Vault na maszynach wirtualnych przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 22a4177d85cb9dbbaa9ed75e063306484c7b48a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298975"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Rozszerzenie maszyny wirtualnej magazynu kluczy dla systemu Windows

Rozszerzenie maszyny Wirtualnej usługi Key Vault zapewnia automatyczne odświeżanie certyfikatów przechowywanych w magazynie kluczy platformy Azure. W szczególności rozszerzenie monitoruje listę obserwowanych certyfikatów przechowywanych w magazynach kluczy, a po wykryciu zmiany pobiera i instaluje odpowiednie certyfikaty. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania rozszerzenia maszyny Wirtualnej usługi Key Vault dla systemu Windows. 

### <a name="operating-system"></a>System operacyjny

Rozszerzenie maszyny Wirtualnej usługi Key Vault obsługuje następujące wersje systemu Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Obsługiwane typy zawartości certyfikatu

- #12 PKCS

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia maszyny Wirtualnej magazynu kluczy. Rozszerzenie nie wymaga chronionych ustawień - wszystkie jego ustawienia są uważane za informacje publiczne. Rozszerzenie wymaga listy monitorowanych certyfikatów, częstotliwości sondowania i magazynu certyfikatów docelowych. Są to:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
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
| type | KeyVaultForWindows | ciąg |
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
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
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
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
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
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
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
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Aby wdrożyć rozszerzenie na zestawie skalowania maszyny wirtualnej:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
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

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
