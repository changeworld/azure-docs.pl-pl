---
title: Integracja z pakietem Operations Management Suite (OMS) | Dokumentacja firmy Microsoft
description: Oprócz przy użyciu standardowych funkcji OMS, możesz też zintegrować ją z innymi aplikacje do zarządzania usługami i zapewnienie środowisku hybrydowym zarządzania scenariusze zarządzania niestandardowe unikatowe w danym środowisku lub niestandardowych zarządzania środowisko dla klientów.  Ten artykuł zawiera omówienie różnych opcji dotyczących integracji z usługą OMS i łącza do artykułów szczegółowe informacje techniczne.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23865862"
---
# <a name="integrating-with-operations-management-suite-oms"></a>Integracja z pakietem Operations Management Suite (OMS)
Operations Management Suite jest firmy Microsoft w chmurze IT rozwiązania do zarządzania ułatwiające zarządzanie i ochrona lokalnej infrastruktury w chmurze.  Oprócz przy użyciu standardowych funkcji OMS, możesz też zintegrować ją z innymi aplikacje do zarządzania usługami i zapewnienie środowisku hybrydowym zarządzania scenariusze zarządzania niestandardowe unikatowe w danym środowisku lub niestandardowych zarządzania środowisko dla klientów.  Ten artykuł zawiera omówienie różnych opcji do integracji z usługami OMS i łącza do artykułów szczegółowe informacje techniczne. 

## <a name="log-analytics"></a>Log Analytics
Dane zbierane przez analizy dzienników danych zarządzania znajduje się w repozytorium, która jest hostowana na platformie Azure.  Wszystkie dane przechowywane w repozytorium jest dostępna w wyszukiwaniach dziennika, które zapewniają szybki analizy w bardzo dużych ilości danych.  Wymagania w zakresie integracji można wypełnić repozytorium nowymi danymi udostępnianie analizy lub wyodrębniania danych w repozytorium, aby zapewnić nowej wizualizacji lub zintegrować z innego narzędzia do zarządzania.

Każda część danych w repozytorium jest przechowywany jako rekord.  Podczas wypełniania repozytorium, należy zapewnić użytkownikom typu rekordu, który używa rozwiązania oraz opis jego właściwości.  Podczas pobierania danych, należy te informacje o pracy z danych.

![Wypełnianie repozytorium OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Wypełnij repozytorium analizy dzienników
Istnieje wiele metod wypełniania repozytorium OMS.  Zależy od używanej metody czynników, takich jak którym znajduje się źródło danych, formatu danych i który musi obsługiwać klientów.  Gdy dane są przechowywane w repozytorium, nie ma różnicy jak został zebrany.

W poniższych sekcjach opisano różne opcje w celu wypełnienia repozytorium OMS.

#### <a name="connected-sources-and-data-sources"></a>Połączonych źródeł i źródła danych
Połączonych źródeł, są lokalizacje, w którym można pobrać danych dla repozytorium OMS.  Źródła danych i rozwiązania należy uruchomić na połączonych źródeł i definiowanie danych, które są zbierane.  Jeśli aplikacja zapisuje dane do jednej z tych źródeł danych, następnie można zebrać go przez skonfigurowanie źródła danych.  Na przykład jeśli aplikacja tworzy zdarzenia dziennika systemowego, następnie one mogą być zbierane przez źródło danych dziennika systemowego na agenta systemu Linux.

* [Źródła danych w analizy dzienników](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Rozwiązania
Rozwiązania rozszerzyć możliwości OMS.  Rozwiązanie może zbierać dane ze źródła połączonych lub go na rekordy już zebrane w repozytorium może wykonywać analizy.  Każdego z rozwiązań firmy Microsoft ma poszczególnych artykułu, który udostępnia szczegółowe informacje na dane, które zbiera.

* [Rozwiązania w analizy dzienników](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>Moduł zbierający dane HTTP interfejsu API
Interfejs API modułów zbierających dane HTTP Analytics dziennika jest interfejsu API REST, która pozwala na dodawanie danych JSON do repozytorium analizy dzienników.  Jeśli masz aplikację, która nie zapewnia danych za pomocą jednego z innych źródeł danych lub rozwiązania, można wykorzystać ten interfejs API.  Może służyć do wypełnienia repozytorium za pomocą dowolnego klienta, który można wywołać interfejsu API i nie bazuje na harmonogram zbierania źródła danych lub rozwiązanie.

* [Moduł zbierający dane HTTP analizy dziennika interfejsu API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Pobieranie danych z repozytorium analizy dzienników
Istnieje wiele metod pobierania danych z repozytorium OMS.  Może użytkownicy do pobierania danych przy użyciu konsoli OMS i udostępnia je z różnych typów wizualizacji i analizy.  Można również pobrać dane z procesu zewnętrznego, takich jak innego rozwiązania do zarządzania.

#### <a name="log-searches"></a>Dziennik wyszukiwania
Wszystkie dane przechowywane w repozytorium OMS jest dostępna za pośrednictwem dziennik wyszukiwania.  Użytkownicy mogą wykonywać własne analizy ad hoc w konsoli OMS lub Utwórz pulpit nawigacyjny wizualizacji wyszukiwania określonego dziennika.  Rozwiązania może zawierać niestandardowe widoki z wizualizacjami opartymi na wstępnie zdefiniowane wyszukiwania.  Z zewnętrznego narzędzia zarządzania lub aplikacji, można użyć interfejsu API Search dziennika uzyskują dostęp do danych w repozytorium OMS.  

* [Dziennik wyszukiwania analizy dzienników](../log-analytics/log-analytics-log-searches.md)
* [Analiza dzienników dziennika wyszukiwania interfejsu API REST](../log-analytics/log-analytics-log-search-api.md)
* [Polecenia cmdlet analizy dzienników](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Widoki niestandardowe
Projektant widoków umożliwia tworzenie niestandardowych widoków w konsoli OMS użytkownikom wizualizacji i analizy danych w rozwiązaniu.  Każdy widok zawiera wyświetlany na stronie głównej w konsoli i dowolną liczbę części wizualizacji, które są oparte na dziennik wyszukiwania, które należy zdefiniować kafelka.

* [Projektant widoków analizy dzienników](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Analiza dzienników można automatycznie eksportować dane z repozytorium OMS do usługi Power BI, można wykorzystać jej wizualizacje i narzędzia do analizy.  Wykonuje tego eksportu zgodnie z harmonogramem, dane są zawsze aktualne. 

* [Eksportuj dane analizy dzienników do usługi Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automatyzacja
OMS można zautomatyzować procesy, aby zareagować na zebranych danych lub wykonania innych funkcji zarządzania.  Może zbierać dane z aplikacji i wstaw go do repozytorium OMS lub mogą zautomatyzować naprawianie znany problem w odpowiedzi na dane znalezione w repozytorium. 

![Automatyzacja](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Elementy Runbook
Elementy Runbook automatyzacji Azure uruchomić przepływy pracy i skrypty programu PowerShell w chmurze Azure.  Można ich użyć do zarządzania zasobami na platformie Azure lub innych zasobów, które są dostępne z chmury.  Elementy Runbook można również uruchomić w lokalnym centrum danych przy użyciu hybrydowego procesu roboczego elementu Runbook.  Można uruchomić elementu runbook z portalu Azure lub przy użyciu metod, takich jak programu PowerShell lub interfejsu API automatyzacji procesów zewnętrznych.

* [Uruchamianie elementu runbook automatyzacji Azure](../automation/automation-starting-a-runbook.md)
* [Polecenia cmdlet usługi automatyzacja systemu Azure](https://msdn.microsoft.com/library/dn690262.aspx)
* [Automatyzacja interfejsu API REST](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automatyzacja .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alerty
Reguły alertów automatycznie uruchamiać dziennik wyszukiwania zgodnie z harmonogramem.  Jeśli wyników spełniających kryteria określonego wynikowy alert może uruchomić element runbook automatyzacji Azure lub wywołaj element webhook, który można uruchomić procesu zewnętrznego.  Oba odpowiedzi na te mogą obejmować szczegóły alertu, w tym dane zwrócone w wyniku wyszukiwania dziennika.

* [Alerty w analizy dzienników](../log-analytics/log-analytics-alerts.md)
* [Interfejs API dziennika analizy alertu](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Kopii zapasowych i odzyskiwania lokacji
Azure Site Recovery i kopii zapasowych należy zapewnić usługi ochrony danych przedsiębiorstwa i zapewnienia dostępności serwerów i aplikacji.  Można korzystać z tych usług do wykonywania takich scenariuszy jako udostępnia usługi tworzenia kopii zapasowej dla aplikacji lub inicjowanie trybu failover maszyny wirtualnej.

* [Polecenia cmdlet tworzenia kopii zapasowej systemu Azure](https://msdn.microsoft.com/library/mt619253.aspx)
* [Usługi Azure Site Recovery interfejsu API REST](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Polecenia cmdlet programu Azure Site Recovery](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Niestandardowe rozwiązania
Logika integracji umożliwiająca Hermetyzowanie w niestandardowym do uruchamiania w obszarze roboczym lub w obszarze roboczym klienta.  Rozwiązanie może zawierać dowolną z metod integracji w tym artykule, oprócz innych zasobów, aby zapewnić scenariusza pełnego zarządzania.  Zasoby w rozwiązaniu są umieszczone w taki sposób, że po usunięciu rozwiązania, wszystkie zasoby, które on utworzony są usuwane z obszarem roboczym pakietu OMS i subskrypcji platformy Azure.

Na przykład rozwiązanie zawiera element runbook usługi Automatyzacja do zbierania i przetwarzania danych, a następnie wypełnij repozytorium analizy dzienników przy użyciu interfejsu API modułów zbierających dane HTTP.  Mogą również obejmować widok niestandardowy, który umożliwia wyświetlanie i analizuje dane zebrane.  

* Tworzenie niestandardowych rozwiązań (wkrótce)    

## <a name="next-steps"></a>Następne kroki
* Odwołanie [OMS SDK](operations-management-suite-sdk.md) Aby uzyskać informacje techniczne na temat automatyzacji usługi OMS.  

