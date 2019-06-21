---
title: Optymalizowanie przepływności sieci maszyny Wirtualnej | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202914"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optymalizowanie przepływności sieci maszyn wirtualnych platformy Azure

Usługa Azure virtual machines (VM) ma domyślne ustawienia sieci, które można dodatkowo zoptymalizować przepustowości sieci. W tym artykule opisano sposób optymalizowanie przepływności sieci dla programu Microsoft Azure Windows i maszyn wirtualnych systemu Linux, łącznie z głównych są takie dystrybucje jak Red Hat, Ubuntu i CentOS.

## <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows

Jeśli maszyna wirtualna Windows obsługuje [Accelerated Networking](create-vm-accelerated-networking-powershell.md), włączenie tej funkcji może być konfiguracją optymalną przepływności. W przypadku wszystkich innych Windows maszyn wirtualnych przy użyciu skalowanie po stronie odbierania (RSS) docierać do większej przepływności maksymalny niż maszynę Wirtualną bez danych RSS. Funkcja RSS może być domyślnie wyłączona w systemie maszyny Wirtualnej z systemem Windows. Aby ustalić, czy RSS jest włączone, a następnie włącz go, jeśli jest obecnie wyłączona, wykonaj następujące czynności:

1. Zobacz, czy funkcja RSS jest włączone dla karty sieciowej z `Get-NetAdapterRss` polecenia programu PowerShell. W następujące przykładowe dane wyjściowe zwrócone w wyniku `Get-NetAdapterRss`, RSS jest wyłączona.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Aby włączyć RSS, wprowadź następujące polecenie:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Poprzednie polecenie nie ma danych wyjściowych. Polecenie zmienić ustawienia karty Sieciowej, powodujące utraty łączności tymczasowego dla około jednej minuty. Podczas utraty połączenia pojawia się okno dialogowe Ponowne nawiązywanie połączenia. Zwykle po przywróceniu łączności po próbie trzeciego.
3. Upewnij się, że RSS jest włączone w maszynę Wirtualną, wprowadzając `Get-NetAdapterRss` ponownie polecenie. Jeśli to się powiedzie, jest zwracany następujące przykładowe dane wyjściowe:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Maszyna wirtualna z systemem Linux

Funkcja RSS jest zawsze włączona domyślnie w maszynie Wirtualnej systemu Linux platformy Azure. Jądra systemu Linux wydane od października 2017 r. obejmują nowe opcje optymalizacji sieci, które umożliwiają maszyny Wirtualnej systemu Linux w celu uzyskania większej przepływności sieci.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu w przypadku nowych wdrożeń

Jądra Ubuntu Azure zapewnia najlepszą wydajność sieci na platformie Azure i została domyślnego jądra od 21 września 2017 r. Aby uzyskać ten jądra, najpierw w następujący sposób zainstalować najnowszą obsługiwaną wersję 16.04 LTS:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po zakończeniu tworzenia wprowadź następujące polecenia w celu uzyskania najnowszych aktualizacji. Te kroki również działać w przypadku maszyn wirtualnych, aktualnie uruchomione jądra Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Następujący zestaw opcjonalne polecenia mogą być pomocne dla istniejących wdrożeń Ubuntu, który jeszcze jądrze systemu Azure, ale który nie powiodło się dalsze aktualizacje z błędami.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure jądra uaktualnienie dla istniejących maszyn wirtualnych

Po uaktualnieniu do jądra systemu Linux platformy Azure można osiągnąć znaczne obniżenie przepustowości. Aby sprawdzić, czy ten jądra, sprawdź wersję jądra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Jeśli maszyna wirtualna nie ma jądra systemu Azure, numer wersji zazwyczaj rozpoczyna się od "w wersji 4.4." Jeśli maszyna wirtualna nie ma jądra systemu Azure, uruchom następujące polecenia jako użytkownik główny:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Aby uzyskać najnowsze optymalizacje, najlepiej jest utworzyć maszynę Wirtualną z najnowszą obsługiwaną wersję, określając następujące parametry:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalowanie najnowszych integracji usługi LIS (Linux). Optymalizacji w trybie przepływności jest lis, od 4.2.2-2, chociaż nowsze wersje zawierają dalsze ulepszenia. Wprowadź następujące polecenia, aby zainstalować najnowsze LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Aby uzyskać optymalizacje, najlepiej jest utworzyć maszynę Wirtualną z najnowszą obsługiwaną wersję, określając następujące parametry:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalowanie najnowszych integracji usługi LIS (Linux). Optymalizacji w trybie przepływności jest lis, zaczynając od 4.2. Wprowadź następujące polecenia, aby pobrać i zainstalować LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Więcej informacji na temat 4.2 wersję usług integracji systemu Linux dla funkcji Hyper-V, wyświetlając [stronę pobierania](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Kolejne kroki
* Zobacz zoptymalizowane wyników za pomocą [przepustowość/testowania maszyny Wirtualnej platformy Azure](virtual-network-bandwidth-testing.md) dla danego scenariusza.
* Przeczytaj o tym, jak [przepustowość jest przydzielona do maszyn wirtualnych](virtual-machine-network-throughput.md)
* Dowiedz się więcej dzięki [sieci wirtualnej platformy Azure — często zadawane pytania (FAQ)](virtual-networks-faq.md)
