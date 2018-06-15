---
title: Migracja platformy usługi Azure Security Center | Microsoft Docs
description: W tym dokumencie omówiono niektóre zmiany w sposobie zbierania danych usługi Azure Security Center.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 67cb532b6c8a5d00923bc3b41709956971ead5c3
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774475"
---
# <a name="azure-security-center-platform-migration"></a>Migracja platformy usługi Azure Security Center

Począwszy od początku czerwca 2017 usługa Azure Security Center wprowadza ważne zmiany dotyczące sposobu zbierania i przechowywania danych zabezpieczeń.  Te zmiany otwierają nowe możliwości, takie jak możliwość łatwego wyszukiwania danych zabezpieczeń i lepszego dopasowania do innych usług zarządzania i monitorowania platformy Azure.

> [!NOTE]
> Migracja platformy nie powinna mieć wpływu na zasoby produkcyjne i nie wymaga żadnej akcji ze strony użytkownika.


## <a name="whats-happening-during-this-platform-migration"></a>Co się stanie podczas tej migracji platformy?

Wcześniej usługa Security Center używała programu Azure Monitoring Agent do zbierania danych zabezpieczeń z maszyn wirtualnych. Dane te obejmują informacje o konfiguracjach zabezpieczeń, używane do identyfikowania luk w zabezpieczeniach, oraz zdarzenia zabezpieczeń, używane do wykrywania zagrożeń. Te dane były przechowywane na kontach magazynu na platformie Azure.

Teraz usługa Security Center korzysta z programu Microsoft Monitoring Agent — jest to ten sam agent, który jest używany przez usługę Log Analytics. Dane zbierane z tego agenta są przechowywane w każdym istniejącym [obszarze roboczym](../log-analytics/log-analytics-manage-access.md) usługi *Log Analytics* skojarzonym z subskrypcją platformy Azure lub w nowych obszarach roboczych, uwzględniając lokalizację geograficzną maszyny wirtualnej.

## <a name="agent"></a>Agent

W ramach przejścia program Microsoft Monitoring Agent (dla systemu [Windows](../log-analytics/log-analytics-windows-agent.md) lub [Linux](../log-analytics/log-analytics-linux-agents.md)) jest instalowany na wszystkich maszynach wirtualnych platformy Azure, z których obecnie są zbierane dane.  Jeśli na maszynie wirtualnej jest już zainstalowany program Microsoft Monitoring Agent, usługa Security Center korzysta z zainstalowanego agenta.

Przez pewien okres (zazwyczaj kilka dni) obaj agenci będą uruchamiani równolegle, aby zapewnić płynne przejście bez utraty danych. Umożliwi to firmie Microsoft sprawdzenie, czy nowy potok danych działa, przed zaprzestaniem korzystania z bieżącego potoku. Po zweryfikowaniu poprawnego działania nowego potoku program Azure Monitoring Agent zostanie usunięty z maszyn wirtualnych. Nie jest wymagane żadne działanie ze strony użytkownika. Po przeprowadzeniu migracji wszystkich klientów otrzymasz wiadomość e-mail z powiadomieniem.
 
Nie zaleca się ręcznego odinstalowywania programu Azure Monitoring Agent podczas migracji, ponieważ może to skutkować lukami w danych zabezpieczeń. Jeśli potrzebujesz dodatkowej pomocy, skonsultuj się z [działem obsługi klienta i pomocą techniczną firmy Microsoft](https://support.microsoft.com/contactus/). 

Program Microsoft Monitoring Agent dla systemu Windows wymaga użycia portu TCP 443. Przeczytaj [Przewodnik rozwiązywania problemów z usługą Azure Security Center](security-center-troubleshooting-guide.md), aby uzyskać więcej informacji.


> [!NOTE] 
> Ponieważ program Microsoft Monitoring Agent może być używany przez inne usługi zarządzania i monitorowania platformy Azure, agent ten nie zostanie odinstalowany automatycznie po wyłączeniu zbierania danych w usłudze Security Center. Można jednak ręcznie odinstalować agenta w razie potrzeby.

## <a name="workspace"></a>Obszar roboczy

Zgodnie z wcześniejszym opisem dane zbierane z programu Microsoft Monitoring Agent (w imieniu usługi Security Center) są przechowywane w istniejących obszarach roboczych usługi Log Analytics skojarzonych z subskrypcją platformy Azure lub w nowych obszarach roboczych, uwzględniając lokalizację geograficzną maszyny wirtualnej.

W witrynie Azure Portal można wyświetlić listę swoich obszarów roboczych usługi Log Analytics, w tym wszystkich utworzonych przez usługę Security Center. W przypadku nowych obszarów roboczych zostanie utworzona powiązana grupa zasobów. Stosowana jest następująca konwencja nazewnictwa:

- Obszar roboczy: *DefaultWorkspace-[identyfikator-subskrypcji]-[lokalizacja-geograficzna]*
- Grupa zasobów: *DefaultResouceGroup-[lokalizacja-geograficzna]* 
 
W przypadku obszarów roboczych utworzonych przez usługę Security Center dane są przechowywane przez 30 dni. W przypadku istniejących obszarów przechowywanie zależy od warstwy cenowej obszaru roboczego.

> [!NOTE]
> Dane wcześniej zebrane przez usługę Security Center pozostają na kontach magazynu. Po zakończeniu migracji można usunąć te konta magazynu.

### <a name="security-management-solution"></a>Rozwiązanie do zarządzania zabezpieczeniami 

W przypadku istniejących klientów, którzy nie mają zainstalowanego rozwiązania do zarządzania zabezpieczeniami w usłudze Log Analytics, firma Microsoft zainstaluje je w ich obszarze roboczym, ale tylko dla maszyn wirtualnych platformy Azure. Nie należy odinstalowywać tego rozwiązania, ponieważ nie ma żadnego automatycznego korygowania, jeśli zostanie to zrobione za pomocą konsoli zarządzania.


## <a name="other-updates"></a>Inne aktualizacje

W połączeniu z migracją platform wprowadzamy pewne dodatkowe drobne aktualizacje:

- Będą obsługiwane dodatkowe wersje systemów operacyjnych. Zapoznaj się z listą [tutaj](security-center-faq.md#virtual-machines).
- Lista luk w zabezpieczeniach systemu operacyjnego zostanie rozszerzona. Zapoznaj się z listą [tutaj](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- [Ceny](https://azure.microsoft.com/pricing/details/security-center/) będą ustalane proporcjonalnie do liczby godzin (wcześniej do liczby dni), co przyniesie oszczędności niektórym klientom.
- Zbieranie danych będzie wymagane i automatycznie włączone dla klientów w warstwie cenowej Standardowa.
- Usługa Azure Security Center rozpocznie odnajdywanie rozwiązań chroniących przed złośliwym kodem, które nie zostały wdrożone za pomocą rozszerzeń platformy Azure. Najpierw dostępne będzie odnajdywanie programów Endpoint Protection i Defender firmy Symantec dla systemu Windows 2016.
- Zasady zapobiegania i powiadomienia można konfigurować tylko na poziomie *Subskrypcja*, ale cennik nadal można ustawić na poziomie *Grupa zasobów*.

