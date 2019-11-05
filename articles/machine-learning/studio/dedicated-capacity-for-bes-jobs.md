---
title: Zadania usługi Azure Batch
titleSuffix: Azure Machine Learning Studio (classic)
description: Omówienie zadań usług Azure Batch Services dla Machine Learning Studio (klasycznych). Przetwarzanie puli wsadowej umożliwia tworzenie pul, na których można przesyłać zadania wsadowe.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 1de7ad10a54c34595f69a543843097cee1377067
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493032"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Usługa Azure Batch dla zadań Azure Machine Learning Studio (klasycznych)

Machine Learning przetwarzanie puli usługi Batch zapewnia skalę zarządzaną przez klienta dla usługi wykonywania wsadowego Azure Machine Learning. Klasyczne przetwarzanie wsadowe dla uczenia maszynowego odbywa się w środowisku z wieloma dzierżawcami, które ogranicza liczbę współbieżnych zadań, które można przesłać, a zadania są umieszczane w kolejce w pierwszej kolejności. Ta niepewność oznacza, że nie można dokładnie przewidzieć, kiedy zadanie zostanie uruchomione.

Przetwarzanie puli wsadowej umożliwia tworzenie pul, na których można przesyłać zadania wsadowe. Kontrolujesz rozmiar puli i do której puli zostanie przesłane zadanie. Zadanie BES jest uruchamiane we własnym miejscu przetwarzania zapewniającym przewidywalną wydajność przetwarzania i możliwość tworzenia pul zasobów, które odpowiadają przesyłanemu załadowanemu obciążeniu.

> [!NOTE]
> Aby można było utworzyć pulę, należy dysponować nową Menedżer zasobów opartą na Machine Learning usłudze sieci Web. Po utworzeniu można uruchomić dowolną usługę sieci Web BES, na przykład na podstawie Menedżer zasobów nowej i klasycznej, w puli.

## <a name="how-to-use-batch-pool-processing"></a>Jak korzystać z przetwarzania puli wsadowej

Konfiguracja przetwarzania puli wsadowej nie jest obecnie dostępna za pomocą Azure Portal. Aby można było korzystać z przetwarzania puli wsadowej, należy:

-   Wywołaj kod CSS, aby utworzyć konto puli usługi Batch i uzyskać adres URL usług puli i klucz autoryzacji
-   Tworzenie nowej usługi sieci Web opartej na Menedżer zasobów i planu rozliczeniowego

Aby utworzyć konto, skontaktuj się z pomocą techniczną firmy Microsoft (CSS) i podaj identyfikator subskrypcji. CSS będzie współpracować z ty, aby określić odpowiednią pojemność dla danego scenariusza. Następnie CSS skonfiguruje konto z maksymalną liczbą pul, które można utworzyć, oraz maksymalną liczbę maszyn wirtualnych, które można umieścić w każdej puli. Po skonfigurowaniu konta otrzymasz adres URL usługi puli i klucz autoryzacji.

Po utworzeniu konta należy użyć adresu URL usługi puli i klucza autoryzacji do wykonywania operacji zarządzania pulą w puli usługi Batch.

![Architektura usług puli usługi Batch.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Tworzysz pule, wywołując operację Utwórz pulę na adres URL usługi puli udostępniony przez kod CSS. Podczas tworzenia puli Określ liczbę maszyn wirtualnych i adres URL pliku Swagger. JSON nowej Menedżer zasobów opartej na Machine Learning usłudze sieci Web. Ta usługa sieci Web jest świadczona w celu ustanowienia skojarzenia rozliczeń. Usługa puli usługi Batch używa pliku Swagger. JSON do kojarzenia puli z planem rozliczeniowym. W puli można uruchomić dowolną usługę sieci Web BES, na przykład na podstawie Menedżer zasobów nowej i klasycznej.

Można użyć dowolnej nowej usługi sieci Web opartej na Menedżer zasobów, ale należy pamiętać, że opłaty za zadania są naliczane zgodnie z planem rozliczeniowym skojarzonym z tą usługą. Możesz chcieć utworzyć usługę sieci Web i nowy plan rozliczeń przeznaczony do uruchamiania zadań puli usługi Batch.

Aby uzyskać więcej informacji na temat tworzenia usług sieci Web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

Po utworzeniu puli należy przesłać zadanie BES przy użyciu adresu URL żądań wsadowych dla usługi sieci Web. Możesz przesłać go do puli lub do klasycznego przetwarzania wsadowego. Aby przesłać zadanie do przetwarzania puli wsadowej, należy dodać następujący parametr do treści żądania przesłania zadania:

"AzureBatchPoolId": "&lt;Identyfikator puli&gt;"

Jeśli parametr nie zostanie dodany, zadanie zostanie uruchomione w środowisku klasycznego procesu wsadowego. Jeśli pula ma dostępne zasoby, zadanie zaczyna działać od razu. Jeśli pula nie ma bezpłatnych zasobów, zadanie jest umieszczane w kolejce do momentu udostępnienia zasobu.

Jeśli okaże się, że często docierasz do pojemności pul i potrzebujesz większej wydajności, możesz wywołać CSS i skontaktować się z przedstawicielem, aby zwiększyć limity przydziału.

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

## <a name="considerations-when-using-batch-pool-processing"></a>Zagadnienia dotyczące korzystania z przetwarzania puli usługi Batch

Przetwarzanie puli usługi Batch to zawsze włączona usługa, która wymaga skojarzenia z planem rozliczeniowym opartym na Menedżer zasobów. Opłaty są naliczane wyłącznie za liczbę godzin obliczeniowych, w których Pula jest uruchomiona; bez względu na liczbę zadań wykonywanych w tej puli czasowej. W przypadku tworzenia puli opłaty są naliczane za godziny obliczeniowe każdej maszyny wirtualnej w puli do momentu usunięcia puli, nawet jeśli w puli nie są uruchomione żadne zadania usługi Batch. Rozliczenia dla maszyn wirtualnych są uruchamiane po zakończeniu aprowizacji i zatrzymaniu, gdy zostały usunięte. Możesz użyć dowolnego planu znajdującego się na [stronie cennika Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Przykład rozliczania:

W przypadku utworzenia puli wsadowej z 2 maszynami wirtualnymi i usunięciu jej po 24 godzinach plan rozliczeniowy będzie obciążany 48 godzin obliczeniowych; bez względu na liczbę zadań uruchomionych w tym okresie.

W przypadku utworzenia puli wsadowej z 4 maszynami wirtualnymi i usunięciu jej po upływie 12 godzin plan rozliczeniowy będzie również obciążany przez 48 godzin obliczeniowych.

Zalecamy sondowanie stanu zadania w celu określenia czasu ukończenia zadań. Gdy wszystkie zadania zakończą działanie, wywołaj operację zmiany puli, aby ustawić liczbę maszyn wirtualnych w puli na zero. Jeśli masz krótkie zasoby puli i musisz utworzyć nową pulę, na przykład aby rozliczać za inny plan rozliczeniowy, możesz usunąć pulę zamiast tego, gdy wszystkie zadania zakończą działanie.


| **Użyj przetwarzania puli wsadowej, gdy**    | **Użyj klasycznego przetwarzania wsadowego, gdy**  |
|---|---|
|Należy uruchomić dużą liczbę zadań<br>Lub<br/>Musisz wiedzieć, że zadania będą uruchamiane natychmiast<br/>Lub<br/>Wymagana jest gwarantowana przepływność. Na przykład trzeba uruchomić wiele zadań w danym przedziale czasu i chcieć skalować zasoby obliczeniowe odpowiednio do potrzeb.    | Uruchamiasz zaledwie kilka zadań<br/>And<br/> Zadania nie są potrzebne do natychmiastowego uruchomienia |
