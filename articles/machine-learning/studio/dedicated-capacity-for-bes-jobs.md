---
title: Pojemność Machine Learning partii wykonywania usługi zadań w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Omówienie usług partii zadań Azure Machine Learning zadań.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.service: machine-learning
ms.component: studio
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: 1e4cf34582e28e00108e280d928eea8a8134699a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834529"
---
# <a name="azure-batch-service-for-machine-learning-jobs"></a>Azure usługa partia zadań dla zadania uczenia maszynowego

Machine Learning puli partii przetwarzania zapewnia skalowalnego zarządzany przez klienta dla usługi Azure Machine Learning partii wykonywania. Przetwarzanie wsadowe klasycznego uczenia maszynowego odbywa się w środowisku wielodostępnym ogranicza liczbę współbieżnych zadań możesz mogą przesyłać i na podstawie pierwszego w pierwszym poza są kolejkowane zadania. Ta niedokładność oznacza, że nie można dokładnie przewidzieć uruchomienia zadania.

Przetwarzanie wsadowe puli służy do tworzenia pul, na których można przesłać zadania wsadowe. Możesz kontrolować rozmiar puli, a do puli, które przesyłania zadania. Uruchamia zadanie BES w jego własnej przestrzeni przetwarzania przetwarzania przewidywalną wydajność i może tworzyć pule zasobów, które odpowiadają obciążenia przetwarzania, które można przesłać.

## <a name="how-to-use-batch-pool-processing"></a>Jak używać przetwarzania wsadowego puli

Przetwarzanie wsadowe puli konfiguracji nie jest obecnie dostępna za pośrednictwem portalu Azure. Aby użyć przetwarzania wsadowego puli, musisz:

-   Wywołaj CSS w celu utworzenia konta puli partii i uzyskiwanie adresu URL usługi puli i klucza autoryzacji
-   Tworzenie nowego Menedżera zasobów na podstawie usługi sieci web i plan rozliczeniowy

Aby utworzyć konto, wywołaj obsługi klienta firmy Microsoft i pomocy technicznej (CSS) i podaj identyfikator subskrypcji. CSS będzie działać z Tobą w celu ustalenia odpowiednich pojemności dla danego scenariusza. CSS skonfiguruje konto z maksymalną liczbę zestawów, które można utworzyć i maksymalną liczbę maszyn wirtualnych (VM), które można umieścić w każdej puli. Po skonfigurowaniu konta podano adres URL usługi puli i klucza autoryzacji.

Po utworzeniu konta służy do wykonywania operacji zarządzania w puli na pulę partii klucz puli adres URL usługi i autoryzacji.

![Architektura usługi puli partii.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Pule można utworzyć przez wywołanie operacji tworzenia puli na adres URL usługi puli otrzymany od CSS. Podczas tworzenia puli określić usługi sieci web Machine Learning na podstawie liczby maszyn wirtualnych i adres URL swagger.json nowego Menedżera zasobów. Ta usługa sieci web podano związek rozliczeń. Usługa puli partii używa swagger.json pulę można skojarzyć z plan rozliczeniowy. Można uruchamiać żadnych BES usługi sieci web, zarówno nowego Menedżera zasobów na podstawie i klasyczne, wybierz w puli.

Można używać nowego Menedżera zasobów na podstawie usługi sieci web, ale należy pamiętać, że rozliczeń dla zadań są powiązane z plan rozliczeniowy skojarzone z tą usługą. Można utworzyć usługi sieci web i nowy plan rozliczeniowy specjalnie z myślą o uruchomionych puli partii zadań.

Aby uzyskać więcej informacji na temat tworzenia usług sieci web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](publish-a-machine-learning-web-service.md).

Po utworzeniu puli można przesłać zadania usługi BES, usługi sieci web przy użyciu adresu URL żądania wsadowego. Można przesłać je do puli lub przetwarzania wsadowego klasycznego. Aby przesłać zadanie do puli partii przetwarzania, należy dodać następujący parametr treści żądania przesłania zadania:

"AzureBatchPoolId":"&lt;pool ID&gt;"

Jeśli parametr nie zostanie dodany, zadanie jest uruchamiane w środowisku procesu wsadowego klasycznego. Jeśli pula zawiera dostępnych zasobów, zadanie uruchamiania natychmiast. Pula nie ma wolnego zasobów, zadanie jest w kolejce do momentu zasób jest dostępny.

Jeśli okaże się regularnie osiągnąć pojemności z pulami, czy należy zwiększenia wydajności, można wywołać CSS i pracować z przedstawicielem w celu zwiększenia przydziałami.

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

## <a name="considerations-when-using-batch-pool-processing"></a>Uwagi dotyczące korzystania z przetwarzania wsadowego puli

Przetwarzanie wsadowe puli jest zawsze rozliczeniowy usługi i który należy skojarzyć go z Menedżera zasobów na podstawie plan rozliczeniowy. Rozliczenie jest przeprowadzane tylko dla liczby godzin obliczeń puli jest uruchomiony; niezależnie od liczby zadania są uruchamiane w tej puli czasu. Po utworzeniu puli rozliczenie jest godziny obliczeniowe poszczególnych maszyn wirtualnych w puli do czasu usunięcia puli nawet wtedy, gdy są uruchomione żadne zadania wsadowego w puli. Rozliczeń dla maszyn wirtualnych rozpoczyna się po zakończeniu ich inicjowania obsługi administracyjnej i zatrzymywana, gdy zostały usunięte. Można użyć dowolnego z planami na [Machine Learning — cennik strony](https://azure.microsoft.com/pricing/details/machine-learning/).

Przykład rozliczeń:

Jeśli z maszyn wirtualnych 2. Utwórz pulę partii i usuń go po 24 godzinach planu rozliczeniowego obciąża 48 godzin obliczeń; niezależnie od tego, ile zadania zostały uruchomione w tym okresie.

Tworzenie puli partii z 4 maszynami wirtualnymi, usuń go po 12 godzinach planu rozliczeniowego jest również zaksięgowana 48 godzin obliczeń.

Zaleca się, że sondowania stan zadania, aby określić, po zakończeniu zadania. Po zakończeniu operacji uruchamiania wszystkich zadań mają wywołania operacji zmiany rozmiaru puli, można ustawić liczbę maszyn wirtualnych w puli na zero. Jeśli krótkich puli zasobów i należy utworzyć nową pulę, na przykład rozliczyć względem innego planu rozliczeniowego, można usunąć puli zamiast tego po wszystkich zadań zakończeniu pracy.


| **Korzystanie z puli partii podczas przetwarzania**    | **Użyj klasycznego partii podczas przetwarzania**  |
|---|---|
|Musisz uruchomić dużej liczby zadań<br>Lub<br/>Musisz wiedzieć, że Twoje zadania będą uruchamiane natychmiast<br/>Lub<br/>Należy gwarantowanej przepustowości. Na przykład trzeba uruchomić liczba zadań w określonym przedziale czasu i chcesz skalować w poziomie zasoby obliczeniowe do własnych potrzeb.    | Użytkownicy korzystający z kilku zadań<br/>Oraz<br/> Nie ma potrzeby zadania, aby natychmiast uruchomić |
