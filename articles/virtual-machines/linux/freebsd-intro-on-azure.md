---
title: Wprowadzenie do FreeBSD na platformie Azure | Microsoft Docs
description: Dowiedz się więcej o korzystaniu z maszyn wirtualnych FreeBSD na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: cce66ff5d5270596ef9d9911764b7eb2a6460fd7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083249"
---
# <a name="introduction-to-freebsd-on-azure"></a>Wprowadzenie do FreeBSD na platformie Azure
Ten artykuł zawiera omówienie uruchamiania maszyny wirtualnej FreeBSD na platformie Azure.

## <a name="overview"></a>Omówienie
FreeBSD for Microsoft Azure to zaawansowany system operacyjny komputera używany do obsługi nowoczesnych serwerów, komputerów stacjonarnych i platform osadzonych.

Firma Microsoft Corporation udostępnia obrazy FreeBSD dostępne na platformie Azure z wstępnie skonfigurowanym [agentem gościa maszyny wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) . Obecnie następujące wersje FreeBSD są oferowane jako obrazy przez firmę Microsoft:

- FreeBSD 10.3-RELEASE
- FreeBSD 10,4 — wydanie
- FreeBSD 11,1 — wydanie

Agent jest odpowiedzialny za komunikację między maszyną wirtualną FreeBSD i siecią szkieletową Azure dla operacji, takich jak inicjowanie obsługi maszyny wirtualnej przy pierwszym użyciu (nazwa użytkownika, hasło lub klucz SSH, nazwa hosta itp.) i Włączanie funkcji selektywnych rozszerzeń maszyn wirtualnych.

Tak jak w przypadku przyszłych wersji FreeBSD, strategia ma być aktualna i najnowsze wersje są dostępne wkrótce po ich opublikowaniu przez zespół inżynierów wydawniczych FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Wdrażanie maszyny wirtualnej FreeBSD
Wdrażanie maszyny wirtualnej FreeBSD jest prostym procesem przy użyciu obrazu z witryny Azure Marketplace z poziomu Azure Portal:

- [FreeBSD 10,4 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11,2 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Tworzenie maszyny wirtualnej FreeBSD za pomocą interfejsu wiersza polecenia platformy Azure na platformie FreeBSD
Najpierw należy zainstalować [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , chociaż następujące polecenie na komputerze FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Jeśli bash nie jest zainstalowana na maszynie FreeBSD, uruchom następujące polecenie przed instalacją. 

```bash
sudo pkg install bash
```

Jeśli nie zainstalowano języka Python na maszynie FreeBSD, uruchom następujące polecenia przed instalacją. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Podczas instalacji zostanie wyświetlony monit `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Jeśli odpowiesz `y` i `/etc/rc.conf` wprowadzisz jako `a path to an rc file to update`, możesz rozwiązać ten `ERROR: [Errno 13] Permission denied`problem. Aby rozwiązać ten problem, należy udzielić uprawnienia do zapisu dla bieżącego użytkownika względem pliku `etc/rc.conf`.

Teraz możesz zalogować się do platformy Azure i utworzyć maszynę wirtualną FreeBSD. Poniżej przedstawiono przykład tworzenia maszyny wirtualnej z systemem FreeBSD 11,0. Możesz również dodać parametr `--public-ip-address-dns-name` z globalnie unikatową nazwą DNS dla nowo utworzonego publicznego adresu IP. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Następnie możesz zalogować się do maszyny wirtualnej FreeBSD za pomocą adresu IP, który został wydrukowany w danych wyjściowych powyższego wdrożenia. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozszerzenia maszyn wirtualnych dla FreeBSD
Poniżej przedstawiono obsługiwane rozszerzenia maszyn wirtualnych w FreeBSD.

### <a name="vmaccess"></a>VMAccess
Rozszerzenie [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) może:

* Zresetuj hasło oryginalnego użytkownika sudo.
* Utwórz nowego użytkownika sudo z określonym hasłem.
* Ustaw publiczny klucz hosta z podanym kluczem.
* Zresetuj publiczny klucz hosta podany podczas aprowizacji maszyny wirtualnej, jeśli nie podano klucza hosta.
* Otwórz port SSH (22) i Przywróć sshd_config, jeśli reset_ssh jest ustawiona na true.
* Usuń istniejącego użytkownika.
* Sprawdź dyski.
* Naprawianie dodanego dysku.

### <a name="customscript"></a>CustomScript
Rozszerzenie [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) może:

* Jeśli to możliwe, Pobierz dostosowane skrypty z usługi Azure Storage lub zewnętrznego magazynu publicznego (na przykład GitHub).
* Uruchom skrypt punktu wejścia.
* Obsługuj polecenia wbudowane.
* Automatycznie Konwertuj nowy wiersz stylu systemu Windows w skryptach powłoki i języka Python.
* Automatycznie Usuń BOM ze skryptów powłoki i języka Python.
* Ochrona poufnych danych w sekcji commandtoexecute.

> [!NOTE]
> Maszyna wirtualna w FreeBSD obsługuje tylko CustomScript w wersji 1. x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Uwierzytelnianie: nazwy użytkowników, hasła i klucze SSH
Podczas tworzenia maszyny wirtualnej FreeBSD przy użyciu Azure Portal należy podać nazwę użytkownika, hasło lub klucz publiczny SSH.
Nazwy użytkowników do wdrożenia maszyny wirtualnej FreeBSD na platformie Azure nie mogą być zgodne z nazwami kont systemu (UID < 100), które już znajdują się na maszynie wirtualnej (na przykład "root").
Obecnie obsługiwany jest tylko klucz SSH RSA. Wielowierszowy klucz SSH musi zaczynać `---- BEGIN SSH2 PUBLIC KEY ----` się od i kończyć się znakiem. `---- END SSH2 PUBLIC KEY ----`

## <a name="obtaining-superuser-privileges"></a>Uzyskiwanie uprawnień administratora
Konto użytkownika określone podczas wdrażania wystąpienia maszyny wirtualnej na platformie Azure jest kontem uprzywilejowanym. Pakiet sudo został zainstalowany w opublikowanym obrazie FreeBSD.
Po zalogowaniu się za pomocą tego konta użytkownika można uruchomić polecenia jako główne przy użyciu składni polecenia.

```
$ sudo <COMMAND>
```

Opcjonalnie możesz uzyskać powłokę główną przy użyciu `sudo -s`.

## <a name="known-issues"></a>Znane problemy
[Agent gościa maszyny wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) w wersji 2.2.2 ma [znany problem](https://github.com/Azure/WALinuxAgent/pull/517) , który powoduje niepowodzenie inicjowania obsługi maszyny wirtualnej FreeBSD na platformie Azure. Poprawka została przechwycona przez [agenta gościa maszyny wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) w wersji 2.2.3 i nowszych. 

## <a name="next-steps"></a>Następne kroki
* Przejdź do [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) , aby utworzyć maszynę wirtualną FreeBSD.
