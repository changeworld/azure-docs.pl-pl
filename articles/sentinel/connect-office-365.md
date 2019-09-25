---
title: Łączenie danych pakietu Office 365 z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane pakietu Office 365 z platformą Azure — wskaźnikiem.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 481acfdccb54077f18c1f4bf0375c0dc66e66007
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240663"
---
# <a name="connect-data-from-office-365-logs"></a>Łączenie danych z dzienników pakietu Office 365



Dzienniki inspekcji z [pakietu Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia. Można przesyłać strumieniowo dzienniki inspekcji z wielu dzierżawców do jednego obszaru roboczego na platformie Azure. Łącznik dziennika aktywności pakietu Office 365 zapewnia wgląd w bieżące działania użytkownika. Zostaną wyświetlone informacje o różnych akcjach użytkownika, administratora, systemu i zasad oraz zdarzeniach z pakietu Office 365. Łącząc dzienniki pakietu Office 365 z platformą Azure, możesz użyć tych danych do wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania procesu badania.

> [!IMPORTANT]
> Jeśli masz licencję E3, zanim będzie można uzyskać dostęp do danych za pomocą interfejsu API działania zarządzania pakietu Office 365, musisz włączyć ujednolicone rejestrowanie inspekcji dla organizacji pakietu Office 365. W tym celu należy włączyć dziennik inspekcji pakietu Office 365. Aby uzyskać instrukcje, zobacz [Włączanie lub wyłączanie wyszukiwania w dzienniku inspekcji pakietu Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API działania zarządzania pakietu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w swojej dzierżawie
- Na komputerze, z którego zalogowano się do punktu kontrolnego platformy Azure, aby utworzyć połączenie, upewnij się, że port 4433 jest otwarty dla ruchu w sieci Web. Ten port można zamknąć ponownie po pomyślnym nawiązaniu połączenia.
- Jeśli dzierżawa nie ma licencji pakietu Office 365 E3 lub pakietu Office 365 E5, należy włączyć ujednoliconą inspekcję w dzierżawie przy użyciu jednego z następujących procesów:
    - [Za pomocą polecenia cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) i włączania parametru "UnifiedAuditLogIngestionEnabled").
    - [Lub przy użyciu interfejsu użytkownika Centrum zabezpieczeń i zgodności](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Łączenie z usługą Office 365

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Office 365** .

2. Jeśli nie zostało to jeszcze włączone, w obszarze **połączenie** Użyj przycisku **Włącz** , aby włączyć rozwiązanie pakietu Office 365. Jeśli została już włączona, zostanie ona zidentyfikowana na ekranie połączenia, ponieważ jest już włączona.
1. Pakiet Office 365 umożliwia przesyłanie strumieniowe danych z wielu dzierżawców do usługi Azure wskaźnikowej. Dla każdej dzierżawy, z którą chcesz nawiązać połączenie, Dodaj dzierżawcę w obszarze **łączenie dzierżawców z platformą Azure**. 
1. Zostanie otwarty ekran Active Directory. Zostanie wyświetlony monit o uwierzytelnienie z uprawnieniami administratora globalnego dla każdej dzierżawy, którą chcesz połączyć z usługą Azure wskaźnikiem, i podawanie uprawnień do usługi Azure wskaźnikowej na potrzeby odczytywania dzienników. 
5. W obszarze strumienie dzienników aktywności pakietu Office 365 kliknij pozycję **Wybierz** , aby wybrać, które typy dzienników mają być przesyłane strumieniowo do usługi Azure wskaźnikowej. Obecnie platforma Azure — Wskaźnikowanie obsługuje programy Exchange i SharePoint.

4. Kliknij przycisk **Zastosuj zmiany**.

3. Aby użyć odpowiedniego schematu w Log Analytics dla dzienników pakietu Office 365, Wyszukaj pozycję **Office**.


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia między pakietem Office 365 a wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

