---
title: Dla systemu Linux na platformie Azure przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker
description: Opisuje Docker i rozszerzenia maszyny wirtualnej platformy Azure i pokazuje, jak programowo Tworzenie maszyn wirtualnych na platformie Azure, które są hostów docker z poziomu wiersza polecenia przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: 91f7ea54afce0e94953d4bb01bbb1b33f167fe22
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Korzystanie z rozszerzenia maszyny wirtualnej platformy Docker za pomocą interfejsu wiersza polecenia platformy (Azure CLI)
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Aby dowiedzieć się, jak za pomocą rozszerzenia maszyny Wirtualnej platformy Docker z modelu Resource Manager, zobacz [tutaj](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

W tym temacie opisano sposób tworzenia maszyny Wirtualnej z rozszerzenia maszyny Wirtualnej platformy Docker niż tryb zarządzania (asm) usługi Azure CLI na dowolnej platformie. [Docker](https://www.docker.com/) jest jednym z najpopularniejszych podejść wirtualizacji, które używa [kontenery Linux](http://en.wikipedia.org/wiki/LXC) zamiast maszyn wirtualnych w sposób izolowanie danych i przetwarzania danych w udostępnionych zasobów. Można użyć rozszerzenia maszyny Wirtualnej platformy Docker i [agenta systemu Linux Azure](../../extensions/agent-linux.md) tworzenie Docker maszyny Wirtualnej, który obsługuje dowolną liczbę kontenerów dla aplikacji na platformie Azure. Aby wyświetlić ogólne omówienie kontenery i ich zalety, zobacz [Docker wysoki poziom tablicy](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Jak użyć Docker rozszerzenia maszyny Wirtualnej platformy Azure
Aby użyć rozszerzenia Docker maszyny Wirtualnej platformy Azure, należy zainstalować wersję [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) wyższa niż 0.8.6 (zgodnie z tym bieżąca wersja to 0.10.0). Interfejs wiersza polecenia platformy Azure można zainstalować na Mac, Linux i Windows.

Zakończenie procesu do użycia Docker na platformie Azure jest prosty:

* Instalowanie interfejsu wiersza polecenia Azure i jego zależności na komputerze, z którego chcesz kontrolować Azure (w systemie Windows, to będzie dystrybucji systemu Linux, uruchomione jako maszyny wirtualnej)
* Utwórz hosta maszyny Wirtualnej platformy Docker na platformie Azure za pomocą poleceń Azure CLI Docker
* Lokalne polecenia Docker umożliwia zarządzanie kontenerów Docker w maszynie Wirtualnej platformy Docker na platformie Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Instalowanie platformy Azure interfejsu wiersza polecenia (Azure CLI)
Aby zainstalować i skonfigurować interfejs wiersza polecenia Azure, zobacz [jak zainstalować interfejs wiersza polecenia Azure](../../../cli-install-nodejs.md). Aby sprawdzić instalację, wpisz `azure` w wierszu polecenia i po chwili krótkich powinna zostać wyświetlona grafikę ASCII interfejsu wiersza polecenia Azure, który zawiera listę poleceń podstawowe dostępne dla Ciebie. Jeśli instalacja działał poprawnie, powinno być możliwe do typu `azure help vm` i sprawdzić, czy jest jedną z wymienionych polecenia "docker".

> [!NOTE]
> Docker ma narzędzi dla systemu Windows, [maszyny Docker](https://docs.docker.com/installation/windows/), którym można również zautomatyzować tworzenie klienta docker, który służy do pracy z maszyn wirtualnych platformy Azure jako hostów docker.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Azure CLI do nawiązania połączenia konta platformy Azure
Przed użyciem wiersza polecenia platformy Azure poświadczenia konta Azure należy skojarzyć z wiersza polecenia platformy Azure na platformie. Sekcja [sposób nawiązywania połączenia z subskrypcją platformy Azure](/cli/azure/authenticate-azure-cli) wyjaśniono, jak pobrać i zaimportować Twojej **.publishsettings** plików lub kojarzenie z wiersza polecenia platformy Azure przy użyciu identyfikatora organizacyjnego.

> [!NOTE]
> Istnieją pewne różnice w zachowaniu, korzystając z jednego lub innych metod uwierzytelniania, dlatego zaleca się przeczytanie dokumentu powyżej, aby poznać różne funkcje.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Zainstaluj Docker i użyj rozszerzenia maszyny Wirtualnej platformy Docker na platformie Azure
Postępuj zgodnie z [instrukcje dotyczące instalacji Docker](https://docs.docker.com/installation/#installation) zainstalować Docker lokalnie na komputerze.

Aby użyć Docker z maszyny wirtualnej platformy Azure, musi mieć obraz Linux używany dla maszyny Wirtualnej [Agent maszyny Wirtualnej systemu Linux](../../extensions/agent-linux.md) zainstalowane. Obecnie istnieją tylko dwa typy obrazów, które to zapewniają:

* Obraz Ubuntu z galerii obrazów Azure lub
* Niestandardowy obraz systemu Linux utworzone przy użyciu agenta maszyny Wirtualnej systemu Linux Azure zainstalowane i skonfigurowane. Zobacz [Agent maszyny Wirtualnej systemu Linux](../../extensions/agent-linux.md) Aby uzyskać więcej informacji o sposobie tworzenia niestandardowych maszyny Wirtualnej systemu Linux przy użyciu agenta maszyny Wirtualnej Azure.

### <a name="using-the-azure-image-gallery"></a>Za pomocą galerii Azure obrazu
Z Bash lub sesję terminalu Użyj następującego polecenia wiersza polecenia platformy Azure można znaleźć najnowszy Ubuntu obraz w galerii maszyn wirtualnych do użycia przez wpisanie

`azure vm image list | grep Ubuntu-14_04`

i wybierz jedną z nazw obrazów, takich jak `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`i użyj następującego polecenia, aby utworzyć nową maszynę Wirtualną za pomocą tego obrazu.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Gdzie:

* *&lt;Nazwa maszyny wirtualnej cloudservice&gt;*  jest nazwa maszyny wirtualnej, który ma zostać komputera hosta kontenera Docker na platformie Azure
* *&lt;Nazwa użytkownika&gt;*  jest nazwa użytkownika głównych domyślnego maszyny wirtualnej
* *&lt;hasło&gt;*  jest hasło *username* konta, które spełnia wymogi złożoności dla platformy Azure

> [!NOTE]
> Obecnie hasło musi być co najmniej 8 znaków, zawiera jeden małe litery i Wielka litera, liczbę i znaków specjalnych, takich jak jeden z następujących znaków: `!@#$%^&+=`. Nie, okres na końcu poprzedniego zdania nie jest znak specjalny.
> 
> 

Jeśli polecenie zostało zakończone pomyślnie, powinien zostać wyświetlony przypominać następujące polecenie, w zależności od argumentów dokładne i opcje używanego:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> Tworzenie maszyny wirtualnej może potrwać kilka minut, ale po zostały udostępnione (wartość stanu jest `ReadyRole`) uruchamiania demona Docker (usługa Docker) i możesz połączyć się z hostem kontenera Docker.
> 
> 

Aby przetestować Docker maszyny Wirtualnej zostały utworzone na platformie Azure, wpisz

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

gdzie *&lt;vm nazwa--użyta&gt;* jest nazwa maszyny wirtualnej, który został użyty w wywołania do `azure vm docker create`. Powinny zostać wyświetlone informacje podobne do następujących, co oznacza, że Host maszyny Wirtualnej platformy Docker działa i działających na platformie Azure i Oczekiwanie na poleceniach. 

Teraz możesz spróbować połączyć się przy użyciu klienta programu docker można uzyskać informacji o (w niektórych ustawień klienta Docker, takim jak dla komputerów Mac, może być konieczne użycie `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Chcemy mieć pewność, że jest ona wszystkie pracy, należy zbadać Docker rozszerzenia maszyny Wirtualnej:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Uwierzytelnianie programu docker hosta maszyny Wirtualnej
Oprócz tworzenia wirtualna Docker `azure vm docker create` polecenia automatycznie tworzy wymagane certyfikaty, aby zezwolić na komputerze klienckim Docker do połączenia z hostem kontenera platformy Azure przy użyciu protokołu HTTPS, a certyfikaty są przechowywane na klienta i Host maszyn, zależnie od potrzeb. W kolejnych prób istniejących certyfikatów są ponownie i udostępniane z nowym hostem.

Domyślnie certyfikaty są umieszczane w `~/.docker`, i Docker zostanie skonfigurowany do uruchamiania na porcie **2376**. Jeśli chcesz użyć innego portu lub katalog, a następnie można użyć jednego z następujących `azure vm docker create` opcji wiersza polecenia, aby skonfigurować Twoje kontenera Docker hosta maszyny Wirtualnej, aby użyć innego portu lub różnych certyfikatów klientów nawiązujących połączenie:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Demon Docker na hoście jest skonfigurowana do nasłuchiwania i uwierzytelnianie połączeń klienta na określonym porcie przy użyciu certyfikaty generowane przez `azure vm docker create` polecenia. Komputer kliencki musi mieć te certyfikaty w celu uzyskania dostępu do hostów Docker.

> [!NOTE]
> Sieci hosta z systemem bez tych certyfikatów będzie narażony na każdy użytkownik może połączyć się z komputerem. Aby zmodyfikować domyślną konfigurację, upewnij się, że rozumiesz zagrożeń dotyczących komputerów i aplikacji.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
* Wszystko będzie gotowe przejść do [Podręcznik użytkownika Docker] i użyć maszyny Wirtualnej platformy Docker. Aby utworzyć Maszynę wirtualną z włączonym Docker w nowego portalu, zobacz [jak Docker rozszerzenia maszyny Wirtualnej za pomocą portalu].
* Rozszerzenie maszyny Wirtualnej platformy Docker Azure obsługuje również rozwiązania Docker Compose, używający deklaratywne pliku yaml programu do podjęcia modelowane Deweloper aplikacji w każdym środowisku oraz do generowania spójne wdrażanie. Zobacz [wprowadzenie Docker i wysyłanych do definiowania i uruchomić aplikację usługi kontenera na maszynie wirtualnej platformy Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[jak Docker rozszerzenia maszyny Wirtualnej za pomocą portalu]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Podręcznik użytkownika Docker]:https://docs.docker.com/userguide/

[wprowadzenie Docker i wysyłanych do definiowania i uruchomić aplikację usługi kontenera na maszynie wirtualnej platformy Azure]:../docker-compose-quickstart.md
