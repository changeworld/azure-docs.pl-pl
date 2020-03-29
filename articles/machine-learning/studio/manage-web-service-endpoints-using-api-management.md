---
title: Zarządzanie usługami sieci Web przy użyciu usługi API Management
titleSuffix: ML Studio (classic) - Azure
description: Przewodnik przedstawiający sposób zarządzania usługami sieci Web usługi AzureML przy użyciu usługi API Management. Zarządzanie punktami końcowymi interfejsu API REST, definiując dostęp użytkowników, ograniczanie użycia i monitorowanie pulpitu nawigacyjnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: cbe01ee9b8edeab349db484cea6c25dca32bf213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218023"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Zarządzanie usługami sieci Web usługi Azure Machine Learning Studio (klasyczne) przy użyciu usługi API Management

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak szybko rozpocząć korzystanie z usługi API Management w celu zarządzania usługami sieci Web usługi Azure Machine Learning Studio (klasyczne).

## <a name="what-is-azure-api-management"></a>Co to jest usługa Azure API Management?
Usługa Azure API Management to usługa platformy Azure, która umożliwia zarządzanie punktami końcowymi interfejsu REST przez definiowanie dostępu użytkowników, ograniczania użycia i monitorowania pulpitu nawigacyjnego. Aby uzyskać więcej informacji, zobacz [witrynę zarządzania interfejsem API platformy Azure.](https://azure.microsoft.com/services/api-management/) Aby rozpocząć korzystanie z usługi Azure API Management, zobacz [przewodnik importu i publikowania](/azure/api-management/import-and-publish). Ten inny przewodnik, na którym opiera się ten przewodnik, obejmuje więcej tematów, w tym konfiguracje powiadomień, ustalanie cen warstw, obsługę odpowiedzi, uwierzytelnianie użytkowników, tworzenie produktów, subskrypcje deweloperów i pulpit nawigacyjny użycia.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:

* Konto platformy Azure.
* Konto usługi AzureML.
* Obszar roboczy, usługa i api_key eksperymentu usługi AzureML wdrożonego jako usługa sieci web. Aby uzyskać szczegółowe informacje na temat tworzenia eksperymentu usługi AzureML, zobacz [Przewodnik Szybki start w studio](create-experiment.md). Aby uzyskać informacje na temat wdrażania eksperymentu Studio (klasycznego) jako usługi sieci web, zobacz [instrukcje wdrażania programu Studio, aby uzyskać](deploy-a-machine-learning-web-service.md) szczegółowe informacje na temat wdrażania eksperymentu usługi AzureML jako usługi sieci web. Alternatywnie dodatek A zawiera instrukcje dotyczące tworzenia i testowania prostego eksperymentu usługi AzureML i wdrażania go jako usługi sieci web.

## <a name="create-an-api-management-instance"></a>Tworzenie wystąpienia usługi API Management

Usługę sieci web usługi Azure Machine Learning można zarządzać za pomocą wystąpienia usługi API Management.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób**.
3. W polu wyszukiwania wpisz "Zarządzanie interfejsami API", a następnie wybierz zasób "Zarządzanie interfejsem API".
4. Kliknij przycisk **Utwórz**.
5. Nazwa **Name** wartość zostanie użyta do utworzenia unikatowego adresu URL (w tym przykładzie używa "demoazureml").
6. Wybierz **subskrypcję,** **grupę zasobów**i **lokalizację** dla wystąpienia usługi.
7. Określ wartość **nazwy organizacji** (w tym przykładzie użyto "demoazureml").
8. Wprowadź **adres e-mail administratora** - ta wiadomość e-mail będzie używana do powiadomień z systemu zarządzania interfejsami API.
9. Kliknij przycisk **Utwórz**.

Utworzenie nowej usługi może potrwać do 30 minut.

![tworzenie-usługa](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Tworzenie interfejsu API
Po utworzeniu wystąpienia usługi następnym krokiem jest utworzenie interfejsu API. Interfejs API składa się z zestawu operacji, które mogą być wywoływane z poziomu aplikacji klienckiej. Operacje interfejsu API są przekierowywane do istniejących usług sieci Web. W tym przewodniku utworzy się interfejsy API, które mają wskaźnik proxy do istniejących usług AzureML RRS i BES w sieci web.

Aby utworzyć interfejs API:

1. W witrynie Azure portal otwórz utworzone wystąpienie usługi.
2. W lewym okienku nawigacji wybierz pozycję **Interfejsy API**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kliknij **pozycję Dodaj interfejs API**.
2. Wprowadź **nazwę interfejsu API sieci Web** (w tym przykładzie użyto "AzureML Demo API").
3. W przypadku adresu URL`https://ussouthcentral.services.azureml.net`usługi sieci **Web**wpisz " ".
4. Wprowadź sufiks adresu URL interfejsu API sieci Web". Stanie się to ostatnią częścią adresu URL, który klienci będą używać do wysyłania żądań do wystąpienia usługi (w tym przykładzie użyto "azureml-demo").
5. W przypadku **schematu adresów URL interfejsu API sieci Web**wybierz pozycję **HTTPS**.
6. W przypadku **produktów**wybierz **starter**.
7. Kliknij przycisk **Zapisz**.


## <a name="add-the-operations"></a>Dodawanie operacji

Operacje są dodawane i konfigurowane do interfejsu API w portalu wydawcy. Aby uzyskać dostęp do portalu **wydawcy,** kliknij pozycję **Portal programu Publisher** w portalu Azure dla usługi zarządzania interfejsami API, wybierz pozycję Interfejsy API , **Operacje**, a następnie kliknij przycisk **Dodaj operację**.

![operacja dodawania](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Zostanie wyświetlone okno **Nowa operacja,** a karta **Podpis** zostanie wybrana domyślnie.

## <a name="add-rrs-operation"></a>Dodaj operację RRS
Najpierw utwórz operację dla usługi AzureML RRS:

1. Dla **czasownika HTTP**wybierz **POST**.
2. Dla **szablonu adresu**`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`URL wpisz " ".
3. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "RRS Execute").

   ![add-rrs-operation-signature](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kliknij **pozycję Odpowiedzi** > **DODAJ** po lewej stronie i wybierz **200 OK**.
5. Kliknij **przycisk Zapisz,** aby zapisać tę operację.

   ![add-rrs-operation-response](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Dodawanie operacji BES

> [!NOTE]
> Zrzuty ekranu nie są tutaj uwzględniane dla operacji BES, ponieważ są bardzo podobne do tych, które służą do dodawania operacji RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Prześlij (ale nie rozpocznij) zadania wykonywania wsadowego

1. Kliknij **dodaj operację,** aby dodać operację BES do interfejsu API.
2. Dla **czasownika HTTP**wybierz **POST**.
3. Dla **szablonu adresu**`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`URL wpisz " ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "BES Submit").
5. Kliknij **pozycję Odpowiedzi** > **DODAJ** po lewej stronie i wybierz **200 OK**.
6. Kliknij przycisk **Zapisz**.

### <a name="start-a-batch-execution-job"></a>Uruchamianie zadania wykonywania wsadowego

1. Kliknij **dodaj operację,** aby dodać operację BES do interfejsu API.
2. Dla **czasownika HTTP**wybierz **POST**.
3. Dla **zlecenia HTTP**,`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`wpisz " ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "BES Start").
6. Kliknij **pozycję Odpowiedzi** > **DODAJ** po lewej stronie i wybierz **200 OK**.
7. Kliknij przycisk **Zapisz**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Pobierz stan lub wynik zadania wykonywania wsadowego

1. Kliknij **dodaj operację,** aby dodać operację BES do interfejsu API.
2. Dla **czasownika**HTTP **wybierz**GET .
3. Dla **szablonu adresu**`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`URL wpisz " ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "Status BES").
6. Kliknij **pozycję Odpowiedzi** > **DODAJ** po lewej stronie i wybierz **200 OK**.
7. Kliknij przycisk **Zapisz**.

### <a name="delete-a-batch-execution-job"></a>Usuwanie zadania wykonywania wsadowego

1. Kliknij **dodaj operację,** aby dodać operację BES do interfejsu API.
2. Dla **zlecenia HTTP**wybierz pozycję **DELETE**.
3. Dla **szablonu adresu**`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`URL wpisz " ".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "BES Delete").
5. Kliknij **pozycję Odpowiedzi** > **DODAJ** po lewej stronie i wybierz **200 OK**.
6. Kliknij przycisk **Zapisz**.

## <a name="call-an-operation-from-the-developer-portal"></a>Wywoływanie operacji z portalu dla deweloperów

Operacje można wywołać bezpośrednio z portalu dewelopera, który zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API. W tym kroku wywołasz **RRS Execute** metoda, która została dodana do **interfejsu API demo azureml**. 

1. Kliknij pozycję **Portal deweloperów**.

   ![developer-portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kliknij **interfejsy API** z górnego menu, a następnie kliknij pozycję **AzureML Demo API,** aby wyświetlić dostępne operacje.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Wybierz **RRS Execute** dla operacji. Kliknij przycisk **Wypróbuj**.

   ![try-it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. W przypadku **parametrów żądania**wpisz **obszar roboczy** i **usługę**, wpisz "2.0 dla **apiversion**i "true" dla **szczegółów**. **Obszar roboczy** i **usługa** można znaleźć na pulpicie nawigacyjnym usługi sieci Web AzureML (zobacz **Testowanie usługi sieci web** w dodatku A).

   W polu **Nagłówki żądania**kliknij pozycję **Dodaj nagłówek** i wpisz "Typ zawartości" i "application/json". Kliknij przycisk **Dodaj nagłówek** ponownie i wpisz "Autoryzacja" i "Nadźwignie * \<usługa\>API-KEY".* Klucz interfejsu API można znaleźć na pulpicie nawigacyjnym usługi sieci Web AzureML (zobacz **Testowanie usługi sieci web** w dodatku A).

   W przypadku treści `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}` **Żądanie**wpisz .

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kliknij pozycję **Wyślij**.

   ![wyślij](./media/manage-web-service-endpoints-using-api-management/send.png)

Po wywołaniu operacji portal dewelopera wyświetla **żądany adres URL** z usługi zaplecza, **stan Odpowiedzi,** **nagłówki odpowiedzi**i dowolną **zawartość odpowiedzi.**

![stan odpowiedzi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Dodatek A — tworzenie i testowanie prostej usługi sieci Web usługi AzureML
### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Poniżej znajdują się kroki tworzenia prostego eksperymentu azureml i wdrażania go jako usługi sieci web. Usługa sieci web przyjmuje jako dane wejściowe kolumnę dowolnego tekstu i zwraca zestaw funkcji reprezentowanych jako liczby całkowite. Przykład:

| Tekst | Tekst skrótowy |
| --- | --- |
| To jest dobry dzień |1 1 2 2 0 2 0 1 |

Najpierw, korzystając z wybranej przeglądarki, [https://studio.azureml.net/](https://studio.azureml.net/) przejdź do: i wprowadź swoje poświadczenia, aby się zalogować. Następnie utwórz nowy pusty eksperyment.

![szablony wyszukiwania i eksperymentów](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Zmień jego nazwę na **SimpleFeatureHashingExperiment**. Rozwiń **pozycję Zapisane zestawy danych** i przeciągnij **recenzje książek z Amazon** na eksperyment.

![simple-feature-hashing-experiment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozwiń **transformację** i **manipulowanie** danymi i przeciągnij do **eksperymentu pozycję Wybierz kolumny w zestawie danych.** Połącz **recenzje książek z Amazon,** aby **wybrać kolumny w zestawie danych**.

![Łączenie modułu zestawu danych recenzji książek z modułem Kolumny projektów](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kliknij **pozycję Zaznacz kolumny w zestawie danych,** a następnie kliknij pozycję Uruchom selektor **kolumny** i wybierz **pozycję Col2**. Kliknij znacznik wyboru, aby zastosować te zmiany.

![Zaznaczanie kolumn przy użyciu nazw kolumn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozwiń **rozszerzenie analizy tekstu** i przeciągnij **haszowanie funkcji** do eksperymentu. Połącz **wybierz kolumny w zestawie danych** z **haszingu funkcji**.

![connect-project-kolumny](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** dla **rozmiaru bitu mieszania**. Spowoduje to utworzenie 8 (23) kolumn.

![rozmiar mieszania](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

W tym momencie można kliknąć przycisk **Uruchom,** aby przetestować eksperyment.

![Uruchomić](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Tworzenie usługi internetowej
Teraz utwórz usługę internetową. Rozwiń węzeł **Web Service** i przeciągnij **dane wejściowe** do eksperymentu. Podłącz **dane wejściowe** do **haszowania funkcji**. Przeciągnij również **dane wyjściowe** na eksperyment. Podłącz **dane wyjściowe** do **haszowania funkcji**.

![mieszanie od wyjścia do funkcji](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kliknij pozycję **Publikuj usługę sieci Web**.

![publikowanie-web-usługa](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kliknij **przycisk Tak,** aby opublikować eksperyment.

![tak do opublikowania](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testowanie usługi internetowej
Usługa sieci web AzureML składa się z punktów końcowych RSS (usługa żądania/odpowiedzi) i BES (usługa wykonywania wsadowego). RSS jest do synchroniczego wykonywania. BES jest dla asynchronii wykonanie zadania. Aby przetestować usługę sieci web przy próbkie źródła języka Python poniżej, może być konieczne pobranie i zainstalowanie pakietu Azure SDK dla języka Python (zobacz: [Jak zainstalować język Python](/azure/python/python-sdk-azure-install)).

Potrzebny będzie również **obszar roboczy,** **usługa**i **api_key** eksperymentu dla poniższego przykładowego źródła. Obszar roboczy i usługa można znaleźć, klikając **przycisk Żądaj/Odpowiedź** lub **Wykonanie wsadowe** dla eksperymentu na pulpicie nawigacyjnym usługi sieci web.

![znajdź obszar roboczy i usługę](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

**api_key** można znaleźć, klikając eksperyment na pulpicie nawigacyjnym usługi sieci Web.

![znajdź-api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Test punktu końcowego RRS
##### <a name="test-button"></a>Przycisk Testuj
Łatwym sposobem przetestowania punktu końcowego RRS jest **kliknięcie przycisku Testuj** na pulpicie nawigacyjnym usługi sieci web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **To jest dobry dzień** dla **col2**. Kliknij znacznik wyboru.

![enter-dane](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zobaczysz coś takiego jak

![wyjście próbki](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Przykładowy kod
Innym sposobem przetestowania rrs. Jeśli klikniesz **żądanie/odpowiedź** na pulpicie nawigacyjnym i przewiń do dołu, zobaczysz przykładowy kod dla języka C#, Python i R. Zostanie również wyświetlono składnię żądania RRS, w tym identyfikator URI żądania, nagłówki i treść.

W tym przewodniku przedstawiono działający przykład języka Python. Należy go zmodyfikować za pomocą **obszaru roboczego,** **usługi**i **api_key** eksperymentu.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>Test punktu końcowego BES
Kliknij **pozycję Wykonywanie partii** na pulpicie nawigacyjnym i przewiń w dół. Zobaczysz przykładowy kod dla języka C#, Python i R. Zostanie również wyświetlono składnię żądań BES do przesłania zadania, rozpoczęcia zadania, uzyskania stanu lub wyników zadania i usunięcia zadania.

W tym przewodniku przedstawiono działający przykład języka Python. Należy go zmodyfikować za pomocą **obszaru roboczego,** **usługi**i **api_key** eksperymentu. Ponadto należy zmodyfikować **nazwę konta magazynu,** **klucz konta magazynu**i **nazwę kontenera magazynu**. Na koniec należy zmodyfikować lokalizację **pliku wejściowego** i lokalizację **pliku wyjściowego**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
