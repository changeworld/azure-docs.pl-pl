---
title: Importowanie i eksportowanie pliku strefy domeny do usługi Azure DNS przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak importować i eksportować plik strefy DNS do usługi Azure DNS przy użyciu wiersza polecenia platformy Azure
services: dns
author: vhorne
ms.service: dns
ms.date: 4/3/2019
ms.author: victorh
ms.openlocfilehash: 25445415141372e1f231549c5b8f8575a89363c6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905413"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importowanie i eksportowanie pliku strefy DNS przy użyciu wiersza polecenia platformy Azure

W tym artykule przedstawiono sposób importowanie i eksportowanie plików strefy DNS dla usługi Azure DNS przy użyciu wiersza polecenia platformy Azure.

## <a name="introduction-to-dns-zone-migration"></a>Wprowadzenie do migracji strefy DNS

Plik strefy DNS jest plik tekstowy, który zawiera szczegóły każdego rekordu systemu nazw domen (DNS, Domain Name System) w strefie. Jest zgodna z formatem, ułatwiając nadające się do przenoszenia rekordy DNS między systemem DNS. Przy użyciu pliku strefy jest szybkie, niezawodne i wygodny sposób transferu strefy DNS do lub z usługi Azure DNS.

Usługa DNS platformy Azure obsługuje importowanie i eksportowanie plików strefy przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure. Importowanie pliku strefy **nie** obecnie są obsługiwane za pośrednictwem programu Azure PowerShell lub witryny Azure portal.

Interfejs wiersza polecenia platformy Azure to Międzyplatformowe narzędzie wiersza polecenia używane do zarządzania usługami platformy Azure. Jest ona dostępna dla platformy Windows, Mac i Linux z [strony plików do pobrania w usłudze Azure](https://azure.microsoft.com/downloads/). Obsługa wielu platform jest ważne w przypadku importowanie i eksportowanie plików strefy, ponieważ najbardziej typowe oprogramowanie serwera nazw [POWIĄZAĆ](https://www.isc.org/downloads/bind/), zwykle działa w systemie Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Uzyskaj do istniejącego pliku strefy DNS

Przed zaimportowaniem pliku strefy DNS w usłudze Azure DNS, należy uzyskać kopię pliku strefy. Źródłu tego pliku zależy od tego, gdzie jest obecnie hostowany strefy DNS.

* Jeśli swoją strefę DNS jest hostowana przez partnera usługi (np. Rejestrator domen, dedykowanych dostawcy hostingu DNS lub dostawcy usług w chmurze alternatywne), usługi należy podać możliwość pobrania pliku strefy DNS.
* Jeśli swoją strefę DNS znajduje się na Windows DNS, domyślny folder plików strefy jest **%systemroot%\system32\dns**. Pełna ścieżka do każdego pliku strefy również zawiera **ogólne** kartę konsolę DNS.
* Jeśli swoją strefę DNS znajduje się za pomocą BIND, lokalizację pliku strefy w każdej strefie jest określona w pliku konfiguracji powiązania **named.conf**.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importowanie pliku strefy DNS do usługi Azure DNS

Importowanie pliku strefy tworzy nową strefę w usłudze Azure DNS, jeśli już nie istnieje. Jeśli istnieje już strefa, zestawów rekordów w pliku strefy muszą zostać połączone z istniejących zestawów rekordów.

### <a name="merge-behavior"></a>Scal zachowanie

* Domyślnie istniejących i nowych zestawów rekordów są scalane. Usuń zduplikowane są identyczne rekordy w zestawie rekordów scalone.
* W przypadku scalania zestawów rekordów służy czas wygaśnięcia (TTL) z istniejących zestawów rekordów.
* Początek parametry uwierzytelniania (SOA) (z wyjątkiem `host`) są zawsze pobierane z pliku importowanych strefy. Podobnie dla rekordu serwera nazw, ustaw w wierzchołku strefy, czasem wygaśnięcia zawsze pochodzi z pliku importowanych strefy.
* Importowany rekord CNAME nie zastępuje istniejący rekord CNAME o takiej samej nazwie.  
* Gdy wystąpi konflikt między rekordu CNAME i innego rekordu o takiej samej nazwie, ale innego typu (niezależnie od tego, który jest istniejące lub nowe), istniejący rekord jest zachowywana. 

### <a name="additional-information-about-importing"></a>Dodatkowe informacje na temat importowania

Poniższe informacje o zawierają dodatkowe informacje techniczne na temat strefy procesu importowania.

* `$TTL` Dyrektywa jest opcjonalna i jest obsługiwana. Gdy nie `$TTL` podano dyrektywy, rekordy bez jawnego czas wygaśnięcia, które są importowane ustawiono jako domyślny czas wygaśnięcia 3600 sekund. Gdy dwa rekordy, w tym samym zestawie rekordów określić różnych czasów wygaśnięcia, niższa wartość jest używana.
* `$ORIGIN` Dyrektywa jest opcjonalna i jest obsługiwana. Gdy nie `$ORIGIN` jest ustawiona wartość domyślna używana jest nazwa strefy, jak określono w wierszu polecenia (plus przerywa ".").
* `$INCLUDE` i `$GENERATE` dyrektywy nie są obsługiwane.
* Obsługiwane są następujące typy rekordów: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV i TXT.
* Rekord SOA jest tworzone automatycznie przez usługę Azure DNS, gdy tworzona jest strefa. Podczas importowania pliku strefy wszystkie parametry SOA są pobierane z pliku strefy *z wyjątkiem* `host` parametru. Ten parametr używa wartości dostarczone przez usługę Azure DNS. Jest to spowodowane ten parametr musi odwoływać się do serwera nazwy podstawowej, dostarczone przez usługę Azure DNS.
* Rekord serwera nazw, ustaw w wierzchołku strefy jest tworzona automatycznie przez usługę Azure DNS podczas tworzenia strefy. Tylko czas wygaśnięcia to zestaw rekordów jest importowany. Te rekordy zawierają nazwy serwerów nazw, dostarczone przez usługę Azure DNS. Rekord danych nie zostanie zastąpiona wartości zawarte w pliku importowanych strefy.
* Publicznej wersji zapoznawczej usługi Azure DNS obsługuje tylko rekordy TXT pojedynczego ciągu. Wielociągu rekordy TXT, które są połączone się obcięte do 255 znaków.

### <a name="cli-format-and-values"></a>Format interfejsu wiersza polecenia i wartości

Format polecenie wiersza polecenia platformy Azure, aby zaimportować strefy DNS jest:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>` jest nazwą grupy zasobów dla strefy w usłudze Azure DNS.
* `<zone name>` jest nazwą strefy.
* `<zone file name>` jest ścieżka/nazwa pliku strefy do zaimportowania.

Strefy o tej nazwie nie istnieje w grupie zasobów, zostanie utworzony automatycznie. Jeśli istnieje już strefa, zaimportowane zestawy rekordów są scalane z istniejących zestawów rekordów. 

### <a name="step-1-import-a-zone-file"></a>Krok 1. Importuj plik strefy

Aby zaimportować plik strefy do strefy **contoso.com**.

1. Jeśli nie masz jeszcze, należy utworzyć grupę zasobów usługi Resource Manager.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Aby zaimportować strefy **contoso.com** z pliku **contoso.com.txt** do nowej strefy DNS w grupie zasobów **myresourcegroup**, spowoduje uruchomienie polecenia `az network dns zone import` .<BR>To polecenie ładuje plik strefy i analizuje go. Polecenie wykonuje serię poleceń w usłudze DNS platformy Azure można utworzyć strefy i zestawy wszystkich rekordów w strefie. Polecenie Raporty postęp w oknie konsoli oraz wszelkie błędy lub ostrzeżenia. Ponieważ zestawy rekordów są tworzone w serii, może upłynąć kilka minut, aby zaimportować plik dużej strefy.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Krok 2. Sprawdź strefy

Aby sprawdzić strefy DNS, po zaimportowaniu pliku, można użyć jednej z następujących metod:

* Można wyświetlić listę rekordów za pomocą następującego polecenia wiersza polecenia platformy Azure:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* Możesz wyświetlić listę rekordów za pomocą polecenia interfejsu wiersza polecenia Azure `az network dns record-set ns list`.
* Możesz użyć `nslookup` można sprawdzić rozpoznawanie nazw dla rekordów. Ponieważ strefa nie jest jeszcze delegować, musisz jawnie określić poprawne serwery nazw usługi Azure DNS. Poniższy przykład pokazuje, jak można pobrać nazw serwerów nazw przypisanych do strefy. To także pokazuje, jak wykonywać zapytania rekordów "www" za pomocą `nslookup`.

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

### <a name="step-3-update-dns-delegation"></a>Krok 3. Zaktualizuj Delegowanie DNS

Po upewnieniu się, że strefa zostały zaimportowane prawidłowo, należy zaktualizować delegowania usługi DNS, aby wskazywał serwerów nazw usługi Azure DNS. Aby uzyskać więcej informacji, zobacz artykuł [zaktualizowania delegowania DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Eksportowanie pliku strefy DNS w usłudze Azure DNS

Format polecenie wiersza polecenia platformy Azure, aby zaimportować strefy DNS jest:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Wartości:

* `<resource group>` jest nazwą grupy zasobów dla strefy w usłudze Azure DNS.
* `<zone name>` jest nazwą strefy.
* `<zone file name>` jest ścieżka/nazwa pliku strefy do wyeksportowania.

Jako importowanie strefy można najpierw należy do logowania, wybierz subskrypcję i Konfigurowanie interfejsu wiersza polecenia platformy Azure do używania trybu usługi Resource Manager.

### <a name="to-export-a-zone-file"></a>Aby eksportować plik strefy

Aby wyeksportować istniejącej strefy DNS platformy Azure **contoso.com** w grupie zasobów **myresourcegroup** do pliku **contoso.com.txt** (w bieżącym folderze), uruchom `azure network dns zone export`. To polecenie wymaga usługi Azure DNS, aby wyliczyć zestawów rekordów w strefie i wyeksportować wyniki do pliku strefy zgodny z BIND.

```
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [Zarządzanie zestawy rekordów i rekordy](dns-getstarted-create-recordset-cli.md) w strefie DNS.

* Dowiedz się, jak [delegować domenę do usługi Azure DNS](dns-domain-delegation.md).
