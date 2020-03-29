---
title: Testowanie przepływności sieci maszyn wirtualnych platformy Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60743085"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testowanie przepustowości/przepływności (NTTTCP)

Podczas testowania wydajności przepływności sieci na platformie Azure najlepiej jest użyć narzędzia, które jest przeznaczone dla sieci do testowania i minimalizuje użycie innych zasobów, które mogą mieć wpływ na wydajność. NTTTCP jest zalecane.

Skopiuj narzędzie do dwóch maszyn wirtualnych platformy Azure o tym samym rozmiarze. Jedna maszyna wirtualna działa jako nadawca, a druga jako ODBIORCA.

#### <a name="deploying-vms-for-testing"></a>Wdrażanie maszyn wirtualnych do testowania
Na potrzeby tego testu dwie maszyny wirtualne powinny znajdować się w tej samej usłudze w chmurze lub w tym samym zestawie dostępności, abyśmy mogli użyć ich wewnętrznych serwerów IP i wykluczyć moduły równoważenia obciążenia z testu. Jest możliwe, aby przetestować z VIP, ale tego rodzaju badania jest poza zakresem tego dokumentu.

Zanotuj adres IP odbiorcy. Nazwijmy to IP "a.b.c.r"

Zanotuj liczbę rdzeni na maszynie Wirtualnej. Nazwijmy to\#"rdzeniami num"\_

Uruchom test NTTTCP przez 300 sekund (lub 5 minut) na maszynie wirtualnej nadawcy i odbiornika maszyn wirtualnych.

Wskazówka: podczas konfigurowania tego testu po raz pierwszy możesz wypróbować krótszy okres testowy, aby szybciej uzyskać opinię. Gdy narzędzie działa zgodnie z oczekiwaniami, wydłuż okres testu do 300 sekund, aby uzyskać najdokładniejsze wyniki.

> [!NOTE]
> Nadawca **i** odbiorca muszą określić **ten sam** parametr czasu trwania testu (-t).

Aby przetestować pojedynczy strumień TCP przez 10 sekund:

Parametry odbiornika: ntttcp -r -t 10 -P 1

Parametry nadawcy: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> Poprzednia próbka powinna być używana tylko do potwierdzenia konfiguracji. Prawidłowe przykłady testowania są omówione w dalszej części tego dokumentu.

## <a name="testing-vms-running-windows"></a>Testowanie maszyn wirtualnych z systemem WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Pobierz NTTTCP na maszyny wirtualne.

Pobierz najnowszą wersję:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Lub poszukaj <https://www.bing.com/search?q=ntttcp+download> \< go, jeśli zostanie przeniesiony: -- powinien być pierwszy trafiony

Rozważ umieszczenie NTTTCP w osobnym\\folderze, takim jak narzędzia c:

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Zezwalaj ntttcp przez zaporę systemu Windows
W receiverie utwórz regułę Zezwalaj na Zaporę systemu Windows, aby umożliwić przychodzący ruch NTTTCP. Najłatwiej jest zezwolić całemu programowi NTTTCP według nazwy, a nie zezwolić na przychodzące określone porty TCP.

Zezwól ntttcp za pośrednictwem Zapory systemu Windows w ten sposób:

netsh advfirewall firewall add\<\>\\rule program= PATH ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Na przykład, jeśli plik ntttcp.exe został\\skopiowany do folderu "c:tools", będzie to polecenie: 

netsh advfirewall firewall add rule\\\\program=c: tools ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Uruchamianie testów NTTTCP

Uruchom ntttcp na odbiorniku **(uruchom z CMD**, a nie z programu PowerShell):

ntttcp -r –m\*\#[2 rdzenie num],\_\*a.b.c.r -t 300

Jeśli maszyna wirtualna ma cztery rdzenie i adres IP 10.0.0.4, będzie wyglądać następująco:

ntttcp -r –m\*8, ,10.0.0.4 -t 300


Uruchom ntttcp na nadawcy **(uruchom z CMD**, a nie z programu PowerShell):

ntttcp -s –m\*8, ,10.0.0.4 -t 300 

Poczekaj na wyniki.


## <a name="testing-vms-running-linux"></a>Testowanie maszyn wirtualnych z systemem LINUX:

Użyj nttcp-for-linux. Jest on dostępny od<https://github.com/Microsoft/ntttcp-for-linux>

Na maszynach wirtualnych z systemem Linux (zarówno nadawca, jak i odbiornik) uruchom następujące polecenia, aby przygotować ntttcp-for-linux na maszynach wirtualnych:

CentOS - Zainstaluj Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Zainstaluj Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Dokonaj i zainstaluj na obu:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Podobnie jak w przykładzie systemu Windows, zakładamy, że adres IP odbiornika Linuksa wynosi 10.0.0.4

Uruchom NTTTCP-for-Linux na odbiorniku:

``` bash
ntttcp -r -t 300
```

A na nadawcy uruchom:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Domyślna długość testu wynosi 60 sekund, jeśli nie podano parametru czasu

## <a name="testing-between-vms-running-windows-and-linux"></a>Testowanie między maszynami wirtualnymi z systemem Windows i LINUX:

W tym scenariuszu należy włączyć tryb bez synchronizacji, dzięki czemu można uruchomić test. Odbywa się to przy użyciu **flagi -N** dla linuksa i **flagi -ns** dla systemu Windows.

#### <a name="from-linux-to-windows"></a>Od Linuksa do Systemu Windows:

Odbiornik \<> systemu Windows:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Nadawca \<Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Z systemu Windows do Linuksa:

Odbiornik \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Nadawca \<windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testowanie wystąpień usługi w chmurze:
Do servicedefinition.csdef należy dodać następującą sekcję
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Następne kroki
* W zależności od wyników może być miejsce na [optymalizację maszyn przepływności sieci dla](virtual-network-optimize-network-bandwidth.md) twojego scenariusza.
* Przeczytaj o [przydzielaniu przepustowości do maszyn wirtualnych](virtual-machine-network-throughput.md)
* Dowiedz się więcej dzięki [często zadawanym pytaniom usługi Azure Virtual Network (CZĘSTO ZADAWANE PYTANIA)](virtual-networks-faq.md)
