---
title: Szyfrowanie dysków platformy Azure dla systemu Linux
description: Wdraża szyfrowanie dysków platformy Azure dla systemu Linux na maszynie wirtualnej przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066913"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Szyfrowanie dysków platformy Azure dla systemu Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Omówienie

Usługa Azure Disk Encryption wykorzystuje podsystem dm-crypt w systemie Linux, aby zapewnić pełne szyfrowanie dysku w [wybranych dystrybucjach systemu Azure Linux.](https://aka.ms/adelinux)  To rozwiązanie jest zintegrowane z usługą Azure Key Vault do zarządzania kluczami szyfrowania dysku i wpisami tajnymi.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać pełną listę wymagań wstępnych, zobacz [Szyfrowanie dysków platformy Azure dla maszyn wirtualnych z systemem Linux](../linux/disk-encryption-overview.md), w szczególności następujące sekcje:

- [Obsługiwane maszyny wirtualne i systemy operacyjne](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Dodatkowe wymagania dotyczące maszyn wirtualnych](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Wymagania dotyczące sieci](../linux/disk-encryption-overview.md#networking-requirements)
- [Wymagania dotyczące przechowywania kluczy szyfrowania](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Schemat rozszerzenia

Istnieją dwie wersje schematu rozszerzenia dla szyfrowania dysków azure (ADE):
- wersja 1.1 — nowszy zalecany schemat, który nie używa właściwości usługi Azure Active Directory (AAD).
- wersja 0.1 — starszy schemat, który wymaga właściwości usługi Azure Active Directory (AAD). 

Aby wybrać schemat docelowy, `typeHandlerVersion` właściwość musi być równa wersji schematu, którego chcesz użyć.

### <a name="schema-v11-no-aad-recommended"></a>Schemat v1.1: Brak AAD (zalecane)

Schemat w wersji 1.1 jest zalecany i nie wymaga właściwości usługi Azure Active Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schemat v0.1: z AAD 

Schemat 0.1 wymaga `AADClientID` i `AADClientSecret` albo `AADClientCertificate`albo .

Korzystanie `AADClientSecret`z :

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| type | Usługa AzureDiskEncryptionForLinux | ciąg |
| typHandlerVersion | 1.1, 0.1 | int |
| (schemat 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx | Identyfikator GUID | 
| (schemat 0,1) Protokół AADClientSecret | hasło | ciąg |
| (schemat 0,1) Certyfikat AADClientCertificate | Odcisk palca | ciąg |
| (opcjonalnie) (schemat 0,1) Hasło | hasło | ciąg |
| Kwerenda dysku | {"dev_path":"","name":"","file_system":""} | Słownik JSON |
| Operacje szyfrowania | EnableEncryption, EnableEncryptionFormatAll | ciąg | 
| (opcjonalnie - domyślna RSA-OAEP) KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | ciąg |
| Klucz KeyVaultURL | url | ciąg |
| KeyVaultResourceId ( KeyVaultResourceId ) | url | ciąg |
| (opcjonalnie) Klucz KeyEncryptionKeyURL | url | ciąg |
| (opcjonalnie) KekVaultResourceId | url | ciąg |
| (opcjonalnie) SequenceVersion (Wersja sekwenc | uniqueidentifier | ciąg |
| Typ woluminu | OS, Dane, Wszystkie | ciąg |

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Na przykład wdrożenia szablonu opartego na schemacie w wersji 1.1 zobacz szablon szybki start platformy Azure [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Na przykład wdrożenia szablonu opartego na schemacie w wersji 0.1 zobacz szablon szybki start platformy Azure [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Jeśli wcześniej używano szyfrowania dysków platformy Azure z usługą Azure AD do szyfrowania maszyny wirtualnej, należy nadal używać tej opcji do szyfrowania maszyny wirtualnej.
> - Podczas szyfrowania woluminów systemu operacyjnego Linux maszyna wirtualna powinna być uważana za niedostępną. Zdecydowanie zalecamy unikanie logowania SSH podczas szyfrowania w toku, aby uniknąć problemów z blokowaniem otwartych plików, które będą musiały być dostępne podczas procesu szyfrowania. Aby sprawdzić postęp, użyj polecenia [cmdlet Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell lub [szyfrowania maszyny wirtualnej pokaż](/cli/azure/vm/encryption#az-vm-encryption-show) polecenia CLI. Można oczekiwać, że ten proces zajmie kilka godzin dla woluminu systemu operacyjnego o pojemności 30 GB, a także dodatkowy czas na szyfrowanie woluminów danych. Czas szyfrowania woluminu danych będzie proporcjonalny do rozmiaru i ilości woluminów danych, chyba że używana jest cała opcja formatu szyfrowania. 
> - Wyłączenie szyfrowania na maszynach wirtualnych z systemem Linux jest obsługiwane tylko dla woluminów danych. Nie jest obsługiwany na woluminach danych lub systemu operacyjnego, jeśli wolumin systemu operacyjnego został zaszyfrowany. 

>[!NOTE]
> Również `VolumeType` jeśli parametr jest ustawiony na Wszystkie, dyski danych będą szyfrowane tylko wtedy, gdy są prawidłowo zamontowane.

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby zapoznać się z problemami, zapoznaj się z [przewodnikiem rozwiązywania problemów z szyfrowaniem dysków platformy Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/community/). 

Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje dotyczące korzystania z pomocy technicznej platformy Azure, przeczytaj [często zadawane pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat rozszerzeń maszyn wirtualnych, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Linux](features-linux.md).
* Aby uzyskać więcej informacji na temat szyfrowania dysków azure dla systemu Linux, zobacz [maszyny wirtualne systemu Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
