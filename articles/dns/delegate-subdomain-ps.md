---
title: Delegowanie poddomeny — usługa Azure PowerShell — Azure DNS
description: Dzięki tej ścieżce szkoleniowej rozpocznij delegowanie poddomeny usługi Azure DNS przy użyciu programu Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: 7e019afaae98422b8d5a3c8fa7a5f79e26c6a149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937708"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegowanie poddomeny usługi Azure DNS przy użyciu programu Azure PowerShell

Za pomocą programu Azure PowerShell można delegować poddomenę DNS. Jeśli na przykład jesteś właścicielem domeny contoso.com, możesz delegować poddomenę o nazwie *engineering* do innej oddzielnej strefy, którą można administrować oddzielnie od strefy contoso.com.

Jeśli wolisz, możesz delegować poddomenę za pomocą [usługi Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com jest używany jako przykład w tym artykule. Zastąp contoso.com swoją nazwą domeny.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby delegować poddomenę usługi Azure DNS, należy najpierw delegować domenę publiczną do usługi Azure DNS. Zobacz [delegowanie domeny do usługi Azure DNS, aby](./dns-delegate-domain-azure-dns.md) uzyskać instrukcje dotyczące konfigurowania serwerów nazw do delegowania. Po przekazaniu domeny do strefy DNS platformy Azure można delegować poddomenę.

## <a name="create-a-zone-for-your-subdomain"></a>Tworzenie strefy dla poddomeny

Najpierw utwórz strefę dla poddomeny **inżynierskiej.**

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Zanotuj serwery nazw

Następnie zanotuj cztery serwery nazw poddomeny inżynierii.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Tworzenie rekordu testu

Utwórz rekord **A** w strefie inżyniera, który będzie używany do testowania.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Tworzenie rekordu NS

Następnie utwórz rekord serwera nazw (NS) dla strefy **inżynierskiej** w strefie contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testowanie delegacji

Użyj nslookup, aby przetestować delegowanie.

1. Otwórz okno programu PowerShell.
2. W wierszu polecenia wpisz`nslookup www.engineering.contoso.com.`
3. Należy otrzymać nieuzyskajną odpowiedź pod adresem **10.10.10.10**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [skonfigurować odwrotny system DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).