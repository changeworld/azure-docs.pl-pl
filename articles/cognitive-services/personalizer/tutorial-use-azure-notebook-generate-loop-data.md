---
title: 'Samouczek: Notes platformy Azure — personalizator'
titleSuffix: Azure Cognitive Services
description: Ten samouczek symuluje pętlę Personalizer _system w notesie platformy Azure, co sugeruje, jaki typ kawy klient powinien zamówić. Użytkownicy i ich preferencje są przechowywane w zestawie danych użytkownika. Informacje o kawie są również dostępne i przechowywane w zestawie danych kawy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: 03e8b658f7edf4640d738e5ea3af84953185d0f5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986839"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Samouczek: Używanie personalizatora w notesie platformy Azure

W tym samouczku uruchomi się pętlę Personalizer w notesie platformy Azure, demonstrując cykl życia od końca do końca w pętli personalizatora.

Pętla sugeruje, jaki rodzaj kawy klient powinien zamówić. Użytkownicy i ich preferencje są przechowywane w zestawie danych użytkownika. Informacje o kawie są przechowywane w zestawie danych kawy.

## <a name="users-and-coffee"></a>Użytkownicy i kawa

Notes, symulując interakcję użytkownika z witryną sieci Web, wybiera losowego użytkownika, pochwę dnia i typ pogody z zestawu danych. Podsumowanie informacji o użytkowniku to:

|Klienci — funkcje kontekstowe|Porę dnia|Rodzaje pogody|
|--|--|--|
|Alice<br>Bob<br>Cathy<br>Dave|Rano<br>Po południu<br>Wieczorem|Słoneczny<br>Deszczowy<br>Snowy|

Aby pomóc Personalizerowi w nauce, z czasem _system_ zna również szczegóły dotyczące wyboru kawy dla każdej osoby.

|Kawa - funkcje akcji|Rodzaje temperatur|Miejsca pochodzenia|Rodzaje pieczeń|organiczne|
|--|--|--|--|--|
|Cappacino ( Cappacino )|Gorąca|Kenia|Ciemny|organiczne|
|Napar na zimno|Chłodne|Brazylia|Jasny|organiczne|
|Mrożona mokka|Chłodne|Etiopia|Jasny|Nie organiczne|
|Latte|Gorąca|Brazylia|Ciemny|Nie organiczne|

**Celem** pętli Personalizer jest znalezienie najlepszego dopasowania między użytkownikami a kawą jak najwięcej czasu.

Kod dla tego samouczka jest dostępny w [repozytorium Personalizer Samples GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook).

## <a name="how-the-simulation-works"></a>Jak działa symulacja

Na początku uruchomionego systemu, sugestie z Personalizer są skuteczne tylko między 20% do 30%. Ten sukces jest oznaczony nagrodą odesłaną do API nagrody Personalizera, z wynikiem 1. Po kilku wezwaniach rangi i nagrody system się poprawia.

Po początkowych żądaniach uruchom ocenę w trybie offline. Dzięki temu Personalizer do przeglądania danych i sugerują lepszą politykę uczenia się. Zastosuj nowe zasady uczenia się i uruchom notes ponownie z 20% poprzedniej liczby żądań. Pętla będzie działać lepiej z nową polityką uczenia się.

## <a name="rank-and-reward-calls"></a>Zaproszenia do rangi i nagrody

Dla każdego z kilku tysięcy wywołań usługi Personalizer notes platformy Azure wysyła żądanie **rangi** do interfejsu API REST:

* Unikatowy identyfikator zdarzenia Ranga/Prośba
* Funkcje kontekstowe — losowy wybór użytkownika, pogody i porę dnia — symulacja użytkownika na stronie internetowej lub urządzeniu mobilnym
* Akcje z funkcjami - _Wszystkie_ dane kawy - z których Personalizer sprawia, że sugestia

System odbiera żądanie, a następnie porównuje to przewidywanie ze znanym wyborem użytkownika dla tej samej pory dnia i pogody. Jeśli znany wybór jest taki sam jak przewidywany wybór, **nagroda** 1 jest wysyłana z powrotem do Personalizera. W przeciwnym razie nagroda odesłana wynosi 0.

> [!Note]
> Jest to symulacja, więc algorytm nagrody jest prosty. W rzeczywistym scenariuszu algorytm powinien używać logiki biznesowej, ewentualnie z wagami dla różnych aspektów doświadczenia klienta, aby określić wynik nagrody.


## <a name="prerequisites"></a>Wymagania wstępne

* Konto [usługi Azure w notesie.](https://notebooks.azure.com/)
* [Zasób personalizatora platformy Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
    * Jeśli użyto już zasobu Personalizer, upewnij się, aby [wyczyścić dane](how-to-settings.md#clear-data-for-your-learning-loop) w witrynie Azure portal dla zasobu.
* Przekaż wszystkie pliki dla [tego przykładu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) do projektu usługi Azure Notebook.

Opisy plików:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) jest notebookiem Jupyter dla tego samouczka.
* [Zestaw danych użytkownika](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) jest przechowywany w obiekcie JSON.
* [Zestaw danych kawy](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) jest przechowywany w obiekcie JSON.
* [Przykładowy żądać JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) jest oczekiwanym formatem żądania POST do interfejsu API rangi.

## <a name="configure-personalizer-resource"></a>Konfigurowanie zasobu Personalizer

W witrynie Azure portal skonfiguruj [zasób Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) z **częstotliwością modelu aktualizacji** ustawioną na 15 sekund i **czasem oczekiwania nagrody** 15 sekund. Te wartości znajdują się na stronie **[Konfiguracja.](how-to-settings.md#configure-service-settings-in-the-azure-portal)**

|Ustawienie|Wartość|
|--|--|
|aktualizowanie częstotliwości modelu|15 sekund|
|czas oczekiwania na nagrody|15 sekund|

Wartości te mają bardzo krótki czas trwania, aby pokazać zmiany w tym samouczku. Te wartości nie powinny być używane w scenariuszu produkcji bez sprawdzania poprawności osiągają cel za pomocą pętli Personalizer.

## <a name="set-up-the-azure-notebook"></a>Konfigurowanie notesu platformy Azure

1. Zmień jądro `Python 3.6`na .
1. Otwórz plik `Personalizer.ipynb`.

## <a name="run-notebook-cells"></a>Uruchamianie komórek notesu

Uruchom każdą komórkę wykonywalną i poczekaj, aż powróci. Wiesz, że odbywa się to, gdy nawiasy obok komórki `*`wyświetlają liczbę zamiast . W poniższych sekcjach wyjaśniono, co każda komórka robi programowo i czego można oczekiwać od danych wyjściowych.

### <a name="include-the-python-modules"></a>Dołącz moduły pythona

Dołącz wymagane moduły python. Komórka nie ma danych wyjściowych.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Ustawianie klucza i nazwy zasobu personalizatora

W witrynie Azure portal znajdź klucz i punkt końcowy na stronie **Szybki start** zasobu Personalizer. Zmień wartość `<your-resource-name>` nazwy zasobu Personalizer. Zmień wartość `<your-resource-key>` klucza Personalizer.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Drukowanie bieżącej daty i godziny
Ta funkcja służy do zanotowania czasów rozpoczęcia i zakończenia funkcji iteracyjnej iteracji.

Komórki te nie mają danych wyjściowych. Funkcja wyprowadza bieżącą datę i godzinę wywołania.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Pobierz czas ostatniej aktualizacji modelu

Gdy funkcja `get_last_updated`, funkcja jest wywoływana, funkcja drukuje datę i godzinę ostatniej modyfikacji, że model został zaktualizowany.

Komórki te nie mają danych wyjściowych. Funkcja nie wyprowadza datę ostatniego szkolenia modelu, gdy wywoływane.

Funkcja używa interfejsu API GET REST, aby [uzyskać właściwości modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties).

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Pobierz konfigurację zasad i usług

Sprawdź poprawność stanu usługi za pomocą tych dwóch wywołań REST.

Komórki te nie mają danych wyjściowych. Funkcja powoduje wyprowadzanie wartości usługi, gdy są wywoływane.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Konstruuj adresy URL i odczytuj pliki danych JSON

Ta komórka

* tworzy adresy URL używane w wywołaniach REST
* ustawia nagłówek zabezpieczeń przy użyciu klucza zasobu Personalizer
* ustawia losowy materiał siewny dla identyfikatora zdarzenia rangi
* odczyty w plikach danych JSON
* metoda `get_last_updated` wywołania - polityka uczenia się została usunięta w przykładowym wyjściu
* metoda `get_service_settings` wywołań

Komórka ma dane wyjściowe `get_last_updated` `get_service_settings` z wywołania i funkcji.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Sprawdź, czy dane `rewardWaitTime` `modelExportFrequency` wyjściowe są ustawione na 15 sekund.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Rozwiązywanie problemów z pierwszym wywołaniem REST

Ta poprzednia komórka jest pierwszą komórką, która wywołuje Personalizer. Upewnij się, że kod stanu `<Response [200]>`REST w danych wyjściowych jest . Jeśli zostanie wyświetlony błąd, taki jak 404, ale masz pewność, że klucz zasobu i nazwa są poprawne, załaduj notes ponownie.

Upewnij się, że liczba kawy i użytkowników jest zarówno 4. Jeśli pojawia się błąd, sprawdź, czy zostały przesłane wszystkie 3 pliki JSON.

### <a name="set-up-metric-chart-in-azure-portal"></a>Konfigurowanie wykresu metrycznego w witrynie Azure portal

W dalszej części tego samouczka długotrwały proces 10 000 żądań jest widoczny z przeglądarki z zaktualizowanym polem tekstowym. Może być łatwiej widoczne na wykresie lub jako suma całkowita, gdy kończy się długotrwały proces. Aby wyświetlić te informacje, należy użyć metryki dostarczone z zasobem. Wykres można utworzyć teraz po zakończeniu żądania do usługi, a następnie okresowo odświeżać wykres, gdy trwa długotrwały proces.

1. W witrynie Azure portal wybierz zasób Personalizer.
1. W nawigacji zasobów wybierz **metryki** pod Monitorowanie.
1. Na wykresie wybierz pozycję **Dodaj metrykę**.
1. Obszar nazw zasobu i metryki są już ustawione. Wystarczy wybrać metrykę **udanych połączeń** i agregację **sumy**.
1. Zmień filtr czasu na ostatnie 4 godziny.

    ![Skonfiguruj wykres metryki w witrynie Azure portal, dodając metrykę dla udanych połączeń w ciągu ostatnich 4 godzin.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Na wykresie powinny być widoczne trzy udane wywołania.

### <a name="generate-a-unique-event-id"></a>Generowanie unikatowego identyfikatora zdarzenia

Ta funkcja generuje unikatowy identyfikator dla każdego wywołania rangi. Identyfikator służy do identyfikowania informacji o zaproszeniu do rangi i nagrody. Ta wartość może pochodzić z procesu biznesowego, takiego jak identyfikator widoku sieci Web lub identyfikator transakcji.

Komórka nie ma danych wyjściowych. Funkcja nie wyprowadza unikatowy identyfikator, gdy wywoływane.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Uzyskaj losowego użytkownika, pogodę i porę dnia

Ta funkcja wybiera unikatowy użytkownik, pogodę i porę dnia, a następnie dodaje te elementy do obiektu JSON, aby wysłać je do żądania rangi.

Komórka nie ma danych wyjściowych. Po wywołaniu funkcji zwraca nazwę losowego użytkownika, losową pogodę i losową porę dnia.

Lista 4 użytkowników i ich preferencje - tylko niektóre preferencje są wyświetlane dla zwięzłości:

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Dodaj wszystkie dane kawy

Ta funkcja dodaje całą listę kawy do obiektu JSON, aby wysłać do żądania rangi.

Komórka nie ma danych wyjściowych. Funkcja zmienia się `rankjsonobj` po wywołaniu.


Przykładem cech pojedynczej kawy jest:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Porównaj przewidywanie ze znanymi preferencjami użytkownika

Ta funkcja jest wywoływana po wywołaniu interfejsu API rangi dla każdej iteracji.

Ta funkcja porównuje preferencje użytkownika dotyczące kawy, na podstawie pogody i porę dnia, z sugestią personalizatora dla użytkownika dla tych filtrów. Jeśli sugestia pasuje, zwracany jest wynik 1, w przeciwnym razie wynik wynosi 0. Komórka nie ma danych wyjściowych. Funkcja wyprowadza wynik, gdy jest wywoływana.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Zapętlaj połączenia do rangi i nagrody

Następna komórka jest _główną_ pracą notesu, uzyskanie losowego użytkownika, uzyskanie listy kawy, wysyłanie zarówno do interfejsu API rangi. Porównanie prognozy ze znanymi preferencjami użytkownika, a następnie wysłanie nagrody z powrotem do usługi Personalizator.

Pętla działa `num_requests` przez czasy. Personalizator potrzebuje kilku tysięcy połączeń do rangi i nagrody, aby stworzyć model.

Przykład JSON wysłane do rangi interfejsu API następuje. Lista kawy nie jest kompletna, dla zwięzłości. Możesz zobaczyć cały JSON na `coffee.json`kawę w .

JSON wysłane do interfejsu API rangi:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Odpowiedź JSON z interfejsu API rangi:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Na koniec każda pętla pokazuje losowy wybór użytkownika, pogodę, porę dnia i ustaloną nagrodę. Nagroda w wysokości 1 oznacza, że zasób Personalizer wybrał odpowiedni typ kawy dla danego użytkownika, pogodę i porę dnia.

```console
1 Alice Rainy Morning Latte 1
```

Funkcja wykorzystuje:

* Ranga: INTERFEJS API SPOCZYNK POST, aby [uzyskać rangę](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Nagroda: INTERFEJS API POST REST do [zgłaszania nagrody](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Uruchom dla 10 000 iteracji
Uruchom pętlę Personalizer dla 10 000 iteracji. Jest to długotrwałe wydarzenie. Nie zamykaj przeglądarki z notesem. Okresowo odświeżyj wykres metryk w portalu Azure portal, aby wyświetlić łączną liczbę wywołań usługi. Gdy masz około 20 000 połączeń, wezwanie rangi i nagrody dla każdej iteracji pętli, iteracje są wykonywane.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Wyniki wykresu, aby zobaczyć poprawę

Tworzenie wykresu `count` na `rewards`podstawie i .

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Uruchom wykres dla 10 000 żądań rangi

Uruchom `createChart` tę funkcję.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Czytanie wykresu

Ten wykres pokazuje sukces modelu dla bieżącej domyślnej zasady uczenia się.

![Ten wykres pokazuje sukces bieżącej zasady uczenia się na czas trwania testu.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Idealny cel, który do końca testu, pętla jest średnio wskaźnik sukcesu, który jest bliski 100 procent minus eksploracji. Domyślna wartość eksploracji wynosi 20%.

`100-20=80`

Ta wartość eksploracji znajduje się w witrynie Azure portal dla zasobu Personalizer na stronie **Konfiguracja.**

Aby znaleźć lepsze zasady uczenia się, na podstawie danych do interfejsu API rangi, uruchom [ocenę offline](how-to-offline-evaluation.md) w portalu dla pętli Personalizer.

## <a name="run-an-offline-evaluation"></a>Uruchamianie oceny w trybie offline

1. W witrynie Azure portal otwórz stronę **Oceny** zasobu Personalizer.
1. Wybierz **pozycję Utwórz ocenę**.
1. Wprowadź wymagane dane nazwy oceny i zakresu dat dla oceny pętli. Zakres dat powinien obejmować tylko dni, na których się koncentrujesz w celu oceny.
    ![W witrynie Azure portal otwórz stronę Oceny zasobu Personalizer. Wybierz pozycję Utwórz ocenę. Wprowadź nazwę oceny i zakres dat.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Celem uruchomienia tej oceny w trybie offline jest ustalenie, czy istnieje lepsza zasada uczenia się dla funkcji i działań używanych w tej pętli. Aby znaleźć tę lepszą zasadę uczenia się, upewnij się, że **odnajdowanie optymalizacji** jest włączone.

1. Wybierz **przycisk OK,** aby rozpocząć ocenę.
1. Ta strona **Oceny** zawiera listę nowej oceny i jej bieżącego stanu. W zależności od ilości posiadane dane, ta ocena może zająć trochę czasu. Możesz wrócić do tej strony po kilku minutach, aby zobaczyć wyniki.
1. Po zakończeniu oceny wybierz ocenę, a następnie wybierz **porównanie różnych zasad uczenia się**. Pokazuje dostępne zasady uczenia się i jak będą zachowywać się z danymi.
1. Wybierz zasady uczenia się w tabeli i wybierz pozycję **Zastosuj**. Dotyczy to _najlepszych_ zasad uczenia się do modelu i przekwalifikowania.

## <a name="change-update-model-frequency-to-5-minutes"></a>Zmiana częstotliwości aktualizacji modelu na 5 minut

1. W witrynie Azure portal nadal w zasobie Personalizer wybierz **stronę Konfiguracja.**
1. Zmień **częstotliwość aktualizacji modelu** i **czas oczekiwania na 5** minut i wybierz **zapisz**.

Dowiedz się więcej o [czasie oczekiwania na nagrody](concept-rewards.md#reward-wait-time) i częstotliwości aktualizacji [modelu](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Sprawdź, czy dane `rewardWaitTime` `modelExportFrequency` wyjściowe są ustawione na 5 minut.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Sprawdzanie poprawności nowych zasad uczenia się

Wróć do notesu platformy Azure i kontynuuj, uruchamiając tę samą pętlę, ale tylko dla 2000 iteracji. Okresowo odświeżyj wykres metryk w portalu Azure portal, aby wyświetlić łączną liczbę wywołań usługi. Gdy masz około 4000 połączeń, rangi i nagrody wywołania dla każdej iteracji pętli, iteracje są wykonywane.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Wykres uruchamiania dla 2000 żądań rangi

Uruchom `createChart` tę funkcję.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Przejrzyj drugi wykres

Drugi wykres powinien wykazywać widoczny wzrost prognoz rangi zgodnych z preferencjami użytkownika.

![Drugi wykres powinien wykazywać widoczny wzrost prognoz rangi zgodnych z preferencjami użytkownika.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie zamierzasz kontynuować serii samouczków, wyczyść następujące zasoby:

* Usuń swój projekt usługi Azure Notebook.
* Usuń zasób Personalizer.

## <a name="next-steps"></a>Następne kroki

[Notes Jupyter i pliki danych](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) używane w tym przykładzie są dostępne w repozytorium GitHub dla personalizatora.

