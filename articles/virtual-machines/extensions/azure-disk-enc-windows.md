---
title: Szyfrowanie dysków platformy Azure dla systemu Windows
description: Wdraża szyfrowanie dysków platformy Azure na maszynie wirtualnej systemu Windows przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066863"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Szyfrowanie dysków platformy Azure dla systemu Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Omówienie

Usługa Azure Disk Encryption wykorzystuje funkcję BitLocker do zapewnienia pełnego szyfrowania dysku na maszynach wirtualnych platformy Azure z systemem Windows.  To rozwiązanie jest zintegrowane z usługą Azure Key Vault do zarządzania kluczami szyfrowania dysku i wpisami tajnymi w subskrypcji magazynu kluczy. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych systemu Windows](../windows/disk-encryption-overview.md), w szczególności następujące sekcje:

- [Obsługiwane maszyny wirtualne i systemy operacyjne](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Wymagania dotyczące sieci](../windows/disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące zasad grupy](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Schemat rozszerzenia

Istnieją dwie wersje schematu rozszerzenia dla szyfrowania dysków azure (ADE):
- wersja 2.2 — nowszy zalecany schemat, który nie używa właściwości usługi Azure Active Directory (AAD).
- wersja 1.1 — starszy schemat, który wymaga właściwości usługi Azure Active Directory (AAD). 

Aby wybrać schemat docelowy, `typeHandlerVersion` właściwość musi być równa wersji schematu, którego chcesz użyć.

### <a name="schema-v22-no-aad-recommended"></a>Schemat v2.2: Brak AAD (zalecane)

Schemat w wersji 2.2 jest zalecany dla wszystkich nowych maszyn wirtualnych i nie wymaga właściwości usługi Azure Active Directory.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schemat v1.1: z AAD 

Schemat 1.1 wymaga `aadClientID` i `aadClientSecret` albo `AADClientCertificate` lub i nie jest zalecane dla nowych maszyn wirtualnych.

Korzystanie `aadClientSecret`z :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Korzystanie `AADClientCertificate`z :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład | Typ danych |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| wydawca | Microsoft.Azure.Security | ciąg |
| type | Usługa AzureDiskEncryption | ciąg |
| typHandlerVersion | 2.2, 1.1 | ciąg |
| (schemat 1.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx | Identyfikator GUID | 
| (schemat 1.1) Protokół AADClientSecret | hasło | ciąg |
| (schemat 1.1) Certyfikat AADClientCertificate | Odcisk palca | ciąg |
| Operacje szyfrowania | EnableEncryption, EnableEncryptionFormatAll | ciąg | 
| (opcjonalnie - domyślna RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | ciąg |
| Klucz KeyVaultURL | url | ciąg |
| KeyVaultResourceId ( KeyVaultResourceId ) | url | ciąg |
| (opcjonalnie) Klucz KeyEncryptionKeyURL | url | ciąg |
| (opcjonalnie) KekVaultResourceId | url | ciąg |
| (opcjonalnie) SequenceVersion (Wersja sekwenc | uniqueidentifier | ciąg |
| Typ woluminu | OS, Dane, Wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Na przykład wdrożenia szablonu opartego na schemacie w wersji 2.2 zobacz Szablon szybki start platformy Azure [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Na przykład wdrożenia szablonu opartego na schemacie w wersji 1.1 zobacz Szablon szybki start platformy Azure [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Również `VolumeType` jeśli parametr jest ustawiony na Wszystkie, dyski danych będą szyfrowane tylko wtedy, gdy są poprawnie sformatowane. 

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby zapoznać się z problemami, zapoznaj się z [przewodnikiem rozwiązywania problemów z szyfrowaniem dysków platformy Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/community/). 

Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje dotyczące korzystania z pomocy technicznej platformy Azure, przeczytaj [często zadawane pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat rozszerzeń, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Windows](features-windows.md).
* Aby uzyskać więcej informacji na temat szyfrowania dysków platformy Azure dla systemu Windows, zobacz [Maszyny wirtualne systemu Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
