---
title: Zarządzanie usługami sieci Web przy użyciu API Management
titleSuffix: ML Studio (classic) - Azure
description: Przewodnik, w którym pokazano, jak zarządzać usługami sieci web Azure ml za pomocą usługi API Management. Zarządzanie punktami końcowymi usługi interfejsu API REST, definiując dostępu użytkownika, ograniczenie przepustowości i pulpit nawigacyjny monitorowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: d0e9fff56949125c5fa797e0e4ef7e1183448dd0
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168573"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Zarządzanie usługami sieci Web Azure Machine Learning Studio (klasycznymi) za pomocą API Management
## <a name="overview"></a>Omówienie
W tym przewodniku pokazano, jak szybko rozpocząć korzystanie z API Management do zarządzania usługami sieci Web Azure Machine Learning Studio (klasycznymi).

## <a name="what-is-azure-api-management"></a>Co to jest usługa Azure API Management?
Usługa Azure API Management jest usługą platformy Azure, która umożliwia zarządzanie punktami końcowymi usługi interfejsu API REST, definiując dostępu użytkownika, ograniczenie przepustowości i pulpit nawigacyjny monitorowania. Aby uzyskać więcej informacji, zobacz [witrynę Azure API Management](https://azure.microsoft.com/services/api-management/) . Aby rozpocząć pracę z usługą Azure API Management, zobacz [Przewodnik importowania i publikowania](/azure/api-management/import-and-publish). Inne w przewodniku, na podstawie tego przewodnika, obejmuje więcej tematów dotyczących, w tym konfiguracji powiadomień, cennik warstwy, Obsługa odpowiedzi, uwierzytelnianie użytkowników, tworzenie produktów, subskrypcje dla deweloperów i dashboarding użycia.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:

* Konto platformy Azure.
* Konto usługi Azure ml.
* Obszar roboczy, usług i api_key eksperymentu uczenia maszynowego Azure wdrożyć jako usługę sieci web. Aby uzyskać szczegółowe informacje na temat sposobu tworzenia eksperymentu dotyczącego platformy Azure, zobacz [Przewodnik Szybki Start](create-experiment.md)dotyczący programu Studio. Aby uzyskać informacje na temat sposobu wdrażania eksperymentu programu Studio (klasycznego) jako usługi sieci Web, zobacz sekcję dotyczącą [wdrożenia programu Studio](deploy-a-machine-learning-web-service.md) , aby uzyskać szczegółowe informacje na temat sposobu wdrażania eksperymentu platformy Azure jako usługi sieci Web. Alternatywnie dodatek a. zawiera instrukcje dotyczące sposobu tworzenia i testowania prostego eksperymentu uczenia maszynowego Azure i wdrożyć go jako usługę sieci web.

## <a name="create-an-api-management-instance"></a>Tworzenie wystąpienia usługi API Management

Możesz zarządzać usługi sieci web Azure Machine Learning, za pomocą wystąpienia usługi API Management.

1. Zaloguj się do [Azure portal](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób**.
3. W polu wyszukiwania wpisz "Interfejsu API management", a następnie wybierz zasób "Interfejsu API management".
4. Kliknij przycisk **Utwórz**.
5. Wartość **Nazwa** zostanie użyta do utworzenia unikatowego adresu URL (w tym przykładzie użyto "demoazureml").
6. Wybierz **subskrypcję**, **grupę zasobów**i **lokalizację** wystąpienia usługi.
7. Określ wartość dla **nazwy organizacji** (w tym przykładzie jest stosowane "demoazureml").
8. Wprowadź **adres E-mail administratora** — ten adres e-mail będzie używany na potrzeby powiadomień z systemu API Management.
9. Kliknij przycisk **Utwórz**.

Może potrwać do 30 minut do utworzenia nowej usługi.

![Utwórz usługę](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Tworzenie interfejsu API
Po utworzeniu wystąpienia usługi następnym krokiem jest utworzenie interfejsu API. Interfejs API składa się z zestawu operacji, które mogą być wywoływane z poziomu aplikacji klienckiej. Operacje interfejsu API są przekierowywane do istniejących usług sieci Web. Ten przewodnik tworzy interfejsy API tego serwera proxy do istniejących usług sieci web Azure ml RRS i BES.

Aby utworzyć interfejs API:

1. W Azure Portal Otwórz utworzone wystąpienie usługi.
2. W okienku nawigacji po lewej stronie wybierz pozycję **interfejsy API**.

   ![Interfejs API zarządzania menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kliknij pozycję **Dodaj interfejs API**.
2. Wprowadź **nazwę interfejsu API sieci Web** (w tym przykładzie jest użyty "interfejs API demonstracyjnej usługi Azure").
3. W obszarze **adres URL usługi sieci Web**wpisz "`https://ussouthcentral.services.azureml.net`".
4. Wprowadź ** sufiks adresu URL interfejsu API sieci Web ". Będzie to ostatnia część adresu URL, do którego użytkownicy będą używać do wysyłania żądań do wystąpienia usługi (w tym przykładzie użyto "wersji demonstracyjnej usługi Azure ml").
5. W obszarze **schemat adresu URL interfejsu API sieci Web**wybierz pozycję **https**.
6. W obszarze **produkty**wybierz pozycję **Starter**.
7. Kliknij przycisk **Save** (Zapisz).


## <a name="add-the-operations"></a>Dodawanie operacji

Operacje dodawania i skonfigurowane do interfejsu API w portalu wydawcy. Aby uzyskać dostęp do portalu wydawców, kliknij pozycję **Portal wydawcy** w Azure Portal dla usługi API Management, wybierz pozycję **interfejsy API**, **operacje**, a następnie kliknij pozycję **Dodaj operację**.

![Dodawanie operacji](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Zostanie wyświetlone okno **Nowa operacja** , a karta **podpis** zostanie domyślnie wybrana.

## <a name="add-rrs-operation"></a>Dodaj operację rekordy zasobów
Najpierw utwórz operacji dla usługi Azure ml rekordy zasobów:

1. Dla **zlecenia http**wybierz pozycję **post**.
2. W polu **Szablon adresu URL**wpisz "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Wprowadź **nazwę wyświetlaną** (w tym przykładzie jest używana funkcja "rerr Execute").

   ![Dodaj rekordy zasobów — operacja — podpis](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kliknij pozycję **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
5. Kliknij przycisk **Zapisz** , aby zapisać tę operację.

   ![Dodaj rekordy zasobów — operacja odpowiedzi](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Dodawanie usługi BES operacji

> [!NOTE]
> Zrzuty ekranu nie są uwzględniane w tym miejscu dla operacji usługi BES są bardzo podobne do dodawania operacji rekordy zasobów.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Przesyłanie (ale nie uruchomione) zadania wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http**wybierz pozycję **post**.
3. W polu **Szablon adresu URL**wpisz "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie zostanie użyta wartość "BES Submit").
5. Kliknij pozycję **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
6. Kliknij przycisk **Save** (Zapisz).

### <a name="start-a-batch-execution-job"></a>Uruchamianie zadania wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http**wybierz pozycję **post**.
3. Dla **zlecenia http**wpisz "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie jest stosowana wartość "BES Start").
6. Kliknij pozycję **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
7. Kliknij przycisk **Save** (Zapisz).

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Pobierz stan lub wynik zadania wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http**wybierz pozycję **Pobierz**.
3. W polu **Szablon adresu URL**wpisz "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie jest stosowana wartość "BES status").
6. Kliknij pozycję **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
7. Kliknij przycisk **Save** (Zapisz).

### <a name="delete-a-batch-execution-job"></a>Usuwanie zadania wykonywania wsadowego

1. Kliknij przycisk **Dodaj operację** , aby dodać operację BES do interfejsu API.
2. Dla **zlecenia http**wybierz pozycję **Usuń**.
3. W polu **Szablon adresu URL**wpisz "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie zostanie użyta wartość "BES Delete").
5. Kliknij pozycję **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
6. Kliknij przycisk **Save** (Zapisz).

## <a name="call-an-operation-from-the-developer-portal"></a>Wywoływanie operacji z portalu dla deweloperów

Operacje mogą być wywoływane bezpośrednio z portalu dla deweloperów, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API. W tym kroku zostanie wywołana metoda **Execute RR** , która została dodana do **interfejsu API demonstracyjnego usługi Azure**. 

1. Kliknij przycisk **Portal dla deweloperów**.

   ![portal dla deweloperów](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kliknij pozycję **interfejsy API** w górnym menu, a następnie kliknij pozycję **demona interfejsu API platformy Azure** , aby wyświetlić dostępne operacje.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Wybierz pozycję **rekordy zasobów wykonaj** dla operacji. Kliknij przycisk **Wypróbuj**.

   ![try it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. W polu **parametry żądania**wpisz swój **obszar roboczy** i **usługę**, wpisz "2,0 dla **apiversion**i" true ", aby uzyskać **szczegółowe informacje**. **Obszar roboczy** i **usługę** można znaleźć na pulpicie nawigacyjnym usługi sieci Web Azure (zobacz **Testowanie usługi sieci Web** w dodatku A).

   W przypadku **nagłówków żądania**kliknij pozycję **Dodaj nagłówek** i wpisz "Content-Type" i "Application/JSON". Kliknij ponownie pozycję **Dodaj nagłówek** i wpisz "Authorization" i "bearer *\<Twojej\>interfejsu API usługi* ". Klucz API-KEY można znaleźć na pulpicie nawigacyjnym usługi sieci Web Azure (zobacz **Testowanie usługi sieci Web** w dodatku A).

   W obszarze **treść żądania**wpisz `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kliknij pozycję **Wyślij**.

   ![Wyślij](./media/manage-web-service-endpoints-using-api-management/send.png)

Po wywołaniu operacji Portal dla deweloperów wyświetla **żądany adres URL** z usługi zaplecza, **stan odpowiedzi**, **nagłówki odpowiedzi**i dowolną **zawartość odpowiedzi**.

![Stan odpowiedzi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Dodatek A — tworzenie i testowanie prostego Azure ml usługi sieci web
### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Poniżej przedstawiono procedurę tworzenia prostego eksperymentu uczenia maszynowego Azure i wdrożyć go jako usługę sieci web. Przyjmuje usługi sieci web, jakie dane wejściowe kolumny dowolnego tekstu i zwraca zestaw funkcji, reprezentowane jako wartości całkowite. Na przykład:

| Tekst | Tekst skrótu |
| --- | --- |
| Jest to dobrego dnia |1 1 2 2 0 2 0 1 |

Najpierw za pomocą wybranej przeglądarki przejdź do: [https://studio.azureml.net/](https://studio.azureml.net/) a następnie wprowadź swoje poświadczenia, aby się zalogować. Następnie należy utworzyć nowy eksperyment puste.

![Wyszukiwanie eksperymentu szablony](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Zmień nazwę na **SimpleFeatureHashingExperiment**. Rozwiń pozycję **zapisane zestawy danych** i przeciągnij **przeglądy książki z usługi Amazon** do eksperymentu.

![proste — funkcja-wyznaczania wartości skrótu — eksperyment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozwiń pozycję **Przekształcanie danych** i **manipulowanie** , a następnie przeciągnij **pozycję Wybierz kolumny w zestawie danych** do eksperymentu. Połącz **przeglądy książki z usługi Amazon** , aby **wybrać kolumny w zestawie danych**.

![Łączenie modułu zestawu danych przeglądów książki z modułem kolumny projektu](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kliknij pozycję **Wybierz kolumny w zestawie danych** , a następnie kliknij pozycję **Uruchom selektor kolumny** i wybierz pozycję **Col2**. Kliknij znacznik wyboru, aby zastosować te zmiany.

![Wybieranie kolumn przy użyciu nazw kolumn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozwiń **Analiza tekstu** i przeciągnij **skróty funkcji** na eksperyment. Połącz **Wybierz kolumny w zestawie danych** , aby **wyznaczać skróty funkcji**.

![Connect —-kolumny projektu](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Wpisz **3** dla **bitsize skrótu**. Spowoduje to utworzenie 8 (23) kolumny.

![bitsize wyznaczania wartości skrótu](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

W tym momencie można kliknąć przycisk **Uruchom** , aby przetestować eksperyment.

![Uruchom](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Tworzenie usługi internetowej
Teraz można tworzyć usługi sieci web. Rozwiń węzeł **Usługa sieci Web** i przeciągnij **dane wejściowe** na eksperyment. Połącz **dane wejściowe** z **mieszaniem funkcji**. Przeciągnij również **dane wyjściowe** na eksperyment. Połącz **dane wyjściowe** z **mieszaniem funkcji**.

![dane wyjściowe do-— Tworzenie skrótu funkcji](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kliknij pozycję **Opublikuj usługę sieci Web**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kliknij przycisk **tak** , aby opublikować eksperyment.

![tak publikowania](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testowanie usługi internetowej
Usługi sieci web Azure ml składa się z RSS (żądania/odpowiedzi usługi) i BES (batch wykonywania) punktom. Funkcja RSS jest synchronicznej. Usługi BES służy do wykonywania zadania asynchronicznego. Aby przetestować usługę sieci Web za pomocą przykładowego źródła Python poniżej, może być konieczne pobranie i zainstalowanie zestawu Azure SDK dla języka Python (zobacz: [jak zainstalować język Python](/azure/python/python-sdk-azure-install)).

Przykładem może być również **obszar roboczy**, **Usługa**i **api_key** eksperymentu dla poniższego źródła. Obszar roboczy i usługę można znaleźć, klikając pozycję **żądanie/odpowiedź** lub **wykonanie wsadowe** dla eksperymentu na pulpicie nawigacyjnym usługi sieci Web.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

**Api_key** można znaleźć, klikając eksperyment na pulpicie nawigacyjnym usługi sieci Web.

![Znajdź api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Punkt końcowy rekordy zasobów testowych
##### <a name="test-button"></a>Przycisk Testuj
Łatwym sposobem przetestowania punktu końcowego RR jest kliknięcie przycisku **Testuj** na pulpicie nawigacyjnym usługi sieci Web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Wpisz **to dobry dzień** dla **Col2**. Kliknij znacznik wyboru.

![Wprowadź dane](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zostanie wyświetlony, mniej więcej tak

![Przykładowe dane wyjściowe](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Przykładowy kod
Innym sposobem, aby przetestować swoje rekordy zasobów jest z poziomu kodu klienta. Jeśli klikniesz pozycję **żądanie/odpowiedź** na pulpicie nawigacyjnym i przejdziesz do dolnej krawędzi, zobaczysz C#przykładowy kod dla, Python i R. Zostanie również wyświetlona składnia żądania RR, w tym identyfikator URI żądania, nagłówki i treść.

W tym przewodniku przedstawiono działający przykład języka Python. Należy zmodyfikować ją z **obszarem roboczym**, **usługą**i **api_key** eksperymentu.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
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

#### <a name="test-bes-endpoint"></a>Punkt końcowy usługi BES testu
Kliknij pozycję **wykonywanie wsadowe** na pulpicie nawigacyjnym i przewiń w dół. Zostanie wyświetlony kod przykładowy w C#, Python i R. Składnia żądania usługi BES Prześlij zadanie rozpoczęcia zadania, stanu lub wyniki zadania i Usuń zadanie również zostanie wyświetlony.

W tym przewodniku przedstawiono działający przykład języka Python. Należy zmodyfikować ją z **obszarem roboczym**, **usługą**i **api_key** eksperymentu. Ponadto należy zmodyfikować **nazwę konta magazynu**, **klucz konta magazynu**i **nazwę kontenera magazynu**. Na koniec należy zmodyfikować lokalizację **pliku wejściowego** i lokalizację **pliku wyjściowego**.

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
