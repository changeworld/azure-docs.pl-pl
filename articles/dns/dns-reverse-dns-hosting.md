---
title: Hostowanie stref wyszukiwania wstecznego wyszukiwania DNS w usłudze Azure DNS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DNS do obsługi odwrotne strefy wyszukiwania DNS dla zakresów IP
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: cb2f04c692d4b5f385a89ba6a3071c20ef1bdf21
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143656"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostowanie odwrotnego wyszukiwania stref DNS w usłudze Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak hostować odwrotne strefy wyszukiwania DNS dla przypisanych zakresów adresów IP w taki sposób, w usłudze Azure DNS. Zakresy adresów IP, reprezentowane przez strefy wyszukiwania wstecznego musi zostać przypisany do organizacji, zwykle przez usługodawcę internetowego.

Aby skonfigurować odwrotnym systemem DNS dla adresu IP należącą do Azure, która jest przypisana do usługi platformy Azure, zobacz [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).

Przed przeczytaniem tego artykułu, należy się zapoznać z [omówienie odwrotnego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W tym artykule przedstawiono kroki umożliwiające utworzenie po raz pierwszy wyszukiwania wstecznego DNS strefy i rekordu przy użyciu witryny Azure portal, programu Azure PowerShell, klasycznego wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Tworzenie strefy wyszukiwania wstecznego DNS

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na **Centrum** menu, wybierz opcję **New** > **sieć**, a następnie wybierz pozycję **strefy DNS**.

   ![Wybór "Strefy DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. W **tworzenie strefy DNS** okienko, nazwę strefy DNS. Nazwa strefy jest specjalnie inaczej w przypadku prefiksów protokołów IPv4 i IPv6. Postępuj zgodnie z instrukcjami dla [IPv4](#ipv4) lub [IPv6](#ipv6) nazwę strefy. Gdy skończysz, wybierz pozycję **Utwórz** do utworzenia strefy.

### <a name="ipv4"></a>IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 opiera się na zakres adresów IP, który reprezentuje. Powinna być w następującym formacie: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Aby uzyskać przykłady, zobacz [omówienie odwrotnego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Podczas tworzenia classless strefy wyszukiwania wstecznego DNS w usłudze Azure DNS, należy użyć łącznika (`-`) zamiast ukośnika (`/`) w nazwie strefy.
>
> Na przykład dla 192.0.2.128/26 zakres adresów IP, należy użyć `128-26.2.0.192.in-addr.arpa` jako nazwę strefy, zamiast `128/26.2.0.192.in-addr.arpa`.
>
> Mimo że standardy systemu DNS obsługuje obie metody, system DNS Azure nie obsługuje nazw strefy DNS, które zawierają dla ukośnika (`/`) znaków.

Poniższy przykład pokazuje, jak utworzyć klasy C odwrotnej strefę DNS o nazwie `2.0.192.in-addr.arpa` w usłudze Azure DNS przy użyciu witryny Azure portal:

 ![Okienko "Tworzenie strefy DNS", z polami wypełnione](./media/dns-reverse-dns-hosting/figure2.png)

**Lokalizacja grupy zasobów** Określa lokalizację dla grupy zasobów. Go nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

Następujące przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Nazwa strefy wyszukiwania wstecznego IPv6 powinien znajdować się w następującej postaci: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Aby uzyskać przykłady, zobacz [omówienie odwrotnego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv6).


Poniższy przykład pokazuje, jak utworzyć IPv6 strefy wyszukiwania wstecznego DNS wyszukiwania o nazwie `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` w usłudze Azure DNS przy użyciu witryny Azure portal:

 ![Okienko "Tworzenie strefy DNS", z polami wypełnione](./media/dns-reverse-dns-hosting/figure3.png)

**Lokalizacja grupy zasobów** Określa lokalizację dla grupy zasobów. Go nie ma wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

Następujące przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegowanie strefy wyszukiwania wstecznego DNS

Teraz, po utworzeniu Twojej strefy wyszukiwania wstecznego wyszukiwania DNS, należy się upewnić, że strefa jest delegowane ze strefy nadrzędnej. Delegowanie DNS umożliwia proces rozpoznawania nazw DNS znaleźć serwery nazw hostujące usługi strefy wyszukiwania wstecznego wyszukiwania DNS. Te serwery nazw może następnie odpowiedzieć zapytań wstecznych DNS dla adresów IP z zakresu adresów.

Dla stref wyszukiwania do przodu opisano proces delegowanie strefy DNS w [delegować domenę do usługi Azure DNS](dns-delegate-domain-azure-dns.md). Delegowanie dla strefy wyszukiwania wstecznego działa tak samo. Jedyna różnica polega na tym, należy skonfigurować serwery nazw z poziomu usługodawcy internetowego podanym zakresowi adresów IP, a nie rejestratora nazw domen.

## <a name="create-a-dns-ptr-record"></a>Tworzenie rekordu PTR systemu DNS

### <a name="ipv4"></a>IPv4

Poniższy przykład przeprowadzi Cię przez proces tworzenia rekordu PTR w DNS strefy wyszukiwania wstecznego w usłudze Azure DNS. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. W górnej części **strefy DNS** okienku wybierz **+ zestaw rekordów** otworzyć **Dodawanie zestawu rekordów** okienka.

   ![Przycisk tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure4.png)

1. Nazwa zestawu dla rekordu PTR rekordów należy pozostałą część adresu IPv4 w odwrotnej kolejności. 

   W tym przykładzie pierwsze trzy oktety są już wypełnione jako część nazwy strefy (.2.0.192). W związku z tym, tylko ostatni oktet zostanie podana w **nazwa** pole. Na przykład można nazwać zestaw rekordów **15** dla zasobu, którego adres IP jest 192.0.2.15.  
1. Aby uzyskać **typu**, wybierz opcję **PTR**.  
1. Aby uzyskać **nazwy domeny**, wprowadź w pełni kwalifikowana nazwa domeny (FQDN) z zasobem, który używa adres IP.
1. Wybierz **OK** w dolnej części okienka, aby utworzyć DNS rejestrowania.

   ![Okienko "Dodawanie zestawu rekordów", z polami wypełnione](./media/dns-reverse-dns-hosting/figure5.png)

Następujące przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu PowerShell lub wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Poniższy przykład przeprowadzi Cię przez proces tworzenia nowego rekordu PTR. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. W górnej części **strefy DNS** okienku wybierz **+ zestaw rekordów** otworzyć **Dodawanie zestawu rekordów** okienka.

   ![Przycisk tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure6.png)

2. Nazwa zestawu dla rekordu PTR rekordów należy pozostałą część adresu IPv6 w odwrotnej kolejności. Nie może zawierać zero kompresji. 

   W tym przykładzie pierwsze 64 bity IPv6 są już wypełnione jako część nazwy strefy (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). W związku z tym, tylko ostatnie 64 bity są dostarczane w **nazwa** pole. Ostatnie 64-bitowy adres IP są wprowadzane w odwrotnej kolejności z okresem jako separator każdej liczby szesnastkowej. Na przykład można nazwać zestaw rekordów **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** dla zasobu, którego adres IP jest 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Aby uzyskać **typu**, wybierz opcję **PTR**.  
4. Aby uzyskać **nazwy domeny**, wprowadź nazwę FQDN zasobem, który używa adres IP.
5. Wybierz **OK** w dolnej części okienka, aby utworzyć DNS rejestrowania.

![Okienko "Dodawanie zestawu rekordów", z polami wypełnione](./media/dns-reverse-dns-hosting/figure7.png)

Następujące przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu PowerShell lub wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić rekordy, które zostały utworzone, przejdź do strefy DNS w witrynie Azure portal. W dolnej części **strefy DNS** okienku widoczne są rekordy dla strefy DNS. Powinien zostać wyświetlony domyślne NS i SOA rekordy oraz nowe rekordy, które zostały utworzone. Rekordy NS i SOA są tworzone w każdej strefie. 

### <a name="ipv4"></a>IPv4

**Strefy DNS** okienko zawiera rekordów IPv4 PTR:

![Okienko "Strefy DNS" z rekordami IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Następujące przykłady przedstawiają sposób wyświetlania rekordów PTR przy użyciu programu PowerShell lub wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

**Strefy DNS** okienko zawiera rekordów IPv6 PTR:

![Okienko "Strefy DNS" rekordy IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Poniższe przykłady pokazują, jak wyświetlać rekordy przy użyciu programu PowerShell lub wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Często zadawane pytania

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Czy możesz hostować stref odwrotnego systemu DNS, aby Moje bloki IP przypisany przez usługodawcę internetowego w usłudze Azure DNS?

Tak. Hostowanie stref wyszukiwania wstecznego (ARPA) dla własnych zakresów adresów IP w usłudze Azure DNS jest w pełni obsługiwany.

Tworzenie strefy wyszukiwania wstecznego w usłudze Azure DNS, zgodnie z opisem w tym artykule, a następnie pracować z usługodawcą Internetowym w celu [Deleguj strefę](dns-domain-delegation.md). Następnie można zarządzać rekordów PTR dla każdego wyszukiwania wstecznego w taki sam sposób jak inne typy rekordów.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Ile kosztuje hostingu moich kosztów strefy wstecznego wyszukiwania DNS?

Hostowanie strefy wyszukiwania wstecznego wyszukiwania DNS dla swojej bloku IP przypisany przez usługodawcę internetowego w usłudze Azure DNS jest rozliczana według [standardowych stawek usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Czy można hostowanie stref wyszukiwania wstecznego wyszukiwania DNS dla adresów IPv4 i IPv6 w usłudze Azure DNS?

Tak. W tym artykule opisano sposób tworzenia IPv4 i IPv6 stref wyszukiwania wstecznego DNS wyszukiwania w usłudze Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Można zaimportować istniejącej strefy wyszukiwania wstecznego DNS?

Tak. Aby zaimportować istniejące strefy DNS do usługi Azure DNS, można użyć wiersza polecenia platformy Azure. Ta metoda działa w przypadku stref wyszukiwania do przodu i strefy wyszukiwania wstecznego.

Aby uzyskać więcej informacji, zobacz [importowanie i eksportowanie pliku strefy DNS przy użyciu wiersza polecenia platformy Azure](dns-import-export.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat odwrotnym systemem DNS, zobacz [wyszukiwania wstecznego DNS w witrynie Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [Zarządzanie rekordami odwrotnego systemu DNS dla usług platformy Azure](dns-reverse-dns-for-azure-services.md).
