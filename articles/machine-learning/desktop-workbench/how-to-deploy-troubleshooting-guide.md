---
title: "Przewodnik rozwiązywania problemów wdrożenia usługi Azure Machine Learning | Dokumentacja firmy Microsoft"
description: "Podręcznik rozwiązywania problemów dotyczących wdrażania i tworzenia usługi"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 7b8d2f4fb52c6d4faed8e813779e3b01d1817355
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Rozwiązywanie problemów z wdrażania usługi i konfigurowanie środowiska
Poniższe informacje mogą pomóc w określeniu przyczyny błędów, podczas konfigurowania środowiska zarządzania modelu.

## <a name="model-management-environment"></a>Model zarządzania środowiskiem
### <a name="contributor-permission-required"></a>Wymagane uprawnienia współautora
Należy współautora dostęp do subskrypcji lub grupy zasobów do skonfigurowania klastra dla wdrożenia usług sieci web.

### <a name="resource-availability"></a>Dostępność zasobów
Musisz ma za mało zasobów, które są dostępne w Twojej subskrypcji, więc może zapewnić zasobów środowiska.

### <a name="subscription-caps"></a>Caps subskrypcji
Subskrypcja może być zakończenia na rozliczenia, co może uniemożliwić udostępniania zasobów środowiska. Usunięcie tego centralnych zasad dostępu, aby umożliwić inicjowanie obsługi administracyjnej.

### <a name="enable-debug-and-verbose-options"></a>Włącz opcje pełne i debugowania
Użyj `--debug` i `--verbose` flagi w polecenia Instalatora, aby wyświetlić informacje debugowania i śledzenia, zgodnie z informacjami podanymi środowiska.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Wdrożenie usługi
Następujące informacje mogą pomóc w określeniu przyczyny błędów podczas wdrażania lub podczas wywoływania usługi sieci web.

### <a name="service-logs"></a>dzienniki usługi
`logs` Opcja interfejsu wiersza polecenia usługi zawiera dane dziennika z Docker i Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Ustawienia dziennika dodatkowe, użyj `--help` (lub `-h`) opcja.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Opcje debugowania i pełne
Użyj `--debug` Flaga jako wdrażana jest usługa dzienników debugowania.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Użyj `--verbose` flagę, aby wyświetlić dodatkowe szczegóły, jak wdrażania usługi.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Włącz rejestrowanie w usłudze App Insights żądania
Ustaw `-l` flagi na wartość true, podczas tworzenia usługi sieci web, aby włączyć rejestrowanie na poziomie żądania. Dzienniki żądania są zapisywane do wystąpienia usługi App Insights dla środowiska na platformie Azure. Wyszukiwania dla tego wystąpienia przy użyciu nazwy środowiska, używane podczas `az ml env setup` polecenia.

- Ustaw `-l` na true, podczas tworzenia usługi.
- Otwórz aplikację wgląd w portalu Azure. Znajdź wystąpienie usługi App Insights przy użyciu nazwy środowiska.
- Raz w aplikacji szczegółowych informacji, kliknij wyszukiwania w menu górnym, aby wyświetlić wyniki.
- Lub przejdź do `Analytics`  >  `Exceptions`  >  `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Dodawanie obsługi błędów w oceniania skryptu
Użyj obsługi wyjątków w Twojej `scoring.py` skryptu **Uruchom** funkcja zwraca komunikat o błędzie w ramach danych wyjściowych usługi sieci web.

Przykład Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Inne typowe problemy
- Jeśli instalacja pip uczenie maszynowe interfejsu wiersza polecenia azure zakończy się niepowodzeniem z powodu błędu `cannot find the path specified` na komputerze z systemem Windows, należy włączyć obsługę długich ścieżek. Zobacz https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Jeśli `env setup` polecenia nie powiodło się `LocationNotAvailableForResourceType`, prawdopodobnie używasz niewłaściwej lokalizacji (regionu) dla komputera materiałów szkoleniowych. Upewnij się, lokalizacja określony za pomocą `-l` parametr jest `eastus2`, `westcentralus`, lub `australiaeast`.
- Jeśli `env setup` polecenia nie powiodło się `Resource quota limit exceeded`, upewnij się, że masz wystarczająco dużo rdzeni dostępne w Twojej subskrypcji i że zasoby nie są używane w górę w innych procesów.
- Jeśli `env setup` polecenia nie powiodło się `Invalid environment name. Name must only contain lowercase alphanumeric characters`, upewnij się, że nazwa usługi nie zawiera wielkie litery, symbole lub podkreślenia (_) (jak w *my_environment*).
- Jeśli `service create` polecenia nie powiodło się `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, upewnij się, że nazwa usługi jest od 3 do 32 znaków; rozpoczyna się i kończy się wyrazem małe znaki alfanumeryczne; i nie zawiera wielkie litery, symbole inne niż łącznik (-) i okres ( . ), lub podkreślenia (_) (jak w *my_webservice*).
- Spróbuj ponownie, jeśli zostanie wyświetlony `502 Bad Gateway` wystąpił błąd podczas wywoływania usługi sieci web. Zwykle oznacza to, że kontener nie został jeszcze wdrożony do klastra.
- Jeśli otrzymasz `CrashLoopBackOff` wystąpił błąd podczas tworzenia usługi, sprawdź dzienniki. Zazwyczaj jest wynikiem brakujące zależności w **init** funkcji.
