---
title: Usługa metadanych wystąpienia platformy Azure | Dokumentacja firmy Microsoft
description: RESTful interfejsu, aby uzyskać informacje o obliczeniowych, sieci i zdarzeń planowanych konserwacji systemu Windows maszyny Wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: harijayms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/10/2017
ms.author: harijayms
ms.openlocfilehash: f6eef8be28ed7e1a7c4be2316dbf670bde76a637
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="azure-instance-metadata-service"></a>Usługa Azure wystąpienie metadanych


Usługę Azure wystąpienie metadanych dostarcza informacji o uruchomionych wystąpień maszyn wirtualnych, które mogą służyć do zarządzania i skonfigurować maszyny wirtualne.
Dotyczy to również informacje, takie jak jednostka SKU, konfiguracji sieci i zdarzeń planowanych konserwacji. Aby uzyskać więcej informacji na jakie rodzaje informacji jest dostępnych w temacie [kategorie metadanych](#instance-metadata-data-categories).

Usługa metadanych wystąpienia platformy Azure jest punkt końcowy REST jest dostępny dla wszystkich maszyn wirtualnych IaaS utworzony za pośrednictwem [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Punkt końcowy jest dostępny w dobrze znanego adresu IP bez obsługi routingu (`169.254.169.254`) który jest możliwy tylko z poziomu maszyny Wirtualnej.

> [!IMPORTANT]
> Ta usługa jest **ogólnie dostępna** we wszystkich regionach platformy Azure.  Regularnie odbiera aktualizacje, aby udostępnić nowe informacje o wystąpieniu maszyny wirtualnej. Ta strona odzwierciedla aktualne [kategorii danych](#instance-metadata-data-categories) dostępne.

## <a name="service-availability"></a>Dostępność usług
Usługa jest dostępna w ogólnie dostępne regiony platformy Azure. Nie wszystkie wersja interfejsu API mogą być dostępne we wszystkich regionach platformy Azure.

Regiony                                        | Dostępność?                                 | Obsługiwane wersje
-----------------------------------------------|-----------------------------------------------|-----------------
[Wszystkie ogólnie dostępna globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | Ogólnie dostępna   | 2017-04-02 2017-08-01, 2017-12-01, 2018-02-01
[Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/)              | Ogólnie dostępna | 2017-04-02,2017-08-01
[Chin Azure](https://www.azure.cn/)                                                           | Ogólnie dostępna | 2017-04-02,2017-08-01
[Niemcy Azure](https://azure.microsoft.com/overview/clouds/germany/)                    | Ogólnie dostępna | 2017-04-02,2017-08-01

Ta tabela jest aktualizowany, gdy są dostępne aktualizacje usługi i/lub są dostępne nowe wersje obsługiwane

Wypróbowanie usługi metadanych wystąpienia, należy utworzyć Maszynę wirtualną z [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) lub [portalu Azure](http://portal.azure.com) w regionach powyżej i postępuj zgodnie z poniższym przykładzie.

## <a name="usage"></a>Sposób użycia

### <a name="versioning"></a>Obsługa wersji
Wystąpienie usługi metadanych jest kontrolą wersji. Wersje są obowiązkowe i bieżąca wersja globalne Azure to `2017-12-01`. Bieżący obsługiwane wersje to (2017-04-02, 2017-08-01,2017-12-01)

> [!NOTE] 
> Poprzednie wersje Podgląd zdarzeń zaplanowane {najnowszych} obsługiwana jako wersja interfejsu api. Ten format jest już obsługiwane i zostaną wycofane w przyszłości.

Miarę dodawania nowszych wersji, starsze wersje nadal będą dostępne dla zgodności czy skrypty są zależne formatów określonych danych. Jednak nie mogą być dostępne poprzedniej wersji zapoznawczej (2017-03-01), gdy usługa jest ogólnie dostępna.

### <a name="using-headers"></a>Korzystanie z nagłówków
Kwerenda usługi metadanych wystąpienia, należy określić nagłówek `Metadata: true` aby upewnić się, żądanie nie zostało przekierowane przypadkowo.

### <a name="retrieving-metadata"></a>Pobieranie metadanych

Wystąpienie metadanych jest dostępna do uruchamiania maszyny wirtualne utworzone/zarządzane przy użyciu [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Dostęp do wszystkich kategorii danych dla wystąpienia maszyny wirtualnej za pomocą następujących żądania:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE] 
> Wszystkie wystąpienia zapytania metadanych jest rozróżniana wielkość liter.

### <a name="data-output"></a>Dane wyjściowe
Domyślnie usługa metadanych wystąpienia zwraca dane w formacie JSON (`Content-Type: application/json`). Jednak różnych interfejsów API zwrócić dane w różnych formatach, jeśli jest to wymagane.
Poniższa tabela jest odwołaniem innych formatów danych, który może obsługiwać interfejsy API.

Interfejs API | Domyślny Format danych | W innych formatach
--------|---------------------|--------------
/instance | JSON | Tekst
/scheduledevents | JSON | brak

Aby uzyskać dostęp, format odpowiedzi z systemem innym niż domyślny, określ żądany format jako parametr querystring w żądaniu. Na przykład:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

### <a name="security"></a>Bezpieczeństwo
Punkt końcowy usługi metadanych wystąpienia jest dostępny tylko w obrębie uruchomione wystąpienie maszyny wirtualnej bez obsługi routingu adresu IP. Ponadto wszelkie żądania z `X-Forwarded-For` nagłówek został odrzucony przez usługę.
Żądania musi zawierać `Metadata: true` nagłówka, aby upewnić się, że rzeczywistego żądania bezpośrednio był zamierzony i nie jest częścią niezamierzonych przekierowania. 

### <a name="error"></a>Błąd
Jeśli istnieje element danych nie można odnaleźć lub źle sformułowane żądanie, wystąpienie usługi metadanych zwraca standardowe komunikaty o błędach HTTP. Na przykład:

Kod stanu HTTP | Przyczyna
----------------|-------
200 OK |
400 Niewłaściwe żądanie | Brak `Metadata: true` nagłówka
404 — Nie odnaleziono | Żądany element nie istnieje. 
405 — metoda nie jest dozwolone | Tylko `GET` i `POST` żądania są obsługiwane.
429 zbyt wiele żądań | Interfejs API obsługuje obecnie maksymalnie 5 zapytania na sekundę
Błąd usługi 500     | Spróbuj ponownie za jakiś czas

### <a name="examples"></a>Przykłady

> [!NOTE] 
> Wszystkie odpowiedzi interfejsu API są ciągami formatu JSON. Wszystkie odpowiedzi na przykład następujące są drukowane pretty dla czytelności.

#### <a name="retrieving-network-information"></a>Trwa pobieranie informacji o sieci

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciągu JSON. Następujący przykład odpowiedzi jest drukowany pretty dla czytelności.

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

#### <a name="retrieving-public-ip-address"></a>Trwa pobieranie publicznego adresu IP

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
> Odpowiedź jest ciągu JSON. Następujący przykład odpowiedzi jest drukowany pretty dla czytelności.

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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Pobieranie metadanych maszyny wirtualnej w systemie Windows

**Żądanie**

W systemie Windows za pomocą można pobrać metadanych wystąpienia `curl` program: 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-08-01 | select -ExpandProperty Content
```

Lub za pomocą `Invoke-RestMethod` polecenia cmdlet programu PowerShell:
    
```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-08-01 -Method get 
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciągu JSON. Następujący przykład odpowiedzi jest drukowany pretty dla czytelności.

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

## <a name="instance-metadata-data-categories"></a>Kategorie danych metadanych wystąpienia
Następujące kategorie danych są dostępne za pośrednictwem usługi metadanych wystąpienie:

Dane | Opis | Wprowadzona wersja 
-----|-------------|-----------------------
location | Region platformy Azure, maszyna wirtualna jest uruchomiona | 2017-04-02 
name | Nazwa maszyny Wirtualnej | 2017-04-02
oferta | Oferują informacji o obrazie maszyny Wirtualnej. Ta wartość ma tylko obrazy wdrożone z galerii Azure obrazu. | 2017-04-02
Wydawcy | Wydawcy obrazu maszyny Wirtualnej | 2017-04-02
sku | Określonej jednostki SKU dla obrazu maszyny Wirtualnej | 2017-04-02
wersja | Wersja obrazu maszyny Wirtualnej | 2017-04-02
osType | Linux lub Windows | 2017-04-02
platformUpdateDomain |  [Domeny aktualizacji](manage-availability.md) wirtualna jest uruchomiona | 2017-04-02
platformFaultDomain | [Domena błędów](manage-availability.md) wirtualna jest uruchomiona | 2017-04-02
vmId | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny Wirtualnej | 2017-04-02
vmSize | [Rozmiar maszyny Wirtualnej](sizes.md) | 2017-04-02
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
tags | [Tagi](../../azure-resource-manager/resource-group-using-tags.md) dla maszyny wirtualnej  | 2017-08-01
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/resource-group-overview.md) dla maszyny wirtualnej | 2017-08-01
placementGroupId | [Grupy umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) Twojego skali maszyny wirtualnej ustawić | 2017-08-01
vmScaleSetName | [Nazwa maszyny wirtualnej ScaleSet] (.. /.. / virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) z skali maszyny wirtualnej ustawić | 2017-12-01
strefa | [Dostępność strefy](../../availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01 
ipv4/privateIpAddress | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
ipv4/publicIpAddress | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
podsieć lub adres. | Adres podsieci maszyny wirtualnej | 2017-04-02 
prefiks podsieci / | Prefiks podsieci, przykład 24 | 2017-04-02 
adres IPv6/IP | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02 
macAddress | Adres mac dla maszyny Wirtualnej | 2017-04-02 
scheduledevents | Zobacz [zaplanowane zdarzenia](scheduled-events.md) | 2017-08-01
identity | (Wersja zapoznawcza) Tożsamość usługi zarządzanej. Zobacz [uzyskać token dostępu](../../active-directory/managed-service-identity/how-to-use-vm-token.md) | 2018-02-01

## <a name="example-scenarios-for-usage"></a>Przykładowe scenariusze użycia  

### <a name="tracking-vm-running-on-azure"></a>Śledzenie maszyn wirtualnych działających na platformie Azure

Jako dostawcę usług może wymagać śledzić liczbę maszyn wirtualnych z oprogramowaniem lub agentami, którzy muszą śledzić unikatowości maszyny wirtualnej. Aby można było pobrać Unikatowy identyfikator dla maszyny Wirtualnej, użyj `vmId` pola wystąpienia metadanych usługi.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umieszczania kontenery, partycje danych na podstawie domen błędów lub zaktualizowania 

Dla niektórych scenariuszy, umieszczania repliki danych różnych znaczenie ma. Na przykład [umieszczania repliki systemu plików HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) lub położenia kontenera za pomocą [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać wiedzieć `platformFaultDomain` i `platformUpdateDomain` maszyna wirtualna jest uruchomiona na.
Można też skorzystać [stref dostępności](../../availability-zones/az-overview.md) wystąpień podejmowanie tych decyzji.
Umożliwia wysyłanie zapytań tych danych bezpośrednio za pomocą wystąpienia usługi metadanych.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text" 
```

**Odpowiedź**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Więcej informacji na temat maszyny Wirtualnej podczas do sprawę pomocy technicznej 

Jako dostawcę usług mogą wystąpić pomocy technicznej gdzie chcesz dowiedzieć się więcej informacji na temat maszyny Wirtualnej. Pytania klientów do udostępniania metadanych obliczeń zapewnia podstawowe informacje o pomocy technicznej wiedzieć o rodzaju maszyny Wirtualnej na platformie Azure. 

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-08-01"
```

**Odpowiedź**

> [!NOTE] 
> Odpowiedź jest ciągu JSON. Następujący przykład odpowiedzi jest drukowany pretty dla czytelności.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Przykłady wywoływania usługi metadanych przy użyciu różnych języków, w ramach maszyny Wirtualnej 

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
    

## <a name="faq"></a>Często zadawane pytania
1. Pojawia się błąd `400 Bad Request, Required metadata header not specified`. Co to znaczy?
   * Wystąpienie usługi metadanych wymaga nagłówka `Metadata: true` , należy przesłać żądanie. Przekazywanie tego nagłówka w wywołaniu REST umożliwia dostęp do wystąpienia usługi metadanych. 
2. Dlaczego nie występują obliczeniowe informacji Moje maszyny wirtualnej?
   * Obecnie usługa metadanych wystąpienie obsługuje tylko wystąpienia utworzone za pomocą Menedżera zasobów Azure. W przyszłości Obsługa maszyn wirtualnych usługi w chmurze mogą być dodane.
3. Napisany wcześniej utworzony Moje maszyny wirtualnej za pomocą usługi Azure Resource Manager. Dlaczego mam nie zawiera compute metadane?
   * Wszystkie maszyny wirtualne utworzone po wrz 2016, można dodać [Tag](../../azure-resource-manager/resource-group-using-tags.md) aby zacząć wyświetlać obliczeniowe metadanych. Dla starszych maszyn wirtualnych (utworzone przed 2016 wrz) Dodaj/Usuń rozszerzenia lub danych dysków do maszyny Wirtualnej, aby odświeżyć metadane.
4. Nie są wyświetlane wszystkie dane wypełnione dla nowej wersji 2017-08-01
   * Wszystkie maszyny wirtualne utworzone po wrz 2016, można dodać [Tag](../../azure-resource-manager/resource-group-using-tags.md) aby zacząć wyświetlać obliczeniowe metadanych. Dla starszych maszyn wirtualnych (utworzone przed 2016 wrz) Dodaj/Usuń rozszerzenia lub danych dysków do maszyny Wirtualnej, aby odświeżyć metadane.
5. Dlaczego otrzymuję błąd `500 Internal Server Error`?
   * Ponów żądanie w oparciu wykładniczego wycofywania systemu. Jeśli problem będzie się powtarzał skontaktuj się z pomocą techniczną platformy Azure.
6. Gdzie udostępniać dodatkowe pytania/komentarze?
   * Wyślij komentarze http://feedback.azure.com.
7. Czy to pomoże dla wystąpienia ustawić skali maszyny wirtualnej?
   * Tak, usługa metadanych jest dostępna dla wystąpień ustawić skali. 
8. Jak uzyskać pomoc techniczną dla usługi?
   * Aby uzyskać pomoc techniczną dla usługi, utworzyć problem pomocy technicznej w portalu Azure dla maszyny Wirtualnej, gdy nie jest możliwe uzyskanie odpowiedzi metadanych mimo ponownych prób długa 

   ![Obsługa metadanych wystąpienia](./media/instance-metadata-service/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zaplanowane zdarzenia](scheduled-events.md)
