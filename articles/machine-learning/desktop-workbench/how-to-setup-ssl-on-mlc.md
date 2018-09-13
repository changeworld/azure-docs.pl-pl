---
title: Włączanie protokołu SSL na klastrze usługi Azure Machine Learning obliczeń (MLC) | Dokumentacja firmy Microsoft
description: Uzyskaj instrukcje dotyczące konfigurowania protokołu SSL do oceniania wywołań w klastrze usługi Azure Machine Learning obliczeń (MLC)
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 982a6807ccaf393c3aea42f39f7e60bb7e0d3ac3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649153"
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Włączanie protokołu SSL na klastrze usługi Azure Machine Learning obliczeń (MLC) 

Instrukcje te umożliwiają konfigurowanie protokołu SSL do oceniania wywołań w klastrze Machine Learning obliczeń (MLC). 

## <a name="prerequisites"></a>Wymagania wstępne 

1. Decyzję w sprawie CNAME (nazwa DNS) do użycia podczas nawiązywania połączeń oceniania w czasie rzeczywistym.

2. Tworzenie *bez hasła* certyfikat przy użyciu tego rekordu CNAME.

3. Jeśli certyfikat nie jest już w formacie PEM, przekonwertuj go na format PEM przy użyciu narzędzi takich jak *openssl*.

Będziesz mieć dwa pliki po ukończeniu wymagania wstępne:

* Plik certyfikatu, na przykład `cert.pem`. Upewnij się, że plik ma łańcuch certyfikatów pełne.
* Plik klucza, na przykład `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Konfigurowanie certyfikatu SSL w nowym klastrze ACS

Za pomocą Machine Learning wiersza polecenia platformy Azure, uruchom następujące polecenie z `-c` przełącznik, aby utworzyć klaster usługi ACS za pomocą certyfikatu SSL dołączone:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Konfigurowanie certyfikatu SSL w istniejącym klastrze usługi ACS

Jeśli klaster, który został utworzony bez zastosowania protokołu SSL, można dodać certyfikatu przy użyciu poleceń cmdlet programu Azure PowerShell.

Musisz podać klucz i certyfikatu w formacie nieprzetworzonym PEM. Można je odczytać w zmiennych środowiska PowerShell:

```
$keyValueInPemFormat = [IO.File]::ReadAllText('<path to key.pem file>')
$certValueInPemFormat = [IO.File]::ReadAllText('<path to cert.pem file>')
```
Dodaj certyfikat do klastra: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mapuj rekord CNAME i adres IP

Utwórz mapowanie między CNAME wybrane w wymaganiach wstępnych i adres IP w czasie rzeczywistym frontonu (FE). Aby odnaleźć adres IP FE, uruchom poniższe polecenie. Dane wyjściowe wyświetla pole o nazwie "publicIpAddress", który zawiera adres IP frontonu klastra w czasie rzeczywistym. Zapoznaj się z instrukcjami dostawcy usługi DNS, aby skonfigurować rekord z z nazwą FQDN używaną w CNAME publiczny adres IP.



## <a name="auto-detection-of-a-certificate"></a>Automatyczne wykrywanie certyfikatu 

Podczas tworzenia usługi internetowej czasu rzeczywistego przy użyciu "`az ml service create realtime`" poleceń usługi Azure Machine Learning automatycznie wykryje, protokołu SSL na klastrze i automatycznie konfiguruje adres URL oceniania, przy użyciu wyznaczonej certyfikatu. 

Aby sprawdzić stan certyfikatu, uruchom następujące polecenie. Należy zauważyć prefiks "https" Usunięto identyfikator URI i rekord CNAME w nazwie hosta. 

``` 
az ml service show realtime -n <service name>
{
                "id" : "<your service id>",
                "name" : "<your service name >",
                "state" : "Succeeded",
                "createdAt" : "<datetime>”,
                "updatedAt" : "< datetime>",
                "scoringUri" : "https://<your CNAME>/api/v1/service/<service name>/score"
}
```
