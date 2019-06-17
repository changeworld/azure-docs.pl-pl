---
title: Delegowanie poddomeny usługi Azure DNS przy użyciu programu Azure PowerShell
description: Dowiedz się, jak delegować domeny podrzędnej z usługi Azure DNS przy użyciu programu Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 4ee4d9e6390c9a091096bb7c06160b76fd8af90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730286"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegowanie poddomeny usługi Azure DNS przy użyciu programu Azure PowerShell

Aby delegować domenę podrzędną DNS, można użyć programu Azure PowerShell. Na przykład, jeśli jesteś właścicielem domeny contoso.com, należy delegować domenę podrzędną o nazwie *inżynierów* inną, oddzielną strefę, które mogą administrować niezależnie od strefy contoso.com.

Jeśli wolisz, możesz delegować domeny podrzędnej przy użyciu [witryny Azure Portal](delegate-subdomain.md).

> [!NOTE]
> Contoso.com jest używany jako przykład w tym artykule. Zastąp contoso.com swoją nazwą domeny.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby delegować przywoływana jest poddomena DNS platformy Azure, musisz delegować domenę publiczną do usługi Azure DNS. Zobacz [delegować domenę do usługi Azure DNS](./dns-delegate-domain-azure-dns.md) instrukcje dotyczące sposobu konfigurowania serwerów nazw dla celów delegacji. Gdy domeny jest delegowane do strefy DNS platformy Azure, możesz delegować Twojej domeny podrzędnej.

## <a name="create-a-zone-for-your-subdomain"></a>Utwórz strefę w Twojej domenie podrzędnej

Najpierw należy utworzyć strefę dla **inżynierów** poddomeny.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Należy pamiętać, serwery nazw

Następnie należy zwrócić uwagę cztery serwery nazw dla inżynierów poddomeny.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Utwórz rekord testu

Tworzenie **A** rekordów w strefie inżynierów na potrzeby testowania.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Tworzenie rekordów NS

Następnie utwórz rekord serwera (nazw NS) nazwa **inżynierów** strefy w strefie contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Test delegowania

Nslookup umożliwia testowanie delegowanie.

1. Otwórz okno programu PowerShell.
2. W wierszu polecenia wpisz polecenie `nslookup www.engineering.contoso.com.`
3. Powinna pojawić się jako nieautorytatywny odpowiedzi, wyświetlanie adresu **10.10.10.10**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).