---
title: Integruj dzienniki z zasobów platformy Azure z systemami SIEM | Microsoft Docs
description: Dowiedz się więcej na temat Azure Log Integration, jego najważniejszych możliwości i sposobu działania.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9ba4a64268bcc57f04e92be83edb2ac71f18bcaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727593"
---
# <a name="introduction-to-azure-log-integration"></a>Wprowadzenie do Azure Log Integration

>[!IMPORTANT]
> Funkcja integracja z usługą Azure log zostanie zaniechana przez 06/15/2019. Pliki do pobrania AzLog zostały wyłączone w dniu 27 czerwca 2018. Aby uzyskać wskazówki na temat tego, co należy zrobić, przejrzyj temat publikowanie [za pomocą usługi Azure monitor w celu integracji z narzędziami Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integration udostępnione, aby uprościć zadanie integrowania dzienników platformy Azure z lokalnym systemem informacji o zabezpieczeniach i systemie zarządzania zdarzeniami (SIEM).

 Zalecaną metodą integrowania dzienników platformy Azure jest użycie łączników dostawcy SIEM. Azure Monitor zapewnia możliwość przesyłania strumieniowego dzienników do centrów zdarzeń, a dostawcy SIEM mogą pisać łączniki w celu dalszej integracji dzienników z centrum zdarzeń w SIEM.  Aby uzyskać opis tego, jak to działa, postępuj zgodnie z instrukcjami podanymi w temacie monitorowanie [monitorowania strumienia dla centrów zdarzeń danych](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). Ten artykuł zawiera również listę rozwiązań Siem, dla których bezpośrednie łączniki platformy Azure są już dostępne.  

> [!IMPORTANT]
> Jeśli podstawowe zainteresowanie zbiera dzienniki maszyn wirtualnych, większość dostawców SIEM obejmuje tę opcję w rozwiązaniu. Użycie łącznika dostawcy SIEM jest zawsze preferowaną alternatywą.

Dokumentacja funkcji Azure Log Integration jest nadal utrzymywana do momentu, gdy ta funkcja nie zostanie wycofana.

Przeczytaj więcej, aby dowiedzieć się więcej o funkcji Azure Log Integration:

Azure Log Integration zbiera zdarzenia systemu Windows z dzienników Podgląd zdarzeń systemu Windows, [dzienników aktywności platformy Azure](/azure/azure-monitor/platform/activity-logs-overview), [alertów Azure Security Center](/azure/security-center/security-center-intro)i [dzienników Diagnostyka Azure](/azure/azure-monitor/platform/diagnostic-logs-overview) z zasobów platformy Azure. Integracja pomaga swojemu rozwiązaniu SIEM na jednolity pulpit nawigacyjny dla wszystkich zasobów, zarówno lokalnie, jak i w chmurze. Możesz użyć pulpitu nawigacyjnego do odbierania, agregowania, skorelowania i analizowania alertów dotyczących zdarzeń zabezpieczeń.

> [!NOTE]
> Obecnie Azure Log Integration obsługuje tylko chmury komercyjne i Azure Government platformy Azure. Inne chmury nie są obsługiwane.

![Proces Azure Log Integration](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>Jakie dzienniki mogę zintegrować?

Platforma Azure tworzy obszerne rejestrowanie dla każdej usługi platformy Azure. Dzienniki reprezentują trzy typy dzienników:

* **Dzienniki kontroli/zarządzania**: Zapewnianie wglądu w [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) operacji tworzenia, aktualizowania i usuwania. Przykładem tego typu dziennika jest dziennik aktywności platformy Azure.
* **Dzienniki płaszczyzny danych**: Zapewnianie wglądu w zdarzenia, które są zgłaszane w przypadku korzystania z zasobu platformy Azure. Przykładem tego typu dziennika jest **system**Podgląd zdarzeń Windows, **zabezpieczenia**i kanały **aplikacji** na maszynie wirtualnej z systemem Windows. Innym przykładem jest rejestrowanie Diagnostyka Azure, które można skonfigurować za poorednictwem Azure Monitor.
* **Przetworzone zdarzenia**: Podaj przeanalizowane zdarzenia i informacje o alertach, które są przez Ciebie przetwarzane. Przykładem tego typu zdarzenia jest Azure Security Center alertów. Azure Security Center przetwarza i analizuje subskrypcję w celu zapewnienia alertów, które są istotne dla bieżącego stan zabezpieczeń.

Azure Log Integration obsługuje ArcSight, QRadar i Splunk. Skontaktuj się z dostawcą SIEM, aby ocenić, czy dostawca ma natywny łącznik. Nie używaj Azure Log Integration, jeśli jest dostępny łącznik natywny.

Jeśli nie są dostępne żadne inne opcje, należy rozważyć użycie Azure Log Integration. Poniższa tabela zawiera nasze zalecenia:

|SIEM | Klient korzysta już z integratora dzienników platformy Azure | Klient bada opcje integracji SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Rozpocznij migrację do [Azure monitor dodatku dla Splunk](https://splunkbase.splunk.com/app/3534/). | Użyj [łącznika Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Przeprowadź migrację do lub Rozpocznij korzystanie z łącznika QRadar, który opisano w ostatniej sekcji [strumienia danych monitorowania platformy Azure, do centrum zdarzeń w celu użycia przez narzędzie zewnętrzne](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). | Użyj łącznika QRadar, który opisano w ostatniej sekcji [strumienia danych monitorowania platformy Azure, do centrum zdarzeń w celu użycia przez narzędzie zewnętrzne](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). |
|**ArcSight** | Kontynuuj korzystanie z integratora dzienników platformy Azure do momentu udostępnienia łącznika, a następnie Przeprowadź migrację do rozwiązania opartego na łączniku.  | Rozważ użycie dzienników Azure Monitor jako alternatywy. Nie dołączaj do Azure Log Integration, chyba że wolisz przejść przez proces migracji, gdy łącznik będzie dostępny. |

> [!NOTE]
> Chociaż Azure Log Integration jest bezpłatnym rozwiązaniem, dostępne są koszty usługi Azure Storage związane z magazynem informacji o plikach dziennika.

Jeśli potrzebujesz pomocy, możesz utworzyć [żądanie pomocy technicznej](/azure/azure-supportability/how-to-create-azure-support-request). Dla usługi wybierz pozycję **integracja dzienników**.

## <a name="next-steps"></a>Następne kroki

W tym artykule wprowadzono Azure Log Integration. Aby dowiedzieć się więcej o Azure Log Integration i typach obsługiwanych dzienników, zobacz następujące artykuły:

* [Rozpocznij pracę z Azure log Integration](azure-log-integration-get-started.md). Ten samouczek przeprowadzi Cię przez proces instalacji Azure Log Integration. Opisano w nim również sposób integrowania dzienników z magazynu systemu Windows Diagnostyka Azure (funkcji wad), dzienników aktywności platformy Azure, alertów Azure Security Center i dzienników inspekcji Azure Active Directory.
* [Azure log Integration często zadawanych pytań](azure-log-integration-faq.md). Często zadawane pytania dotyczące Azure Log Integration.
* Dowiedz się więcej na temat [przesyłania strumieniowego danych monitorowania platformy Azure do centrum zdarzeń w celu użycia przez narzędzie zewnętrzne](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs).

