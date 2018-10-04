---
title: Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL
description: Dowiedz się, jak zabezpieczyć usługę sieci web wdrażane za pomocą usługi Azure Machine Learning. Można ograniczyć dostęp do usług sieci web i zabezpieczanie danych przesyłanych przez klientów przy użyciu warstwy SSL (SSL) i uwierzytelniania opartego na kluczu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 8a26491acc7215598e57ce6074fffe26a4374a96
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251087"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Zabezpieczania usług sieci web Azure Machine Learning przy użyciu protokołu SSL

W tym artykule dowiesz się, jak zabezpieczyć usługę sieci web wdrażane za pomocą usługi Azure Machine Learning. Można ograniczyć dostęp do usług sieci web i zabezpieczanie danych przesyłanych przez klientów przy użyciu warstwy SSL (SSL) i uwierzytelniania opartego na kluczu.

> [!Warning]
> Jeśli protokół SSL nie jest włączona, każdy użytkownik w Internecie będzie do nawiązywania połączeń z usługą sieci web.

Protokół SSL są szyfrowane dane przesyłane między klientem a usługą sieci web. On również używany przez klienta, aby zweryfikować tożsamość serwera. Uwierzytelnianie jest włączona tylko dla usług, które zostały podane certyfikat i klucz.  Po włączeniu protokołu SSL, klucz uwierzytelniania jest wymagane podczas uzyskiwania dostępu do usługi sieci web.

Wdrażanie usługi sieci web włączone przy użyciu protokołu SSL lub włączyć protokół SSL dla istniejących wdrożonej usługi sieci web, kroki są takie same:

1. Pobierz nazwę domeny.

2. Uzyskaj certyfikat protokołu SSL.

3. Wdrażanie lub zaktualizować usługę sieci web przy użyciu ustawienia protokołu SSL, włączona.

4. Zaktualizuj serwer DNS, aby wskazać usługę sieci web.

Istnieją drobne różnice podczas zabezpieczania usług sieci web w [celów wdrażania](how-to-deploy-and-where.md). 

## <a name="get-a-domain-name"></a>Pobierz nazwę domeny

Jeśli nie jesteś już właścicielem nazwy domeny, możesz kupić jeden z __rejestratora nazw domen__. Ten proces różni się między rejestratorów, tak jak koszt. Rejestrator również udostępnia narzędzia do zarządzania nazwy domeny. Te narzędzia są używane do mapowania w pełni kwalifikowaną nazwę domeny (np. www.contoso.com) do adresu IP, obsługującego usługi sieci web.

## <a name="get-an-ssl-certificate"></a>Uzyskaj certyfikat protokołu SSL

Istnieje wiele sposobów, aby uzyskać certyfikat SSL. Najczęściej jest nabyć jeden z __urzędu certyfikacji__ (CA). Niezależnie od tego, gdzie można uzyskać certyfikat potrzebne są następujące pliki:

* A __certyfikatu__. Certyfikat musi zawierać łańcucha certyfikatów pełnego i musi być zakodowany w formacie PEM.
* A __klucz__. Klucz musi być zakodowany w formacie PEM.

Podczas żądania certyfikatu, podaj w pełni kwalifikowana nazwa domeny (FQDN) adresu, który ma być używany przez usługę sieci web. Na przykład www.contoso.com. Adres, który został oznaczony do certyfikatu i adres używany przez klientów, są porównywane podczas weryfikowania tożsamości usługi sieci web. Jeśli adresy różnią się od siebie, klienci spowoduje wystąpienie błędu. 

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikat i klucz jako pliki zakodowane w formacie PEM, możesz użyć narzędzia takie jak [OpenSSL](https://www.openssl.org/) zmian w formacie.

> [!IMPORTANT]
> Certyfikaty z podpisem własnym należy używać tylko w celach deweloperskich. Nie powinny one być używane w środowisku produkcyjnym. Jeśli używasz certyfikatu z podpisem własnym, zobacz [korzystanie z usług sieci web za pomocą certyfikatów z podpisem własnym](#self-signed) sekcji, aby uzyskać szczegółowe instrukcje.


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

+ **Wdrażanie na tablicach programowalny gamma pola (układów FPGA)**

  Odpowiedź `create_service` operacja zawiera adres IP usługi. Adres IP jest używany, gdy mapowanie nazwy DNS na adres IP usługi. Odpowiedź zawiera również __klucza podstawowego__ i __klucz pomocniczy__ służące do korzystania z usługi. Podaj wartości parametrów związanych z protokołu SSL, jak pokazano we fragmencie kodu:

    ```python
    from amlrealtimeai import DeploymentClient
    
    subscription_id = "<Your Azure Subscription ID>"
    resource_group = "<Your Azure Resource Group Name>"
    model_management_account = "<Your AzureML Model Management Account Name>"
    location = "eastus2"
    
    model_name = "resnet50-model"
    service_name = "quickstart-service"
    
    deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)
    
    with open('cert.pem','r') as cert_file:
        with open('key.pem','r') as key_file:
            cert = cert_file.read()
            key = key_file.read()
            service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
    ```

## <a name="update-your-dns"></a>Zaktualizuj serwer DNS

Następnie należy zaktualizować serwer DNS, aby wskazać usługę sieci web.

+ **ACI i FPGA**:  

  Użyj narzędzi dostarczanych przez rejestratora nazw domen, aby zaktualizować rekord DNS dla nazwy domeny. Rekord musi wskazywać na adres IP usługi.  

  W zależności od Rejestrator i czas wygaśnięcia (TTL) skonfigurowany dla nazwy domeny, może upłynąć kilka minut do kilku godzin, zanim klienci mogą rozpoznać nazwę domeny.

+ **Dla usługi AKS**: 

  Aktualizowanie systemu DNS, na karcie "Konfiguracja" "Publiczny adres IP" dla klastra AKS, jak pokazano na ilustracji. Możesz znaleźć publiczny adres IP jako jeden z typów zasobów utworzonych w ramach grupy zasobów, która zawiera węzły agenta usługi AKS i innych zasobów sieciowych.

  [ ![Usługa Azure Machine Learning: Zabezpieczanie usługi sieci web przy użyciu protokołu SSL](./media/how-to-secure-web-service/aks-public-ip-address.png) ] ((.media/how-to-secure-web-service/aks-public-ip-address.png#lightbox)

## <a name="consume-authenticated-services"></a>Używanie usług uwierzytelnionego

### <a name="how-to-consume"></a>Jak używać 
+ **ACI i AKS**: 

  Usługami ACI i AKS usług sieci web Dowiedz się, jak korzystać z usług sieci web w następujących artykułach:
  + [Jak wdrożyć w usłudze ACI](how-to-deploy-to-aci.md)

  + [Sposób wdrażania usługi AKS](how-to-deploy-to-aks.md)

+ **ACI i FPGA**:  

  Poniższe przykłady pokazują, jak używać usługi FPGA uwierzytelniony w języku Python i C#.
  Zastąp `authkey` z kluczem podstawowym lub pomocniczym, który został zwrócony podczas wdrożono usługę.

  Przykład języka Python:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  Przykład C#:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Ustaw nagłówek autoryzacji
Inni klienci gRPC można uwierzytelnić żądania, ustawiając nagłówka autoryzacji. Ogólne podejście jest utworzenie `ChannelCredentials` obiekt, który łączy `SslCredentials` z `CallCredentials`. To jest dodawany do nagłówka autoryzacji żądania. Aby uzyskać więcej informacji dotyczących implementowania pomocy technicznej dla Twojego określonych nagłówków, zobacz [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

W poniższych przykładach pokazano, jak ustawić nagłówek w języku C# i przejdź do sekcji:

+ Używanie języka C#, aby ustawić nagłówek:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Korzystanie z języka Go, aby ustawić nagłówek:
    ```go
    conn, err := grpc.Dial(serverAddr, 
        grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
        grpc.WithPerRPCCredentials(&authCreds{
        Key: "authKey"}))
    
    type authCreds struct {
        Key string
    }
    
    func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
        return map[string]string{
            "authorization": c.Key,
        }, nil
    }
    
    func (c *authCreds) RequireTransportSecurity() bool {
        return true
    }
    ```

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Korzystanie z usług z certyfikatami z podpisem własnym

Istnieją dwa sposoby, aby umożliwić klientowi uwierzytelniać się na serwerze, zabezpieczonego przy użyciu certyfikatu z podpisem własnym:

* W systemie klienta ustaw `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` zmiennej środowiskowej, aby wskazać plik certyfikatu w systemie klienta.

* Podczas tworzenia `SslCredentials` obiektów, Przekaż zawartość pliku certyfikatu do konstruktora.

Przy użyciu jednej z metod powoduje, że gRPC użyć certyfikatu jako certyfikatu głównego.

> [!IMPORTANT]
> gRPC Akceptuj niezaufane certyfikaty. Za pomocą niezaufanego certyfikatu zakończy się niepowodzeniem z `Unavailable` kod stanu. Szczegóły dotyczące błędu zawierają `Connection Failed`.
