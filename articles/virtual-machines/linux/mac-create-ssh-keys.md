---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Jak tworzenie i używanie pary kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux na platformie Azure w celu zwiększenia bezpieczeństwa procesu uwierzytelniania.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 2e3d86d776f44c47a33bf075cf7f2140a3940e5e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928458"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: tworzenie i używanie pary kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux na platformie Azure
Para kluczy Secure Shell (SSH) umożliwia tworzenie na platformie Azure maszyn wirtualnych, które używają kluczy SSH do uwierzytelniania, eliminując konieczność logowania przy użyciu haseł. W tym artykule przedstawiono sposób szybkiego generowania i używania pary plików kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux. Można wykonać te czynności przy użyciu usługi Azure Cloud Shell, macOS lub Linux hosta, podsystem Windows dla systemu Linux i innych narzędzi, które obsługują OpenSSH. 

Aby uzyskać więcej ogólnych informacji i przykładów, zobacz [par kluczy szczegółowe instrukcje dotyczące tworzenia SSH](create-ssh-keys-detailed.md).

Aby poznać dodatkowe sposoby generowania i używania kluczy SSH na komputerze Windows, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH
Użyj `ssh-keygen` polecenie, aby wygenerować SSH pliki publicznych i prywatnych kluczy, które są zazwyczaj tworzone w `~/.ssh` katalogu. Możesz określić inną lokalizację oraz dodatkowe hasło (do dostępu do pliku klucza prywatnego) po wyświetleniu monitu. Jeśli istnieje parę kluczy SSH w bieżącej lokalizacji, te pliki zostaną zastąpione.

```bash
ssh-keygen -t rsa -b 2048
```

Jeśli używasz [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) tworzysz maszynę Wirtualną, może opcjonalnie generować SSH pliki publicznych i prywatnych kluczy, uruchamiając [tworzenie az vm](/cli/azure/vm#az_vm_create) polecenia `--generate-ssh-keys` opcji. Klucze są przechowywane w katalogu ~/.ssh. Należy pamiętać, że to polecenie nie powoduje zastąpienia klucze Jeśli już istnieją w tej lokalizacji.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH w przypadku wdrażania maszyny Wirtualnej
Do utworzenia maszyny Wirtualnej systemu Linux, która używa kluczy SSH do uwierzytelniania, podaj swój klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal, interfejsu wiersza polecenia szablonów usługi Resource Manager lub innej metody:

* [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny Wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Jeśli nie jesteś zaznajomiony z formatem klucza publicznego SSH, możesz wyświetlić swój klucz publiczny, uruchamiając `cat` następująco, zastępując `~/.ssh/id_rsa.pub` z tej lokalizacji pliku klucza publicznego:

```bash
cat ~/.ssh/id_rsa.pub
```

Pamiętaj, aby podczas kopiowania i wklejania zawartości klucza publicznego do użycia w witrynie Azure Portal lub szablonie usługi Resource Manager nie kopiować dodatkowych spacji. Na przykład, jeśli używasz systemu macOS, można przekazać pliku klucza publicznego (domyślnie `~/.ssh/id_rsa.pub`) do **pbcopy** skopiować zawartość (istnieją inne programy dla systemu Linux, które obsługują to samo, takich jak **xclip**).

Klucz publiczny, który można umieścić na maszynie Wirtualnej systemu Linux na platformie Azure jest domyślnie przechowywany w `~/.ssh/id_rsa.pub`, chyba że zmieniono tę lokalizację, podczas tworzenia kluczy. Jeśli używasz [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) tworzysz maszynę Wirtualną przy użyciu istniejącego publicznego klucza, określ wartość lub lokalizację tego klucza publicznego, uruchamiając [tworzenie az vm](/cli/azure/vm#az_vm_create) polecenia `--ssh-key-value` opcji. 

## <a name="ssh-to-your-vm"></a>SSH z maszyną Wirtualną
Za pomocą klucza publicznego, wdrożone na maszynie Wirtualnej platformy Azure, a klucz prywatny w systemie lokalnym SSH z maszyną Wirtualną przy użyciu adresu IP lub nazwę DNS maszyny wirtualnej. Zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* w następującym poleceniu przy użyciu podanej nazwy użytkownika administratora i w pełni kwalifikowaną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy podano hasło, wprowadź je, gdy podczas logowania zostanie wyświetlony odpowiedni monit. (Serwer zostanie dodany do folderu `~/.ssh/known_hosts` i monit o ponowne połączenie nie zostanie wyświetlony, dopóki klucz publiczny na maszynie wirtualnej nie ulegnie zmianie lub nazwa serwera nie zostanie usunięta z folderu `~/.ssh/known_hosts`).

W domyślnej konfiguracji maszyn wirtualnych utworzonych przy użyciu kluczy SSH hasła są wyłączone, aby próby odgadnięcia hasła za pomocą ataków siłowych były znacznie bardziej kosztowne, a przez to również trudniejsze. 

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano tworzenie prostej pary kluczy SSH do szybkiego używania. 

* Jeśli potrzebujesz dodatkowej pomocy, aby pracować z pary kluczy SSH, zobacz [par kluczy szczegółowe instrukcje dotyczące tworzenia i zarządzania nimi SSH](create-ssh-keys-detailed.md).

* Jeśli masz problemy z połączeniami protokołu SSH z Maszyną wirtualną platformy Azure, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z Maszyną wirtualną systemu Linux platformy Azure](troubleshoot-ssh-connection.md).


