---
title: Używanie dynamicznego systemu DNS do rejestrowania nazw hostów na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować dynamiczny system DNS w celu rejestrowania nazw hostów na własnych serwerach DNS.
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
ms.openlocfilehash: c2ef842fd62ef060f06536d66387c3facd0627b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60640382"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Używanie dynamicznej usługi DNS do rejestrowania nazw hostów na własnym serwerze DNS

[Platforma Azure zapewnia rozpoznawanie nazw](virtual-networks-name-resolution-for-vms-and-role-instances.md) dla maszyn wirtualnych (VM) i wystąpień ról. Gdy twoje potrzeby rozpoznawania nazw przekraczają możliwości zapewniane przez domyślny system DNS platformy Azure, możesz udostępnić własne serwery DNS. Korzystanie z własnych serwerów DNS daje możliwość dostosowania rozwiązania DNS do własnych potrzeb. Na przykład może być konieczne uzyskianie dostępu do zasobów lokalnych za pośrednictwem kontrolera domeny usługi Active Directory.

Gdy niestandardowe serwery DNS są hostowane jako maszyny wirtualne platformy Azure, można przesłać dalej zapytania nazwy hosta dla tej samej sieci wirtualnej na platformę Azure, aby rozpoznać nazwy hostów. Jeśli nie chcesz używać tej opcji, możesz zarejestrować nazwy hostów maszyn wirtualnych na serwerze DNS przy użyciu dynamicznego systemu DNS (DDNS). Platforma Azure nie ma poświadczeń do bezpośredniego tworzenia rekordów na serwerach DNS, więc często potrzebne są alternatywne rozwiązania. Niektóre typowe scenariusze, z alternatywami:

## <a name="windows-clients"></a>Klienci systemu Windows
Klienci systemu Windows nieprzyłączeni do domeny próbują niezabezpieczonych aktualizacji DDNS podczas uruchamiania lub zmiany adresu IP. Nazwa DNS jest nazwą hosta oraz podstawowym sufiksem DNS. Platforma Azure pozostawia podstawowy sufiks DNS pusty, ale sufiks można ustawić na maszynie wirtualnej za pośrednictwem [interfejsu użytkownika](https://technet.microsoft.com/library/cc794784.aspx) lub [programu PowerShell](/powershell/module/dnsclient/set-dnsclient).

Klienci systemu Windows przyłączeni do domeny rejestrują swoje adresy IP na kontrolerze domeny przy użyciu bezpiecznej usługi DDNS. Proces sprzężenia domeny ustawia podstawowy sufiks DNS na kliencie i tworzy i utrzymuje relację zaufania.

## <a name="linux-clients"></a>Klienci systemu Linux
Klienci systemu Linux zazwyczaj nie rejestrują się na serwerze DNS podczas uruchamiania, zakładają, że serwer DHCP to robi. Serwery DHCP platformy Azure nie mają poświadczeń do rejestrowania rekordów na serwerze DNS. Można użyć narzędzia `nsupdate`o nazwie , który jest zawarty w pakiecie Bind, do wysyłania aktualizacji DDNS. Ponieważ protokół DDNS jest standaryzowany, można używać `nsupdate` nawet wtedy, gdy nie używasz powiązania na serwerze DNS.

Za pomocą haków dostarczanych przez klienta DHCP można utworzyć i zachować wpis nazwy hosta na serwerze DNS. Podczas cyklu DHCP klient wykonuje skrypty w */etc/dhcp/dhclient-exit-hooks.d/*. Za pomocą haków można zarejestrować nowy `nsupdate`adres IP za pomocą programu . Przykład:

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

Można również użyć `nsupdate` tego polecenia do wykonywania bezpiecznych aktualizacji DDNS. Na przykład podczas korzystania z serwera BIND DNS [jest generowana](http://linux.yyz.us/nsupdate/)para kluczy publiczno-prywatnych . Serwer DNS jest [skonfigurowany](http://linux.yyz.us/dns/ddns-server.html) z publiczną częścią klucza, dzięki czemu może zweryfikować podpis w żądaniu. Aby udostępnić parę `nsupdate`kluczy `-k` do , użyj opcji, aby żądanie aktualizacji DDNS było podpisane.

Podczas korzystania z serwera DNS systemu Windows można użyć `-g` uwierzytelniania `nsupdate`Kerberos z parametrem w programie `nsupdate`, ale nie jest on dostępny w wersji systemu Windows . Aby użyć protokołu `kinit` Kerberos, należy załadować poświadczenia. Na przykład można załadować poświadczenia z pliku karty `nsupdate -g` [klucza](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), a następnie pobiera poświadczenia z pamięci podręcznej.

W razie potrzeby można dodać sufiks wyszukiwania DNS do maszyn wirtualnych. Sufiks DNS jest określony w pliku */etc/resolv.conf.* Większość dystrybucji Linuksa automatycznie zarządza zawartością tego pliku, więc zwykle nie można go edytować. Sufiks można jednak zastąpić za pomocą `supersede` polecenia klienta DHCP. Aby zastąpić sufiks, dodaj następujący wiersz do pliku */etc/dhcp/dhclient.conf:*

```
supersede domain-name <required-dns-suffix>;
```
