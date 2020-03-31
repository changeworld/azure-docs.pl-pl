---
title: Integruj dzienniki z programem ArcSight za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak zintegrować dzienniki usługi Azure Active Directory z usługą ArcSight przy użyciu usługi Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05002c1b11ef31b61fb4036f09dc8edcdafca767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75608384"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrowanie dzienników usługi Azure Active Directory z usługą ArcSight przy użyciu usługi Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) to rozwiązanie do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM), które pomaga wykrywać zagrożenia bezpieczeństwa na platformie i reagować na nie. Teraz można kierować dzienniki usługi Azure Active Directory (Azure AD) do usługi ArcSight przy użyciu usługi Azure Monitor przy użyciu łącznika ArcSight dla usługi Azure AD. Ta funkcja umożliwia monitorowanie dzierżawy pod kątem naruszenia zabezpieczeń przy użyciu funkcji ArcSight.  

W tym artykule dowiesz się, jak rozsyłać dzienniki usługi Azure AD do usługi ArcSight przy użyciu usługi Azure Monitor. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Centrum zdarzeń platformy Azure zawierające dzienniki aktywności usługi Azure AD. Dowiedz się, jak [przesyłać strumieniowo dzienniki aktywności do centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Skonfigurowane wystąpienie modułu przejmowania demona SmartConnector ArcSight NG (SmartConnector) lub modułu równoważenia obciążenia ArcSight. Jeśli zdarzenia są wysyłane do modułu równoważenia obciążenia ArcSight, są one w związku z tym wysyłane do SmartConnector przez moduł równoważenia obciążenia.

Pobierz i otwórz [przewodnik konfiguracji usługi ArcSight SmartConnector for Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Ten przewodnik zawiera kroki, które należy zainstalować i skonfigurować system ArcSight SmartConnector for Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrowanie dzienników usługi Azure AD z usługą ArcSight

1. Najpierw wykonaj kroki opisane w sekcji **Wymagania wstępne** przewodnika konfiguracji. Ta sekcja zawiera następujące kroki:
    * Ustaw uprawnienia użytkownika na platformie Azure, aby upewnić się, że istnieje użytkownik z rolą **właściciela** do wdrożenia i skonfigurowania łącznika.
    * Otwórz porty na serwerze za pomocą narzędzia Syslog NG Daemon SmartConnector, dzięki czemu jest on dostępny z platformy Azure. 
    * Wdrożenie uruchamia skrypt programu Windows PowerShell, więc należy włączyć program PowerShell do uruchamiania skryptów na komputerze, na którym chcesz wdrożyć łącznik.

2. Postępuj zgodnie z instrukcjami w sekcji **Wdrażanie łącznika** w przewodniku konfiguracji, aby wdrożyć łącznik. W tej sekcji opisano sposób pobierania i wyodrębniania łącznika, konfigurowania właściwości aplikacji i uruchamiania skryptu wdrażania z wyodrębnianych folderów. 

3. Skorzystaj z kroków **w weryfikacji wdrożenia na platformie Azure,** aby upewnić się, że łącznik jest skonfigurowany i działa poprawnie. Sprawdź następujące informacje:
    * Wymagane funkcje platformy Azure są tworzone w ramach subskrypcji platformy Azure.
    * Dzienniki usługi Azure AD są przesyłane strumieniowo do właściwego miejsca docelowego. 
    * Ustawienia aplikacji z wdrożenia są zachowywane w ustawieniach aplikacji w usłudze Azure Function Apps. 
    * Nowa grupa zasobów dla arcsight jest tworzony na platformie Azure, z aplikacją usługi Azure AD dla łącznika ArcSight i kont magazynu zawierających mapowane pliki w formacie CEF.

4. Na koniec wykonaj kroki po wdrożeniu w **konfiguracji po wdrożeniu** przewodnika konfiguracji. W tej sekcji wyjaśniono, jak wykonać dodatkową konfigurację, jeśli korzystasz z planu usługi aplikacji, aby zapobiec bezczynności aplikacji funkcji po upływie limitu czasu, skonfigurować przesyłanie strumieniowe dzienników diagnostycznych z centrum zdarzeń i zaktualizować demona SysLog NG Certyfikat magazynu kluczy SmartConnector do skojarzenia go z nowo utworzonym kontem magazynu.

5. W przewodniku konfiguracji wyjaśniono również, jak dostosować właściwości łącznika na platformie Azure oraz jak uaktualnić i odinstalować łącznik. Istnieje również sekcja dotycząca poprawy wydajności, w tym uaktualniania do [planu użycia platformy Azure](https://azure.microsoft.com/pricing/details/functions) i konfigurowania modułu równoważenia obciążenia ArcSight, jeśli obciążenie zdarzenia jest większe niż to, co może obsłużyć pojedynczy moduł demona SmartConnector Syslog NG.

## <a name="next-steps"></a>Następne kroki

[Przewodnik konfiguracji rozwiązania ArcSight SmartConnector for Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
