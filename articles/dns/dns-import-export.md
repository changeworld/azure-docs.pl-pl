---
title: Importowanie i eksportowanie pliku strefy domeny — narzędzie interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure DNS
description: Dowiedz się, jak importować i eksportować plik strefy DNS do usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure
services: dns
author: rohinkoul
ms.service: dns
ms.date: 4/3/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: a5c2fdde564eba2d95e7f14f4d47e4d381739d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365172"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importowanie i eksportowanie pliku strefy systemu DNS za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule opisano, jak importować i eksportować pliki stref DNS dla usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="introduction-to-dns-zone-migration"></a>Wprowadzenie do migracji strefy DNS

Plik strefy DNS to plik tekstowy zawierający szczegóły każdego rekordu dns (Domain Name System) w strefie. Jest zgodny ze standardowym formatem, dzięki czemu nadaje się do przesyłania rekordów DNS między systemami DNS. Korzystanie z pliku strefy jest szybkim, niezawodnym i wygodnym sposobem przenoszenia strefy DNS do lub z usługi Azure DNS.

Usługa Azure DNS obsługuje importowanie i eksportowanie plików stref przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Importowanie plików strefy **nie** jest obecnie obsługiwane za pośrednictwem programu Azure PowerShell ani witryny Azure portal.

Narzędzie cli platformy Azure to wieloplatformowe narzędzie wiersza polecenia używane do zarządzania usługami platformy Azure. Jest on dostępny dla platform Windows, Mac i Linux ze [strony pobierania platformy Azure](https://azure.microsoft.com/downloads/). Obsługa wielu platform jest ważna dla importowania i eksportowania plików stref, ponieważ najczęściej używane oprogramowanie serwera nazw, [BIND,](https://www.isc.org/downloads/bind/)zazwyczaj działa na linuksie.

## <a name="obtain-your-existing-dns-zone-file"></a>Uzyskiwanie istniejącego pliku strefy DNS

Przed zaimportowania pliku strefy DNS do usługi Azure DNS należy uzyskać kopię pliku strefy. Źródło tego pliku zależy od tego, gdzie jest aktualnie hostowana strefa DNS.

* Jeśli strefa DNS jest obsługiwana przez usługę partnera (na przykład rejestratora domen, dedykowanego dostawcy hostingu DNS lub alternatywnego dostawcy chmury), usługa ta powinna umożliwiać pobranie pliku strefy DNS.
* Jeśli strefa DNS jest hostowana w systemie Windows DNS, domyślnym folderem dla plików strefy jest **%systemroot%\system32\dns**. Pełna ścieżka do każdego pliku strefy jest również wyświetlana na karcie **Ogólne** konsoli DNS.
* Jeśli strefa DNS jest obsługiwana przy użyciu funkcji BIND, lokalizacja pliku strefy dla każdej strefy jest określona w pliku konfiguracyjnym BIND **o nazwie.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importowanie pliku strefy DNS do usługi Azure DNS

Importowanie pliku strefy tworzy nową strefę w usłudze Azure DNS, jeśli jeszcze nie istnieje. Jeśli strefa już istnieje, zestawy rekordów w pliku strefy muszą zostać scalone z istniejącymi zestawami rekordów.

### <a name="merge-behavior"></a>Zachowanie scalania

* Domyślnie istniejące i nowe zestawy rekordów są scalane. Identyczne rekordy w scalonym zestawie rekordów są usuwane z duplikowanych.
* Gdy zestawy rekordów są scalane, używany jest czas wygaśnięcia (TTL) istniejących wcześniej zestawów rekordów.
* Parametry startu urzędu (SOA) (z wyjątkiem) `host`są zawsze pobierane z importowanego pliku strefy. Podobnie w przypadku rekordu serwera nazw ustawionego w wierzchołku strefy czas wygaśnięcia jest zawsze pobierany z importowanego pliku strefy.
* Zaimportowany rekord CNAME nie zastępuje istniejącego rekordu CNAME o tej samej nazwie.  
* Gdy powstaje konflikt między rekordem CNAME a innym rekordem o tej samej nazwie, ale innego typu (niezależnie od tego, który jest istniejący lub nowy), istniejący rekord jest zachowywany. 

### <a name="additional-information-about-importing"></a>Dodatkowe informacje o imporcie

Poniższe uwagi zawierają dodatkowe szczegóły techniczne dotyczące procesu importowania strefy.

* Dyrektywa `$TTL` jest opcjonalna i jest obsługiwana. Gdy `$TTL` nie podano dyrektywy, rekordy bez jawnego czasu wygaśnięcia są importowane ustawione na domyślny czas wygaśnięcia 3600 sekund. Gdy dwa rekordy w tym samym zestawie rekordów określają różne listy TTLs, używana jest niższa wartość.
* Dyrektywa `$ORIGIN` jest opcjonalna i jest obsługiwana. Gdy `$ORIGIN` nie jest ustawiona, używana wartość domyślna to nazwa strefy określona w wierszu polecenia (plus kończąca się wartość ".").
* Dyrektywy `$INCLUDE` `$GENERATE` i dyrektywy nie są obsługiwane.
* Te typy rekordów są obsługiwane: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV i TXT.
* Rekord SOA jest tworzony automatycznie przez usługę Azure DNS podczas tworzenia strefy. Podczas importowania pliku strefy wszystkie parametry SOA są pobierane z pliku strefy *z wyjątkiem* parametru. `host` Ten parametr używa wartości dostarczonej przez usługę Azure DNS. Dzieje się tak, ponieważ ten parametr musi odwoływać się do serwera nazw podstawowych dostarczonych przez usługę Azure DNS.
* Rekord serwera nazw ustawiony w wierzchołku strefy jest również tworzony automatycznie przez usługę Azure DNS podczas tworzenia strefy. Importowany jest tylko czas wygaśnięcia tego zestawu rekordów. Te rekordy zawierają nazwy serwerów nazw dostarczane przez usługę Azure DNS. Dane rekordu nie są zastępowane przez wartości zawarte w importowanym pliku strefy.
* Podczas publicznej wersji zapoznawczej usługa Azure DNS obsługuje tylko jednostrużowe rekordy TXT. Wieloskrążne rekordy TXT są łączone i obcinane do 255 znaków.

### <a name="cli-format-and-values"></a>Format i wartości interfejsu wiersza polecenia

Format polecenia interfejsu wiersza polecenia platformy Azure do zaimportowania strefy DNS jest:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>`to nazwa grupy zasobów dla strefy w usłudze Azure DNS.
* `<zone name>`to nazwa strefy.
* `<zone file name>`jest ścieżką/nazwą pliku strefy, który ma zostać zaimportowany.

Jeśli strefa o tej nazwie nie istnieje w grupie zasobów, jest tworzona dla Ciebie. Jeśli strefa już istnieje, importowane zestawy rekordów są scalane z istniejącymi zestawami rekordów. 

### <a name="step-1-import-a-zone-file"></a>Krok 1. Importowanie pliku strefy

Aby zaimportować plik strefy dla **strefy contoso.com**.

1. Jeśli jeszcze go nie masz, musisz utworzyć grupę zasobów Menedżera zasobów.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Aby zaimportować strefę **contoso.com** z pliku **contoso.com.txt** do nowej strefy DNS w grupie zasobów `az network dns zone import` **myresourcegroup**, zostanie uruchomione polecenie .<BR>To polecenie ładuje plik strefy i analizuje go. Polecenie wykonuje serię poleceń w usłudze Azure DNS, aby utworzyć strefę i wszystkie zestawy rekordów w strefie. Polecenie raportuje postęp w oknie konsoli, wraz z wszelkimi błędami lub ostrzeżeniami. Ponieważ zestawy rekordów są tworzone w serii, importowanie dużego pliku strefy może potrwać kilka minut.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Krok 2. Sprawdź strefę

Aby zweryfikować strefę DNS po zaimportowaniu pliku, można użyć jednej z następujących metod:

* Rekordy można wyświetlić przy użyciu następującego polecenia interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Rekordy można wyświetlić przy użyciu `az network dns record-set ns list`polecenia interfejsu wiersza polecenia platformy Azure .
* Można użyć `nslookup` do zweryfikowania rozpoznawania nazw rekordów. Ponieważ strefa nie jest jeszcze delegowana, należy określić poprawne serwery nazw DNS platformy Azure jawnie. W poniższym przykładzie pokazano, jak pobrać nazwy serwerów nazw przypisanych do strefy. Pokazuje to również, jak zbadać rekord `nslookup`"www" za pomocą programu .

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>Krok 3. Aktualizowanie delegowania DNS

Po sprawdzeniu, że strefa została zaimportowana poprawnie, należy zaktualizować delegowanie DNS, aby wskazać serwery nazw DNS platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł [Aktualizowanie delegowania DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Eksportowanie pliku strefy DNS z usługi Azure DNS

Format polecenia interfejsu wiersza polecenia platformy Azure do eksportowania strefy DNS jest:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>`to nazwa grupy zasobów dla strefy w usłudze Azure DNS.
* `<zone name>`to nazwa strefy.
* `<zone file name>`jest ścieżką/nazwą pliku strefy, który ma zostać wyeksportowany.

Podobnie jak w przypadku importu strefy, najpierw musisz się zalogować, wybrać subskrypcję i skonfigurować interfejsu wiersza polecenia platformy Azure, aby używać trybu Menedżera zasobów.

### <a name="to-export-a-zone-file"></a>Aby wyeksportować plik strefy

Aby wyeksportować istniejącą strefę DNS platformy Azure **contoso.com** w grupie zasobów **myresourcegroup** do `azure network dns zone export`pliku **contoso.com.txt** (w bieżącym folderze), uruchom program . To polecenie wywołuje usługę Azure DNS, aby wyliczyć zestawy rekordów w strefie i wyeksportować wyniki do pliku strefy zgodnego z bindem.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zarządzać zestawami rekordów i rekordami](dns-getstarted-create-recordset-cli.md) w strefie DNS.

* Dowiedz się, jak [delegować domenę do usługi Azure DNS](dns-domain-delegation.md).
