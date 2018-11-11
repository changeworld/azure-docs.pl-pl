---
title: Przewidywanie kondycję pojazdów i nawyki nawyków — Azure | Dokumentacja firmy Microsoft
description: Korzystając z możliwości pakietu Cortana Intelligence możesz uzyskać w czasie rzeczywistym i predykcyjny wgląd w kondycję pojazdów i nawyki nawyków.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 0594b9b40fdf8647f99946a31dbb47a95c9694ac
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300357"
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Podręcznik dotyczący rozwiązań analizy Telemetrii pojazdów

Komputery Super superkomputery laboratorium i są teraz zaparkowane w warsztatów samochodowych. Są one teraz są umieszczane w samochody, które zawierają wiele czujników. Czujniki te umożliwiają je śledzić i monitorować miliony zdarzeń co sekundę. Najpóźniej w roku 2020 większość tych pojazdów będzie połączona z Internetem. Sięgnięcie do tego bogactwa danych zapewnia większe bezpieczeństwo, niezawodność, a więc zapewniają lepsze środowisko. Firma Microsoft udziela to dream mogą stać się rzeczywistością dzięki pakietowi Cortana Intelligence.

Pakiet Cortana Intelligence to w pełni zarządzane dane big Data i zaawansowanych analiz, można użyć w celu przekształcania danych w inteligentną akcję. Szablon rozwiązania analizy Telemetrii pojazdów Cortana analizy pokazano, jak dealerzy i producenci samochodów oraz firmy ubezpieczeniowe mogą uzyskać w czasie rzeczywistym i predykcyjny wgląd w kondycję pojazdów i nawyki kierowców.

Rozwiązanie jest implementowane jako [wzorzec architektury lambda](https://en.wikipedia.org/wiki/Lambda_architecture), które wyświetla pełną potencjalnych platformy Cortana Intelligence w czasie rzeczywistym i przetwarzania wsadowego.

## <a name="architecture"></a>Architektura
Architektura rozwiązania analizy Telemetrii pojazdów zostało zilustrowane na poniższym diagramie:

![Diagram architektury rozwiązania](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


To rozwiązanie obejmuje następujące składniki pakietu Cortana Intelligence i przedstawiono ich integracji:

* **Usługa Azure Event Hubs** pozyskuje miliony zdarzeń telemetrii pojazdów na platformie Azure.
* **Usługa Azure Stream Analytics** udostępnia w czasie rzeczywistym szczegółowe informacje o kondycji pojazdu i utrwalenia tych danych w długoterminowym magazynie do pełniejszej analizy wsadowej.
* **Usługa Azure Machine Learning** wykrywa anomalie w czasie rzeczywistym i używa przetwarzania wsadowego, aby zapewnić predykcyjnego wglądu w dane.
* **Usługa Azure HDInsight** przekształca dane w dużej skali.
* **Usługa Azure Data Factory** obsługuje organizowanie, planowanie, zarządzanie zasobami i monitorowanie potoku przetwarzania wsadowego.
* **Usługa Power BI** wyposaża to rozwiązanie w rozbudowany pulpit nawigacyjny dla danych w czasie rzeczywistym i wizualizacje analizy predykcyjnej.

To rozwiązanie uzyskuje dostęp do dwóch różnych źródeł danych: 

* **Symulowane sygnały pojazdu i Diagnostyka**: symulator telematyce pojazdu emituje informacje diagnostyczne i sygnałów, które odpowiadają stan pojazdu i opracowuje wzorca w danym punkcie w czasie. 
* **Katalog pojazdów**: tego zestawu danych referencyjnych mapuje VIN numery modeli.

## <a name="next-steps"></a>Następne kroki

Aby poznać dalsze tego rozwiązania, zobacz [podręcznik dotyczący rozwiązań analizy Telemetrii pojazdów: szczegółami rozwiązania](cortana-analytics-playbook-vehicle-telemetry-deep-dive.md).

Aby dowiedzieć się, jak skonfigurować raporty usługi Power BI i pulpity nawigacyjne dla tego rozwiązania, zobacz [instrukcje dotyczące konfiguracji pulpitu nawigacyjnego można przeprowadzić przy użyciu danych Telemetrycznych analizy rozwiązania szablonu usługi Power BI](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
