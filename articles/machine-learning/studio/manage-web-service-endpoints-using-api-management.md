---
title: Zarządzanie usługami sieci web za pomocą usługi API Management
titleSuffix: Azure Machine Learning Studio
description: Przewodnik, w którym pokazano, jak zarządzać usługami sieci web Azure ml za pomocą usługi API Management. Zarządzanie punktami końcowymi usługi interfejsu API REST, definiując dostępu użytkownika, ograniczenie przepustowości i pulpit nawigacyjny monitorowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 0d79bc167ea0416218a4d4822bcd6221699643ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347259"
---
# <a name="manage-azure-machine-learning-studio-web-services-using-api-management"></a>Zarządzanie za pomocą interfejsu API zarządzania usługami sieci web Azure Machine Learning Studio
## <a name="overview"></a>Przegląd
Ten przewodnik pokazuje, jak szybko rozpocząć pracę z usługą API Management do zarządzania usługami sieci web Azure Machine Learning Studio.

## <a name="what-is-azure-api-management"></a>Co to jest usługa Azure API Management?
Usługa Azure API Management jest usługą platformy Azure, która umożliwia zarządzanie punktami końcowymi usługi interfejsu API REST, definiując dostępu użytkownika, ograniczenie przepustowości i pulpit nawigacyjny monitorowania. Kliknij przycisk [tutaj](https://azure.microsoft.com/services/api-management/) szczegółowe informacje na temat usługi Azure API Management. Kliknij przycisk [tutaj](/azure/api-management/import-and-publish) wskazówki na temat rozpocząć pracę z usługą Azure API Management. Inne w przewodniku, na podstawie tego przewodnika, obejmuje więcej tematów dotyczących, w tym konfiguracji powiadomień, cennik warstwy, Obsługa odpowiedzi, uwierzytelnianie użytkowników, tworzenie produktów, subskrypcje dla deweloperów i dashboarding użycia.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:

* Konto platformy Azure. Jeśli nie masz konta platformy Azure, kliknij przycisk [tutaj](https://azure.microsoft.com/pricing/free-trial/) szczegółowe informacje na temat utworzyć konto bezpłatnej wersji próbnej.
* Konto usługi Azure ml. Jeśli nie masz konta usługi Azure ml, kliknij przycisk [tutaj](https://studio.azureml.net/) szczegółowe informacje na temat utworzyć konto bezpłatnej wersji próbnej.
* Obszar roboczy, usług i api_key eksperymentu uczenia maszynowego Azure wdrożyć jako usługę sieci web. Kliknij przycisk [tutaj](create-experiment.md) Aby uzyskać szczegółowe informacje na temat tworzenia eksperymentu uczenia maszynowego Azure. Kliknij przycisk [tutaj](publish-a-machine-learning-web-service.md) Aby uzyskać szczegółowe informacje na temat wdrażania usługi Azure ml eksperymentować jako usługę sieci web. Alternatywnie dodatek a. zawiera instrukcje dotyczące sposobu tworzenia i testowania prostego eksperymentu uczenia maszynowego Azure i wdrożyć go jako usługę sieci web.

## <a name="create-an-api-management-instance"></a>Tworzenie wystąpienia usługi API Management

Możesz zarządzać usługi sieci web Azure Machine Learning, za pomocą wystąpienia usługi API Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób**.
3. W polu wyszukiwania wpisz "Interfejsu API management", a następnie wybierz zasób "Interfejsu API management".
4. Kliknij pozycję **Utwórz**.
5. **Nazwa** zostanie użyta wartość, aby utworzyć unikatowy adres URL (w tym przykładzie użyto "demoazureml").
6. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji** danego wystąpienia usługi.
7. Określ wartość dla **nazwa organizacji** (w tym przykładzie użyto "demoazureml").
8. Wprowadź swoje **adres e-mail administratora** -tę wiadomość e-mail będzie używany do powiadomień z poziomu systemu usługi API Management.
9. Kliknij pozycję **Utwórz**.

Może potrwać do 30 minut do utworzenia nowej usługi.

![Utwórz usługę](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Tworzenie interfejsu API
Po utworzeniu wystąpienia usługi następnym krokiem jest utworzenie interfejsu API. Interfejs API składa się z zestawu operacji, które mogą być wywoływane z poziomu aplikacji klienckiej. Operacje interfejsu API są przekierowywane do istniejących usług sieci Web. Ten przewodnik tworzy interfejsy API tego serwera proxy do istniejących usług sieci web Azure ml RRS i BES.

Aby utworzyć interfejs API:

1. W witrynie Azure portal Otwórz wystąpienie usługi, który został utworzony.
2. W okienku nawigacji po lewej stronie wybierz **interfejsów API**.

   ![Interfejs API zarządzania menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kliknij przycisk **Dodawanie interfejsu API**.
2. Wprowadź **Nazwa internetowego interfejsu API** (w tym przykładzie użyto "Usługi Azure ml wersji demonstracyjnej interfejsu API").
3. Aby uzyskać **adres URL usługi sieci Web**, wprowadź "`https://ussouthcentral.services.azureml.net`".
4. Wprowadź ** sufiks adresu URL interfejsu API sieci Web ". Będzie to ostatnia część adresu URL, do którego użytkownicy będą używać do wysyłania żądań do wystąpienia usługi (w tym przykładzie użyto "wersji demonstracyjnej usługi Azure ml").
5. Aby uzyskać **schemat adresu URL interfejsu API sieci Web**, wybierz opcję **HTTPS**.
6. Aby uzyskać **produktów**, wybierz opcję **Starter**.
7. Kliknij pozycję **Zapisz**.


## <a name="add-the-operations"></a>Dodawanie operacji

Operacje dodawania i skonfigurowane do interfejsu API w portalu wydawcy. Aby uzyskać dostęp do portalu wydawcy, kliknij przycisk **portalu wydawców** w witrynie Azure portal dla usługi API Management, wybierz **interfejsów API**, **operacji**, następnie kliknij przycisk **Operacja dodania**.

![Dodawanie operacji](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

**Nową operację** zostanie wyświetlone okno i **podpisu** karta zostanie wybrana domyślnie.

## <a name="add-rrs-operation"></a>Dodaj operację rekordy zasobów
Najpierw utwórz operacji dla usługi Azure ml rekordy zasobów:

1. Aby uzyskać **czasownik HTTP**, wybierz opcję **WPIS**.
2. Aby uzyskać **szablon adresu URL**, wpisz "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "Rekordy zasobów wykonania").

   ![Dodaj rekordy zasobów — operacja — podpis](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kliknij przycisk **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
5. Kliknij przycisk **Zapisz** można zapisać tej operacji.

   ![Dodaj rekordy zasobów — operacja odpowiedzi](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Dodawanie usługi BES operacji

> [!NOTE]
> Zrzuty ekranu nie są uwzględniane w tym miejscu dla operacji usługi BES są bardzo podobne do dodawania operacji rekordy zasobów.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Przesyłanie (ale nie uruchomione) zadania wykonywania wsadowego

1. Kliknij przycisk **operacji dodawania** można dodać operacji usługi BES do interfejsu API.
2. Aby uzyskać **czasownik HTTP**, wybierz opcję **WPIS**.
3. Aby uzyskać **szablon adresu URL**, wpisz "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "Prześlij BES").
5. Kliknij przycisk **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
6. Kliknij pozycję **Zapisz**.

### <a name="start-a-batch-execution-job"></a>Uruchamianie zadania wykonywania wsadowego

1. Kliknij przycisk **operacji dodawania** można dodać operacji usługi BES do interfejsu API.
2. Aby uzyskać **czasownik HTTP**, wybierz opcję **WPIS**.
3. Aby uzyskać **czasownik HTTP**, wpisz "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "BES Start").
6. Kliknij przycisk **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
7. Kliknij pozycję **Zapisz**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Pobierz stan lub wynik zadania wykonywania wsadowego

1. Kliknij przycisk **operacji dodawania** można dodać operacji usługi BES do interfejsu API.
2. Aby uzyskać **czasownik HTTP**, wybierz opcję **UZYSKAĆ**.
3. Aby uzyskać **szablon adresu URL**, wpisz "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "Stan usługi BES").
6. Kliknij przycisk **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
7. Kliknij pozycję **Zapisz**.

### <a name="delete-a-batch-execution-job"></a>Usuwanie zadania wykonywania wsadowego

1. Kliknij przycisk **operacji dodawania** można dodać operacji usługi BES do interfejsu API.
2. Aby uzyskać **czasownik HTTP**, wybierz opcję **Usuń**.
3. Aby uzyskać **szablon adresu URL**, wpisz "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Wprowadź **nazwę wyświetlaną** (w tym przykładzie użyto "BES Delete").
5. Kliknij przycisk **odpowiedzi** > **Dodaj** po lewej stronie i wybierz pozycję **200 OK**.
6. Kliknij pozycję **Zapisz**.

## <a name="call-an-operation-from-the-developer-portal"></a>Wywoływanie operacji z portalu dla deweloperów

Operacje mogą być wywoływane bezpośrednio z portalu dla deweloperów, która zapewnia wygodny sposób wyświetlania i testowania operacji interfejsu API. W tym kroku będzie wywoływać **wykonania RRS** metodę, która została dodana do **API pokaz usługi Azure ml**. 

1. Kliknij przycisk **portalu dla deweloperów**.

   ![portal dla deweloperów](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kliknij przycisk **interfejsów API** z górnego menu, a następnie kliknij **API pokaz usługi Azure ml** Aby wyświetlić dostępne operacje.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Wybierz **wykonania RRS** dla tej operacji. Kliknij przycisk **wypróbuj**.

   ![try it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Dla **parametry żądania**, typ usługi **obszaru roboczego** i **usługi**, typu "2.0 dla **apiversion**i jest to"true"dla **szczegóły**. Możesz znaleźć swojej **obszaru roboczego** i **usługi** pulpicie nawigacyjnym usługi sieci web Azure ml (zobacz **przetestować usługę sieci web** w załączniku A).

   Aby uzyskać **nagłówki żądań**, kliknij przycisk **Dodaj nagłówek** i wpisz "Content-Type" i "application/json". Kliknij przycisk **Dodaj nagłówek** ponownie i wpisz "Autoryzacja" i "Bearer  *\<klucz interfejsu API usługi\>* ". KLUCZ interfejsu API można znaleźć na pulpicie nawigacyjnym usługi sieci web Azure ml (zobacz **przetestować usługę sieci web** w załączniku A).

   Aby uzyskać **treść żądania**, typ `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kliknij przycisk **wysyłania**.

   ![Wyślij](./media/manage-web-service-endpoints-using-api-management/send.png)

Po wywołaniu operacji portal dla deweloperów Wyświetla **żądany adres URL** z usługi zaplecza **stan odpowiedzi**, **nagłówki odpowiedzi**oraz wszelkie  **Zawartość odpowiedzi**.

![Stan odpowiedzi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Dodatek A — tworzenie i testowanie prostego Azure ml usługi sieci web
### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Poniżej przedstawiono procedurę tworzenia prostego eksperymentu uczenia maszynowego Azure i wdrożyć go jako usługę sieci web. Przyjmuje usługi sieci web, jakie dane wejściowe kolumny dowolnego tekstu i zwraca zestaw funkcji, reprezentowane jako wartości całkowite. Na przykład:

| Tekst | Tekst skrótu |
| --- | --- |
| Jest to dobrego dnia |1 1 2 2 0 2 0 1 |

Za pomocą przeglądarki wybranych przez użytkownika, przejdź do: [ https://studio.azureml.net/ ](https://studio.azureml.net/) i wprowadź swoje poświadczenia, aby zalogować się. Następnie należy utworzyć nowy eksperyment puste.

![Wyszukiwanie eksperymentu szablony](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Zmień jej nazwę na **SimpleFeatureHashingExperiment**. Rozwiń **zapisane zestawów danych** i przeciągnij **przeglądy książki ze środowiska Amazon** obszarze eksperymentu.

![proste — funkcja-wyznaczania wartości skrótu — eksperyment](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozwiń **przekształcania danych** i **manipulowania** i przeciągnij **Select Columns in Dataset** obszarze eksperymentu. Połącz **Zarezerwuj przeglądy ze środowiska Amazon** do **Wybieranie kolumn w zestawie danych**.

![Łączenie modułu zestawu danych przeglądy książki modułu kolumny projektu](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kliknij przycisk **Select Columns in Dataset** a następnie kliknij przycisk **uruchamianie selektora kolumn** i wybierz **Col2**. Kliknij znacznik wyboru, aby zastosować te zmiany.

![Wybierz kolumny przy użyciu nazwy kolumn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozwiń **analizy tekstu** i przeciągnij **Tworzenie skrótu funkcji** na eksperyment. Połącz **Wybieranie kolumn w zestawie danych** do **Tworzenie skrótu funkcji**.

![Connect —-kolumny projektu](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** dla **wyznaczania wartości skrótu bitsize**. Spowoduje to utworzenie 8 (23) kolumny.

![bitsize wyznaczania wartości skrótu](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

W tym momencie można kliknąć przycisk **Uruchom** do testowania eksperymentu.

![Uruchom](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Tworzenie usługi internetowej
Teraz można tworzyć usługi sieci web. Rozwiń **usługi sieci Web** i przeciągnij **dane wejściowe** obszarze eksperymentu. Połącz **dane wejściowe** do **Tworzenie skrótu funkcji**. Również przeciągnąć **dane wyjściowe** obszarze eksperymentu. Połącz **dane wyjściowe** do **Tworzenie skrótu funkcji**.

![dane wyjściowe do-— Tworzenie skrótu funkcji](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kliknij przycisk **opublikować usługi sieci web**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kliknij przycisk **tak** publikowanie eksperymentu.

![tak publikowania](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Test usługi sieci web
Usługi sieci web Azure ml składa się z RSS (żądania/odpowiedzi usługi) i BES (batch wykonywania) punktom. Funkcja RSS jest synchronicznej. Usługi BES służy do wykonywania zadania asynchronicznego. Aby przetestować usługę sieci web z przykładowym źródle Python poniżej, konieczne może być Pobierz i zainstaluj zestaw Azure SDK dla języka Python (zobacz: [Jak zainstalować Python](../../python-how-to-install.md)).

Należy również **obszaru roboczego**, **usługi**, i **api_key** eksperymentu źródła przykładowe poniżej. Obszar roboczy i usługi można znaleźć, klikając wartość **żądań/odpowiedzi** lub **wykonywanie wsadowe** swojego eksperymentu w pulpicie nawigacyjnym usługi sieci web.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Możesz znaleźć **api_key** , klikając eksperymentu w pulpicie nawigacyjnym usługi sieci web.

![Znajdź api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Punkt końcowy rekordy zasobów testowych
##### <a name="test-button"></a>Przycisk Testuj
Łatwe testowanie punktu końcowego rekordy zasobów jest kliknięcie **testu** na pulpicie nawigacyjnym usługi sieci web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **to dobrego dnia** dla **col2**. Kliknij znacznik wyboru.

![Wprowadź dane](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zostanie wyświetlony, mniej więcej tak

![Przykładowe dane wyjściowe](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Przykładowy kod
Innym sposobem, aby przetestować swoje rekordy zasobów jest z poziomu kodu klienta. Jeśli klikniesz **żądań/odpowiedzi** na pulpicie nawigacyjnym i przewiń w dół, zostanie wyświetlony kod przykładowy w C#, Python i R. Zobaczysz również składnia żądania rekordy zasobów, w tym żądaniu identyfikatora URI, nagłówki i treść.

W tym przewodniku przedstawiono działający przykład języka Python. Konieczne będzie zmodyfikuj go za pomocą **obszaru roboczego**, **usługi**, i **api_key** eksperymentu.

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
Kliknij przycisk **wykonywanie wsadowe** na pulpicie nawigacyjnym i przewiń w dół. Zostanie wyświetlony kod przykładowy w C#, Python i R. Składnia żądania usługi BES Prześlij zadanie rozpoczęcia zadania, stanu lub wyniki zadania i Usuń zadanie również zostanie wyświetlony.

W tym przewodniku przedstawiono działający przykład języka Python. Należy ją zmodyfikować **obszaru roboczego**, **usługi**, i **api_key** eksperymentu. Ponadto należy zmodyfikować **nazwa konta magazynu**, **klucza konta magazynu**, i **nazwa kontenera magazynu**. Ponadto trzeba będzie zmodyfikować lokalizację **pliku wejściowego** i lokalizację **plik wyjściowy**.

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
    with open(output_file, "w+") as f:
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
