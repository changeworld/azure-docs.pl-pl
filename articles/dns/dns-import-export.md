---
title: Importowanie i eksportowanie pliku strefy domeny do usługi Azure DNS za pomocą usługi Azure CLI 2.0 | Dokumentacja firmy Microsoft
description: Informacje o sposobie importowania i eksportowania pliku strefy DNS do usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: gwallace
ms.openlocfilehash: 6bb9b6de195eaea1f7c8591d2de47d360ccde488
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-20"></a>Importowanie i eksportowanie pliku strefy DNS przy użyciu 2.0 interfejsu wiersza polecenia platformy Azure 

W tym artykule przedstawiono sposób importowania i eksportowania plików strefy DNS dla usługi Azure DNS za pomocą 2.0 interfejsu wiersza polecenia platformy Azure.

## <a name="introduction-to-dns-zone-migration"></a>Wprowadzenie do migracji strefy DNS

Pliku strefy DNS to plik tekstowy zawierający szczegóły każdego rekordu systemu nazw domen (DNS) w strefie. Wynika z formatem, dzięki czemu odpowiednie do transferu między systemami DNS rekordów DNS. Przy użyciu pliku strefy to szybkie, niezawodne i wygodny sposób transferu strefy DNS do lub z usługi Azure DNS.

Usługa DNS platformy Azure obsługuje importowania i eksportowania plików strefy przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Importowanie pliku strefy jest **nie** obecnie obsługiwane za pośrednictwem programu Azure PowerShell lub w portalu Azure.

Azure CLI 2.0 jest narzędziem wiersza polecenia i platform używanym do zarządzania usługami Azure. Jest dostępna dla platformy systemu Windows, Mac i Linux z [Azure pliki do pobrania](https://azure.microsoft.com/downloads/). Obsługa platform jest ważne w przypadku importowania i eksportowania plików stref, ponieważ najczęściej używane oprogramowanie serwera Nazwa [POWIĄZAĆ](https://www.isc.org/downloads/bind/), zwykle działa w systemie Linux.


## <a name="obtain-your-existing-dns-zone-file"></a>Uzyskaj do istniejącego pliku strefy DNS

Przed zaimportowaniem pliku strefy DNS w usłudze Azure DNS, należy uzyskać kopię pliku strefy. Źródło tego pliku zależy od tego, gdzie jest obecnie hostowany strefy DNS.

* Jeśli strefy DNS jest hostowana przez partnera usługi (na przykład rejestratora domen, dedykowane dostawcy hostingu DNS lub dostawcy usług w chmurze alternatywne), czy usługa powinien dostarczyć możliwość pobierania pliku strefy DNS.
* Jeśli strefy DNS jest obsługiwana w systemie DNS systemu Windows, jest domyślnym folderem plików strefy **%systemroot%\system32\dns**. Pełna ścieżka do pliku każdej strefy przedstawiono również na **ogólne** kartę konsolę DNS.
* Jeżeli strefy DNS znajduje się za pomocą powiązania, lokalizację pliku strefy w każdej strefie jest określony w pliku konfiguracji powiązania **named.conf**.

> [!NOTE]
> Strefy pliki pobierane z GoDaddy ma nieco niestandardowym formacie. Musisz rozwiązać ten problem przed zaimportowaniem plików tych stref w usłudze Azure DNS.
>
> Nazwy DNS w RDATA każdego rekordu DNS są określane jako w pełni kwalifikowane nazwy, ale nie mają kończącym "." Oznacza to, że są interpretowane przez inne systemy DNS jako nazw względnych. Musisz zmodyfikować plik strefy do dołączenia przerywa "." nazwy przed ich zaimportowaniem do usługi Azure DNS.
>
> Na przykład należy zmienić rekord CNAME "www 3600 CNAME contoso.com" na "" www"3600 w domenie contoso.com CNAME.
> (z tokenem ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importowanie pliku strefy DNS do usługi Azure DNS

Importowanie pliku strefy tworzy nową strefę w usłudze Azure DNS, jeśli już nie istnieje. Jeśli strefa już istnieje, zestawów rekordów w pliku strefy muszą zostać połączone z istniejących zestawów rekordów.

### <a name="merge-behavior"></a>Scal zachowanie

* Domyślnie są scalane istniejących i nowych zestawów rekordów. Usuń zduplikowane są identyczne rekordy w zestawie rekordów scalone.
* W przypadku zestawów rekordów scalania, jest używany czas wygaśnięcia (TTL) istniejących zestawów rekordów.
* Początek parametry uwierzytelniania (SOA) (z wyjątkiem `host`) są zawsze pobierana z pliku importowanych stref. Podobnie dla Ustaw w wierzchołku strefy rekord serwera nazw czas wygaśnięcia jest zawsze pobierana z pliku importowanych stref.
* Importowany rekord CNAME nie zastępuje istniejący rekord CNAME o takiej samej nazwie.  
* Gdy wystąpi konflikt między rekord CNAME, a inny rekord o tej samej nazwie, ale innego typu (niezależnie od tego, który jest istniejących lub nowych), jest zachowywana istniejącego rekordu. 

### <a name="additional-information-about-importing"></a>Dodatkowe informacje na temat importowania

Poniższe uwagi znajdują się dodatkowe informacje techniczne na temat strefy procesu importowania.

* `$TTL` Dyrektywa jest opcjonalna i jest obsługiwana. Gdy nie `$TTL` podano dyrektywy, rekordy bez jawnego TTL są importowane ustawioną domyślną TTL 3600 sekund. Gdy dwa rekordy z tego samego zestawu rekordów, określ inną TTLs, niższą wartość jest używana.
* `$ORIGIN` Dyrektywa jest opcjonalna i jest obsługiwana. Gdy nie `$ORIGIN` jest ustawiona, zostanie użyta wartość domyślna nazwa strefy określonego w wierszu polecenia (oraz przerywanie ".").
* `$INCLUDE` i `$GENERATE` dyrektywy nie są obsługiwane.
* Obsługiwane są następujące typy rekordów: A, AAAA, CNAME, MX, NS, SOA, SRV i TXT.
* Rekord SOA jest tworzona automatycznie przez usługę Azure DNS, gdy tworzona jest strefa. Podczas importowania pliku strefy, wszystkie parametry SOA są pobierane z pliku strefy *z wyjątkiem* `host` parametru. Ten parametr używa wartości dostarczone przez usługę Azure DNS. Jest to spowodowane tego parametru musi odwoływać się do serwera podstawowa nazwa podana przez usługę Azure DNS.
* Rekord serwera nazw ustawiony w wierzchołku strefy jest również tworzone automatycznie przez usługę Azure DNS podczas tworzenia strefy. Zostaną zaimportowane tylko TTL tego zestawu rekordów. Te rekordy zawierają nazw serwerów nazw, udostępnionych przez usługę Azure DNS. Rekord danych nie jest zastępowana przez wartości zawarte w pliku strefy zaimportowany.
* W publicznej wersji zapoznawczej usługi DNS platformy Azure obsługuje tylko jeden ciąg rekordów TXT. Wielociągu rekordów TXT jest połączony i obcięte do 255 znaków.

### <a name="cli-format-and-values"></a>Format interfejsu wiersza polecenia i wartości

Format polecenie wiersza polecenia platformy Azure, aby zaimportować strefę DNS jest:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>` to nazwa grupy zasobów dla strefy w usłudze Azure DNS.
* `<zone name>` to nazwa strefy.
* `<zone file name>` jest/nazwa ścieżki pliku strefy do zaimportowania.

Jeśli strefa o tej nazwie nie istnieje w grupie zasobów, jest tworzony automatycznie. Jeśli strefa już istnieje, importowane zestawy rekordów są scalane z istniejących zestawów rekordów. 


### <a name="step-1-import-a-zone-file"></a>Krok 1. Zaimportuj plik strefy

Aby zaimportować plik strefy strefy **contoso.com**.

1. Jeśli nie masz już, należy utworzyć grupę zasobów Menedżera zasobów.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Aby zaimportować strefy **contoso.com** z pliku **contoso.com.txt** do nowej strefy DNS w grupie zasobów **myresourcegroup**, należy uruchomić polecenie `az network dns zone import` .<BR>To polecenie ładuje plik strefy i analizuje go. Polecenie wykonuje szereg poleceń w usłudze Azure DNS, można utworzyć strefy i ustawia wszystkich rekordów w strefie. Polecenie Raporty postęp w oknie konsoli wraz z jakiekolwiek błędy i ostrzeżenia. Ponieważ zestawy rekordów są tworzone w serii, może upłynąć kilka minut, aby zaimportować plik dużej strefy.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Krok 2. Sprawdź strefy

Aby sprawdzić strefę DNS, po zaimportowaniu plików, można użyć jednej z następujących metod:

* Można wyświetlić listę rekordów za pomocą następującego polecenia wiersza polecenia platformy Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Wyświetl listę rekordów za pomocą polecenia cmdlet programu PowerShell `Get-AzureRmDnsRecordSet`.
* Można użyć `nslookup` Aby sprawdzić rozpoznawanie nazw dla rekordów. Ponieważ strefa nie jest jeszcze delegowana, musisz jawnie określić poprawne serwerów nazw usługi Azure DNS. Poniższy przykład pokazuje, jak można pobrać nazw serwerów nazw przypisanych do strefy. To również przedstawia sposób zapytanie dotyczące rekordu "www" za pomocą `nslookup`.
```
C:\>az network dns record-set ns list -g myresourcegroup -z  --output json 
  [
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

        C:\> nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
```

### <a name="step-3-update-dns-delegation"></a>Krok 3. Zaktualizuj Delegowanie DNS

Po upewnieniu się, że strefa zostały zaimportowane prawidłowo, należy zaktualizować Delegowanie DNS, aby wskazywał serwerów nazw usługi Azure DNS. Aby uzyskać więcej informacji, zobacz artykuł [zaktualizowania delegowania DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Eksportowanie pliku strefy DNS z usługi Azure DNS

Format polecenie wiersza polecenia platformy Azure, aby zaimportować strefę DNS jest:

```azurecli
az network dns zone export -g <resource group> -z <zone name> -f <zone file name>
```

Wartości:

* `<resource group>` to nazwa grupy zasobów dla strefy w usłudze Azure DNS.
* `<zone name>` to nazwa strefy.
* `<zone file name>` jest/nazwa ścieżki pliku strefy do wyeksportowania.

Jako importowania strefy najpierw należy się zalogować, wybraniu subskrypcji i Konfigurowanie interfejsu wiersza polecenia Azure do pracy w trybie Menedżera zasobów.

### <a name="to-export-a-zone-file"></a>Aby wyeksportować plik strefy

Aby wyeksportować istniejący strefy DNS platformy Azure **contoso.com** w grupie zasobów **myresourcegroup** do pliku **contoso.com.txt** (w bieżącym folderze), uruchom `azure network dns zone export`. To polecenie wymaga usługa Azure DNS wyliczyć zestawów rekordów w strefie i wyeksportować wyniki do pliku strefy POWIĄZANIE zgodne.

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
