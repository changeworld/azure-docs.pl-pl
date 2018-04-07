---
title: Dołącz RedHat Linux maszyny Wirtualnej do usług Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak sprzęgać istniejącej RedHat Enterprise Linux 7 maszyny Wirtualnej z platformy Azure usług domenowych Active Directory.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
ms.openlocfilehash: 68971372a3c9d9671ccad43db8d7b543d286fc7a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Dołącz RedHat Linux maszyny Wirtualnej z usługą domeny usługi Azure Active Directory

W tym artykule przedstawiono sposób Dołącz maszynę wirtualną Red Hat Enterprise Linux (RHEL) 7 do domeny zarządzanej interfejsów Azure Active Directory domeny usług (AADDS).  Wymagania są następujące:

- [Konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

- [Pliki kluczy publicznych i prywatnych SSH](mac-create-ssh-keys.md)

- [Azure Active Directory Domain Services kontrolera domeny](../../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Szybkie polecenia

_Przykładami należy zastąpić własnymi ustawieniami._

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Przełącznik wiersza polecenia platformy azure do trybu klasycznego wdrożenia

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>Wyszukaj RHEL wersji i obrazów

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Utwórz Redhat Linux VM

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>Łączenie przez protokół SSH z maszyną wirtualną

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>YUM pakietów aktualizacji

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>Zainstaluj wymagane pakiety

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Teraz, wymagane pakiety są zainstalowane na maszynie wirtualnej systemu Linux, następne zadanie to można dołączyć maszyny wirtualnej do domeny zarządzanej.

### <a name="discover-the-aad-domain-services-managed-domain"></a>Odnajdywanie domeny zarządzanej usług domenowych w usłudze AAD

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>Inicjowanie protokołu kerberos

Upewnij się, że określ użytkownika, który należy do grupy "Administratorzy kontrolera domeny usługi AAD". Tylko ci użytkownicy mogą dołączania komputerów do domeny zarządzanej.

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>Dołącz maszynę do domeny

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>Sprawdź, czy komputer jest przyłączony do domeny

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>Następne kroki

* [Red Hat aktualizacji infrastruktury (RHUI) na żądanie Red Hat Enterprise Linux w maszynach wirtualnych na platformie Azure](update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurowanie magazynu kluczy dla maszyn wirtualnych w usłudze Azure Resource Manager](key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Wdrażanie i zarządzanie maszynami wirtualnymi przy użyciu szablonów usługi Azure Resource Manager i wiersza polecenia platformy Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
