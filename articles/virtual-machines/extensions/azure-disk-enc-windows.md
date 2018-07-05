---
title: Usługa Azure Disk Encryption for Windows | Dokumentacja firmy Microsoft
description: Służy do wdrażania usługi Azure Disk Encryption na maszynę wirtualną Windows przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: f9b8c64aadf813ab1157820fc4051d48989e48c4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450030"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Usługa Azure Disk Encryption for Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Przegląd

Usługa Azure Disk Encryption korzysta z funkcji Bitlocker, aby zapewnić pełne szyfrowanie dysków na maszynach wirtualnych z systemem Windows Azure.  To rozwiązanie jest zintegrowana z usługą Azure Key Vault do zarządzania wpisami tajnymi w ramach subskrypcji usługi key vault i kluczami szyfrowania dysków. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [wymagań wstępnych szyfrowania dysków Azure](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>System operacyjny

Aby uzyskać listę aktualnie wersje Windows, zobacz [wymagań wstępnych szyfrowania dysków Azure](../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="internet-connectivity"></a>Łączność z Internetem

Usługa Azure Disk Encryption wymaga łączności z Internetem, aby uzyskać dostęp do usługi Active Directory, Key Vault, magazynu i punktów końcowych zarządzania pakietu.  Aby uzyskać więcej informacji na temat ustawień zabezpieczeń sieci, zobacz [wymagań wstępnych szyfrowania dysków Azure](
../../security/azure-security-disk-encryption.md#prerequisites).

## <a name="extension-schema"></a>Schemat rozszerzenia

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| apiVersion | 2015-06-15 | data |
| Wydawcy | Microsoft.Azure.Security | ciąg |
| type | AzureDiskEncryptionForWindows| ciąg |
| typeHandlerVersion | 1.0, 2.2 (ZESTAWU SKALOWANIA MASZYN WIRTUALNYCH) | Int |
| (opcjonalnie) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Identyfikator GUID | 
| (opcjonalnie) AADClientSecret | hasło | ciąg |
| (opcjonalnie) AADClientCertificate | Odcisk palca | ciąg |
| EncryptionOperation | EnableEncryption | ciąg | 
| KeyEncryptionAlgorithm | RSA OAEP | ciąg |
| KeyEncryptionKeyURL | url | ciąg |
| KeyVaultURL | url | ciąg |
| SequenceVersion | uniqueidentifier | ciąg |
| VolumeType | Systemu operacyjnego, danych, wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Na przykład wdrożenie szablonu zobacz [ Tworzenie nowej maszyny Wirtualnej Windows zaszyfrowane na podstawie obrazu z galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Instrukcje znajdują się najnowsze [dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Zapoznaj się [przewodnik rozwiązywania problemów z usługi Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/community/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [rozszerzeń maszyn wirtualnych i funkcji dla Windows](features-windows.md).