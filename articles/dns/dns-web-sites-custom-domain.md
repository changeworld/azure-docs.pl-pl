---
title: Tworzenie niestandardowych rekordów DNS dla aplikacji sieci web | Dokumentacja firmy Microsoft
description: Jak utworzyć rekordy DNS dla aplikacji sieci web przy użyciu usługi Azure DNS domeny niestandardowej.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: kumud
ms.openlocfilehash: 00a56a2683e95e70bb13acd6b936e766f044e1cd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Utwórz rekordy DNS dla aplikacji sieci web w domenę niestandardową

Usługi Azure DNS służy do hosta domeny niestandardowej aplikacji sieci web. Na przykład tworzysz aplikację sieci web platformy Azure i ma do niego dostęp przez użytkowników przy użyciu contoso.com lub www.contoso.com jako nazwy FQDN.

Aby to zrobić, należy utworzyć dwa rekordy:

* Rekordu głównego "A" wskazuje do domeny contoso.com
* Rekord "CNAME" dla nazwy www wskazujący rekord A

Należy pamiętać, że w przypadku utworzenia rekordu A dla aplikacji sieci web na platformie Azure, A musi być ręcznie zaktualizowany, jeśli adres IP źródłowego zmian aplikacji sieci web.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem, najpierw musisz utworzyć strefę DNS w usłudze Azure DNS i delegowanie strefy w rejestratora do usługi Azure DNS.

1. Aby utworzyć strefę DNS, postępuj zgodnie z instrukcjami [utworzyć strefę DNS](dns-getstarted-create-dnszone.md).
2. Aby delegować serwery DNS do usługi Azure DNS, postępuj zgodnie z instrukcjami [Delegowanie domeny DNS](dns-domain-delegation.md).

Po utworzeniu strefy i delegowanie go do usługi Azure DNS, następnie możesz utworzyć rekordów dla domeny niestandardowej.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Tworzenie rekordu A dla domeny niestandardowej

Rekord jest używany do mapowania nazwy na adres IP. W poniższym przykładzie zostanie możemy Przypisz jako rekord A adresu IPv4:

### <a name="step-1"></a>Krok 1

Utwórz rekord a. i przypisać do zmiennej $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Krok 2

Dodaj wartość IPv4 do wcześniej utworzonego zestawu rekordów "@" przy użyciu zmiennej $rs przypisana. Wartość IPv4 przypisana będzie adres IP dla aplikacji sieci web.

Aby znaleźć adres IP dla aplikacji sieci web, postępuj zgodnie z instrukcjami [Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Krok 3

Zatwierdź zmiany w zestawie rekordów. Użyj `Set-AzureRMDnsRecordSet` można przekazać zmian do zestawu do usługi Azure DNS rekordów:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Utwórz rekord CNAME dla domeny niestandardowej

Jeśli domena jest już zarządzany przez usługę Azure DNS (zobacz [Delegowanie domeny DNS](dns-domain-delegation.md), można użyć następujących przykład, aby utworzyć rekord CNAME dla contoso.azurewebsites.net.

### <a name="step-1"></a>Krok 1

Otwórz program PowerShell i Utwórz nowy zestaw rekordów CNAME i przypisać do zmiennej $rs. W tym przykładzie zostanie utworzenie typu zestawu rekordów CNAME z "czas wygaśnięcia" 600 sekund w strefie DNS o nazwie "contoso.com".

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

Po utworzeniu zestawu rekordów CNAME, należy utworzyć alias wartość, która będzie wskazywać aplikacji sieci web.

Za pomocą przypisanego wcześniej zmiennej "$rs" można użyć poniższego polecenia programu PowerShell można utworzyć aliasu dla contoso.azurewebsites.net aplikacji sieci web.

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

Można zweryfikować rekord został utworzony prawidłowo badając "www.contoso.com" za pomocą polecenia nslookup, jak pokazano poniżej:

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

## <a name="create-an-awverify-record-for-web-apps"></a>Utwórz rekord "awverify" dla aplikacji sieci web

Jeśli zdecydujesz się używać rekordu A dla aplikacji sieci web, należy przeprowadzić proces weryfikacji, aby upewnić się, że jesteś właścicielem domeny niestandardowej. Ten krok weryfikacji jest realizowane przez utworzenie rekordu CNAME specjalne o nazwie "awverify". Ta sekcja dotyczy tylko rekordy.

### <a name="step-1"></a>Krok 1

Utwórz rekord "awverify". W poniższym przykładzie zostanie utworzony rekord "aweverify" dla domeny contoso.com zweryfikować prawo własności dla domeny niestandardowej.

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

Po utworzeniu zestawu rekordów "awverify" przypisać aliasu zestawu rekordów CNAME. W poniższym przykładzie mamy przypisze alias ustawioną awverify.contoso.azurewebsites.net rekord CNAMe.

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

Postępuj zgodnie z instrukcjami [Konfigurowanie niestandardowej nazwy domeny dla usługi App Service](../app-service/app-service-web-tutorial-custom-domain.md) do skonfigurowania aplikacji sieci web do korzystania z niestandardowej domeny.
