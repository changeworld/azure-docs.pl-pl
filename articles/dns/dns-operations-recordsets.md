---
title: Zarządzanie rekordami DNS w usłudze Azure DNS przy użyciu programu Azure PowerShell | Dokumenty firmy Microsoft
description: Zarządzanie zestawami rekordów DNS i rekordami w usłudze Azure DNS podczas hostowania domeny w usłudze Azure DNS. Wszystkie polecenia programu PowerShell dla operacji w zestawach rekordów i rekordach.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: b9244d9b2bdc9cb20195bbc103c0b1eb48a9de63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932543"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Zarządzanie rekordami i zestawami rekordów DNS w usłudze Azure DNS przy użyciu programu Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Klasyczny interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli-nodejs.md)
> * [Interfejs wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md)
> * [Powershell](dns-operations-recordsets.md)

W tym artykule pokazano, jak zarządzać rekordami DNS dla strefy DNS przy użyciu programu Azure PowerShell. Rekordy DNS można również zarządzać za pomocą wieloplatformowego [interfejsu wiersza polecenia platformy Azure](dns-operations-recordsets-cli.md) lub portalu [Azure.](dns-operations-recordsets-portal.md)

Przykłady w tym artykule zakładają, że masz już [zainstalowany program Azure PowerShell, zalogowany i utworzony strefę DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Wprowadzenie

Przed utworzeniem rekordów DNS w usłudze Azure DNS należy najpierw zrozumieć, w jaki sposób usługa Azure DNS organizuje rekordy DNS w zestawy rekordów DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Aby uzyskać więcej informacji na temat rekordów DNS w usłudze Azure DNS, zobacz [Strefy i rekordy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Tworzenie nowego rekordu DNS

Jeśli nowy rekord ma taką samą nazwę i typ jak istniejący rekord, należy [go dodać do istniejącego zestawu rekordów](#add-a-record-to-an-existing-record-set). Jeśli nowy rekord ma inną nazwę i typ do wszystkich istniejących rekordów, należy utworzyć nowy zestaw rekordów. 

### <a name="create-a-records-in-a-new-record-set"></a>Tworzenie rekordów "A" w nowym zestawie rekordów

Zestawy rekordów są tworzone za pomocą polecenia cmdlet `New-AzDnsRecordSet`. Podczas tworzenia zestawu rekordów należy określić nazwę zestawu rekordów, strefę, czas wygaśnięcia (TTL), typ rekordu i rekordy, które mają zostać utworzone.

Parametry używane do dodawania rekordów do zestawu rekordów różnią się w zależności od typu zestawu rekordów. Na przykład, korzystając z zestawu rekordów typu "A", należy określić `-IPv4Address`adres IP przy użyciu parametru . Inne parametry są używane dla innych typów rekordów. Zobacz dodatkowe przykłady typów rekordów, aby uzyskać szczegółowe informacje.

Poniższy przykład tworzy zestaw rekordów o względnej nazwie "www" w strefie DNS "contoso.com". W pełni kwalifikowaną nazwą zestawu rekordów jest "www.contoso.com". Typ rekordu to "A", a czas wygaśnięcia wynosi 3600 sekund. Zestaw rekordów zawiera jeden rekord o adresie IP "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Aby utworzyć rekord ustawiony na 'wierzchołku' strefy (w tym przypadku "contoso.com"),\@należy użyć nazwy zestawu rekordów ' ' (z wyłączeniem cudzysłowów):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Jeśli chcesz utworzyć zestaw rekordów zawierający więcej niż jeden rekord, najpierw utwórz tablicę `New-AzDnsRecordSet` lokalną i dodaj rekordy, a następnie przekaż tablicę w następujący sposób:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucza-wartości. W poniższym przykładzie pokazano, jak utworzyć zestaw rekordów z dwoma wpisami metadanych: "dept=finance" i "environment=production".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Usługa Azure DNS obsługuje również "puste" zestawy rekordów, które mogą działać jako symbol zastępczy, aby zarezerwować nazwę DNS przed utworzeniem rekordów DNS. Puste zestawy rekordów są widoczne na płaszczyźnie sterowania usługi Azure DNS, ale są wyświetlane na serwerach nazw dns platformy Azure. Poniższy przykład tworzy pusty zestaw rekordów:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Tworzenie rekordów innych typów

Po obejrzeniu szczegółowo, jak utworzyć rekordy "A", poniższe przykłady pokazują, jak utworzyć rekordy innych typów rekordów obsługiwanych przez usługę Azure DNS.

W każdym przypadku pokazujemy, jak utworzyć zestaw rekordów zawierający pojedynczy rekord. Wcześniejsze przykłady rekordów "A" można dostosować do tworzenia zestawów rekordów innych typów zawierających wiele rekordów, z metadanymi lub do tworzenia pustych zestawów rekordów.

Nie podajemy przykładu, aby utworzyć zestaw rekordów SOA, ponieważ soa są tworzone i usuwane z każdej strefy DNS i nie mogą być tworzone lub usuwane oddzielnie. Jednak [SOA mogą być modyfikowane, jak pokazano w późniejszym przykładzie](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów AAAA z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów CAA z jednym rekordem

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów CNAME z pojedynczym rekordem

> [!NOTE]
> Standardy DNS nie zezwalają na rekordy CNAME`-Name '@'`na szczycie strefy ( ), ani nie zezwalają na zestawy rekordów zawierające więcej niż jeden rekord.
> 
> Aby uzyskać więcej informacji, zobacz [rekordy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów MX z pojedynczym rekordem

W tym przykładzie używamy nazwy\@zestawu rekordów ' do utworzenia rekordu MX w wierzchołku strefy (w tym przypadku "contoso.com").


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów NS z pojedynczym rekordem

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów PTR z pojedynczym rekordem

W takim przypadku "my-arpa-zone.com" reprezentuje strefę wyszukiwania wstecznego ARPA reprezentującą zakres adresów IP. Każdy rekord PTR w tej strefie odnosi się do adresu IP w tym zakresie adresów IP. Nazwa rekordu "10" jest ostatnim oktetem adresu IP w tym zakresie IP reprezentowanym przez ten rekord.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów SRV z pojedynczym rekordem

Podczas tworzenia [zestawu rekordów SRV](dns-zones-records.md#srv-records)należy * \_* określić * \_usługę* i protokół w nazwie zestawu rekordów. Nie ma potrzeby dołączania ' '\@w nazwie zestawu rekordów podczas tworzenia rekordu SRV ustawionego w wierzchołku strefy.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Tworzenie zestawu rekordów TXT z jednym rekordem

W poniższym przykładzie pokazano, jak utworzyć rekord TXT. Aby uzyskać więcej informacji na temat maksymalnej długości ciągu obsługiwanej w rekordach TXT, zobacz [Rekordy TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Ustawianie rekordu

Aby pobrać istniejący zestaw `Get-AzDnsRecordSet`rekordów, użyj programu . To polecenie cmdlet zwraca obiekt lokalny reprezentujący rekord ustawiony w usłudze Azure DNS.

Podobnie `New-AzDnsRecordSet`jak w polu , podana nazwa zestawu rekordów musi być nazwą *względną,* co oznacza, że musi wykluczać nazwę strefy. Należy również określić typ rekordu i strefę zawierającą zestaw rekordów.

W poniższym przykładzie pokazano, jak pobrać zestaw rekordów. W tym przykładzie strefa jest `-ZoneName` `-ResourceGroupName` określona przy użyciu parametrów i.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie można również określić strefę za pomocą `-Zone` obiektu strefy, przekazywane za pomocą parametru.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Lista zestawów rekordów

Można również `Get-AzDnsZone` użyć do listy zestawów rekordów w `-Name` strefie, `-RecordType` pomijając parametry i/lub.

Poniższy przykład zwraca wszystkie zestawy rekordów w strefie:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Poniższy przykład pokazuje, jak można pobrać wszystkie zestawy rekordów danego typu, określając typ rekordu, pomijając nazwę zestawu rekordów:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Aby pobrać wszystkie zestawy rekordów o danej nazwie, w różnych typach rekordów należy pobrać wszystkie zestawy rekordów, a następnie przefiltrować wyniki:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

We wszystkich powyższych przykładach strefę można określić `-ZoneName` `-ResourceGroupName`za pomocą parametrów i (jak pokazano) lub określając obiekt strefy:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Dodawanie rekordu do istniejącego zestawu rekordów

Aby dodać rekord do istniejącego zestawu rekordów, wykonaj następujące trzy kroki:

1. Pobierz istniejący zestaw rekordów

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Dodaj nowy rekord do lokalnego zestawu rekordów. Jest to operacja off-line.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdz zmiany z powrotem do usługi Azure DNS. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Użycie `Set-AzDnsRecordSet` *zastępuje istniejący* zestaw rekordów w usłudze Azure DNS (i wszystkie rekordy, które zawiera) określonym zestawem rekordów. [Kontrole Etag](dns-zones-records.md#etags) są używane w celu zapewnienia, że równoczesne zmiany nie są zastępowane. Można użyć przełącznika opcjonalnego, `-Overwrite` aby pominąć te kontrole.

Ta sekwencja operacji może być również *potokowa,* co oznacza, że obiekt zestawu rekordów jest przekazywał za pomocą potoku, a nie przekazywał go jako parametr:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Powyższe przykłady pokazują, jak dodać rekord "A" do istniejącego zestawu rekordów typu "A". Podobna sekwencja operacji jest używana do dodawania rekordów do `-Ipv4Address` zestawów rekordów innych typów, zastępując parametr innymi `Add-AzDnsRecordConfig` parametrami specyficznymi dla każdego typu rekordu. Parametry dla każdego typu rekordu są `New-AzDnsRecordConfig` takie same jak dla polecenia cmdlet, jak pokazano w przykładach dodatkowego typu rekordu powyżej.

Zestawy rekordów typu "CNAME" lub "SOA" nie mogą zawierać więcej niż jednego rekordu. To ograniczenie wynika ze standardów DNS. Nie jest to ograniczenie usługi Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Usuwanie rekordu z istniejącego zestawu rekordów

Proces usuwania rekordu z zestawu rekordów jest podobny do procesu dodawania rekordu do istniejącego zestawu rekordów:

1. Pobierz istniejący zestaw rekordów

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Usuń rekord z obiektu lokalnego zestawu rekordów. Jest to operacja off-line. Usuwany rekord musi być dokładnie zgodny z istniejącym rekordem we wszystkich parametrach.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Zatwierdz zmiany z powrotem do usługi Azure DNS. Użyj przełącznika opcjonalnego, `-Overwrite` aby pominąć sprawdzanie [Etag](dns-zones-records.md#etags) dla równoczesnych zmian.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Użycie powyższej sekwencji do usunięcia ostatniego rekordu z zestawu rekordów nie powoduje usunięcia zestawu rekordów, a nie pozostawia pustego zestawu rekordów. Aby całkowicie usunąć zestaw rekordów, zobacz [Usuwanie zestawu rekordów](#delete-a-record-set).

Podobnie jak dodawanie rekordów do zestawu rekordów, sekwencja operacji usuwania zestawu rekordów może być również potokowa:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Różne typy rekordów są obsługiwane przez przekazywanie odpowiednich parametrów specyficznych dla typu do `Remove-AzDnsRecordSet`. Parametry dla każdego typu rekordu są `New-AzDnsRecordConfig` takie same jak dla polecenia cmdlet, jak pokazano w przykładach dodatkowego typu rekordu powyżej.


## <a name="modify-an-existing-record-set"></a>Modyfikowanie istniejącego zestawu rekordów

Kroki modyfikowania istniejącego zestawu rekordów są podobne do czynności podejmowanych podczas dodawania lub usuwania rekordów z zestawu rekordów:

1. Pobierz istniejący zestaw rekordów za pomocą programu `Get-AzDnsRecordSet`.
2. Zmodyfikuj obiekt lokalnego zestawu rekordów przez:
    * Dodawanie lub usuwanie rekordów
    * Zmiana parametrów istniejących rekordów
    * Zmiana metadanych zestawu rekordów i czasu na żywo (TTL)
3. Zaobwiduj `Set-AzDnsRecordSet` zmiany za pomocą polecenia cmdlet. Spowoduje to zastąpienie istniejącego zestawu *rekordów* w usłudze Azure DNS określonym zestawem rekordów.

Podczas `Set-AzDnsRecordSet`korzystania , [Etag kontroli](dns-zones-records.md#etags) są używane w celu zapewnienia równoczesnych zmian nie są zastępowane. Można użyć przełącznika opcjonalnego, `-Overwrite` aby pominąć te kontrole.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aby zaktualizować rekord w istniejącym zestawie rekordów

W tym przykładzie zmieniamy adres IP istniejącego rekordu "A":

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Aby zmodyfikować rekord SOA

Nie można dodawać ani usuwać rekordów z automatycznie utworzonego`-Name "@"`rekordu SOA ustawionego w wierzchołku strefy ( , w tym znaków cudzysłowu). Można jednak zmodyfikować dowolny z parametrów w rekordzie SOA (z wyjątkiem "Host") i rekordu ustawionego TTL.

W poniższym przykładzie pokazano, jak zmienić właściwość *Poczta e-mail* rekordu SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Aby zmodyfikować rekordy NS w wierzchołku strefy

Rekord NS ustawiony w wierzchołku strefy jest tworzony automatycznie z każdą strefą DNS. Zawiera nazwy serwerów nazw DNS platformy Azure przypisanych do strefy.

Do tego zestawu rekordów NS można dodać dodatkowe serwery nazw, aby obsługiwać domeny współ hostingowe z więcej niż jednym dostawcą DNS. Można również zmodyfikować czas wygaśnięcia i metadane dla tego zestawu rekordów. Jednak nie można usunąć ani zmodyfikować wstępnie wypełnionych serwerów nazw DNS platformy Azure.

Należy zauważyć, że dotyczy to tylko rekordu NS ustawionego w wierzchołku strefy. Inne zestawy rekordów NS w strefie (używane do delegowania stref podrzędnych) mogą być modyfikowane bez ograniczeń.

W poniższym przykładzie pokazano, jak dodać dodatkowy serwer nazw do rekordu NS ustawionego w wierzchołku strefy:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Aby zmodyfikować metadane zestawu rekordów

[Metadane zestawu rekordów](dns-zones-records.md#tags-and-metadata) mogą służyć do kojarzenia danych specyficznych dla aplikacji z każdym zestawem rekordów jako par klucza-wartości.

W poniższym przykładzie pokazano, jak zmodyfikować metadane istniejącego zestawu rekordów:

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

Zestawy rekordów można usunąć `Remove-AzDnsRecordSet` za pomocą polecenia cmdlet. Usunięcie zestawu rekordów powoduje również usunięcie wszystkich rekordów w zestawie rekordów.

> [!NOTE]
> Nie można usunąć zestawów rekordów SOA i`-Name '@'`NS w wierzchołku strefy ( ).  Usługa Azure DNS utworzyła je automatycznie podczas tworzenia strefy i usuwa je automatycznie po usunięciu strefy.

W poniższym przykładzie pokazano, jak usunąć zestaw rekordów. W tym przykładzie nazwa zestawu rekordów, typ zestawu rekordów, nazwa strefy i grupa zasobów są wyraźnie określone.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternatywnie zestaw rekordów można określić według nazwy i typu, a strefa określona za pomocą obiektu:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Jako trzecia opcja sam zestaw rekordów można określić za pomocą obiektu zestawu rekordów:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Po określeniu zestawu rekordów do usunięcia przy użyciu obiektu zestawu [rekordów, kontrole Etag](dns-zones-records.md#etags) są używane w celu zapewnienia, że równoczesne zmiany nie są usuwane. Można użyć przełącznika opcjonalnego, `-Overwrite` aby pominąć te kontrole.

Obiekt zestawu rekordów może być również potokowany, a nie przekazywany jako parametr:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Monity o potwierdzenie

Polecenia cmdlet `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` i `Remove-AzDnsRecordSet` obsługują monity o potwierdzenie.

Każde polecenie cmdlet monituje `$ConfirmPreference` o potwierdzenie, jeśli zmienna preferencji programu PowerShell ma wartość `Medium` lub niższą. Ponieważ wartością `$ConfirmPreference` domyślną jest `High`, monity te nie są podane podczas korzystania z domyślnych ustawień programu PowerShell.

Bieżące ustawienie `$ConfirmPreference` można zastąpić przy użyciu parametru `-Confirm`. W przypadku wybrania elementów `-Confirm` lub `-Confirm:$True` polecenie cmdlet monituje o potwierdzenie przed uruchomieniem. W przypadku wybrania elementu `-Confirm:$False` polecenie cmdlet nie monituje o potwierdzenie. 

Aby uzyskać więcej informacji na temat elementów `-Confirm` i `$ConfirmPreference`, zobacz [About Preference Variables (Informacje o zmiennych preferencji)](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [strefach i rekordach w usłudze Azure DNS](dns-zones-records.md).
<br>
Dowiedz się, jak [chronić strefy i rekordy](dns-protect-zones-recordsets.md) podczas korzystania z usługi Azure DNS.
<br>
Przejrzyj [dokumentację referencyjną programu Azure DNS PowerShell](/powershell/module/az.dns).
