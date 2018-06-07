---
title: Przy użyciu rozszerzenia maszyny Wirtualnej platformy Docker dla systemu Linux | Dokumentacja firmy Microsoft
description: Opisuje Docker i rozszerzenia maszyn wirtualnych platformy Azure i sposobu tworzenia maszyn wirtualnych Azure, które są hostów docker w klasycznym modelu wdrażania przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 76497f58678e5ecfbab7d263b3adb4c475763cd8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653590"
---
# <a name="using-the-docker-vm-extension-with-the-azure-portal"></a>Przy użyciu rozwiązania Docker rozszerzenia maszyny Wirtualnej z portalu Azure
> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[Docker](https://www.docker.com/) jest jednym z najpopularniejszych podejść wirtualizacji, które używa [kontenery Linux](http://en.wikipedia.org/wiki/LXC) zamiast maszyn wirtualnych w sposób izolowanie danych i przetwarzania danych w udostępnionych zasobów. Można użyć rozszerzenia maszyny Wirtualnej platformy Docker zarządza [Azure agenta systemu Linux] tworzenie Docker maszyny Wirtualnej, który obsługuje dowolną liczbę kontenerów dla aplikacji na platformie Azure.

> [!NOTE]
> W tym temacie opisano sposób tworzenia maszyny Wirtualnej platformy Docker w portalu Azure. Aby sprawdzić, jak można utworzyć maszyny Wirtualnej platformy Docker w wierszu polecenia, zobacz [Jak używać Docker rozszerzenia maszyny Wirtualnej z interfejsu wiersza polecenia platformy Azure (Azure CLI)]. Aby wyświetlić ogólne omówienie kontenery i ich zalety, zobacz [Docker wysoki poziom tablicy](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Utwórz nową maszynę Wirtualną z galerii obrazów
Pierwszym krokiem wymaga maszyny Wirtualnej platformy Azure z obrazu systemu Linux, który obsługuje Docker rozszerzenia maszyny Wirtualnej, używając obrazu Ubuntu 14.04 LTS z galerii obrazów jako przykład obrazu serwera i Ubuntu 14.04 pulpitu jako klienta. W portalu kliknij **+ nowy** do utworzenia nowego wystąpienia maszyny Wirtualnej, a następnie wybierz obraz Ubuntu 14.04 LTS z dostępnych opcji lub pełną Galeria obrazów, jak pokazano poniżej.

> [!NOTE]
> Obecnie tylko obrazy Ubuntu 14.04 LTS nowsza niż lipca 2014 obsługują Docker rozszerzenia maszyny Wirtualnej.
> 
> 

![Utwórz nowy obraz Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Tworzenie certyfikatów Docker
Po utworzeniu maszyny Wirtualnej, upewnij się, że Docker jest zainstalowany na komputerze klienckim. (Aby uzyskać więcej informacji, zobacz [instrukcje dotyczące instalacji Docker](https://docs.docker.com/installation/#installation).)

Tworzenie plików certyfikat i klucz do komunikacji Docker zgodnie z [Uruchomiony Docker z protokołu https] i umieścić je w **`~/.docker`** katalogu na komputerze klienckim.

> [!NOTE]
> Rozszerzenia maszyny Wirtualnej platformy Docker w portalu obecnie wymagane poświadczenia, które są kodowany w standardzie base64.
> 
> 

W wierszu polecenia, użyj **`base64`** lub innego ulubionego narzędzia kodowania, aby utworzyć tematy algorytmem Base64. W ten sposób z prostym zestawem plików certyfikat i klucz może wyglądać podobnie do poniższego:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Dodawanie rozszerzenia maszyny Wirtualnej platformy Docker
Aby dodać Docker rozszerzenia maszyny Wirtualnej, zlokalizować wystąpienia maszyny Wirtualnej utworzone, a następnie przewiń w dół do **rozszerzenia** i kliknij go, aby wyświetlić rozszerzeń maszyny Wirtualnej, jak pokazano poniżej.

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Dodaj rozszerzenie
Kliknij przycisk **+ Dodaj** do wyświetlenia możliwych rozszerzeń maszyny Wirtualnej, można dodać do tej maszyny Wirtualnej.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Wybierz rozszerzenia maszyny Wirtualnej platformy Docker
Wybierz Docker rozszerzenia maszyny Wirtualnej, który wywołuje Docker opis i ważne łącza, a następnie kliknij polecenie **Utwórz** na dole, aby rozpocząć procedurę instalacji.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Dodaj certyfikat i pliki klucza:
Do pól formularza wprowadź algorytmem Base64 wersje certyfikat urzędu certyfikacji, certyfikat serwera i klucz serwera, jak pokazano na poniższym rysunku.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Należy pamiętać, czy (tak jak w poprzednim obrazu) 2376 wypełnieniu domyślnie. Możesz wprowadzić tutaj dowolnego punktu końcowego, ale następnym krokiem będzie otwarcie punktu końcowego pasującego. Wartość domyślna w przypadku zmiany, upewnij się, że otwarcie punktu końcowego pasującego w następnym kroku.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Dodaj punkt końcowy komunikacji Docker
Podczas wyświetlania po utworzeniu grupy zasobów, wybierz grupy zabezpieczeń sieci skojarzonych z maszyny Wirtualnej i kliknij przycisk **reguły zabezpieczeń ruchu przychodzącego** Aby wyświetlić reguły, jak pokazano poniżej.

![](media/portal-use-docker/AddingEndpoint.png)

Kliknij przycisk **+ Dodaj** dodanie inną regułę, a w przypadku domyślny, wprowadź nazwę dla punktu końcowego (w tym przykładzie **Docker**), a 2376 "zakres portów docelowych". Ustaw przedstawiający wartość protokołu **TCP**i kliknij przycisk **OK** do utworzenia reguły.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Przetestować klienta Docker i hostów Azure Docker
Zlokalizuj i skopiuj nazwę domeny maszyny Wirtualnej, a w wierszu polecenia na komputerze klienckim typu `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (gdzie *dockerextension* zastępuje poddomeny dla maszyny Wirtualnej).

Wynik powinien być podobny do poniższego:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Po wykonaniu powyższych czynności masz teraz funkcjonalnej hostów Docker uruchomione na maszynie Wirtualnej platformy Azure, skonfigurować połączyć się zdalnie z innych klientów.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Kolejne kroki
Wszystko będzie gotowe przejść do [Podręcznik użytkownika Docker] i użyć maszyny Wirtualnej platformy Docker. Jeśli chcesz zautomatyzować tworzenie hostów Docker na maszynach wirtualnych Azure za pomocą interfejsu wiersza polecenia, zobacz [Jak używać Docker rozszerzenia maszyny Wirtualnej z interfejsu wiersza polecenia platformy Azure (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Jak używać Docker rozszerzenia maszyny Wirtualnej z interfejsu wiersza polecenia platformy Azure (Azure CLI)]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure agenta systemu Linux]:../../extensions/agent-linux.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Uruchomiony Docker z protokołu https]:http://docs.docker.com/articles/https/
[Podręcznik użytkownika docker]:https://docs.docker.com/userguide/
