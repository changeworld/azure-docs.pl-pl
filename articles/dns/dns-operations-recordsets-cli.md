---
title: Zarządzanie rekordami DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure
description: Zarządzanie zestawami rekordów DNS i rekordami w usłudze Azure DNS podczas hostowania domeny w usłudze Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4e017dc940e1d32888ff279904e44d34db1fd5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936884"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Zarządzanie rekordami i zestawami rekordów DNS w usłudze Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

W tym artykule pokazano, jak zarządzać rekordami DNS dla strefy DNS przy użyciu wieloplatformowego interfejsu wiersza polecenia platformy Azure, który jest dostępny dla systemów Windows, Mac i Linux. Można również zarządzać rekordami DNS za pomocą [programu Azure PowerShell](dns-operations-recordsets.md) lub [witryny Azure Portal](dns-operations-recordsets-portal.md).

Przykłady w tym artykule zakładają, że masz już [zainstalowany interfejsu wiersza polecenia platformy Azure, zalogowany i utworzony strefę DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Tworzenie rekordu DNS

Aby utworzyć rekord DNS, `az network dns record-set <record-type> add-record` użyj `<record-type>` polecenia (gdzie jest typ rekordu, tj. a, srv, txt itp.) Aby uzyskać `az network dns record-set --help`pomoc, zobacz .

Podczas tworzenia rekordu należy określić nazwę grupy zasobów, nazwę strefy, nazwę zestawu rekordów, typ rekordu oraz szczegóły tworzonego rekordu. Podana nazwa zestawu rekordów musi być nazwą *względną,* co oznacza, że musi wykluczać nazwę strefy.

Jeśli zestaw rekordów jeszcze nie istnieje, to polecenie utworzy go dla Ciebie. Jeśli zestaw rekordów już istnieje, to polecenie doda określony rekord do istniejącego zestawu rekordów.

Jeśli jest tworzony nowy rekord, używany jest domyślny czas wygaśnięcia wynoszący 3600. Aby uzyskać instrukcje dotyczące używania różnych list TTLs, zobacz [Tworzenie zestawu rekordów DNS](#create-a-dns-record-set).

Poniższy przykład tworzy rekord A o nazwie *www* w strefie *contoso.com* w grupie zasobów *MyResourceGroup*. Adres IP rekordu A to *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Aby utworzyć rekord ustawiony w wierzchołku strefy (w tym przypadku "contoso.com"), należy użyć nazwy rekordu "\@", w tym cudzysłowów:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Tworzenie zestawu rekordów DNS

W powyższych przykładach rekord DNS został dodany do istniejącego zestawu rekordów lub zestaw rekordów został utworzony *niejawnie*. Można również utworzyć zestaw rekordów *jawnie* przed dodaniem do niego rekordów. Usługa Azure DNS obsługuje "puste" zestawy rekordów, które mogą działać jako symbol zastępczy w celu zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Puste zestawy rekordów są widoczne na płaszczyźnie sterowania usługi Azure DNS, ale nie są wyświetlane na serwerach nazw DNS platformy Azure.

Zestawy rekordów są `az network dns record-set <record-type> create` tworzone za pomocą polecenia. Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> create --help`.

Utworzenie zestawu rekordów jawnie umożliwia określenie właściwości zestawu rekordów, takich jak [czas wygaśnięcia (TTL)](dns-zones-records.md#time-to-live) i metadane. [Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucza-wartości.

Poniższy przykład tworzy pusty zestaw rekordów typu "A" z 60-sekundowym TTL, używając parametru `--ttl` (formularz `-l`krótki):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Poniższy przykład tworzy zestaw rekordów z dwoma wpisami metadanych, "dept=finance" i "environment=production", przy użyciu parametru: `--metadata`

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Po utworzeniu pustego zestawu rekordów `azure network dns record-set <record-type> add-record` rekordy można dodawać w sposób opisany w [opisie w artykułach Utwórz rekord DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

Po obejrzeniu szczegółowo, jak utworzyć rekordy "A", poniższe przykłady pokazują, jak utworzyć rekord innych typów rekordów obsługiwanych przez usługę Azure DNS.

Parametry używane do określenia danych rekordu różnią się w zależności od typu rekordu. Na przykład w przypadku rekordu typu „A” należy podać adres IPv4 przy użyciu parametru `--ipv4-address <IPv4 address>`. Parametry dla każdego typu rekordu `az network dns record-set <record-type> add-record --help`można wymienić za pomocą .

W każdym przypadku pokazujemy, jak utworzyć pojedynczy rekord. Rekord zostanie dodany do istniejącego zestawu rekordów lub zestaw rekord utworzony niejawnie. Aby uzyskać więcej informacji na temat jawnego tworzenia zestawów rekordów i definiowania parametru zestawu rekordów, zobacz [Tworzenie zestawu rekordów DNS](#create-a-dns-record-set).

Nie podajemy przykładu, aby utworzyć zestaw rekordów SOA, ponieważ soa są tworzone i usuwane z każdej strefy DNS i nie mogą być tworzone lub usuwane oddzielnie. Jednak [SOA mogą być modyfikowane, jak pokazano w późniejszym przykładzie](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Tworzenie rekordu AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Tworzenie rekordu CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Tworzenie rekordu CNAME

> [!NOTE]
> Standardy DNS nie zezwalają na rekordy CNAME`--Name "@"`na szczycie strefy ( ), ani nie zezwalają na zestawy rekordów zawierające więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Tworzenie rekordu MX

W tym przykładzie używamy nazwy\@zestawu rekordów " do utworzenia rekordu MX w wierzchołku strefy (w tym przypadku "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Tworzenie rekordu NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Tworzenie rekordu PTR

W takim przypadku "my-arpa-zone.com" reprezentuje strefę ARPA reprezentującą zakres adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP.  Nazwa rekordu "10" jest ostatnim oktetem adresu IP w tym zakresie IP reprezentowanym przez ten rekord.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Tworzenie rekordu SRV

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records)należy * \_* określić * \_usługę* i protokół w nazwie zestawu rekordów. Nie ma potrzeby dołączania " " w\@nazwie zestawu rekordów podczas tworzenia rekordu SRV ustawionego w wierzchołku strefy.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Tworzenie rekordu TXT

W poniższym przykładzie pokazano, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalnej długości ciągu obsługiwanej w rekordach TXT, zobacz [Rekordy TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Ustawianie rekordu

Aby pobrać istniejący zestaw `az network dns record-set <record-type> show`rekordów, użyj programu . Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> show --help`.

Podobnie jak podczas tworzenia rekordu lub zestawu rekordów, podana nazwa zestawu rekordów musi być nazwą *względną,* co oznacza, że musi wykluczać nazwę strefy. Należy również określić typ rekordu, strefę zawierającą zestaw rekordów oraz grupę zasobów zawierającą strefę.

Poniższy przykład pobiera rekord *www* typu A ze strefy *contoso.com* w grupie zasobów *MyResourceGroup:*

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Za pomocą `az network dns record-set list` polecenia można wyświetlić listę wszystkich rekordów w strefie DNS. Aby uzyskać pomoc, zobacz `az network dns record-set list --help`.

W tym przykładzie zwraca wszystkie zestawy rekordów w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*, niezależnie od nazwy lub typu rekordu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

W tym przykładzie zwraca wszystkie zestawy rekordów, które pasują do danego typu rekordu (w tym przypadku rekordów "A"):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodawanie rekordu do istniejącego zestawu rekordów

Można użyć `az network dns record-set <record-type> add-record` zarówno do utworzenia rekordu w nowym zestawie rekordów, jak i do dodania rekordu do istniejącego zestawu rekordów.

Aby uzyskać więcej informacji, zobacz [Tworzenie rekordu DNS](#create-a-dns-record) i Tworzenie [rekordów innych typów](#create-records-of-other-types) powyżej.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usuwanie rekordu z istniejącego zestawu rekordów.

Aby usunąć rekord DNS z istniejącego `az network dns record-set <record-type> remove-record`zestawu rekordów, użyj programu . Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> remove-record -h`.

To polecenie usuwa rekord DNS z zestawu rekordów. Jeśli ostatni rekord w zestawie rekordów zostanie usunięty, sam zestaw rekordów jest również usuwany. Aby zamiast tego zachować pusty `--keep-empty-record-set` zestaw rekordów, użyj tej opcji.

Należy określić rekord do usunięcia i strefę, z której powinien zostać usunięty, używając `az network dns record-set <record-type> add-record`tych samych parametrów, co podczas tworzenia rekordu przy użyciu programu . Parametry te są opisane w [obszarze Tworzenie rekordu DNS](#create-a-dns-record) i Tworzenie [rekordów innych typów](#create-records-of-other-types) powyżej.

Poniższy przykład usuwa rekord A o wartości "1.2.3.4" z zestawu rekordów o nazwie *www* w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Modyfikowanie istniejącego zestawu rekordów

Każdy zestaw rekordów zawiera [czas wygaśnięcia (TTL),](dns-zones-records.md#time-to-live) [metadane](dns-zones-records.md#tags-and-metadata)i rekordy DNS. W poniższych sekcjach wyjaśniono, jak zmodyfikować każdą z tych właściwości.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Aby zmodyfikować rekord A, AAAA, CAA, MX, NS, PTR, SRV lub TXT

Aby zmodyfikować istniejący rekord typu A, AAAA, CAA, MX, NS, PTR, SRV lub TXT, należy najpierw dodać nowy rekord, a następnie usunąć istniejący rekord. Aby uzyskać szczegółowe instrukcje dotyczące usuwania i dodawania rekordów, zobacz wcześniejsze sekcje tego artykułu.

Poniższy przykład pokazuje, jak zmodyfikować rekord "A", z adresu IP 1.2.3.4 do adresu IP 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nie można dodawać, usuwać ani modyfikować rekordów w automatycznie utworzonym`--Name "@"`rekordzie NS ustawionym w wierzchołku strefy ( , w tym znaków cudzysłowu). Dla tego zestawu rekordów jedynymi dozwolonymi zmianami są modyfikowanie rekordu zestawu TTL i metadanych.

### <a name="to-modify-a-cname-record"></a>Aby zmodyfikować rekord CNAME

W przeciwieństwie do większości innych typów rekordów zestaw rekordów CNAME może zawierać tylko jeden rekord.  W związku z tym nie można zastąpić bieżącej wartości przez dodanie nowego rekordu i usunięcie istniejącego rekordu, podobnie jak w przypadku innych typów rekordów.

Zamiast tego, aby zmodyfikować rekord `az network dns record-set cname set-record`CNAME, należy użyć programu . Aby uzyskać pomoc, zobacz`az network dns record-set cname set-record --help`

W przykładzie modyfikuje rekord CNAME ustawiony *www* w strefie *contoso.com*, w grupie zasobów *MyResourceGroup*, aby wskazać "www.fabrikam.net" zamiast jego istniejącej wartości:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

W przeciwieństwie do większości innych typów rekordów zestaw rekordów CNAME może zawierać tylko jeden rekord.  W związku z tym nie można zastąpić bieżącej wartości przez dodanie nowego rekordu i usunięcie istniejącego rekordu, podobnie jak w przypadku innych typów rekordów.

Zamiast tego, aby zmodyfikować rekord `az network dns record-set soa update`SOA, należy użyć pliku . Aby uzyskać pomoc, zobacz `az network dns record-set soa update --help`.

W poniższym przykładzie pokazano, jak ustawić właściwość "email" rekordu SOA dla strefy *contoso.com* w grupie zasobów *MyResourceGroup:*

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Rekord NS ustawiony w wierzchołku strefy jest tworzony automatycznie z każdą strefą DNS. Zawiera nazwy serwerów nazw DNS platformy Azure przypisanych do strefy.

Do tego zestawu rekordów NS można dodać dodatkowe serwery nazw, aby obsługiwać domeny współ hostingowe z więcej niż jednym dostawcą DNS. Można również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw DNS platformy Azure.

Należy zauważyć, że dotyczy to tylko rekordu NS ustawionego w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) mogą być modyfikowane bez ograniczeń.

W poniższym przykładzie pokazano, jak dodać dodatkowy serwer nazw do rekordu NS ustawionego w wierzchołku strefy:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Aby zmodyfikować czas wygaśnięcia istniejącego zestawu rekordów

Aby zmodyfikować czas wygaśnięcia istniejącego `azure network dns record-set <record-type> update`zestawu rekordów, należy użyć programu . Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> update --help`.

W poniższym przykładzie pokazano, jak zmodyfikować rekord zestaw TTL, w tym przypadku do 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Aby zmodyfikować metadane istniejącego zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucza-wartości. Aby zmodyfikować metadane istniejącego `az network dns record-set <record-type> update`zestawu rekordów, użyj programu . Aby uzyskać pomoc, zobacz `az network dns record-set <record-type> update --help`.

W poniższym przykładzie pokazano, jak zmodyfikować zestaw rekordów z dwoma wpisami metadanych, "dept=finance" i "environment=production". Należy zauważyć, że wszystkie istniejące metadane są *zastępowane* przez podane wartości.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Usuwanie zestawu rekordów

Zestawy rekordów można usunąć `az network dns record-set <record-type> delete` za pomocą polecenia. Aby uzyskać pomoc, zobacz `azure network dns record-set <record-type> delete --help`. Usunięcie zestawu rekordów powoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć zestawów rekordów SOA i`--name "@"`NS w wierzchołku strefy ( ).  Są one tworzone automatycznie podczas tworzenia strefy i są usuwane automatycznie po usunięciu strefy.

Poniższy przykład usuwa zestaw rekordów o nazwie *www* typu A ze strefy *contoso.com* w grupie zasobów *MyResourceGroup:*

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Zostanie wyświetlony monit o potwierdzenie operacji usuwania. Aby pominąć ten `--yes` monit, użyj przełącznika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [strefach i rekordach w usłudze Azure DNS](dns-zones-records.md).
<br>
Dowiedz się, jak [chronić strefy i rekordy](dns-protect-zones-recordsets.md) podczas korzystania z usługi Azure DNS.
