---
title: Zabezpieczania usług sieci web przy użyciu protokołu SSL
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zabezpieczyć usługę sieci web wdrażane za pomocą usługi Azure Machine Learning przez włączenie protokołu HTTPS. Protokół HTTPS zabezpiecza dane wysyłane przez klientów za pomocą zabezpieczeń warstwy transportu (TLS), mogą zastąpić warstwy SSL (SSL). Również służy przez klientów do zweryfikowania tożsamości usługi sieci web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 527f16e34e0f21d435fbd166328235566687bc88
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65852004"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Użyj protokołu SSL do zabezpieczania usług sieci web za pomocą usługi Azure Machine Learning

W tym artykule dowiesz się, jak zabezpieczyć usługę sieci web wdrażane za pomocą usługi Azure Machine Learning. Można ograniczyć dostęp do usług sieci web i zabezpieczanie danych przesyłanych przez klientów przy użyciu [Hypertext Transfer Protocol bezpieczne (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

Protokół HTTPS jest używany do zabezpieczania komunikacji między klientem a usługi sieci web dzięki szyfrowaniu komunikacji między nimi. Szyfrowanie jest obsługiwane za pomocą [zabezpieczeń TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Czasami protokół TLS jest nadal określane jako Secure Sockets Layer (SSL), która poprzednika protokołu TLS była.

> [!TIP]
> Zestaw SDK usługi Azure Machine Learning używany jest termin "SSL" dla właściwości powiązany z włączaniem bezpieczną komunikację. Nie oznacza to, że protokół TLS, nie jest używany przez usługi sieci web, po prostu ten protokół SSL jest bardziej rozpoznawalnych terminem dla wielu czytników.

Protokoły TLS i SSL obie opierają się na __certyfikaty cyfrowe__, które są używane do przeprowadzenia weryfikacji szyfrowania i tożsamości. Aby uzyskać więcej informacji na pracy, certyfikatów cyfrowych, zobacz wpis Wikipedia na [infrastruktury kluczy publicznych (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Jeśli nie zostanie włączone i używać protokołu HTTPS dla usługi sieci web, dane wysyłane do i z usługi może być widoczny na innych użytkowników w Internecie.
>
> Protokół HTTPS umożliwia także klienta w celu sprawdzenia autentyczności serwera, który nawiązuje połączenie z. To chroni klientów przed [ataków typu man-in--middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ataków.

Ogólny proces zabezpieczania nowej usługi sieci web lub istniejąca jest następująca:

1. Pobierz nazwę domeny.

2. Uzyskiwanie certyfikatu cyfrowego.

3. Wdrażanie lub zaktualizować usługę sieci web przy użyciu ustawienia protokołu SSL, włączona.

4. Zaktualizuj serwer DNS, aby wskazać usługę sieci web.

> [!IMPORTANT]
> Jeśli wdrażasz do usługi Azure Kubernetes Service (AKS), możesz podać własne certyfikatu lub używany certyfikat dostarczony przez firmę Microsoft. Jeśli używasz certyfikatu oferowanych przez firmę Microsoft, nie musisz uzyskać nazwy domeny lub certyfikatu protokołu SSL. Aby uzyskać więcej informacji, zobacz [włączenia protokołu SSL i wdrażanie](#enable) sekcji.

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

## <a id="enable"></a> Włącz protokół SSL i wdrażanie

Aby wdrożyć (lub ponownego wdrażania) usługi z włączonym protokołem SSL, ustaw `ssl_enabled` parametr `True`, gdy mają zastosowanie. Ustaw `ssl_certificate` parametru na wartość __certyfikatu__ pliku i `ssl_key` wartość __klucz__ pliku.

+ **Wdrażanie w usłudze Azure Kubernetes Service (AKS) i FPGA**

  > [!NOTE]
  > Informacje przedstawione w tej sekcji ma zastosowanie, podczas wdrażania usługi bezpiecznej sieci web dla interfejsu wizualnego. Jeśli nie jesteś zaznajomiony z przy użyciu zestawu SDK języka Python, zobacz [Przegląd zestawu SDK języka Python do programu Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

  Podczas wdrażania usługi AKS, możesz utworzyć nowy klaster AKS lub dołączenie istniejącego. Tworzenie nowego klastra używa [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) podczas dołączania do istniejącego klastra używa [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Oba zwracają obiekt konfiguracji, który ma `enable_ssl` metody.

  `enable_ssl` Metody można użyć certyfikatu dostarczane przez firmę Microsoft lub jednego, który podasz.

  * W przypadku używania certyfikatu __obsługiwane przez firmę Microsoft__, należy użyć `leaf_domain_label` parametru. Użycie tego parametru spowoduje utworzenie usługi przy użyciu certyfikatu, który został dostarczony przez firmę Microsoft. `leaf_domain_label` Służy do generowania nazwy DNS dla usługi. Na przykład, wartość `myservice` tworzy nazwę domeny `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, gdzie `<azureregion>` to region, które zawiera tę usługę. Opcjonalnie możesz użyć `overwrite_existing_domain` parametru, aby zastąpić istniejącą etykietę domeny typu liść.

    Aby wdrożyć (lub ponownie wdrożyć) usługi z włączonym protokołem SSL, ustaw `ssl_enabled` parametr `True`, gdy mają zastosowanie. Ustaw `ssl_certificate` parametru na wartość __certyfikatu__ pliku i `ssl_key` wartość __klucz__ pliku.

    > [!IMPORTANT]
    > W przypadku używania certyfikatu, obsługiwane przez firmę Microsoft, nie musisz kupować własnego certyfikatu lub nazwy domeny.

    Poniższy przykład pokazuje, jak tworzyć konfiguracje, które umożliwiają certyfikat SSL utworzony przez firmę Microsoft:

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

  * Korzystając z __certyfikatu zakupiono__, użyj `ssl_cert_pem_file`, `ssl_key_pem_file`, i `ssl_cname` parametrów. Poniższy przykład pokazuje, jak tworzyć konfiguracje, które używają certyfikatu SSL należy wprowadzać za pomocą `.pem` plików:

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

  Aby uzyskać więcej informacji na temat `enable_ssl`, zobacz [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) i [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Wdrażanie w usłudze Azure Container Instances (ACI)**

  Podczas wdrażania usługi ACI, podaj wartości dla parametrów związanych z protokołu SSL, jak pokazano we fragmencie kodu:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Aby uzyskać więcej informacji, zobacz [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Zaktualizuj serwer DNS

Następnie należy zaktualizować serwer DNS, aby wskazać usługę sieci web.

+ **Aby uzyskać ACI**:

  Użyj narzędzi dostarczanych przez rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać na adres IP usługi.

  W zależności od Rejestrator i czas wygaśnięcia (TTL) skonfigurowany dla nazwy domeny, może upłynąć kilka minut do kilku godzin, zanim klienci mogą rozpoznać nazwę domeny.

+ **Dla usługi AKS**:

  > [!WARNING]
  > Jeśli użyto `leaf_domain_label` do tworzenia usługi przy użyciu certyfikatu firmy Microsoft, nie ręcznie zaktualizować wartość DNS dla klastra. Wartość powinna być ustawiane automatycznie.

  Aktualizowanie systemu DNS, na karcie "Konfiguracja" "Publiczny adres IP" dla klastra AKS, jak pokazano na ilustracji. Możesz znaleźć publiczny adres IP jako jeden z typów zasobów utworzonych w ramach grupy zasobów, która zawiera węzły agenta usługi AKS i innych zasobów sieciowych.

  ![Usługa Azure Machine Learning: Zabezpieczanie usług sieci web przy użyciu protokołu SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)



## <a name="next-steps"></a>Kolejne kroki
Instrukcje:
+ [Korzystanie z usługi machine learning model wdrożyć jako usługę sieci web](how-to-consume-web-service.md)
+ [Bezpiecznego uruchamiania eksperymentów oraz wnioskowania wewnątrz usługi Azure Virtual Network](how-to-enable-virtual-network.md)

