---
title: Wystąpienie usługi Azure Metadata Service | Dokumentacja firmy Microsoft
description: Interfejsu rESTful, aby uzyskać informacje o Windows VM obliczeniowych, sieci i zdarzeń o zbliżającej się konserwacji.
services: virtual-machines-windows
documentationcenter: ''
author: KumariSupriya
manager: harijayms
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 9097fef88a2c3c667416761c341a2e320c790121
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919053"
---
# <a name="azure-instance-metadata-service"></a>Usługa Azure Instance Metadata service

Azure Instance Metadata Service dostarcza informacji o uruchomionych wystąpień maszyn wirtualnych, które może służyć do zarządzania i konfigurowania maszyn wirtualnych.
Obejmuje to informacje, takie jak SKU, konfiguracja sieci i zdarzenia zbliżającej się konserwacji. Aby uzyskać więcej informacji na temat typu informacji, które jest dostępne, zobacz [metadanych interfejsów API](#metadata-apis).

Azure Instance Metadata Service jest dostępna dla wszystkich maszyn wirtualnych IaaS utworzone za pośrednictwem punktu końcowego REST [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Punkt końcowy jest dostępny w dobrze znanego adresu IP bez obsługi routingu (`169.254.169.254`), są dostępne tylko z poziomu maszyny Wirtualnej.

> [!IMPORTANT]
> Ta usługa jest **jest ogólnie dostępna** we wszystkich regionach platformy Azure.  Będzie ona otrzymywać regularne aktualizacje do udostępnienia nowych informacji o wystąpieniach maszyn wirtualnych. Ta strona zawiera aktualne [metadanych interfejsów API](#metadata-apis) dostępne.

## <a name="service-availability"></a>Dostępność usług

Usługa jest dostępna w regionach platformy Azure jest ogólnie dostępna. Nie wszystkie wersji interfejsu API mogą być dostępne we wszystkich regionach platformy Azure.

Regiony                                        | Dostępność?                                 | Obsługiwane wersje
-----------------------------------------------|-----------------------------------------------|-----------------
[Wszystkie ogólnie dostępne globalnych regionów platformy Azure](https://azure.microsoft.com/regions/)     | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Platforma Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Chińska wersja platformy Azure](https://www.azure.cn/)                                                     | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01
[Publiczne zachodnie środkowe stany USA](https://azure.microsoft.com/regions/)                           | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01

Ta tabela jest aktualizowany, gdy są dostępne aktualizacje usług i/lub nowych wersji są dostępne.

> [!NOTE]
> 2019-02-01 aktualnie jest wprowadzenie wdrażana i jest dostępny w innych regionach wkrótce.

Aby wypróbować Instance Metadata Service, Utwórz Maszynę wirtualną z [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) lub [witryny Azure portal](https://portal.azure.com) w regionach powyżej i postępuj zgodnie z poniższych przykładów.

## <a name="usage"></a>Sposób użycia

### <a name="versioning"></a>Obsługa wersji

Instance Metadata Service jest wersjonowany. Wersje są obowiązkowe, a bieżąca wersja na platformie Azure globalne to `2018-10-01`. Bieżący obsługiwane wersje to (2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02 2018-10-01).

Miarę dodawania nowszych wersji starsze wersje nadal może zostać oceniony zgodności, jeśli skrypty mają zależności dotyczących formatów danych specyficznych dla.

Jeśli wersja nie jest określony, zwracany jest błąd z listą najnowszych wersji.

> [!NOTE]
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Odpowiedź**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

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
/ zaświadczenia | json | brak

Aby uzyskać dostęp, format odpowiedzi innych niż domyślne, określ żądany format jako parametr ciągu zapytania w żądaniu. Na przykład:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> Dla węzły liści `format=json` nie działa. Aby te zapytania `format=text` należy jawnie określić, czy domyślny format json.

### <a name="security"></a>Bezpieczeństwo

Instance Metadata Service punkt końcowy jest dostępny tylko w obrębie uruchomionego wystąpienia maszyny wirtualnej bez obsługi routingu adresu IP. Ponadto, żądanie przy użyciu `X-Forwarded-For` nagłówek zostanie odrzucone przez usługę.
Żądania musi zawierać `Metadata: true` nagłówka, aby upewnić się, że rzeczywistego żądania bezpośrednio był zamierzony i nie jest częścią niezamierzone przekierowania.

### <a name="error"></a>Błąd

Jeśli istnieje, nie znaleziono elementu danych lub źle sformułowane żądanie, Instance Metadata Service zwraca standardowy błędy HTTP. Na przykład:

Kod stanu HTTP | Przyczyna
----------------|-------
200 OK |
400 Niewłaściwe żądanie | Brak `Metadata: true` nagłówek lub Brak formatu podczas wykonywania zapytań dotyczących węzeł liścia
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
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"
```

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "jubilee",
    "offer": "Windows-10",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "1",
    "platformUpdateDomain": "1",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftWindowsDesktop",
    "resourceGroupName": "myrg",
    "sku": "rs4-pro",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "17134.345.59",
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

Można pobrać metadanych wystąpienia w Windows za pośrednictwem `curl` program:

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2018-10-01 | select -ExpandProperty Content
```

Lub za pomocą `Invoke-RestMethod` polecenia cmdlet programu PowerShell:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2018-10-01 -Method get
```

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

```json
{
  "compute": {
    "azEnvironment": "AZUREPUBLICCLOUD",
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "placementGroupId": "",
    "plan": {
        "name": "",
        "product": "",
        "publisher": ""
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "MicrosoftSQLServer",
    "resourceGroupName": "myrg",
    "sku": "Enterprise",
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmScaleSetName": "",
    "vmSize": "Standard_DS2",
    "zone": ""
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
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Interfejsy API metadanych

#### <a name="the-following-apis-are-available-through-the-metadata-endpoint"></a>Następujące interfejsy API są dostępne za pośrednictwem punktu końcowego metadanych:

Dane | Opis | Wprowadzona w wersji
-----|-------------|-----------------------
zaświadczenia | Zobacz [zaświadczenia danych](#attested-data) | 2018-10-01
identity | Zarządzanych tożsamości dla zasobów platformy Azure. Zobacz [uzyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
wystąpienie | Zobacz [wystąpienia interfejsu API](#instance-api) | 2017-04-02
scheduledevents | Zobacz [zaplanowane zdarzenia](scheduled-events.md) | 2017-08-01

#### <a name="instance-api"></a>Wystąpienie interfejsu API
##### <a name="the-following-compute-categories-are-available-through-the-instance-api"></a>Następujące kategorie obliczeniowych są dostępne za pośrednictwem interfejsu API Instance:

> [!NOTE]
> Za pośrednictwem punktu końcowego metadanych następujące kategorie są dostępne za pośrednictwem wystąpienia/obliczeniowe

Dane | Opis | Wprowadzona w wersji
-----|-------------|-----------------------
azEnvironment | Gdy maszyna wirtualna jest uruchomiona w środowisku platformy Azure | 2018-10-01
customData | Zobacz [danych niestandardowych](#custom-data) | 2019-02-01
location | Region platformy Azure maszyna wirtualna jest uruchomiona w | 2017-04-02
name | Nazwa maszyny Wirtualnej | 2017-04-02
oferty | Oferuje informacje dotyczące obrazu maszyny Wirtualnej. Ta wartość dotyczy tylko obrazy wdrożone z galerii obrazów systemu Azure. | 2017-04-02
osType | System Linux lub Windows | 2017-04-02
placementGroupId | [Grupy umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych | 2017-08-01
Plan | [Planowanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) dla maszyny Wirtualnej w jej obraz Azure Marketplace zawiera nazwę, produktu i wydawcy | 2018-04-02
platformUpdateDomain |  [Domena aktualizacji](manage-availability.md) maszyna wirtualna jest uruchomiona | 2017-04-02
platformFaultDomain | [Domena błędów](manage-availability.md) maszyna wirtualna jest uruchomiona | 2017-04-02
dostawca | Dostawca maszyny wirtualnej | 2018-10-01
publicKeys | [Kolekcja kluczy publicznych](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisane do maszyny Wirtualnej i ścieżek | 2018-04-02
Wydawcy | Wydawca obrazu maszyny Wirtualnej | 2017-04-02
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/resource-group-overview.md) dla maszyny wirtualnej | 2017-08-01
sku | Określone jednostki SKU dla obrazu maszyny Wirtualnej | 2017-04-02
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
tags | [Tagi](../../azure-resource-manager/resource-group-using-tags.md) dla maszyny wirtualnej  | 2017-08-01
version | Wersja obrazu maszyny Wirtualnej | 2017-04-02
vmId | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny Wirtualnej | 2017-04-02
vmScaleSetName | [Nazwa zestawu skalowania maszyny wirtualnej](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zestawu skalowania maszyn wirtualnych | 2017-12-01
vmSize | [Rozmiar maszyny wirtualnej](sizes.md) | 2017-04-02
strefa | [Strefa dostępności](../../availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01

##### <a name="the-following-network-categories-are-available-through-the-instance-api"></a>Następujące kategorie sieci są dostępne za pośrednictwem interfejsu API Instance:

> [!NOTE]
> Za pośrednictwem punktu końcowego metadanych następujące kategorie są dostępne za pośrednictwem wystąpienia/sieciowej

Dane | Opis | Wprowadzona w wersji
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
ipv4/publicIpAddress | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
subnet/address | Adres podsieci maszyny wirtualnej | 2017-04-02
podsieci/prefiks | Prefiks podsieci, przykład 24 | 2017-04-02
ipv6/ipAddress | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02
macAddress | Adres mac dla maszyny Wirtualnej | 2017-04-02

## <a name="attested-data"></a>Attested Data

Instance Metadata odpowiada w punkcie końcowym http na 169.254.169.254. Jest częścią scenariusza obsługiwanej przez Instance Metadata Service zagwarantować, że dane odpowiedzi pochodzi z usługi Azure. Część tych informacji możemy podpisać tak, aby obrazy z witryny marketplace, można się, że jest ich obraz działający na platformie Azure.

### <a name="example-attested-data"></a>Przykład zaświadczenia danych

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami formatu JSON. Następujące przykładowe odpowiedzi są drukowane pretty dla czytelności.

 **Żądanie**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Wersja interfejsu API jest polem wymaganym i wersja obsługiwana w przypadku danych zaświadczenia jest 2018-10-01.
Identyfikator jednorazowy jest opcjonalny ciąg 10-cyfrowy podane. (Tymczasowo) może służyć do śledzenia żądań, a jeśli nie zostanie podana, w odpowiedzi zakodowane w ciągu bieżącego czasu UTC sygnatura czasowa jest zwracana.

 **Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Obiekt blob podpisu jest [pkcs7](https://aka.ms/pkcs7) podpisaną wersję dokumentu. Zawiera on certyfikat używany do podpisywania wraz ze szczegółami maszyny Wirtualnej, takie jak vmId jednorazowego, znacznik czasu: tworzenie i wygaśnięcia dokumentu i informacji o planie o obrazie. Informacji o planie tylko jest wypełniana w przypadku obrazów miejsce na rynku platformy Azure. Certyfikat można wyodrębnić z odpowiedzi i służy do sprawdzania, czy odpowiedź jest prawidłowa i czy pochodzi z platformy Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Pobieranie metadanych zaświadczenia na maszynie wirtualnej Windows

 **Żądanie**

Można pobrać metadanych wystąpienia w Windows za pośrednictwem narzędzia PowerShell `curl`:

 ```bash
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Lub za pomocą `Invoke-RestMethod` polecenia cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Wersja interfejsu API jest polem wymaganym i wersja obsługiwana w przypadku danych zaświadczenia jest 2018-10-01.
Identyfikator jednorazowy jest opcjonalny ciąg 10-cyfrowy podane. (Tymczasowo) może służyć do śledzenia żądań, a jeśli nie zostanie podana, w odpowiedzi zakodowane w ciągu bieżącego czasu UTC sygnatura czasowa jest zwracana.

 **Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciąg JSON. Następujące przykładową odpowiedź jest drukowany pretty dla czytelności.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> Obiekt blob podpisu jest [pkcs7](https://aka.ms/pkcs7) podpisaną wersję dokumentu. Zawiera on certyfikat używany do podpisywania wraz ze szczegółami maszyny Wirtualnej, takie jak vmId jednorazowego, znacznik czasu: tworzenie i wygaśnięcia dokumentu i informacji o planie o obrazie. Informacji o planie tylko jest wypełniana w przypadku obrazów miejsce na rynku platformy Azure. Certyfikat można wyodrębnić z odpowiedzi i służy do sprawdzania, czy odpowiedź jest prawidłowa i czy pochodzi z platformy Azure.


## <a name="example-scenarios-for-usage"></a>Przykładowe scenariusze użycia  

### <a name="tracking-vm-running-on-azure"></a>Śledzenie maszyny wirtualnej działającej na platformie Azure

Jako dostawca usług mogą wymagać, aby śledzić liczbę maszyn wirtualnych z oprogramowaniem lub agenci, którzy muszą śledzić unikatowości maszyny wirtualnej. Aby można było uzyskać unikatowego Identyfikatora dla maszyny Wirtualnej, należy użyć `vmId` pola z Instance Metadata Service.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umieszczanie kontenerów, domena błędów/aktualizacji bazująca na partycjach danych 

W przypadku niektórych scenariuszy rozmieszczenie replik różnych danych znaczenie ma. Na przykład [umieszczania repliki systemu plików HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) lub położenia kontenera za pomocą [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać wiedzieć `platformFaultDomain` i `platformUpdateDomain` maszyna wirtualna jest uruchomiona.
Można również użyć [strefy dostępności](../../availability-zones/az-overview.md) wystąpień podejmowanie tych decyzji.
Można tworzyć zapytania te dane bezpośrednio za pośrednictwem Instance Metadata Service.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Uzyskiwanie dodatkowych informacji o maszynie wirtualnej podczas przetwarzania zgłoszenia do pomocy technicznej

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

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Uzyskiwanie informacji o środowisku platformy Azure, w którym działa maszyna wirtualna

Platforma Azure oferuje różne chmur suwerennych, takich jak [Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/). Czasami konieczne jest środowisko platformy Azure do podejmowania decyzji dotyczących niektórych środowiska uruchomieniowego. Poniższy przykład pokazuje, jak można osiągnąć to zachowanie.

**Żądanie**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Odpowiedź**
```bash
AZUREPUBLICCLOUD
```

### <a name="getting-the-tags-for-the-vm"></a>Trwa pobieranie tagów dla maszyny Wirtualnej

Tagi mogą zostały zastosowane do maszyny Wirtualnej platformy Azure umożliwia ich logiczne zorganizowanie w taksonomii. Tagi przypisane do maszyny Wirtualnej mogą być pobierane za pomocą żądanie poniżej.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Odpowiedź**

```text
Department:IT;Environment:Test;Role:WebRole
```

> [!NOTE]
> Znaczniki są oddzielone średnikami. Analizator składni są zapisywane do programowego wyodrębnić tagi, nazwy i wartości tagów zakres nie powinien zawierać średnikami w kolejności dla analizatora do prawidłowego działania.

### <a name="validating-that-the-vm-is-running-in-azure"></a>Weryfikowanie, czy maszyna wirtualna jest uruchomiona na platformie Azure

Dostawców Marketplace chcesz upewnić się, że ich oprogramowanie jest licencjonowane do uruchomienia tylko na platformie Azure. Jeśli ktoś kopiuje plik VHD do środowiska lokalnego, następnie powinny mieć możliwość wykrywania, która. Przez wywołanie Instance Metadata Service, dostawców Marketplace można uzyskać podpisanych danych, który gwarantuje odpowiedzi tylko na platformie Azure.

> [!NOTE]
> Wymaga jq do zainstalowania.

**Żądanie**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2018-10-01 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Odpowiedź**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34"
}
```

Dane | Opis
-----|------------
Identyfikator jednorazowy | Użytkownik podał opcjonalny ciąg z żądaniem. W przypadku jednorazowego nie został podany w żądaniu, zwracana jest bieżącą sygnaturę czasową UTC
Plan | [Planowanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) dla maszyny Wirtualnej w niej jest obraz Azure Marketplace, zawiera nazwę, produktu i wydawcy
timestamp/createdOn | Sygnatura czasowa, w którym utworzono pierwszy podpisany dokument
timestamp/expiresOn | Sygnatura czasowa, w którym wygasa podpisany dokument
vmId |  [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny Wirtualnej

#### <a name="verifying-the-signature"></a>Sprawdzanie podpisu

Po pobraniu podpisu powyżej, można sprawdzić, czy podpis pochodzi od firmy Microsoft. Również może sprawdzić certyfikat pośredniego i łańcucha certyfikatów.

> [!NOTE]
> Certyfikat dla chmury publicznej i należących do suwerennej chmury może się różnić.

 Regiony | Certyfikat
---------|-----------------
[Wszystkie ogólnie dostępne globalnych regionów platformy Azure](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Platforma Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Chińska wersja platformy Azure](https://www.azure.cn/)                                                           | metadata.azure.cn
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
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

1. Uruchom następujące polecenie i użyj adresu interfejsu dla lokalizacji docelowej sieci (`0.0.0.0`) czyli (`10.0.1.10`) w tym przykładzie.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="custom-data"></a>Dane niestandardowe
Instance Metadata Service umożliwia dla maszyny Wirtualnej, aby mieć dostęp do swoich danych niestandardowych. Dane binarne muszą być mniej niż 64KB i jest dostarczany do maszyny Wirtualnej w postaci zakodowane w formacie base64. Aby uzyskać szczegółowe informacje dotyczące sposobu tworzenia maszyny Wirtualnej przy użyciu niestandardowych danych, zobacz [wdrożyć maszynę wirtualną za pomocą funkcji CustomData](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

#### <a name="retrieving-custom-data-in-virtual-machine"></a>Trwa pobieranie danych niestandardowych na maszynie wirtualnej
Instance Metadata Service udostępnia danych niestandardowych do maszyny Wirtualnej w postaci zakodowane w formacie base64. Poniższy przykład Dekoduje ciąg zakodowany w formacie base64.

> [!NOTE]
> Niestandardowe dane w tym przykładzie jest interpretowany jako ciąg znaków ASCII o treści "Dane super secret.".

**Żądanie**

```bash
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute/customData?api-version=2019-02-01&&format=text" | base64 --decode
```

**Odpowiedź**

```text
My super secret data.
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
   * Wyślij swoje komentarze dotyczące https://feedback.azure.com.
7. To będzie działać dla wystąpienia zestawu skalowania maszyn wirtualnych?
   * Tak Metadata service jest dostępna dla wystąpień zestawu skalowania.
8. Jak uzyskać pomoc techniczną dla usługi?
   * Aby uzyskać pomoc techniczną dla usługi, należy utworzyć problemu wymagającego pomocy technicznej w witrynie Azure portal dla maszyny Wirtualnej, której nie jesteś można uzyskać odpowiedź metadanych długie ponowne próby.
9. Uzyskać żądań przekroczyła limit czasu dla mojego wywołanie usługi?
   * Wywołania metadanych musi nastąpić z podstawowego adresu IP przypisane do karty sieciowej maszyny wirtualnej, ponadto w przypadku, gdy zmieniono trasy musi być trasę dla adresu 169.254.0.0/16 poza karty sieciowej.
10. Czy mogę zaktualizować Moje znaczniki w zestawie skalowania maszyn wirtualnych, ale nie są wyświetlane w przypadkach, w przeciwieństwie do maszyn wirtualnych?
    * Obecnie dla ScaleSets tagi zawierają tylko do maszyny Wirtualnej na ponowne uruchomienie/odtworzenia z obrazu/lub dyskiem zmiany do wystąpienia.

    ![Obsługa metadanych wystąpienia](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [zaplanowanych zdarzeń](scheduled-events.md)
