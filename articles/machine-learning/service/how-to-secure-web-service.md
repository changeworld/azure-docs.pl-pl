---
title: Zabezpieczania usług sieci web przy użyciu protokołu SSL
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zabezpieczyć usługę sieci web, który jest wdrażany za pomocą usługi Azure Machine Learning przez włączenie protokołu HTTPS. Protokół HTTPS zabezpiecza dane z przez klientów przy użyciu zabezpieczeń warstwy transportu (TLS), mogą zastąpić warstwy SSL (SSL). Klienci używają również protokołu HTTPS, aby zweryfikować tożsamość usługi sieci web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: c176458cfc404a9d55d7fb71a36ea63110b3a6d6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657948"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Zabezpieczanie usługi sieci web za pomocą usługi Azure Machine Learning za pomocą protokołu SSL

W tym artykule pokazano, jak zabezpieczyć usługę sieci web, który jest wdrażany za pomocą usługi Azure Machine Learning.

Możesz użyć [HTTPS](https://en.wikipedia.org/wiki/HTTPS) do ograniczenia dostępu do usług sieci web i zabezpieczania danych, który przedstawia klientów. Protokół HTTPS pomaga bezpiecznej komunikacji między klientem a usługą sieci web dzięki szyfrowaniu komunikacji między nimi. Szyfrowanie używa [zabezpieczeń TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest nadal czasami określane jako *Secure Sockets Layer* (SSL), który był następca TLS.

> [!TIP]
> Zestaw SDK usługi Azure Machine Learning używany jest termin "SSL" dla właściwości, które są powiązane z bezpiecznej komunikacji. Nie oznacza to, że usługi sieci web nie używa *TLS*. Protokół SSL jest częściej rozpoznawanym termin.

Protokoły TLS i SSL obie opierają się na *certyfikaty cyfrowe*, która ułatwić weryfikacji szyfrowania i tożsamości. Aby uzyskać więcej informacji na pracy, certyfikatów cyfrowych, zobacz temat Wikipedia [infrastruktury kluczy publicznych](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Jeśli nie używasz protokołu HTTPS dla usługi sieci web, dane są przesyłane do i z usługi mogą być widoczne dla innych osób w Internecie.
>
> Protokół HTTPS umożliwia także klienta w celu sprawdzenia autentyczności serwera, który nawiązuje połączenie z. Funkcja ta chroni klientów przed [ataków typu man-in--middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ataków.

Oto ogólny proces, aby zabezpieczyć usługi sieci web:

1. Pobierz nazwę domeny.

2. Uzyskiwanie certyfikatu cyfrowego.

3. Wdrażanie lub zaktualizować usługę sieci web z włączonym protokołem SSL.

4. Zaktualizuj serwer DNS, aby wskazać usługę sieci web.

> [!IMPORTANT]
> Jeśli wdrażasz do usługi Azure Kubernetes Service (AKS), możesz kupić własnego certyfikatu lub używania certyfikatu, który jest obsługiwane przez firmę Microsoft. Jeśli używasz certyfikatu z firmy Microsoft, nie trzeba uzyskać nazwy domeny lub certyfikatu protokołu SSL. Aby uzyskać więcej informacji, zobacz [włączenia protokołu SSL i wdrażanie](#enable) dalszej części tego artykułu.

Istnieją drobne różnice podczas zabezpieczania usług sieci web w [celów wdrażania](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Pobierz nazwę domeny

Jeśli nie jesteś już właścicielem nazwy domeny, kupić jeden z *rejestratora nazw domen*. Ten proces i ceny różnią się między rejestratorów. Rejestrator oferuje narzędzia do zarządzania nazwy domeny. Za pomocą tych narzędzi do mapowania w pełni kwalifikowaną nazwę domeny (FQDN) (takich jak www\.contoso.com) adres IP, który jest hostem usługi sieci web.

## <a name="get-an-ssl-certificate"></a>Uzyskaj certyfikat protokołu SSL

Istnieje wiele sposobów, aby uzyskać certyfikat SSL (certyfikat cyfrowy). Najczęściej jest nabyć jeden z *urząd certyfikacji* (CA). Niezależnie od tego, gdzie można uzyskać certyfikat potrzebne są następujące pliki:

* A **certyfikatu**. Certyfikat musi zawierać łańcucha certyfikatów pełną, i musi być "Zakodowany w formacie PEM."
* A **klucz**. Klucz musi być również kodowany w formacie PEM.

W przypadku żądania certyfikatu, należy podać nazwę FQDN adresu, który ma być używane dla usługi sieci web (na przykład www\.contoso.com). Adres, który jest oznaczony do certyfikatu i adres, używanego przez klientów, są porównywane do zweryfikowania tożsamości usługi sieci web. Jeśli te adresy nie są zgodne, klient pobiera komunikat o błędzie.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikat i klucz jako pliki zakodowane w formacie PEM, możesz użyć narzędzia takie jak [OpenSSL](https://www.openssl.org/) zmian w formacie.

> [!WARNING]
> Użyj *z podpisem własnym* certyfikaty tylko w celach deweloperskich. Nie należy używać ich w środowisku produkcyjnym. Certyfikaty z podpisem własnym może powodować problemy w swoim kliencie aplikacji. Aby uzyskać więcej informacji zobacz dokumentację bibliotek sieciowych używanych przez aplikację klienta.

## <a id="enable"></a> Włącz protokół SSL i wdrażanie

Aby wdrożyć (lub ponownego wdrażania) usługi z włączonym protokołem SSL, ustaw *ssl_enabled* parametr "True", wszędzie tam, gdzie to możliwe. Ustaw *ssl_certificate* parametru na wartość *certyfikatu* pliku. Ustaw *ssl_key* wartość *klucz* pliku.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Wdrażanie w usłudze AKS i brama programowalny tablicy (FPGA)

  > [!NOTE]
  > Informacje przedstawione w tej sekcji ma zastosowanie, gdy wdrażasz usługę bezpiecznego sieci web dla interfejsu wizualnego. Jeśli nie są zaznajomieni z używaniem zestawu SDK języka Python, zobacz [co to jest Azure Machine Learning zestaw SDK for Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Podczas wdrażania usługi AKS możesz utworzyć nowy klaster AKS, lub dołączenie istniejącego.
  
-  Jeśli tworzysz nowy klaster, możesz użyć  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Jeśli dołączysz istniejącego klastra, możesz użyć  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Oba zwracają obiekt konfiguracji, który ma **enable_ssl** metody.

**Enable_ssl** metody można użyć certyfikatu, który jest obsługiwane przez firmę Microsoft lub możesz kupić certyfikat.

  * Jeśli używasz certyfikatu z firmy Microsoft, musisz użyć *leaf_domain_label* parametru. Ten parametr generuje nazwę DNS dla usługi. Na przykład wartość "myservice" tworzy nazwę domeny "myservice\<sześciu — losowych znaków >.\< region_świadczenia_usługi_azure >. cloudapp.azure.com ", gdzie \<region_świadczenia_usługi_azure > to region, które zawiera tę usługę. Opcjonalnie możesz użyć *overwrite_existing_domain* parametru, aby zastąpić istniejące *leaf_domain_label*.

    Aby wdrożyć (lub ponownego wdrażania) usługi z włączonym protokołem SSL, ustaw *ssl_enabled* parametr "True", wszędzie tam, gdzie to możliwe. Ustaw *ssl_certificate* parametru na wartość *certyfikatu* pliku. Ustaw *ssl_key* wartość *klucz* pliku.

    > [!IMPORTANT]
    > Gdy używasz certyfikatu z firmy Microsoft, nie trzeba kupować własnego certyfikatu lub nazwy domeny.

    Poniższy przykład pokazuje, jak utworzyć konfigurację umożliwiającą certyfikatu SSL od firmy Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Kiedy używasz *certyfikat zakupiony*, możesz użyć *ssl_cert_pem_file*, *ssl_key_pem_file*, i *ssl_cname* Parametry. Poniższy przykład pokazuje sposób użycia *PEM* pliki, aby utworzyć konfigurację, która używa certyfikatu protokołu SSL zakupiony:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Aby uzyskać więcej informacji na temat *enable_ssl*, zobacz [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) i [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Wdrażanie w usłudze Azure Container Instances

Podczas wdrażania usługi Azure Container Instances możesz podać wartości parametrów związanych z protokołu SSL, co ilustruje poniższy fragment kodu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Aby uzyskać więcej informacji, zobacz [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Zaktualizuj serwer DNS

Następnie należy zaktualizować serwer DNS, aby wskazać usługę sieci web.

+ **Dla wystąpienia kontenera:**

  Użyj narzędzi z Rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać na adres IP usługi.

  Może to nastąpić z opóźnieniem, minuty lub godziny, zanim klienci mogą rozpoznać nazwę domeny, w zależności od Rejestrator i "czas wygaśnięcia" (TTL) skonfigurowanego dla nazwy domeny.

+ **Dla usługi AKS:**

  > [!WARNING]
  > Jeśli użyto *leaf_domain_label* Aby utworzyć usługę za pomocą certyfikatu firmy Microsoft, nie ręcznie zaktualizować wartość DNS dla klastra. Wartość powinna być ustawiane automatycznie.

  Aktualizowanie systemu DNS na **konfiguracji** kartę publiczny adres IP klastra AKS. (Zobacz poniższy obraz). Publiczny adres IP jest typem zasobu, który jest tworzony w ramach grupy zasobów, która zawiera węzły agenta usługi AKS i innych zasobów sieciowych.

  ![Usługa Azure Machine Learning: Zabezpieczanie usług sieci web przy użyciu protokołu SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Kolejne kroki
Instrukcje:
+ [Korzystanie z usługi machine learning model wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
+ [Bezpiecznego uruchamiania eksperymentów oraz wnioskowania wewnątrz sieci wirtualnej platformy Azure](how-to-enable-virtual-network.md)
