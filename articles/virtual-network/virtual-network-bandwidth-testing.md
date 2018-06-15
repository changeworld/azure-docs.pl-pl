---
title: Testowanie przepływność sieci maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przetestować przepływność sieci maszyny wirtualnej platformy Azure.
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
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: d65b86cc63a4fd39824a6421afd5ce9abb7fd270
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200983"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Przepustowość/przepływność, testowanie (NTTTCP)

Podczas testowania przepustowość sieci na platformie Azure, najlepiej użyć narzędzia dotyczy sieci do testowania i minimalizuje inne zasoby, które mogą wpłynąć na wydajność. NTTTCP jest zalecane.

Skopiuj narzędzie do dwóch maszyn wirtualnych platformy Azure o tej samej wielkości. Jedna maszyna wirtualna działa jak NADAWCA, a drugi jako ODBIORNIK.

#### <a name="deploying-vms-for-testing"></a>Wdrażanie maszyn wirtualnych na potrzeby testowania
Na potrzeby tego testu dwóch maszyn wirtualnych powinna być w tej samej usługi w chmurze lub tego samego zestawu dostępności, aby firma Microsoft może używać ich wewnętrznych adresów IP i wykluczyć usługi równoważenia obciążenia z testu. Można przetestować przy użyciu adresu VIP, ale tego rodzaju testowania wykracza poza zakres tego dokumentu.
 
Zanotuj adres IP ODBIORNIKA. Umożliwia wywołanie tego adresu IP "a.b.c.r"

Zanotuj liczbę rdzeni na maszynie Wirtualnej. Umożliwia to wywołanie "\#num\_rdzeni"
 
Testu NTTTCP 300 sekund (lub 5 minut) na nadawcy maszyny Wirtualnej i odbiorcy maszyny Wirtualnej.

Porada: Podczas konfigurowania tego testu po raz pierwszy, spróbuj krótszy okres testu wcześniej uzyskanie opinii. Po narzędziu działa zgodnie z oczekiwaniami, rozszerzyć 300 sekund dla najdokładniejszych wyników testu.

> [!NOTE]
> Nadawca **i** odbiornika należy określić **takie same** parametru czas trwania testu (-t).

Aby przetestować jednego strumienia TCP przez 10 sekund:

Odbiornik parametrów: - r ntttcp -t 10 - P 1

Nadawca parametry: ntttcp-s10.27.33.7 metoda "-t 10 - n" 1 -P 1

> [!NOTE]
> Powyższego przykładu należy używać tylko w celu potwierdzenia konfiguracji. Prawidłowe przykłady testów są uwzględnione w dalszej części tego dokumentu.

## <a name="testing-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Pobierz NTTTCP na maszynach wirtualnych.

Pobierz najnowszą wersję: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Lub wyszukaj go w przypadku przeniesienia: <https://www.bing.com/search?q=ntttcp+download> \< — należy najpierw trafień

Należy rozważyć umieszczenie w oddzielnym folderze, takich jak c: NTTTCP\\narzędzia

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Zezwalaj na NTTTCP przez zaporę systemu Windows
Odbiornika należy utworzyć regułę Zezwalaj zaporę systemu Windows, aby zezwolić na ruch NTTTCP odebranie. Najłatwiej Zezwalaj całego programu NTTTCP według nazwy, a nie umożliwia ruchu przychodzącego dla określonych portów TCP.

Zezwalaj na ntttcp przez zaporę systemu Windows w następujący sposób:

netsh advfirewall zapory Dodawanie reguły programu =\<ścieżki\>\\ntttcp.exe nazwa = "ntttcp" protokołu żadnych dir = = w akcji = Zezwalaj enable = yes profile = ANY

Na przykład, jeśli został skopiowany ntttcp.exe do "c:\\narzędzia" folder, to polecenie: 

netsh advfirewall zapory Dodawanie reguły programu = c:\\narzędzia\\ntttcp.exe nazwa = "ntttcp" protokołu żadnych dir = = w akcji = Zezwalaj enable = yes profile = ANY

#### <a name="running-ntttcp-tests"></a>Uruchamianie testów NTTTCP

Uruchom NTTTCP odbiornika (**Uruchom w powłoce CMD z**, a nie z programu PowerShell):

ntttcp - r — m [2\*\#num\_rdzeni],\*, a.b.c.r -t 300

Jeśli maszyna wirtualna ma cztery rdzeni i adres IP 10.0.0.4, będzie wyglądać następująco:

ntttcp - r — m 8,\*, 10.0.0.4 -t 300


Uruchom NTTTCP na nadawcy (**Uruchom w powłoce CMD z**, a nie z programu PowerShell):

ntttcp -s — m 8,\*, 10.0.0.4 -t 300 

Poczekaj na wyniki.


## <a name="testing-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem LINUX:

Użyj nttcp dla systemu linux. Jest ona dostępna z <https://github.com/Microsoft/ntttcp-for-linux>

Na maszyn wirtualnych systemu Linux (NADAWCĄ i odbiorcą) uruchom następujące polecenia, aby przygotować ntttcp dla linux na maszyny wirtualne:

CentOS - Install Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Install Git:
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

Jak pokazano w przykładzie Windows przyjęto założenie, że adres IP ODBIORNIKA Linux jest 10.0.0.4

Uruchom NTTTCP dla systemu Linux odbiornika:

``` bash
ntttcp -r -t 300
```

I na nadawcy, uruchom:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Podano długość domyślnie 60 sekund, jeśli nie parametru czasu testu

## <a name="testing-between-vms-running-windows-and-linux"></a>Testowanie między maszynami wirtualnymi z systemem Windows i LINUX:

Na tym scenariuszy możemy włączyć tryb synchronizacji nie, aby uruchomić test. Jest to zrobić za pomocą **flagi -N** dla systemu Linux i **flagi -ns** dla systemu Windows.

#### <a name="from-linux-to-windows"></a>Z systemu Linux w systemie Windows:

Odbiornik <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Nadawca <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Z systemu Windows do systemu Linux:

Odbiornik <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Nadawca <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testowanie wystąpienia usługi chmury:
Konieczne jest dodanie następujących sekcji w Twojej ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Kolejne kroki
* W zależności od wyników, może być miejsca, aby [zoptymalizować maszyny przepustowość sieci](virtual-network-optimize-network-bandwidth.md) dla danego scenariusza.
* Przeczytaj temat [przepustowości jest przydzielona do maszyny wirtualnej] (wirtualny machine sieci throughput.md)
* Dowiedz się więcej o [sieci wirtualnej platformy Azure — często zadawane pytania (FAQ)](virtual-networks-faq.md)
