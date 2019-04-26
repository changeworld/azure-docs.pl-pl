---
title: Integrowanie dzienników z zasobów platformy Azure z systemów SIEM | Dokumentacja firmy Microsoft
description: Więcej informacji na temat integracji dzienników platformy Azure, jego kluczowych możliwości i jak to działa.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: c644d81b647fc1151b10a94fb636e3eeaba1ce5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478316"
---
# <a name="introduction-to-azure-log-integration"></a>Wprowadzenie do integracji dzienników platformy Azure

>[!IMPORTANT]
> Funkcja integracji dziennika Azure zostaną wycofane 06/01/2019 r. Pliki do pobrania AzLog zostały wyłączone w dniu 27 cze 2018. Aby uzyskać wskazówki dotyczące co należy zrobić przenoszenie do przodu przeglądu wpis [użycia w usłudze Azure monitor do integracji z narzędziami rozwiązania SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integracja dzienników platformy Azure została udostępniona upraszcza zadanie integracji Azure dzienników z lokalnym systemem Security Information and Event Management (SIEM).

 Zalecaną metodą zintegrowanie dzienniki platformy Azure jest korzystanie z łączników z dostawcą rozwiązania SIEM. Usługa Azure Monitor umożliwia przesyłanie strumieniowe dzienników do usługi event hubs i dostawców rozwiązania SIEM można napisać łączniki do dalszej integracji dzienników z Centrum zdarzeń do rozwiązania SIEM.  Aby uzyskać opis sposobu działania, postępuj zgodnie z instrukcjami w [Monitor strumienia danych monitorowania usługi event hubs](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Artykuł zawiera również listę rozwiązań Siem, dla których bezpośrednie łączników usługi Azure są już dostępne.  

> [!IMPORTANT]
> Jeśli zainteresowanie podstawowy jest zbieranie dzienników maszyny wirtualnej, większość dostawców rozwiązania SIEM uwzględnić tę opcję w swoich rozwiązaniach. Za pomocą rozwiązania SIEM dostawcy łącznik jest zawsze preferowana alternatywa.

Dokumentacja na temat funkcji Azure Log Integration nadal jest już uaktualniana, dopóki ta funkcja jest przestarzała.

Odczyt, aby dowiedzieć się więcej na temat funkcji Azure Log Integration:

Integracja dzienników platformy Azure zbiera Windows zdarzenia w dziennikach podglądu zdarzeń Windows [Dzienniki aktywności platformy Azure](../azure-monitor/platform/activity-logs-overview.md), [alerty Centrum zabezpieczeń Azure](../security-center/security-center-intro.md), i [dzienniki diagnostyczne platformy Azure](../azure-monitor/platform/diagnostic-logs-overview.md) z Zasoby platformy Azure. Integracja ułatwia rozwiązania SIEM, czy zapewnić jednolity pulpit nawigacyjny dla wszystkich zasobów w środowisku lokalnym lub w chmurze. Pulpit nawigacyjny służy do odbierania, agregacji, korelowanie i analizowanie alerty dla zdarzeń dotyczących zabezpieczeń.

> [!NOTE]
> Obecnie usługa Azure Log Integration obsługuje tylko Azure komercyjnych i chmury Azure Government. Inne chmury nie są obsługiwane.

![Proces integracji dzienników Azure][1]

## <a name="what-logs-can-i-integrate"></a>Jakie dzienniki można zintegrować?

Azure tworzy szczegółowe rejestrowanie dla poszczególnych usług platformy Azure. Dzienniki reprezentują trzy typy dzienników:

* **Dzienniki kontroli/zarządzania**: Zapewniają widoczność [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) operacje tworzenia, AKTUALIZOWANIA lub usuwania. W dzienniku aktywności platformy Azure znajduje się przykład tego typu dziennika.
* **Dzienniki na płaszczyźnie danych**: Zapewniają wgląd w zdarzenia, które są wywoływane, gdy używasz zasobów platformy Azure. Przykładem tego typu dziennika jest Podgląd zdarzeń Windows **systemu**, **zabezpieczeń**, i **aplikacji** kanałów na maszynie wirtualnej Windows. Innym przykładem jest rejestrowania diagnostyki platformy Azure można skonfigurować za pomocą usługi Azure Monitor.
* **Przetworzone zdarzenia**: Podaj przeanalizowany zdarzeń i informacje o alertach, które są przetwarzane dla Ciebie. Przykładem tego typu zdarzenia jest alerty Centrum zabezpieczeń Azure. Usługa Azure Security Center przetwarza i analizuje subskrypcji, aby zapewnić alerty, które są istotne dla bieżącego poziomu bezpieczeństwa.

Integracja dzienników platformy Azure obsługuje ArcSight QRadar i Splunk. Skontaktuj się z dostawcą rozwiązania SIEM, aby ocenić, czy dostawca posiada natywnych łącznika. Nie należy używać usługi Azure Log Integration, jeśli dostępny jest łącznik natywnych.

Jeśli brak innych dostępnych opcji, należy wziąć pod uwagę za pomocą usługi Azure Log Integration. Poniższa tabela zawiera Nasze zalecenia:

|SIEM | Klient używa już integratora dziennika platformy Azure | Klient, bada opcji integracji rozwiązania SIEM|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Rozpocznij Migrowanie na [dodatek usługi Azure Monitor dla Splunk](https://splunkbase.splunk.com/app/3534/). | Użyj [łącznika Splunk](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Migrowanie do lub rozpocząć korzystanie z łącznika QRadar, które jest opisane w ostatniej sekcji [Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). | Używanie łącznika QRadar wymienioną w ostatniej sekcji [Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | W dalszym ciągu używać integratora dziennika platformy Azure, dopóki łącznik jest dostępny, a następnie przeprowadzić migrację do rozwiązań opartych na łącznika.  | Należy wziąć pod uwagę przy użyciu dzienników usługi Azure Monitor alternatywą. Powiadomień nie dołączy do integracji dziennika Azure, chyba że chcesz przejść przez proces migracji po udostępnieniu łącznika. |

> [!NOTE]
> Integracja dzienników platformy Azure jest bezpłatne rozwiązanie, istnieją koszty magazynów platformy Azure związane z magazynem informacje pliku dziennika.

Jeśli potrzebujesz pomocy, możesz utworzyć [żądania pomocy technicznej](../azure-supportability/how-to-create-azure-support-request.md). Usługi, wybierz **integracji dzienników**.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł zawiera wprowadzenie do integracji dziennika Azure. Aby dowiedzieć się więcej na temat integracji dzienników platformy Azure i typy dzienników, które są obsługiwane, zobacz następujące artykuły:

* [Rozpoczynanie pracy z usługą Azure Log Integration](security-azure-log-integration-get-started.md). Ten samouczek przeprowadzi instalację integracji dzienników Azure. Ona również opis integracji dzienników z magazynu Windows Azure Diagnostics narzędzie diagnostyczne, dzienniki aktywności platformy Azure, alertów usługi Azure Security Center i dzienników inspekcji usługi Azure Active Directory.
* [Integracja dzienników platformy Azure — często zadawane pytania (FAQ)](security-azure-log-integration-faq.md). Tych często zadawanych PYTAŃ, odpowiedzi na często zadawane pytania dotyczące usługi Azure Log Integration.
* Dowiedz się więcej na temat [strumienia monitorujących dane do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
