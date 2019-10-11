---
title: Śledzenie postępu projektów procesów naukowych dotyczących danych zespołu
description: Jak Menedżerowie grupy analizy danych, liderzy zespołu i potencjalni klienci projektu mogą śledzić postęp projektu analizy danych.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244060"
---
# <a name="track-the-progress-of-data-science-projects"></a>Śledzenie postępu projektów analizy danych

Menedżerowie grupy nauki o danych, liderzy zespołu i potencjalni klienci projektu mogą śledzić postęp swoich projektów, takich jak wykonane prace, osoby, które pozostały do pracy. 

## <a name="azure-devops-dashboards"></a>Pulpity nawigacyjne usługi Azure DevOps

Jeśli używasz usługi Azure DevOps, możesz tworzyć pulpity nawigacyjne w celu śledzenia działań i elementów roboczych skojarzonych z danym projektem Agile. Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych, zobacz [pulpity nawigacyjne, raporty i widżety](/azure/devops/report/dashboards/).

Aby uzyskać instrukcje dotyczące sposobu tworzenia i dostosowywania pulpitów nawigacyjnych i widżetów w usłudze Azure DevOps, zobacz następujące przewodniki szybki start:

- [Dodawanie pulpitów nawigacyjnych i zarządzanie nimi](/azure/devops/report/dashboards/dashboards)
- [Dodawanie widżetów do pulpitu nawigacyjnego](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Przykładowy pulpit nawigacyjny

Oto prosty przykładowy pulpit nawigacyjny, który śledzi działania przebiegu projektu analizy danych Agile, łącznie z liczbą zatwierdzeń skojarzonych z repozytoriami. 

- Kafelek **odliczanie** pokazuje liczbę dni, które pozostaną w bieżącym przebiegu. 

- Dwa **kafelki kodu** pokazują liczbę zatwierdzeń w dwóch repozytoriach projektu dla ostatnich siedmiu dni. 

- **Elementy robocze dla projektu klienta przetwarzania TDSP** przedstawia wyniki zapytania dla wszystkich elementów roboczych i ich stanu. 

- **Skumulowany Diagram przepływu** (CFD) pokazuje liczbę zamkniętych i aktywnych elementów roboczych.

- **Wykres postępu** pokazuje pracę w dalszym ciągu z pozostałą godziną w przebiegu.

- **Wykres burnup** pokazuje ukończoną pracę w porównaniu do łącznej ilości pracy w przebiegu.

![Pulpit nawigacyjny](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Następne kroki

[Instruktaże wykonujące analizy danych zespołowych](walkthroughs.md) — instruktaże pokazujące wszystkie kroki procesu dla konkretnych scenariuszy, z linkami i opisami miniatur. Połączone scenariusze ilustrują sposób łączenia narzędzi i usług w chmurze i lokalnych z przepływami pracy lub potokami w celu tworzenia inteligentnych aplikacji. 
