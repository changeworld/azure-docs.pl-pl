---
title: Rejestrowanie nazwy hostów na platformie Azure przy użyciu dynamicznych DNS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dynamicznych DNS do rejestrowania nazwy hostów w serwerach DNS.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: bbbce45b7c321fd4934374c76f2a4421b125d46f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Użyj dynamicznych DNS, aby zarejestrować nazwy hostów na serwerze DNS

[Platforma Azure udostępnia rozpoznawanie nazw](virtual-networks-name-resolution-for-vms-and-role-instances.md) dla maszyn wirtualnych (VM) i wystąpień ról. Gdy programu rozpoznawania nazw musi przekracza możliwości oferowane przez domyślny Azure DNS, musisz podać własne serwery DNS. Przy użyciu serwerów DNS umożliwia można dostosować do własnych potrzeb określonego rozwiązania DNS. Na przykład konieczne może uzyskiwać dostęp do zasobów lokalnych za pomocą kontrolera domeny usługi Active Directory.

Gdy niestandardowe serwery DNS są przechowywane jako maszynach wirtualnych platformy Azure, może przekazywać kwerendy nazwy hosta dla tej samej sieci wirtualnej Azure do rozpoznania nazwy hostów. Jeśli nie chcesz użyć tej opcji, możesz zarejestrować Twojej nazwy hostów maszyny Wirtualnej na serwerze DNS przy użyciu dynamicznych DNS (DDNS). Azure nie ma poświadczenia, aby bezpośrednio utworzyć rekordy w serwerach DNS, potrzebne są często alternatywne uzgodnienia. Należy wykonać kilka typowych scenariuszy, alternatyw:

## <a name="windows-clients"></a>Klienci systemu Windows
Klienci z systemem Windows przyłączonych do domeny inne niż podejmować niezabezpieczona DDNS aktualizacji po uruchomieniu lub zmianie ich adresów IP. Nazwa DNS jest nazwą hosta i sufiks podstawowej domeny DNS. Azure pozostawia puste sufiks podstawowej domeny DNS, ale sufiks w Maszynie wirtualnej, można ustawić za pomocą [interfejsu użytkownika](https://technet.microsoft.com/library/cc794784.aspx) lub [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Klienci z systemem Windows przyłączonych do domeny rejestrują swoje adresy IP z kontrolerem domeny przy użyciu bezpiecznego DDNS. Proces przyłączania do domeny ustawia sufiks podstawowej domeny DNS na kliencie i tworzy i obsługuje relacji zaufania.

## <a name="linux-clients"></a>Klienci systemu Linux
Klienci systemu Linux zazwyczaj nie rejestrują się z serwera DNS podczas uruchamiania, ich przyjęto założenie, że jest serwer DHCP. Serwery DHCP platformy Azure nie mają poświadczenia do zarejestrowania rekordów znajdujących się na serwerze DNS. Można użyć narzędzia o nazwie `nsupdate`, który jest uwzględniony w pakiecie Bind, aby wysłać DDNS aktualizacji. Ponieważ jest standardowym protokołem DDNS, można użyć `nsupdate` nawet jeśli nie używasz Bind na serwerze DNS.

Możesz użyć punkty zaczepienia, które są udostępniane przez klienta DHCP można tworzyć i obsługiwać wpis nazwy hosta na serwerze DNS. Podczas cyklu DHCP, klient wykonuje skryptów w */etc/dhcp/dhclient-exit-hooks.d/*. Punkty zaczepienia można użyć, aby zarejestrować nowy adres IP przy użyciu `nsupdate`. Na przykład:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

Można również użyć `nsupdate` polecenie do wykonania bezpiecznego DDNS aktualizacji. Na przykład podczas korzystania z serwera Bind DNS, to pary kluczy publiczno prywatnych [wygenerowany](http://linux.yyz.us/nsupdate/). Serwer DNS jest [skonfigurowane](http://linux.yyz.us/dns/ddns-server.html) z publicznej części klucza, tak że można zweryfikować podpisu na żądanie. Zapewnienie para klucz do `nsupdate`, użyj `-k` opcję DDNS aktualizacji żądania do podpisania.

Podczas korzystania z serwera DNS systemu Windows, można użyć uwierzytelniania Kerberos z `-g` parametru w `nsupdate`, ale nie jest dostępna w wersji Windows `nsupdate`. Aby użyć protokołu Kerberos, użyj `kinit` można załadować poświadczenia. For example, można załadować poświadczeń z [pliku keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), następnie `nsupdate -g` przejmuje poświadczenia z pamięci podręcznej.

W razie potrzeby można dodać sufiks wyszukiwania DNS do maszyn wirtualnych. Sufiks DNS jest określona w */etc/resolv.conf* pliku. Większość dystrybucjach systemu Linux automatycznie zarządzać zawartość tego pliku, dlatego zazwyczaj nie można edytować go. Jednak można zastąpić sufiks przy użyciu klienta DHCP `supersede` polecenia. Aby zastąpić sufiks, Dodaj następujący wiersz do */etc/dhcp/dhclient.conf* pliku:

```
supersede domain-name <required-dns-suffix>;
```
