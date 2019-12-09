---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Jak utworzyć i używać pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure w celu zwiększenia bezpieczeństwa procesu uwierzytelniania.
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
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 6cf636e7d7ee35680c1da872b186748c333a81dc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930004"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: Tworzenie pary kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure

Za pomocą pary kluczy Secure Shell (SSH) można tworzyć maszyny wirtualne na platformie Azure, które używają kluczy SSH do uwierzytelniania, eliminując konieczność logowania przy użyciu haseł. W tym artykule pokazano, jak szybko generować i używać pary plików prywatnego publicznego klucza SSH dla maszyn wirtualnych z systemem Linux. Te kroki można wykonać przy użyciu Azure Cloud Shell, hosta macOS lub Linux, podsystemu Windows dla systemu Linux i innych narzędzi, które obsługują OpenSSH. 

> [!NOTE]
> Maszyny wirtualne utworzone przy użyciu kluczy SSH są domyślnie skonfigurowane z wyłączonymi hasłami, co znacznie zwiększa trudność ataków typu "rozpuszczenie". 

Aby uzyskać więcej informacji o tle i przykładach, zobacz [szczegółowe instrukcje dotyczące tworzenia par kluczy SSH](create-ssh-keys-detailed.md).

Aby uzyskać dodatkowe sposoby generowania i używania kluczy SSH na komputerze z systemem Windows, zobacz [jak używać kluczy SSH w systemie Windows na platformie Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

Użyj `ssh-keygen` polecenie, aby wygenerować pliki publicznego i prywatnego klucza SSH. Domyślnie te pliki są tworzone w katalogu ~/.SSH. Aby uzyskać dostęp do pliku klucza prywatnego, można określić inną lokalizację i opcjonalne hasło (*hasło*). Jeśli para kluczy SSH o tej samej nazwie istnieje w danej lokalizacji, te pliki są zastępowane.

Następujące polecenie tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długość bitową 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) , aby utworzyć maszynę wirtualną z poleceniem [AZ VM Create](/cli/azure/vm#az-vm-create) , możesz opcjonalnie wygenerować pliki publicznego i prywatnego klucza SSH przy użyciu opcji `--generate-ssh-keys`. Pliki kluczy są przechowywane w katalogu ~/.SSH, chyba że określono inaczej z opcją `--ssh-dest-key-path`. Opcja `--generate-ssh-keys` nie spowoduje zastąpienia istniejących plików kluczy, zamiast zwraca błąd. W poniższym poleceniu Zastąp wartości *VMName* i *RGname* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, określ klucz publiczny SSH podczas tworzenia maszyny wirtualnej przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, szablonów Azure Resource Manager lub innych metod:

* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Jeśli nie znasz formatu klucza publicznego SSH, możesz wyświetlić klucz publiczny za pomocą następującego `cat` polecenia, zastępując `~/.ssh/id_rsa.pub` ścieżką i nazwą pliku klucza publicznego w razie konieczności:

```bash
cat ~/.ssh/id_rsa.pub
```

Typowa wartość klucza publicznego wygląda podobnie do tego przykładu:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Jeśli skopiujesz i wkleisz zawartość pliku klucza publicznego do użycia w Azure Portal lub szablon Menedżer zasobów, pamiętaj, aby nie kopiować żadnych białych znaków. Aby skopiować klucz publiczny w macOS, można przekazać plik klucza publicznego do **pbcopy**. Podobnie w systemie Linux można potokować plik klucza publicznego do programów, takich jak **XCLIP**.

Klucz publiczny umieszczany na maszynie wirtualnej z systemem Linux na platformie Azure jest domyślnie przechowywany w folderze ~/.ssh/id_rsa. pub, chyba że podczas tworzenia pary kluczy określono inną lokalizację. Aby użyć [interfejsu wiersza polecenia platformy Azure 2,0](/cli/azure) do utworzenia maszyny wirtualnej z istniejącym kluczem publicznym, określ wartość i opcjonalnie lokalizację tego klucza publicznego przy użyciu polecenia [AZ VM create](/cli/azure/vm#az-vm-create) z opcją `--ssh-key-value`. W poniższym poleceniu Zastąp wartości *VMName*, *RGname*i *keyFile* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Z kluczem publicznym wdrożonym na maszynie wirtualnej platformy Azure i kluczem prywatnym w systemie lokalnym Użyj protokołu SSH do maszyny wirtualnej przy użyciu adresu IP lub nazwy DNS maszyny wirtualnej. W poniższym poleceniu Zastąp *azureuser* i *MyVM.westus.cloudapp.Azure.com* nazwą użytkownika administratora oraz w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy określono hasło, wprowadź je po wyświetleniu monitu podczas procesu logowania. Maszyna wirtualna jest dodawana do pliku ~/.ssh/known_hosts i nie zostanie wyświetlony monit o ponowne nawiązanie połączenia do momentu zmiany klucza publicznego na maszynie wirtualnej platformy Azure lub usunięcia nazwy serwera z ~/.ssh/known_hosts.

Jeśli maszyna wirtualna korzysta z zasad dostępu just in Time, przed nawiązaniem połączenia z maszyną wirtualną należy zażądać dostępu. Aby uzyskać więcej informacji na temat zasad just in Time, zobacz [Zarządzanie dostępem do maszyn wirtualnych przy użyciu zasad just in Time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z parami kluczy SSH, zobacz [szczegółowe instrukcje dotyczące tworzenia par kluczy SSH i zarządzania nimi](create-ssh-keys-detailed.md).

* Jeśli masz problemy z połączeniami SSH z maszynami wirtualnymi platformy Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure Linux](troubleshoot-ssh-connection.md).
