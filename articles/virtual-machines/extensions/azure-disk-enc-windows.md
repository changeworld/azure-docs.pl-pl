---
title: Azure Disk Encryption dla systemu Windows
description: Wdraża Azure Disk Encryption na maszynie wirtualnej z systemem Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250649"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption dla systemu Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Omówienie

Azure Disk Encryption wykorzystuje funkcję BitLocker, aby zapewnić pełne szyfrowanie dysków na maszynach wirtualnych platformy Azure z systemem Windows.  To rozwiązanie jest zintegrowane z usługą Azure Key Vault w celu zarządzania kluczami i wpisami tajnymi dysków w ramach subskrypcji magazynu kluczy. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../linux/disk-encryption-overview.md), w tym w następujących sekcjach:

- [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](../windows/disk-encryption-overview.md#networking-requirements)
- [Wymagania zasady grupy](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Schematu rozszerzenia

Istnieją dwa schematu rozszerzenia Windows AzureDiskEncryption: v 2.2, nowszy, zalecany schemat, który nie korzysta z właściwości Azure Active Directory (AAD), i 1.1, starsze schemat, który wymaga właściwości usługi AAD. Należy użyć wersji schematu odpowiadającej używanemu rozszerzeniu: schemat v 2.2 dla rozszerzenia AzureDiskEncryption w wersji 2,2, schematu v 1.1 dla rozszerzenia AzureDiskEncryption w wersji 1,1.

### <a name="schema-v22-no-aad-recommended"></a>Schemat v 2.2: Brak usługi AAD (zalecane)

Schemat v 2.2 jest zalecany dla wszystkich nowych maszyn wirtualnych i nie wymaga Azure Active Directory właściwości.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>Schemat v 1.1: z usługą AAD 

Schemat 1,1 wymaga `aadClientID` i `aadClientSecret` lub `AADClientCertificate` i nie jest zalecany dla nowych maszyn wirtualnych.

Korzystanie z programu `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Korzystanie z programu `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | ciąg |
| type | AzureDiskEncryptionForLinux | ciąg |
| typeHandlerVersion | 1,1, 2,2 | ciąg |
| (schemat 1,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schemat 1,1) AADClientSecret | hasło | ciąg |
| (schemat 1,1) AADClientCertificate | thumbprint | ciąg |
| DiskFormatQuery | {"dev_path": "", "name": "", "file_system": ""} | Słownik JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | ciąg | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | ciąg |
| KeyEncryptionKeyURL | url | ciąg |
| KeyVaultURL | url | ciąg |
| obowiązkowe Danym | hasło | ciąg | 
| SequenceVersion | uniqueidentifier | ciąg |
| Liczba woluminów | System operacyjny, dane, wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Aby zapoznać się z przykładem wdrożenia szablonów, zobacz [Tworzenie nowej zaszyfrowanej maszyny wirtualnej z systemem Windows na podstawie obrazu z galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Instrukcje znajdują się w najnowszej [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Zapoznaj się z [przewodnikiem rozwiązywania problemów Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](features-windows.md).
