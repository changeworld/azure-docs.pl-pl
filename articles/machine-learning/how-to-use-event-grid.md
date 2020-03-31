---
title: Tworzenie przepływów pracy uczenia maszynowego opartych na zdarzeniach
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z siatki zdarzeń za pomocą usługi Azure Machine Learning, aby włączyć rozwiązania oparte na zdarzeniach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129737"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>Tworzenie przepływów pracy uczenia maszynowego opartych na zdarzeniach (wersja zapoznawcza)

[Usługa Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) obsługuje zdarzenia usługi Azure Machine Learning. Można subskrybować i używać zdarzeń, takich jak zmiana stanu uruchamiania, zakończenie uruchamiania, rejestracja modelu, wdrażanie modelu i wykrywanie dryfu danych w obszarze roboczym.

Aby uzyskać więcej informacji na temat typów zdarzeń, zobacz [Integracja usługi Azure Machine Learning z siatką zdarzeń](concept-event-grid-integration.md) usługi Azure Machine Learning i [schemat siatki zdarzeń usługi Azure Machine Learning.](/azure/event-grid/event-schema-machine-learning)

Użyj siatki zdarzeń, aby włączyć typowe scenariusze, takie jak:

* Wysyłanie wiadomości e-mail po niepowodzeniu uruchamiania i uruchamianie ukończenia
* Użyj funkcji platformy Azure po zarejestrowaniu modelu
* Przesyłanie strumieniowe zdarzeń z usługi Azure Machine Learning do różnych punktów końcowych
* Wyzwalanie potoku ML po wykryciu dryfu

> [!NOTE] 
> Obecnie runStatusZmienia zdarzenia wyzwalane tylko wtedy, gdy stan uruchomienia **nie powiódł się**
>

## <a name="prerequisites"></a>Wymagania wstępne
* Dostęp współautora lub właściciela do obszaru roboczego usługi Azure Machine Learning, dla którego utworzysz zdarzenia.

### <a name="configure-eventgrid-using-the-azure-portal"></a>Konfigurowanie usługi EventGrid przy użyciu portalu Azure

1. Otwórz [witrynę Azure portal](https://portal.azure.com) i przejdź do obszaru roboczego usługi Azure Machine Learning.

1. Na lewym pasku wybierz pozycję __Zdarzenia,__ a następnie wybierz pozycję **Subskrypcje zdarzeń**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Wybierz typ zdarzenia do wykorzystania. Na przykład na poniższym zrzucie ekranu wybrano __zarejestrowany model,__ __Wdrożony model,__ __Uruchom ukończono__i __wykryto dryft zestawu danych:__

    ![typ zdarzenia dodawania](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Wybierz punkt końcowy, do który ma opublikować zdarzenie. Na poniższym zrzucie ekranu __Centrum zdarzeń__ jest wybranym punktem końcowym:

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

Po potwierdzeniu wyboru kliknij przycisk __Utwórz__. Po konfiguracji te zdarzenia zostaną wypchnięte do punktu końcowego.


### <a name="configure-eventgrid-using-the-cli"></a>Konfigurowanie programu EventGrid przy użyciu interfejsu wiersza polecenia

Można zainstalować najnowszą [platformę Interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)lub użyć usługi Azure Cloud Shell, która jest dostarczana w ramach subskrypcji platformy Azure.

Aby zainstalować rozszerzenie Siatki zdarzeń, użyj następującego polecenia z interfejsu wiersza polecenia:

```azurecli-interactive
az add extension --name eventgrid
```

W poniższym przykładzie pokazano, jak wybrać subskrypcję platformy Azure i tworzy e nową subskrypcję zdarzeń dla usługi Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>Filtruj zdarzenia

Podczas konfigurowania zdarzeń można zastosować filtry tylko do wyzwalania określonych danych zdarzeń. W poniższym przykładzie dla zdarzeń zmiany stanu uruchamiania można filtrować według typów uruchamiania. Zdarzenie wyzwala tylko wtedy, gdy kryteria są spełnione. Zapoznaj się ze [schematem siatki zdarzeń usługi Azure Machine Learning,](/azure/event-grid/event-schema-machine-learning) aby dowiedzieć się więcej o danych zdarzeń, według których można filtrować. 

1. Przejdź do witryny Azure portal, wybierz nową subskrypcję lub istniejącą. 

1. Wybierz kartę filtry i przewiń w dół do opcji Filtry zaawansowane. W **klucz** i **wartość** podaj typy właściwości, które chcesz filtrować. W tym miejscu widać zdarzenie będzie wyzwalać tylko wtedy, gdy typ uruchomienia jest uruchomienie potoku lub przebieg kroku potoku.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrowanie zdarzeń":::

## <a name="sample-scenarios"></a>Przykładowe scenariusze

### <a name="use-a-logic-app-to-send-email-alerts"></a>Wysyłanie alertów e-mail za pomocą aplikacji logiki

Skorzystaj z [usługi Azure Logic Apps,](https://docs.microsoft.com/azure/logic-apps/) aby skonfigurować wiadomości e-mail dla wszystkich zdarzeń. Dostosuj warunki i określ odbiorców, aby umożliwić współpracę i świadomość w zespołach współpracujących ze sobą.

1. W witrynie Azure portal przejdź do obszaru roboczego usługi Azure Machine Learning i wybierz kartę zdarzenia z lewego paska. W tym miejscu wybierz pozycję __Aplikacje logiki__. 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. Zaloguj się do interfejsu użytkownika aplikacji logiki i wybierz usługę uczenia maszynowego jako typ tematu. 

    ![wybierz typ tematu](./media/how-to-use-event-grid/select-topic-type.png)

1. Wybierz zdarzenia, dla których mają być zgłoszone. Na przykład poniższy zrzut ekranu __RunCompleted__.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Metody filtrowania można użyć w powyższej sekcji lub dodać filtry, aby wyzwolić tylko aplikację logiki w podzbiorze typów zdarzeń. Na poniższym zrzucie ekranu używany jest __filtr prefiksu__ __/datadriftID/runs/.__

    ![zdarzenia filtracyjne](./media/how-to-use-event-grid/filtering-events.png)

1. Następnie dodaj krok, aby korzystać z tego zdarzenia i wyszukaj pozycję e-mail. Istnieje kilka różnych kont pocztowych, których można używać do odbierania zdarzeń. Można również skonfigurować warunki dotyczące wysyłania alertu e-mail.

    ![select-email-akcja](./media/how-to-use-event-grid/select-email-action.png)

1. Wybierz __pozycję Wyślij wiadomość e-mail__ i wypełnij parametry. W temacie można dołączyć __typ zdarzenia__ i __temat,__ aby ułatwić filtrowanie zdarzeń. Można również dołączyć łącze do strony obszaru roboczego dla uruchomień w treści wiadomości. 

    ![konfigurowanie treści wiadomości e-mail](./media/how-to-use-event-grid/configure-email-body.png)

1. Aby zapisać tę akcję, wybierz **pozycję Zapisz jako** w lewym rogu strony. Z prawego paska, który się pojawi, potwierdź utworzenie tej akcji.

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>Używanie aplikacji logiki do wyzwalania przepływów pracy ponownego trenowania w przypadku dryfowania danych

Modele zestarzeć w czasie i nie pozostają przydatne w kontekście, w który jest uruchomiony w. Jednym ze sposobów, aby stwierdzić, czy nadszedł czas, aby przekwalifikować model jest wykrywanie dryfu danych. 

W tym przykładzie pokazano, jak używać siatki zdarzeń z aplikacją logiki Azure do wyzwalania ponownego szkolenia. W przykładzie wyzwala potok usługi Azure Data Factory, gdy występuje dryf danych między szkolenia modelu i obsługujących zestawy danych.

Przed rozpoczęciem wykonaj następujące czynności:

* Konfigurowanie monitora zestawu danych w celu [wykrywania dryfu danych]( https://aka.ms/datadrift) w obszarze roboczym
* Utwórz opublikowany [potok usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/).

W tym przykładzie prosty potok fabryki danych służy do kopiowania plików do magazynu obiektów blob i uruchamiania opublikowanego potoku usługi Machine Learning. Aby uzyskać więcej informacji na temat tego scenariusza, zobacz jak skonfigurować [krok uczenia maszynowego w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![adf-mlpipeline-etap](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Zacznij od utworzenia aplikacji logiki. Przejdź do [witryny Azure portal](https://portal.azure.com), wyszukaj aplikacje logiki i wybierz pozycję Utwórz.

    ![szukaj-logika-aplikacja](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Wypełnij wymagane informacje. Aby uprościć środowisko, należy użyć tej samej subskrypcji i grupy zasobów, co potok fabryki danych platformy Azure i obszar roboczy usługi Azure Machine Learning.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po utworzeniu aplikacji logiki wybierz pozycję __Kiedy występuje zdarzenie zasobu siatki zdarzeń__. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Zaloguj się i wypełnij szczegóły wydarzenia. Ustaw __nazwę zasobu__ na nazwę obszaru roboczego. Ustaw __typ zdarzenia__ na __DatasetDriftDetected__.

    ![zdarzenie logowania i dodawania](./media/how-to-use-event-grid/login-and-add-event.png)

1. Dodaj nowy krok i wyszukaj __usługę Azure Data Factory__. Wybierz __pozycję Utwórz przebieg potoku__. 

    ![tworzenie-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Zaloguj się i określ opublikowany potok usługi Azure Data Factory do uruchomienia.

    ![określenie potoku adf](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Zapisz i utwórz aplikację logiki za pomocą przycisku **zapisz** w lewym górnym rogu strony. Aby wyświetlić aplikację, przejdź do obszaru roboczego w [witrynie Azure portal](https://portal.azure.com) i kliknij **zdarzenia**.

    ![pokaż-logika-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Teraz potok fabryki danych jest wyzwalany po wystąpieniu dryfu. Wyświetl szczegóły dotyczące przebiegu dryfu danych i potoku uczenia maszynowego w [nowym portalu obszaru roboczego](https://ml.azure.com). 

![widok w obszarze roboczym](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Wdrażanie modelu opartego na znacznikach za pomocą usługi Azure Functions

Obiekt modelu usługi Azure Machine Learning zawiera parametry, na których można przestawiać wdrożenia, takie jak nazwa modelu, wersja, tag i właściwość. Zdarzenie rejestracji modelu można wyzwolić punkt końcowy i można użyć funkcji platformy Azure do wdrożenia modelu na podstawie wartości tych parametrów.

Na przykład zobacz [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) repozytorium i wykonaj kroki opisane w pliku **readme.**

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o dostępnych zdarzeniach, zobacz [schemat zdarzeń usługi Azure Machine Learning](/azure/event-grid/event-schema-machine-learning)
