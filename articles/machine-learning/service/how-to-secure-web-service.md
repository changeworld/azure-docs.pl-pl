---
title: Zabezpieczanie usług sieci Web przy użyciu protokołu SSL
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak włączyć protokół HTTPS w celu zbyt bezpiecznej usługi sieci Web, która jest wdrażana za pośrednictwem Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: f1021ad1983f78252d924a5d3cb674419732d66e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932055"
---
# <a name="use-ssl-to-secure-a--through-azure-machine-learning"></a>Użyj protokołu SSL do zabezpieczenia za pośrednictwem Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule opisano sposób zabezpieczenia wdrożenia wdrożonego za pomocą Azure Machine Learning.

Używasz [protokołu HTTPS](https://en.wikipedia.org/wiki/HTTPS) , aby ograniczyć dostęp do s i zabezpieczyć dane przesyłane przez klientów. Protokół HTTPS pomaga zabezpieczyć komunikację między klientem a a przez szyfrowanie komunikacji między nimi. Szyfrowanie używa [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest czasami nazywany *SSL* (SSL), który był POPRZEDNIKIEM protokołu TLS.

> [!TIP]
> Zestaw Azure Machine Learning SDK używa terminu "SSL" dla właściwości, które są związane z bezpieczną komunikacją. Nie oznacza to, że *protokół TLS*nie jest używany. Protokół SSL jest zaledwie częściej uznawany za okres.

Protokoły TLS i SSL są zależne od *certyfikatów cyfrowych*, które pomagają w szyfrowaniu i weryfikacji tożsamości. Aby uzyskać więcej informacji na temat sposobu działania certyfikatów cyfrowych, zobacz temat [infrastruktura kluczy publicznych](https://en.wikipedia.org/wiki/Public_key_infrastructure)tematu witryny Wikipedia.

> [!WARNING]
> Jeśli nie używasz protokołu HTTPS, dane wysyłane do i z usługi mogą być widoczne dla innych osób w Internecie.
>
> Protokół HTTPS umożliwia również klientowi zweryfikowanie autentyczności serwera, z którym jest nawiązywane połączenie. Ta funkcja chroni klientów przed atakami typu [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Jest to ogólny proces zabezpieczania:

1. Pobierz nazwę domeny.

2. Pobierz certyfikat cyfrowy.

3. Wdróż lub zaktualizuj włączony protokół SSL.

4. Zaktualizuj system DNS, aby wskazywał.

> [!IMPORTANT]
> W przypadku wdrażania w usłudze Azure Kubernetes Service (AKS) można zakupić własny certyfikat lub użyć certyfikatu dostarczonego przez firmę Microsoft. Jeśli używasz certyfikatu od firmy Microsoft, nie musisz uzyskać nazwy domeny ani certyfikatu SSL. Aby uzyskać więcej informacji, zobacz sekcję [Włączanie protokołu SSL i wdrażania](#enable) w tym artykule.

Istnieją niewielkie różnice w przypadku zabezpieczania serwerów docelowych w ramach [wdrożenia](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Pobierz nazwę domeny

Jeśli nie masz jeszcze nazwy domeny, Kup ją w *rejestratorze nazw domen*. Proces i cena różnią się między Rejestratorami. Rejestrator udostępnia narzędzia do zarządzania nazwą domeny. Te narzędzia służą do mapowania w pełni kwalifikowanej nazwy domeny (FQDN) (takiej jak www\.contoso.com) na adres IP, który hostuje.

## <a name="get-an-ssl-certificate"></a>Pobierz certyfikat SSL

Istnieje wiele sposobów uzyskiwania certyfikatu SSL (certyfikatu cyfrowego). Najbardziej powszechnym celem jest zakupienie jednego z *urzędów* certyfikacji (CA). Bez względu na to, gdzie otrzymujesz certyfikat, potrzebne są następujące pliki:

* **Certyfikat**. Certyfikat musi zawierać pełny łańcuch certyfikatów i musi być "zakodowany przez PEM".
* **Klucz**. Klucz musi być również zakodowany przez PEM.

W przypadku żądania certyfikatu należy podać nazwę FQDN adresu, który ma być używany dla programu (na przykład www\.contoso.com). Adres, który jest opatrzony sygnaturą i adresem używanym przez klientów, jest porównywany w celu zweryfikowania tożsamości. Jeśli te adresy nie są zgodne, klient otrzymuje komunikat o błędzie.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikatu i klucza jako plików zakodowanych przez PEM, można użyć narzędzia, takiego jak [OpenSSL](https://www.openssl.org/) , aby zmienić format.

> [!WARNING]
> Używaj certyfikatów z podpisem *własnym* tylko do celów deweloperskich. Nie używaj ich w środowiskach produkcyjnych. Certyfikaty z podpisem własnym mogą spowodować problemy w aplikacjach klienckich. Aby uzyskać więcej informacji, zobacz dokumentację bibliotek sieciowych używanych przez aplikację kliencką.

## <a id="enable"></a>Włącz protokół SSL i Wdróż

Aby wdrożyć (lub ponownie wdrożyć) usługę z włączonym protokołem SSL, należy ustawić parametr *ssl_enabled* na wartość "true" wszędzie tam, gdzie ma to zastosowanie. Dla parametru *ssl_certificate* ustaw wartość pliku *certyfikatu* . Ustaw *ssl_key* na wartość pliku *klucza* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Wdróż w AKS i programowalnej bramie (FPGA)

  > [!NOTE]
  > Informacje przedstawione w tej sekcji dotyczą również wdrażania bezpiecznego dla projektanta. Jeśli nie masz doświadczenia w korzystaniu z zestawu SDK języka Python, zobacz artykuł [co to jest zestaw Azure Machine Learning SDK dla języka Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Podczas wdrażania programu do AKS można utworzyć nowy klaster AKS lub dołączyć istniejący. Aby uzyskać więcej informacji na temat tworzenia lub dołączania klastra, zobacz [Wdrażanie modelu w klastrze usługi Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).
  
-  W przypadku tworzenia nowego klastra należy użyć **[AksCompute. provisionining_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Jeśli dołączysz istniejący klaster, użyj **[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Oba zwracają obiekt konfiguracji, który ma metodę **enable_ssl** .

Metoda **enable_ssl** może korzystać z certyfikatu dostarczonego przez firmę Microsoft lub zakupionego certyfikatu.

  * Korzystając z certyfikatu firmy Microsoft, należy użyć parametru *leaf_domain_label* . Ten parametr generuje nazwę DNS usługi. Na przykład wartość "Moja usługa" tworzy nazwę domeny "WebService\<sześć znaków losowych >.\<azureregion >. cloudapp. Azure. com ", gdzie \<azureregion > to region, w którym znajduje się usługa. Opcjonalnie można użyć parametru *overwrite_existing_domain* , aby zastąpić istniejące *leaf_domain_label*.

    Aby wdrożyć (lub ponownie wdrożyć) usługę z włączonym protokołem SSL, należy ustawić parametr *ssl_enabled* na wartość "true" wszędzie tam, gdzie ma to zastosowanie. Dla parametru *ssl_certificate* ustaw wartość pliku *certyfikatu* . Ustaw *ssl_key* na wartość pliku *klucza* .

    > [!IMPORTANT]
    > Jeśli używasz certyfikatu od firmy Microsoft, nie musisz kupować własnego certyfikatu ani nazwy domeny.

    Poniższy przykład pokazuje, jak utworzyć konfigurację, która umożliwia certyfikat SSL od firmy Microsoft:

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

  * Korzystając z *zakupionego certyfikatu*, należy użyć parametrów *ssl_cert_pem_file*, *ssl_key_pem_file*i *ssl_cname* . Poniższy przykład ilustruje sposób użycia plików *PEM* do utworzenia konfiguracji korzystającej z zakupionego certyfikatu SSL:

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

Aby uzyskać więcej informacji na temat *enable_ssl*, zobacz [AksProvisioningConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) i [AksAttachConfiguration. enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Wdróż na Azure Container Instances

Podczas wdrażania programu do Azure Container Instances należy podać wartości parametrów związanych z protokołem SSL, jak pokazano w poniższym fragmencie kodu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Aby uzyskać więcej informacji, zobacz [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Aktualizowanie systemu DNS

Następnie należy zaktualizować system DNS, aby wskazywał.

+ **Dla Container Instances:**

  Użyj narzędzi z rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać adres IP usługi.

  Może wystąpić opóźnienie minut lub godzin, po upływie którego klienci mogą rozpoznać nazwę domeny, w zależności od rejestratora i "czasu wygaśnięcia", który jest skonfigurowany dla nazwy domeny.

+ **Dla AKS:**

  > [!WARNING]
  > Jeśli usługa *leaf_domain_label* została użyta do utworzenia usługi przy użyciu certyfikatu firmy Microsoft, nie należy ręcznie aktualizować wartości DNS dla klastra. Wartość powinna być ustawiana automatycznie.

  Zaktualizuj serwer DNS publicznego adresu IP klastra AKS na karcie **Konfiguracja** w obszarze **Ustawienia** w okienku po lewej stronie. (Zobacz poniższy obraz). Publiczny adres IP to typ zasobu, który jest tworzony w ramach grupy zasobów zawierającej węzły agenta AKS i inne zasoby sieciowe.

  [![Azure Machine Learning: Zabezpieczanie s przy użyciu protokołu SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>Aktualizowanie certyfikatu SSL

Certyfikaty SSL wygasną i należy je odnowić. Zwykle odbywa się to co rok. Skorzystaj z informacji w poniższych sekcjach, aby zaktualizować i odnowić certyfikat dla modeli wdrożonych w usłudze Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualizowanie certyfikatu wygenerowanego przez firmę Microsoft

Jeśli certyfikat został pierwotnie wygenerowany przez firmę Microsoft (w przypadku korzystania z *leaf_domain_label* do tworzenia usługi), użyj jednego z poniższych przykładów, aby zaktualizować certyfikat:

**Korzystanie z zestawu SDK**

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

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aktualizowanie certyfikatu niestandardowego

Jeśli certyfikat został pierwotnie wygenerowany przez urząd certyfikacji, wykonaj następujące czynności:

1. Aby odnowić certyfikat, użyj dokumentacji dostarczonej przez urząd certyfikacji. Ten proces tworzy nowe pliki certyfikatów.

1. Użyj zestawu SDK lub interfejsu wiersza polecenia, aby zaktualizować usługę przy użyciu nowego certyfikatu:

    **Korzystanie z zestawu SDK**

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

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>Wyłącz protokół SSL

Aby wyłączyć protokół SSL dla modelu wdrożonego w usłudze Azure Kubernetes, Utwórz `SslConfiguration` z `status="Disabled"`, a następnie wykonaj aktualizację:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Następne kroki
Instrukcje:
+ [Korzystanie z modelu uczenia maszynowego wdrożonego jako](how-to-consume-web-service.md)
+ [Bezpieczne uruchamianie eksperymentów i wnioskowania wewnątrz sieci wirtualnej platformy Azure](how-to-enable-virtual-network.md)
