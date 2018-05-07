---
title: Host stref wyszukiwania wstecznego wyszukiwania DNS w usłudze Azure DNS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure DNS do obsługi stref wyszukiwania wstecznego wyszukiwania DNS dla zakresy IP
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: c5d53838ffb77ff8d33fee9d10aea4a74f3c523c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Strefy hosta do wyszukiwania wstecznego DNS w usłudze Azure DNS

W tym artykule wyjaśniono, jak udostępniać stref wyszukiwania wstecznego wyszukiwania DNS dla przypisane zakresy IP w usłudze Azure DNS. Zakresy IP reprezentowany przez strefy wyszukiwania wstecznego musi być przypisany do organizacji, zazwyczaj przez Usługodawcę internetowego.

Aby skonfigurować wstecznego DNS dla adresu IP należącą do Azure, który jest przypisany do usługi Azure, zobacz [Konfiguruj reverse DNS dla usługi hostowanej na platformie Azure](dns-reverse-dns-for-azure-services.md).

Przed przeczytaniem tego artykułu, należy się zapoznać z [omówienie wstecznego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W tym artykule przedstawiono kroki, aby utworzyć pierwszą strefę DNS wyszukiwania wstecznego, a rekord przy użyciu portalu Azure, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure w wersji 1.0 lub 2.0 interfejsu wiersza polecenia platformy Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Utwórz strefę wyszukiwania wstecznego DNS

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na **Centrum** menu, wybierz opcję **nowy** > **sieci**, a następnie wybierz **strefy DNS**.

   ![Wybór "Strefę DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. W **strefy DNS Utwórz** okienku Nazwa strefy DNS. Nazwa strefy, co jest inaczej dla prefiksy IPv4 i IPv6. Postępuj zgodnie z instrukcjami dla [IPv4](#ipv4) lub [IPv6](#ipv6) nazwę strefy. Gdy skończysz, wybierz **Utwórz** można utworzyć strefy.

### <a name="ipv4"></a>Protokół IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 opiera się na zakres IP, który reprezentuje. Powinna być w następującym formacie: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Aby uzyskać przykłady, zobacz [omówienie wstecznego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Podczas tworzenia classless strefy wyszukiwania wstecznego DNS w usłudze Azure DNS, należy użyć łącznika (`-`) zamiast ukośnikiem (`/`) w nazwie strefy.
>
> Na przykład dla 192.0.2.128/26 zakres IP, należy użyć `128-26.2.0.192.in-addr.arpa` jako nazwa strefy zamiast `128/26.2.0.192.in-addr.arpa`.
>
> Standardy usługi DNS obsługują obu metod, usługi Azure DNS nie obsługuje nazw strefy DNS, które zawierają dla ukośnika (`/`) znaków.

Poniższy przykład przedstawia sposób tworzenia strefy DNS wyszukiwania wstecznego C klasy o nazwie `2.0.192.in-addr.arpa` w usłudze Azure DNS za pomocą portalu Azure:

 ![Okienko "Utworzyć strefę DNS", z polami wypełnione](./media/dns-reverse-dns-hosting/figure2.png)

**Lokalizacja grupy zasobów** Określa lokalizację dla grupy zasobów. Nie ma to wpływu na strefy DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

Poniższe przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>Protokół IPv6

Nazwa strefy wyszukiwania wstecznego IPv6, powinna być w następującym formacie: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Aby uzyskać przykłady, zobacz [omówienie wstecznego DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv6).


Poniższy przykład przedstawia sposób tworzenia IPv6 strefy wyszukiwania wstecznego DNS wyszukiwania o nazwie `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` w usłudze Azure DNS za pomocą portalu Azure:

 ![Okienko "Utworzyć strefę DNS", z polami wypełnione](./media/dns-reverse-dns-hosting/figure3.png)

**Lokalizacja grupy zasobów** Określa lokalizację dla grupy zasobów. Nie ma to wpływu na strefy DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

Poniższe przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegowanie strefy wyszukiwania wstecznego DNS

Teraz, po utworzeniu strefy wyszukiwania wstecznego DNS, należy się upewnić, że strefa jest delegowane ze strefy nadrzędnej. Delegowanie DNS umożliwia proces rozpoznawania nazw DNS znaleźć serwery nazw hostujące strefy wyszukiwania wstecznego DNS. Te serwery nazw można następnie odpowiedzi DNS zapytań wstecznych dla adresów IP z zakresu adresów.

Proces delegowanie strefy DNS dla strefy wyszukiwania do przodu, opisano w [Delegowanie domeny do usługi Azure DNS](dns-delegate-domain-azure-dns.md). Delegowanie dla strefy wyszukiwania wstecznego działa tak samo. Jedyna różnica polega na tym, że należy skonfigurować serwery nazw z usługodawcą Internetowym, że podany zakres IP, a nie rejestratora nazw domen.

## <a name="create-a-dns-ptr-record"></a>Tworzenie rekordu PTR systemu DNS

### <a name="ipv4"></a>Protokół IPv4

Poniższy przykład przeprowadzi Cię przez proces tworzenia rekordu PTR w strefy wyszukiwania wstecznego DNS w usłudze Azure DNS. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. W górnej części **strefy DNS** okienku wybierz **+ zestawu rekordów** otworzyć **dodać zestaw rekordów** okienka.

   ![Przycisk tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure4.png)

1. Nazwa zestawu dla rekordu PTR rekordów należy pozostałą część adresu IPv4 w odwrotnej kolejności. 

   W tym przykładzie pierwsze trzy oktety są już wypełnione jako część nazwy strefy (.2.0.192). W związku z tym tylko ostatni oktet jest dostarczany w **nazwa** pole. Na przykład może być nazwa zestawu rekordów **15** dla zasobu, którego adres IP jest 192.0.2.15.  
1. Aby uzyskać **typu**, wybierz pozycję **PTR**.  
1. Aby uzyskać **nazwy domeny**, wprowadź w pełni kwalifikowaną nazwę (FQDN) z zasobem, który korzysta z adresu IP.
1. Wybierz **OK** w dolnej części okienka, aby utworzyć DNS rekordów.

 !["Dodaj zestaw rekordów" okienku z polami wypełnione](./media/dns-reverse-dns-hosting/figure5.png)

Poniższe przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>Protokół IPv6

Poniższy przykład przeprowadzi Cię przez proces tworzenia nowego rekordu PTR. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. W górnej części **strefy DNS** okienku wybierz **+ zestawu rekordów** otworzyć **dodać zestaw rekordów** okienka.

   ![Przycisk tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure6.png)

2. Nazwa zestawu dla rekordu PTR rekordów należy pozostałą część adresu IPv6 w odwrotnej kolejności. Nie może zawierać zero kompresji. 

   W tym przykładzie pierwsze 64-bitowy IPv6 są już wypełnione jako część nazwy strefy (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). W związku z tym ostatnie 64 bity są dostarczane w **nazwa** pole. Ostatnie 64 bity adresu IP są wprowadzane w odwrotnej kolejności kropką jako separator każdą liczbę szesnastkową. Na przykład może być nazwa zestawu rekordów **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** dla zasobu, którego adres IP jest 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. Aby uzyskać **typu**, wybierz pozycję **PTR**.  
4. Aby uzyskać **nazwy domeny**, wprowadź nazwę FQDN zasobu, który korzysta z adresu IP.
5. Wybierz **OK** w dolnej części okienka, aby utworzyć DNS rekordów.

!["Dodaj zestaw rekordów" okienku z polami wypełnione](./media/dns-reverse-dns-hosting/figure7.png)

Poniższe przykłady przedstawiają sposób wykonania tego zadania przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić rekordy, które zostały utworzone, przejdź do strefy DNS w portalu Azure. W dolnej części **strefy DNS** okienku widać rekordów dla strefy DNS. Powinna zostać wyświetlona domyślna NS rekordów SOA, a także nowych rekordów, które zostały utworzone. Rekordy NS i SOA są tworzone w każdej strefie. 

### <a name="ipv4"></a>Protokół IPv4

**Strefy DNS** w okienku zostaną wyświetlone rekordów IPv4 PTR:

![Okienko "Strefę DNS" rekordy IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Poniższe przykłady przedstawiają sposób wyświetlania rekordów PTR przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>Protokół IPv6

**Strefy DNS** w okienku zostaną wyświetlone rekordów IPv6 PTR:

![Okienko "Strefę DNS" rekordy IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Poniższe przykłady przedstawiają sposób wyświetlania rekordów przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Interfejs wiersza polecenia platformy Azure CLI w wersji 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Często zadawane pytania

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Stref wyszukiwania wstecznego wyszukiwania DNS może być obsługiwać Moje bloków IP przypisany usługodawcy internetowego w usłudze Azure DNS?

Tak. Hosting stref wyszukiwania wstecznego (ARPA) do własnego zakresów IP w usłudze Azure DNS jest w pełni obsługiwany.

Tworzenie strefy wyszukiwania wstecznego w usłudze Azure DNS, zgodnie z objaśnieniem w tym artykule, a następnie skontaktowanie się z usługodawcą Internetowym w celu [delegowanie strefy](dns-domain-delegation.md). Następnie można zarządzać rekordów PTR dla każdego wyszukiwania wstecznego w taki sam sposób jak inne typy rekordów.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Jaka jest hosting moich kosztów strefy wstecznego wyszukiwania DNS?

Hosting strefy wyszukiwania wstecznego wyszukiwania DNS dla sieci bloku IP przypisany usługodawcy internetowego w usłudze Azure DNS jest rozliczana według [standardowe opłaty za usługę Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Czy można udostępniać stref wyszukiwania wstecznego wyszukiwania DNS dla adresów IPv4 i IPv6 w usłudze Azure DNS?

Tak. W tym artykule opisano sposób tworzenia protokołów IPv4 i IPv6 stref wyszukiwania wstecznego DNS wyszukiwania w usłudze Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Można zaimportować istniejący strefy wyszukiwania wstecznego DNS?

Tak. Interfejsu wiersza polecenia Azure umożliwia importowanie istniejących stref DNS w usłudze Azure DNS. Ta metoda działa zarówno dla strefy wyszukiwania do przodu i strefy wyszukiwania wstecznego.

Aby uzyskać więcej informacji, zobacz [importowanie i eksportowanie pliku strefy DNS przy użyciu interfejsu wiersza polecenia Azure](dns-import-export.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących wstecznego DNS, zobacz [istnienia wstecznego wyszukiwania DNS dla Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [Zarządzanie odwrotnej rekordy DNS dla usług Azure](dns-reverse-dns-for-azure-services.md).
