---
title: Zoptymalizować przepływność sieci maszyny Wirtualnej | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: b604782f917584d1ecec432c20de75f427176ed1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29132895"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Zoptymalizować przepływność sieci maszyn wirtualnych platformy Azure

Maszyny wirtualne platformy Azure (VM) ma domyślne ustawienia sieci, które mogą być optymalizowane więcej przepustowości sieci. W tym artykule opisano, jak zoptymalizować przepływność sieci systemu Microsoft Azure Windows oraz maszyn wirtualnych systemu Linux, łącznie z głównych dystrybucje, takich jak Ubuntu i CentOS, Red Hat.

## <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows

Jeśli maszyna wirtualna systemu Windows obsługuje [przyspieszony sieci](create-vm-accelerated-networking-powershell.md), włączenie tej funkcji może być konfiguracją optymalną przepustowości. Dla wszystkich innych maszyn wirtualnych systemu Windows przy użyciu skalowanie po stronie odbierającej (RSS) mogą skorzystać z wyższej przepustowości maksymalnej niż Maszynę wirtualną bez RSS. Funkcja RSS mogą być wyłączone domyślnie na maszynie wirtualnej systemu Windows. Aby ustalić, czy funkcja RSS jest włączone i włącz go, jeśli jest obecnie wyłączona, wykonaj następujące kroki:

1. Czy funkcja RSS jest włączona dla karty sieciowej z `Get-NetAdapterRss` polecenia programu PowerShell. W poniższym przykładzie danych wyjściowych zwrócony z `Get-NetAdapterRss`, funkcja RSS nie jest włączona.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Aby włączyć funkcję RSS, wprowadź następujące polecenie:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Poprzednie polecenie nie ma danych wyjściowych. Polecenie zmienić ustawienia karty Sieciowej, powodując utracie połączenia tymczasowego około jednej minuty. Okno dialogowe Reconnecting pojawia się podczas utraty połączenia. Po próbie trzeci zwykle przywróceniu łączności.
3. Potwierdź, że funkcja RSS jest włączona na maszynie wirtualnej, wprowadzając `Get-NetAdapterRss` polecenie ponownie. W przypadku powodzenia zwrócono następujący przykład danych wyjściowych:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux VM

Funkcja RSS jest zawsze włączona domyślnie w maszynie Wirtualnej systemu Linux platformy Azure. Jądra systemu Linux wydane od października 2017 obejmują nowe opcje optymalizacji sieci, które umożliwiają maszyny Wirtualnej systemu Linux w celu osiągnięcia wyższej przepustowości sieci.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu o nowych wdrożeniach

Jądro Ubuntu Azure zapewnia najlepszą wydajność sieci na platformie Azure i została jądra domyślnej od 21 września 2017 r. Aby uzyskać ten jądra, najpierw zainstaluj najnowszą obsługiwaną wersję 16.04-LTS, następujący sposób:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Po zakończeniu tworzenia, wprowadź następujące polecenia, aby pobrać najnowsze aktualizacje. Te kroki są również działać dla aktualnie uruchomionych jądra Ubuntu Azure maszyn wirtualnych.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Następujący zestaw poleceń opcjonalnie może być przydatne dla istniejących wdrożeń Ubuntu, które mają już Azure jądra, ale które nie udało się dodatkowe aktualizacje z błędami.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure uaktualniania jądra dla istniejących maszyn wirtualnych

Można osiągnąć znaczne przepustowość przez uaktualnienie do jądra systemu Linux platformy Azure. Aby sprawdzić, czy ta jądra, sprawdź wersję jądra.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Jeśli maszyna wirtualna nie ma Azure jądra, numer wersji zwykle zaczyna się od "4.4." Jeśli maszyna wirtualna nie ma jądra Azure, uruchom następujące polecenia, jako katalogu głównego:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Aby uzyskać najnowsze funkcje optymalizacji, najlepiej utworzyć Maszynę wirtualną z najnowszej wersji obsługiwanych przez określenie następujących parametrów:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalowania najnowsze usługi integracji systemu Linux (LIS). Optymalizacji przepływności jest LIS, zaczynając od 4.2.2-2, chociaż nowsze wersje zawierają dodatkowe ulepszenia. Wprowadź następujące polecenia, aby zainstalować najnowsze LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Aby uzyskać optymalizacji, najlepiej utworzyć Maszynę wirtualną z najnowszej wersji obsługiwanych przez określenie następujących parametrów:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nowe i istniejące maszyny wirtualne mogą korzystać z instalowania najnowsze usługi integracji systemu Linux (LIS). Optymalizacji przepływności jest LIS, zaczynając od 4.2. Wprowadź następujące polecenia, aby pobrać i zainstalować LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Dowiedz się więcej o Linux Integration Services wersji 4.2 dla funkcji Hyper-V przeglądając [stronę pobierania](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Kolejne kroki
* Zobacz zoptymalizowane wyniku [przepustowości/przepływność, testowanie maszyny Wirtualnej Azure](virtual-network-bandwidth-testing.md) dla danego scenariusza.
* Przeczytaj temat [przepustowości jest przydzielona do maszyny wirtualnej] (wirtualny machine sieci throughput.md)
* Dowiedz się więcej o [sieci wirtualnej platformy Azure — często zadawane pytania (FAQ)](virtual-networks-faq.md)
