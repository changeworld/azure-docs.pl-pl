---
title: Etap wdrożenia cyklu życia zespołowego danych dla celów naukowych
description: Cele, zadania i elementy dostarczane na etapie wdrażania projektów do nauki o danych
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 00710183828892c81d3ea887e4394237288eb6bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303540"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Etap wdrożenia cyklu życia zespołowego danych dla celów naukowych

W tym artykule opisano cele, zadania i cele do zrealizowania skojarzonym z wdrożeniem procesu do nauki o danych zespołu (TDSP). Ten proces obejmuje zalecane cyklu życia, który umożliwia tworzenie struktury projektów do nauki o danych. Cykl życia przedstawia główne etapy, które projekty zazwyczaj są wykonywane, często iteracyjne:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i opis**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualnej reprezentacji cyklu przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
Wdrażaj modele za pomocą potoku danych do produkcji lub środowiska przypominającej środowisko produkcyjne akceptacji użytkownika końcowego. 

## <a name="how-to-do-it"></a>Jak to zrobić
Głównym zadaniem, które zostały rozwiązane na tym etapie:

**Operacjonalizowanie modelu**: Wdrażanie modelu i potoku do produkcji lub środowiska przypominającej środowisko produkcyjne dla użycia aplikacji.

### <a name="operationalize-a-model"></a>Operacjonalizowanie modelu
Po umieszczeniu zestaw modeli, które wykonują dobrze. można zoperacjonalizować je dla innych aplikacji korzystających ze. W zależności od wymagań biznesowych prognozy są wykonywane w czasie rzeczywistym lub na podstawie usługi batch. Aby wdrażać modele, należy udostępnić je za pomocą interfejsu open API. Interfejs umożliwia modelu można łatwo korzystać z różnych aplikacji, takich jak:

   * Witryny sieci Web w trybie online
   * Arkusze kalkulacyjne 
   * Pulpity nawigacyjne
   * Line-of-business aplikacji 
   * Zaplecza aplikacji 

Przykłady operacjonalizacji modelu przy użyciu usługi sieci web Azure Machine Learning, zobacz [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). Jest najlepszym rozwiązaniem jest tworzenie danych telemetrycznych i monitorowania w modelu produkcji i potoku danych, który można wdrożyć. Praktyka ta pomaga ze stanem kolejnych system raportowania i rozwiązywania problemów.  

## <a name="artifacts"></a>Artefakty

* Stan pulpit nawigacyjny metryk kondycji i klucz systemu
* Raport końcowy modelowania z szczegóły wdrożenia
* Ostateczne rozwiązanie architektury dokumentu


## <a name="next-steps"></a>Kolejne kroki

Poniżej podano linki do każdego kroku w cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i opis](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Firma Microsoft oferuje instruktaży pełnej end-to-end, które przedstawiają wszystkie kroki w procesie dla konkretnych scenariuszy. [Przykładowe przewodniki](walkthroughs.md) artykuł zawiera listę scenariuszy wraz z linkami i opisy miniatur. Przewodniki pokazują, jak połączyć chmury, lokalnego narzędzia i usługi w przepływie pracy lub potoku do tworzenia inteligentnych aplikacji. 

Przykłady sposób wykonywania kroków w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [przetwarzania TDSP za pomocą usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
