---
title: Zabezpieczanie usług internetowych przy użyciu protokołu SSL
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zabezpieczyć usługę sieci web wdrażane za pomocą usługi Azure Machine Learning przez włączenie protokołu HTTPS. Protokół HTTPS zabezpiecza dane wysyłane przez klientów za pomocą zabezpieczeń warstwy transportu (TLS), mogą zastąpić warstwy SSL (SSL). Również służy przez klientów do zweryfikowania tożsamości usługi sieci web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818534"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Użyj protokołu SSL do zabezpieczania usług sieci web za pomocą usługi Azure Machine Learning

W tym artykule dowiesz się, jak zabezpieczyć usługę sieci web wdrażane za pomocą usługi Azure Machine Learning. Można ograniczyć dostęp do usług sieci web i zabezpieczanie danych przesyłanych przez klientów przy użyciu [Hypertext Transfer Protocol bezpieczne (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

Protokół HTTPS jest używany do zabezpieczania komunikacji między klientem a usługi sieci web dzięki szyfrowaniu komunikacji między nimi. Szyfrowanie jest obsługiwane za pomocą [zabezpieczeń TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Czasami jest to nadal określane jako Secure Sockets Layer (SSL), która poprzednika protokołu TLS była.

> [!TIP]
> Zestaw SDK usługi Azure Machine Learning używany jest termin "SSL" dla właściwości powiązany z włączaniem bezpieczną komunikację. Nie oznacza to, że protokół TLS, nie jest używany przez usługi sieci web, po prostu ten protokół SSL jest bardziej rozpoznawalnych terminem dla wielu czytników.

Protokoły TLS i SSL obie opierają się na __certyfikaty cyfrowe__, które są używane do przeprowadzenia weryfikacji szyfrowania i tożsamości. Aby uzyskać więcej informacji na pracy, certyfikatów cyfrowych, zobacz wpis Wikipedia na [infrastruktury kluczy publicznych (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Jeśli nie zostanie włączone i używać protokołu HTTPS dla usługi sieci web, dane wysyłane do i z usługi może być widoczny na innych użytkowników w Internecie.
>
> Protokół HTTPS umożliwia także klienta w celu sprawdzenia autentyczności serwera, który nawiązuje połączenie z. To chroni klientów przed [ataków typu man-in--middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ataków.

Proces zabezpieczania nowej usługi sieci web lub istniejąca jest następująca:

1. Pobierz nazwę domeny.

2. Uzyskiwanie certyfikatu cyfrowego.

3. Wdrażanie lub zaktualizować usługę sieci web przy użyciu ustawienia protokołu SSL, włączona.

4. Zaktualizuj serwer DNS, aby wskazać usługę sieci web.

Istnieją drobne różnice podczas zabezpieczania usług sieci web w [celów wdrażania](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Pobierz nazwę domeny

Jeśli nie jesteś już właścicielem nazwy domeny, możesz kupić jeden z __rejestratora nazw domen__. Ten proces różni się między rejestratorów, tak jak koszt. Rejestrator również udostępnia narzędzia do zarządzania nazwy domeny. Te narzędzia są używane do mapowania w pełni kwalifikowanej nazwy domeny (np. www\.contoso.com) do adresu IP, obsługującego usługi sieci web.

## <a name="get-an-ssl-certificate"></a>Uzyskaj certyfikat protokołu SSL

Istnieje wiele sposobów, aby uzyskać certyfikat SSL (certyfikat cyfrowy). Najczęściej jest nabyć jeden z __urzędu certyfikacji__ (CA). Niezależnie od tego, gdzie można uzyskać certyfikat potrzebne są następujące pliki:

* A __certyfikatu__. Certyfikat musi zawierać łańcucha certyfikatów pełnego i musi być zakodowany w formacie PEM.
* A __klucz__. Klucz musi być zakodowany w formacie PEM.

Podczas żądania certyfikatu, podaj w pełni kwalifikowana nazwa domeny (FQDN) adresu, który ma być używany przez usługę sieci web. Na przykład www\.contoso.com. Adres, który został oznaczony do certyfikatu i adres używany przez klientów, są porównywane podczas weryfikowania tożsamości usługi sieci web. Jeśli adresy różnią się od siebie, klienci spowoduje wystąpienie błędu.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikat i klucz jako pliki zakodowane w formacie PEM, możesz użyć narzędzia takie jak [OpenSSL](https://www.openssl.org/) zmian w formacie.

> [!WARNING]
> Certyfikaty z podpisem własnym należy używać tylko w celach deweloperskich. Nie powinny one być używane w środowisku produkcyjnym. Certyfikaty z podpisem własnym może powodować problemy w swoim kliencie aplikacji. Aby uzyskać więcej informacji zobacz dokumentację bibliotek sieciowych używanych w aplikacji klienckiej.

## <a name="enable-ssl-and-deploy"></a>Włącz protokół SSL i wdrażanie

Aby wdrożyć (lub ponownie wdrożyć) usługi z włączonym protokołem SSL, ustaw `ssl_enabled` parametr `True`, gdy mają zastosowanie. Ustaw `ssl_certificate` parametru na wartość __certyfikatu__ pliku i `ssl_key` wartość __klucz__ pliku.

+ **Wdrażanie w usłudze Azure Kubernetes Service (AKS)**

  Podczas aprowizowania klastra usługi AKS, podaj wartości dla parametrów związanych z protokołu SSL, jak pokazano we fragmencie kodu:

    ```python
    from azureml.core.compute import AksCompute

    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Wdrażanie w usłudze Azure Container Instances (ACI)**

  Podczas wdrażania usługi ACI, podaj wartości dla parametrów związanych z protokołu SSL, jak pokazano we fragmencie kodu:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Wdrażanie na Tablice bramek programowane (FPGA)**

  Podczas wdrażania FPGA, podaj wartości dla parametrów związanych z protokołu SSL, jak pokazano we fragmencie kodu:

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>Zaktualizuj serwer DNS

Następnie należy zaktualizować serwer DNS, aby wskazać usługę sieci web.

+ **Aby uzyskać ACI**:

  Użyj narzędzi dostarczanych przez rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać na adres IP usługi.

  W zależności od Rejestrator i czas wygaśnięcia (TTL) skonfigurowany dla nazwy domeny, może upłynąć kilka minut do kilku godzin, zanim klienci mogą rozpoznać nazwę domeny.

+ **Dla usługi AKS**:

  Aktualizowanie systemu DNS, na karcie "Konfiguracja" "Publiczny adres IP" dla klastra AKS, jak pokazano na ilustracji. Możesz znaleźć publiczny adres IP jako jeden z typów zasobów utworzonych w ramach grupy zasobów, która zawiera węzły agenta usługi AKS i innych zasobów sieciowych.

  ![Usługa Azure Machine Learning: Zabezpieczanie usług sieci web przy użyciu protokołu SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Kolejne kroki
Instrukcje:
+ [Korzystanie z usługi machine learning model wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
+ [Bezpiecznego uruchamiania eksperymentów oraz wnioskowania wewnątrz usługi Azure Virtual Network](how-to-enable-virtual-network.md)
