---
title: Integracja rozwiązania monitorowania zewnętrznych z stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować stosu Azure z zewnętrznego rozwiązanie monitorowania w centrum danych.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807343"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integracja rozwiązania monitorowania zewnętrznych z Azure stosu

Dla zewnętrznych monitorowania infrastruktury stosu Azure, należy monitorować oprogramowania stosu Azure, komputery fizyczne i przełączniki sieci fizycznej. Każdy z tych obszarów udostępnia metody można pobrać informacji o kondycji i alertów:

- Oprogramowanie stosu Azure oferuje opartego na interfejsie REST interfejsu API można pobrać kondycji i alertów. Korzystanie z technologii zdefiniowanych przez oprogramowanie, takie jak bezpośrednie miejsca do magazynowania, kondycję magazynu i alerty są częścią monitorowania oprogramowania.
- Komputerów fizycznych można udostępnić kondycji i informacji o alertach za pomocą kontrolerów zarządzania płytą główną (BMC).
- Fizyczne urządzenia sieciowe można udostępnić kondycji i informacji o alertach za pośrednictwem protokołu SNMP.

Każde rozwiązanie stosu Azure jest dostarczany z hostem cyklu życia sprzętu. Ten host uruchamia monitorowania oprogramowania dostawcy sprzętu Original Equipment Manufacturer (OEM) dla serwerów fizycznych i urządzeń sieciowych. W razie potrzeby można pominąć te rozwiązania monitorowania i bezpośrednio zintegrować z istniejącymi rozwiązaniami do monitorowania w centrum danych.

> [!IMPORTANT]
> Zewnętrznych rozwiązanie monitorowania, którego używasz, musi być bez wykorzystania agentów. Nie można zainstalować agentów innych firm wewnątrz składniki stosu Azure.

Na poniższym diagramie przedstawiono przepływ ruchu między systemu Azure stosu zintegrowane, hosta cyklu życia sprzętu zewnętrznych rozwiązanie monitorowania i system biletów/danych zewnętrznych kolekcji.

![Diagram przedstawiający ruchu między Azure stosu, monitorowania i wystawiania biletów rozwiązania.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

W tym artykule wyjaśniono, jak zintegrować stosu Azure z zewnętrznego monitorowania rozwiązań, takich jak System Center Operations Manager i Nagios. Zawiera również sposób pracy z alertami programowo przy użyciu programu PowerShell lub za pośrednictwem interfejsu API REST.

## <a name="integrate-with-operations-manager"></a>Integracja z programem Operations Manager

Operations Manager służy do monitorowania zewnętrznych stosu Azure. Pakiet administracyjny System Center dla programu Microsoft Azure stosu umożliwia monitorowanie wielu wdrożeń stosu Azure przy użyciu pojedynczego wystąpienia programu Operations Manager. Pakiet zarządzania korzysta z dostawcy zasobów kondycji i dostawcy zasobów aktualizacji interfejsów API REST do komunikowania się z stosu Azure. Jeśli planujesz obejścia OEM monitorowania oprogramowania, które jest uruchomiona na hoście cyklu życia sprzętu, należy zainstalować pakiety administracyjne dostawcy do monitorowania serwerów fizycznych. Aby monitorować przełączników sieciowych umożliwia także odnajdywania urządzeń sieciowych programu Operations Manager.

Pakiet administracyjny dla stosu Azure oferuje następujące możliwości:

- Możesz zarządzać wielu wdrożeń Azure stosu
- Brak obsługi dla usługi Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS)
- Możesz pobrać i Zamknij alerty
- Brak kondycję i wydajność pulpit nawigacyjny
- Obejmuje wykrywania automatycznego trybu konserwacji dla gdy poprawek i aktualizacji (P & U) jest w toku
- Obejmuje zadania Wymuszanie aktualizacji do wdrożenia i regionu
- Możesz dodawać niestandardowe informacje w regionie
- Obsługa powiadomień i raportowanie

Możesz pobrać pakiet administracyjny programu System Center dla programu Microsoft Azure stosu oraz skojarzonych z nimi [Podręcznik użytkownika](https://www.microsoft.com/en-us/download/details.aspx?id=55184), lub bezpośrednio z programu Operations Manager.

W przypadku obsługi biletów rozwiązania można zintegrować programu Operations Manager z programem System Center Service Manager. Łącznik produktu zintegrowane umożliwia komunikację dwukierunkową, który umożliwia zamknięcie alertu w stosie Azure a programem Operations Manager po rozwiązaniu żądania obsługi w programie Service Manager.

Na poniższym diagramie przedstawiono integracji stosu Azure z istniejącego wdrożenia programu System Center. Można zautomatyzować programu Service Manager dalsze z programu System Center Orchestrator lub automatyzacji zarządzania usługi (SMA) do uruchamiania operacji w stosie Azure.

![Diagram przedstawiający integrację z modelu obiektów programu Service Manager i SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integracja z Nagios

Nagios, monitorowanie wtyczki opracowano wraz z rozwiązań Cloudbase partnerskich, który jest dostępny w ramach licencji ograniczająca bezpłatnego oprogramowania — MIT (Massachusetts Institute of Technology).

Wtyczka jest napisany w języku Python i wykorzystuje dostawcy zasobów kondycji interfejsu API REST. Zapewnia podstawowe funkcje do pobierania i Zamknij alerty w stosie Azure. Podobnie jak pakiet administracyjny programu System Center umożliwia dodawanie wielu wdrożeń stosu Azure oraz wysyłać powiadomienia.

Wtyczka współpracuje z Nagios przedsiębiorstwa i Nagios podstawowe. Można go pobrać [tutaj](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Pobieranie zawiera także szczegóły instalacji i konfiguracji.

### <a name="plugin-parameters"></a>Parametry wtyczki

Konfigurowanie pliku wtyczki "Azurestack_plugin.py" z następującymi parametrami:

| Parametr | Opis | Przykład |
|---------|---------|---------|
| *arm_endpoint* | Punktu końcowego platformy Azure Resource Manager (administrator) |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Punktu końcowego platformy Azure Resource Manager (administrator)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Identyfikator subskrypcji administratora | Pobrać za pomocą portalu administratora lub programu PowerShell |
| *nazwa_użytkownika* | Nazwa użytkownika subskrypcji — operator | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Hasło subskrypcji — operator | mojehasło |
| *Client_id* | Klient | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nazwa regionu platformy Azure stosu | lokalne |
|  |  |

* PowerShell identyfikator GUID, który jest dostarczany jest uniwersalny. Służy on do każdego wdrożenia.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Monitor kondycji i alerty przy użyciu programu PowerShell

Jeśli nie używasz programu Operations Manager, Nagios lub rozwiązanie oparte na Nagios, można włączyć szeroki zakres monitorowania rozwiązania do integracji z stosu Azure za pomocą programu PowerShell.

1. Przy użyciu programu PowerShell, upewnij się, że masz [PowerShell zainstalowany i skonfigurowany](azure-stack-powershell-configure-quickstart.md) dla środowiska operator stosu Azure. Instalowanie programu PowerShell na komputerze lokalnym, który można osiągnąć punktu końcowego Menedżera zasobów (administrator) (https://adminmanagement. [ Region]. [External_FQDN]).

2. Uruchom następujące polecenia, aby Połącz ze środowiskiem Azure stosu jako operator Azure stosu:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Użyj polecenia takie jak następujące przykłady do pracy z alertami:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Dowiedz się więcej

Informacje o monitorowaniu kondycji wbudowanych, zobacz [monitorowania kondycji i alertów w stosie Azure](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Kolejne kroki

[Integracja z zabezpieczeniami](azure-stack-integrate-security.md)
