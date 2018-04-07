---
title: Wprowadzenie do FreeBSD na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o używaniu maszyn wirtualnych FreeBSD na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
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
ms.openlocfilehash: 9c7cf223eab3e989436e12c39b122f2aee7619a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-freebsd-on-azure"></a>Wprowadzenie do FreeBSD na platformie Azure
Ten temat zawiera omówienie uruchomienie maszyny wirtualnej FreeBSD na platformie Azure.

## <a name="overview"></a>Przegląd
FreeBSD platformy Microsoft Azure to zaawansowane systemu operacyjnego używany do zasilania nowoczesnych serwerów, komputerów stacjonarnych i osadzone platform.

Microsoft Corporation jest udostępniania obrazów FreeBSD na platformie Azure za pomocą [Agent gościa maszyny Wirtualnej Azure](https://github.com/Azure/WALinuxAgent/) wstępnie skonfigurowane. Obecnie następujące wersje FreeBSD są jako obrazów oferowanych przez firmę Microsoft:

- FreeBSD 10.3-RELEASE
- FreeBSD 11.0-RELEASE
- FreeBSD 11.1-RELEASE

Agent jest odpowiedzialny za komunikację między FreeBSD maszyny Wirtualnej i sieci szkieletowej Azure dla operacji, takich jak inicjowanie obsługi maszyn wirtualnych przy pierwszym użyciu (nazwa użytkownika, hasło lub klucz SSH, nazwy hosta, itp.) i włączenie funkcji selektywnego rozszerzeń maszyny Wirtualnej.

Podobnie jak w przypadku przyszłych wersji FreeBSD strategii jest Pozostań na bieżąco i udostępnić najnowsze wersje, wkrótce po ich opublikowaniu przez zespół inżynieryjny wersji FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Wdrażanie maszyny wirtualnej FreeBSD
Wdrażanie maszyny wirtualnej FreeBSD jest dość proste przy użyciu obrazu z portalu Azure Marketplace z portalu Azure:

- [FreeBSD 10.3 w witrynie Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 w witrynie Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)
- [FreeBSD 11.1 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Tworzenie maszyny Wirtualnej FreeBSD za pośrednictwem interfejsu wiersza polecenia platformy Azure 2.0 na FreeBSD
Najpierw należy zainstalować [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) chociaż następujące polecenia na komputerze FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Jeśli bash nie jest zainstalowany na tym komputerze FreeBSD, uruchom następujące polecenie przed rozpoczęciem instalacji. 

```bash
sudo pkg install bash
```

Jeśli python nie jest zainstalowany na tym komputerze FreeBSD, uruchom następujące polecenia, przed rozpoczęciem instalacji. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Podczas instalacji, zostanie wyświetlona prośba `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Jeśli wybierzesz odpowiedź `y` , a następnie wprowadź `/etc/rc.conf` jako `a path to an rc file to update`, problem może spełniać `ERROR: [Errno 13] Permission denied`. Aby rozwiązać ten problem, należy udzielić zapisu prawo do bieżącego użytkownika w odniesieniu do pliku `etc/rc.conf`.

Teraz możesz zalogować się na platformie Azure i utworzyć FreeBSD maszyny Wirtualnej. Poniżej znajduje się przykład można utworzyć maszyny Wirtualnej 11.0 FreeBSD. Możesz także dodać parametr `--public-ip-address-dns-name` z globalnie unikatowej nazwy DNS dla nowo utworzonego publicznego adresu IP. 

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

## <a name="vm-extensions-for-freebsd"></a>Rozszerzenia maszyny Wirtualnej dla FreeBSD
Poniżej przedstawiono obsługiwane rozszerzeń maszyny Wirtualnej w FreeBSD.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) rozszerzenia można:

* Zresetuj hasło dla oryginalnego użytkownika sudo.
* Utwórz nowego użytkownika sudo z określone hasło.
* Ustaw klucz publiczny hosta przy użyciu danego klucza.
* Zresetować klucz publiczny hosta dostarczone podczas dostarczania maszyny Wirtualnej, jeśli nie zostanie podany klucz hosta.
* Otwarcie portu SSH (22) i przywrócić sshd_config, jeśli reset_ssh jest ustawiona na true.
* Usuwanie istniejącego użytkownika.
* Sprawdź dyski.
* Napraw dodanych dysków.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) rozszerzenia można:

* Jeśli zostanie podana, należy pobrać dostosowane skrypty z usługi Azure Storage lub magazynu publicznego zewnętrznego (na przykład GitHub).
* Uruchom skrypt punktu wejścia.
* Obsługuje wbudowanego polecenia.
* Konwertuj automatycznie dopasuje styl systemu Windows w powłoki i skryptów języka Python.
* Automatycznie Usuń BOM powłoki i skryptów języka Python.
* Ochrona poufnych danych w CommandToExecute.

> [!NOTE]
> Maszyna wirtualna FreeBSD obsługuje tylko wersję CustomScript 1.x przez teraz.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Uwierzytelniania: nazwy użytkowników, hasła i klucze SSH
Podczas tworzenia maszyny wirtualnej FreeBSD przy użyciu portalu Azure, musisz podać nazwę użytkownika, hasło lub klucz publiczny SSH.
Nazwy użytkownika podczas wdrażania maszyny wirtualnej FreeBSD na platformie Azure nie muszą być zgodne nazwy kont systemowych (UID < 100) znajduje się już w maszynie wirtualnej ("root", na przykład).
Aktualnie obsługiwana jest tylko RSA klucza SSH. Wielowierszowy klucz SSH musi zaczynać się od `---- BEGIN SSH2 PUBLIC KEY ----` się i kończyć `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Uzyskanie uprawnień administratora
Konto użytkownika, które określono podczas wdrażania wystąpienia maszyny wirtualnej na platformie Azure jest uprzywilejowanego konta. Pakiet sudo został zainstalowany w opublikowanej FreeBSD obrazu.
Po zalogowano się za pomocą tego konta użytkownika, możesz uruchamiać polecenia jako główny przy użyciu składni polecenia.

```
$ sudo <COMMAND>
```

Powłoka głównego Opcjonalnie można uzyskać za pomocą `sudo -s`.

## <a name="known-issues"></a>Znane problemy
[Agent gościa maszyny Wirtualnej Azure](https://github.com/Azure/WALinuxAgent/) wersji 2.2.2 ma [znany problem] (https://github.com/Azure/WALinuxAgent/pull/517) powodujący błąd udostępniania dla maszyny Wirtualnej FreeBSD na platformie Azure. Ta poprawka została przechwycona przez [Agent gościa maszyny Wirtualnej Azure](https://github.com/Azure/WALinuxAgent/) wersji 2.2.3 i jego nowszych wersjach. 

## <a name="next-steps"></a>Kolejne kroki
* Przejdź do [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) tworzenie FreeBSD maszyny Wirtualnej.
