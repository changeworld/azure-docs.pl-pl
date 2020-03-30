---
title: Omówienie dzienników platformy Azure | Dokumenty firmy Microsoft
description: Omówienie dzienników w usłudze Azure Monitor, które zapewniają bogate, częste dane dotyczące działania zasobu platformy Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659324"
---
# <a name="overview-of-azure-platform-logs"></a>Omówienie dzienników platformy Azure
Dzienniki platformy zawierają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, na których zależą. Są one generowane automatycznie, chociaż trzeba skonfigurować niektóre dzienniki platformy, które mają być przekazywane do jednego lub więcej miejsc docelowych, które mają zostać zachowane. Ten artykuł zawiera omówienie dzienników platformy, w tym informacje, które dostarczają i jak można skonfigurować je do zbierania i analizy.

## <a name="types-of-platform-logs"></a>Rodzaje dzienników platform
W poniższej tabeli wymieniono określone dzienniki platformy, które są dostępne w różnych warstwach platformy Azure.

| Log | Warstwa | Opis |
|:---|:---|:---|
| Dzienniki zasobów | Zasoby platformy Azure | Zapewnij wgląd w operacje, które zostały wykonane w ramach zasobu platformy Azure *(płaszczyzny danych),* na przykład uzyskanie klucza tajnego z usługi Key Vault lub złożenie żądania do bazy danych. Zawartość dzienników zasobów zależy od usługi platformy Azure i typu zasobu.<br><br>*Dzienniki zasobów były wcześniej określane jako dzienniki diagnostyczne.*  |
| Dziennik aktywności | Subskrypcja platformy Azure | Zapewnia wgląd w operacje na każdym zasobie platformy Azure w subskrypcji z zewnątrz *(płaszczyzna zarządzania)* oprócz aktualizacji zdarzeń kondycji usługi. Użyj dziennika aktywności, aby określić, _co_, _kto_i _kiedy_ dla wszelkich operacji zapisu (PUT, POST, DELETE) podjęte na zasoby w ramach subskrypcji. Można również zrozumieć stan operacji i inne odpowiednie właściwości.  Istnieje jeden dziennik aktywności dla każdej subskrypcji platformy Azure. |
| Dzienniki usługi Azure Active Directory | Dzierżawa platformy Azure |  Zawiera historię działania logowania i dziennik inspekcji zmian wprowadzonych w usłudze Azure Active Directory dla określonej dzierżawy. Zobacz [Co to są raporty usługi Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)   |

> [!NOTE]
> Dziennik aktywności platformy Azure jest przede wszystkim dla działań, które występują w usłudze Azure Resource Manager. Nie śledzi zasobów przy użyciu modelu Classic/RDFE. Niektóre klasyczne typy zasobów mają dostawcę zasobów serwera proxy w usłudze Azure Resource Manager (na przykład Microsoft.ClassicCompute). Jeśli wchodzisz w interakcję z klasycznym typem zasobu za pośrednictwem usługi Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy, operacje są wyświetlane w dzienniku aktywności. Jeśli wchodzisz w interakcję z klasycznym typem zasobu poza serwerami proxy usługi Azure Resource Manager, twoje akcje są rejestrowane tylko w dzienniku operacji. Dziennik operacji można przeglądać w osobnej sekcji portalu.

![Omówienie dzienników platformy](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Wyświetlanie dzienników platformy
Istnieją różne opcje wyświetlania i analizowania różnych dzienników platformy Azure.

- Wyświetl dziennik aktywności w portalu Azure i uzyskaj dostęp do zdarzeń z programu PowerShell i interfejsu wiersza polecenia. Zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure, aby](activity-log-view.md) uzyskać szczegółowe informacje. 
- Wyświetlanie raportów zabezpieczeń i aktywności usługi Azure Active Directory w witrynie Azure portal. Zobacz [Co to są raporty usługi Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  szczegółowe informacje.
- Dzienniki zasobów są generowane automatycznie przez obsługiwane zasoby platformy Azure, ale nie są dostępne do wyświetlania, chyba że zostanie wysłane do [miejsca docelowego.](#destinations) 

## <a name="destinations"></a>Miejsca docelowe
Dzienniki platformy można wysyłać do jednego lub więcej miejsc docelowych w poniższej tabeli, w zależności od wymagań dotyczących monitorowania. Skonfiguruj miejsca docelowe dla dzienników [platformy, tworząc ustawienie diagnostyczne](diagnostic-settings.md).

| Element docelowy | Scenariusz | Dokumentacja |
|:---|:---|:---|:---|
| Obszar roboczy usługi Log Analytics | Analizuj dzienniki za pomocą innych danych monitorowania i korzystaj z funkcji usługi Azure Monitor, takich jak zapytania dziennika i alerty. | [Dziennik aktywności i dzienniki zasobów](resource-logs-collect-workspace.md)<br>[Dzienniki usługi Azure Activity Directory](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archiwizuj dzienniki do inspekcji, analizy statycznej lub kopii zapasowej. |[Dziennik aktywności i dzienniki zasobów](archive-diagnostic-logs.md)<br>[Dzienniki usługi Azure Activity Directory](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum zdarzeń | Przesyłaj strumieniowo dzienniki do systemów rejestrowania i telemetrii innych firm.  |[Dziennik aktywności i dzienniki zasobów](resource-logs-stream-event-hubs.md)<br>[Dzienniki usługi Azure Activity Directory](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie schematu dziennika aktywności dla różnych kategorii](activity-log-schema.md)
* [Wyświetlanie schematu dziennika zasobów dla różnych usług platformy Azure](diagnostic-logs-schema.md)
