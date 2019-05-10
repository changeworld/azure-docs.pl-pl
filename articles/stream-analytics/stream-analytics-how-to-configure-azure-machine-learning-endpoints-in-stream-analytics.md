---
title: Użyj punktów końcowych usługi Machine Learning w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób użycia funkcji zdefiniowanych przez użytkownika języka maszyny w usłudze Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: dfb34f8c0fca792618860e0a8d5a1bf1736f3611
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416055"
---
# <a name="machine-learning-integration-in-stream-analytics-preview"></a>Usługi Machine Learning integracji w usłudze Stream Analytics (wersja zapoznawcza)
Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika, które wywołują z punktami końcowymi usługi Azure Machine Learning. Obsługa interfejsu API REST, aby ta funkcja została szczegółowo opisana w [biblioteki interfejsu API REST usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Ten artykuł zawiera dodatkowe informacje wymagane do pomyślnego wdrożenia tej funkcji w usłudze Stream Analytics. Samouczek również została opublikowana i jest dostępny [tutaj](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Omówienie: Terminologii platformy Azure Machine Learning
Microsoft Azure Machine Learning zapewnia narzędzie do współpracy, obsługiwane metodą przeciągania i upuszczania, które służy do tworzenia, testowania i wdrażania rozwiązań do analizy predykcyjnej na podstawie posiadanych danych. To narzędzie jest nazywany *Azure Machine Learning Studio*. Studio służy do interakcji z zasobami usługi Machine Learning i łatwego tworzenia, testowania i wykonania iteracji projektu. Te zasoby i ich definicje są poniżej.

* **Obszar roboczy**: *Obszaru roboczego* jest kontenerem, który zawiera inne zasoby usługi Machine Learning ze sobą w kontenerze zarządzania i kontroli.
* **Eksperyment**: *Eksperymenty* są tworzone przez analityków danych korzystanie z zestawów danych i szkolenie modelu uczenia maszynowego.
* **Punkt końcowy**: *Punkty końcowe* jest obiektem usługi Azure Machine Learning umożliwia wybranie funkcji jako dane wejściowe, model uczenia maszynowego określonego i zwraca wynik ocenami.
* **Ocenianie usługi sieci Web**: A *oceny Usługa sieci Web* jest kolekcją punktów końcowych, jak wspomniano powyżej.

Każdy punkt końcowy ma interfejsów API w celu wykonywania wsadowego i wykonania synchroniczne. Stream Analytics używa synchronicznej. Nosi nazwę określonej usługi [żądań/odpowiedzi usługi](../machine-learning/studio/consume-web-services.md) w usłudze Azure Machine Learning studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Machine Learning zasoby wymagane dla zadania usługi Stream Analytics
Na potrzeby usługi Stream Analytics, zadania przetwarzania punktu końcowego żądania/odpowiedzi [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md), i definicji struktury swagger są wszystkie niezbędne do pomyślnego wykonania. Stream Analytics ma dodatkowy punkt końcowy, który konstruuje adres url punktu końcowego struktury swagger, wyszukuje interfejsu i zwraca domyślną definicją funkcji zdefiniowanej przez użytkownika dla użytkownika.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurowanie usługi Stream Analytics i Machine Learning funkcji zdefiniowanej przez użytkownika za pośrednictwem interfejsu API REST
Za pomocą interfejsów API REST można skonfigurować zadania do wywoływania funkcji języka maszyny platformy Azure. Dostępne są następujące czynności:

1. Tworzenie zadania usługi Stream Analytics
2. Zdefiniuj wejściowe
3. Zdefiniuj dane wyjściowe
4. Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)
5. Zapis przekształcania usługi Stream Analytics, która wywołuje funkcji zdefiniowanej przez użytkownika
6. Uruchamianie zadania

## <a name="creating-a-udf-with-basic-properties"></a>Tworzenie funkcji zdefiniowanej przez użytkownika za pomocą właściwości podstawowe
Na przykład następujący przykładowy kod tworzy skalarny systemu plików UDF o nazwie *newudf* wiąże punktu końcowego usługi Azure Machine Learning. Należy pamiętać, że *punktu końcowego* (identyfikator URI usługi) można znaleźć na stronie pomocy interfejsu API dla wybranej usługi i *apiKey* można znaleźć na stronie głównej usługi.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Przykład treść żądania:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Wywołanie punktu końcowego RetrieveDefaultDefinition domyślnego systemu plików UDF
Po szkielet UDF kompletną definicję funkcji zdefiniowanej przez użytkownika jest wymagana. Punkt końcowy RetrieveDefaultDefinition ułatwiają definicji domyślnej dla funkcji skalarnej, który jest powiązany z punktu końcowego usługi Azure Machine Learning. Ładunek poniżej wymaga można pobrać definicji domyślnej funkcji zdefiniowanej przez użytkownika, dla funkcji skalarnej, który jest powiązany z punktu końcowego usługi Azure Machine Learning. Nie go określić istniejący punkt końcowy, ponieważ już zostało podane podczas wykonywania żądania PUT. Stream Analytics wywołuje punkt końcowy, podany w żądaniu, jeśli podano jawnie. W przeciwnym razie używa jednego pierwotnie odwołania. W tym miejscu przyjmuje funkcji zdefiniowanej przez użytkownika, w jeden ciąg parametrów (zdanie) i zwraca pojedynczy danych wyjściowych typu ciąg, co oznacza "opinie" etykietę dla tego zdania.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Przykład treść żądania:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Przykład danych wyjściowych, to czy Szukaj coś w rodzaju poniżej.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Poprawka funkcji zdefiniowanej przez użytkownika z odpowiedzią
Teraz funkcji zdefiniowanej przez użytkownika należy poprawić z poprzedniej odpowiedzi, jak pokazano poniżej.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Treść żądania (dane wyjściowe z RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementowanie przekształcania usługi Stream Analytics do wywołania funkcji zdefiniowanej przez użytkownika
Teraz zapytanie funkcji zdefiniowanej przez użytkownika (w tym miejscu o nazwie scoreTweet) dla każdego zdarzenia wejściowe i zapisywać dane wyjściowe odpowiedzi dla tego zdarzenia.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
