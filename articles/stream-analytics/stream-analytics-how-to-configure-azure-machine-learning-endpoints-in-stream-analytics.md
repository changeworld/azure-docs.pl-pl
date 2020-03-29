---
title: Korzystanie z punktów końcowych usługi Machine Learning w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób używania funkcji zdefiniowanych przez użytkownika w języku maszynowym w usłudze Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 239955025f21d8679cbcf0bbfe68f9070f0217c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426201"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integracja usługi Azure Machine Learning Studio (klasyczna) w usłudze Stream Analytics (wersja zapoznawcza)
Usługa Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika, które wywołują klasyczne punkty końcowe usługi Azure Machine Learning Studio (klasyczne). Obsługa interfejsu API REST dla tej funkcji jest szczegółowo opisana w [bibliotece interfejsu API REST usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Ten artykuł zawiera dodatkowe informacje potrzebne do pomyślnej implementacji tej funkcji w usłudze Stream Analytics. Tutorial został również opublikowany i jest dostępny [tutaj](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Omówienie: Terminologia usługi Azure Machine Learning Studio (klasyczna)
Microsoft Azure Machine Learning Studio (klasyczny) zapewnia narzędzie współpracy, przeciągania i upuszczania, które można użyć do tworzenia, testowania i wdrażania rozwiązań analizy predykcyjnej na danych. To narzędzie nosi nazwę *usługi Azure Machine Learning Studio (classic).* Studio służy do interakcji z zasobami uczenia maszynowego i łatwe tworzenie, testowanie i iterować na projekcie. Te zasoby i ich definicje znajdują się poniżej.

* **Obszar roboczy:** *Obszar roboczy* jest kontenerem, który przechowuje wszystkie inne zasoby usługi Machine Learning razem w kontenerze do zarządzania i kontroli.
* **Eksperyment:** *Eksperymenty* są tworzone przez analityków danych w celu wykorzystania zestawów danych i uczenia modelu uczenia maszynowego.
* **Punkt końcowy:** *Punkty końcowe* to obiekt usługi Azure Machine Learning Studio (klasyczny) używany do wprowadzania funkcji jako danych wejściowych, stosowania określonego modelu uczenia maszynowego i zwracania wyników wynikowych.
* **Scoring Webservice:** *Usługa webu oceniania* jest zbiorem punktów końcowych, jak wspomniano powyżej.

Każdy punkt końcowy ma apis do wykonywania partii i synchroniczne wykonanie. Usługa Stream Analytics używa synchronicznego wykonywania. Określona usługa nosi nazwę [usługi żądania/odpowiedzi](../machine-learning/studio/consume-web-services.md) w usłudze Azure Machine Learning Studio (klasyczna).

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Zasoby usługi Machine Learning potrzebne do zadań usługi Stream Analytics
Na potrzeby przetwarzania zadań usługi Stream Analytics punkt końcowy żądania/odpowiedzi, [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)i definicja swagger są niezbędne do pomyślnego wykonania. Usługa Stream Analytics ma dodatkowy punkt końcowy, który konstruuje adres URL punktu końcowego swagger, wyszukuje interfejs i zwraca domyślną definicję UDF dla użytkownika.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Konfigurowanie usługi UDF usługi Stream Analytics i Machine Learning za pośrednictwem interfejsu REST API
Za pomocą interfejsów API REST można skonfigurować zadanie do wywoływania funkcji języka maszynowego platformy Azure. Kroki tego procesu są następujące:

1. Tworzenie zadania usługi Stream Analytics
2. Definiowanie danych wejściowych
3. Definiowanie danych wyjściowych
4. Tworzenie funkcji zdefiniowanej przez użytkownika (UDF)
5. Napisz transformację usługi Stream Analytics, która wywołuje UDF
6. Uruchamianie zadania

## <a name="creating-a-udf-with-basic-properties"></a>Tworzenie UDF z podstawowymi właściwościami
Na przykład poniższy przykładowy kod tworzy skalarny UDF o nazwie *newudf,* który wiąże się z punktem końcowym usługi Azure Machine Learning Studio (klasycznym). Należy zauważyć, że *punkt końcowy* (usługa URI) można znaleźć na stronie pomocy interfejsu API dla wybranej usługi i *apiKey* można znaleźć na stronie głównej usług.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Przykładowa treść żądania:

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

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Wywołanie punktu końcowego odzyskiwaniadefaultDefinition dla domyślnego udf
Po utworzeniu szkieletu UDF wymagana jest pełna definicja UDF. Punkt końcowy retrieveDefaultDefinition pomaga uzyskać domyślną definicję funkcji skalarnej powiązanej z punktem końcowym usługi Azure Machine Learning Studio (klasycznym). Ładunek poniżej wymaga uzyskania domyślnej definicji UDF dla funkcji skalarnej, która jest powiązana z punktem końcowym usługi Azure Machine Learning. Nie określa rzeczywistego punktu końcowego, ponieważ został już dostarczony podczas żądania PUT. Usługa Stream Analytics wywołuje punkt końcowy podany w żądaniu, jeśli jest on podany jawnie. W przeciwnym razie używa tego, do którego pierwotnie się odwoływano. W tym miejscu UDF przyjmuje parametr pojedynczego ciągu (zdanie) i zwraca pojedyncze dane wyjściowe ciągu typu, który wskazuje etykietę "sentiment" dla tego zdania.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Przykładowa treść żądania:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Przykładowe dane wyjściowe to będzie wyglądać mniej więcej poniżej.

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

## <a name="patch-udf-with-the-response"></a>Popraw UDF z odpowiedzią
Teraz UDF musi być poprawione z poprzedniej odpowiedzi, jak pokazano poniżej.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Treść żądania (dane wyjściowe z retrieveDefaultDefinition):

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

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementowanie transformacji usługi Stream Analytics w celu wywołania UDF
Teraz kwerendy UDF (tutaj o nazwie scoreTweet) dla każdego zdarzenia wejściowego i napisać odpowiedź dla tego zdarzenia do danych wyjściowych.

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

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
