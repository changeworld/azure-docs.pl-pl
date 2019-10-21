---
title: Azure Disk Encryption dla systemu Linux | Microsoft Docs
description: Wdraża Azure Disk Encryption dla systemu Linux na maszynie wirtualnej przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 1801ca8bd59153de81ef680253da229e2bd4a338
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597873"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption dla systemu Linux (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Przegląd

Azure Disk Encryption korzysta z podsystemu dm-crypt w systemie Linux, aby zapewnić pełne szyfrowanie dysków w przypadku [wybrania dystrybucji systemu Azure Linux](https://aka.ms/adelinux).  To rozwiązanie jest zintegrowane z usługą Azure Key Vault w celu zarządzania kluczami i wpisami tajnymi dysków.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../linux/disk-encryption-overview.md), w tym w następujących sekcjach:

- [Azure Disk Encryption dla maszyn wirtualnych z systemem Linux](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Dodatkowe wymagania dotyczące maszyn wirtualnych](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Wymagania dotyczące sieci](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Schematu rozszerzenia

Istnieją dwa schematu Azure Disk Encryption: v 1.1, nowszy, zalecany schemat, który nie korzysta z właściwości Azure Active Directory (AAD) i v 0,1, starszy schemat, który wymaga właściwości usługi AAD. Musisz użyć wersji schematu odpowiadającej używanemu rozszerzeniu: schemat v 1.1 dla rozszerzenia AzureDiskEncryptionForLinux w wersji 1,1, schemat v 0,1 dla rozszerzenia AzureDiskEncryptionForLinux w wersji 0,1.
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
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Schemat v 0,1: z usługą AAD 

Schemat 0,1 wymaga `aadClientID` i `aadClientSecret` lub `AADClientCertificate`.

Używanie `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Używanie `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość/przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| dawc | Microsoft. Azure. Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (schemat 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Ident | 
| (schemat 0,1) AADClientSecret | hasło | string |
| (schemat 0,1) AADClientCertificate | odcisk palca | string |
| DiskFormatQuery | {"dev_path": "", "name": "", "file_system": ""} | Słownik JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | string |
| keyEncryptionKeyURL | url | string |
| obowiązkowe KeyVaultURL | url | string |
| Danym | hasło | string | 
| sequenceVersion | uniqueidentifier | string |
| liczba woluminów | System operacyjny, dane, wszystkie | string |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Przykład wdrażania szablonów można znaleźć [w temacie Włączanie szyfrowania na działającej maszynie wirtualnej z systemem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Instrukcje znajdują się w najnowszej [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Rozwiązywanie problemów można znaleźć w [przewodniku rozwiązywania problemów Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Linux](features-linux.md).