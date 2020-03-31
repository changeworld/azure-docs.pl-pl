---
title: Rejestrowanie i inspekcja zabezpieczeń platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o dziennikach dostępnych na platformie Azure i szczegółowych informacjach dotyczących zabezpieczeń, które możesz uzyskać.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750790"
---
# <a name="azure-security-logging-and-auditing"></a>Rejestrowanie i inspekcja zabezpieczeń platformy Azure

Platforma Azure oferuje szeroką gamę konfigurowalnych opcji inspekcji i rejestrowania zabezpieczeń, które ułatwiają identyfikowanie luk w zasadach i mechanizmach zabezpieczeń. W tym artykule omówiono generowanie, zbieranie i analizowanie dzienników zabezpieczeń z usług hostowanych na platformie Azure.

> [!Note]
> Niektóre zalecenia zawarte w tym artykule mogą spowodować zwiększenie wykorzystania zasobów danych, sieci lub zasobów obliczeniowych oraz zwiększyć koszty licencji lub subskrypcji.

## <a name="types-of-logs-in-azure"></a>Typy dzienników na platformie Azure

Aplikacje w chmurze są złożone z wieloma ruchomymi częściami. Rejestrowanie danych może dostarczyć szczegółowych informacji na temat aplikacji i pomóc:

- Rozwiązywanie wcześniejszych problemów lub zapobieganie potencjalnym problemom
- Zwiększ wydajność aplikacji lub łatwość konserwacji
- Automatyzacja działań, które w przeciwnym razie wymagałyby ręcznej interwencji

Dzienniki platformy Azure są podzielone na następujące typy:
* **Dzienniki kontroli/zarządzania** zawierają informacje o operacjach Azure Resource Manager CREATE, UPDATE i DELETE. Aby uzyskać więcej informacji, zobacz [Dzienniki aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md).

* **Dzienniki płaszczyzny danych** zawierają informacje o zdarzeniach wywoływanych w ramach użycia zasobów platformy Azure. Przykładami tego typu dziennika są system zdarzeń systemu Windows, zabezpieczenia i dzienniki aplikacji na maszynie wirtualnej (VM) i [dzienniki diagnostyczne,](../../azure-monitor/platform/platform-logs-overview.md) które są skonfigurowane za pośrednictwem usługi Azure Monitor.

* **Przetworzone zdarzenia** zawierają informacje o analizowanych zdarzeniach/alertach, które zostały przetworzone w Twoim imieniu. Przykładami tego typu są [alerty usługi Azure Security Center,](../../security-center/security-center-managing-and-responding-alerts.md) w których [usługa Azure Security Center](../../security-center/security-center-intro.md) przetworzyła i przeanalizowała subskrypcję oraz udostępnia zwięzłe alerty zabezpieczeń.

W poniższej tabeli wymieniono najważniejsze typy dzienników dostępnych na platformie Azure:

| Kategoria dziennika | Typ dziennika | Sposób użycia | Integracja |
| ------------ | -------- | ------ | ----------- |
|[Dzienniki aktywności](../../azure-monitor/platform/platform-logs-overview.md)|Zdarzenia płaszczyzny sterowania w zasobach usługi Azure Resource Manager|  Zapewnia wgląd w operacje, które zostały wykonane na zasoby w ramach subskrypcji.|    Interfejs API odpoczynku, [monitor platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)|
|[Dzienniki zasobów platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)|Częste dane dotyczące działania zasobów usługi Azure Resource Manager w ramach subskrypcji|   Zapewnia wgląd w operacje wykonywane przez sam zasób.| Azure Monitor|
|[Raporty w usłudze Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)|Dzienniki i raporty | Raportuje działania logowania użytkowników i informacje o aktywności systemowej dotyczące użytkowników i zarządzania grupami.|[Interfejs API wykresu](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Maszyny wirtualne i usługi w chmurze](../../azure-monitor/learn/quick-collect-azurevm.md)|Usługa Dziennika zdarzeń systemu Windows i syslog systemu Linux|  Przechwytuje dane systemowe i rejestrowanie danych na maszynach wirtualnych i przesyła te dane do wybranego konta magazynu.|   Windows (przy użyciu systemu Windows Azure Diagnostics [[WAD](../../monitoring-and-diagnostics/azure-diagnostics.md)] storage) i Linux w usłudze Azure Monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Rejestrowanie magazynu, udostępnia dane metryki dla konta magazynu|Zapewnia wgląd w żądania śledzenia, analizuje trendy użycia i diagnozuje problemy z kontem magazynu.|   INTERFEJS API REST lub [biblioteka klienta](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Dzienniki przepływu sieciowej grupy zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|Format JSON, pokazuje przepływy wychodzące i przychodzące na podstawie reguły|Wyświetla informacje o ruchu ip ruchu przychodzącego i wychodzącego za pośrednictwem sieciowej grupy zabezpieczeń.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Wgląd w aplikacje](../../azure-monitor/app/app-insights-overview.md)|Dzienniki, wyjątki i diagnostyka niestandardowa|  Zapewnia usługę monitorowania wydajności aplikacji (APM) dla deweloperów sieci web na wielu platformach.| INTERFEJS API REST, [usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Przetwarzanie danych / alertów bezpieczeństwa](../../security-center/security-center-intro.md)|  Alerty usługi Azure Security Center, monitor platformy Azure rejestruje alerty|    Zawiera informacje o zabezpieczeniach i alerty.|  INTERFEJSY API REST, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Integracja dziennika z lokalnymi systemami SIEM
[Integracja alertów usługi Security Center](../../security-center/security-center-export-data-to-siem.md) omawia sposób synchronizowania alertów usługi Security Center, zdarzeń zabezpieczeń maszyny wirtualnej zebranych przez dzienniki diagnostyczne platformy Azure i dzienników inspekcji platformy Azure za pomocą dzienników usługi Azure Monitor lub rozwiązania SIEM.

## <a name="next-steps"></a>Następne kroki

- [Inspekcja i rejestrowanie:](management-monitoring-overview.md)Ochrona danych przez zachowanie widoczności i szybkie reagowanie na alerty zabezpieczeń w odpowiednim czasie.

- [Rejestrowanie zabezpieczeń i zbieranie dzienników inspekcji na platformie Azure:](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)Wymuszaj te ustawienia, aby upewnić się, że wystąpienia platformy Azure zbierają poprawne dzienniki zabezpieczeń i inspekcji.

- [Konfigurowanie ustawień inspekcji dla zbioru witryn:](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)Jeśli jesteś administratorem zbioru witryn, pobierz historię akcji poszczególnych użytkowników i historię działań podejmowanych w określonym zakresie dat.

- [Przeszukaj dziennik inspekcji w Centrum zgodności & zabezpieczeń usługi Office 365:](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)Przeszukiwanie ujednoliconego dziennika inspekcji i wyświetlanie aktywności użytkowników i administratorów w organizacji usługi Office 365 za pomocą Centrum zgodności zabezpieczeń & usługi Office 365.
