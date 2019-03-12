---
title: Tworzenie zależności wyzwalacza okna wirowania w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć zależności na wyzwalacz okna wirowania w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: f3a547f5b953262d7263d1be0897161cf4091a1d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574393"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Tworzenie zależności wyzwalacza okna wirowania

Ten artykuł zawiera kroki, aby utworzyć zależność na wyzwalacz okna wirowania. Aby uzyskać ogólne informacje o wyzwalaczach okna wirowania, zobacz [Tworzenie wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md).

Aby można było skompilować łańcuch zależności i upewnij się, że wyzwalacz jest wykonywany tylko po pomyślnym wykonaniu innego wyzwalacza w usłudze data factory, należy używać tej funkcji zaawansowanych, aby utworzyć zależność okna wirowania.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Tworzenie zależności w interfejs użytkownika usługi Data Factory

Aby utworzyć zależność względem wyzwalacza, wybierz **wyzwalacza > Zaawansowane > New**, a następnie wybierz wyzwalacz są zależne od przy użyciu odpowiednich przesunięcie i rozmiaru. Wybierz **Zakończ** i publikowanie zmian fabryki danych, zależności, które zostały wprowadzone.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Właściwości zależności okna wirowania

Zależność wyzwalacza okna wirowania ma następujące właściwości:

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

Poniższa tabela zawiera listę atrybutów niezbędnej do zdefiniowania zależności okno wirowania.

| **Nazwa właściwości** | **Opis**  | **Typ** | **Wymagane** |
|---|---|---|---|
| Wyzwalacz  | Wszystkie istniejące wyzwalacze okna wirowania są wyświetlane w tej listy w dół. Wybierz wyzwalacz przyjmą zależności.  | TumblingWindowTrigger | Yes |
| Przesunięcie | Przesunięcie wyzwalacza zależności. Podaj wartość w formacie zakresu i przesunięcia dodatnie i ujemne są dozwolone. Ten parametr jest wymagany, jeśli wyzwalacz znajduje się w zależności od samego i we wszystkich innych przypadkach jest to opcjonalne. Zależność Self powinny zawsze być ujemne przesunięcie. | Zakres czasu | Zależność samoobsługowego: Tak, inne: Nie |
| Rozmiar okna | Rozmiar okna wirowania zależności. Podaj wartość w formacie zakresu czasu. Ten parametr jest opcjonalny. | Zakres czasu | Nie  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Właściwości zależności self okna wirowania

W scenariuszach, w której wyzwalacz nie powinien kontynuować do następnego okna, dopóki nie zakończy się pomyślnie poprzedniego okna twórz zależność samodzielnie. Wyzwalacz zależności Self zostaną opisane poniżej właściwości:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

Poniżej przedstawiono przykłady scenariuszy i użycie okna wirowania właściwości zależności.

## <a name="dependency-offset"></a>Przesunięcie zależności

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Rozmiar zależności

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Funkcja samoobsługowego zależności

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Scenariusze użycia

### <a name="dependency-on-another-tumbling-window-trigger"></a>Zależność od innego zarówno wyzwalacz okna wirowania

Na przykład codzienne zadania przetwarzania danych telemetrycznych w zależności od innego zadania codziennego agregowania ostatnich siedmiu dni danych wyjściowych i generuje 7 dniowy stopniowe okna strumieni:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Zależność od samego

Codzienne zadania bez przerw w strumieniach danych wyjściowych zadania:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Monitoruj zależności

Możesz monitorować łańcuch zależności, a odpowiednie systemu windows z wyzwalacza Uruchom monitorowania strony. Przejdź do **monitorowania > wyzwolić uruchamianie**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Kliknij szkło wyszukiwanie, aby wyświetlić wszystkie zależne wyzwalacz, który jest uruchamiany z wybranego okna.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Kolejne kroki

Przegląd [Tworzenie wyzwalacza okna wirowania](how-to-create-tumbling-window-trigger.md).