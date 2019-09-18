---
title: 'Dokumentacja interfejsu API platformy Azure monitor stanu v2: Wyłącz aparat Instrumentacji | Microsoft Docs'
description: Dokumentacja interfejsu API monitor stanu v2. Disable-InstrumentationEngine. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
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
ms.openlocfilehash: 98f70a457b1e09f755a854a28cfd31a8fc9ee9f2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033174"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine"></a>Interfejs API monitor stanu v2: Disable-InstrumentationEngine

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis
Wyłącza aparat instrumentacji, usuwając niektóre klucze rejestru.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry 

### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Przykładowe dane wyjściowe pomyślnie wyłączające aparat Instrumentacji

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Następne kroki

 Więcej informacji o monitor stanu v2:
 - Skorzystaj z naszego przewodnika, aby [rozwiązywać problemy z](status-monitor-v2-troubleshoot.md) Monitor stanu v2.
