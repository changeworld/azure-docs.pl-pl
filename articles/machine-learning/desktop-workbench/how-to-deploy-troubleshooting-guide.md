---
title: Wdrożenie usługi Azure Machine Learning przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Podręcznik rozwiązywania problemów dotyczących wdrażania i tworzenia usługi
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 4cf86466d5fca4f5095c67a8400643bff29bb56c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649892"
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Rozwiązywanie problemów z wdrożenia usługi i konfigurowanie środowiska
Poniższe informacje mogą pomóc ustalić przyczyny błędów podczas konfigurowania środowiska zarządzania w modelu.

## <a name="model-management-environment"></a>Środowisko zarządzania modelu
### <a name="contributor-permission-required"></a>Wymagane uprawnienia współautora
Należy prawa dostępu współautora do subskrypcji lub grupy zasobów, do skonfigurowania klastra dla wdrożenia usług internetowych.

### <a name="resource-availability"></a>Dostępność zasobów
Musisz mieć za mało zasobów, które są dostępne w Twojej subskrypcji, więc można udostępnić zasoby w środowisku.

### <a name="subscription-caps"></a>Limity subskrypcji
Twoja subskrypcja może mieć limit na rozliczenia, co może uniemożliwić aprowizowanie zasobów w środowisku. Usuń ten limit do włączenia aprowizacji.

### <a name="enable-debug-and-verbose-options"></a>Włączanie debugowania, jak i pełne opcje
Użyj `--debug` i `--verbose` flagi w polecenia Instalatora, aby przedstawiały informacje debugowania i śledzenia, zgodnie z informacjami podanymi środowiska.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Wdrażanie usługi
Poniższe informacje mogą pomóc ustalić przyczyny błędów podczas wdrażania lub podczas wywoływania usługi sieci web.

### <a name="service-logs"></a>Dzienniki usługi
`logs` Opcja service interfejs wiersza polecenia zawiera dane dziennika przez firmy Docker i Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Ustawienia dodatkowe dziennika, należy użyć `--help` (lub `-h`) opcji.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Opcje debugowania, jak i pełne
Użyj `--debug` flagę, aby wyświetlić dzienniki debugowania, ponieważ usługa jest wdrażana.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Użyj `--verbose` flagi, aby zobaczyć szczegółowe informacje, jak wdrażana jest usługa.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Włącz żądania rejestrowania w usłudze App Insights
Ustaw `-l` flagi na wartość true, podczas tworzenia usługi sieci web, aby włączyć rejestrowanie na poziomie żądania. Dzienniki żądania są zapisywane do wystąpienia usługi App Insights dla środowiska na platformie Azure. Wyszukiwania dla tego wystąpienia przy użyciu nazwy środowiska używany podczas `az ml env setup` polecenia.

- Ustaw `-l` wartość true, podczas tworzenia usługi.
- Otwórz App Insights w witrynie Azure portal. Użyj nazwy środowiska, aby znaleźć wystąpienie usługi App Insights.
- Jeden raz w usłudze App Insights polecenie wyszukiwania w górnym menu, aby wyświetlić wyniki.
- Lub przejdź do `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Dodawanie obsługi błędów w oceniania skryptu
Użyj obsługi wyjątków w Twojej `scoring.py` skryptu **Uruchom** funkcja zwraca komunikat o błędzie jako część danych wyjściowych usługi sieci web.

Przykład języka Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Inne typowe problemy
- Jeśli instalowanie narzędzia pip azure-cli-ml zakończy się niepowodzeniem z powodu błędu `cannot find the path specified` na komputerze Windows, należy włączyć obsługę długich ścieżek. Zobacz https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Jeśli `env setup` polecenie kończy się niepowodzeniem `LocationNotAvailableForResourceType`, prawdopodobnie używasz nieprawidłową lokalizację (region) dla maszyny, materiałów szkoleniowych. Upewnić się, że Twoja lokalizacja określony za pomocą `-l` parametr jest `eastus2`, `westcentralus`, lub `australiaeast`.
- Jeśli `env setup` polecenie kończy się niepowodzeniem `Resource quota limit exceeded`, upewnij się, że masz wystarczającej liczby rdzeni, które są dostępne w Twojej subskrypcji i że zasoby nie są używane w górę w innych procesów.
- Jeśli `env setup` polecenie kończy się niepowodzeniem `Invalid environment name. Name must only contain lowercase alphanumeric characters`, upewnij się, że nazwa usługi nie zawiera wielkie litery, symbole lub podkreślenia (_) (jak w *my_environment*).
- Jeśli `service create` polecenie kończy się niepowodzeniem `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, upewnij się, nazwa usługi jest od 3 do 32 znaków; rozpoczyna się i kończy się na małe znaki alfanumeryczne; i nie zawiera wielkie litery, symbole inne niż łącznik (-) i okres ( . ), lub znakiem podkreślenia (_) (jak w *my_webservice*).
- Spróbuj ponownie, jeśli `502 Bad Gateway` wystąpił błąd podczas wywoływania usługi sieci web. Zwykle oznacza to, że kontener nie została jeszcze wdrożona w klastrze.
- Jeśli otrzymasz `CrashLoopBackOff` wystąpił błąd podczas tworzenia usługi, sprawdź w dziennikach. Zazwyczaj jest wynikiem brakujących zależności w **init** funkcji.
