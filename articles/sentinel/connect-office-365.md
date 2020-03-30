---
title: Łączenie danych usługi Office 365 z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane usługi Office 365 z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252514"
---
# <a name="connect-data-from-office-365-logs"></a>Łączenie danych z dzienników usługi Office 365



Dzienniki inspekcji z [usługi Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) można przesyłać strumieniowo za pomocą jednego kliknięcia. Dzienniki inspekcji z usługi Office 365 można przesyłać strumieniowo do obszaru roboczego usługi Azure Sentinel w tej samej dzierżawie. Łącznik dziennika aktywności usługi Office 365 zapewnia wgląd w bieżące działania użytkowników. Otrzymasz informacje o różnych akcjach i zdarzeniach użytkowników, administratorów, systemu i zasad z usługi Office 365. Łącząc dzienniki usługi Office 365 z usługą Azure Sentinel, można używać tych danych do wyświetlania pulpitów nawigacyjnych, tworzenia alertów niestandardowych i usprawniania procesu badania.

> [!IMPORTANT]
> Jeśli masz licencję E3, zanim będzie można uzyskać dostęp do danych za pośrednictwem interfejsu API działania zarządzania usługą Office 365, musisz włączyć ujednolicone rejestrowanie inspekcji dla organizacji usługi Office 365. W tym celu należy włączyć dziennik inspekcji usługi Office 365. Aby uzyskać instrukcje, zobacz [Włączanie lub wyłączanie wyszukiwania w dzienniku inspekcji usługi Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Aby uzyskać więcej informacji, [zobacz Odwołanie do interfejsu API działania zarządzania usługą Office 365.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w dzierżawie.
- Dzierżawa musi mieć włączoną inspekcję ujednoliconą. Dzierżawcy z licencjami usługi Office 365 E3 lub E5 mają domyślnie włączoną ujednoliconą inspekcję. <br>Jeśli dzierżawa nie ma jednej z tych licencji, należy włączyć ujednoliconą inspekcję dzierżawy przy użyciu jednej z następujących metod:
    - [Za pomocą polecenia cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) i włączyć parametr "UnifiedAuditLogIngestionEnabled").
    - [Korzystanie z interfejsu użytkownika Centrum zgodności & zabezpieczeń](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Łączenie z usługą Office 365

1. W usłudze Azure Sentinel wybierz pozycję **Łączniki danych,** a następnie kliknij kafelek **usługi Office 365.**

2. Jeśli nie masz jeszcze włączonej, możesz to zrobić, przechodząc do bloku **Łączniki danych** i wybierając łącznik **usługi Office 365.** W tym miejscu możesz kliknąć **stronę Otwórz łącznik** i w sekcji konfiguracja o nazwie **Konfiguracja** wybierz wszystkie dzienniki aktywności usługi Office 365, które chcesz połączyć z usługą Azure Sentinel. 
   > [!NOTE]
   > Jeśli masz już połączenie wielu dzierżaw w wcześniej obsługiwanej wersji łącznika usługi Office 365 w usłudze Azure Sentinel, będzie można wyświetlać i modyfikować dzienniki, które zbierasz od każdej dzierżawy. Nie będzie można dodać dodatkowych dzierżaw, ale można usunąć wcześniej dodane dzierżawy.
3. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla dzienników usługi Office 365, wyszukaj hasło **OfficeActivity**.


## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć usługę Office 365 z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).

