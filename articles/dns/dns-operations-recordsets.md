---
title: Zarządzanie rekordami systemu DNS w usłudze Azure DNS przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie zestawami rekordów DNS i rekordów w usłudze Azure DNS podczas hosting domeny w usłudze Azure DNS. Wszystkie polecenia programu PowerShell dla operacji na zestawy rekordów i rekordy.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: victorh
ms.openlocfilehash: ad9e22668d0f6d994ad0fce7b207b5849dfa27e9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651247"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Zarządzanie rekordami systemu DNS i zestawów rekordów w usłudze Azure DNS przy użyciu programu Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule pokazano, jak zarządzać rekordami DNS dla strefy DNS przy użyciu programu Azure PowerShell. Można również zarządzać rekordami systemu DNS przy użyciu dla wielu platform [wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md) lub [witryny Azure portal](dns-operations-recordsets-portal.md).

W przykładach w tym artykule założono, że już [zainstalowany program Azure PowerShell, zalogowano się i utworzono strefę DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Wprowadzenie

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Tworzenie nowego rekordu DNS

Jeśli nowy rekord ma taką samą nazwę i typ co istniejący rekord, musisz [dodać go do istniejącego zestawu rekordów](#add-a-record-to-an-existing-record-set). Jeśli nowy rekord ma inną nazwę i typ do wszystkich istniejących rekordów, musisz utworzyć nowy zestaw rekordów. 

### <a name="create-a-records-in-a-new-record-set"></a>Tworzenie rekordów "" w nowy zestaw rekordów

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzDnsRecordSet`. Podczas tworzenia zestawu rekordów, należy określić zestawu rekordów nazwy, strefa, czas wygaśnięcia (TTL), typ rekordu i rekordy, które ma zostać utworzony.

Parametry używane do dodawania rekordów do zestawu rekordów różnią się w zależności od typu zestawu rekordów. Na przykład, korzystając z zestawu rekordów typu "A", należy określić adres IP za pomocą parametru `-IPv4Address`. Inne parametry są używane dla innych typów rekordów. Zobacz przykłady dodatkowy typ rekordu, aby uzyskać szczegółowe informacje.

Poniższy przykład tworzy zestaw rekordów o nazwie względnej "www" w strefie DNS "contoso.com". W pełni kwalifikowaną nazwą zestawu rekordów jest "www.contoso.com". Typ rekordu to "A", a czas wygaśnięcia wynosi 3600 sekund. Zestaw rekordów zawiera jeden rekord z adresem IP "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Aby utworzyć rekord, który został ustawiony na "wierzchołku" strefy (w tym przypadku "contoso.com"), użyj nazwy zestawu rekordów "\@" (bez cudzysłowu):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Jeśli musisz utworzyć rekord, zestaw zawierający więcej niż jeden rekord, najpierw utwórz lokalnej tablicy i dodać rekordy, a następnie przekazać tablicę na `New-AzDnsRecordSet` w następujący sposób:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość. Poniższy przykład pokazuje, jak utworzyć zestaw z dwoma wpisami metadanych, rekordów "dział = Finanse" i "środowisko = produkcji".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Usługa DNS platformy Azure obsługuje również "empty" zestawy rekordów, które mogą działać jako symbol zastępczy do zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Puste zestawy rekordów są widoczne na płaszczyźnie kontroli usługi Azure DNS, ale pojawiają się na serwerach nazw usługi Azure DNS. Poniższy przykład tworzy pusty zestaw rekordów:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

Posiadanie przedstawiono szczegółowo sposób tworzenia rekordów "", następujące przykłady przedstawiają sposób tworzenia rekordów o innych typach rekordów, obsługiwane przez usługę Azure DNS.

W każdym przypadku samouczków pokazano, jak utworzyć rekord, zestaw zawierający pojedynczy rekord. Wcześniejszych przykładów dla rekordów "" można dostosować, aby utworzyć zestawy rekordów innych typów zawierające wiele rekordów z metadanymi, lub pustą zestawy rekordów są tworzone.

Nie udostępniamy przykład, aby utworzyć zestaw rekordów SOA, ponieważ SOAs są tworzone i usunąć z każdej strefy DNS i nie można utworzyć ani usunąć oddzielnie. Jednak [SOA można modyfikować, jak pokazano w przykładzie nowsze](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów AAAA z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Utwórz rekord CAA, ustaw z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów CNAME z pojedynczym rekordem

> [!NOTE]
> Standardy systemu DNS nie zezwalają na rekordy CNAME w wierzchołku strefy (`-Name '@'`), ani nie zezwalają one zestawach rekordów zawierających więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów MX z pojedynczym rekordem

W tym przykładzie używamy nazwy zestawu rekordów "\@" Aby utworzyć rekord MX w wierzchołku strefy (w tym przypadku "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów NS z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów PTR z pojedynczym rekordem

W tym przypadku "Moja-arpa-arpa.com" reprezentuje strefy wyszukiwania wstecznego ARPA reprezentującej zakres adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP. Nazwa rekordu "10" jest ostatni oktet adresu IP, w tym zakresie adresów IP, reprezentowane przez ten rekord.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów SRV z pojedynczym rekordem

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records), określ  *\_usługi* i  *\_protokołu* w nazwie zestawu rekordów. Nie ma potrzeby obejmujący "\@" w nazwie zestawu rekordów, podczas tworzenia rekordu SRV zestawu w wierzchołku strefy.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów TXT z pojedynczym rekordem

Poniższy przykład pokazuje, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalną długość ciągu obsługiwane w rekordów TXT zobacz [rekordów TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Pobierz zestaw rekordów

Aby pobrać istniejącego zestawu rekordów, użyj `Get-AzDnsRecordSet`. To polecenie cmdlet zwraca obiekt lokalny reprezentujący zestaw rekordów w usłudze Azure DNS.

Podobnie jak w przypadku `New-AzDnsRecordSet`, musi być podana nazwa zestawu rekordów *względną* nazwy, co oznacza, należy wykluczyć, nazwę strefy. Należy także określić typ rekordu, a zestaw stref, zawierające rekord.

Poniższy przykład pokazuje, jak można pobrać zestawu rekordów. W tym przykładzie strefa jest określony, przy użyciu `-ZoneName` i `-ResourceGroupName` parametrów.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie można również określić strefy za pomocą obiektu strefy przekazywanych przy użyciu `-Zone` parametru.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Można również użyć `Get-AzDnsZone` do listy zestawów rekordów w strefie, pomijając `-Name` i/lub `-RecordType` parametrów.

Poniższy przykład zwraca zestawy wszystkich rekordów w strefie:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

W poniższym przykładzie pokazano, jak zarejestrować wszystkie zestawy danego typu mogą być pobierane, określając typ rekordu, gdy pominięcie rekord nazwy zestawu:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Aby pobrać wszystkie zestawy rekordów o podanej nazwie, różnych typów rekordów, należy pobrać wszystkich zestawów rekordów, a następnie przeprowadź filtrowanie wyników:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

We wszystkich powyższych przykładach można określić strefy za pomocą `-ZoneName` i `-ResourceGroupName`parametrów (jak pokazano), albo przez wyszczególnienie obiekt strefy:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodaj rekord do istniejącego zestawu rekordów

Aby dodać rekord do istniejącego zestawu rekordów, wykonaj następujące trzy kroki:

1. Pobieranie istniejącego zestawu rekordów

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Dodaj nowy rekord do lokalnego zestawu rekordów. Jest to operacja offline.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdź zmiany w usłudze DNS platformy Azure. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Za pomocą `Set-AzDnsRecordSet` *zastępuje* istniejącego zestawu rekordów w usłudze DNS platformy Azure (i wszystkie rekordy, które zawiera) z zestawu rekordów określony. [Element etag kontroli](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną zastąpione. Możesz użyć opcjonalnego `-Overwrite` przełącznik pomija tych sprawdzeń.

Ta sekwencja operacji może być również *potokiem*, co oznacza, przekazać obiekt zestawu rekordów przy użyciu potoku zamiast przekazywania go jako parametr:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Powyższe przykłady pokazują, jak dodać rekord "" do istniejącego zestawu rekordów typu "A". Podobne sekwencja operacji jest używana do dodawania rekordów do zestawów rekordów innych typów, zastępując `-Ipv4Address` parametru `Add-AzDnsRecordConfig` z innymi parametrami, które są specyficzne dla każdego typu rekordu. Parametry dla każdego typu rekordu są takie same jak w przypadku `New-AzDnsRecordConfig` polecenia cmdlet, jak pokazano w przykładach dodatkowy typ rekordu.

Zestawy rekordów typu "CNAME" lub "SOA" nie może zawierać więcej niż jeden rekord. To ograniczenie wynika z standardy systemu DNS. Nie jest to ograniczenie usługi Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usunięcie rekordu z istniejącego zestawu rekordów

Usunięcie rekordu z zestawu rekordów proces jest podobny do procesu, aby dodać rekord do istniejącego zestawu rekordów:

1. Pobieranie istniejącego zestawu rekordów

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Usuń rekord z obiektu lokalnego zestawu rekordów. Jest to operacja offline. Rekord, który jest usuwany musi być identyczny z istniejącym rekordem we wszystkich parametrów.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdź zmiany w usłudze DNS platformy Azure. Użyj opcjonalnego `-Overwrite` przełącznika, aby pominąć [sprawdza, czy tag Etag](dns-zones-records.md#etags) równoczesnych zmian.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Za pomocą sekwencji powyżej, aby usunąć ostatniego rekordu z zestawu rekordów nie powoduje usunięcia zestawu rekordów, lecz pozostawia pusty zestaw rekordów. Aby usunąć zestaw całkowicie rekordów, zobacz [usuwanie zestawu rekordów](#delete-a-record-set).

Podobnie do dodawania rekordów do zestawu rekordów, kolejność operacji można usunąć zestawu rekordów można również przekazać w potoku:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Różne typy rekordów są obsługiwane, przekazując odpowiednie parametry specyficzne dla typu, aby `Remove-AzDnsRecordSet`. Parametry dla każdego typu rekordu są takie same jak w przypadku `New-AzDnsRecordConfig` polecenia cmdlet, jak pokazano w przykładach dodatkowy typ rekordu.


## <a name="modify-an-existing-record-set"></a>Modyfikowanie istniejącego zestawu rekordów

Procedura modyfikowania istniejącego zestawu rekordów są podobne do czynności, które należy wykonać podczas dodawania lub usuwania rekordów z zestawu rekordów:

1. Pobieranie istniejącego rekordu, ustawić za pomocą `Get-AzDnsRecordSet`.
2. Zmodyfikuj obiekt lokalny zestaw rekordów przez:
    * Dodawanie i usuwanie rekordów
    * Zmiana parametrów istniejące rekordy
    * Zmiana rekordu równa metadanych i czas wygaśnięcia (TTL)
3. Zatwierdź zmiany przy użyciu `Set-AzDnsRecordSet` polecenia cmdlet. To *zastępuje* istniejącego zestawu rekordów w usłudze Azure DNS z określony zestaw rekordów.

Korzystając z `Set-AzDnsRecordSet`, [sprawdza, czy tag Etag](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną zastąpione. Możesz użyć opcjonalnego `-Overwrite` przełącznik pomija tych sprawdzeń.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aby zaktualizować rekord w istniejącego zestawu rekordów

W tym przykładzie możemy zmienić adres IP do istniejącego rekordu "":

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

Nie można dodać lub usunąć rekordy z automatycznie utworzoną SOA zestaw rekordów w wierzchołku strefy (`-Name "@"`, w tym znaków cudzysłowu). Jednak można modyfikować parametrów w ramach rekord SOA (z wyjątkiem "Host") i czas wygaśnięcia zestawu rekordów.

Poniższy przykład pokazuje, jak zmienić *E-mail* właściwość o rekord SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Zestaw w wierzchołku strefy rekordów NS jest tworzony automatycznie w każdej strefie DNS. Zawiera ona nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Możesz dodać dodatkowe serwery do tego rekordu NS skonfigurowane, do obsługi domeny hosting współpracujących z więcej niż jednego dostawcę DNS. Można również zmodyfikować czas wygaśnięcia i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw usługi Azure DNS.

Należy zauważyć, że dotyczy to tylko zestaw w wierzchołku strefy rekordów NS. Inne zestawy rekordów NS w strefie (co umożliwia delegowanie strefy podrzędnej) można zmodyfikować bez ograniczeń.

Poniższy przykład pokazuje, jak dodać serwer nazw dodatkowe do zestawu w wierzchołku strefy rekordów NS:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Aby zmodyfikować metadanych zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość.

Poniższy przykład pokazuje, jak edytować metadane istniejącego zestawu rekordów:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Usuwanie zestawu rekordów

Można usunąć zestawów rekordów przy użyciu `Remove-AzDnsRecordSet` polecenia cmdlet. Trwa usuwanie zestawu rekordów spowoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć SOA i zestawy rekordów NS w wierzchołku strefy (`-Name '@'`).  Usługa DNS platformy Azure są automatycznego utworzenia strefy został utworzony, a następnie usuwa je automatycznie, gdy strefa zostanie usunięty.

Poniższy przykład pokazuje, jak można usunąć zestawu rekordów. W tym przykładzie nazwy zestawu rekordów, typ zestawu rekordów, nazwę strefy i grupy zasobów są każdego określone jawnie.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie można określić zestaw rekordów według nazwy i typu i strefy określony za pomocą obiektu:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Trzecią możliwością, sam zestaw rekordów można określić za pomocą obiektu zestaw rekordów:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Po określeniu zestawu rekordów do usunięcia za pomocą obiektu zestawu rekordów [sprawdza, czy tag Etag](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną usunięte. Możesz użyć opcjonalnego `-Overwrite` przełącznik pomija tych sprawdzeń.

Obiekt zestawu rekordów można również przekazać w potoku zamiast przekazywania jako parametr:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` i `Remove-AzDnsRecordSet` obsługują monity o potwierdzenie.

Każde polecenie cmdlet monituje o potwierdzenie, jeśli `$ConfirmPreference` zmiennej preferencji programu PowerShell ma wartość `Medium` lub niższą. Ponieważ domyślną wartością dla `$ConfirmPreference` jest `High`, monity nie są podane w przypadku korzystania z domyślnych ustawień programu PowerShell.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie. 

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [stref i rekordów w usłudze Azure DNS](dns-zones-records.md).
<br>
Dowiedz się, jak [chronić strefy i rekordy](dns-protect-zones-recordsets.md) podczas korzystania z usługi Azure DNS.
<br>
Przegląd [dokumentacja usługi Azure DNS z programu PowerShell](/powershell/module/azurerm.dns).
