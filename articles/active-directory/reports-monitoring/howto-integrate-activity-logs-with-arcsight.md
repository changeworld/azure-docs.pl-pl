---
title: Integrowanie dzienników z usługą ArcSight przy użyciu Azure Monitor | Microsoft Docs
description: Dowiedz się, jak zintegrować dzienniki Azure Active Directory z usługą ArcSight przy użyciu Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037240e6006b5e0625bf8fb75492d8e458899a8e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820884"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrowanie dzienników Azure Active Directory z usługą ArcSight przy użyciu Azure Monitor

[ArcSight Micro Focus](https://software.microfocus.com/products/siem-security-information-event-management/overview) to rozwiązanie do zarządzania informacjami i zdarzeniami zabezpieczeń (Siem), które ułatwia wykrywanie zagrożeń bezpieczeństwa i reagowanie na nie na platformie. Teraz możesz kierować dzienniki usługi Azure Active Directory (Azure AD), aby ArcSight użycie Azure Monitor przy użyciu łącznika ArcSight dla usługi Azure AD. Ta funkcja umożliwia monitorowanie dzierżawy pod kątem naruszenia zabezpieczeń przy użyciu ArcSight.  

W tym artykule dowiesz się, jak kierować dzienniki usługi Azure AD do ArcSight przy użyciu Azure Monitor. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać tej funkcji, potrzebujesz następujących elementów:
* Centrum zdarzeń platformy Azure, które zawiera dzienniki aktywności usługi Azure AD. Dowiedz się, jak [przesyłać strumieniowo dzienniki aktywności do centrum zdarzeń](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Skonfigurowane wystąpienie ArcSight dziennika systemu SmartConnector demona (SmartConnector) lub ArcSight Load Balancer. Jeśli zdarzenia są wysyłane do ArcSight Load Balancer, są w związku z tym wysyłane do SmartConnector przez Load Balancer.

Pobierz i Otwórz [Przewodnik konfiguracji dla usługi ArcSight SmartConnector dla Azure monitor centrum zdarzeń](https://community.microfocus.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/8/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf). Ten przewodnik zawiera kroki, które należy wykonać, aby zainstalować i skonfigurować ArcSight SmartConnector dla Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrowanie dzienników usługi Azure AD z usługą ArcSight

1. Najpierw wykonaj kroki opisane w sekcji **wymagania wstępne** przewodnika konfiguracji. Ta sekcja obejmuje następujące kroki:
    * Ustaw uprawnienia użytkownika na platformie Azure, aby upewnić się, że użytkownik ma rolę **właściciela** do wdrożenia i skonfigurowania łącznika.
    * Otwórz porty na serwerze za pomocą demona dziennika systemu w systemie SmartConnector, aby uzyskać dostęp z platformy Azure. 
    * Wdrożenie uruchamia skrypt programu Windows PowerShell, dlatego należy włączyć uruchamianie skryptów programu PowerShell na komputerze, na którym ma zostać wdrożony łącznik.

2. Postępuj zgodnie z instrukcjami w sekcji **wdrażanie łącznika** przewodnika po konfiguracji, aby wdrożyć łącznik. W tej sekcji omówiono sposób pobierania i wyodrębniania łącznika, konfigurowania właściwości aplikacji i uruchamiania skryptu wdrażania z wyodrębnionego folderu. 

3. Wykonaj kroki opisane w sekcji **Weryfikowanie wdrożenia na platformie Azure** , aby upewnić się, że łącznik został skonfigurowany i działa prawidłowo. Sprawdź następujące kwestie:
    * Wymagane funkcje platformy Azure są tworzone w ramach subskrypcji platformy Azure.
    * Dzienniki usługi Azure AD są przesyłane strumieniowo do poprawnej lokalizacji docelowej. 
    * Ustawienia aplikacji z wdrożenia są utrwalane w ustawieniach aplikacji w aplikacjach funkcji platformy Azure. 
    * Nowa grupa zasobów dla ArcSight jest tworzona na platformie Azure z aplikacją usługi Azure AD dla łącznika ArcSight i kont magazynu zawierających zamapowane pliki w formacie CEF.

4. Na koniec wykonaj kroki po wdrożeniu w **konfiguracjach po wdrożeniu** przewodnika konfiguracji. W tej sekcji wyjaśniono, jak przeprowadzić dodatkową konfigurację, jeśli korzystasz z planu App Service, aby zapobiec przejściu aplikacji funkcji po upływie limitu czasu, skonfigurować przesyłanie strumieniowe dzienników diagnostycznych z centrum zdarzeń i zaktualizować demona dziennika systemu w systemie. SmartConnector certyfikat magazynu kluczy, aby skojarzyć go z nowo utworzonym kontem magazynu.

5. W przewodniku konfiguracji wyjaśniono również, jak dostosować właściwości łącznika na platformie Azure oraz jak uaktualnić i odinstalować łącznik. Znajduje się w nim także sekcja ulepszenia wydajności, w tym uaktualnienie do [planu zużycia na platformie Azure](https://azure.microsoft.com/pricing/details/functions) i konfigurowanie ArcSight Load Balancer, jeśli obciążenie zdarzenia jest większe niż to, co może obsłużyć jeden demon w systemie SmartConnector.

## <a name="next-steps"></a>Następne kroki

[Przewodnik konfiguracji programu ArcSight SmartConnector dla centrum zdarzeń Azure Monitor](https://community.softwaregrp.com/dcvta86296/attachments/dcvta86296/connector-documentation/1232/2/Microsoft%20Azure%20Monitor%20Event%20Hub.pdf)
