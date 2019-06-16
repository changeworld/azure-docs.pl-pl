---
title: Testowanie przepływności sieci maszyny Wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak przetestować przepływność sieci maszyny wirtualnej platformy Azure.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743085"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Przepustowość/testowania (NTTTCP)

Testowanie wydajności przepustowość sieci na platformie Azure, najlepiej użyć narzędzia, który jest przeznaczony dla sieci do testowania i minimalizuje użycie innych zasobów, które mogą mieć wpływ na wydajność. Zaleca się NTTTCP.

Skopiuj narzędzie do dwóch maszyn wirtualnych platformy Azure w tej samej wielkości. Jedna maszyna wirtualna działa jak NADAWCA, a drugi jako ODBIORNIK.

#### <a name="deploying-vms-for-testing"></a>Wdrażanie maszyn wirtualnych do testowania
Na potrzeby tego testu dwie maszyny wirtualne powinny być w tej samej usługi w chmurze lub w tym samym zestawie dostępności, można używać ich wewnętrznych adresów IP i wykluczyć modułów równoważenia obciążenia z testu. Istnieje możliwość testowania przy użyciu adresu VIP, ale tego rodzaju testy wykracza poza zakres tego dokumentu.

Zanotuj adres IP ODBIORNIKA. Nadajmy ten adres IP "a.b.c.r"

Zanotuj liczbę rdzeni na maszynie Wirtualnej. Nazwiemy to "\#num\_rdzeni"

Uruchom NTTTCP test 300 sekund (lub 5 minut) na nadawcy maszyny Wirtualnej i odbiorcy maszyny Wirtualnej.

Porada: Podczas konfigurowania tego testu po raz pierwszy, możesz spróbować krótszy okres test, aby szybciej uzyskać opinie. Gdy narzędzie działa zgodnie z oczekiwaniami, dotyczyć 300 sekund, aby uzyskać najbardziej dokładne wyniki testu.

> [!NOTE]
> Nadawca **i** odbiornika należy określić **takie same** parametru czas trwania testu (-t).

Aby przetestować jednemu strumieniowi TCP przez 10 sekund:

Parametry odbiorcy: ntttcp - r -t 10 - P 1

Parametry nadawcy: ntttcp-s10.27.33.7 metoda "-t 10 - n" 1 -P 1

> [!NOTE]
> Poprzedni przykład stosować tylko o potwierdzenie konfigurację. Prawidłowe przykłady testów zostały omówione w dalszej części tego dokumentu.

## <a name="testing-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Uzyskaj NTTTCP na maszynach wirtualnych.

Pobierz najnowszą wersję: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Lub wyszukaj go w przypadku przeniesienia: <https://www.bing.com/search?q=ntttcp+download> \< — należy najpierw zostanie osiągnięty.

Rozważ umieszczenie NTTTCP w oddzielnym folderze, np. c:\\narzędzia

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Zezwalaj na NTTTCP przez zaporę Windows
Na ODBIORNIK Utwórz regułę zezwalającą na zaporze Windows, aby zezwolić na ruch NTTTCP dostarczenie. Najłatwiej można umożliwić całego programu NTTTCP według nazwy, a nie umożliwia określonych portów TCP dla ruchu przychodzącego.

Zezwalaj na ntttcp przez zaporę Windows następująco:

netsh advfirewall zapory Dodawanie reguły programu =\<ścieżki\>\\ntttcp.exe nazwa = "ntttcp" protokołu = wszystkie dir = w działaniu = Zezwalaj na włączenie = yes profile = ANY

Na przykład, jeśli został skopiowany ntttcp.exe do "c:\\narzędzia" folder, to polecenie: 

netsh advfirewall zapory Dodawanie reguły programu = c:\\narzędzia\\ntttcp.exe nazwa = "ntttcp" protokołu = wszystkie dir = w działaniu = Zezwalaj na włączenie = yes profile = ANY

#### <a name="running-ntttcp-tests"></a>Uruchamianie testów NTTTCP

Rozpocznij NTTTCP odbiornika (**Uruchom z CMD**, a nie z programu PowerShell):

ntttcp - r – m [2\*\#num\_rdzeni],\*, a.b.c.r -t 300

Jeśli maszyna wirtualna ma cztery rdzenie i adresie IP 10.0.0.4, wyglądałby następująco:

ntttcp - r – m 8\*, 10.0.0.4 -t 300


Rozpocznij NTTTCP nadawcy (**Uruchom z CMD**, a nie z programu PowerShell):

ntttcp -s – m 8\*, 10.0.0.4 -t 300 

Poczekaj, aż wyniki.


## <a name="testing-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem LINUX:

Użyj nttcp dla systemu linux. Jest ona dostępna z <https://github.com/Microsoft/ntttcp-for-linux>

W systemie Linux maszyn wirtualnych (NADAWCĄ i odbiorcą) uruchom następujące polecenia, aby przygotować ntttcp for linux na maszynach wirtualnych:

CentOS — instalacja narzędzia Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Git instalacji:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Wprowadź, a następnie zainstalować zarówno:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Tak jak w przykładzie Windows przyjęto założenie, że adres IP ODBIORNIKA Linux 10.0.0.4

Rozpocznij NTTTCP dla systemu Linux w ODBIORNIKU:

``` bash
ntttcp -r -t 300
```

I na nadawcy, uruchom:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Biorąc pod uwagę testu długość wartość domyślna to 60 sekund, jeśli nie parametrze czasu

## <a name="testing-between-vms-running-windows-and-linux"></a>Testowanie między maszynami wirtualnymi z systemem Windows i LINUX:

W tym scenariuszy firma Microsoft Włącz tryb nie synchronizacji, aby uruchomić test. Jest to wykonywane przy użyciu **flagi -N** dla systemu Linux i **flagi -ns** dla Windows.

#### <a name="from-linux-to-windows"></a>Z systemu Linux do Windows:

Odbiornik \<Windows >:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Nadawca \<Linux >:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Od Windows z systemem Linux:

Odbiornik \<Linux >:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Sender \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testing Cloud Service Instances:
Należy dodać następujące sekcji do Twojej ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Kolejne kroki
* W zależności od wyników, może być miejsca, aby [optymalizacji maszyn przepływność sieci](virtual-network-optimize-network-bandwidth.md) dla danego scenariusza.
* Przeczytaj o tym, jak [przepustowość jest przydzielona do maszyn wirtualnych](virtual-machine-network-throughput.md)
* Dowiedz się więcej dzięki [sieci wirtualnej platformy Azure — często zadawane pytania (FAQ)](virtual-networks-faq.md)
