---
title: Rozwiązanie do zarządzania w Azure najlepsze rozwiązania w zakresie | Dokumentacja firmy Microsoft
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 0bd5e19e00dbae1d0ece27d0498a1f599dba05b7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887839"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Najlepsze rozwiązania dotyczące tworzenia rozwiązań do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> To jest wstępna dokumentacji do tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej może ulec zmianie.  

W tym artykule przedstawiono najlepsze rozwiązania dotyczące [Tworzenie pliku rozwiązania zarządzania](monitoring-solutions-solution-file.md) na platformie Azure.  Te informacje zostaną zaktualizowane określonych dodatkowych najlepszych rozwiązań.

## <a name="data-sources"></a>Źródła danych
- Źródła danych może być [skonfigurowany przy użyciu szablonu usługi Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), ale nie powinny znajdować się w pliku rozwiązania.  Przyczyną jest to, że Konfigurowanie źródeł danych nie jest obecnie idempotentności, co oznacza, że rozwiązania można zastąpić istniejącą konfigurację w obszarze roboczym użytkownika.<br><br>Na przykład rozwiązania może wymagać ostrzeżeń i błędów zdarzenia w dzienniku zdarzeń aplikacji.  Jeśli określisz to jako źródło danych w rozwiązaniu, istnieje ryzyko, usuwanie informacje o zdarzeniach, jeśli użytkownik ma to skonfigurowanych w ich obszaru roboczego.  Jeśli dołączysz wszystkie zdarzenia, następnie użytkownik może zbierać nadmiernego informacje o zdarzeniach w obszarze roboczym użytkownika.

- Jeśli rozwiązanie wymaga danych z jednego ze źródeł danych standardowych, następnie należy zdefiniować to jako warunek wstępny.  Stan w dokumentacji klienta należy skonfigurować źródło danych na ich własnych.  
- Dodaj [Weryfikacja przepływu danych](../log-analytics/log-analytics-view-designer-tiles.md) komunikat do widoków w rozwiązaniu aby poinstruować użytkowników do źródeł danych, które należy skonfigurować dla wymaganych danych mają być zbierane.  Ten komunikat jest wyświetlany na kafelku widoku, gdy nie można odnaleźć wymaganych danych.


## <a name="runbooks"></a>Elementy Runbook
- Dodaj [Harmonogram automatyzacji](../automation/automation-schedules.md) dla każdego elementu runbook w rozwiązaniu musi być uruchamiane zgodnie z harmonogramem.
- Obejmują [modułu IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) w rozwiązaniu do użycia przez zapisywanie danych w repozytorium analizy dzienników elementów runbook.  Konfigurowanie rozwiązania pod kątem [odwołania](monitoring-solutions-solution-file.md#solution-resource) tego zasobu, tak że pozostaje po rozwiązaniu.  Dzięki temu wielu rozwiązań w celu udostępniania modułu.
- Użyj [zmienne automatyzacji](../automation/automation-schedules.md) podać wartości do rozwiązania, które użytkownicy można zmienić później.  Nawet jeśli rozwiązanie jest skonfigurowany do przechowywania zmiennej, nadal można zmienić jej wartości.

## <a name="views"></a>Widoki
- Wszystkie rozwiązania powinny obejmować jeden widok, która jest wyświetlana w portalu użytkownika.  Widok może zawierać wielu [części wizualizacji](../log-analytics/log-analytics-view-designer-parts.md) w celu zilustrowania różne zestawy danych.
- Dodaj [Weryfikacja przepływu danych](../log-analytics/log-analytics-view-designer-tiles.md) komunikat do widoków w rozwiązaniu aby poinstruować użytkowników do źródeł danych, które należy skonfigurować dla wymaganych danych mają być zbierane.
- Konfigurowanie rozwiązania pod kątem [zawiera](monitoring-solutions-solution-file.md#solution-resource) widok, tak że jego usunięte, jeśli rozwiązanie zostanie usunięta.

## <a name="alerts"></a>Alerty
- Zdefiniuj listy adresatów jako parametru w pliku rozwiązania, użytkownik może zdefiniować je przy instalacji rozwiązania.
- Konfigurowanie rozwiązania pod kątem [odwołania](monitoring-solutions-solution-file.md#solution-resource) reguły alertu, że użytkownik może zmienić ich konfiguracji.  Warto wprowadzić zmiany, takie jak modyfikowanie listy adresatów, zmianę wartości progowej alertu lub wyłączania reguły alertów. 


## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z artykułem podstawowego procesu [projektowania i konfigurowania rozwiązania do zarządzania](monitoring-solutions-creating.md).
* Dowiedz się, jak [Utwórz plik rozwiązania](monitoring-solutions-solution-file.md).
* [Dodaj zapisanych wyszukiwań i alerty](monitoring-solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodawanie widoków](monitoring-solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj element runbook usługi Automatyzacja i innych zasobów](monitoring-solutions-resources-automation.md) do rozwiązania do zarządzania.

