---
title: Zabezpieczanie usług sieci Web przy użyciu protokołu SSL
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zabezpieczyć usługę sieci Web, która jest wdrażana za pośrednictwem usługi Azure Machine Learning, włączając protokół HTTPS. Protokół HTTPS zabezpiecza dane przez klientów przy użyciu protokołu TLS (Transport Layer Security), zastępując je warstwą Secure Sockets (SSL). Klienci używają również protokołu HTTPS do weryfikowania tożsamości usługi sieci Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: ee8af77ce8f3897fdf1cb3da9a125acca28f9419
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358697"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Użyj protokołu SSL, aby zabezpieczyć usługę sieci Web za pośrednictwem Azure Machine Learning

W tym artykule opisano sposób zabezpieczania usługi sieci Web, która jest wdrażana za pomocą usługi Azure Machine Learning.

Używasz [protokołu HTTPS](https://en.wikipedia.org/wiki/HTTPS) , aby ograniczyć dostęp do usług sieci Web i zabezpieczyć dane przesyłane przez klientów. Protokół HTTPS pomaga w zabezpieczeniu komunikacji między klientem a usługą sieci Web przez szyfrowanie komunikacji między nimi. Szyfrowanie używa [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokół TLS jest czasami nazywany *SSL* (SSL), który był POPRZEDNIKIEM protokołu TLS.

> [!TIP]
> Zestaw Azure Machine Learning SDK używa terminu "SSL" dla właściwości, które są związane z bezpieczną komunikacją. Nie oznacza to, że usługa sieci Web nie korzysta z *protokołu TLS*. Protokół SSL jest zaledwie częściej uznawany za okres.

Protokoły TLS i SSL są zależne od *certyfikatów cyfrowych*, które pomagają w szyfrowaniu i weryfikacji tożsamości. Aby uzyskać więcej informacji na temat sposobu działania certyfikatów cyfrowych, zobacz temat [infrastruktura kluczy publicznych](https://en.wikipedia.org/wiki/Public_key_infrastructure)tematu witryny Wikipedia.

> [!WARNING]
> Jeśli nie używasz protokołu HTTPS dla usługi sieci Web, dane wysyłane do i z usługi mogą być widoczne dla innych osób w Internecie.
>
> Protokół HTTPS umożliwia również klientowi zweryfikowanie autentyczności serwera, z którym jest nawiązywane połączenie. Ta funkcja chroni klientów przed atakami typu [man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Jest to ogólny proces zabezpieczania usługi sieci Web:

1. Pobierz nazwę domeny.

2. Pobierz certyfikat cyfrowy.

3. Wdróż lub zaktualizuj usługę sieci Web z włączonym protokołem SSL.

4. Zaktualizuj serwer DNS, aby wskazać usługę sieci web.

> [!IMPORTANT]
> W przypadku wdrażania w usłudze Azure Kubernetes Service (AKS) można zakupić własny certyfikat lub użyć certyfikatu dostarczonego przez firmę Microsoft. Jeśli używasz certyfikatu od firmy Microsoft, nie musisz uzyskać nazwy domeny ani certyfikatu SSL. Aby uzyskać więcej informacji, zobacz sekcję [Włączanie protokołu SSL i wdrażania](#enable) w tym artykule.

Istnieją niewielkie różnice w przypadku zabezpieczania usług sieci Web w ramach [celów wdrożenia](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Pobierz nazwę domeny

Jeśli nie masz jeszcze nazwy domeny, Kup ją w *rejestratorze nazw domen*. Proces i cena różnią się między Rejestratorami. Rejestrator udostępnia narzędzia do zarządzania nazwą domeny. Te narzędzia służą do mapowania w pełni kwalifikowanej nazwy domeny (FQDN) (takiej jak\.www contoso.com) na adres IP, który hostuje usługę sieci Web.

## <a name="get-an-ssl-certificate"></a>Uzyskaj certyfikat protokołu SSL

Istnieje wiele sposobów uzyskiwania certyfikatu SSL (certyfikatu cyfrowego). Najbardziej powszechnym celem jest zakupienie jednego z *urzędów* certyfikacji (CA). Bez względu na to, gdzie otrzymujesz certyfikat, potrzebne są następujące pliki:

* A **certyfikatu**. Certyfikat musi zawierać pełny łańcuch certyfikatów i musi być "zakodowany przez PEM".
* A **klucz**. Klucz musi być również zakodowany przez PEM.

Żądając certyfikatu, należy podać nazwę FQDN adresu, który ma być używany przez usługę sieci Web (na przykład www\.contoso.com). Adres, który jest umieszczony w certyfikacie i adres używany przez klientów, jest porównywany w celu zweryfikowania tożsamości usługi sieci Web. Jeśli te adresy nie są zgodne, klient otrzymuje komunikat o błędzie.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikatu i klucza jako plików zakodowanych przez PEM, można użyć narzędzia, takiego jak [OpenSSL](https://www.openssl.org/) , aby zmienić format.

> [!WARNING]
> Używaj certyfikatów z podpisem *własnym* tylko do celów deweloperskich. Nie używaj ich w środowiskach produkcyjnych. Certyfikaty z podpisem własnym może powodować problemy w swoim kliencie aplikacji. Aby uzyskać więcej informacji, zobacz dokumentację bibliotek sieciowych używanych przez aplikację kliencką.

## <a id="enable"></a>Włącz protokół SSL i Wdróż

Aby wdrożyć (lub ponownie wdrożyć) usługę z włączonym protokołem SSL, należy ustawić parametr *ssl_enabled* na wartość "true" wszędzie tam, gdzie ma to zastosowanie. Dla parametru *ssl_certificate* ustaw wartość pliku *certyfikatu* . Ustaw *ssl_key* na wartość pliku *klucza* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Wdróż w AKS i programowalnej bramie (FPGA)

  > [!NOTE]
  > Informacje przedstawione w tej sekcji dotyczą również wdrażania bezpiecznej usługi sieci Web dla interfejsu wizualizacji. Jeśli nie masz doświadczenia w korzystaniu z zestawu SDK języka Python, zobacz artykuł [co to jest zestaw Azure Machine Learning SDK dla języka Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Podczas wdrażania programu do AKS można utworzyć nowy klaster AKS lub dołączyć istniejący.
  
-  W przypadku tworzenia nowego klastra należy użyć **[AksCompute. provisionining_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Jeśli dołączysz istniejący klaster, użyj **[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Oba zwracają obiekt konfiguracji, który ma metodę **enable_ssl** .

Metoda **enable_ssl** może używać certyfikatu dostarczonego przez firmę Microsoft lub zakupionego certyfikatu.

  * W przypadku korzystania z certyfikatu firmy Microsoft należy użyć parametru *leaf_domain_label* . Ten parametr generuje nazwę DNS usługi. Na przykład wartość "Moja usługa" tworzy nazwę domeny "polecenie" >\<sześć losowo-znaków ".\< azureregion >. cloudapp. Azure. com ", gdzie \<azureregion > to region, w którym znajduje się usługa. Opcjonalnie można użyć parametru *overwrite_existing_domain* , aby zastąpić istniejący *leaf_domain_label*.

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

  * Korzystając z zakupionego *certyfikatu*, należy użyć parametrów *ssl_cert_pem_file*, *ssl_key_pem_file*i *ssl_cname* . Poniższy przykład ilustruje sposób użycia plików *PEM* do utworzenia konfiguracji korzystającej z zakupionego certyfikatu SSL:

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

## <a name="update-your-dns"></a>Zaktualizuj serwer DNS

Następnie należy zaktualizować serwer DNS, aby wskazać usługę sieci web.

+ **Dla Container Instances:**

  Użyj narzędzi z rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać adres IP usługi.

  Może wystąpić opóźnienie minut lub godzin, po upływie którego klienci mogą rozpoznać nazwę domeny, w zależności od rejestratora i "czasu wygaśnięcia", który jest skonfigurowany dla nazwy domeny.

+ **Dla AKS:**

  > [!WARNING]
  > Jeśli usługa *leaf_domain_label* została użyta do utworzenia usługi przy użyciu certyfikatu firmy Microsoft, nie należy ręcznie aktualizować wartości DNS dla klastra. Wartość powinna być ustawiana automatycznie.

  Zaktualizuj system DNS na karcie **Konfiguracja** publicznego adresu IP klastra AKS. (Zobacz poniższy obraz). Publiczny adres IP to typ zasobu, który jest tworzony w ramach grupy zasobów zawierającej węzły agenta AKS i inne zasoby sieciowe.

  ![Usługa Azure Machine Learning: Zabezpieczanie usług sieci Web przy użyciu protokołu SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Następne kroki
Instrukcje:
+ [Korzystanie z modelu uczenia maszynowego wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
+ [Bezpieczne uruchamianie eksperymentów i wnioskowania wewnątrz sieci wirtualnej platformy Azure](how-to-enable-virtual-network.md)
