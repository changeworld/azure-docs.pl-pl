---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Sposób tworzenia i używania pary kluczy publicznych i prywatnych SSH dla maszyn wirtualnych systemu Linux na platformie Azure w celu zwiększenia bezpieczeństwa procesu uwierzytelniania.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
ms.locfileid: "31602634"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: tworzenie i używanie pary kluczy publicznych i prywatnych SSH dla maszyn wirtualnych systemu Linux na platformie Azure
Para kluczy Secure Shell (SSH) umożliwia tworzenie na platformie Azure maszyn wirtualnych, które używają kluczy SSH do uwierzytelniania, eliminując konieczność logowania przy użyciu haseł. W tym artykule przedstawiono, jak szybko generowania i użytkowania parę pliku klucza publicznego i prywatnego SSH dla maszyn wirtualnych systemu Linux. Można wykonać te kroki powłoki chmury Azure, macOS lub Linux host podsystemu systemu Windows dla systemu Linux i innych narzędzi, które obsługują OpenSSH. 

Dodatkowe tła i przykłady, zobacz [par kluczy szczegółowy opis kroków, aby utworzyć SSH](create-ssh-keys-detailed.md).

Aby uzyskać dodatkowe sposoby Generowanie i używanie kluczy SSH na komputerze z systemem Windows, zobacz [kluczy sposobu korzystania z protokołu SSH z systemem Windows Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
Użyj `ssh-keygen` polecenie, aby wygenerować SSH publicznego i prywatnego klucza pliki, które są domyślnie tworzone w `~/.ssh` katalogu. Możesz określić inną lokalizację i hasło dodatkowe (hasło dostępu do pliku klucza prywatnego) po wyświetleniu monitu. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione.

```bash
ssh-keygen -t rsa -b 2048
```

Jeśli używasz [Azure CLI 2.0](/cli/azure) do utworzenia maszyny Wirtualnej, może opcjonalnie generować SSH publicznego i prywatnego klucza pliki, uruchamiając [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) polecenie z `--generate-ssh-keys` opcji. Klucze są przechowywane w katalogu ~/.ssh. Należy pamiętać, że to polecenie nie powoduje zastąpienia klucze Jeśli już istnieją w tej lokalizacji.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH, podczas wdrażania maszyny Wirtualnej
Aby utworzyć maszynę Wirtualną systemu Linux, która używa kluczy SSH do uwierzytelnienia, podaj klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu portalu Azure, interfejsu wiersza polecenia, szablony usługi Resource Manager lub innych metod:

* [Utwórz maszynę wirtualną systemu Linux przy użyciu portalu Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz maszynę wirtualną systemu Linux z wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz Maszynę wirtualną systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Jeśli nie masz doświadczenia w obsłudze format klucz publiczny SSH, można wyświetlić klucz publiczny, uruchamiając `cat` , zastępując `~/.ssh/id_rsa.pub` lokalizację pliku klucza publicznego:

```bash
cat ~/.ssh/id_rsa.pub
```

Pamiętaj, aby podczas kopiowania i wklejania zawartości klucza publicznego do użycia w witrynie Azure Portal lub szablonie usługi Resource Manager nie kopiować dodatkowych spacji. Na przykład, jeśli używasz macOS, możesz przekazać plik klucza publicznego (domyślnie `~/.ssh/id_rsa.pub`) do **pbcopy** Aby skopiować zawartość (istnieją inne programy systemu Linux, które wykonują tę samą operację, takich jak **xclip**).

Klucz publiczny, który można umieścić na maszynie Wirtualnej systemu Linux na platformie Azure są domyślnie przechowywane w `~/.ssh/id_rsa.pub`, chyba że położenie zostało zmienione po utworzeniu kluczy. Jeśli używasz [Azure CLI 2.0](/cli/azure) do utworzenia maszyny Wirtualnej z istniejącego klucza publicznego, określ wartość lub lokalizacji tego klucza publicznego, uruchamiając [tworzenia maszyny wirtualnej az](/cli/azure/vm#az_vm_create) z `--ssh-key-value` opcji. 

## <a name="ssh-to-your-vm"></a>SSH do maszyny Wirtualnej
Z kluczem publicznym wdrożone na maszynie Wirtualnej platformy Azure i klucza prywatnego w systemie lokalnym SSH do maszyny Wirtualnej przy użyciu adresu IP lub nazwę DNS maszyny Wirtualnej. Zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* w poniższym poleceniu z nazwą użytkownika administratora i pełną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy podano hasło, wprowadź je, gdy podczas logowania zostanie wyświetlony odpowiedni monit. (Serwer zostanie dodany do folderu `~/.ssh/known_hosts` i monit o ponowne połączenie nie zostanie wyświetlony, dopóki klucz publiczny na maszynie wirtualnej nie ulegnie zmianie lub nazwa serwera nie zostanie usunięta z folderu `~/.ssh/known_hosts`).

W domyślnej konfiguracji maszyn wirtualnych utworzonych przy użyciu kluczy SSH hasła są wyłączone, aby próby odgadnięcia hasła za pomocą ataków siłowych były znacznie bardziej kosztowne, a przez to również trudniejsze. 

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano tworzenie prostego parę kluczy SSH szybki sposób użycia. 

* Jeśli potrzebujesz więcej pomocy do pracy z Twojej pary kluczy SSH, zobacz [par kluczy szczegółowy opis kroków, aby utworzyć i zarządzać SSH](create-ssh-keys-detailed.md).

* Jeśli masz problemy z połączeń SSH maszyny Wirtualnej platformy Azure, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyny Wirtualnej systemu Linux Azure](troubleshoot-ssh-connection.md).


