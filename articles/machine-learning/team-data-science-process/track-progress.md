---
title: Śledzenie postępów projektów procesów nauki o danych zespołowych
description: Jak menedżerowie grup do nauki o danych, potencjalni klienci zespołu i potencjalni klienci mogą śledzić postęp projektu do nauki o danych.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864200"
---
# <a name="track-the-progress-of-data-science-projects"></a>Śledzenie postępów w projektach do nauki o danych

Menedżerowie grup do nauki o danych, potencjalni klienci zespołu i potencjalni klienci mogą śledzić postępy swoich projektów.  Menedżerowie chcą wiedzieć, jaką pracę wykonano, kto wykonał pracę i jaka praca pozostaje.   Zarządzanie oczekiwaniami jest ważnym elementem sukcesu.

## <a name="azure-devops-dashboards"></a>Pulpity nawigacyjne usługi Azure DevOps

Jeśli używasz usługi Azure DevOps, można tworzyć pulpity nawigacyjne do śledzenia działań i elementów roboczych skojarzonych z danym projektem Agile. Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych, zobacz [Pulpity nawigacyjne, raporty i widżety](/azure/devops/report/dashboards/).

Aby uzyskać instrukcje dotyczące tworzenia i dostosowywania pulpitów nawigacyjnych i widżetów w usłudze Azure DevOps, zobacz następujące przewodniki Szybki start:

- [Dodawanie pulpitów nawigacyjnych i zarządzanie nimi](/azure/devops/report/dashboards/dashboards)
- [Dodawanie widżetów do pulpitu nawigacyjnego](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Przykładowy pulpit nawigacyjny

Oto prosty przykładowy pulpit nawigacyjny, który śledzi działania sprintu projektu nauki o danych Agile, w tym liczbę zatwierdzeń do skojarzonych repozytoriów. 

- Kafelek **odliczanie** pokazuje liczbę dni, które pozostały w bieżącym sprincie. 

- Dwa **kafelki kodu** pokazują liczbę zatwierdzeń w dwóch repozytoriach projektu w ciągu ostatnich siedmiu dni. 

- **Elementy robocze dla projektu klienta TDSP** pokazuje wyniki kwerendy dla wszystkich elementów roboczych i ich stanu. 

- **Skumulowany diagram przepływu** (CFD) pokazuje liczbę zamkniętych i aktywnych elementów roboczych.

- **Wykres postępu spalania** pokazuje pracę jeszcze do wykonania przed pozostały czas w sprincie.

- **Wykres nagrywania** pokazuje ukończoną pracę w porównaniu do całkowitej ilości pracy w sprincie.

![Pulpit nawigacyjny](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Następne kroki

[Wskazówki wykonujące proces nauki o danych zespołu](walkthroughs.md) zawiera listę instruktajników, które pokazują wszystkie kroki procesu. Połączone scenariusze ilustrują sposób zarządzania chmurą i zasobami lokalnymi w inteligentnych aplikacjach. 
