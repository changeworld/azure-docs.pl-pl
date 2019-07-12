---
title: Wprowadzenie do systemu FreeBSD na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o używaniu FreeBSD maszyn wirtualnych na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 248588ad43f3b3beefa35de468a21cecedc8913d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667787"
---
# <a name="introduction-to-freebsd-on-azure"></a>Wprowadzenie do systemu FreeBSD na platformie Azure
Ten artykuł zawiera omówienie działającej maszyny wirtualnej FreeBSD na platformie Azure.

## <a name="overview"></a>Omówienie
FreeBSD dla systemu Microsoft Azure to zaawansowane systemu operacyjnego używany do zasilania nowoczesnych serwerów, komputerów stacjonarnych i osadzić platformy.

Firma Microsoft udostępnia obrazy FreeBSD na platformie Azure dzięki [agenta gościa maszyny Wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) wstępnie skonfigurowane. Obecnie FreeBSD są oferowane następujące wersje jako obrazów przez firmę Microsoft:

- FreeBSD 10.3-RELEASE
- WERSJI 10.4 FreeBSD
- WERSJI 11.1 FreeBSD

Agent jest odpowiedzialny za komunikację między FreeBSD maszyny Wirtualnej i usługi Azure Service fabric dla operacji, takich jak inicjowanie obsługi administracyjnej maszyn wirtualnych przy pierwszym użyciu (nazwa użytkownika, hasło lub klucz SSH, nazwy hosta, itp.) oraz włączenie funkcji selektywnego rozszerzeń maszyn wirtualnych.

Jak w przypadku przyszłych wersji FreeBSD strategia polega na bieżąco i udostępnić najnowszych wersji, wkrótce, po ich opublikowaniu przez zespół inżynierów wersji FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Wdrażanie maszyny wirtualnej FreeBSD
Wdrażanie maszyny wirtualnej FreeBSD jest prosty proces, za pomocą obrazu z witryny Azure Marketplace w witrynie Azure portal:

- [FreeBSD 10.4 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Tworzenie maszyny Wirtualnej FreeBSD za pośrednictwem wiersza polecenia platformy Azure na FreeBSD
Najpierw musisz zainstalować [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) chociaż następujące polecenie na komputerze FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Jeśli bash nie jest zainstalowany na komputerze FreeBSD, uruchom następujące polecenie przed rozpoczęciem instalacji. 

```bash
sudo pkg install bash
```

Jeśli nie zainstalowano języka python na swojej maszynie FreeBSD, uruchom następujące polecenia, przed rozpoczęciem instalacji. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Podczas instalacji, zostanie wyświetlony monit, `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Jeśli odpowiesz `y` i wprowadź `/etc/rc.conf` jako `a path to an rc file to update`, może spełniać problem `ERROR: [Errno 13] Permission denied`. Aby rozwiązać ten problem, należy udzielić zapisu bezpośrednio do bieżącego użytkownika w odniesieniu do pliku `etc/rc.conf`.

Teraz można zalogować się do platformy Azure i tworzenie maszyny Wirtualnej FreeBSD. Poniżej znajduje się przykład, aby utworzyć Maszynę wirtualną 11.0 FreeBSD. Możesz również dodać parametr `--public-ip-address-dns-name` globalnie unikatową nazwą DNS dla nowo utworzonego publicznego adresu IP. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Następnie możesz zalogować się do maszyny Wirtualnej FreeBSD za pośrednictwem adresu ip, który drukowany w powyższych danych wyjściowych z wdrożenia. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozszerzenia maszyn wirtualnych dla systemu FreeBSD
Poniżej przedstawiono obsługiwane rozszerzenia maszyny Wirtualnej w FreeBSD.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) rozszerzenia można:

* Zresetuj hasło użytkownika oryginalnego "sudo".
* Utwórz nowego użytkownika "sudo" z podanym hasłem.
* Ustaw klucz publiczny hosta za pomocą klucza, biorąc pod uwagę.
* Resetuj klucz publiczny hosta podana podczas aprowizacji maszyny Wirtualnej, jeśli nie zostanie podany klucz hosta.
* Otwieranie portu SSH (22) i przywracanie sshd_config, jeśli reset_ssh jest ustawiona na wartość true.
* Usuwanie istniejącego użytkownika.
* Sprawdzanie dysków.
* Napraw dodany dysk.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) rozszerzenia można:

* Jeśli nie dostarczono, należy pobrać dostosowane skrypty z usługi Azure Storage lub magazynu publicznego zewnętrznego (na przykład GitHub).
* Uruchom skrypt punktu wejścia.
* Obsługuje wbudowanych poleceń.
* Automatycznie Konwertuj Windows style znakami nowego wiersza w powłoki i skryptów w języku Python.
* Automatyczne usuwanie BOM powłoki i skryptów w języku Python.
* Chroń poufne dane podczas CommandToExecute.

> [!NOTE]
> FreeBSD maszyn wirtualnych obsługuje tylko CustomScript wersji 1.x przeprowadzona.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Uwierzytelnianie: nazwy użytkownika, hasła i klucze SSH
Podczas tworzenia maszyny wirtualnej FreeBSD przy użyciu witryny Azure portal, należy podać nazwę użytkownika, hasło lub klucz publiczny SSH.
Nazwy użytkowników dla wdrażania maszyny wirtualnej FreeBSD na platformie Azure nie muszą odpowiadać nazwom kont systemu (UID < 100) już istnieje na maszynie wirtualnej ("root", na przykład).
Aktualnie obsługiwana jest tylko RSA SSH klucz. Wielowierszowy klucz SSH musi zaczynać się od `---- BEGIN SSH2 PUBLIC KEY ----` i kończyć się znakiem `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Uzyskanie uprawnień administratora
Konto użytkownika, który jest określony podczas wdrażania wystąpień maszyn wirtualnych na platformie Azure jest uprzywilejowanego konta. Pakiet "sudo" został zainstalowany w opublikowanego obrazu FreeBSD.
Po użytkownik zalogował się przy użyciu tego konta użytkownika, możesz uruchomić polecenia jako użytkownik główny, używając składni polecenia.

```
$ sudo <COMMAND>
```

Opcjonalnie można uzyskać shell głównej, przy użyciu `sudo -s`.

## <a name="known-issues"></a>Znane problemy
[Agenta gościa maszyny Wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) wersja 2.2.2 [znany problem](https://github.com/Azure/WALinuxAgent/pull/517) powodujący błąd aprowizacji dla maszyny Wirtualnej z FreeBSD na platformie Azure. Poprawka została przechwycona przez [agenta gościa maszyny Wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) wersji 2.2.3 i nowszych wersjach. 

## <a name="next-steps"></a>Następne kroki
* Przejdź do [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) do utworzenia maszyny Wirtualnej z systemem FreeBSD.
