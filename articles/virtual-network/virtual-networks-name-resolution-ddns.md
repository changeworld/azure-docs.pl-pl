---
title: Używanie dynamicznej usługi DNS do rejestrowania nazw hostów na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dynamicznej usługi DNS do rejestrowania nazw hostów na serwerach DNS.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640382"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Używanie dynamicznej usługi DNS do rejestrowania nazw hostów na serwerze DNS

[Platforma Azure udostępnia rozpoznawanie nazw](virtual-networks-name-resolution-for-vms-and-role-instances.md) dla maszyn wirtualnych (VM) i wystąpień roli. W przypadku usługi rozpoznawania nazw musi przekracza możliwości oferowane przez domyślne platformy Azure DNS, można zapewnić własnych serwerów DNS. Przy użyciu serwerów DNS daje możliwość dostosowywania rozwiązania DNS do własnych konkretnych potrzeb. Na przykład należy uzyskać dostęp do zasobów lokalnych za pośrednictwem kontrolera domeny usługi Active Directory.

Jeśli niestandardowe serwery DNS są hostowane jako maszyny wirtualne platformy Azure, możesz przekazywać zapytania nazwę hosta dla tej samej sieci wirtualnej Azure, aby rozpoznać nazwy hostów. Jeśli nie chcesz użyć tej opcji, możesz zarejestrować swojej nazwy hostów maszyn wirtualnych na serwerze DNS przy użyciu dynamicznych DNS (DDNS). Azure nie ma poświadczeń bezpośrednio utworzyć rekordy w serwerach DNS, więc alternatywne ustalenia często są wymagane. Wykonaj kilka typowych scenariuszy z alternatyw:

## <a name="windows-clients"></a>Klienci Windows
Klientów przyłączonych do domeny inne niż Windows próba niezabezpieczony DDNS aktualizacji po uruchomieniu lub zmianie ich adresów IP. Nazwa DNS jest nazwą hosta, a także sufiks podstawowej domeny DNS. Azure pozostawia pustą sufiks podstawowej domeny DNS, ale sufiks w maszynie Wirtualnej, można ustawić za pomocą [interfejsu użytkownika](https://technet.microsoft.com/library/cc794784.aspx) lub [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Klienci Windows przyłączone do domeny rejestrują swoje adresy IP z kontrolerem domeny przy użyciu bezpiecznego DDNS. Procesu przyłączania do domeny ustawia sufiks podstawowej domeny DNS na kliencie i tworzy i obsługuje relacji zaufania.

## <a name="linux-clients"></a>Klienci systemu Linux
Klienci systemu Linux ogólnie nie rejestrują się za pomocą serwera DNS podczas uruchamiania, ich przyjęto założenie, że serwer DHCP jest. Serwery DHCP platformy Azure nie mają poświadczenia do zarejestrowania rekordów na serwerze DNS. Można użyć z narzędzia o nazwie `nsupdate`, który znajduje się w pakiecie Bind, aby wysłać DDNS aktualizacji. Ponieważ protokół DDNS ustandaryzowany, możesz użyć `nsupdate` nawet jeśli nie używasz Bind na serwerze DNS.

Możesz użyć punkty zaczepienia, które są udostępniane przez klienta DHCP, aby tworzyć i obsługiwać wpis nazwy hosta na serwerze DNS. Podczas cyklu DHCP, klient wykonuje skrypty w */etc/dhcp/dhclient-exit-hooks.d/* . Punkty zaczepienia można użyć, aby zarejestrować nowy adres IP, korzystając `nsupdate`. Na przykład:

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

Można również użyć `nsupdate` polecenie, aby wykonać bezpiecznego DDNS aktualizacji. Na przykład podczas korzystania z serwera DNS Powiąż pary kluczy publiczny prywatny jest [generowane](http://linux.yyz.us/nsupdate/). Serwer DNS jest [skonfigurowane](http://linux.yyz.us/dns/ddns-server.html) z publiczną część klucza, tak że można sprawdzić podpis na żądanie. Aby zapewnić pary kluczy do `nsupdate`, użyj `-k` opcję dla DDNS aktualizowanie żądania były podpisane.

Podczas korzystania z serwera Windows DNS można korzystać z uwierzytelniania Kerberos z `-g` parametru w `nsupdate`, ale nie jest dostępna w wersji Windows `nsupdate`. Aby korzystać z protokołu Kerberos, należy użyć `kinit` można załadować poświadczeń. Adapterem, można załadować poświadczeń z [pliku keytab](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)), następnie `nsupdate -g` przejmuje poświadczenia, z pamięci podręcznej.

Jeśli to konieczne, można dodać sufiks wyszukiwania DNS do maszyn wirtualnych. Sufiks DNS jest określona w */etc/resolv.conf* pliku. Dystrybucjach systemu Linux automatycznie zarządzać zawartość tego pliku, więc zazwyczaj nie można edytować go. Jednak można zastąpić sufiks za pomocą klienta DHCP `supersede` polecenia. Aby zastąpić sufiks, Dodaj następujący wiersz do */etc/dhcp/dhclient.conf* pliku:

```
supersede domain-name <required-dns-suffix>;
```
