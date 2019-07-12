---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Jak tworzenie i używanie pary kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux na platformie Azure w celu zwiększenia bezpieczeństwa procesu uwierzytelniania.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: c26d58b5b6d39f05a1b077f89627e2fdde3653c7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671389"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: Tworzenie i używanie pary kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux na platformie Azure

Przy użyciu protokołu secure shell (SSH) pary kluczy można utworzyć maszyny wirtualne (VM) na platformie Azure, które używają kluczy SSH do uwierzytelniania, eliminując konieczność łączenia się haseł do logowania. W tym artykule przedstawiono sposób szybkiego generowania i używania pary plików kluczy publiczny prywatny SSH dla maszyn wirtualnych systemu Linux. Można wykonać te czynności przy użyciu usługi Azure Cloud Shell, macOS lub Linux hosta, podsystem Windows dla systemu Linux i innych narzędzi, które obsługują OpenSSH. 

> [!NOTE]
> Maszyny wirtualne utworzone przy użyciu kluczy SSH są domyślnie skonfigurowane przy użyciu haseł wyłączone, co znacznie zwiększa trudności odgadnięcia ataków siłowych. 

Aby uzyskać więcej ogólnych informacji i przykładów, zobacz [par kluczy szczegółowe instrukcje dotyczące tworzenia SSH](create-ssh-keys-detailed.md).

Aby poznać dodatkowe sposoby generowania i używania kluczy SSH na komputerze Windows, zobacz [jak używać protokołu SSH kluczy przy użyciu Windows Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Użyj `ssh-keygen` polecenie, aby wygenerować SSH pliki publicznych i prywatnych kluczy. Domyślnie te pliki są tworzone w katalogu ~/.ssh. Można określić inną lokalizację i opcjonalnie hasło (*hasło*) do dostępu do pliku klucza prywatnego. Jeśli pary kluczy SSH o takiej samej nazwie istnieje w danej lokalizacji, te pliki zostaną zastąpione.

Następujące polecenie tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długość w bitach 2048:

```bash
ssh-keygen -t rsa -b 2048
```

Jeśli używasz [wiersza polecenia platformy Azure](/cli/azure) tworzysz maszynę Wirtualną za pomocą [tworzenie az vm](/cli/azure/vm#az-vm-create) polecenia i może opcjonalnie generować SSH pliki publicznych i prywatnych kluczy przy użyciu `--generate-ssh-keys` opcji. Pliki kluczy są przechowywane w katalogu ~/.ssh, chyba że określono inaczej, za pomocą `--ssh-dest-key-path` opcji. `--generate-ssh-keys` Opcja nie spowoduje zastąpienie istniejących plików kluczy, w zamian zwróci błąd. W poniższym poleceniu zastąp *VMname* i *RGname* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj publiczny klucz SSH, podczas wdrażania maszyny Wirtualnej

Aby utworzyć maszynę Wirtualną systemu Linux, która używa kluczy SSH do uwierzytelniania, należy określić klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure, szablony usługi Azure Resource Manager lub innych metod:

* [Utwórz maszynę wirtualną systemu Linux w witrynie Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Utwórz maszynę wirtualną systemu Linux przy użyciu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny Wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Jeśli nie jesteś zaznajomiony z formatem klucza publicznego SSH, możesz wyświetlić swój klucz publiczny z następującymi `cat` polecenia, zastępując `~/.ssh/id_rsa.pub` z ścieżkę i nazwę własnego pliku klucza publicznego w razie potrzeby:

```bash
cat ~/.ssh/id_rsa.pub
```

Typowa wartość klucza publicznego wygląda następująco:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Jeśli skopiujesz i wkleisz zawartość pliku klucza publicznego do użycia w witrynie Azure portal lub szablonu usługi Resource Manager, upewnij się, że wszystkie końcowe białe znaki nie są kopiowane. Aby skopiować klucz publiczny w systemie macOS, można przekazać pliku klucza publicznego do **pbcopy**. Podobnie w systemie Linux, można przekazać pliku klucza publicznego do programów takich jak **xclip**.

Klucz publiczny, który można umieścić na maszynie Wirtualnej systemu Linux na platformie Azure jest domyślnie przechowywany w ~ /.ssh/id_rsa.pub, o ile nie określono innej lokalizacji, podczas tworzenia pary kluczy. Do użycia [interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure) tworzysz maszynę Wirtualną przy użyciu istniejącego publicznego klucza, określ wartość i opcjonalnie lokalizację tego klucza publicznego przy użyciu [tworzenie az vm](/cli/azure/vm#az-vm-create) polecenia `--ssh-key-value` opcji. W poniższym poleceniu zastąp *VMname*, *RGname*, i *keyFile* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Za pomocą klucza publicznego, wdrożone na maszynie Wirtualnej platformy Azure, a klucz prywatny w systemie lokalnym SSH z maszyną wirtualną przy użyciu adresu IP lub nazwę DNS maszyny wirtualnej. W poniższym poleceniu zastąp *azureuser* i *myvm.westus.cloudapp.azure.com* przy użyciu podanej nazwy użytkownika administratora i w pełni kwalifikowaną nazwę domeny (lub adres IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli hasło jest określony, podczas tworzenia pary kluczy, wprowadź hasło, że po wyświetleniu monitu podczas procesu logowania. Maszyna wirtualna zostanie dodany do Twojego pliku ~/.ssh/known_hosts i nie będzie trzeba podawać połączyć się ponownie do czasu albo klucza publicznego w zmiany maszyny Wirtualnej platformy Azure lub nazwa serwera jest usuwana z ~/.ssh/known_hosts.

Jeśli maszyna wirtualna używa zasad dostępu just in time, należy zażądać dostępu, aby nawiązać połączenie z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [zarządzanie dostępem maszyny wirtualnej przy użyciu tylko w zasadach czasu](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat pracy z pary kluczy SSH, zobacz [par kluczy szczegółowe instrukcje dotyczące tworzenia i zarządzania nimi SSH](create-ssh-keys-detailed.md).

* Jeśli masz problemy z połączeniami SSH do maszyn wirtualnych platformy Azure, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z Maszyną wirtualną systemu Linux platformy Azure](troubleshoot-ssh-connection.md).
