---
title: Tworzenie niestandardowych rekordów DNS dla aplikacji sieci web | Dokumentacja firmy Microsoft
description: Jak utworzyć rekordy DNS dla aplikacji internetowej przy użyciu usługi Azure DNS domeny niestandardowej.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: victorh
ms.openlocfilehash: f24c301cea5ef91d101206e71b69b7ceb03b0282
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172453"
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Utwórz rekordy DNS dla aplikacji sieci web w domenie niestandardowej

Usługa Azure DNS umożliwia hostowanie domeny niestandardowej dla aplikacji sieci web. Na przykład tworzysz aplikację internetową platformy Azure i mają do niego dostęp przez użytkowników przy użyciu contoso.com lub www.contoso.com jako nazwy FQDN.

Aby to zrobić, należy utworzyć dwa rekordy:

* Rekordu głównego "A", wskazującego do domeny contoso.com
* Rekord "CNAME" dla nazwy www, wskazujący rekord A

Należy pamiętać, że jeśli tworzysz rekord A dla aplikacji sieci web na platformie Azure, rekord A musi być ręcznie aktualizowane, jeśli adres IP podstawowej aplikacji sieci web ulegnie zmianie.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem należy najpierw utworzyć strefę DNS w usłudze Azure DNS i delegowanie strefy w rejestratora do usługi Azure DNS.

1. Aby utworzyć strefę DNS, wykonaj kroki opisane w [utworzyć strefę DNS](dns-getstarted-create-dnszone.md).
2. Delegowania usługi DNS do usługi Azure DNS, należy wykonać czynności opisane w [Delegowanie domeny DNS](dns-domain-delegation.md).

Po utworzeniu strefy i delegowanie go do usługi Azure DNS, następnie można utworzyć rekordy dla domeny niestandardowej.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Utwórz rekord A dla domeny niestandardowej

Rekord jest używany do mapowania nazwy na jej adres IP. W poniższym przykładzie przypiszemy \@ jako rekord A adres IPv4:

### <a name="step-1"></a>Krok 1

Należy utworzyć rekord A i przypisać do zmiennej $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Krok 2

Dodaj wartość IPv4 do wcześniej utworzonego zestawu rekordów "\@" przy użyciu zmiennej $rs przypisane. Wartość IPv4 przypisany będzie adres IP dla aplikacji sieci web.

Aby znaleźć adres IP dla aplikacji sieci web, wykonaj kroki opisane w [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Krok 3

Zatwierdź zmiany do zestawu rekordów. Użyj `Set-AzureRMDnsRecordSet` Aby przekazać zmiany do zestawu do usługi Azure DNS rekordów:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Utwórz rekord CNAME dla domeny niestandardowej

Jeśli domena jest już zarządzane przez usługę Azure DNS (zobacz [Delegowanie domeny DNS](dns-domain-delegation.md), można użyć następujących przykładu tak, aby utworzyć rekord CNAME dla contoso.azurewebsites.net.

### <a name="step-1"></a>Krok 1

Otwórz program PowerShell i utworzyć nowy zestaw rekordów CNAME i przypisz ją do zmiennej $rs. W tym przykładzie spowoduje utworzenie typu zestawu rekordów CNAME z "czas wygaśnięcia" 600 sekund w strefie DNS o nazwie "contoso.com".

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

Odpowiedzią jest poniższy przykład.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Krok 2

Po utworzeniu zestawu rekordów CNAME, musisz utworzyć wartość aliasu, który będzie wskazywać aplikacji sieci web.

Przy użyciu uprzednio przypisanych zmiennej "$rs" można użyć poniższego polecenia programu PowerShell do utworzenia aliasu dla contoso.azurewebsites.net aplikacji sieci web.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

Odpowiedzią jest poniższy przykład.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Krok 3

Zatwierdź zmiany przy użyciu `Set-AzureRMDnsRecordSet` polecenia cmdlet:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Możesz zweryfikować rekord został utworzony prawidłowo, badając "www.contoso.com" za pomocą polecenia nslookup, jak pokazano poniżej:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Tworzenie rekordu "awverify" dla aplikacji sieci web

Jeśli zdecydujesz się użyć rekordu A dla aplikacji sieci web, należy przejść przez proces weryfikacji, aby upewnić się, że jesteś właścicielem domeny niestandardowej. Ten krok weryfikacji odbywa się przez utworzenie rekordu CNAME specjalne o nazwie "awverify". Ta sekcja dotyczy tylko rekordy.

### <a name="step-1"></a>Krok 1

Utwórz rekord "awverify". W poniższym przykładzie zostanie utworzony rekord "aweverify" dla domeny contoso.com zweryfikować prawa własności do domeny niestandardowej.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

Odpowiedzią jest poniższy przykład.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Krok 2

Po utworzeniu zestawu rekordów "awverify" Przypisz alias zestawu rekordów CNAME. W poniższym przykładzie przypiszemy rekord CNAMe, alias równa awverify.contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

Odpowiedzią jest poniższy przykład.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Krok 3

Zatwierdź zmiany przy użyciu `Set-AzureRMDnsRecordSet cmdlet`, jak pokazano w poniższym poleceniu.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z instrukcjami w [Konfigurowanie niestandardowej nazwy domeny dla usługi App Service](../app-service/app-service-web-tutorial-custom-domain.md) skonfigurować aplikację sieci web do używania własnej domeny.
