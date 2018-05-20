---
title: Zarządzanie rekordami DNS w usłudze Azure DNS za pomocą 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie zestawów rekordów DNS i rekordy w usłudze Azure DNS, gdy hosting domeny w usłudze Azure DNS. Wszystkie polecenia 2.0 interfejsu wiersza polecenia dla operacji na zestawów rekordów i rekordów.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: kumud
ms.openlocfilehash: d7a90cb46c25e4e01b89bbf4da563685e92a7249
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli-20"></a>Zarządzanie rekordami DNS i zestawy rekordów w usłudze Azure DNS za pomocą 2.0 interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule przedstawiono sposób zarządzania rekordy DNS dla strefy DNS przy użyciu interfejsu wiersza polecenia platformy Azure i platform (CLI) 2.0, który jest dostępny dla systemu Windows, Mac i Linux. Można również zarządzać rekordami DNS przy użyciu [programu Azure PowerShell](dns-operations-recordsets.md) lub [portalu Azure](dns-operations-recordsets-portal.md).

Przykłady w tym artykule założono, że masz już [zainstalowane 2.0 interfejsu wiersza polecenia Azure, zalogowany i utworzyć strefę DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, użyj `az network dns record-set <record-type> add-record` polecenie (gdzie `<record-type>` jest typem rekordu, tj , srv txt, itp.) Aby uzyskać pomoc, zobacz `az network dns record-set --help`.

Podczas tworzenia rekordu należy określić nazwę grupy zasobów, nazwę strefy, nazwę zestawu rekordów, typ rekordu oraz szczegóły tworzonego rekordu. Podana nazwa zestawu rekordów musi być *względną* nazwy, co oznacza należy wykluczyć Nazwa strefy.

Jeśli zestaw rekordów jeszcze nie istnieje, to polecenie utworzy go dla Ciebie. Jeśli zestaw rekordów już istnieje, to polecenie doda określony rekord do istniejącego zestawu rekordów.

Jeśli jest tworzony nowy rekord, używany jest domyślny czas wygaśnięcia wynoszący 3600. Aby uzyskać instrukcje dotyczące sposobu używania różnych TTLs, zobacz [utworzyć zestaw rekordów DNS](#create-a-dns-record-set).

Poniższy przykład tworzy rekord A o nazwie *www* w strefie *contoso.com* w grupie zasobów *MyResourceGroup*. Adres IP rekordu A to *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Aby utworzyć zestaw rekordów w wierzchołku strefy (w tym przypadku „contoso.com”), użyj nazwy rekordu „@”, wraz z cudzysłowem:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Tworzenie zestawu rekordów DNS

W powyższych przykładach rekord DNS albo został dodany do istniejącego zestawu rekordów lub zestawu rekordów została utworzona *niejawnie*. Można również utworzyć zestaw rekordów *jawnie* przed dodaniem do niej rekordów. Usługa DNS platformy Azure obsługuje "empty" zestawów rekordów, które mogą działać jako symbol zastępczy do zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Pusty zestawów rekordów są widoczne w płaszczyźnie kontroli usługi Azure DNS, ale nie są wyświetlane na serwery nazw usługi Azure DNS.

Zestawy rekordów są tworzone przy użyciu `az network dns record-set <record-type> create` polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> create --help`.

Tworzenie rekordu jawnie ustaw służy do określenia, takie jak właściwości zestawu rekordów [czasu wygaśnięcia (TTL, Time-To-Live)](dns-zones-records.md#time-to-live) i metadanych. [Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość.

Poniższy przykład tworzy zbiór pusty rekord typu "A" TTL 60 sekund, przy użyciu `--ttl` parametr (forma krótka `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Poniższy przykład tworzy rekord z dwóch wpisów metadanych, "dział = not" i "środowiska produkcji =", przy użyciu `--metadata` parametru:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Posiadanie utworzony pusty zestaw rekordów, można dodawać rekordy przy użyciu `azure network dns record-set <record-type> add-record` zgodnie z opisem w [Utwórz rekord DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

O przedstawiono szczegółowo sposób utworzyć rekordy "", następujące przykłady przedstawiają sposób tworzenia rekordu innych typów rekordów obsługiwane przez usługę Azure DNS.

Parametry używane do określenia danych rekordu różnią się w zależności od typu rekordu. Na przykład w przypadku rekordu typu „A” należy podać adres IPv4 przy użyciu parametru `--ipv4-address <IPv4 address>`. Parametry dla poszczególnych typów rekordów mogą być wyświetlane przy użyciu `az network dns record-set <record-type> add-record --help`.

W każdym przypadku zostanie przedstawiony sposób jeden rekord. Rekord jest dodawana do istniejącego zestawu rekordów lub niejawnie utworzyć zestaw rekordów. Aby uzyskać więcej informacji na temat tworzenia zestawów rekordów i rekordu definiujący parametr jawnie, zobacz [utworzyć zestaw rekordów DNS](#create-a-dns-record-set).

Firma Microsoft nie dają przykład można utworzyć zestawu rekordów SOA, ponieważ SOAs są tworzone i usunąć z każdej strefy DNS i nie można utworzyć ani usunąć oddzielnie. Jednak [SOA można modyfikować, jak pokazano w przykładzie nowsze](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Utwórz rekord AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Utwórz rekord CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Utwórz rekord CNAME

> [!NOTE]
> Standardy usługi DNS nie zezwalają na rekordy CNAME w wierzchołku strefy (`--Name "@"`), ani akceptują zestawów rekordów zawierających więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Utwórz rekord MX

W tym przykładzie używamy nazwy zestawu rekordów „@”, aby utworzyć rekord MX w wierzchołku strefy (w tym przypadku „contoso.com”).

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Tworzenie rekordów NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Utwórz rekord PTR

W takim przypadku "Mój-arpa-zone.com" reprezentuje strefy ARPA reprezentujący zakresowi adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP.  Nazwa rekordu "10" nie jest ostatni oktet adresu IP w zakresie adresów IP, to reprezentowany przez ten rekord.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Tworzenie rekordów SRV

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records), określ  *\_usługi* i  *\_protokołu* w nazwie zestawu rekordów. Nie istnieje potrzeba do uwzględnienia "@" w nazwie zestawu rekordów, podczas tworzenia rekordów SRV zestawu w wierzchołku strefy.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Utwórz rekord TXT

Poniższy przykład pokazuje, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalną długość ciągu obsługiwane w rekordach TXT, zobacz [rekordów TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Pobierz zestaw rekordów

Aby uzyskać dostęp do istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> show`. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> show --help`.

Jak podczas tworzenia rekordu lub zestawu rekordów, musi być podana nazwa zestawu rekordów *względną* nazwy, co oznacza należy wykluczyć Nazwa strefy. Należy również określić typ rekordu strefy zawierającej zestawu rekordów i grupę zasobów, zawierającą strefy.

Poniższy przykład pobiera rekordu *www* a typu ze strefy *contoso.com* w grupie zasobów *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Wyświetl listę wszystkich rekordów w strefie DNS przy użyciu `az network dns record-set list` polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set list --help`.

W tym przykładzie zwraca zestawy wszystkich rekordów w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*, niezależnie od tego, czy nazwa lub typ rekordu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

W tym przykładzie zwraca wszystkie zestawy rekordów zgodne podanego typu rekordu (w tym przypadku rekordy ""):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodaj rekord do istniejącego zestawu rekordów

Można użyć `az network dns record-set <record-type> add-record` zarówno tworzenie rekordu w zestawie rekordów lub można dodać rekordu do istniejącego zestawu rekordów.

Aby uzyskać więcej informacji, zobacz [Utwórz rekord DNS](#create-a-dns-record) i [tworzenie rekordów innych typów](#create-records-of-other-types) powyżej.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usuń rekord z istniejącego zestawu rekordów.

Aby usunąć rekord DNS z istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> remove-record`. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> remove-record -h`.

To polecenie usuwa rekord DNS z zestawu rekordów. Usunięcie ostatniego rekordu w zestawie rekordów samego zestawu rekordów jest również usunięte. Aby zachować pusty rekord, ustaw zamiast tego, użyj `--keep-empty-record-set` opcji.

Należy określić usunięcie rekordu i strefy należy ją usunąć, przy użyciu takich samych parametrach co przy tworzeniu przy użyciu rekordu `az network dns record-set <record-type> add-record`. Te parametry są opisane w [Utwórz rekord DNS](#create-a-dns-record) i [tworzenie rekordów innych typów](#create-records-of-other-types) powyżej.

Poniższy przykład umożliwia usunięcie rekordu A o wartości "1.2.3.4" z rekordu ustawić nazwane *www* w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modyfikowanie istniejącego zestawu rekordów

Każdy zestaw rekordów zawiera [czas wygaśnięcia (TTL)](dns-zones-records.md#time-to-live), [metadanych](dns-zones-records.md#tags-and-metadata)i rekordów DNS. W poniższych sekcjach opisano sposób modyfikowania każdej z tych właściwości.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Aby zmodyfikować rekord A, AAAA, CAA, MX, NS, PTR, SRV i TXT

Aby zmodyfikować istniejący rekord z typu A, AAAA, CAA, MX, NS, PTR, SRV i TXT, należy najpierw dodać nowy rekord i następnie usunąć istniejącego rekordu. Aby uzyskać szczegółowe instrukcje na temat usunąć i dodać rekordy Zobacz wcześniejszej części tego artykułu.

Poniższy przykład przedstawia sposób zmodyfikować rekord "", z adresu IP 1.2.3.4 adres IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nie można dodać, usunąć lub zmodyfikować rekordy w tworzonych automatycznie NS zestawu rekordów w wierzchołku strefy (`--Name "@"`, w tym znaki cudzysłowu). Dla tego zestawu rekordów tylko zmiany dozwolone są, aby zmodyfikować rekord ustawić czas wygaśnięcia i metadanych.

### <a name="to-modify-a-cname-record"></a>Aby zmodyfikować rekord CNAME

W przeciwieństwie do większości innych typów rekordów zestawu rekordów CNAME może zawierać tylko jeden rekord.  W związku z tym nie można zastąpić bieżącą wartość dodawania nowego rekordu i usuwanie istniejącego rekordu, podobnie jak w przypadku innych typów rekordów.

Aby zmodyfikować rekord zasobu CNAME, użyj `az network dns record-set cname set-record`. Aby uzyskać pomoc zobacz `az network dns record-set cname set-record --help`

Przykład modyfikuje zestawu rekordów CNAME *www* w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*, aby wskazywał "www.fabrikam.net" zamiast jego istniejącej wartości:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

W przeciwieństwie do większości innych typów rekordów zestawu rekordów CNAME może zawierać tylko jeden rekord.  W związku z tym nie można zastąpić bieżącą wartość dodawania nowego rekordu i usuwanie istniejącego rekordu, podobnie jak w przypadku innych typów rekordów.

Aby zmodyfikować rekord SOA, użyj `az network dns record-set soa update`. Aby uzyskać pomoc, zobacz `az network dns record-set soa update --help`.

Poniższy przykład przedstawia sposób ustawiania właściwości "e-mail" rekord SOA strefy *contoso.com* w grupie zasobów *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Zestaw w wierzchołku strefy rekordów NS jest tworzony automatycznie w każdej strefie DNS. Zawiera ona nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Możesz dodać dodatkowe serwery do tego rekordu NS ustawiona, do obsługi wspólnej hostingu domen z więcej niż jednego dostawcy usługi DNS. Można również zmodyfikować TTL i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnione serwerów nazw usługi Azure DNS.

Należy pamiętać, że dotyczy to tylko zestawu w wierzchołku strefy rekordów NS. Innymi zestawami rekordów NS w strefie (tak jak delegowania strefy podrzędnej) można modyfikować bez ograniczeń.

Poniższy przykład pokazuje, jak dodać serwer dodatkowe nazwy do zestawu w wierzchołku strefy rekordów NS:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Aby zmodyfikować TTL z istniejącego zestawu rekordów

Aby zmodyfikować TTL z istniejącego zestawu rekordów, użyj `azure network dns record-set <record-type> update`. Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> update --help`.

Poniższy przykład przedstawia sposób modyfikowania zestawu rekordów TTL, w tym przypadku do 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Aby zmodyfikować metadane istniejącego zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość. Aby zmodyfikować metadane istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> update`. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> update --help`.

Poniższy przykład przedstawia sposób modyfikowania rekordów dwa wpisy metadanych, "dział = not" i "środowiska produkcyjnego =". Należy zauważyć, że metadane *zastąpione* przez podane wartości.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Usuń zestaw rekordów

Zestawy rekordów można usunąć za pomocą `az network dns record-set <record-type> delete` polecenia. Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> delete --help`. Usunięcie zestawu rekordów spowoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć SOA i zestawy rekordów NS w wierzchołku strefy (`--name "@"`).  Te są tworzone automatycznie podczas strefy został utworzony i są usuwane automatycznie po usunięciu strefy.

Poniższy przykład powoduje usunięcie zestawu o nazwie rekordów *www* a typu ze strefy *contoso.com* w grupie zasobów *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Monit o potwierdzenie operacji usuwania. Aby pominąć ten monit, użyj `--yes` przełącznika.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [strefy i rekordy w usłudze Azure DNS](dns-zones-records.md).
<br>
Dowiedz się, jak [ochrony strefy i rekordy](dns-protect-zones-recordsets.md) przy użyciu usługi Azure DNS.
