---
title: Bezpieczne usługi internetowe przy użyciu protokołu TLS
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak włączyć protokół HTTPS w celu zabezpieczenia usługi sieci web wdrożonej za pośrednictwem usługi Azure Machine Learning. Usługa Azure Machine Learning używa protokołu TLS w wersji 1.2 do bezpiecznych modeli wdrożonych jako usługi sieci web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287343"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule pokazano, jak zabezpieczyć usługę sieci web, która jest wdrażana za pośrednictwem usługi Azure Machine Learning.

Protokół [HTTPS](https://en.wikipedia.org/wiki/HTTPS) służy do ograniczania dostępu do usług sieci web i zabezpieczania przesyłanych przez klientów danych. Protokół HTTPS pomaga zabezpieczyć komunikację między klientem a usługą sieci web, szyfrując komunikację między nimi. Szyfrowanie używa [zabezpieczeń warstwy transportu (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS jest czasami nadal określane jako *Secure Sockets Layer* (SSL), który był poprzednikiem TLS.

> [!TIP]
> Zestaw SDK usługi Azure Machine Learning używa terminu "SSL" dla właściwości, które są związane z bezpieczną komunikacją. Nie oznacza to, że usługa internetowa nie używa *protokołu TLS.* SSL jest po prostu bardziej powszechnie rozpoznawanym terminem.
>
> W szczególności usługi sieci web wdrożone za pośrednictwem usługi Azure Machine Learning obsługują tylko tls w wersji 1.2.

Zarówno TLS, jak i SSL opierają się na *certyfikatach cyfrowych,* które pomagają w szyfrowaniu i weryfikacji tożsamości. Aby uzyskać więcej informacji na temat działania certyfikatów cyfrowych, zobacz temat Wikipedia [Infrastruktura klucza publicznego](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Jeśli nie używasz protokołu HTTPS w usłudze sieci Web, dane wysyłane do i z usługi mogą być widoczne dla innych osób w Internecie.
>
> Protokół HTTPS umożliwia również klientowi sprawdzenie autentyczności serwera, z którego się łączy. Ta funkcja chroni klientów przed atakami [typu man-in-the-middle.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Jest to ogólny proces zabezpieczania usługi sieci web:

1. Pobierz nazwę domeny.

2. Uzyskaj certyfikat cyfrowy.

3. Wdrażanie lub aktualizowanie usługi sieci web z włączoną funkcją protokołu TLS.

4. Zaktualizuj system DNS, aby wskazywał usługę sieci Web.

> [!IMPORTANT]
> Jeśli wdrażasz w usłudze Azure Kubernetes Service (AKS), możesz kupić własny certyfikat lub użyć certyfikatu dostarczonego przez firmę Microsoft. Jeśli używasz certyfikatu firmy Microsoft, nie musisz utrzymywać nazwy domeny ani certyfikatu TLS/SSL. Aby uzyskać więcej informacji, zobacz [włącz tls i wdrożyć](#enable) sekcję tego artykułu.

Istnieją niewielkie różnice, gdy zabezpieczasz s między [celami wdrażania.](how-to-deploy-and-where.md)

## <a name="get-a-domain-name"></a>Uzyskaj nazwę domeny

Jeśli nie masz jeszcze nazwy domeny, kup ją od *rejestratora nazw domen*. Proces i cena różnią się między rejestratorami. Rejestrator udostępnia narzędzia do zarządzania nazwą domeny. Narzędzia te umożliwiają mapowanie w pełni kwalifikowanej nazwy domeny (FQDN) (takiej jak www\.contoso.com) na adres IP obsługujący usługę sieci web.

## <a name="get-a-tlsssl-certificate"></a>Uzyskaj certyfikat TLS/SSL

Istnieje wiele sposobów uzyskania certyfikatu TLS/SSL (certyfikatu cyfrowego). Najczęściej jest zakup jednego z *urzędu certyfikacji(CA).* Niezależnie od tego, gdzie otrzymasz certyfikat, potrzebne są następujące pliki:

* **Certyfikat**. Certyfikat musi zawierać pełny łańcuch certyfikatów i musi być "zakodowany w trybie PEM".
* **Klucz**. Klucz musi być również zakodowany w pem.

Podczas żądania certyfikatu należy podać numer FQDN adresu, który ma być używany w\.usłudze sieci web (na przykład www contoso.com). Adres, który jest stemplowany w certyfikacie i adres, który klienci używają są porównywane do weryfikacji tożsamości usługi sieci web. Jeśli te adresy nie są zgodne, klient otrzymuje komunikat o błędzie.

> [!TIP]
> Jeśli urząd certyfikacji nie może podać certyfikatu i klucza jako plików zakodowanych w formacie PEM, można zmienić format za pomocą narzędzia, takiego jak [OpenSSL.](https://www.openssl.org/)

> [!WARNING]
> Certyfikaty *z podpisem własnym należy używać* tylko do tworzenia. Nie używaj ich w środowiskach produkcyjnych. Certyfikaty z podpisem własnym mogą powodować problemy w aplikacjach klienckich. Aby uzyskać więcej informacji, zobacz dokumentację bibliotek sieciowych używanych przez aplikację kliencką.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Włączanie protokołu TLS i wdrażanie

Aby wdrożyć (lub ponownie wdrożyć) usługę z włączoną funkcją TLS, ustaw *parametr ssl_enabled* na "True", gdziekolwiek ma zastosowanie. Ustaw *ssl_certificate* parametr na wartość pliku *certyfikatu.* Ustaw *ssl_key* na wartość pliku *klucza.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Wdrażanie w udrożeniu w udręki AKS i tablicy bramek programowalnych w terenie (FPGA)

  > [!NOTE]
  > Informacje zawarte w tej sekcji mają również zastosowanie podczas wdrażania bezpiecznej usługi sieci web dla projektanta. Jeśli nie znasz przy użyciu zestawu SDK języka Python, zobacz [Co to jest zestaw SDK usługi Azure Machine Learning dla języka Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Podczas wdrażania w udręki można utworzyć nowy klaster usługi AKS lub dołączyć istniejący. Aby uzyskać więcej informacji na temat tworzenia lub dołączania klastra, zobacz [Wdrażanie modelu w klastrze usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
  
-  W przypadku tworzenia nowego klastra należy użyć **[pliku AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Po dołączeniu istniejącego klastra należy użyć **[pliku AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Oba zwracają obiekt konfiguracji, który ma **metodę enable_ssl.**

Metoda **enable_ssl** może używać certyfikatu dostarczonego przez firmę Microsoft lub zakupionego certyfikatu.

  * Korzystając z certyfikatu firmy Microsoft, należy użyć parametru *leaf_domain_label.* Ten parametr generuje nazwę DNS usługi. Na przykład wartość "contoso" tworzy nazwę domeny "contoso\<sześciu losowych znaków>. \<azureregion>.cloudapp.azure.com", gdzie \<azureregion> jest regionem, który zawiera usługę. Opcjonalnie można użyć parametru *overwrite_existing_domain,* aby zastąpić istniejący *leaf_domain_label*.

    Aby wdrożyć (lub ponownie wdrożyć) usługę z włączoną funkcją TLS, ustaw *parametr ssl_enabled* na "True", gdziekolwiek ma zastosowanie. Ustaw *ssl_certificate* parametr na wartość pliku *certyfikatu.* Ustaw *ssl_key* na wartość pliku *klucza.*

    > [!IMPORTANT]
    > Korzystając z certyfikatu firmy Microsoft, nie trzeba kupować własnego certyfikatu ani nazwy domeny.

    W poniższym przykładzie pokazano, jak utworzyć konfigurację, która umożliwia certyfikat TLS/SSL firmy Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * W przypadku korzystania z *zakupionego certyfikatu*należy użyć parametrów *ssl_cert_pem_file*, *ssl_key_pem_file*i *ssl_cname.* W poniższym przykładzie pokazano, jak używać plików *pem* do tworzenia konfiguracji korzystającej z zakupionego certyfikatu TLS/SSL:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Aby uzyskać więcej informacji na temat *enable_ssl,* zobacz [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) i [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Wdrażanie w przypadku wystąpień kontenerów platformy Azure

Podczas wdrażania w wystąpieniach kontenera platformy Azure należy podać wartości parametrów związanych z TLS, zgodnie z następującym fragmentem kodu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Aby uzyskać więcej informacji, zobacz [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aktualizowanie systemu DNS

Następnie należy zaktualizować system DNS, aby wskazywał usługę sieci web.

+ **W przypadku wystąpień kontenerów:**

  Użyj narzędzi rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać adres IP usługi.

  Może wystąpić opóźnienie minut lub godzin, zanim klienci będą mogli rozpoznać nazwę domeny, w zależności od rejestratora i "czasu wygaśnięcia" (TTL), który jest skonfigurowany dla nazwy domeny.

+ **Dla AKS:**

  > [!WARNING]
  > Jeśli do utworzenia usługi użyto *leaf_domain_label* przy użyciu certyfikatu firmy Microsoft, nie aktualizuj ręcznie wartości DNS klastra. Wartość powinna być ustawiana automatycznie.

  Zaktualizuj dns publicznego adresu IP klastra AKS na karcie **Konfiguracja** w obszarze **Ustawienia** w lewym okienku. (Zobacz poniższą ilustrację). Publiczny adres IP to typ zasobu utworzony w ramach grupy zasobów zawierającej węzły agenta AKS i inne zasoby sieciowe.

  [![Uczenie maszynowe platformy Azure: zabezpieczanie usług sieci web za pomocą protokołu TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Aktualizowanie certyfikatu TLS/SSL

Certyfikaty TLS/SSL wygasają i muszą zostać odnowione. Zazwyczaj dzieje się tak co roku. Użyj informacji w poniższych sekcjach, aby zaktualizować i odnowić certyfikat dla modeli wdrożonych w usłudze Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualizowanie certyfikatu wygenerowanego przez firmę Microsoft

Jeśli certyfikat został pierwotnie wygenerowany przez firmę Microsoft (podczas korzystania z *leaf_domain_label* do utworzenia usługi), użyj jednego z następujących przykładów, aby zaktualizować certyfikat:

**Korzystanie z SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Korzystanie z interfejsu wiersza polecenia**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Aby uzyskać więcej informacji, zobacz następujące dokumenty referencyjne:

* [Konfiguracja SslConfiguracja](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [Konfiguracja AksUpdate](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aktualizowanie certyfikatu niestandardowego

Jeśli certyfikat został pierwotnie wygenerowany przez urząd certyfikacji, należy wykonać następujące czynności:

1. Aby odnowić certyfikat, użyj dokumentacji dostarczonej przez urząd certyfikacji. Ten proces tworzy nowe pliki certyfikatów.

1. Użyj sdk lub interfejsu wiersza polecenia, aby zaktualizować usługę o nowy certyfikat:

    **Korzystanie z SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Korzystanie z interfejsu wiersza polecenia**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Aby uzyskać więcej informacji, zobacz następujące dokumenty referencyjne:

* [Konfiguracja SslConfiguracja](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [Konfiguracja AksUpdate](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Wyłączanie protokołu TLS

Aby wyłączyć TLS dla modelu wdrożonego w usłudze `SslConfiguration` Azure `status="Disabled"`Kubernetes, utwórz za pomocą , a następnie wykonaj aktualizację:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Następne kroki
Instrukcje:
+ [Korzystanie z modelu uczenia maszynowego wdrożonego jako usługa sieci web](how-to-consume-web-service.md)
+ [Bezpieczne uruchamianie eksperymentów i wnioskowanie wewnątrz sieci wirtualnej platformy Azure](how-to-enable-virtual-network.md)
