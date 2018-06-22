---
title: Monitorowanie fabryki danych Azure z usługą OMS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować fabryki danych Azure danych routingu do Operations Management Suite (OMS) w celu analizy.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304423"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Monitor fabryki danych Azure w usłudze Operations Management Suite (OMS)

Za pomocą integracji usługi fabryka danych Azure i Azure Monitor na przesyłanie danych do Operations Management Suite (OMS). Integracja ta jest przydatne w następujących scenariuszach:

1.  Chcesz zapisać złożonych zapytań na bogaty zestaw miar, które są publikowane przez fabrykę danych z usługą OMS. Można również utworzyć niestandardowe alerty na tych zapytań przy użyciu pakietu OMS.

2.  Mają być monitorowane przez fabryki danych. Za przekazywanie danych z wielu fabryk danych do jednej obszar roboczy OMS.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Konfigurowanie ustawień diagnostycznych i obszarem roboczym pakietu OMS

Włącz ustawienia diagnostyki dla fabryką danych.

1.  Wybierz **Azure Monitor** -> **ustawień diagnostycznych** -> Wybierz fabrykę danych -> Włącz diagnostykę.

    ![Monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Podaj ustawienia diagnostyki tym konfiguracja obszar roboczy OMS.

    ![Monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Instalowanie pakietu OMS Analytics fabryki danych Azure z portalu Azure Marketplace

![Monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kliknij przycisk **Utwórz** i wybierz obszar roboczy OMS i ustawień obszarem roboczym pakietu OMS.

![Monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Monitorowanie metryk fabryki danych Azure przy użyciu pakietu OMS

Instalowanie **Analytics fabryki danych Azure** pakietu OMS tworzy domyślny zestaw widoków umożliwiającą następujące metryki:

- Uruchamia potoku ADF uruchamia-1) przez fabrykę danych

- Uruchamia ADF-2) uruchomień działania fabryka danych

- Uruchamia wyzwalacz uruchamia ADF-3) fabryka danych

- Błędy ADF-1) 10 pierwszych błędy potoku fabryki danych

- Błędy ADF-2) uruchomień działania 10 pierwszych fabryka danych

- Błędy ADF-3) 10 pierwszych wyzwalacza błędy fabryka danych

- Statystyki ADF-1) uruchomień działania według typu

- Uruchamia wyzwalacz ADF statystyki-2) według typu

- Czas trwania uruchamia potoku Max ADF statystyki-3)

![Monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![Monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Można zwizualizować powyżej metryki, przyjrzeć się zapytań za te metryki, edytowanie zapytań, tworzyć alerty i tak dalej.

![Monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitora i programowe zarządzanie potoki](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) Aby dowiedzieć się więcej o monitorowaniu i zarządzaniu nimi potoki przez uruchamianie skryptów.
