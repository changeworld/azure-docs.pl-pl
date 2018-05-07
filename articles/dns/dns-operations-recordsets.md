---
title: Zarządzanie rekordami DNS w usłudze Azure DNS przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie zestawów rekordów DNS i rekordy w usłudze Azure DNS, gdy hosting domeny w usłudze Azure DNS. Wszystkie polecenia programu PowerShell dla operacji na zestawów rekordów i rekordów.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: kumud
ms.openlocfilehash: 511af342727dc46369ae70d60a7e9a3171bf986d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Zarządzanie rekordami DNS i zestawy rekordów w usłudze Azure DNS przy użyciu programu Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Interfejs wiersza polecenia platformy Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](dns-operations-recordsets-cli.md)
> * [Program PowerShell](dns-operations-recordsets.md)

W tym artykule przedstawiono sposób zarządzania rekordy DNS dla strefy DNS przy użyciu programu Azure PowerShell. Rekordy DNS można też zarządzać za pomocą wielu platform [interfejsu wiersza polecenia Azure](dns-operations-recordsets-cli.md) lub [portalu Azure](dns-operations-recordsets-portal.md).

Przykłady w tym artykule założono, że masz już [zainstalowany program Azure PowerShell, zalogowany i utworzyć strefę DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Utwórz nowy rekord DNS

Twój nowy rekord ma taką samą nazwę i typ jako istniejącego rekordu, konieczne będzie [dodać go do istniejącego zestawu rekordów](#add-a-record-to-an-existing-record-set). Twój nowy rekord ma inną nazwę i typ do wszystkich istniejących rekordów, należy utworzyć nowy zestaw rekordów. 

### <a name="create-a-records-in-a-new-record-set"></a>Utwórz rekordy "" w zestawie rekordów

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzureRmDnsRecordSet`. Podczas tworzenia zestawu rekordów, należy określić zestawu rekordów nazwę strefy, czas wygaśnięcia (TTL), typ rekordu i rekordy, które ma zostać utworzony.

Parametry używane do dodawania rekordów do zestawu rekordów różnią się w zależności od typu zestawu rekordów. Na przykład podczas korzystania z zestawu rekordów typu "A", należy określić adres IP za pomocą parametru `-IPv4Address`. Inne parametry są używane dla innych typów rekordów. Zobacz [typu rekordu dodatkowe przykłady](#additional-record-type-examples) szczegółowe informacje.

Poniższy przykład tworzy rekord o nazwie względnej "www" w strefie DNS "contoso.com". Pełna nazwa zestawu rekordów jest "www.contoso.com". Typ rekordu jest "A", a czas wygaśnięcia wynosi 3600 sekund. Zestaw rekordów zawiera jeden rekord o adresie IP "1.2.3.4".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Aby utworzyć rekord ustawiony na "wierzchołku" strefy (w tym przypadku "contoso.com"), użyj nazwy zestawu rekordów "@" (bez cudzysłowów):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Jeśli musisz utworzyć rekord zawierający więcej niż jeden rekord, najpierw Utwórz macierz lokalnego i dodać rekordy, a następnie przekazać tablicy, tak aby `New-AzureRmDnsRecordSet` w następujący sposób:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość. Poniższy przykład przedstawia sposób tworzenie zestawu rekordów z dwóch wpisów metadanych, "dział = not" i "środowiska produkcji =".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Usługa DNS platformy Azure obsługuje również "empty" zestawów rekordów, które mogą działać jako symbol zastępczy do zarezerwowania nazwy DNS przed utworzeniem rekordów DNS. Pusty zestawów rekordów są widoczne w płaszczyźnie kontroli usługi Azure DNS, ale są wyświetlane na serwery nazw usługi Azure DNS. Poniższy przykład tworzy pusty zestaw rekordów:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

O przedstawiono szczegółowo sposób utworzyć rekordy "", następujące przykłady przedstawiają sposób tworzenia rekordów innych typów rekordów obsługiwane przez usługę Azure DNS.

W każdym przypadku zostanie przedstawiony sposób tworzenia rekordu zestaw zawierający pojedynczy rekord. Wcześniej przykłady rekordy "" można dostosować, aby utworzyć zestawy rekordów z innych typów zawierający wiele rekordów z metadanymi, lub utworzyć pusty zestawów rekordów.

Firma Microsoft nie dają przykład można utworzyć zestawu rekordów SOA, ponieważ SOAs są tworzone i usunąć z każdej strefy DNS i nie można utworzyć ani usunąć oddzielnie. Jednak [SOA można modyfikować, jak pokazano w przykładzie nowsze](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów AAAA z pojedynczym rekordem

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Utwórz rekord CAA z pojedynczym rekordem

```powershell
New-AzureRmDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów CNAME z pojedynczym rekordem

> [!NOTE]
> Standardy usługi DNS nie zezwalają na rekordy CNAME w wierzchołku strefy (`-Name '@'`), ani akceptują zestawów rekordów zawierających więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów MX z pojedynczym rekordem

W tym przykładzie używamy nazwy zestawu rekordów "@" Aby utworzyć rekord MX w wierzchołku strefy (w tym przypadku "contoso.com").


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów NS z pojedynczym rekordem

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów PTR z pojedynczym rekordem

W takim przypadku "Mój-arpa-zone.com" reprezentuje strefy wyszukiwania wstecznego ARPA reprezentujący zakresowi adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP. Nazwa rekordu "10" nie jest ostatni oktet adresu IP w zakresie adresów IP, to reprezentowany przez ten rekord.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów SRV z pojedynczym rekordem

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records), określ  *\_usługi* i  *\_protokołu* w nazwie zestawu rekordów. Nie istnieje potrzeba do uwzględnienia "@" w nazwie zestawu rekordów, podczas tworzenia rekordów SRV zestawu w wierzchołku strefy.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów TXT z pojedynczym rekordem

Poniższy przykład pokazuje, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalną długość ciągu obsługiwane w rekordach TXT, zobacz [rekordów TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Pobierz zestaw rekordów

Aby uzyskać dostęp do istniejącego zestawu rekordów, użyj `Get-AzureRmDnsRecordSet`. To polecenie cmdlet zwraca obiekt reprezentujący zestawu w usłudze Azure DNS rekordów.

Jak `New-AzureRmDnsRecordSet`, musi być podana nazwa zestawu rekordów *względną* nazwy, co oznacza należy wykluczyć Nazwa strefy. Należy również określić typ rekordu i zestaw stref zawierających rekordu.

Poniższy przykład pokazuje, jak pobrać zestawu rekordów. W tym przykładzie strefa jest określona za pomocą `-ZoneName` i `-ResourceGroupName` parametrów.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie można również określić strefy za pomocą obiektu strefy przekazywane przy użyciu `-Zone` parametru.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Można również użyć `Get-AzureRmDnsZone` do listy zestawów rekordów w strefie, pomijając `-Name` i/lub `-RecordType` parametrów.

Poniższy przykład zwraca zestawy wszystkich rekordów w strefie:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

W poniższym przykładzie pokazano, jak rejestrować wszystkie zestawy danego typu mogą być pobierane przez określenie typu rekordu podczas pominięcie rekord Nazwa zestawu:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Aby pobrać wszystkie zestawy rekordów o podanej nazwie, różnych typów rekordów, należy pobrać wszystkie zestawy rekordów, a następnie przeprowadź filtrowanie wyników:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

We wszystkich powyższych przykładach można określić strefy za pomocą `-ZoneName` i `-ResourceGroupName`parametrów (jak pokazano) albo określając obiektu strefy:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodaj rekord do istniejącego zestawu rekordów

Aby dodać rekordu do istniejącego zestawu rekordów, wykonaj następujące trzy kroki:

1. Pobierz istniejącego zestawu rekordów

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Dodaj nowy rekord do lokalnego zestawu rekordów. Jest to operacja offline.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdź zmiany do usługi Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Przy użyciu `Set-AzureRmDnsRecordSet` *zastępuje* istniejącego zestawu rekordów w usłudze Azure DNS (i wszystkich rekordów zawiera) z zestawu rekordów określony. [Element etag kontroli](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną zastąpione. Można użyć opcjonalnego `-Overwrite` przełącznik, aby pominąć kontrole.

Ta sekwencja operacji można też *przetwarzana potokowo*, co oznacza przekazać obiekt zestawu rekordów za pomocą potoku zamiast przekazaniem go jako parametr:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Powyższe przykłady pokazują, jak dodać rekord "" do istniejącego zestawu rekordów typu "A". Podobne sekwencja operacji jest używana do dodawania rekordów do zestawów rekordów innych typów, zastępując `-Ipv4Address` parametr `Add-AzureRmDnsRecordConfig` z innymi parametrami, które są specyficzne dla poszczególnych typów rekordów. Parametry dla poszczególnych typów rekordów są takie same, jak w przypadku `New-AzureRmDnsRecordConfig` polecenia cmdlet, jak pokazano w [typu rekordu dodatkowe przykłady](#additional-record-type-examples) powyżej.

Zestawy rekordów typu "CNAME" lub "SOA" nie może zawierać więcej niż jeden rekord. To ograniczenie wynika z standardy usługi DNS. Nie jest to ograniczenie usługi Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usuń rekord z istniejącego zestawu rekordów

Usuwanie rekordu zestawu rekordów proces jest podobny do procesu można dodać rekordu do istniejącego zestawu rekordów:

1. Pobierz istniejącego zestawu rekordów

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Usuń rekord z obiektu lokalnego zestawu rekordów. Jest to operacja offline. Rekord, który jest usuwana musi być identyczna z istniejącym rekordem we wszystkich parametrów.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdź zmiany do usługi Azure DNS. Użyj opcjonalnego `-Overwrite` przełącznik, aby pominąć [sprawdza Etag](dns-zones-records.md#etags) równoczesnych zmian.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

Za pomocą sekwencji powyżej, aby usunąć ostatni rekord z zestawu rekordów nie powoduje usunięcia zestawu rekordów, a nie wydostaje pusty zestaw rekordów. Aby usunąć rekord całkowicie, zobacz [Usuń zestaw rekordów](#delete-a-record-set).

Podobnie do dodawania rekordów do zestawu rekordów, kolejność operacji, aby usunąć zestaw rekordów mogą również być przekazywane w potoku:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Różnych typów rekordów są obsługiwane, przekazując odpowiednie parametry określonego typu, aby `Remove-AzureRmDnsRecordSet`. Parametry dla poszczególnych typów rekordów są takie same, jak w przypadku `New-AzureRmDnsRecordConfig` polecenia cmdlet, jak pokazano w [typu rekordu dodatkowe przykłady](#additional-record-type-examples) powyżej.


## <a name="modify-an-existing-record-set"></a>Modyfikowanie istniejącego zestawu rekordów

Procedura modyfikowania istniejącego zestawu rekordów są podobne do czynności, które należy wykonać podczas dodawania lub usuwania rekordów z zestawu rekordów:

1. Pobrać istniejącego rekordu skonfigurowane przy użyciu `Get-AzureRmDnsRecordSet`.
2. Modyfikowanie obiektu lokalnego zestawu rekordów przez:
    * Dodawanie lub usuwanie rekordów
    * Zmiana parametrów istniejące rekordy
    * Zmiana rekordu ustawioną metadanych i czas wygaśnięcia (TTL)
3. Zatwierdź zmiany przy użyciu `Set-AzureRmDnsRecordSet` polecenia cmdlet. To *zastępuje* istniejącego zestawu rekordów w usłudze Azure DNS z określony zestaw rekordów.

Korzystając z `Set-AzureRmDnsRecordSet`, [sprawdza Etag](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną zastąpione. Można użyć opcjonalnego `-Overwrite` przełącznik, aby pominąć kontrole.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aby zaktualizować rekord w istniejącego zestawu rekordów

W tym przykładzie zmienimy istniejący rekord "" adres IP:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

Nie można dodać lub usunąć rekordy z automatycznie utworzonych SOA zestawu rekordów w wierzchołku strefy (`-Name "@"`, w tym znaki cudzysłowu). Jednak można modyfikować żadnego z parametrów w ramach rekord SOA (z wyjątkiem "Host") i czas wygaśnięcia zestawu rekordów.

Poniższy przykład przedstawia sposób zmiany *E-mail* właściwości rekord SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Zestaw w wierzchołku strefy rekordów NS jest tworzony automatycznie w każdej strefie DNS. Zawiera ona nazwy serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Możesz dodać dodatkowe serwery do tego rekordu NS ustawiona, do obsługi wspólnej hostingu domen z więcej niż jednego dostawcy usługi DNS. Można również zmodyfikować TTL i metadanych dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnione serwerów nazw usługi Azure DNS.

Należy pamiętać, że dotyczy to tylko zestawu w wierzchołku strefy rekordów NS. Innymi zestawami rekordów NS w strefie (tak jak delegowania strefy podrzędnej) można modyfikować bez ograniczeń.

Poniższy przykład pokazuje, jak dodać serwer dodatkowe nazwy do zestawu w wierzchołku strefy rekordów NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Aby zmodyfikować metadanych zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) można skojarzyć dane specyficzne dla aplikacji z każdego zestawu rekordów jako pary klucz wartość.

Poniższy przykład przedstawia sposób modyfikowania metadane istniejącego zestawu rekordów:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Usuń zestaw rekordów

Zestawy rekordów można usunąć za pomocą `Remove-AzureRmDnsRecordSet` polecenia cmdlet. Usunięcie zestawu rekordów spowoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć SOA i zestawy rekordów NS w wierzchołku strefy (`-Name '@'`).  Usługa Azure DNS utworzenia są automatycznie strefy został utworzony i usunie je automatycznie, gdy strefa zostanie usunięta.

Poniższy przykład pokazuje, jak można usunąć zestawu rekordów. W tym przykładzie nazwy zestawu rekordów, typ zestawu rekordów, nazwę strefy i grupy zasobów są każdego określonego jawnie.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie można określić zestaw rekordów przez nazwę i typ i strefy określonej za pomocą obiektu:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Jak trzecia opcja samego zestawu rekordów, można określić za pomocą obiektu zestaw rekordów:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Po określeniu zestawu do usunięcia za pomocą obiektu zestawu rekordów, rekordów [sprawdza Etag](dns-zones-records.md#etags) są używane do zapewnienia równoległe zmiany nie zostaną usunięte. Można użyć opcjonalnego `-Overwrite` przełącznik, aby pominąć kontrole.

Obiekt zestawu rekordów może również być przekazywane w potoku zamiast przekazywana jako parametr:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` i `Remove-AzureRmDnsRecordSet` obsługują monity o potwierdzenie.

Każde polecenie cmdlet monituje o potwierdzenie, jeśli `$ConfirmPreference` PowerShell preferencji Zmienna ma wartość `Medium` lub niższej. Ponieważ domyślna wartość `$ConfirmPreference` jest `High`, monity nie są podane w przypadku korzystania z domyślnych ustawień programu PowerShell.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie. 

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [strefy i rekordy w usłudze Azure DNS](dns-zones-records.md).
<br>
Dowiedz się, jak [ochrony strefy i rekordy](dns-protect-zones-recordsets.md) przy użyciu usługi Azure DNS.
<br>
Przegląd [dokumentacji programu PowerShell usługi Azure DNS](/powershell/module/azurerm.dns).
