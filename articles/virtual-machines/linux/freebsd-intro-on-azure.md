---
title: Wprowadzenie do systemu FreeBSD na platformie Azure
description: Dowiedz się więcej o korzystaniu z maszyn wirtualnych FreeBSD na platformie Azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: 0825a29b45ea701315a57ff5248731e64e29de32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261639"
---
# <a name="introduction-to-freebsd-on-azure"></a>Wprowadzenie do systemu FreeBSD na platformie Azure
Ten artykuł zawiera omówienie uruchamiania maszyny wirtualnej FreeBSD na platformie Azure.

## <a name="overview"></a>Omówienie
FreeBSD for Microsoft Azure to zaawansowany system operacyjny dla komputerów służący do zasilania nowoczesnych serwerów, komputerów stacjonarnych i platform wbudowanych.

Firma Microsoft Corporation udostępnia obrazy freebsd na platformie Azure za pomocą wstępnie skonfigurowanego [agenta gościa maszyny Wirtualnej platformy Azure.](https://github.com/Azure/WALinuxAgent/) Obecnie następujące wersje FreeBSD są oferowane jako obrazy przez firmę Microsoft:

- [FreeBSD 10.4 w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12.0 w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

Agent jest odpowiedzialny za komunikację między maszyną wirtualną FreeBSD i sieci szkieletowej platformy Azure dla operacji, takich jak inicjowanie obsługi administracyjnej maszyny wirtualnej przy pierwszym użyciu (nazwa użytkownika, hasło lub klucz SSH, nazwa hosta itp.) i włączanie funkcji dla selektywnych rozszerzeń maszyn wirtualnych.

Jeśli chodzi o przyszłe wersje FreeBSD, strategia jest utrzymanie na bieżąco i udostępnić najnowsze wersje wkrótce po ich opublikowaniu przez zespół inżynierów wydania FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Tworzenie maszyny Wirtualnej FreeBSD za pośrednictwem interfejsu wiersza polecenia platformy Azure na freebsd
Najpierw musisz zainstalować [narzędzie interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) mimo że następujące polecenie na komputerze FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Jeśli bash nie jest zainstalowany na komputerze FreeBSD, uruchom następujące polecenie przed instalacją. 

```bash
sudo pkg install bash
```

Jeśli python nie jest zainstalowany na komputerze FreeBSD, uruchom następujące polecenia przed instalacją. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Podczas instalacji zostaniesz `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`poproszony . `y` Jeśli odpowiesz `/etc/rc.conf` i `a path to an rc file to update`wpisz jako , `ERROR: [Errno 13] Permission denied`może wystąpić problem . Aby rozwiązać ten problem, należy przyznać prawo do `etc/rc.conf`zapisu bieżącemu użytkownikowi w pliku .

Teraz możesz zalogować się na platformie Azure i utworzyć maszynę wirtualną FreeBSD. Poniżej znajduje się przykład utworzenia maszyny Wirtualnej FreeBSD 11.0. Można również dodać `--public-ip-address-dns-name` parametr o globalnie unikatowej nazwie DNS dla nowo utworzonego publicznego adresu IP. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Następnie można zalogować się do maszyny Wirtualnej FreeBSD za pośrednictwem adresu IP, który został wydrukowany w danych wyjściowych powyższego wdrożenia. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Rozszerzenia maszyn wirtualnych dla FreeBSD
Poniżej znajdują się obsługiwane rozszerzenia maszyn wirtualnych w FreeBSD.

### <a name="vmaccess"></a>VMAccess
Rozszerzenie [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) może:

* Zresetuj hasło oryginalnego użytkownika sudo.
* Utwórz nowego użytkownika sudo z określonym hasłem.
* Ustaw publiczny klucz hosta z podanym kluczem.
* Zresetuj publiczny klucz hosta podany podczas inicjowania obsługi administracyjnej maszyny Wirtualnej, jeśli klucz hosta nie jest podany.
* Otwórz port SSH (22) i przywróć sshd_config, jeśli reset_ssh jest ustawiona na true.
* Usuń istniejącego użytkownika.
* Sprawdź dyski.
* Naprawianie dodanego dysku.

### <a name="customscript"></a>Kod niestandardowy
Rozszerzenie [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) może:

* Jeśli podana, pobierz dostosowane skrypty z usługi Azure Storage lub zewnętrznego magazynu publicznego (na przykład GitHub).
* Uruchom skrypt punktu wejścia.
* Obsługa poleceń wbudowanych.
* Automatycznie konwertuj nową linię w stylu Windows w powłoki i skrypty Pythona.
* Automatycznie usuń BOM w osłonie i skryptach Pythona.
* Chroń poufne dane w CommandToExecute.

> [!NOTE]
> FreeBSD VM obsługuje tylko CustomScript w wersji 1.x już teraz.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Uwierzytelnianie: nazwy użytkowników, hasła i klucze SSH
Podczas tworzenia maszyny wirtualnej FreeBSD przy użyciu witryny Azure portal, należy podać nazwę użytkownika, hasło lub klucz publiczny SSH.
Nazwy użytkowników do wdrażania maszyny wirtualnej FreeBSD na platformie Azure nie mogą odpowiadać nazw kont systemowych (UID <100) już obecnych na maszynie wirtualnej (na przykład "root").
Obecnie obsługiwany jest tylko klucz SSH RSA. Wieloliniowy klucz SSH `---- BEGIN SSH2 PUBLIC KEY ----` musi `---- END SSH2 PUBLIC KEY ----`zaczynać się od pliku .

## <a name="obtaining-superuser-privileges"></a>Uzyskiwanie uprawnień administratora
Konto użytkownika, które jest określone podczas wdrażania wystąpienia maszyny wirtualnej na platformie Azure jest kontem uprzywilejowanym. Pakiet sudo został zainstalowany na opublikowanym obrazie FreeBSD.
Po zalogowaniu się za pośrednictwem tego konta użytkownika można uruchamiać polecenia jako katalog główny przy użyciu składni polecenia.

```
$ sudo <COMMAND>
```

Opcjonalnie można uzyskać powłokę `sudo -s`główną za pomocą programu .

## <a name="known-issues"></a>Znane problemy
[Agent gościa maszyny Wirtualnej platformy Azure](https://github.com/Azure/WALinuxAgent/) w wersji 2.2.2 ma znany [problem,](https://github.com/Azure/WALinuxAgent/pull/517) który powoduje błąd aprowidzania dla maszyny Wirtualnej FreeBSD na platformie Azure. Poprawka została przechwycona przez [agenta gościa platformy Azure w](https://github.com/Azure/WALinuxAgent/) wersji 2.2.3 i nowszych wersjach. 

## <a name="next-steps"></a>Następne kroki
* Przejdź do [witryny Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) aby utworzyć maszynę wirtualną FreeBSD.
