---
title: Jak wdrożyć model jako usługę sieci web na układu FPGA przy użyciu usługi Azure Machine Learning
description: Dowiedz się, jak wdrożyć usługę sieci web z modelem systemem układu FPGA przy użyciu usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Wdróż model jako usługę sieci web na układu FPGA przy użyciu usługi Azure Machine Learning

W tym dokumencie zostanie przedstawiony sposób konfigurowania środowiska stacji roboczej i wdrożyć ten model jako usługę sieci web na [pola Tablice bramek programowane (układu FPGA)](concept-accelerate-with-fpgas.md). Usługa sieci web używa Brainwave projektu do uruchamiania na układu FPGA modelu.

Używanie FPGAs zapewnia inferencing bardzo niskim opóźnieniem, nawet w przypadku pojedynczej partii o rozmiarze.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Tworzenie konta usługi Azure Machine Learning Model zarządzania

1. Przejdź do strony tworzenia konta zarządzania modelu w [Azure Portal](https://aka.ms/aml-create-mma).

2. W portalu, Utwórz konto zarządzania modelu w **wschodnie stany USA 2** regionu.

   ![Obraz ekranu Utwórz konto zarządzania modelu](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Nadaj nazwę konta Model zarządzania, wybierz subskrypcję i wybierz grupę zasobów.

   >[!IMPORTANT]
   >Lokalizacja, musisz wybrać **wschodnie stany USA 2** jako obszar.  Nie inne regiony są obecnie dostępne.

4. Wybierz warstwę cenową (S1 jest wystarczająca, ale również działać S2 i S3).  Warstwa DevTest nie jest obsługiwana.  

5. Kliknij przycisk **wybierz** o potwierdzenie warstwę cenową.

6. Kliknij przycisk **Utwórz** Zarządzanie modelu uczenia Maszynowego po lewej stronie.

## <a name="get-model-management-account-information"></a>Uzyskaj informacje o koncie Model zarządzania

Aby uzyskać informacje o Twojej modelu administracyjnego konta (MMA), kliknij przycisk __konto zarządzania modelu__ w portalu Azure.

Skopiuj wartości z następujących elementów:

+ Nazwa konta zarządzania modelu (w w lewym górnym rogu)
+ Nazwa grupy zasobów
+ Identyfikator subskrypcji
+ Lokalizacja (Użyj "eastus2")

![Informacje o koncie zarządzania modelu](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Konfigurowanie komputera

Aby skonfigurować stację roboczą do układu FPGA wdrożenia, wykonaj następujące kroki:

1. Pobierz i zainstaluj najnowszą wersję [Git](https://git-scm.com/downloads).

2. Zainstaluj [Anaconda (Python 3,6)](https://conda.io/miniconda.html).

3. Aby pobrać środowiska Anaconda, użyj następującego polecenia w wierszu Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Aby utworzyć środowisko, otwórz **wiersza Anaconda** (Brak monitów Azure Machine Learning Workbench) i uruchom następujące polecenie:

    > [!IMPORTANT]
    > `environment.yml` Plik znajduje się w repozytorium git sklonowany w poprzednim kroku. Zmień ścieżkę, w razie potrzeby, aby wskazać plik na stację roboczą.

    ```
    conda env create -f environment.yml
    ```

5. Aby aktywować środowiska, użyj następującego polecenia:

    ```
    conda activate amlrealtimeai
    ```

6. Aby uruchomić serwera notesu Jupyter, użyj następującego polecenia:

    ```
    jupyter notebook
    ```

    Dane wyjściowe tego polecenia jest podobny do następującego tekstu:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Inny token zostanie wyświetlony każdorazowo po uruchomieniu polecenia.

    Jeśli przeglądarka nie zostanie automatycznie uruchomiony z notesem Jupyter, użyj adresu URL HTTP zwrócony przez poprzednie polecenie, aby otworzyć stronę.

    ![Obraz strony sieci web notesu Jupyter](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Wdróż model

Z notesu Jupyter Otwórz `00_QuickStart.ipynb` notesu z `notebooks/resnet50` katalogu. Postępuj zgodnie z instrukcjami w notesie do:

* Zdefiniuj usługę
* Wdrażanie modelu
* Korzystanie z wdrożonym modelu
* Usunięcie wdrożonych usług

> [!IMPORTANT]
> Aby zoptymalizować opóźnienia i przepływności, stacji roboczej powinna być w tym samym regionie Azure jako punktu końcowego.  Obecnie interfejsy API są tworzone w regionie wschodnie nam Azure.

## <a name="ssltls-and-authentication"></a>SSL/TLS i uwierzytelniania

Usługa Azure Machine Learning zapewnia obsługę protokołu SSL i uwierzytelniania opartego na kluczach. Dzięki temu można ograniczyć dostęp do usługi i zabezpieczonych danych przesyłanych przez klientów.

> [!NOTE]
> Kroki opisane w tej sekcji dotyczą tylko Azure Machine Learning sprzętu przyspieszony modeli. Dotyczących standardowych usług Azure Machine Learning, zobacz [jak skonfigurować protokół SSL dla usługi Azure Machine Learning obliczeniowe](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) dokumentu.

> [!IMPORTANT]
> Uwierzytelnianie jest włączone tylko dla usług, które zostały podane certyfikat i klucz. 
>
> Jeśli protokół SSL nie jest włączone, każdy użytkownik w Internecie będzie do nawiązywania połączeń z usługą.
>
> Jeśli włączenie protokołu SSL i uwierzytelniania jest wymagany w przypadku uzyskiwania dostępu do usługi.

Protokół SSL szyfruje dane przesyłane między klientem a usługą. On również używany przez klienta, aby sprawdzić tożsamość serwera.

Możesz wdrożyć usługę z włączonym protokołem SSL lub zaktualizować już wdrożonej usługi, aby je włączyć. Kroki są takie same:

1. Uzyskać nazwę domeny.

2. Należy uzyskać certyfikat SSL.

3. Wdrożeniem lub aktualizację usługi z włączonym protokołem SSL.

4. Zaktualizuj systemie DNS, aby wskazywał usługi.

### <a name="acquire-a-domain-name"></a>Uzyskanie nazwy domeny

Jeśli nie ma już nazwę domeny, możesz kupić ją od __rejestratora nazw domen__. Proces jest różny od rejestratorów, tak jak w przypadku kosztów. Rejestratora także udostępnia narzędzia do zarządzania nazwy domeny. Te narzędzia są używane do mapowania w pełni kwalifikowaną nazwą domeny (np. www.contoso.com) do usługi hostowanej na adres IP.

### <a name="acquire-an-ssl-certificate"></a>Uzyskanie certyfikatu SSL.

Istnieje wiele sposobów, aby uzyskać certyfikat SSL. Najbardziej typowe ma kupić ją od __urzędu certyfikacji__ (CA). Niezależnie od tego, gdzie można uzyskać certyfikat potrzebne są następujące pliki:

* A __certyfikatu__. Certyfikat musi zawierać łańcucha certyfikatów pełne i musi być zakodowany w formacie PEM.
* A __klucza__. Klucz musi być zakodowany w formacie PEM.

> [!TIP]
> Jeśli urząd certyfikacji nie może dostarczyć certyfikat i klucz jako pliki zakodowane w formacie PEM, można użyć narzędzia takie jak [OpenSSL](https://www.openssl.org/) zmienić format.

> [!IMPORTANT]
> Certyfikaty z podpisem własnym używanego tylko do tworzenia. Ich nie można używać w środowisku produkcyjnym.
>
> Jeśli używasz certyfikatu z podpisem własnym, zobacz [korzystających z tych usług z certyfikatów z podpisem własnym](#self-signed) sekcji, aby uzyskać szczegółowe instrukcje.

> [!WARNING]
> Podczas żądania certyfikatu, podaj w pełni kwalifikowaną nazwę (FQDN) adresu, który planujesz używać usługi. Na przykład www.contoso.com. Adres oznaczony do certyfikatu i adres używany przez klientów są porównywane podczas weryfikowania tożsamości usługi.
>
> Jeśli adres nie są zgodne, klienci spowoduje wystąpienie błędu. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Wdrożeniem lub aktualizację usługi z włączonym protokołem SSL

Aby wdrożyć usługę z włączonym protokołem SSL, należy ustawić `ssl_enabled` parametr `True`. Ustaw `ssl_certificate` parametru z wartością __certyfikatu__ pliku i `ssl_key` wartość __klucza__ pliku. W poniższym przykładzie pokazano wdrażania usługi z włączonym protokołem SSL:

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

Odpowiedź `create_service` operacji zawiera adres IP usługi. Adres IP jest używany podczas mapowania nazw DNS na adres IP usługi.

Odpowiedź zawiera także __klucza podstawowego__ i __klucza pomocniczego__ służące do korzystania z usługi.

### <a name="update-your-dns-to-point-to-the-service"></a>Zaktualizuj systemie DNS, aby wskazywał usługi

Korzystania z narzędzi dostarczanych przez rejestratora nazw domen do zaktualizowania rekordu DNS dla nazwy domeny. Rekord musi wskazywać na adres IP usługi.

> [!NOTE]
> W zależności od rejestratora i czas wygaśnięcia (TTL) skonfigurowany dla nazwy domeny, może upłynąć kilka minut do kilku godzin, zanim klienci mogą rozpoznać nazwę domeny.

### <a name="consuming-authenticated-services"></a>Korzystające usługi uwierzytelnionego

W poniższych przykładach pokazano, jak korzystać z usługi uwierzytelniony przy użyciu języka Python i C#:

> [!NOTE]
> Zastąp `authkey` kluczem podstawowym lub pomocniczym zwrócony podczas tworzenia usługi.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Innym klientom gRPC może uwierzytelnić żądania przez ustawienie Nagłówek uwierzytelnienia. Ogólne podejście jest utworzenie `ChannelCredentials` obiekt, który łączy `SslCredentials` z `CallCredentials`. To jest dodawany do nagłówku autoryzacji żądania. Aby uzyskać więcej informacji na obsługę nagłówkach określonej implementacji, zobacz [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Poniższe przykłady pokazują, jak można ustawić nagłówka w języku C#, a następnie przejść:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

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

### <a id="self-signed"></a>Korzystanie z usług za pomocą certyfikatów z podpisem własnym

Istnieją dwa sposoby klienta do uwierzytelniania na serwerze zabezpieczone przy użyciu certyfikatu z podpisem własnym:

* W systemie klienckim ustawiona `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` zmiennej środowiskowej, aby wskazać plik certyfikatu w systemie klienta.

* Podczas konstruowania `SslCredentials` obiektu, do konstruktora przekazać zawartość pliku certyfikatu.

Przy użyciu jednej z metod powoduje, że gRPC do używania certyfikatu jako certyfikatu głównego.

> [!IMPORTANT]
> gRPC nie akceptuje certyfikatów niezaufanych. Za pomocą niezaufanego certyfikatu kończy się niepowodzeniem z `Unavailable` kod stanu. Szczegóły dotyczące błędu zawierają `Connection Failed`.
