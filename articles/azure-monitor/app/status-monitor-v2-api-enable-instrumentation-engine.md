---
title: 'Dokumentacja interfejsu API w wersji 2 usługi Azure Monitor stanu: Włączenie Instrumentacji aparatu | Dokumentacja firmy Microsoft'
description: Dokumentacja interfejsu API w wersji 2 Monitor stanu. Enable-InstrumentationEngine. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami sieci web platformy ASP.NET hostowanej lokalnie, na maszynach wirtualnych lub na platformie Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514380"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Enable-InstrumentationEngine (v0.2.1 alfa)

W tym artykule opisano polecenia cmdlet, które jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Opis

Umożliwia aparatowi instrumentacji, ustawiając niektórych kluczy rejestru.
Ponowne uruchomienie usług IIS, aby zmiany zaczęły obowiązywać.

Aparat Instrumentacji można uzupełnić dane zebrane przez zestawy SDK platformy .NET.
Zbiera zdarzenia i komunikaty, które opisują wykonywania zarządzanego procesu. Te zdarzenia i komunikaty zawierają kody wyników programu zależności, zleceń HTTP i tekst polecenia SQL.

Włącz aparat instrumentacji, jeśli:
- Już zostało włączone monitorowanie za pomocą polecenia cmdlet Enable, ale nie zostały włączone aparatu instrumentacji.
- Ręcznie zinstrumentowano aplikację za pomocą zestawów SDK platformy .NET i chcesz zbierać dodatkowe dane telemetryczne.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

> [!NOTE] 
> - To polecenie cmdlet wymaga Przejrzyj i zaakceptuj nasze licencyjne i zasady zachowania poufności.
> - Aparat Instrumentacji dodaje dodatkowe obciążenie i jest domyślnie wyłączona.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcjonalnie.** Aby zaakceptować umowę licencyjną i zachowania poufności w przypadku instalacji nienadzorowanej, należy użyć tego przełącznika.

### <a name="-verbose"></a>-Verbose
**Typowy parametr.** W danych wyjściowych szczegółowych dzienników, należy użyć tego przełącznika.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Przykładowe dane wyjściowe z pomyślnym włączeniu aparat Instrumentacji

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Kolejne kroki

  Wyświetlanie telemetrii:
 - [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) do monitorowania wydajności i użycia.
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów.
- Użyj [analytics](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wywołania śledzenia.
- [Dodaj zestaw Application Insights SDK do kodu](../../azure-monitor/app/asp-net.md) dzięki czemu można wstawić ślad i rejestrować wywołania.
 
 Wykonuj więcej zadań dzięki v2 Monitora stanu:
 - Użyj zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
 - [Pobierz konfigurację](status-monitor-v2-api-get-config.md) aby upewnić się, że Twoje ustawienia zostały prawidłowo zarejestrowane.
 - [Pobierz stan](status-monitor-v2-api-get-status.md) do inspekcji, monitorowania.
