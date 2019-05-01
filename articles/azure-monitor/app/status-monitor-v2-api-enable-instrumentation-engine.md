---
title: 'Dokumentacja interfejsu API w wersji 2 Monitora stanu platformy Azure: Włączenie Instrumentacji aparatu | Dokumentacja firmy Microsoft'
description: Stan monitora v2 interfejsu API odwołania Enable-InstrumentationEngine. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: 7d337c9b6b22f3abfcb4aea1c47127706ed9e9d7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870510"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Interfejs API w wersji 2 Monitora stanu: Enable-InstrumentationEngine (v0.2.1 alfa)

W tym dokumencie opisano polecenia cmdlet, który jest dostarczany jako członek [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Opis

To polecenie cmdlet spowoduje włączenie aparatu instrumentacji, ustawiając niektórych kluczy rejestru.
Ponowne uruchomienie usług IIS, aby te zmiany zaczęły obowiązywać.

Aparat Instrumentacji można uzupełnić dane zebrane przez zestawy SDK platformy .NET.
Zdarzenia i komunikaty będą zbierane, które opisują wykonywania zarządzanego procesu. W tym między innymi kody wyników programu zależności, zleceń HTTP i tekst polecenia SQL. 

Włącz aparat instrumentacji, jeśli:
- Już zostało włączone monitorowanie za pomocą polecenia cmdlet Enable, ale nie zostały włączone InstrumentationEngine.
- Ręcznie zinstrumentowano aplikację przy użyciu zestawów SDK platformy .NET i chcesz zbierać dodatkowe dane telemetryczne.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

> [!NOTE] 
> To polecenie cmdlet wymaga Przejrzyj i zaakceptuj nasze licencyjne i zasady zachowania poufności.

> [!NOTE] 
> Aparat Instrumentacji dodaje dodatkowe obciążenie i jest domyślnie wyłączona.

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
