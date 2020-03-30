---
title: Najważniejsze wskazówki dotyczące zarządzania w zakresie platformy Azure | Dokumenty firmy Microsoft
description: ''
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 837fb87d73698961ec1550b122840563d1707f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663202"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Najważniejsze wskazówki dotyczące tworzenia rozwiązań do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja do tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.  

Ten artykuł zawiera najlepsze rozwiązania dotyczące [tworzenia pliku rozwiązania do zarządzania na](solutions-solution-file.md) platformie Azure.  Informacje te będą aktualizowane w miarę identyfikowania dodatkowych najlepszych praktyk.

## <a name="data-sources"></a>Źródła danych
- Źródła danych można [skonfigurować za pomocą szablonu Menedżera zasobów,](../../azure-monitor/platform/template-workspace-configuration.md)ale nie powinny być uwzględniane w pliku rozwiązania.  Powodem jest to, że konfigurowanie źródeł danych nie jest obecnie idempotentne co oznacza, że rozwiązanie może zastąpić istniejącą konfigurację w obszarze roboczym użytkownika.<br><br>Na przykład rozwiązanie może wymagać zdarzenia ostrzeżenie i błąd z dziennika zdarzeń aplikacji.  Jeśli określisz to jako źródło danych w rozwiązaniu, istnieje ryzyko usunięcia zdarzenia informacji, jeśli użytkownik miał to skonfigurowane w obszarze roboczym.  Jeśli uwzględniono wszystkie zdarzenia, możesz zbierać nadmierne zdarzenia informacyjne w obszarze roboczym użytkownika.

- Jeśli rozwiązanie wymaga danych z jednego ze standardowych źródeł danych, należy zdefiniować to jako warunek wstępny.  Stan w dokumentacji, że klient musi skonfigurować źródło danych na własną rękę.  
- Dodaj komunikat [weryfikacji przepływu danych](../../azure-monitor/platform/view-designer-tiles.md) do dowolnych widoków w rozwiązaniu, aby poinstruować użytkownika o źródłach danych, które muszą być skonfigurowane do zbierania wymaganych danych.  Ten komunikat jest wyświetlany na kafelku widoku, gdy wymagane dane nie są znalezione.


## <a name="runbooks"></a>Elementy Runbook
- Dodaj [harmonogram automatyzacji](../../automation/automation-schedules.md) dla każdego egoisty w rozwiązaniu, który musi działać zgodnie z harmonogramem.
- Dołącz [moduł IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) do rozwiązania, który ma być używany przez księgi runbooków zapisujących dane w repozytorium usługi Log Analytics.  Skonfiguruj rozwiązanie, aby [odwoływać się](solutions-solution-file.md#solution-resource) do tego zasobu, tak aby pozostał, jeśli rozwiązanie zostanie usunięte.  Dzięki temu wiele rozwiązań do udostępniania modułu.
- Użyj [zmiennych automatyzacji,](../../automation/automation-schedules.md) aby zapewnić wartości do rozwiązania, które użytkownicy mogą chcieć zmienić później.  Nawet jeśli rozwiązanie jest skonfigurowane tak, aby zawierało zmienną, jego wartość nadal można zmienić.

## <a name="views"></a>Widoki
- Wszystkie rozwiązania powinny zawierać pojedynczy widok, który jest wyświetlany w portalu użytkownika.  Widok może zawierać wiele [części wizualizacji,](../../azure-monitor/platform/view-designer-parts.md) aby zilustrować różne zestawy danych.
- Dodaj komunikat [weryfikacji przepływu danych](../../azure-monitor/platform/view-designer-tiles.md) do dowolnych widoków w rozwiązaniu, aby poinstruować użytkownika o źródłach danych, które muszą być skonfigurowane do zbierania wymaganych danych.
- Skonfiguruj rozwiązanie, aby [zawierało](solutions-solution-file.md#solution-resource) widok, tak aby zostało usunięte, jeśli rozwiązanie zostanie usunięte.

## <a name="alerts"></a>Alerty
- Zdefiniuj listę adresatów jako parametr w pliku rozwiązania, aby użytkownik mógł je zdefiniować podczas instalowania rozwiązania.
- Skonfiguruj rozwiązanie do [odwoływania się](solutions-solution-file.md#solution-resource) do reguł alertów, aby użytkownicy mogli zmienić swoją konfigurację.  Mogą chcieć wprowadzić zmiany, takie jak modyfikowanie listy adresatów, zmiana progu alertu lub wyłączenie reguły alertu. 


## <a name="next-steps"></a>Następne kroki
* Przejmij podstawowy proces [projektowania i budowania rozwiązania do zarządzania.](solutions-creating.md)
* Dowiedz się, jak [utworzyć plik rozwiązania](solutions-solution-file.md).
* [Dodaj zapisane wyszukiwania i alerty](solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodaj widoki](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj elementy runbook automatyzacji i inne zasoby](solutions-resources-automation.md) do rozwiązania do zarządzania.

