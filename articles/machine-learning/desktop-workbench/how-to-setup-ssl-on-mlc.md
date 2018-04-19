---
title: "Włącz protokół SSL w klastrze usługi Azure Machine Learning obliczeniowe (MLC) | Dokumentacja firmy Microsoft"
description: "Pobierz informacje o sposobie konfiguracji protokołu SSL dla oceniania wywołań w klastrze usługi Azure Machine Learning obliczeniowe (MLC)"
services: machine-learning
author: SerinaKaye
ms.author: serinak
manager: hjerez
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/24/2018
ms.openlocfilehash: 444803eeb77e79a8bfe3271ddf27bd428042c875
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="enable-ssl-on-an-azure-machine-learning-compute-mlc-cluster"></a>Włącz protokół SSL w klastrze usługi Azure Machine Learning obliczeniowe (MLC) 

Instrukcje te pozwalają skonfigurować protokół SSL dla oceniania wywołań w klastrze Machine Learning obliczeniowe (MLC). 

## <a name="prerequisites"></a>Wymagania wstępne 

1. Zdecydować się na CNAME (nazwa DNS) do użycia w czasie rzeczywistym oceniania wywołania.

2. Utwórz *bez hasła* certyfikat z tym CNAME.

3. Jeśli certyfikat nie jest już w formacie PEM, przekonwertuj go na PEM za pomocą narzędzi takich jak *openssl*.

Po zakończeniu wymagań wstępnych konieczne będzie dwa pliki:

* Plik certyfikatu, na przykład `cert.pem`. Upewnij się, że plik ma łańcuch certyfikatów pełna.
* Plik klucza, na przykład `key.pem`



## <a name="set-up-an-ssl-certificate-on-a-new-acs-cluster"></a>Konfigurowanie certyfikatu SSL w nowym klastrze ACS

Przy użyciu Azure Machine Learning CLI, uruchom następujące polecenie z `-c` przełącznik, aby utworzyć klaster ACS dołączony certyfikat SSL:

```
az ml env create -c -g <resource group name> -n <cluster name> --cert-cname <CNAME> --cert-pem <path to cert.pem file> --key-pem <path to key.pem file>
```


## <a name="set-up-an-ssl-certificate-on-an-existing-acs-cluster"></a>Konfigurowanie certyfikatu SSL w istniejącym klastrze ACS

Jeśli ma być przeznaczona dla klastra, który został utworzony bez zastosowania protokołu SSL, można dodać certyfikatu przy użyciu poleceń cmdlet programu Azure PowerShell: 

```
Set-AzureRmMlOpCluster -ResourceGroupName my-rg -Name my-cluster -SslStatus Enabled -SslCertificate $certValueInPemFormat -SslKey $keyValueInPemFormat -SslCName foo.mycompany.com
```

## <a name="map-the-cname-and-the-ip-address"></a>Mapowanie CNAME i adres IP

Utwórz mapowania między CNAME wybrane w wymaganiach wstępnych i adres IP w czasie rzeczywistym frontonu (FE). Aby odnaleźć adres IP FE, uruchom poniższe polecenie. Dane wyjściowe zawiera pole o nazwie "publicIpAddress", który zawiera adres IP frontonu klastra w czasie rzeczywistym. Zapoznaj się z instrukcjami dostawcy usługi DNS, aby skonfigurować rekord z z nazwą FQDN używaną w CNAME publicznego adresu IP.



## <a name="auto-detection-of-a-certificate"></a>Automatyczne wykrywanie certyfikatu 

Podczas tworzenia usługi sieci web w czasie rzeczywistym za pomocą "`az ml service create realtime`" polecenia uczenia maszynowego Azure automatycznie wykrywa SSL na klastrze i automatycznie konfiguruje oceniania adresu URL przy użyciu wyznaczonej certyfikatu. 

Aby wyświetlić stan certyfikatu, uruchom następujące polecenie. Zwróć uwagę, prefiksu "https" Usunięto identyfikator URI i CNAME w nazwie hosta. 

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
