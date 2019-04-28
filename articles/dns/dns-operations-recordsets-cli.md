---
title: Zarządzanie rekordami systemu DNS w usłudze Azure DNS przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzanie zestawami rekordów DNS i rekordów w usłudze Azure DNS podczas hosting domeny w usłudze Azure DNS.
services: dns
documentationcenter: na
author: WenJason
manager: digimobile
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
origin.date: 05/15/2018
ms.date: 04/15/2019
ms.author: v-jay
ms.openlocfilehash: 4864a46b91b4e243ce6a2ae3d9d36df28fe74d8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293358"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Zarządzanie rekordami systemu DNS i zestawów rekordów w usłudze Azure DNS przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule pokazano, jak zarządzać rekordami DNS dla strefy DNS przy użyciu interfejsu wiersza polecenia Azure dla wielu platform, który jest dostępny dla systemów Windows, Mac i Linux. Można również zarządzać rekordami DNS przy użyciu [programu Azure PowerShell](dns-operations-recordsets.md) lub [witryny Azure portal](dns-operations-recordsets-portal.md).

W przykładach w tym artykule założono, że już [zainstalowano interfejsu wiersza polecenia Azure, zalogowano się i utworzono strefę DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, należy użyć `az network dns record-set <record-type> add-record` polecenie (gdzie `<record-type>` jest typem rekordu, tj , srv, txt, itp.) Aby uzyskać pomoc, zobacz `az network dns record-set --help`.

Podczas tworzenia rekordu należy określić nazwę grupy zasobów, nazwę strefy, nazwę zestawu rekordów, typ rekordu oraz szczegóły tworzonego rekordu. Musi być podana nazwa zestawu rekordów *względną* nazwy, co oznacza, należy wykluczyć, nazwę strefy.

Jeśli zestaw rekordów jeszcze nie istnieje, to polecenie utworzy go dla Ciebie. Jeśli zestaw rekordów już istnieje, to polecenie doda określony rekord do istniejącego zestawu rekordów.

Jeśli jest tworzony nowy rekord, używany jest domyślny czas wygaśnięcia wynoszący 3600. Aby uzyskać instrukcje na temat sposobu używania różnych czasów wygaśnięcia, zobacz [utworzyć zestaw rekordów DNS](#create-a-dns-record-set).

Poniższy przykład tworzy rekord A o nazwie *www* w strefie *contoso.com* w grupie zasobów *MyResourceGroup*. Adres IP rekordu A to *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Aby utworzyć zestaw rekordów w wierzchołku strefy (w tym przypadku "contoso.com"), użyj nazwy rekordu "\@", wraz z cudzysłowem:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Tworzenie zestawu rekordów DNS

W powyższych przykładach rekordu DNS albo został dodany do istniejącego zestawu rekordów lub został utworzony zestaw rekordów *niejawnie*. Możesz również utworzyć zestaw rekordów *jawnie* przed dodaniem rekordów. Usługa DNS platformy Azure obsługuje "empty" zestawy rekordów, które mogą działać jako symbol zastępczy do zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Puste zestawy rekordów są widoczne na płaszczyźnie kontroli usługi Azure DNS, ale nie pojawiają się na serwerach nazw usługi Azure DNS.

Zestawy rekordów są tworzone przy użyciu `az network dns record-set <record-type> create` polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> create --help`.

Utworzenie rekordu ustawiony w sposób jawny pozwala określić, takie jak właściwości zestawu rekordów [czasu wygaśnięcia (TTL, Time-To-Live)](dns-zones-records.md#time-to-live) i metadanych. [Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość.

Poniższy przykład tworzy zestaw pusty rekordów typu "A" z wartością TTL 60 sekund przy użyciu `--ttl` parametru (skrócona forma `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Poniższy przykład tworzy zestaw z dwoma wpisami metadanych, rekordów "dział = Finanse" i "środowisko = produkcji", przy użyciu `--metadata` parametru:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Jeśli utworzono pusty zestaw rekordów, można dodawać rekordy przy użyciu `azure network dns record-set <record-type> add-record` zgodnie z opisem w [utworzyć rekord DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

Posiadanie przedstawiono szczegółowo sposób tworzenia rekordów "", poniższe przykłady pokazują, jak utworzyć rekord o innych typach rekordów, obsługiwane przez usługę Azure DNS.

Parametry używane do określenia danych rekordu różnią się w zależności od typu rekordu. Na przykład w przypadku rekordu typu „A” należy podać adres IPv4 przy użyciu parametru `--ipv4-address <IPv4 address>`. Parametry dla każdego typu rekordu mogą być wyświetlane przy użyciu `az network dns record-set <record-type> add-record --help`.

W każdym przypadku pokazujemy, jak utworzyć jeden rekord. Rekord jest dodawany do istniejącego zestawu rekordów lub zestawu rekordów tworzone niejawnie. Aby uzyskać więcej informacji na temat tworzenia zestawów rekordów i rekordu Definiowanie parametru jawnie, zobacz [utworzyć zestaw rekordów DNS](#create-a-dns-record-set).

Nie udostępniamy przykład, aby utworzyć zestaw rekordów SOA, ponieważ SOAs są tworzone i usunąć z każdej strefy DNS i nie można utworzyć ani usunąć oddzielnie. Jednak [SOA można modyfikować, jak pokazano w przykładzie nowsze](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Utwórz rekord AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Tworzenie rekordów CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Utwórz rekord CNAME

> [!NOTE]
> Standardy systemu DNS nie zezwalają na rekordy CNAME w wierzchołku strefy (`--Name "@"`), ani nie zezwalają one zestawach rekordów zawierających więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Utwórz rekord MX

W tym przykładzie używamy nazwy zestawu rekordów "\@" Aby utworzyć rekord MX w wierzchołku strefy (w tym przypadku "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Tworzenie rekordów NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Tworzenie rekordu PTR systemu

W tym przypadku "Moja-arpa-arpa.com" reprezentuje strefy ARPA reprezentującej zakres adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP.  Nazwa rekordu "10" jest ostatni oktet adresu IP, w tym zakresie adresów IP, reprezentowane przez ten rekord.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Tworzenie rekordu SRV

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records), określ  *\_usługi* i  *\_protokołu* w nazwie zestawu rekordów. Nie ma potrzeby obejmujący "\@" w nazwie zestawu rekordów, podczas tworzenia rekordu SRV zestawu w wierzchołku strefy.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Utwórz rekord TXT

Poniższy przykład pokazuje, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalną długość ciągu obsługiwane w rekordów TXT zobacz [rekordów TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Pobierz zestaw rekordów

Aby pobrać istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> show`. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> show --help`.

Podczas tworzenia rekordu lub zestawu rekordów, musi być podana nazwa zestawu rekordów *względną* nazwy, co oznacza, należy wykluczyć, nazwę strefy. Należy również określić, czy typ rekordu strefy zawierający zestaw rekordów i grupę zasobów zawierającą strefy.

Poniższy przykład pobiera rekord *www* a typ ze strefy *contoso.com* w grupie zasobów *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Możesz wyświetlić listę wszystkich rekordów w strefie DNS przy użyciu `az network dns record-set list` polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set list --help`.

W tym przykładzie zwraca zestawy wszystkich rekordów w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*, niezależnie od tego, czy nazwa lub typ rekordu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

W tym przykładzie zwraca wszystkie zestawy rekordów, które pasują do podanego typu rekordu (w tym przypadku rekordy ""):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodaj rekord do istniejącego zestawu rekordów

Możesz użyć `az network dns record-set <record-type> add-record` zarówno do tworzenia rekordu w zestawie rekordów lub aby dodać rekord do istniejącego zestaw rekordów.

Aby uzyskać więcej informacji, zobacz [utworzyć rekord DNS](#create-a-dns-record) i [tworzenia rekordów innych typów](#create-records-of-other-types) powyżej.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usunięcie rekordu z istniejącego zestawu rekordów.

Aby usunąć rekord DNS z istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> remove-record`. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> remove-record -h`.

To polecenie usuwa rekord DNS z zestawu rekordów. Usunięcie ostatniego rekordu w zestawie rekordów samego zestawu rekordów jest również usunięte. Aby zachować pusty rekord, zamiast tego zestawu, należy użyć `--keep-empty-record-set` opcji.

Należy określić usunięcie rekordu i strefy którą należy usunąć, przy użyciu tych samych parametrów jako podczas tworzenia rekordu przy użyciu `az network dns record-set <record-type> add-record`. Te parametry są opisane w [utworzyć rekord DNS](#create-a-dns-record) i [tworzenia rekordów innych typów](#create-records-of-other-types) powyżej.

Poniższy przykład usuwa rekord z wartością "1.2.3.4" z rekordu zestawu o nazwie *www* w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modyfikowanie istniejącego zestawu rekordów

Każdy zestaw rekordów zawiera [czas wygaśnięcia (TTL)](dns-zones-records.md#time-to-live), [metadanych](dns-zones-records.md#tags-and-metadata)i rekordów DNS. W poniższych sekcjach opisano sposób modyfikowania każdej z tych właściwości.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Aby zmodyfikować rekord A, AAAA, CAA, MX, NS, PTR, SRV i TXT

Aby zmodyfikować istniejący rekord typu A, AAAA, CAA, MX, NS, PTR, SRV i TXT, należy najpierw dodać nowy rekord, a następnie usuń istniejący rekord. Aby uzyskać szczegółowe instrukcje dotyczące sposobu usuwania i dodawanie rekordów zobacz wcześniejsze sekcje tego artykułu.

Poniższy przykład pokazuje, jak zmodyfikować rekord "", z adresu IP 1.2.3.4 adres IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nie można dodać, usunąć lub zmodyfikować rekordy w automatycznie utworzonej NS zestaw rekordów w wierzchołku strefy (`--Name "@"`, w tym znaków cudzysłowu). Dla tego zestawu rekordów tylko zmiany, dozwolone są do modyfikowania rekordu ustaw czas wygaśnięcia i metadanych.

### <a name="to-modify-a-cname-record"></a>Aby zmodyfikować rekord CNAME

W przeciwieństwie do większości innych typów rekordów zestawu rekordów CNAME mogą zawierać tylko jeden rekord.  W związku z tym nie możesz zastąpić bieżącą wartość przez dodanie nowego rekordu i usuwanie istniejącego rekordu, jak w przypadku innych typów rekordów.

Aby zmodyfikować rekord CNAME, użyj `az network dns record-set cname set-record`. Aby uzyskać pomoc zobacz `az network dns record-set cname set-record --help`

Przykład modyfikuje zestaw rekordów CNAME *www* w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*, aby wskazywał "www.fabrikam.net" zamiast istniejące wartość:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

W przeciwieństwie do większości innych typów rekordów zestawu rekordów CNAME mogą zawierać tylko jeden rekord.  W związku z tym nie możesz zastąpić bieżącą wartość przez dodanie nowego rekordu i usuwanie istniejącego rekordu, jak w przypadku innych typów rekordów.

Aby zmodyfikować rekord SOA, użyj `az network dns record-set soa update`. Aby uzyskać pomoc, zobacz `az network dns record-set soa update --help`.

Poniższy przykład pokazuje, jak ustawić właściwość "email" rekordu SOA strefy *contoso.com* w grupie zasobów *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Zestaw w wierzchołku strefy rekordów NS jest tworzony automatycznie w każdej strefie DNS. Zawiera ona nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Możesz dodać dodatkowe serwery do tego rekordu NS skonfigurowane, do obsługi domeny hosting współpracujących z więcej niż jednego dostawcę DNS. Można również zmodyfikować czas wygaśnięcia i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw usługi Azure DNS.

Należy zauważyć, że dotyczy to tylko zestaw w wierzchołku strefy rekordów NS. Inne zestawy rekordów NS w strefie (co umożliwia delegowanie strefy podrzędnej) można zmodyfikować bez ograniczeń.

Poniższy przykład pokazuje, jak dodać serwer nazw dodatkowe do zestawu w wierzchołku strefy rekordów NS:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.cn
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Aby zmodyfikować czas wygaśnięcia istniejącego zestawu rekordów

Aby zmodyfikować czas wygaśnięcia istniejącego zestawu rekordów, użyj `azure network dns record-set <record-type> update`. Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> update --help`.

Poniższy przykład przedstawia sposób modyfikowania zestawu rekordów czas wygaśnięcia, w tym przypadku do 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Aby zmodyfikować metadane istniejącego zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość. Aby zmodyfikować metadane istniejącego zestawu rekordów, użyj `az network dns record-set <record-type> update`. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> update --help`.

Poniższy przykład przedstawia sposób modyfikowania zestawu rekordów z dwóch wpisów metadanych, "dział = Finanse" i "środowisko produkcji =". Należy zauważyć, że metadane *zastąpione* o wartości podane.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Usuwanie zestawu rekordów

Można usunąć zestawów rekordów przy użyciu `az network dns record-set <record-type> delete` polecenia. Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> delete --help`. Trwa usuwanie zestawu rekordów spowoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć SOA i zestawy rekordów NS w wierzchołku strefy (`--name "@"`).  Są one tworzone automatycznie podczas strefy został utworzony i są usuwane automatycznie po jej usunięciu.

Poniższy przykład usuwa zestaw o nazwie rekordów *www* a typ ze strefy *contoso.com* w grupie zasobów *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Monit o potwierdzenie operacji usuwania. Aby pominąć ten monit, użyj `--yes` przełącznika.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [stref i rekordów w usłudze Azure DNS](dns-zones-records.md).
<br>
Dowiedz się, jak [chronić strefy i rekordy](dns-protect-zones-recordsets.md) podczas korzystania z usługi Azure DNS.
