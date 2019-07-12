---
title: Rozwiązanie do zarządzania w najlepsze rozwiązania platformy Azure | Dokumentacja firmy Microsoft
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: a4f982f6265d1c8cab2ae666b9d6e2e33beb5064
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672925"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Najlepsze rozwiązania dotyczące tworzenia rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wersję wstępną dokumentacji dotyczące tworzenia rozwiązania do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.  

W tym artykule przedstawiono najlepsze rozwiązania dotyczące [Tworzenie pliku rozwiązania zarządzania](solutions-solution-file.md) na platformie Azure.  Te informacje zostaną zaktualizowane, określone dodatkowe najlepsze rozwiązania.

## <a name="data-sources"></a>Źródła danych
- Źródła danych może być [skonfigurowane przy użyciu szablonu usługi Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), ale nie powinny znajdować się w pliku rozwiązania.  Przyczyną jest to, że Konfigurowanie źródeł danych nie jest obecnie idempotentne, co oznacza, że Twoje rozwiązanie może spowodować zastąpienie istniejącej konfiguracji w obszarze roboczym użytkownika.<br><br>Na przykład rozwiązania mogą wymagać zdarzenia ostrzeżeń i błędów w dzienniku zdarzeń aplikacji.  Jeśli określisz to jako źródło danych w swoim rozwiązaniu, istnieje ryzyko, usuwając informacje o zdarzeniach, jeśli użytkownik był to skonfigurowane w ich obszarze roboczym.  W przypadku dołączenia wszystkich zdarzeń, następnie użytkownik może zbierać nadmierne informacje o zdarzeniach w obszarze roboczym użytkownika.

- Jeśli rozwiązanie wymaga danych z jednego ze źródeł danych w warstwie standardowa, następnie należy zdefiniować to jako warunek wstępny.  Stan w dokumentacji klienta należy skonfigurować źródło danych na ich własnych.  
- Dodaj [Weryfikacja przepływu danych](../../azure-monitor/platform/view-designer-tiles.md) komunikat do widoków w rozwiązaniu do Poinstruuj użytkownika do źródeł danych, które muszą być skonfigurowane wymagane dane mogą być zbierane.  Ten komunikat jest wyświetlany na kafelku widoku, gdy nie ma wymaganych danych.


## <a name="runbooks"></a>Elementy Runbook
- Dodaj [harmonogram Automation](../../automation/automation-schedules.md) dla każdego elementu runbook w rozwiązaniu, którego potrzebuje do uruchomienia zgodnie z harmonogramem.
- Obejmują [modułu IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) w Twoim rozwiązaniu, który będzie używany przez elementy runbook, zapisywanie danych w repozytorium usługi Log Analytics.  Konfigurowanie rozwiązania do [odwołania](solutions-solution-file.md#solution-resource) tego zasobu, tak że pozostaje, gdy rozwiązanie zostanie usunięty.  Dzięki temu wiele rozwiązań udostępnić modułu.
- Użyj [zmienne automatyzacji](../../automation/automation-schedules.md) podać wartości do rozwiązania, które użytkownicy mogą chcieć zmienić później.  Nawet jeśli rozwiązanie jest skonfigurowany do zawierać zmiennej, można je zmienić jej wartości.

## <a name="views"></a>Widoki
- Wszystkie rozwiązania powinien zawierać pojedynczy widok, który jest wyświetlana w portalu użytkownika.  Widok może zawierać wiele [części wizualizacji](../../azure-monitor/platform/view-designer-parts.md) celu przedstawienia różnych zestawów danych.
- Dodaj [Weryfikacja przepływu danych](../../azure-monitor/platform/view-designer-tiles.md) komunikat do widoków w rozwiązaniu do Poinstruuj użytkownika do źródeł danych, które muszą być skonfigurowane wymagane dane mogą być zbierane.
- Konfigurowanie rozwiązania do [zawierają](solutions-solution-file.md#solution-resource) widoku, dlatego że użytkownika usunięta, jeśli rozwiązanie zostanie usunięty.

## <a name="alerts"></a>Alerty
- Definiowanie listy adresatów jako parametr w pliku rozwiązania, dzięki czemu użytkownik może zdefiniować je podczas instalacjo rozwiązania.
- Konfigurowanie rozwiązania do [odwołania](solutions-solution-file.md#solution-resource) reguły alertów, aby ten użytkownik może zmienić ich konfiguracji.  Firma chce wprowadzić zmiany, takie jak modyfikowanie listy adresatów, zmieniając wartość progową alertu lub wyłączanie reguły alertu. 


## <a name="next-steps"></a>Kolejne kroki
* Opisano podstawowy przebieg [projektowania i tworzenia rozwiązania do zarządzania](solutions-creating.md).
* Dowiedz się, jak [utworzenie pliku rozwiązania](solutions-solution-file.md).
* [Dodawanie zapisanego wyszukiwania i alerty](solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodawanie widoków](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodawanie elementów runbook usługi Automation i innych zasobów](solutions-resources-automation.md) do rozwiązania do zarządzania.

