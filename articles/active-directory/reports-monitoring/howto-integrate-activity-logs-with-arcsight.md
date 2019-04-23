---
title: Jak zintegrować dzienniki usługi Azure Active Directory z usługą ArcSight przy użyciu usługi Azure Monitor | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 08a265637274f396497da37706391bf44e0c9107
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996310"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrowanie dzienników usługi Azure Active Directory za pomocą ArcSight przy użyciu usługi Azure Monitor

[Micro ArcSight fokus](https://software.microfocus.com/products/siem-security-information-event-management/overview) się informacjami i zdarzeniami (SIEM) rozwiązania do zarządzania zabezpieczeniami, która ułatwia wykrywanie i reagowanie na zagrożenia bezpieczeństwa w danej platformy. Dzienniki usługi Azure Active Directory (Azure AD) mogą teraz kierować do ArcSight przy użyciu usługi Azure Monitor, za pomocą łącznika ArcSight dla usługi Azure AD. Ta funkcja umożliwia monitorowanie złamania zabezpieczeń przy użyciu ArcSight w dzierżawie.  

W tym artykule dowiesz się, jak kierować dzienniki usługi Azure AD do ArcSight przy użyciu usługi Azure Monitor. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Dzienniki Centrum zdarzeń platformy Azure, który zawiera działania usługi Azure AD. Dowiedz się, jak [przesyłanie strumieniowe dzienników aktywności do Centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Skonfigurowane wystąpienie ArcSight Syslog NG demona SmartConnector (SmartConnector) lub ArcSight równoważenia obciążenia. Jeśli zdarzenia są wysyłane do modułu równoważenia obciążenia ArcSight, w związku z tym wysłaniem ich do SmartConnector przez moduł równoważenia obciążenia.

Pobierz i Otwórz [Przewodnik po konfiguracji dla ArcSight SmartConnector dla Centrum zdarzeń usługi Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Ten przewodnik zawiera kroki, które są potrzebne do zainstalowania i skonfigurowania ArcSight SmartConnector dla usługi Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrowanie dzienników usługi Azure AD za pomocą ArcSight

1. Najpierw wykonaj kroki **wymagania wstępne** części przewodnika konfiguracji. Ta sekcja obejmuje następujące kroki:
    * Ustaw uprawnienia użytkownika na platformie Azure, aby upewnić się, ma użytkownika z **właściciela** roli można wdrożyć i skonfigurować łącznik.
    * Otwieranie portów na tym serwerze Syslog NG demona SmartConnector, dzięki czemu jest ona dostępna na platformie Azure. 
    * Wdrożenie uruchamia skrypt programu Windows PowerShell, więc należy włączyć programu PowerShell do uruchamiania skryptów na maszynie, w której chcesz wdrożyć łącznik.

2. Postępuj zgodnie z instrukcjami w **wdrażanie łącznika** części przewodnika konfiguracji w celu wdrożenia łącznika. W tej sekcji przedstawiono sposób pobierania i Wyodrębnij łącznika, konfigurowania właściwości aplikacji i uruchom skrypt wdrażania z wyodrębnionego folderu. 

3. Użyj kroków w **weryfikacji wdrożenia na platformie Azure** aby upewnić się, że łącznik jest skonfigurowany i działa poprawnie. Sprawdź następujące informacje:
    * Wymagania usługi Azure functions są tworzone w ramach subskrypcji platformy Azure.
    * Dzienniki usługi Azure AD są przesyłane strumieniowo do odpowiedniego miejsca docelowego. 
    * Ustawienia aplikacji w danym wdrożeniu są utrwalane w ustawieniach aplikacji w aplikacji funkcji platformy Azure. 
    * Nową grupę zasobów dla ArcSight powstaje na platformie Azure za pomocą aplikacji usługi Azure AD dla łącznika ArcSight i kont magazynu zawierającego pliki mapowane w formacie CEF.

4. Na koniec wykonaj po wdrożeniu kroki w **konfiguracji po wdrożeniu** przewodnika konfiguracji. W tej sekcji wyjaśniono, jak wykonać dodatkowe czynności konfiguracyjne, jeśli użytkownik pracuje na Plan usługi App Service uniemożliwić aplikacji funkcji przechodząc bezczynności, po upływie limitu czasu, konfigurowania, przesyłania strumieniowego dzienników diagnostycznych z Centrum zdarzeń i zaktualizować NG demona usługi SysLog SmartConnector certyfikat magazynu kluczy, aby skojarzyć ją z nowo utworzone konto magazynu.

5. Przewodnik po konfiguracji wyjaśniono również, jak dostosować właściwości łącznika na platformie Azure oraz sposób uaktualniania i odinstalowywania łącznika. Istnieje również sekcja na ulepszenia wydajności, w tym uaktualniania do [planu zużycie Azure](https://azure.microsoft.com/pricing/details/functions) i Konfigurowanie modułu równoważenia obciążenia ArcSight, jeśli obciążenie zdarzeniami jest większa niż pojedynczy SmartConnector demona usługi Syslog NG można dojście.

## <a name="next-steps"></a>Kolejne kroki

[Przewodnik po konfiguracji dla ArcSight SmartConnector dla Centrum zdarzeń usługi Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
