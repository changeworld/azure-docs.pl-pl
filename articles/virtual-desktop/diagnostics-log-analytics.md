---
title: Analiza dziennika diagnostyki pulpitu wirtualnego systemu Windows — platforma Azure
description: Jak korzystać z analizy dzienników za pomocą funkcji diagnostyki pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127925"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Korzystanie z usługi Log Analytics dla funkcji diagnostycznej

Pulpit wirtualny systemu Windows oferuje funkcję diagnostyczną, która umożliwia administratorowi identyfikowanie problemów za pośrednictwem jednego interfejsu. Ta funkcja rejestruje informacje diagnostyczne za każdym razem, gdy ktoś przypisany do roli pulpitu wirtualnego systemu Windows korzysta z usługi. Każdy dziennik zawiera informacje o roli pulpitu wirtualnego systemu Windows, która była zaangażowana w działanie, wszelkie komunikaty o błędach wyświetlane podczas sesji, informacje o dzierżawie i informacje o użytkowniku. Funkcja diagnostyki tworzy dzienniki aktywności dla akcji użytkownika i administracyjnych. Każdy dziennik aktywności należy do trzech głównych kategorii: 

- Działania związane z subskrypcją pliku danych: gdy użytkownik próbuje połączyć się z kanałem informacyjnym za pośrednictwem aplikacji pulpitu zdalnego firmy Microsoft.
- Działania połączenia: gdy użytkownik próbuje połączyć się z pulpitem lub aplikacją RemoteApp za pośrednictwem aplikacji pulpitu zdalnego firmy Microsoft.
- Działania związane z zarządzaniem: gdy administrator wykonuje operacje zarządzania w systemie, takie jak tworzenie pul hostów, przypisywanie użytkowników do grup aplikacji i tworzenie przypisań ról.

Połączenia, które nie docierają do pulpitu wirtualnego systemu Windows, nie będą wyświetlane w wynikach diagnostyki, ponieważ sama usługa roli diagnostyki jest częścią pulpitu wirtualnego systemu Windows. Problemy z połączeniem pulpitu wirtualnego systemu Windows mogą wystąpić, gdy użytkownik doświadcza problemów z łącznością sieciową.

## <a name="why-you-should-use-log-analytics"></a>Dlaczego warto korzystać z usługi Log Analytics

Zaleca się użycie usługi Log Analytics do analizowania danych diagnostycznych na kliencie platformy Azure, które wykraczają poza rozwiązywanie problemów z jednym użytkownikiem. Jak można ciągnąć liczniki wydajności maszyn wirtualnych do usługi Log Analytics masz jedno narzędzie do zbierania informacji dla wdrożenia.

## <a name="before-you-get-started"></a>Przed rozpoczęciem

Aby można było korzystać z usługi Log Analytics z funkcją diagnostyki, należy [utworzyć obszar roboczy](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

Po utworzeniu obszaru roboczego postępuj zgodnie z instrukcjami w [obszarze Łączenie komputerów z systemem Windows z monitorem Azure,](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) aby uzyskać następujące informacje: 

- Identyfikator obszaru roboczego
- Klucz podstawowy obszaru roboczego

Te informacje będą potrzebne w dalszej części procesu instalacji.

## <a name="push-diagnostics-data-to-your-workspace"></a>Wypychanie danych diagnostycznych do obszaru roboczego 

Dane diagnostyczne z dzierżawy pulpitu wirtualnego systemu Windows można wypchnąć do analizy dzienników dla obszaru roboczego. Tę funkcję można skonfigurować od razu podczas pierwszego tworzenia dzierżawy, łącząc obszar roboczy z dzierżawą lub można ją później skonfigurować w istniejącej dzierżawie.

Aby połączyć dzierżawę z obszarem roboczym usługi Log Analytics podczas konfigurowania nowej dzierżawy, uruchom następujące polecenie cmdlet, aby zalogować się do pulpitu wirtualnego systemu Windows za pomocą konta użytkownika TenantCreator: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Jeśli zamierzasz połączyć istniejącą dzierżawę zamiast nowej dzierżawy, uruchom zamiast tego polecenie cmdlet: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Musisz uruchomić te polecenia cmdlet dla każdej dzierżawy, którą chcesz połączyć z usługi Log Analytics. 

>[!NOTE]
>Jeśli nie chcesz łączyć obszaru roboczego usługi Log Analytics podczas `New-RdsTenant` tworzenia dzierżawy, uruchom polecenie cmdlet. 

## <a name="cadence-for-sending-diagnostic-events"></a>Rytm wysyłania zdarzeń diagnostycznych

Zdarzenia diagnostyczne są wysyłane do usługi Log Analytics po zakończeniu.  

## <a name="example-queries"></a>Przykładowe zapytania

Poniższe przykładowe kwerendy pokazują, jak funkcja diagnostyki generuje raport dla najczęstszych działań w systemie:

W tym pierwszym przykładzie pokazano działania połączeń inicjowane przez użytkowników z obsługiwanymi klientami pulpitu zdalnego:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

W tym następnym przykładzie kwerendy pokazano działania zarządzania przez administratorów na dzierżawców:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Zatrzymywać wysyłanie danych do usługi Log Analytics 

Aby zatrzymać wysyłanie danych z istniejącej dzierżawy do usługi Log Analytics, uruchom następujące polecenie cmdlet i ustaw puste ciągi:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

Musisz uruchomić to polecenie cmdlet dla każdej dzierżawy, z której chcesz zatrzymać wysyłanie danych. 

## <a name="next-steps"></a>Następne kroki 

Aby przejrzeć typowe scenariusze błędów, które funkcja diagnostyki może zidentyfikować dla Ciebie, zobacz [Identyfikowanie i diagnozowanie problemów](diagnostics-role-service.md#common-error-scenarios).
