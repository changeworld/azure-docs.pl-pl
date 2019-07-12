---
title: Usługa Azure Disk Encryption dla systemu Linux | Dokumentacja firmy Microsoft
description: Służy do wdrażania usługi Azure Disk Encryption dla systemu Linux na maszynę wirtualną przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: d544aae33faf60be00a2b4ea0a45f405efcedb39
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706141"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption for Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Omówienie

Usługa Azure Disk Encryption korzysta z podsystemu dm-crypt w systemie Linux, aby zapewnić pełne szyfrowanie dysków na [dystrybucje systemu Linux platformy Azure wybierz](https://aka.ms/adelinux).  To rozwiązanie jest zintegrowana z usługą Azure Key Vault do zarządzania wpisami tajnymi i kluczami szyfrowania dysków.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [wymagań wstępnych szyfrowania dysków Azure](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>System operacyjny

Usługa Azure Disk Encryption jest obecnie obsługiwane na wybierz opcję dystrybucji i wersji.  Zobacz [usługi Azure Disk Encryption obsługiwane systemy operacyjne: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) listy dystrybucje systemu Linux, które są obsługiwane.

### <a name="internet-connectivity"></a>Łączność z Internetem

Usługa Azure Disk Encryption dla systemu Linux wymaga łączności z Internetem, aby uzyskać dostęp do usługi Active Directory, Key Vault, magazynu i punktów końcowych zarządzania pakietu.  Aby uzyskać więcej informacji, zobacz [wymagań wstępnych szyfrowania dysków Azure](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Rozszerzenia schematu

Istnieją dwa wypełniana dla usługi Azure Disk Encryption: v1.1, nowsze, zalecane schematu, która nie korzysta z usługi Azure Active Directory (AAD), właściwości i v0.1, starszy schemat, który wymaga właściwości usługi AAD. Należy użyć wersji schematu odpowiadający rozszerzenia, którego używasz: AzureDiskEncryptionForLinux rozszerzenia w wersji 1.1, v0.1 schematu dla AzureDiskEncryptionForLinux wersja rozszerzenia 0,1 w wersji 1.1 schematu.
### <a name="schema-v11-no-aad-recommended"></a>Schemat w wersji 1.1: Nie usługi AAD (zalecane)

Schematu w wersji 1.1 jest zalecana i wymaga właściwości usługi Azure Active Directory.

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


### <a name="schema-v01-with-aad"></a>Schemat v0.1: przy użyciu usługi AAD 

Wymaga schematu 0,1 `aadClientID` i `aadClientSecret` lub `AADClientCertificate`.

Za pomocą `aadClientSecret`:

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

Za pomocą `AADClientCertificate`:

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

| Name (Nazwa) | Wartość / przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | ciąg |
| type | AzureDiskEncryptionForLinux | ciąg |
| typeHandlerVersion | 0.1, 1.1 | int |
| (0.1 schema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Identyfikator GUID | 
| (0.1 schema) AADClientSecret | password | ciąg |
| (0.1 schema) AADClientCertificate | thumbprint | ciąg |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Słownik JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | ciąg | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | ciąg |
| KeyEncryptionKeyURL | url | ciąg |
| (optional) KeyVaultURL | url | ciąg |
| Passphrase | password | ciąg | 
| SequenceVersion | uniqueidentifier | ciąg |
| VolumeType | Systemu operacyjnego, danych, wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Na przykład wdrożenie szablonu zobacz [włączyć szyfrowanie na uruchomionej maszyny Wirtualnej systemu Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Instrukcje znajdują się najnowsze [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Rozwiązywanie problemów, można znaleźć [przewodnik rozwiązywania problemów z usługi Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux](features-linux.md).