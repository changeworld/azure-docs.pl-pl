---
title: Aby zarejestrować nazwy hostów przy użyciu dynamicznych DNS
description: Ta strona przedstawia szczegóły na temat konfigurowania dynamicznych DNS w celu zarejestrowania nazwy hostów w serwerach DNS.
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 0539736f4b7294a613ffd42c28ff6bb29cf9e2bf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Aby zarejestrować nazwy hostów w serwerze DNS przy użyciu dynamicznych DNS
[Platforma Azure udostępnia rozpoznawanie nazw](virtual-networks-name-resolution-for-vms-and-role-instances.md) na maszynach wirtualnych (VM) i wystąpień ról. Jednak gdy programu rozpoznawania nazw musi wykracza poza udostępnianych przez platformę Azure, musisz podać serwery DNS. Zapewnia możliwości dostosować do własnych potrzeb określonego rozwiązania DNS. Na przykład konieczne może uzyskiwać dostęp do zasobów lokalnych za pomocą kontrolera domeny usługi Active Directory.

Jeśli niestandardowe serwery DNS są hostowane jako maszynach wirtualnych platformy Azure można przekazywać kwerendy nazwy hosta dla tej samej sieci wirtualnej Azure do rozpoznania nazwy hostów. Jeśli nie chcesz używać tej trasy, możesz zarejestrować Twojej nazwy hostów maszyny Wirtualnej na serwerze DNS przy użyciu dynamicznych DNS.  Azure nie ma możliwości (np. poświadczenia) do tworzenia rekordów bezpośrednio w serwerach DNS, potrzebne są często alternatywne uzgodnienia. Poniżej przedstawiono kilka typowych scenariuszy z alternatyw.

## <a name="windows-clients"></a>Klienci systemu Windows
Klienci z systemem Windows przyłączonych do domeny inne niż podejmować niezabezpieczona aktualizacji dynamicznych DNS (DDNS), po uruchomieniu lub zmianie ich adresów IP. Nazwa DNS jest nazwą hosta i sufiks podstawowej domeny DNS. Azure pozostawia puste sufiks podstawowej domeny DNS, ale możesz ustawić na maszynie wirtualnej za pomocą [interfejsu użytkownika](https://technet.microsoft.com/library/cc794784.aspx) lub [PowerShell](/powershell/module/dnsclient/set-dnsclient).

Klienci z systemem Windows przyłączonych do domeny rejestrują swoje adresy IP z kontrolerem domeny przy użyciu bezpiecznych dynamicznych DNS. Proces przyłączania do domeny ustawia sufiks podstawowej domeny DNS na kliencie i tworzy i obsługuje relacji zaufania.

## <a name="linux-clients"></a>Klienci systemu Linux
Klienci systemu Linux zazwyczaj nie rejestrują się z serwera DNS podczas uruchamiania, ich przyjęto założenie, że jest serwer DHCP. Serwery DHCP platformy Azure nie mają możliwości lub poświadczenia pod kątem rejestrowania rekordów znajdujących się na serwerze DNS.  Można użyć narzędzia o nazwie *nsupdate*, który jest uwzględniony w pakiecie powiązania, do wysyłania DNS dynamiczne aktualizacje. Ponieważ protokół dynamicznej DNS jest standardowym, można użyć *nsupdate* nawet jeśli nie używasz Bind na serwerze DNS.

Możesz użyć punkty zaczepienia, które są udostępniane przez klienta DHCP można tworzyć i obsługiwać wpis nazwy hosta na serwerze DNS. Podczas cyklu DHCP, klient wykonuje skryptów w */etc/dhcp/dhclient-exit-hooks.d/*. To może służyć do rejestracji przy użyciu nowego adresu IP *nsupdate*. Na przykład:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
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

        
        

Można również użyć *nsupdate* polecenie do wykonania zabezpieczone aktualizacje dynamiczne DNS. Na przykład podczas korzystania z serwera Bind DNS, to pary kluczy publiczno prywatnych [wygenerowany](http://linux.yyz.us/nsupdate/).  Serwer DNS jest [skonfigurowane](http://linux.yyz.us/dns/ddns-server.html) z publicznej części klucza, którego nie można zweryfikować podpisu na żądanie. Należy użyć *-k* opcję, aby zapewnić para klucz do *nsupdate* w kolejności dynamicznych DNS żądanie podpisania aktualizacji.

Podczas korzystania z serwera DNS systemu Windows, można użyć uwierzytelniania Kerberos z *-g* parametru w *nsupdate* (nie jest dostępna w wersji Windows *nsupdate*). Aby to zrobić, użyj *kinit* można załadować poświadczenia (np. z [pliku keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Następnie *nsupdate -g* przejmą poświadczenia z pamięci podręcznej.

W razie potrzeby można dodać sufiks wyszukiwania DNS do maszyn wirtualnych. Sufiks DNS jest określona w */etc/resolv.conf* pliku. Większość dystrybucjach systemu Linux automatycznie zarządzać zawartość tego pliku, dlatego zazwyczaj nie można edytować go. Jednak można zastąpić sufiks przy użyciu klienta DHCP *zastępują* polecenia. Aby to zrobić, w */etc/dhcp/dhclient.conf*, dodać:

        supersede domain-name <required-dns-suffix>;

