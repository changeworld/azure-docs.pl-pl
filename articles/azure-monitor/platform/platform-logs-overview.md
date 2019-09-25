---
title: Omówienie dzienników platformy Azure | Microsoft Docs
description: Omówienie dzienników diagnostycznych na platformie Azure, które zapewniają rozbudowane, częste dane dotyczące operacji zasobu platformy Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262572"
---
# <a name="overview-of-azure-platform-logs"></a>Omówienie dzienników platformy Azure
Dzienniki platformy zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. Są one generowane automatycznie, mimo że konieczne jest skonfigurowanie niektórych dzienników platformy do przechowania do co najmniej jednego miejsca docelowego, które ma zostać zachowane. Ten artykuł zawiera omówienie dzienników platformy, w tym informacje, które zapewnia i jak można je skonfigurować do zbierania i analizy.

## <a name="types-of-platform-logs"></a>Typy dzienników platformy
W poniższej tabeli wymieniono konkretne dzienniki platformy, które są dostępne na różnych warstwach platformy Azure.

| Warstwa | Dzienniki | Opis |
|:---|:---|:---|
| Zasoby platformy Azure | [Dzienniki zasobów](resource-logs-overview.md) | Zapewniaj wgląd w operacje wykonywane w ramach zasobu platformy Azure ( *płaszczyzny danych*), na przykład w celu uzyskania wpisu tajnego z Key Vault lub żądania do bazy danych. Zawartość dzienników zasobów zależy od usługi platformy Azure i typu zasobu.<br>*Dzienniki zasobów wcześniej były nazywane dziennikami diagnostycznymi.*  |
| Subskrypcja platformy Azure | [Dziennik aktywności](activity-logs-overview.md) | Zapewnia wgląd w operacje dotyczące poszczególnych zasobów platformy Azure w ramach subskrypcji z zewnątrz (*płaszczyzny zarządzania*) poza aktualizacjami Service Health zdarzeń. Dla każdej subskrypcji platformy Azure istnieje pojedynczy dziennik aktywności.   |
| Dzierżawa platformy Azure | [Dzienniki Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | Zawiera historię działań związanych z logowaniem i dziennika inspekcji zmian wprowadzonych w Azure Active Directory dla określonej dzierżawy.   |


![Omówienie dzienników platformy](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Wyświetlanie dzienników platformy
Można wyświetlić [Dziennik aktywności](activity-log-view.md) i [dzienniki Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) w Azure Portal. Aby je wyświetlić, należy wysłać dzienniki zasobów do [miejsca docelowego](#destinations) .


## <a name="destinations"></a>Cele
Można wysłać dzienniki platformy do co najmniej jednego miejsca docelowego w poniższej tabeli, w zależności od wymagań dotyczących monitorowania. 

| Destination | Scenariusz | Dokumentacja |
|:---|:---|:---|:---|
| Obszar roboczy usługi Log Analytics | Analizuj dzienniki z innymi danymi monitorowania i korzystaj z funkcji Azure Monitor, takich jak zapytania i alerty dzienników. | [Dzienniki zasobów](resource-logs-collect-storage.md)<br>[Dziennik aktywności](activity-log-collect.md)<br>[Dzienniki katalogów aktywności platformy Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archiwizuj dzienniki na potrzeby inspekcji, analizy statycznej lub kopii zapasowej. |[Dzienniki zasobów](archive-diagnostic-logs.md)<br>[Dziennik aktywności](activity-log-export.md)<br>[Dzienniki katalogów aktywności platformy Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum zdarzeń | Przesyłanie strumieniowe dzienników do systemów rejestrowania i telemetrii innych firm.  |[Dzienniki zasobów](resource-logs-stream-event-hubs.md)<br>[Dziennik aktywności](activity-log-export.md)<br>[Dzienniki katalogów aktywności platformy Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Ustawienia diagnostyczne i profile dzienników
Skonfiguruj lokalizacje docelowe dzienników zasobów i dzienników Azure Active Directory, [tworząc ustawienie diagnostyczne](diagnostic-settings.md). Skonfiguruj miejsca docelowe dla dziennika aktywności, [tworząc profil dziennika](activity-log-export.md) lub [łącząc go z obszarem roboczym log Analytics](activity-log-collect.md).

Ustawienia diagnostyczne i profil dziennika definiują następujące elementy:

- Co najmniej jeden miejsce docelowe do wysyłania wybranych dzienników i metryk.
- Które kategorie dzienników i metryki z zasobu są wysyłane do miejsc docelowych.
- W przypadku wybrania konta magazynu jako miejsca docelowego, jak długo każda kategoria dziennika powinna zostać zachowana.



## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dzienniku aktywności](activity-logs-overview.md)
* [Dowiedz się więcej o dziennikach zasobów](resource-logs-overview.md)
* [Wyświetlanie schematu dziennika zasobów dla różnych usług platformy Azure](diagnostic-logs-schema.md)
