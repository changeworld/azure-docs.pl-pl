---
title: Azure Disk Encryption dla systemu Windows | Microsoft Docs
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
ms.openlocfilehash: 11394f692765cc1df5db0eb5c0dd06425026505d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092643"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption dla systemu Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Przegląd

Azure Disk Encryption wykorzystuje funkcję BitLocker, aby zapewnić pełne szyfrowanie dysków na maszynach wirtualnych platformy Azure z systemem Windows.  To rozwiązanie jest zintegrowane z usługą Azure Key Vault w celu zarządzania kluczami i wpisami tajnymi dysków w ramach subskrypcji magazynu kluczy. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, [Zobacz Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md)wymagania wstępne.

### <a name="operating-system"></a>System operacyjny

Aby zapoznać się z listą obecnie dostępnych wersji systemu Windows, zobacz [Azure Disk Encryption wymagania wstępne](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Łączność z Internetem

Azure Disk Encryption wymaga łączności z Internetem w celu uzyskania dostępu do punktów końcowych Active Directory, Key Vault, magazynu i zarządzania pakietami.  Aby uzyskać więcej informacji na temat ustawień zabezpieczeń [sieci,](
../../security/azure-security-disk-encryption-prerequisites.md)Zobacz Azure Disk Encryption wymagania wstępne.

## <a name="extension-schemata"></a>Schematu rozszerzenia

Istnieją dwa schematu Azure Disk Encryption: v 1.1, nowszy, zalecany schemat, który nie korzysta z właściwości Azure Active Directory (AAD) i v 0,1, starszy schemat, który wymaga właściwości usługi AAD. Musisz użyć wersji schematu odpowiadającej używanemu rozszerzeniu: schemat v 1.1 dla rozszerzenia AzureDiskEncryption w wersji 1,1, schemat v 0,1 dla rozszerzenia AzureDiskEncryption w wersji 0,1.

### <a name="schema-v11-no-aad-recommended"></a>Schemat v 1.1: Brak usługi AAD (zalecane)

Schemat v 1.1 jest zalecany i nie wymaga Azure Active Directory właściwości.

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


### <a name="schema-v01-with-aad"></a>Schemat v 0,1: z usługą AAD 

Schemat 0,1 wymaga `aadClientID` i `aadClientSecret` `AADClientCertificate`albo.

Przy `aadClientSecret`użyciu:

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

Przy `AADClientCertificate`użyciu:

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
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (schemat 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (schemat 0,1) AADClientSecret | password | string |
| (schemat 0,1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path": "", "name": "", "file_system": ""} | Słownik JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| obowiązkowe Danym | password | string | 
| SequenceVersion | uniqueidentifier | string |
| Liczba woluminów | System operacyjny, dane, wszystkie | string |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Aby zapoznać się z przykładem wdrożenia szablonów, zobacz [Tworzenie nowej zaszyfrowanej maszyny wirtualnej z systemem Windows na podstawie obrazu z galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Instrukcje znajdują się w najnowszej [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Zapoznaj się z [przewodnikiem rozwiązywania problemów Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](features-windows.md).
