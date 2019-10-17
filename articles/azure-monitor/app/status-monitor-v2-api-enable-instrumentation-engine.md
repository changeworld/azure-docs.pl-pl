---
title: 'Dokumentacja interfejsu API usługi Azure Application Insights Agent: Włączanie aparatu Instrumentacji | Microsoft Docs'
description: Dokumentacja interfejsu API agenta Application Insights. Enable-InstrumentationEngine. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 9958121c38b96dc9cfa4dda2812fa9ce2b18d785
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388285"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Interfejs API agenta Application Insights: Enable-InstrumentationEngine

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Włącza aparat Instrumentacji przez ustawienie niektórych kluczy rejestru.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

Aparat Instrumentacji może uzupełniać dane zbierane przez zestawy SDK platformy .NET.
Zbiera zdarzenia i komunikaty opisujące wykonywanie procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i [tekst polecenia SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Włącz aparat instrumentacji, jeśli:
- Monitorowanie zostało już włączone za pomocą polecenia cmdlet Enable, ale nie włączono aparatu Instrumentacji.
- Twoja aplikacja została ręcznie przypięta przy użyciu zestawów SDK platformy .NET i chcesz zebrać dodatkową telemetrię.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

> [!NOTE] 
> - To polecenie cmdlet wymaga przejrzenia i zaakceptowania naszych licencji i zasad zachowania poufności informacji.
> - Aparat Instrumentacji dodaje dodatkowe obciążenie i jest domyślnie wyłączony.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry

### <a name="-acceptlicense"></a>-AcceptLicense
**Obowiązkowe.** Ten przełącznik umożliwia zaakceptowanie licencji i zasad zachowania poufności informacji w instalacjach bezobsługowych.

### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Przykładowe dane wyjściowe pomyślnie włączania aparatu Instrumentacji

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Poznaj metryki](../../azure-monitor/app/metrics-explorer.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
- Użyj [analizy](../../azure-monitor/app/analytics.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Utwórz testy sieci Web](monitor-web-app-availability.md) , aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](../../azure-monitor/app/asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.
 
 Zrób więcej dzięki Application Insights agentowi:
 - Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) , aby upewnić się, że Twoje ustawienia zostały poprawnie zarejestrowane.
 - [Pobierz stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
