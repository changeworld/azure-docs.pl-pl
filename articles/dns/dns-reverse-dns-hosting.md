---
title: Hostuj strefy wyszukiwania wstecznego DNS w usłudze Azure DNS
description: Dowiedz się, jak używać usługi Azure DNS do obsługi stref wyszukiwania odwrotnego DNS dla zakresów adresów IP
author: rohinkoul
ms.service: dns
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 78fc3428274be5e1998abe9189bea996f15e278c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454265"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hostuj strefy wyszukiwania wstecznego DNS w usłudze Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak hostować strefy wyszukiwania dns odwrotnej dla przypisanych zakresów adresów IP w usłudze Azure DNS. Zakresy adresów IP reprezentowane przez strefy wyszukiwania wstecznego muszą być przypisane do organizacji, zazwyczaj przez usługodawcę internetowych.

Aby skonfigurować odwrotny system DNS dla adresu IP należącego do platformy Azure, który jest przypisany do usługi platformy Azure, zobacz [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](dns-reverse-dns-for-azure-services.md).

Zanim przeczytasz ten artykuł, powinieneś zapoznać się z [omówieniem odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md).

W tym artykule otrzymasz od kroków, aby utworzyć pierwszą strefę DNS wyszukiwania wstecznego i nagrywać przy użyciu witryny Azure Portal, Azure PowerShell, klasycznego interfejsu wiersza polecenia platformy Azure lub interfejsu wiersza polecenia platformy Azure.

## <a name="create-a-reverse-lookup-dns-zone"></a>Tworzenie strefy DNS wyszukiwania wstecznego

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu **Centrum** wybierz polecenie **Nowa** > **sieć**, a następnie wybierz pozycję **Strefa DNS**.

   ![Wybór "strefy DNS"](./media/dns-reverse-dns-hosting/figure1.png)

1. W okienku **Tworzenie strefy DNS** nazwij strefę DNS. Nazwa strefy jest inaczej spreparowana dla prefiksów IPv4 i IPv6. Użyj instrukcji dla [IPv4](#ipv4) lub [IPv6,](#ipv6) aby nazwać strefę. Po zakończeniu wybierz pozycję **Utwórz,** aby utworzyć strefę.

### <a name="ipv4"></a>Protokół IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 jest oparta na zakresie IP, który reprezentuje. Powinien być w następującym `<IPv4 network prefix in reverse order>.in-addr.arpa`formacie: . Aby uzyskać przykłady, zobacz [Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Podczas tworzenia stref wyszukiwania wstecznego DNS bezklasowe w usłudze Azure`-`DNS należy użyć łącznika ( ) zamiast ukośnika do przodu (`/`) w nazwie strefy.
>
> Na przykład dla zakresu IP 192.0.2.128/26, `128-26.2.0.192.in-addr.arpa` należy użyć jako `128/26.2.0.192.in-addr.arpa`nazwy strefy zamiast .
>
> Mimo że standardy DNS obsługują obie metody, usługa Azure DNS nie`/`obsługuje nazw stref DNS, które zawierają znak ukośnika do przodu ( ).

W poniższym przykładzie pokazano, jak utworzyć `2.0.192.in-addr.arpa` odwrotną strefę DNS klasy C o nazwie w usłudze Azure DNS za pośrednictwem witryny Azure portal:

 ![Okienko "Tworzenie strefy DNS" z wypełnionymi polami](./media/dns-reverse-dns-hosting/figure2.png)

**Lokalizacja grupy zasobów** definiuje lokalizację grupy zasobów. Nie ma to wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

Poniższe przykłady pokazują, jak wykonać to zadanie przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure.

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

### <a name="ipv6"></a>Protokół IPv6

Nazwa strefy wyszukiwania wstecznego IPv6 powinna mieć następującą formę: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Aby uzyskać przykłady, zobacz [Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure](dns-reverse-dns-overview.md#ipv6).


W poniższym przykładzie pokazano, jak utworzyć strefę `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` wyszukiwania wstecznego DNS IPv6 o nazwie w usłudze Azure DNS za pośrednictwem witryny Azure portal:

 ![Okienko "Tworzenie strefy DNS" z wypełnionymi polami](./media/dns-reverse-dns-hosting/figure3.png)

**Lokalizacja grupy zasobów** definiuje lokalizację grupy zasobów. Nie ma to wpływu na strefę DNS. Lokalizacja strefy DNS jest zawsze „globalna” i nie jest wyświetlana.

Poniższe przykłady pokazują, jak wykonać to zadanie przy użyciu programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure.

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

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegowanie strefy wyszukiwania odwrotnego DNS

Po utworzeniu strefy wyszukiwania DNS odwrotnej należy upewnić się, że strefa jest delegowana ze strefy nadrzędnej. Delegowanie DNS umożliwia procesowi rozpoznawania nazw DNS znajdowanie serwerów nazw obsługujących strefę wyszukiwania wstecznego DNS. Te serwery nazw mogą następnie odpowiadać na zapytania wsteczne DNS dotyczące adresów IP w zakresie adresów.

W przypadku stref wyszukiwania do przodu proces delegowania strefy DNS jest opisany w części [Delegowanie domeny do usługi Azure DNS](dns-delegate-domain-azure-dns.md). Delegowanie stref wyszukiwania wstecznego działa w ten sam sposób. Jedyną różnicą jest to, że trzeba skonfigurować serwery nazw z usługodawcą, który dostarczył zakres ip, a nie rejestratora nazw domen.

## <a name="create-a-dns-ptr-record"></a>Tworzenie rekordu PTR DNS

### <a name="ipv4"></a>Protokół IPv4

W poniższym przykładzie przedstawiono proces tworzenia rekordu PTR w strefie odwrotnej DNS w usłudze Azure DNS. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. U góry okienka **strefa DNS** wybierz pozycję **+ Rekord ustawionego,** aby otworzyć okienko **Dodaj zestaw rekordów.**

   ![Przycisk do tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure4.png)

1. Nazwa rekordu ustawionego dla rekordu PTR musi być resztą adresu IPv4 w odwrotnej kolejności. 

   W tym przykładzie pierwsze trzy oktety są już wypełniane jako część nazwy strefy (.2.0.192). W związku z tym tylko ostatni oktet jest dostarczany w polu **Nazwa.** Na przykład można nazwać zestaw rekordów **15** dla zasobu, którego adres IP jest 192.0.2.15.  
1. W przypadku **opcji Typ**wybierz **opcję PTR**.  
1. W przypadku **nazwy domeny**wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) zasobu korzystającego z adresu IP.
1. Wybierz **przycisk OK** u dołu okienka, aby utworzyć rekord DNS.

   ![Okienko "Dodaj zestaw rekordów" z wypełnionymi polami](./media/dns-reverse-dns-hosting/figure5.png)

Poniższe przykłady pokazują, jak wykonać to zadanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

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

### <a name="ipv6"></a>Protokół IPv6

W poniższym przykładzie przedstawiono proces tworzenia nowego rekordu PTR. Aby uzyskać informacje o innych typach rekordów oraz sposobie modyfikowania istniejących rekordów, zobacz [Manage DNS records and record sets by using the Azure portal](dns-operations-recordsets-portal.md) (Zarządzanie rekordami i zestawami rekordów DNS przy użyciu witryny Azure Portal).

1. U góry okienka **strefa DNS** wybierz pozycję **+ Rekord ustawionego,** aby otworzyć okienko **Dodaj zestaw rekordów.**

   ![Przycisk do tworzenia zestawu rekordów](./media/dns-reverse-dns-hosting/figure6.png)

2. Nazwa rekordu ustawionego dla rekordu PTR musi być resztą adresu IPv6 w odwrotnej kolejności. Nie może zawierać żadnej kompresji zerowej. 

   W tym przykładzie pierwsze 64 bity protokołu IPv6 są już wypełnione jako część nazwy strefy (0.0.0.0.0.c.d.b.a.8.b.d.0.0.0.2.ip6.arpa). W związku z tym tylko ostatnie 64 bity są dostarczane w **polu Nazwa.** Ostatnie 64 bity adresu IP są wprowadzane w odwrotnej kolejności, z kropką jako ogranicznik między każdą liczbą szesnastkową. Na przykład można nadać nazwę zestawowi rekordów **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** dla zasobu, którego adres IP to 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. W przypadku **opcji Typ**wybierz **opcję PTR**.  
4. W przypadku **nazwy domeny**wprowadź nazwę FQDN zasobu korzystającego z adresu IP.
5. Wybierz **przycisk OK** u dołu okienka, aby utworzyć rekord DNS.

![Okienko "Dodaj zestaw rekordów" z wypełnionymi polami](./media/dns-reverse-dns-hosting/figure7.png)

Poniższe przykłady pokazują, jak wykonać to zadanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Wyświetlanie rekordów

Aby wyświetlić utworzone rekordy, przejdź do strefy DNS w witrynie Azure portal. W dolnej części okienka **strefa DNS** można wyświetlić rekordy strefy DNS. Powinny być widoczne domyślne rekordy NS i SOA oraz wszystkie nowe rekordy, które zostały utworzone. Rekordy NS i SOA są tworzone w każdej strefie. 

### <a name="ipv4"></a>Protokół IPv4

W okienku **strefy DNS** są wyświetlane rekordy PTR IPv4:

![Okienko "strefa DNS" z rekordami IPv4](./media/dns-reverse-dns-hosting/figure8.png)

Poniższe przykłady pokazują, jak wyświetlić rekordy PTR przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

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
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>Protokół IPv6

W okienku **strefy DNS** są wyświetlane rekordy PTR IPv6:

![Okienko "strefa DNS" z rekordami IPv6](./media/dns-reverse-dns-hosting/figure9.png)

Poniższe przykłady pokazują, jak wyświetlić rekordy przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

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
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Czy mogę hostować strefy wyszukiwania wstecznego DNS dla bloków IP przypisanych przez usługodawcę isp w usłudze Azure DNS?

Tak. Hostowanie stref wyszukiwania wstecznego (ARPA) dla własnych zakresów adresów IP w usłudze Azure DNS jest w pełni obsługiwane.

Utwórz strefę wyszukiwania wstecznego w usłudze Azure DNS, jak wyjaśniono w tym artykule, a następnie pracuj z usługodawcączym, aby [delegować strefę](dns-domain-delegation.md). Następnie można zarządzać rekordami PTR dla każdego wyszukiwania wstecznego w taki sam sposób, jak inne typy rekordów.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Ile kosztuje hostowanie mojej strefy wyszukiwania DNS?

Hostowanie strefy wyszukiwania odwrotnego DNS dla bloku IP przypisanego przez usługodawcę isp w usłudze Azure DNS jest naliczane [według standardowych stawek DNS platformy Azure.](https://azure.microsoft.com/pricing/details/dns/)

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Czy mogę hostować strefy wyszukiwania dns wstecznego dla adresów IPv4 i IPv6 w usłudze Azure DNS?

Tak. W tym artykule wyjaśniono, jak utworzyć zarówno strefy wyszukiwania wstecznego DNS IPv4, jak i IPv6 w usłudze Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Czy mogę zaimportować istniejącą strefę wyszukiwania wstecznego DNS?

Tak. Za pomocą interfejsu wiersza polecenia platformy Azure można importować istniejące strefy DNS do usługi Azure DNS. Ta metoda działa zarówno dla stref wyszukiwania do przodu, jak i stref wyszukiwania wstecznego.

Aby uzyskać więcej informacji, zobacz [Importowanie i eksportowanie pliku strefy DNS przy użyciu interfejsu wiersza polecenia platformy Azure](dns-import-export.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat odwrotnego DNS, zobacz [odwrotne wyszukiwanie DNS na Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [zarządzać wsteczne rekordy DNS dla usług platformy Azure](dns-reverse-dns-for-azure-services.md).
