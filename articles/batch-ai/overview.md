---
title: Usługa Azure Batch AI — szkolenie modeli sztucznej inteligencji | Microsoft Docs
description: Dowiedz się, jak używać zarządzanej usługi Azure Batch AI do szkolenia modeli sztucznej inteligencji (AI) i innych modeli uczenia maszynowego w klastrach procesorów GPU i procesorów CPU.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052703"
---
# <a name="what-is-azure-batch-ai"></a>Co to jest Azure Batch AI?

Azure Batch AI to zarządzana usługa, która ułatwia analitykom danych i badaczom SI uczenie i testowanie modeli SI oraz uczenia maszynowego na dużą skalę na platformie Azure bez konieczności zarządzania złożoną infrastrukturą. Użytkownik opisuje zasoby obliczeniowe, zadania, które chce uruchomić, miejsca, gdzie będą przechowywane dane wejściowe i dane wyjściowe modelu, a usługa Batch AI zajmuje się resztą.

Można użyć usługi Batch AI jako usługi autonomicznej albo przeprowadzić szkolenie modeli w ramach większego przepływu pracy tworzenia oprogramowania:

* Używaj samej usługi Batch AI do szkolenia, testowania i oceniania modeli uczenia maszynowego i sztucznej inteligencji w klastrach procesorów [GPU](../virtual-machines/linux/sizes-gpu.md) lub CPU. 

* Na potrzeby klastra usługi Batch AI w przepływie pracy zastosuj usługę [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) lub inne [narzędzia platformy Azure AI](https://azure.microsoft.com/overview/ai-platform/). Usługa Azure ML oferuje zaawansowane środowisko na potrzeby przygotowywania danych, eksperymentowania oraz historii zadań. Usługa Azure ML umożliwia również utworzenie pakietu przeszkolonego modelu w kontenerze oraz wdrożenie modelu na potrzeby wnioskowania lub na urządzeniach IoT.  

## <a name="train-machine-learning-and-ai-models"></a>Szkolenie modeli sztucznej inteligencji i uczenia maszynowego

Za pomocą usługi Batch AI można szkolić modele uczenia maszynowego i głębokie sieci neuronowe (uczenie głębokie) oraz inne rozwiązania sztucznej inteligencji. Usługa Batch AI oferuje wbudowaną obsługę popularnych bibliotek i struktur sztucznej inteligencji typu open source, w tym [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) i [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK).

Po zidentyfikowaniu problematycznego obszaru i przygotowaniu danych współpracuj interaktywnie z usługą Batch AI, aby przetestować koncepcje modelu. Następnie, gdy wszystko będzie gotowe do eksperymentowania na dużą skalę, uruchom zadania na wielu procesorach GPU za pomocą MPI lub innych bibliotek komunikacyjnych i równolegle przeprowadź więcej eksperymentów.

Usługa Batch AI ułatwia szkolenie modeli na dużą skalę na kilka sposobów. Na przykład: 

|  |  |
|---------|---------|
| **Dystrybucja szkolenia**<br/>![Szkolenie rozproszone](./media/overview/distributed-training.png)  | Skaluj w górę szkolenie na potrzeby jednego zadania na wielu połączonych z siecią procesorach GPU w celu szkolenia większych sieci z dużą ilością danych.|
| **Eksperymentowanie**<br/>![Eksperymentowanie](./media/overview/experimentation.png) | Skaluj w poziomie szkolenie przy użyciu wielu zadań. Uruchom czyszczenie parametrów, aby przetestować nowe koncepcje, lub dostrój hiperparametry, aby zoptymalizować dokładność i wydajność. |
| **Wykonywanie równoległe**![Wykonywanie równoległe](./media/overview/parallel-execution.png) | Szkól lub oceniaj wiele modeli jednocześnie, uruchamiając równolegle na wielu serwerach, aby szybciej wykonywać zadania.|

Po przeszkoleniu modelu użyj usługi Batch AI do testowania modelu, jeśli skrypt szkolenia tej czynności nie obejmował, lub przeprowadź ocenianie w usłudze Batch.

## <a name="how-it-works"></a>Jak to działa

Używaj zestawów SDK usługi Batch AI, skryptów wiersza polecenia lub witryny Azure Portal, aby zarządzać zasobami obliczeniowymi i planować zadania na potrzeby szkolenia i testowania sztucznej inteligencji: 

* **Ustanawiaj i skaluj klastry maszyn wirtualnych** — wybierz liczbę węzłów (maszyn wirtualnych) oraz rozmiar maszyn wirtualnych z procesorem GPU lub innego typu, który odpowiada Twoim potrzebom w zakresie szkolenia. Skaluj liczbę węzłów w górę lub w dół automatycznie lub ręcznie, aby używać zasobów tylko wtedy, gdy jest to konieczne. 

* **Zarządzaj zależnościami i kontenerami** — domyślnie klastry usługi Batch AI obsługują obrazy maszyn wirtualnych z systemem Linux, które mają wstępnie zainstalowane zależności na potrzeby uruchamiania struktur szkoleniowych opartych na kontenerach na procesorach GPU lub CPU. W przypadku konieczności dodatkowej konfiguracji zastosuj obrazy niestandardowe lub uruchom skrypty uruchamiania.

* **Rozpowszechniaj dane** — wybierz co najmniej jedną opcję magazynu, aby zarządzać danymi wejściowymi, skryptami i danymi wyjściowymi zadań: Azure Files, Azure Blob Storage lub zarządzany serwer NFS. Usługa Batch AI obsługuje również niestandardowe rozwiązania magazynu, m.in. równoległe systemy plików o wysokiej wydajności. Zainstaluj systemy plików magazynu na węzłach kastra i w kontenerach zadań przy użyciu prostych plików konfiguracji.

* **Planuj zadania** — przesyłaj zadania do kolejki zadań opartej na priorytetach, umożliwiającej współużytkowanie zasobów klastra oraz wykorzystywanie maszyn wirtualnych o niskim priorytecie i wystąpień zarezerwowanych.

* **Obsługuj błędy** — monitoruj stan zadania i uruchamiaj ponownie zadania w przypadku awarii maszyn wirtualnych w trakcie wykonywania potencjalnie długotrwałych zadań.

* **Zbieraj wyniki** — łatwo uzyskuj dostęp do dzienników danych wyjściowych, Stdout, Stderr oraz przeszkolonych modeli. Konfiguruj zadania usługi Batch AI, aby wypychać dane wyjściowe bezpośrednio do zainstalowanego magazynu.

Będąc usługą platformy Azure, usługa Batch AI obsługuje na potrzeby bezpieczeństwa popularne narzędzia, takie jak kontrola dostępu na podstawie ról (RBAC) oraz sieci wirtualne platformy Azure.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [zasobów usługi Batch AI](resource-concepts.md) na potrzeby szkolenia modelu uczenia maszynowego lub sztucznej inteligencji.

* Rozpocznij [szkolenie przykładowego modelu uczenia głębokiego](quickstart-tensorflow-training-cli.md) za pomocą usługi Batch AI.

* Sprawdź przykładowe [przepisy na szkolenie](https://github.com/Azure/BatchAI/blob/master/recipes) dla popularnych struktur sztucznej inteligencji.
