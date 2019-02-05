---
title: Wystąpienie usługi Azure Metadata Service | Dokumentacja firmy Microsoft
description: Interfejsu rESTful, aby uzyskać informacje dotyczące systemu Linux VM obliczeniowych, sieci i zdarzeń o zbliżającej się konserwacji.
services: virtual-machines-linux
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: e05e26c944dac2fb18508e73c6d35c6875d26548
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729442"
---
# <a name="azure-instance-metadata-service"></a>Usługa Azure Instance Metadata service


Azure Instance Metadata Service dostarcza informacji o uruchomionych wystąpień maszyn wirtualnych, które może służyć do zarządzania i konfigurowania maszyn wirtualnych.
Obejmuje to informacje, takie jak SKU, konfiguracja sieci i zdarzenia zbliżającej się konserwacji. Aby uzyskać więcej informacji na temat typu informacji, które jest dostępne, zobacz [kategorie metadanych](#instance-metadata-data-categories).

Azure Instance Metadata Service jest dostępna dla maszyn wirtualnych IaaS utworzone za pośrednictwem punktu końcowego REST [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Punkt końcowy jest dostępny w dobrze znanego adresu IP bez obsługi routingu (`169.254.169.254`), są dostępne tylko z poziomu maszyny Wirtualnej.

> [!IMPORTANT]
> Ta usługa jest **jest ogólnie dostępna** w regionach platformy Azure.  Będzie ona otrzymywać regularne aktualizacje do udostępnienia nowych informacji o wystąpieniach maszyn wirtualnych. Ta strona zawiera aktualne [kategorii danych](#instance-metadata-data-categories) dostępne.

## <a name="service-availability"></a>Dostępność usług
Usługa jest dostępna w regionach platformy Azure jest ogólnie dostępna. Nie wszystkie wersji interfejsu API mogą być dostępne we wszystkich regionach platformy Azure.

Regiony                                        | Dostępność?                                 | Obsługiwane wersje
-----------------------------------------------|-----------------------------------------------|-----------------
[Wszystkie ogólnie dostępne globalnych regionów platformy Azure](https://azure.microsoft.com/regions/)     | Ogólnie dostępne   | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02
[Platforma Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Chińska wersja platformy Azure](https://www.azure.cn/)                                                           | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01

Ta tabela jest aktualizowany, gdy są dostępne aktualizacje usługi i/lub nowych wersji są dostępne

Aby wypróbować Instance Metadata Service, Utwórz Maszynę wirtualną z [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) lub [witryny Azure portal](http://portal.azure.com) w regionach powyżej i postępuj zgodnie z poniższych przykładów.

## <a name="usage"></a>Sposób użycia

### <a name="versioning"></a>Obsługa wersji
Instance Metadata Service jest wersjonowany. Wersje są obowiązkowe, a bieżąca wersja na platformie Azure globalne to `2018-04-02`. Bieżący obsługiwane wersje to (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02)

> [!NOTE] 
> Poprzednich wersjach zapoznawczych zaplanowanych zdarzeń {najnowsza wersja} są obsługiwane jako parametru api-version. Ten format nie jest już obsługiwane i zostaną wycofane w przyszłości.

Miarę dodawania nowszych wersji starsze wersje nadal może zostać oceniony zgodności, jeśli skrypty mają zależności dotyczących formatów danych specyficznych dla. Jednak nie mogą być dostępne poprzedniej wersji zapoznawczej (2017-03-01), gdy usługa stanie się ogólnie dostępna.

### <a name="using-headers"></a>Korzystanie z nagłówków
Kiedy wykonujesz zapytanie o Instance Metadata Service, musisz podać nagłówek `Metadata: true` aby upewnić się, żądanie nie zostało przekierowane przypadkowo.

### <a name="retrieving-metadata"></a>Pobieranie metadanych

Wystąpienie metadanych jest dostępna do uruchamiania maszyn wirtualnych tworzone lub zarządzane przy użyciu [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Dostęp do wszystkich kategorii danych dla wystąpienia maszyny wirtualnej przy użyciu następującego żądania:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Wszystkie wystąpienia metadanych zapytania jest rozróżniana wielkość liter.

### <a name="data-output"></a>Dane wyjściowe
Domyślnie Instance Metadata Service zwraca dane w formacie JSON (`Content-Type: application/json`). Jednak różne interfejsy API zwracają dane w różnych formatach, jeśli jest to wymagane.
Poniższa tabela jest odwołaniem do innych formatów danych, które mogą obsługiwać interfejsy API.

Interfejs API | Domyślny Format danych | W innych formatach
--------|---------------------|--------------
/instance | json | tekst
/scheduledevents | json | brak

Aby uzyskać dostęp, format odpowiedzi innych niż domyślne, do określania żądanego jako parametr querystring w żądaniu. Na przykład:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Bezpieczeństwo
Instance Metadata Service punkt końcowy jest dostępny tylko w obrębie uruchomionego wystąpienia maszyny wirtualnej bez obsługi routingu adresu IP. Ponadto, żądanie przy użyciu `X-Forwarded-For` nagłówek zostanie odrzucone przez usługę.
Żądania musi zawierać `Metadata: true` nagłówka, aby upewnić się, że rzeczywistego żądania bezpośrednio był zamierzony i nie jest częścią niezamierzone przekierowania. 

### <a name="error"></a>Błąd
Jeśli istnieje, nie znaleziono elementu danych lub źle sformułowane żądanie, Instance Metadata Service zwraca standardowy błędy HTTP. Na przykład:

Kod stanu HTTP | Przyczyna
----------------|-------
200 OK |
400 Niewłaściwe żądanie | Brak `Metadata: true` nagłówka
404 — Nie odnaleziono | Żądany element nie istnieje. 
Metoda 405 nie jest dozwolona | Tylko `GET` i `POST` żądania są obsługiwane
429 zbyt wiele żądań | Interfejs API obsługuje obecnie maksymalnie 5 zapytań na sekundę
Błąd usługi 500     | Spróbuj ponownie po pewnym czasie

### <a name="examples"></a>Przykłady

> [!NOTE] 
> Wszystkie odpowiedzi interfejsu API są ciągami formatu JSON. Wszystkie następujące przykładowe odpowiedzi są drukowane pretty dla czytelności.

#### <a name="retrieving-network-information"></a>Trwa pobieranie informacji o sieci

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>Trwa pobieranie publiczny adres IP

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Pobieranie wszystkich metadanych dla wystąpienia

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-12-01"
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

```json
{
  "compute": {
    "location": "westus",
    "name": "avset2",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "placementGroupId": "",
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "publisher": "Canonical",
    "resourceGroupName": "myrg",
    "sku": "16.04-LTS",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "",
    "version": "16.04.201708030",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_D1",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Pobieranie metadanych na maszynie wirtualnej Windows

**Żądanie**

Można pobrać metadanych wystąpienia w Windows za pośrednictwem narzędzia PowerShell `curl`: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Lub za pomocą `Invoke-RestMethod` polecenia cmdlet:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

```json
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>Wystąpienie metadanych danych kategorii
Następujące kategorie danych są dostępne za pośrednictwem Instance Metadata Service:

Dane | Opis | Wprowadzona w wersji 
-----|-------------|-----------------------
location | Region platformy Azure maszyna wirtualna jest uruchomiona w | 2017-04-02 
name | Nazwa maszyny Wirtualnej | 2017-04-02
oferty | Oferuje informacje dotyczące obrazu maszyny Wirtualnej. Ta wartość dotyczy tylko obrazy wdrożone z galerii obrazów systemu Azure. | 2017-04-02
Wydawcy | Wydawca obrazu maszyny Wirtualnej | 2017-04-02
jednostka SKU | Określone jednostki SKU dla obrazu maszyny Wirtualnej | 2017-04-02
version | Wersja obrazu maszyny Wirtualnej | 2017-04-02
osType | System Linux lub Windows | 2017-04-02
platformUpdateDomain |  [Domena aktualizacji](manage-availability.md) maszyna wirtualna jest uruchomiona | 2017-04-02
platformFaultDomain | [Domena błędów](manage-availability.md) maszyna wirtualna jest uruchomiona | 2017-04-02
vmId | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny Wirtualnej | 2017-04-02
vmSize | [Rozmiar maszyny wirtualnej](sizes.md) | 2017-04-02
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
tags | [Tagi](../../azure-resource-manager/resource-group-using-tags.md) dla maszyny wirtualnej  | 2017-08-01
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/resource-group-overview.md) dla maszyny wirtualnej | 2017-08-01
placementGroupId | [Grupy umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych | 2017-08-01
Plan | [Planowanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) dla maszyny Wirtualnej w niej jest obraz z witryny Marketplace usługi Azure, zawiera nazwę, produktu i wydawcy | 2018-04-02
publicKeys | Kolekcja kluczy publicznych [https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey] przypisane do maszyny Wirtualnej i ścieżek | 2018-04-02
vmScaleSetName | [Nazwa zestawu skalowania maszyny wirtualnej](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zestawu skalowania maszyn wirtualnych | 2017-12-01
strefa | [Strefa dostępności](../../availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01 
ipv4/privateIpAddress | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
ipv4/publicIpAddress | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
subnet/address | Adres podsieci maszyny wirtualnej | 2017-04-02 
podsieci/prefiks | Prefiks podsieci, przykład 24 | 2017-04-02 
ipv6/ipAddress | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02 
macAddress | Adres mac dla maszyny Wirtualnej | 2017-04-02 
scheduledevents | Zobacz [zaplanowane zdarzenia](scheduled-events.md) | 2017-08-01
identity | (Wersja zapoznawcza) Zarządzanych tożsamości dla zasobów platformy Azure. Zobacz [uzyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01 

## <a name="example-scenarios-for-usage"></a>Przykładowe scenariusze użycia  

### <a name="tracking-vm-running-on-azure"></a>Śledzenie maszyn wirtualnych działających na platformie Azure

Jako dostawca usług mogą wymagać, aby śledzić liczbę maszyn wirtualnych z oprogramowaniem lub agenci, którzy muszą śledzić unikatowości maszyny wirtualnej. Aby można było uzyskać unikatowego Identyfikatora dla maszyny Wirtualnej, należy użyć `vmId` pola z Instance Metadata Service.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umieszczanie kontenerów, domeny błędów/aktualizacji na podstawie partycji danych 

W przypadku niektórych scenariuszy rozmieszczenie replik różnych danych znaczenie ma. Na przykład [umieszczania repliki systemu plików HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) lub położenia kontenera za pomocą [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać wiedzieć `platformFaultDomain` i `platformUpdateDomain` maszyna wirtualna jest uruchomiona.
Można również korzystać z [strefy dostępności](../../availability-zones/az-overview.md) wystąpień podejmowanie tych decyzji. Można tworzyć zapytania te dane bezpośrednio za pośrednictwem Instance Metadata Service.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Odpowiedź**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Uzyskiwanie dodatkowych informacji o maszynie Wirtualnej podczas zgłoszenia do pomocy technicznej 

Jako dostawca usług może zostać pomocy technicznej gdzie chcesz wiedzieć więcej informacji na temat maszyny Wirtualnej. Pytania klientów do udostępniania metadanych obliczeń może zapewnić podstawowe informacje dotyczące pomocy technicznej wiedzieć o rodzaju maszyny Wirtualnej na platformie Azure. 

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

```json
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Wprowadzenie środowisko platformy Azure, w którym maszyna wirtualna jest uruchomiona 

Platforma Azure oferuje różne chmur suwerennych, takich jak [Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/). Czasami konieczne jest środowisko platformy Azure do podejmowania decyzji dotyczących niektórych środowiska uruchomieniowego. Poniższy przykład pokazuje, jak można to osiągnąć.

**Żądanie**

> [!NOTE] 
> Wymaga jq do zainstalowania. 

```bash
  metadata=$(curl "http://169.254.169.254/metadata/instance/compute?api-version=2018-02-01" -H "Metadata:true")
  endpoints=$(curl "https://management.azure.com/metadata/endpoints?api-version=2017-12-01")
 
  location=$(echo $metadata | jq .location -r)
 
  is_ww=$(echo $endpoints | jq '.cloudEndpoint.public.locations[]' -r | grep -w $location)
  is_us=$(echo $endpoints | jq '.cloudEndpoint.usGovCloud.locations[]' -r | grep -w $location)
  is_cn=$(echo $endpoints | jq '.cloudEndpoint.chinaCloud.locations[]' -r | grep -w $location)
  is_de=$(echo $endpoints | jq '.cloudEndpoint.germanCloud.locations[]' -r | grep -w $location)
 
  environment="Unknown"
  if [ ! -z $is_ww ]; then environment="AzureCloud"; fi
  if [ ! -z $is_us ]; then environment="AzureUSGovernment"; fi
  if [ ! -z $is_cn ]; then environment="AzureChinaCloud"; fi
  if [ ! -z $is_de ]; then environment="AzureGermanCloud"; fi
 
  echo $environment
```

### <a name="failover-clustering-in-windows-server"></a>Klastrze trybu failover w systemie Windows Server

W przypadku niektórych scenariuszy, podczas wykonywania zapytań dotyczących Instance Metadata Service przy użyciu klastra trybu Failover jest niezbędne dodać trasę do tabeli routingu.

1. Otwórz wiersz polecenia z uprawnieniami administratora.

2. Uruchom następujące polecenie i zanotuj adres dla interfejsu sieci docelowej (`0.0.0.0`) w tabeli tras IPv4.

```bat
route print
```

> [!NOTE] 
> Następujące przykładowe dane wyjściowe z maszyny Wirtualnej z systemu Windows Server przy użyciu klastra trybu Failover włączono zawiera tylko IPv4 tabeli tras dla uproszczenia.

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

3. Uruchom następujące polecenie i użyj adresu interfejsu dla lokalizacji docelowej sieci (`0.0.0.0`) czyli (`10.0.1.10`) w tym przykładzie.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Przykłady wywoływania usługi metadanych przy użyciu różnych języków, wewnątrz maszyny Wirtualnej 

Język | Przykład 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Przejdź  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata
    

## <a name="faq"></a>Często zadawane pytania
1. Otrzymuję błąd `400 Bad Request, Required metadata header not specified`. Co to oznacza?
   * Instance Metadata Service wymaga nagłówka `Metadata: true` do przekazania w żądaniu. Przekazanie tego pliku nagłówkowego w wywołaniu REST umożliwia dostęp do Instance Metadata Service. 
2. Dlaczego nie występują obliczeniowe informacji dla mojej maszyny Wirtualnej?
   * Obecnie Instance Metadata Service obsługuje tylko wystąpienia utworzone za pomocą usługi Azure Resource Manager. W przyszłości pomoc techniczna dla maszyn wirtualnych usługi w chmurze, które mogą być dodawane.
3. Czas ponownie utworzony mojej maszyny wirtualnej za pomocą usługi Azure Resource Manager. Dlaczego mogę nie Zobacz compute informacji o metadanych?
   * W przypadku maszyn wirtualnych utworzonych po września 2016 roku, dodać [Tag](../../azure-resource-manager/resource-group-using-tags.md) do wykonywanych obliczeń metadanych. Starsze maszyn wirtualnych (utworzonych przed 2016 r. aplikacja Sep) dodawać i usuwać dyski rozszerzenia lub danych do maszyny Wirtualnej, aby odświeżyć metadane.
4. Nie widzę wszystkich danych wypełnione dla nowej wersji
   * W przypadku maszyn wirtualnych utworzonych po września 2016 roku, dodać [Tag](../../azure-resource-manager/resource-group-using-tags.md) do wykonywanych obliczeń metadanych. Starsze maszyn wirtualnych (utworzonych przed 2016 r. aplikacja Sep) dodawać i usuwać dyski rozszerzenia lub danych do maszyny Wirtualnej, aby odświeżyć metadane.
5. Dlaczego otrzymuję błąd `500 Internal Server Error`?
   * Ponowić swoje żądanie, w oparciu o wykładniczego wycofywania systemu. Jeśli problem będzie się powtarzał skontaktuj się z działem pomocy technicznej platformy Azure.
6. Gdzie udostępnianie dodatkowych pytań/komentarzy?
   * Wyślij swoje komentarze dotyczące http://feedback.azure.com.
7. To będzie działać dla wystąpienia zestawu skalowania maszyn wirtualnych?
   * Tak Metadata service jest dostępna dla wystąpień zestawu skalowania. 
8. Jak uzyskać pomoc techniczną dla usługi?
   * Aby uzyskać pomoc techniczną dla usługi, utworzyć problemu wymagającego pomocy technicznej w witrynie Azure portal dla maszyny Wirtualnej, której nie jesteś można uzyskać odpowiedź metadanych długie ponowne próby 
9. Uzyskać żądań przekroczyła limit czasu dla mojego wywołanie usługi?
   * Wywołania metadanych musi nastąpić z podstawowego adresu IP przypisane do karty sieciowej maszyny wirtualnej, ponadto w przypadku, gdy zmieniono trasy musi być trasę dla adresu 169.254.0.0/16 poza karty sieciowej.
10. Czy mogę zaktualizować Moje znaczniki w zestawie skalowania maszyn wirtualnych, ale nie są wyświetlane w przypadkach, w przeciwieństwie do maszyn wirtualnych?
   * Obecnie dla ScaleSets tagi zawierają tylko do maszyny Wirtualnej na ponowne uruchomienie/odtworzenia z obrazu/lub dyskiem zmiany do wystąpienia. 

   ![Obsługa metadanych wystąpienia](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zaplanowanych zdarzeń](scheduled-events.md)

