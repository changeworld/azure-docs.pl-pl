---
title: Tworzenie i używanie pary kluczy SSH dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Jak utworzyć i używać pary kluczy publiczno-prywatnych SSH dla maszyn wirtualnych z systemem Linux na platformie Azure, aby poprawić bezpieczeństwo procesu uwierzytelniania.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969056"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Szybkie kroki: tworzenie i używanie pary kluczy publiczno-prywatnych SSH dla maszyn wirtualnych z systemem Linux na platformie Azure

Za pomocą pary kluczy bezpiecznej powłoki (SSH) można tworzyć maszyny wirtualne (VM) na platformie Azure, które używają kluczy SSH do uwierzytelniania, eliminując konieczność logowania się haseł. W tym artykule pokazano, jak szybko wygenerować i używać pary plików klucza publiczno-prywatnego SSH dla maszyn wirtualnych z systemem Linux. Te kroki można wykonać za pomocą usługi Azure Cloud Shell, hosta systemu macOS lub Linux, podsystemu windows dla systemu Linux i innych narzędzi obsługujących usługę OpenSSH. 

> [!NOTE]
> Maszyny wirtualne utworzone przy użyciu kluczy SSH są domyślnie skonfigurowane z wyłączonymi hasłami, co znacznie zwiększa poziom trudności ataków zgadujących typu brute-force. 

Aby uzyskać więcej tła i przykładów, zobacz [Szczegółowe kroki tworzenia par kluczy SSH](create-ssh-keys-detailed.md).

Aby uzyskać dodatkowe sposoby generowania i używania kluczy SSH na komputerze z systemem Windows, zobacz [Jak używać kluczy SSH z systemem Windows na platformie Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Tworzenie pary kluczy SSH

`ssh-keygen` Polecenie służy do generowania plików kluczy publicznych i prywatnych SSH. Domyślnie pliki te są tworzone w katalogu ~/.ssh. Można określić inną lokalizację i*opcjonalne*hasło ( hasło ), aby uzyskać dostęp do pliku klucza prywatnego. Jeśli para kluczy SSH o tej samej nazwie istnieje w danej lokalizacji, pliki te są zastępowane.

Następujące polecenie tworzy parę kluczy SSH przy użyciu szyfrowania RSA i długości bitowej 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Jeśli używasz [interfejsu wiersza polecenia platformy Azure](/cli/azure) do utworzenia maszyny Wirtualnej za pomocą polecenia [az vm create,](/cli/azure/vm#az-vm-create) opcjonalnie można wygenerować pliki kluczy publicznych i prywatnych SSH przy użyciu `--generate-ssh-keys` tej opcji. Pliki kluczy są przechowywane w katalogu ~/.ssh, `--ssh-dest-key-path` chyba że określono inaczej za pomocą opcji. Opcja `--generate-ssh-keys` nie zastąpi istniejących plików kluczy, zamiast tego zwróci błąd. W poniższym poleceniu zastąp *VMname* i *RGname* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Podaj klucz publiczny SSH podczas wdrażania maszyny Wirtualnej

Aby utworzyć maszynę wirtualną z systemem Linux, która używa kluczy SSH do uwierzytelniania, określ klucz publiczny SSH podczas tworzenia maszyny Wirtualnej przy użyciu witryny Azure portal, interfejsu wiersza polecenia platformy Azure, szablonów usługi Azure Resource Manager lub innych metod:

* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Linux za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Tworzenie maszyny wirtualnej systemu Linux przy użyciu szablonu platformy Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Jeśli nie znasz formatu klucza publicznego SSH, możesz wyświetlić klucz `cat` publiczny `~/.ssh/id_rsa.pub` za pomocą następującego polecenia, zastępując ścieżką i nazwami plików własnego pliku klucza publicznego, jeśli zajdzie taka potrzeba:

```bash
cat ~/.ssh/id_rsa.pub
```

Typowa wartość klucza publicznego wygląda następująco:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Jeśli skopiujesz i wklejasz zawartość pliku klucza publicznego do użycia w witrynie Azure portal lub szablonie Menedżera zasobów, upewnij się, że nie kopiujesz żadnych końcowych odstępów. Aby skopiować klucz publiczny w systemie macOS, `pbcopy`można przekąsić plik klucza publicznego do pliku . Podobnie w systemie Linux, można potok pliku klucza `xclip`publicznego do programów, takich jak .

Klucz publiczny umieszczany na maszynie Wirtualnej systemu Linux na platformie Azure jest domyślnie przechowywany w ~/.ssh/id_rsa.pub, chyba że podczas tworzenia pary kluczy określono inną lokalizację. Aby użyć [interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure) do utworzenia maszyny Wirtualnej z istniejącym kluczem publicznym, należy `--ssh-key-values` określić wartość i opcjonalnie lokalizację tego klucza publicznego przy użyciu polecenia [az vm create](/cli/azure/vm#az-vm-create) z opcją. W następującym poleceniu zastąp *VMname*, *RGname*i *keyFile* własnymi wartościami:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Jeśli chcesz używać wielu kluczy SSH z maszyną wirtualną, możesz wprowadzić je `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`na liście oddzielonej spacją, w tym stylu .


## <a name="ssh-into-your-vm"></a>Łączenie z maszyną wirtualną za pośrednictwem protokołu SSH

Po wdrożeniu klucza publicznego na maszynie wirtualnej platformy Azure i klucza prywatnego w systemie lokalnym ssh do maszyny wirtualnej przy użyciu adresu IP lub nazwy DNS maszyny Wirtualnej. W poniższym poleceniu zastąp *użytkownika platformy Azure* i *myvm.westus.cloudapp.azure.com* nazwą użytkownika administratora i w pełni kwalifikowaną nazwą domeny (lub adresem IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Jeśli podczas tworzenia pary kluczy określono hasło, wprowadź to hasło po wyświetleniu monitu podczas procesu logowania. Maszyna wirtualna jest dodawana do pliku ~/.ssh/known_hosts i nie zostaniesz poproszony o ponowne połączenie, dopóki nie zmieni się klucz publiczny na maszynie Wirtualnej platformy Azure lub nazwa serwera zostanie usunięta z ~/.ssh/known_hosts.

Jeśli maszyna wirtualna używa zasad dostępu just-in-time, należy zażądać dostępu, zanim będzie można połączyć się z maszyną wirtualną. Aby uzyskać więcej informacji na temat zasad just-in-time, zobacz [Zarządzanie dostępem do maszyny wirtualnej przy użyciu zasad just in time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat pracy z parami kluczy SSH, zobacz [Szczegółowe kroki tworzenia par kluczy SSH i zarządzania nimi](create-ssh-keys-detailed.md).

* Jeśli masz trudności z połączeniami SSH z maszynami wirtualnymi platformy Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną systemu Azure Linux](troubleshoot-ssh-connection.md).
