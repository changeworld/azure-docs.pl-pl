---
title: Informacje o sposobie zarządzania przy użyciu interfejsu API zarządzania usługami sieci web uczenie maszynowe Azure | Dokumentacja firmy Microsoft
description: Przewodnik pokazujący sposób zarządzania przy użyciu interfejsu API zarządzania usługami sieci web uczenie maszynowe Azure.
keywords: uczenia maszynowego, zarządzanie interfejsami api
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 05150ae1-5b6a-4d25-ac67-fb2f24a68e8d
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 4ca551ed07447e41ec94b0334eac0d235e0a5b6f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835087"
---
# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Dowiedz się, jak zarządzać usługami sieci Web Azure ML za pomocą usługi API Management
## <a name="overview"></a>Przegląd
W tym przewodniku przedstawiono sposób szybkie rozpoczęcie pracy przy użyciu interfejsu API zarządzania do zarządzania usługami sieci web uczenie maszynowe Azure.

## <a name="what-is-azure-api-management"></a>Co to jest usługa Azure API Management?
Zarządzanie interfejsami API Azure jest usługą platformy Azure, która umożliwia zarządzanie punktami końcowymi interfejsu API REST, definiując dostępu użytkownika, ograniczenie i pulpit nawigacyjny monitorowania. Kliknij przycisk [tutaj](https://azure.microsoft.com/services/api-management/) szczegółowe informacje na temat usługi Azure API Management. Kliknij przycisk [tutaj](../../api-management/api-management-get-started.md) Przewodnik na temat rozpocząć pracę z usługą Azure API Management. Inne w tym przewodniku, na podstawie tego przewodnika, dotyczą więcej tematy, w tym konfiguracji powiadomień, warstwy cenowej, Obsługa odpowiedzi, uwierzytelnianie użytkowników, tworzenie produktów, subskrypcji dewelopera i dashboarding użycia.

## <a name="what-is-azureml"></a>Co to jest uczenie maszynowe Azure?
Uczenie maszynowe Azure jest usługą platformy Azure do uczenia maszynowego, która umożliwia łatwe tworzenie, wdrażanie i Udostępnianie zaawansowane metody analizy rozwiązania. Kliknij przycisk [tutaj](https://azure.microsoft.com/services/machine-learning/) szczegółowe informacje na temat uczenie maszynowe Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebne są:

* Konto platformy Azure. Jeśli nie masz konta platformy Azure, kliknij przycisk [tutaj](https://azure.microsoft.com/pricing/free-trial/) szczegółowe informacje na temat tworzenia bezpłatne konto próbne.
* Konto uczenie maszynowe Azure. Jeśli nie masz konta uczenie maszynowe Azure, kliknij przycisk [tutaj](https://studio.azureml.net/) szczegółowe informacje na temat tworzenia bezpłatne konto próbne.
* Obszar roboczy, usługi i api_key do eksperymentu uczenie maszynowe Azure wdrożyć jako usługę sieci web. Kliknij przycisk [tutaj](create-experiment.md) szczegółowe informacje na temat tworzenia eksperymentu uczenie maszynowe Azure. Kliknij przycisk [tutaj](publish-a-machine-learning-web-service.md) dla szczegółowe informacje na temat wdrażania uczenie maszynowe Azure eksperymentu jako usługę sieci web. Alternatywnie dodatek a. zawiera instrukcje dotyczące sposobu tworzenia i testowanie prostego eksperymentu uczenie maszynowe Azure i wdróż je jako usługi sieci web.

## <a name="create-an-api-management-instance"></a>Tworzenie wystąpienia usługi API Management

Usługi sieci web uczenie maszynowe Azure przy użyciu wystąpienia interfejsu API zarządzania można zarządzać.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **+ Utwórz zasób**.
3. W polu wyszukiwania wpisz "Interfejsu API zarządzania", a następnie wybierz zasób "Interfejsu API zarządzania".
4. Kliknij przycisk **Utwórz**.
5. **Nazwa** zostanie użyta wartość, aby utworzyć unikatowy adres URL (w tym przykładzie używane "demoazureml").
6. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji** wystąpienia usługi.
7. Określ wartość **nazwa organizacji** (w tym przykładzie używane "demoazureml").
8. Wprowadź użytkownika **e-mail administratora** — ten adres e-mail będzie używany do powiadomień z systemu zarządzania interfejsu API.
9. Kliknij przycisk **Utwórz**.

Może potrwać do 30 minut do utworzenia nowej usługi.

![Utwórz usługę](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Utwórz ten interfejs API
Po utworzeniu wystąpienia usługi, następnym krokiem jest utworzenie interfejsu API. Interfejs API składa się z zestawu operacji, które mogą być wywoływane z poziomu aplikacji klienckiej. Operacje interfejsu API są przekierowywane do istniejących usług sieci Web. Ten przewodnik utworzy interfejsów API czy serwera proxy do istniejących usług sieci web uczenie maszynowe Azure rekordy zasobów i usługi BES.

Aby utworzyć interfejsu API:

1. W portalu Azure Otwórz wystąpienie usługi, którą właśnie utworzony.
2. W lewym okienku nawigacji, wybierz **interfejsów API**.

   ![Interfejs API zarządzania menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Kliknij przycisk **dodać interfejsu API**.
2. Wprowadź **Nazwa interfejsu API sieci Web** (w tym przykładzie używane "API pokaz uczenie maszynowe Azure").
3. Aby uzyskać **adres URL usługi sieci Web**, wprowadź "`https://ussouthcentral.services.azureml.net`".
4. Wprowadź ** sufiks adresu URL interfejsu API sieci Web ". Będzie to ostatnia część adresu URL, do którego użytkownicy będą używać do wysyłania żądań do wystąpienia usługi (w tym przykładzie używane "wersja demonstracyjna uczenie maszynowe Azure").
5. Aby uzyskać **schemat adresu URL interfejsu API sieci Web**, wybierz pozycję **HTTPS**.
6. Aby uzyskać **produktów**, wybierz pozycję **Starter**.
7. Kliknij pozycję **Zapisz**.


## <a name="add-the-operations"></a>Dodawanie działań

Działania są dodawane i skonfigurowane do interfejsu API w portalu wydawcy. Aby uzyskać dostęp do portalu wydawcy, kliknij przycisk **portal wydawcy** w portalu Azure dla usługi interfejsu API zarządzania, wybierz **interfejsów API**, **operacji**, kliknij przycisk **Operacja dodania**.

![Dodaj operację](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

**Nowej operacji** będzie wyświetlane okno i **podpisu** będzie domyślnie wybierany kartę.

## <a name="add-rrs-operation"></a>Dodaj operację rekordy zasobów
Najpierw należy utworzyć operacji dla usługi uczenie maszynowe Azure rekordy zasobów:

1. Aby uzyskać **zlecenie HTTP**, wybierz pozycję **POST**.
2. Aby uzyskać **szablon adresu URL**, typu "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`".
3. Wprowadź **Nazwa wyświetlana** (w tym przykładzie używane "Rekordy zasobów Execute").

   ![Dodawanie rekordów zasobów operacji podpisu](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Kliknij przycisk **odpowiedzi** > **dodać** po lewej i wybierz **200 OK**.
5. Kliknij przycisk **zapisać** można zapisać tej operacji.

   ![Dodawanie rekordów zasobów operacji odpowiedzi](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Dodaj BES operacji

> [!NOTE]
> Zrzuty ekranu nie zostały uwzględnione w tym miejscu dla operacji usługi BES są one bardzo podobne do tych rekordów zasobów operacji dodawania.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Przesyłanie (ale nie start) zadanie wsadowe

1. Kliknij przycisk **operacji dodawania** można dodać operacji BES do interfejsu API.
2. Aby uzyskać **zlecenie HTTP**, wybierz pozycję **POST**.
3. Aby uzyskać **szablon adresu URL**, typu "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Wprowadź **Nazwa wyświetlana** (w tym przykładzie używane "BES przesłać").
5. Kliknij przycisk **odpowiedzi** > **dodać** po lewej i wybierz **200 OK**.
6. Kliknij pozycję **Zapisz**.

### <a name="start-a-batch-execution-job"></a>Uruchom zadanie wsadowe

1. Kliknij przycisk **operacji dodawania** można dodać operacji BES do interfejsu API.
2. Aby uzyskać **zlecenie HTTP**, wybierz pozycję **POST**.
3. Aby uzyskać **zlecenie HTTP**, typu "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Wprowadź **Nazwa wyświetlana** (w tym przykładzie używane BES Start (Rozpocznij)").
6. Kliknij przycisk **odpowiedzi** > **dodać** po lewej i wybierz **200 OK**.
7. Kliknij pozycję **Zapisz**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Pobierz stan lub wyniku zadania wsadowe

1. Kliknij przycisk **operacji dodawania** można dodać operacji BES do interfejsu API.
2. Aby uzyskać **zlecenie HTTP**, wybierz pozycję **UZYSKAĆ**.
3. Aby uzyskać **szablon adresu URL**, typu "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Wprowadź **Nazwa wyświetlana** (w tym przykładzie używane "Stan usługi BES").
6. Kliknij przycisk **odpowiedzi** > **dodać** po lewej i wybierz **200 OK**.
7. Kliknij pozycję **Zapisz**.

### <a name="delete-a-batch-execution-job"></a>Usuń zadanie wsadowe

1. Kliknij przycisk **operacji dodawania** można dodać operacji BES do interfejsu API.
2. Aby uzyskać **zlecenie HTTP**, wybierz pozycję **usunąć**.
3. Aby uzyskać **szablon adresu URL**, typu "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Wprowadź **Nazwa wyświetlana** (w tym przykładzie używane "BES Delete").
5. Kliknij przycisk **odpowiedzi** > **dodać** po lewej i wybierz **200 OK**.
6. Kliknij pozycję **Zapisz**.

## <a name="call-an-operation-from-the-developer-portal"></a>Wywołaj operację z portalu dla deweloperów

Operacje można wywołać bezpośrednio z portalu dla deweloperów, które oferują wygodny sposób, aby wyświetlić i przetestować operacje interfejsu API. W tym kroku wywoła **wykonania rekordy zasobów** metody, która została dodana do **API pokaz uczenie maszynowe Azure**. 

1. Kliknij przycisk **portalu dla deweloperów**.

   ![portalu dla deweloperów](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Kliknij przycisk **interfejsów API** z menu u góry, a następnie kliknij przycisk **API pokaz uczenie maszynowe Azure** Aby wyświetlić dostępne operacje.

   ![demoazureml-api](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Wybierz **wykonania rekordy zasobów** dla tej operacji. Kliknij przycisk **wypróbuj**.

   ![try it](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Dla **parametry żądania**, typ użytkownika **obszaru roboczego** i **usługi**, typu "w wersji 2.0 dla **apiversion**i jest to"true"dla **szczegóły**. Można znaleźć sieci **obszaru roboczego** i **usługi** na pulpicie nawigacyjnym usługi sieci web uczenie maszynowe Azure (zobacz **przetestować usługę sieci web** w dodatku A).

   Aby uzyskać **nagłówki żądań**, kliknij przycisk **Dodaj nagłówek** i wpisz "Content-Type" i "application/json". Kliknij przycisk **Dodaj nagłówek** ponownie i wpisz "Autoryzacji" i "Bearer  *\<klucz interfejsu API usługi\>*". KLUCZ interfejsu API można znaleźć na pulpicie nawigacyjnym usługi sieci web uczenie maszynowe Azure (zobacz **przetestować usługę sieci web** w dodatku A).

   Aby uzyskać **treść żądania**, typ `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![azureml-demo-api](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Kliknij przycisk **wysyłania**.

   ![Wyślij](./media/manage-web-service-endpoints-using-api-management/send.png)

Po wywołaniu operacji portalu dla deweloperów Wyświetla **żądany adres URL** usługi zaplecza **stanu odpowiedzi**, **nagłówki odpowiedzi**, natomiast dla ustawienia **zawartości odpowiedzi**.

![Stan odpowiedzi](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Dodatek A — tworzenie i testowanie prostego uczenie maszynowe Azure usługi sieci web
### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Poniżej przedstawiono procedurę tworzenia prostego eksperymentu uczenie maszynowe Azure i wdrażania go w postaci usługi sieci web. Pobiera usługi sieci web, jakie danych wejściowych z kolumną zawierającą dowolnego tekstu i zwraca zestaw funkcji reprezentowane jako wartości całkowite. Na przykład:

| Tekst | Tekst w formie skrótu |
| --- | --- |
| Jest to dobry dzień |1 1 2 2 0 2 0 1 |

Po pierwsze, za pomocą przeglądarki wybranych przez użytkownika, przejdź do: [ https://studio.azureml.net/ ](https://studio.azureml.net/) i wprowadź swoje poświadczenia, aby się zalogować. Następnie należy utworzyć nowy eksperyment puste.

![Wyszukiwanie — eksperymentu — szablony](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Zmienić jego nazwę na **SimpleFeatureHashingExperiment**. Rozwiń węzeł **zapisane zestawów danych** i przeciągnij **przeglądami książki z Amazon** na eksperymentu.

![prosty — funkcja mieszania eksperymentu](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Rozwiń węzeł **transformacji danych** i **manipulowania** i przeciągnij **Select Columns in Dataset** na eksperymentu. Połącz **skoroszyt z usługi Amazon przeglądami** do **Wybieranie kolumn w zestawie danych**.

![select-columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Kliknij przycisk **Select Columns in Dataset** , a następnie kliknij przycisk **Uruchom selektor kolumn** i wybierz **Col2**. Kliknij znacznik wyboru, aby zastosować te zmiany.

![select-columns](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Rozwiń węzeł **Analiza tekstu** i przeciągnij **Tworzenie skrótu funkcji** na eksperymentu. Połącz **Wybieranie kolumn w zestawie danych** do **Tworzenie skrótu funkcji**.

![Connect--kolumny projektu](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** dla **mieszania bitsize**. Spowoduje to utworzenie 8 (23) kolumny.

![Tworzenie skrótów bitsize](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

W tym momencie można kliknąć przycisk **Uruchom** do testowania eksperymentu.

![Uruchom](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Tworzenie usługi internetowej
Teraz Utwórz usługę sieci web. Rozwiń węzeł **usługi sieci Web** i przeciągnij **dane wejściowe** na eksperymentu. Połącz **dane wejściowe** do **Tworzenie skrótu funkcji**. Przeciągnij również **dane wyjściowe** na eksperymentu. Połącz **dane wyjściowe** do **Tworzenie skrótu funkcji**.

![dane wyjściowe do-— Tworzenie skrótu funkcji](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Kliknij przycisk **opublikować usługi sieci web**.

![publish-web-service](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Kliknij przycisk **tak** publikowania eksperymentu.

![tak aby opublikować](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testowanie usługi sieci web
Usługi sieci web uczenie maszynowe Azure składa się z punktów końcowych (usługi wykonywania wsadowego) BES i RSS (żądania/odpowiedzi usługi). Funkcja RSS jest synchroniczne wykonywania. BES jest do wykonywania zadań asynchronicznych. Aby przetestować usługi sieci web ze źródłem Python próbki poniżej, konieczne może być Pobierz i zainstaluj zestaw Azure SDK dla języka Python (zobacz: [sposób instalowania Python](../../python-how-to-install.md)).

Należy również **obszaru roboczego**, **usługi**, i **api_key** eksperymentu źródła próbki poniżej. Obszar roboczy i usługi można znaleźć, klikając opcję **żądanie/odpowiedź** lub **Batch Execution** swojego eksperymentu w pulpicie nawigacyjnym usługi sieci web.

![find-workspace-and-service](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Można znaleźć **api_key** klikając eksperymentu w pulpicie nawigacyjnym usługi sieci web.

![Znajdź api-key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Punkt końcowy RR testu
##### <a name="test-button"></a>Przycisk Testuj
Łatwe testowanie punktu końcowego rekordy zasobów jest kliknięcie **testu** na pulpicie nawigacyjnym usługi sieci web.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **to dobry dzień** dla **col2**. Kliknij znacznik wyboru.

![Wprowadź dane](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Zobaczysz ekran podobny do

![Przykładowe wyniki](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Przykładowy kod
Innym sposobem sprawdzenia Twoje rekordy zasobów jest z kodu klienta. Jeśli klikniesz przycisk **żądania/odpowiedzi** na pulpicie nawigacyjnym i przewiń w dół, zostanie wyświetlone przykładowego kodu dla C#, Python i R. Można również wyświetlić składnię żądania rekordów zasobów, w tym żądaniu identyfikatora URI, nagłówków i treść.

Ten przewodnik zawiera pracy przykład Python. Konieczne będzie jej modyfikowania **obszaru roboczego**, **usługi**, i **api_key** eksperymentu.

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
Kliknij przycisk **wykonywanie wsadowe** na pulpicie nawigacyjnym i przewiń w dół. Zostanie wyświetlone przykładowego kodu dla C#, Python i R. Widoczne będzie także składnia żądania BES do przesyłania zadania, uruchomić zadanie pobieranie stanu lub wyniki zadania i usunąć zadania.

Ten przewodnik zawiera pracy przykład Python. Należy zmodyfikować za pomocą **obszaru roboczego**, **usługi**, i **api_key** eksperymentu. Ponadto należy zmodyfikować **nazwy konta magazynu**, **klucz konta magazynu**, i **nazwa kontenera magazynu**. Ponadto należy zmodyfikować lokalizację **pliku wejściowego** i lokalizację **pliku wyjściowego**.

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
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
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
