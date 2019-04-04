---
title: Integracja zewnętrznej rozwiązania do monitorowania za pomocą usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zintegrować usługę Azure Stack z zewnętrznego rozwiązania do monitorowania w centrum danych.
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
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 520319fb21dce3cf4f3cc1b36c52657cf9eb24e7
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904002"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integracja zewnętrznej rozwiązania do monitorowania za pomocą usługi Azure Stack

Dla zewnętrznych monitorowania infrastruktury Azure Stack, należy monitorować oprogramowania Azure Stack, komputery fizyczne i przełączniki sieci fizycznej. Każda z tych obszarów udostępnia metodę, aby pobrać informacje o kondycji i alertów:

- Oprogramowania platformy Azure Stack oferuje interfejs API oparty na protokole REST do pobierania kondycji i alertów. Korzystanie z technologii zdefiniowanych przez oprogramowanie, takich jak bezpośrednimi miejscami do magazynowania magazynu kondycji i alerty są częścią oprogramowania monitorowania.
- Komputerów fizycznych można udostępnić kondycji i informacje o alertach za pomocą kontrolerów zarządzania płytą główną (BMC).
- Fizyczne urządzenia sieciowe można udostępnić kondycji i informacje o alertach za pośrednictwem protokołu SNMP.

Każde z tych rozwiązań usługi Azure Stack jest dostarczany z cyklem życia sprzętu hosta. Ten host uruchamia monitorujący oprogramowanie dostawcy sprzętu Original Equipment Manufacturer (OEM) dla serwerów fizycznych i urządzeniami sieciowymi. Jeśli to konieczne, można pominąć te rozwiązania do monitorowania i bezpośrednio integrować z istniejącymi rozwiązaniami monitorowania w centrum danych.

> [!IMPORTANT]
> Zewnętrzne rozwiązanie monitorowania, którego używasz, musi być bez wykorzystania agentów. Nie można zainstalować agentów firm wewnątrz składników usługi Azure Stack.

Na poniższym diagramie przedstawiono przepływ ruchu między zintegrowanym systemie Azure Stack, sprzętu hosta cyklu życia, rozwiązanie do monitorowania zewnętrznych i systemem tworzenia biletów/dane zewnętrzne kolekcji.

![Diagram przedstawiający ruchu między usługi Azure Stack, monitorowania i tworzenia biletów rozwiązania.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Zewnętrzne integracji monitorowania bezpośrednio z fizycznych serwerów i urządzeń sieciowych nie jest dozwolone i aktywnie zablokowane przez listy kontroli dostępu (ACL). 

W tym artykule wyjaśniono, jak zintegrować usługę Azure Stack z zewnętrznego monitorowania rozwiązań, takich jak System Center Operations Manager i Nagios. Obejmuje to również sposób pracy z alertami programowo przy użyciu programu PowerShell lub za pomocą wywołań interfejsu API REST.

## <a name="integrate-with-operations-manager"></a>Integracja z programem Operations Manager

Za pomocą programu Operations Manager do monitorowania zewnętrznych usługi Azure Stack. Pakiet administracyjny System Center dla usługi Microsoft Azure Stack umożliwia monitorowanie wielu wdrożeń będących częścią usługi Azure Stack przy użyciu jednego wystąpienia programu Operations Manager. Pakiet administracyjny używa dostawcy zasobów kondycji i dostawcy zasobów aktualizacji interfejsów API REST do komunikowania się z usługą Azure Stack. Jeśli planujesz obejścia OEM monitorowaniem oprogramowania, która została uruchomiona na hoście cyklu życia sprzętu, można zainstalować pakiety administracyjne dostawcy do monitorowania serwerów fizycznych. Odnajdywanie urządzeń sieciowych programu Operations Manager umożliwia również monitorowanie przełączników sieciowych.

Pakiet administracyjny dla usługi Azure Stack oferuje następujące możliwości:

- Można zarządzać wieloma wdrożeniami usługi Azure Stack
- Brak pomocy technicznej dla usługi Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS)
- Można pobierać i Zamknij alerty
- Brak kondycji i wydajności pulpit nawigacyjny
- Obejmuje wykrywania automatycznego trybu konserwacji dla gdy poprawek i aktualizacji (P & U) jest w toku
- Obejmuje Wymuszanie aktualizacji zadania dotyczące wdrażania i region
- Możesz dodawać niestandardowe informacje do regionu
- Obsługuje powiadamianie i raportowanie

Możesz pobrać pakiet administracyjny programu System Center dla usługi Microsoft Azure Stack oraz skojarzonych z nimi [Podręcznik użytkownika](https://www.microsoft.com/en-us/download/details.aspx?id=55184), lub bezpośrednio z programu Operations Manager.

W przypadku obsługi biletów rozwiązania można zintegrować programu Operations Manager z programem System Center Service Manager. Łącznik produktu zintegrowane umożliwia komunikację dwukierunkową, która umożliwia zamknięcie alertu w usłudze Azure Stack oraz programu Operations Manager, po rozwiązaniu żądania obsługi w programie Service Manager.

Na poniższym diagramie przedstawiono integracji usługi Azure Stack z istniejącego wdrożenia programu System Center. Można zautomatyzować programu Service Manager dalsze za pomocą programu System Center Orchestrator lub Service Management Automation (SMA) umożliwiające uruchamianie operacji w usłudze Azure Stack.

![Diagram przedstawiający integrację z modelu obiektów programu Service Manager i programu SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integracja z usługą Nagios

Nagios, monitorowanie wtyczki opracowano wraz z rozwiązania Cloudbase partnera, który jest dostępny w ramach licencji ograniczająca bezpłatne oprogramowanie — MIT (Massachusetts Institute of Technology).

Wtyczka jest napisany w języku Python i wykorzystuje interfejs API REST dostawcy zasobów kondycji. Oferuje podstawowe funkcje, aby pobrać i Zamknij alerty w usłudze Azure Stack. Pakiet administracyjny programu System Center, np. pozwala ona dodawać wielu wdrożeń będących częścią usługi Azure Stack i wysyłania powiadomień.

Wtyczka działa w Nagios przedsiębiorstwa i Nagios Core. Możesz ją pobrać [tutaj](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Witryna pobierania zawiera także szczegóły instalacji i konfiguracji.

### <a name="plugin-parameters"></a>Parametry wtyczki

Skonfiguruj plik wtyczki "Azurestack_plugin.py" z następującymi parametrami:

| Parametr | Opis | Przykład |
|---------|---------|---------|
| *arm_endpoint* | Punkt końcowy usługi Azure Resource Manager (administrator) | https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Punkt końcowy usługi Azure Resource Manager (administrator)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Identyfikator subskrypcji administratora | Pobieranie za pośrednictwem portalu administratora lub programu PowerShell |
| *Nazwa_użytkownika* | Nazwa operatora subskrypcji użytkownika | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Hasła subskrypcji — operator | mojehasło |
| *Client_id* | Klient | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nazwa regionu w usłudze Azure Stack | lokalne |
|  |  |

* PowerShell identyfikator GUID, który jest dostarczany jest uniwersalnym. Służy on do każdego wdrożenia.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Monitorowanie kondycji i alertów za pomocą programu PowerShell

Jeśli nie używasz programu Operations Manager, Nagios lub rozwiązaniem opartym na Nagios, można użyć programu PowerShell umożliwiające szerokiej gamy monitorowanie rozwiązań do integracji z usługą Azure Stack.

1. Przy użyciu programu PowerShell, upewnij się, że masz [PowerShell zainstalowaną i skonfigurowaną](azure-stack-powershell-configure-quickstart.md) dla środowiska operatora usługi Azure Stack. Instalowanie programu PowerShell na komputerze lokalnym, który można osiągnąć punktu końcowego usługi Resource Manager (administrator) (https://adminmanagement. [ Region]. [External_FQDN]).

2. Uruchom następujące polecenia, aby połączyć się z środowiskiem usługi Azure Stack jako operatorów usługi Azure Stack:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Użyj poleceń, takich jak poniższe przykłady do pracy z alertami:
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Dowiedz się więcej

Aby uzyskać informacji na temat monitorowania kondycji wbudowanych, zobacz [monitorowania kondycji i alertów w usłudze Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Kolejne kroki

[Integracja z zabezpieczeniami](azure-stack-integrate-security.md)
