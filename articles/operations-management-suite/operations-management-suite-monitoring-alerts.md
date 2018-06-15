---
title: Alert zarządzania w programie Microsoft monitoring produktów | Dokumentacja firmy Microsoft
description: Alert wskazuje niektórych problem, który wymaga uwagi ze strony administratora.  W tym artykule opisano różnice w sposób alerty są tworzone i zarządzane w System Center Operations Manager (SCOM) i analizy dzienników i zapewnia najlepszych rozwiązań dzięki wykorzystaniu te dwa produkty strategii zarządzania alertami hybrydowego.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865771"
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Zarządzanie alertami z monitorowania firmy Microsoft
Alert wskazuje niektórych problem, który wymaga uwagi ze strony administratora.  Istnieją różne różnice między programu System Center Operations Manager (SCOM) i analizy dzienników w Operations Management Suite (OMS) pod względem sposobu tworzenia alertów, jak są zarządzane i analizowane i jak otrzymasz powiadomienie, że krytyczne problem został Wykryto.

## <a name="alerts-in-operations-manager"></a>Alerty w programie Operations Manager
Alerty w SCOM są generowane przez poszczególne zasady lub monitory, aby wskazać określonego problemu.  Monitor może wygenerować alert, gdy przechodzi do stanu błędu, gdy reguła może generować alert, aby wskazać, niektóre problem krytyczny, który nie jest bezpośrednio powiązana z kondycję obiektu zarządzanego.  Pakiety administracyjne obejmują wiele przepływów pracy, które tworzą alertów dla aplikacji lub usługi, które zarządzają.  Część procesu konfigurowania nowego pakietu administracyjnego jest dostrajania, aby upewnić się, że nie otrzyma nadmiernego alerty dotyczące problemów, które nie zostały uznane za krytyczne.

![Widok alertów programu SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM zapewnia pełną zarządzania alertami alerty o stanie, który może zostać zmieniona przez administratorów w trakcie pracy, aby rozwiązać ten problem.  Gdy problem został rozwiązany, administrator ustawi alert do zamknięcia po tym czasie będzie już wyświetlane w widokach Wyświetlanie aktywnych alertów.  Alerty generowane przez monitory można rozwiązać automatycznie, gdy monitor powróci do stanu dobrej kondycji.

![Stan alertu](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alerty w analizy dzienników
Alert w analizy dzienników jest tworzona na podstawie zapytania dziennika, który jest automatycznie uruchamiany w regularnych odstępach czasu.  Wszelkie zapytania dziennika można utworzyć zasady alertu.  Jeśli zapytanie zwraca wyników spełniających kryteria określone, tworzona jest alert.  Może to być określonej kwerendy, która tworzy alert po wykryciu określonego zdarzenia lub można użyć ogólniejszych kwerendę, która wyszukuje wszystkie zdarzenia błędów związanych z określonej aplikacji.

Alerty analizy dziennika są zapisywane w repozytorium OMS jako zdarzenie i może zostać pobrany z zapytania dziennika.  Nie mają stanu, takich jak zdarzenia SCOM tak, aby wskazać, gdy problem został rozwiązany.

![OMS alert](media/operations-management-suite-monitoring-alerts/oms-alert.png)

SCOM jest używany jako źródło danych analizy dzienników, SCOM alerty są zapisywane do repozytorium OMS, ponieważ są one tworzone i modyfikowane.  

![Alert programu SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[Rozwiązania zarządzania alertami](http://technet.microsoft.com/library/mt484092.aspx) zawiera podsumowanie aktywne alerty i kilka typowych kwerend do pobierania różnych zestawów alertów.  Zapewnia bardziej efektywną analizy alertów niż raportu w SCOM.  Możesz przechodzenie od podsumowań do szczegółowych danych i tworzenie zapytań ad hoc do pobierania różnych zestawów alertów.

![Rozwiązanie do zarządzania alertu](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Powiadomienia
Powiadomienia w SCOM wysłać poczty lub tekstu w odpowiedzi na alerty, odpowiadających określonym kryteriom.  Można utworzyć subskrypcji powiadomień różnych, które mają różne osoby powiadomienie w zależności od takich kryteriów, jak obiekt monitorowana, ważność alertu, rodzaj problem, którego wykryć lub godzinę.

Kilka subskrypcji, można zaimplementować strategię pełną powiadomień dla dużej liczby pakietów administracyjnych.

![Alerty programu SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Analiza dzienników mogą wyświetlać powiadomienia pocztą utworzony alert przez ustawienie akcji powiadomienia e-mail dla każdego [reguły alertu](http://technet.microsoft.com/library/mt614775.aspx).  Go nie ma możliwości SCOM Subskrybuj wielu alertów, przy użyciu jednej reguły.  Należy także utworzyć własne reguły alertu, ponieważ OMS nie ma żadnych wstępnie skonfigurowane.

![Alerty usługi Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Nie można całkowicie zarządzać SCOM alertów w analizy dzienników jednak ponieważ można modyfikować tylko je w konsoli operacje.  Analiza dzienników jest przydatna jako część zarządzania alertami jednak przetwarzania dla zapewnienie tego samego SCOM narzędzi analizy nie ma.

## <a name="alert-remediation"></a>Korygowanie alertu
[Korygowanie](http://technet.microsoft.com/library/mt614775.aspx) odwołuje się do próba automatycznie rozwiązać problem identyfikowana na podstawie alertu.

SCOM umożliwia uruchamianie diagnostyki i odzyskiwania w odpowiedzi na wprowadzanie zła kondycja monitora.  Dzieje się jednocześnie do tworzenia alertu monitora.  Elementy diagnostyki i przywracania zwykle są zaimplementowane jako skryptu uruchamianego na agencie.  Diagnostyka próbuje zebrać więcej informacji na temat wykrytego problemu podczas odzyskiwania próbuje naprawić problem.

Analiza dzienników umożliwia uruchamianie [runbook usługi Automatyzacja Azure](https://azure.microsoft.com/documentation/services/automation/) lub wywołanie elementu webhook w odpowiedzi na alert analizy dzienników.  Elementy Runbook mogą zawierać złożonej logiki zaimplementowany w programie PowerShell.  Skrypt jest uruchamiany na platformie Azure i można uzyskać dostępu do żadnych zasobów platformy Azure lub dostępnych zasobów zewnętrznych z chmury.  Automatyzacja Azure mieć możliwość wykonania elementów runbook na serwerze w lokalnym centrum danych, ale ta funkcja nie jest obecnie dostępna podczas uruchamiania elementu runbook w odpowiedzi na alerty analizy dzienników.

Zarówno odzyskiwania w SCOM i elementów runbook w pakiecie OMS może zawierać skryptów programu PowerShell, ale odzyskiwania są trudne do tworzenia i zarządzania nimi, ponieważ musi być zawarty w pakiecie administracyjnym.  Elementy Runbook są przechowywane w automatyzacji Azure, która dostarcza funkcje do tworzenia, testowania i zarządzanie elementów runbook.

Jeśli używasz programu SCOM jako źródła danych do analizy dzienników można utworzyć alertu analizy dzienników, za pomocą zapytania dziennika można pobrać alertów SCOM przechowywane w repozytorium OMS.  Dzięki temu można uruchomić element runbook usługi Automatyzacja Azure w odpowiedzi na SCOM alert.  Oczywiście ponieważ element runbook będzie uruchamiany na platformie Azure, nie będzie działało strategię odzyskiwania problemy z lokalnymi.

## <a name="next-steps"></a>Następne kroki
* Poznaj lepiej [alertów w System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).

