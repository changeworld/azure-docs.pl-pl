---
title: Konfiguracja stanu — omówienie
description: Omówienie konfiguracji stanu automatyzacji platformy Azure (DSC), jej warunków i znanych problemów
keywords: powershell dsc, konfiguracja żądanego stanu, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 02e540c9ac2654be97ff247749d1ed18573cfc24
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010260"
---
# <a name="state-configuration-overview"></a>Konfiguracja stanu — omówienie

Konfiguracja stanu automatyzacji platformy Azure to usługa platformy Azure, która umożliwia [pisanie,](/powershell/scripting/dsc/configurations/configurations)zarządzanie i kompilowanie konfiguracji konfiguracji żądanego stanu programu PowerShell (DSC). Usługa importuje również [zasoby DSC](/powershell/scripting/dsc/resources/resources)i przypisuje konfiguracje do węzłów docelowych, wszystkie w chmurze.

## <a name="why-use-azure-automation-state-configuration"></a>Dlaczego warto korzystać z konfiguracji stanu automatyzacji platformy Azure

Konfiguracja stanu automatyzacji platformy Azure zapewnia kilka zalet w stosunku do korzystania z dsc poza platformą Azure.

### <a name="built-in-pull-server"></a>Wbudowany serwer ściągania

Konfiguracja stanu automatyzacji platformy Azure udostępnia serwer ściągania DSC podobny do [usługi DSC funkcji systemu Windows.](/powershell/scripting/dsc/pull-server/pullserver) Węzły docelowe mogą automatycznie odbierać konfiguracje, być zgodne z żądanym stanem i raportować ich zgodność. Wbudowany serwer ściągania w usłudze Azure Automation eliminuje konieczność konfigurowania i utrzymywania własnego serwera ściągania. Usługa Azure Automation może być kierowana na maszyny z systemem Windows lub Linux, w chmurze lub lokalnie.

### <a name="management-of-all-your-dsc-artifacts"></a>Zarządzanie wszystkimi artefaktami DSC

Konfiguracja stanu automatyzacji platformy Azure przynosi tę samą warstwę zarządzania do [konfiguracji żądanego stanu programu PowerShell,](/powershell/scripting/dsc/overview/overview) jak oferuje dla skryptów programu PowerShell. Z witryny Azure portal lub z programu PowerShell można zarządzać wszystkie konfiguracje DSC, zasoby i węzły docelowe.

![Zrzut ekranu przedstawiający stronę usługi Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importowanie danych raportowania do dzienników usługi Azure Monitor

Węzły, które są zarządzane za pomocą konfiguracji stanu automatyzacji usługi Azure wysyłają szczegółowe dane o stanie raportowania do wbudowanego serwera ściągania. Można skonfigurować konfigurację stanu automatyzacji platformy Azure, aby wysłać te dane do obszaru roboczego usługi Log Analytics. Zobacz [Forward Azure Automation State state reporting data to Azure Monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>Wymagania wstępne dotyczące korzystania z konfiguracji stanu automatyzacji platformy Azure

Należy wziąć pod uwagę następujące wymagania podczas korzystania z konfiguracji stanu automatyzacji platformy Azure dla DSC.

### <a name="operating-system-requirements"></a>Wymagania dotyczące systemu operacyjnego

W przypadku węzłów z systemem Windows obsługiwane są następujące wersje:

- Windows Server 2019
- Windows Server 2016
- System Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>Samodzielna jednostka SKU produktu microsoft [Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) nie zawiera implementacji dsc. W związku z tym nie może być zarządzany przez program PowerShell DSC lub konfiguracji stanu automatyzacji platformy Azure.

W przypadku węzłów z systemem Linux rozszerzenie DSC Linux obsługuje wszystkie dystrybucje Linuksa wymienione w obszarze [Obsługiwane dystrybucje Linuksa](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Wymagania DSC

Dla wszystkich węzłów systemu Windows uruchomionych na platformie Azure [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) jest zainstalowany podczas dołączania. W przypadku węzłów z systemem Windows Server 2012 i Windows 7 usługa [WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) jest włączona.

Dla wszystkich węzłów systemu Linux uruchomionych na platformie Azure [program PowerShell DSC dla systemu Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) jest zainstalowany podczas dołączania.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Konfiguracja sieci prywatnych

Jeśli węzły znajdują się w sieci prywatnej, wymagane są następujące porty i adresy URL. Te zasoby zapewniają łączność sieciową dla węzła zarządzanego i umożliwiają dsc do komunikowania się z usługą Azure Automation.

* Port: Tylko TCP 443 wymagany do wychodzącego dostępu do Internetu
* Globalny adres URL: ***.azure-automation.net**
* Globalny adres URL us Gov Virginia: ***.azure-automation.us**
* Usługa agenta: **https://\<obszar\>roboczyId .agentsvc.azure-automation.net**

Jeśli używasz zasobów DSC, które komunikują się między węzłami, takich jak [WaitFor* zasobów,](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)należy również zezwolić na ruch między węzłami. Zapoznaj się z dokumentacją dla każdego zasobu DSC, aby zrozumieć te wymagania sieciowe.

#### <a name="proxy-support"></a>Obsługa serwera proxy

Obsługa serwera proxy dla agenta DSC jest dostępna w systemie Windows w wersji 1809 i nowszej. Ta opcja jest włączona przez `ProxyURL` `ProxyCredential` ustawienie wartości dla i w [skrypcie metakonfiguracji](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) używanym do rejestrowania węzłów.

>[!NOTE]
>Konfiguracja stanu automatyzacji platformy Azure nie zapewnia obsługi serwera proxy DSC dla poprzednich wersji systemu Windows.

W przypadku węzłów systemu Linux agent DSC `http_proxy` obsługuje serwer proxy i używa zmiennej do określenia adresu URL.

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Zakresy sieci konfiguracji stanu automatyzacji platformy Azure i obszar nazw

Zaleca się używanie adresów wymienionych poniżej podczas definiowania wyjątków. W przypadku adresów IP można pobrać [zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ten plik jest aktualizowany co tydzień i ma aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP.

Jeśli masz konto automatyzacji, które jest zdefiniowane dla określonego regionu, można ograniczyć komunikację do tego regionalnego centrum danych. Poniższa tabela zawiera rekord DNS dla każdego regionu:

| **Region** | **Rekord DNS** |
| --- | --- |
| Zachodnio-środkowe stany USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Południowo-środkowe stany USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Wschodnie stany USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kanada Środkowa |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Zachodnia |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Północna |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Azja Południowo-Wschodnia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indie Środkowe |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonia Wschodnia |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australia Południowo-Wschodnia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Południowe Zjednoczone Królestwo | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Wirginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Aby uzyskać listę adresów IP regionu zamiast nazw regionów, pobierz plik XML [adresu IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653) z Centrum pobierania Firmy Microsoft.

> [!NOTE]
> Plik XML adresu IP centrum danych platformy Azure zawiera listę zakresów adresów IP, które są używane w centrach danych platformy Microsoft Azure. Plik zawiera zakresy obliczeniowe, SQL i magazynowe.
>
>Zaktualizowany plik jest publikowany co tydzień. Plik odzwierciedla aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień. Warto pobrać nowy plik XML co tydzień. Następnie zaktualizuj witrynę, aby poprawnie identyfikować usługi uruchomione na platformie Azure. 

Użytkownicy usługi Azure ExpressRoute powinni pamiętać, że ten plik jest używany do aktualizowania reklamy usługi Azure (Border Gateway Protocol) w pierwszym tygodniu każdego miesiąca.

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z usługi DSC w konfiguracji stanu automatyzacji platformy Azure, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure.](automation-dsc-getting-started.md)
- Aby dowiedzieć się, jak dołączać węzły, zobacz [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure](automation-dsc-onboarding.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać informacje o poleceniach cmdlet programu PowerShell, zobacz [polecenia cmdlet konfiguracji stanu automatyzacji platformy Azure](/powershell/module/azurerm.automation/#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Aby zobaczyć przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie przy użyciu konfiguracji stanu automatyzacji platformy Azure i chocolatey.](automation-dsc-cd-chocolatey.md)
