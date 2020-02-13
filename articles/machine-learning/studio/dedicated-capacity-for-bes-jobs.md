---
title: Zadania usługi Azure Batch
titleSuffix: ML Studio (classic) - Azure
description: Omówienie zadań usług Azure Batch Services dla Machine Learning Studio (klasycznych). Przetwarzanie puli usługi Batch pozwala na tworzenie pul, na których można przesłać zadania usługi batch.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 0bb4157ff77ef57f377ba0c6a08bfe8529f663f4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169271"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Usługa Azure Batch dla zadań Azure Machine Learning Studio (klasycznych)

Machine Learning Batch Pool przetwarzania zapewnia skalowanie zarządzanych przez klienta usługa wykonywania wsadowego usługi Azure Machine Learning. Przetwarzanie wsadowe klasycznego uczenia maszynowego odbywa się w środowisku z wieloma dzierżawami, która ogranicza liczbę równoczesnych zadań możesz przesłać, a zadania są umieszczane w kolejce na podstawie pierwszego wejściu — pierwszy na wyjściu. Niepewności oznacza, że nie można dokładnie przewidzieć, kiedy zadanie zostanie wykonane.

Przetwarzanie puli usługi Batch pozwala na tworzenie pul, na których można przesłać zadania usługi batch. Możesz kontrolować rozmiar puli i do której puli przesyłania zadania. Uruchamia zadanie usługi BES w własną przestrzeń przetwarzanie, przetwarzanie przewidywalną wydajność i zdolność do tworzenia pul zasobów, odpowiadające obciążenie przetwarzania, która zostanie przesłana.

> [!NOTE]
> Aby można było utworzyć pulę, należy dysponować nową Menedżer zasobów opartą na Machine Learning usłudze sieci Web. Po utworzeniu można uruchomić dowolną usługę sieci Web BES, na przykład na podstawie Menedżer zasobów nowej i klasycznej, w puli.

## <a name="how-to-use-batch-pool-processing"></a>Jak używać przetwarzania puli usługi Batch

Konfiguracja przetwarzania puli usługi Batch jest obecnie dostępna za pośrednictwem witryny Azure portal. Aby użyć przetwarzania puli usługi Batch, musisz mieć:

-   Wywołaj CSS, aby utworzyć konto puli usługi Batch i uzyskać adres URL usługi puli i klucza autoryzacji
-   Tworzenie nowego Menedżera zasobów na podstawie usługi sieci web i plan rozliczeniowy

Aby utworzyć konto, wywołaj dział obsługi klienta firmy Microsoft i pomocy technicznej (CSS) i podaj identyfikator subskrypcji. CSS będzie działać z Tobą ustalimy odpowiednią pojemność dla danego scenariusza. CSS, następnie konfiguruje Twoje konto za pomocą maksymalna liczba pul, które można tworzyć i maksymalną liczbę maszyn wirtualnych (VM), które można umieścić w każdej puli. Po skonfigurowaniu konta podano adres URL Twojej puli usługi i klucza autoryzacji.

Po utworzeniu konta adresu URL usługi puli i autoryzacji klucz został użyty do wykonywania operacji zarządzania w puli w puli usługi Batch.

![Architektura usługi puli usługi Batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Można utworzyć pule przez wywołanie operacji Utwórz pulę na adres URL usługi puli, otrzymany od CSS. Podczas tworzenia puli Określ liczbę maszyn wirtualnych i adres URL swagger.json nowego Menedżera zasobów na podstawie usługi sieci web Machine Learning. Ta usługa sieci web znajduje się związek rozliczeń. Usługa Batch Pool używa swagger.json można skojarzyć z planem rozliczeniowym. W puli można uruchomić dowolną usługę sieci Web BES, na przykład na podstawie Menedżer zasobów nowej i klasycznej.

Może korzystać dowolna usługa sieci web opartych na nowe Resource Manager, ale należy pamiętać, czy naliczania opłat za zadania są naliczane względem planu rozliczeniowego skojarzone z tą usługą. Można tworzyć usługi sieci web i nowy plan rozliczeniowy specjalnie do uruchamiania zadań puli usługi Batch.

Aby uzyskać więcej informacji na temat tworzenia usług sieci Web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](deploy-a-machine-learning-web-service.md).

Po utworzeniu puli, możesz przesłać zadanie usługi BES usługi sieci web przy użyciu adresu URL żądania wsadowego. Można go przesłać do puli lub przetwarzanie wsadowe klasycznego. Do treści żądania przesłania zadania, aby przesłać zadanie do puli usługi Batch z przetwarzaniem, Dodaj następujący parametr:

"AzureBatchPoolId": "&lt;Identyfikator puli&gt;"

Jeśli nie dodasz parametru, zadanie zostanie uruchomione w środowisku procesu klasycznej usługi batch. Jeśli pula zawiera dostępnych zasobów, zadanie zostanie uruchomione działanie od razu. Jeśli pula nie ma bezpłatnych zasobów, zadania znajduje się w kolejce do momentu zasób jest dostępny.

Jeśli okaże się, że regularnie dotrzeć do pojemności w pulach i wymaga zwiększenia pojemności, można wywołać CSS i pracować z przedstawicielem, aby zwiększyć limity przydziału.

Przykładowe żądanie:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Uwagi dotyczące korzystania z przetwarzania w puli usługi Batch

Przetwarzanie puli usługi Batch jest zawsze na płatne usługi i który, musisz skojarzyć go z Menedżera zasobów na podstawie planu rozliczeniowego. Rozliczenie jest przeprowadzane tylko przez liczbę godzin obliczeniowych, na którym uruchomiona jest puli; bez względu na liczbę zadań, które są uruchamiane podczas tej puli czasu. W przypadku tworzenia puli możesz są rozliczane za godziny obliczeń poszczególnych maszyn wirtualnych w puli do momentu usunięcia puli nawet wtedy, gdy w puli są uruchomione żadne zadania usługi batch. Rozliczenia dla maszyn wirtualnych rozpoczyna się po zakończeniu ich inicjowania obsługi administracyjnej i zatrzymuje, gdy zostały usunięte. Możesz użyć dowolnego planu znajdującego się na [stronie cennika Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Przykład rozliczeń:

Jeśli tworzenie puli usługi Batch przy użyciu 2 maszyny wirtualne i usuniesz go po 24 godzinach planu rozliczeniowego obciąża 48 godzin obliczeniowych; niezależnie od tego, ile zadania zostały uruchomione w tym okresie.

Jeśli tworzenie puli usługi Batch za pomocą 4 maszyny wirtualne i usuniesz go po 12 godzin, po stronie debetowej 48 godzin obliczeniowych jest również planu rozliczeniowego.

Firma Microsoft zaleca, aby sondować stan zadania, aby ustalić, po zakończeniu zadania. W przypadku wszystkich zadań została zakończona uruchomiona, należy wywołać operacji zmiany rozmiaru puli, aby ustawić liczbę maszyn wirtualnych w puli do zera. Jeśli krótkich puli zasobów i należy utworzyć nową pulę, na przykład aby bill przeciwko innego planu rozliczeniowego, można usunąć puli zamiast tego po wszystkich zmian uruchamiania wszystkich zadań.


| **Użyj przetwarzania puli wsadowej, gdy**    | **Użyj klasycznego przetwarzania wsadowego, gdy**  |
|---|---|
|Musisz uruchomić dużej liczby zadań<br>Lub<br/>Musisz wiedzieć, że Twoje zadania będą uruchamiane natychmiast<br/>Lub<br/>Należy gwarantowaną przepływność. Na przykład należy uruchomić szereg zadań w określonym przedziale czasu, a chcesz skalować zasoby obliczeniowe, stosownie do potrzeb.    | Używasz kilku zadań<br/>And<br/> Nie ma potrzeby zadań, aby natychmiast uruchomić |
