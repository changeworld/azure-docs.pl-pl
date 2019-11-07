---
title: Etap wdrażania cyklu życia zespołowego procesu nauki danych
description: Cele, zadania i elementy dostarczane dla etapu wdrożenia projektów analizy danych
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
ms.openlocfilehash: 156f2e95913dffd88de0b4669b13a5af5fc9605c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684683"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Etap wdrażania cyklu życia zespołowego procesu nauki danych

W tym artykule opisano cele, zadania i elementy dostarczane powiązane z wdrożeniem procesu nauki o danych zespołowych (przetwarzania TDSP). Ten proces zapewnia Zalecany cykl życia, którego można użyć do struktury projektów analizy danych. Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

   1. **Zrozumienie biznesowe**
   2. **Pozyskiwanie danych i opis**
   3. **Modelu**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
Wdrażaj modele przy użyciu potoku danych w środowisku produkcyjnym lub w podobnym do produkcyjnym. 

## <a name="how-to-do-it"></a>Jak to zrobić
Główne zadanie rozkierowane na ten etap:

**Operacjonalizować model**: Wdróż model i potok w środowisku produkcyjnym lub w podobnym do produkcyjnym dla użycia aplikacji.

### <a name="operationalize-a-model"></a>Operacjonalizowanie modelu
Po utworzeniu zestawu modeli, które działają prawidłowo, można operacjonalizować je do użycia przez inne aplikacje. W zależności od wymagań firmy przewidywania są wykonywane w czasie rzeczywistym lub na podstawie partii. Aby wdrożyć modele, należy uwidocznić je za pomocą interfejsu Open API. Interfejs umożliwia łatwe wykorzystanie modelu z różnych aplikacji, takich jak:

   * Witryny internetowe w trybie online
   * Arkusze kalkulacyjne 
   * Pulpity nawigacyjne
   * Aplikacje biznesowe 
   * Aplikacje zaplecza 

Przykłady modelu operacjonalizacji z usługą sieci Web Azure Machine Learning można znaleźć w temacie [Deploy a Azure Machine Learning Web Service](../studio/deploy-a-machine-learning-web-service.md). Najlepszym rozwiązaniem jest tworzenie danych telemetrycznych i monitorowanie ich w modelu produkcyjnym i w wdrażanym potoku danych. To rozwiązanie pomaga w kolejnym raportowaniu stanu systemu i rozwiązywaniu problemów.  

## <a name="artifacts"></a>Artefakty

* Pulpit nawigacyjny Stan, który wyświetla kondycję systemu i kluczowe metryki
* Ostateczny raport modelowania ze szczegółami wdrożenia
* Końcowy dokument architektury rozwiązania


## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono linki do poszczególnych etapów cyklu życia przetwarzania TDSP:

   1. [Zrozumienie biznesowe](lifecycle-business-understanding.md)
   2. [Pozyskiwanie i zrozumienie danych](lifecycle-data.md)
   3. [Modelu](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy kompleksowe przewodniki przedstawiające wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
