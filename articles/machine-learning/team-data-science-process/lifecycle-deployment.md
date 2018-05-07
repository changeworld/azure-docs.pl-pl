---
title: Wdrożenia etapie cyklu życia procesu nauki danych zespołu - Azure | Dokumentacja firmy Microsoft
description: Cele, zadań i elementów dostarczanych w etapie wdrażania projektów analizy danych
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: a56aa9c317703186e1ba2ab6bed9aa1875d318c2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="deployment"></a>Wdrożenie

W tym artykule omówiono cele, zadań i elementów dostarczanych skojarzone z wdrożeniem zespołu danych nauki procesu (TDSP). Ten proces obejmuje zalecane cykl służy do struktury projektów analizy danych. Cykl życia przedstawiono główne etapy, które projekty zazwyczaj wykonywane, często wielokrotnie powtarzane:

   1. **Opis biznesowa**
   2. **Uzyskiwanie danych i zrozumienie**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacji klienta**

W tym miejscu jest wizualną reprezentacją życia TDSP: 

![Cykl życia TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Cel
Wdrażanie modeli z potokiem danych produkcyjnych lub środowiska przypominającej środowisko produkcyjne dla użytkowników końcowych. 

## <a name="how-to-do-it"></a>Jak to zrobić
Zadania główne zostały omówione w tym etapie:

**Operacjonalizuj modelu**: wdrażania modelu i potoku na środowisko produkcyjne lub środowiska przypominającej środowisko produkcyjne korzystania z aplikacji.

### <a name="operationalize-a-model"></a>Operacjonalizowanie modelu
Po utworzeniu zestaw modeli, które również wykonywać, aby operacjonalizować je dla innych aplikacji do pracy. W zależności od wymagań biznesowych prognoz są wykonywane w czasie rzeczywistym lub na podstawie partii. Aby wdrożyć modeli, naraża je z interfejsem API otwarty. Interfejs umożliwia modelu można łatwo korzystać z różnych aplikacji, takich jak:

   * Witryny sieci Web w trybie online
   * Arkusze kalkulacyjne 
   * Pulpity nawigacyjne
   * -Aplikacji biznesowych 
   * Aplikacji zaplecza 

Przykłady operationalization modelu z usługą sieci web uczenie maszynowe Azure można znaleźć [wdrażanie usługi sieci web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md). Jest najlepszym rozwiązaniem jest tworzenie telemetrii i monitorowania w modelu produkcji i potoku danych, które można wdrożyć. Takie rozwiązanie pomaga w kolejnych systemu stanu raportowania i rozwiązywania problemów.  

## <a name="artifacts"></a>Artefakty

* Pulpit nawigacyjny stan, który przedstawia metryki kondycji i klucz systemu
* Raport końcowy modelowania o szczegóły wdrożenia
* Architektura dokument ostateczne rozwiązanie


## <a name="next-steps"></a>Kolejne kroki

Oto łącza do każdego kroku w cyklu TDSP:

   1. [Opis biznesowa](lifecycle-business-understanding.md)
   2. [Uzyskiwanie danych i zrozumienie](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacji klienta](lifecycle-acceptance.md)

Firma Microsoft udostępnia pełnej end-to-end wskazówki, które pokazują wszystkie kroki w procesie w określonych scenariuszach. [Wskazówki przykład](walkthroughs.md) artykuł zawiera listę scenariuszy łącza i opisy miniatur. Wskazówki dotyczące ilustrują sposób łączenia chmury, narzędzia lokalnych i usług w przepływie pracy lub potoku, aby utworzyć aplikację inteligentnego. 

Przykłady tego, jak wykonać kroki opisane w TDSPs, które używają usługi Azure Machine Learning Studio, zobacz [TDSP za pomocą usługi Azure Machine Learning](http://aka.ms/datascienceprocess).
