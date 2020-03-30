---
title: Optymalizacja przepustowości sieci maszyn wirtualnych | Dokumenty firmy Microsoft
description: Dowiedz się, jak zoptymalizować przepływność sieci maszyny wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: be5f38bdeaf51dbe23006ecf30b4deb66aa7402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690885"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optymalizacja przepływności sieci dla maszyn wirtualnych platformy Azure

Maszyny wirtualne platformy Azure (VM) mają domyślne ustawienia sieciowe, które można dodatkowo zoptymalizować pod kątem przepływności sieci. W tym artykule opisano sposób optymalizacji przepływności sieci dla maszyn wirtualnych microsoft azure windows i linux, w tym głównych dystrybucji, takich jak Ubuntu, CentOS i Red Hat.

## <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows

Jeśli maszyna wirtualna z systemem Windows obsługuje [przyspieszoną sieć,](create-vm-accelerated-networking-powershell.md)włączenie tej funkcji byłoby optymalną konfiguracją dla przepływności. W przypadku wszystkich innych maszyn wirtualnych z systemem Windows użycie skalowania strony odbierania (RSS) może osiągnąć wyższą maksymalną przepustowość niż maszyna wirtualna bez RSS. RSS może być domyślnie wyłączony na maszynie Wirtualnej systemu Windows. Aby ustalić, czy funkcja RSS jest włączona, i włączyć ją, jeśli jest obecnie wyłączona, wykonaj następujące czynności:

1. Sprawdź, czy funkcja RSS jest włączona dla karty sieciowej za pomocą polecenia `Get-NetAdapterRss` programu PowerShell. W poniższym przykładzie `Get-NetAdapterRss`dane wyjściowe zwrócone z , RSS nie jest włączona.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Aby włączyć funkcję RSS, wprowadź następujące polecenie:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Poprzednie polecenie nie ma danych wyjściowych. Polecenie zmieniło ustawienia karty sieciowej, powodując tymczasową utratę łączności przez około minutę. Okno dialogowe Ponowne łączenie jest wyświetlane podczas utraty łączności. Łączność jest zazwyczaj przywracana po trzeciej próbie.
3. Upewnij się, że RSS jest włączony na `Get-NetAdapterRss` maszynie wirtualnej, wprowadzając polecenie ponownie. W przypadku powodzenia zwracany jest następujący przykładowy wynik:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Maszyna wirtualna z systemem Linux

RSS jest zawsze domyślnie włączona na maszynie wirtualnej systemu Azure Linux. Jądra Linuksa wydane od października 2017 r. zawierają nowe opcje optymalizacji sieci, które umożliwiają maszynie Wirtualnej Systemu Linux osiągnięcie wyższej przepustowości sieci.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu dla nowych wdrożeń

Jądro platformy Azure Ubuntu zapewnia najlepszą wydajność sieci na platformie Azure i jest domyślnym jądrem od 21 września 2017 roku. Aby uzyskać to jądro, najpierw zainstaluj najnowszą obsługiwaną wersję 16.04-LTS, w następujący sposób:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po zakończeniu tworzenia wprowadź następujące polecenia, aby uzyskać najnowsze aktualizacje. Te kroki działają również dla maszyn wirtualnych aktualnie uruchomionych jądra platformy Azure Ubuntu.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Poniższy opcjonalny zestaw poleceń może być przydatny w przypadku istniejących wdrożeń Ubuntu, które mają już jądro platformy Azure, ale nie powiodły się dalsze aktualizacje z błędami.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Uaktualnienie jądra platformy Ubuntu Azure dla istniejących maszyn wirtualnych

Znaczną wydajność przepływności można osiągnąć, uaktualniając jądro systemu Azure Linux. Aby sprawdzić, czy masz to jądro, sprawdź wersję jądra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Jeśli maszyna wirtualna nie ma jądra platformy Azure, numer wersji zwykle zaczyna się od "4.4". Jeśli maszyna wirtualna nie ma jądra platformy Azure, uruchom następujące polecenia jako katalog główny:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Aby uzyskać najnowsze optymalizacje, najlepiej jest utworzyć maszynę wirtualną z najnowszą obsługiwaną wersją, określając następujące parametry:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalacji najnowszych usług integracji systemu Linux (LIS). Optymalizacja przepływności jest w lis, począwszy od 4.2.2-2, chociaż nowsze wersje zawierają dalsze ulepszenia. Wprowadź następujące polecenia, aby zainstalować najnowsze lisy:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Aby uzyskać optymalizacje, najlepiej jest utworzyć maszynę wirtualną z najnowszą obsługiwaną wersją, określając następujące parametry:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalacji najnowszych usług integracji systemu Linux (LIS). Optymalizacja przepływności jest w lis, począwszy od 4.2. Wprowadź następujące polecenia, aby pobrać i zainstalować lis:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Dowiedz się więcej o usługach integracji linuksowej w wersji 4.2 dla funkcji Hyper-V, przeglądając [stronę pobierania](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Następne kroki
* Zobacz zoptymalizowany wynik z [bandwidth/przepływności testowania maszyny Wirtualnej platformy Azure](virtual-network-bandwidth-testing.md) dla twojego scenariusza.
* Przeczytaj o [przydzielaniu przepustowości do maszyn wirtualnych](virtual-machine-network-throughput.md)
* Dowiedz się więcej dzięki [często zadawanym pytaniom usługi Azure Virtual Network (CZĘSTO ZADAWANE PYTANIA)](virtual-networks-faq.md)
