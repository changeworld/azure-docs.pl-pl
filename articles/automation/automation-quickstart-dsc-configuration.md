---
title: Szybki start platformy Azure — konfigurowanie maszyny wirtualnej za pomocą DSC | Microsoft Docs
description: Konfigurowanie stosu LAMP w maszynie wirtualnej systemu Linux za pomocą konfiguracji żądanego stanu (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
keywords: dsc, konfiguracja, automatyzacja
author: KrisBash
ms.author: krbash
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a30f9c1a61044c0911a5afc27ad95fc758b4c83e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738301"
---
# <a name="configure-a-linux-virtual-machine-with-desired-state-configuration"></a>Konfigurowanie maszyny wirtualnej systemu Linux za pomocą DSC

Włączając konfigurację żądanego stanu (DSC), możesz zarządzać i monitorować konfiguracje serwerów systemu Windows i Linux. Można zidentyfikować lub poprawić konfiguracje, które odstają od wymaganej konfiguracji. Ta procedura szybkiego startu pokazuje kroki dołączania maszyny wirtualnej systemu Linux i wdrażania stosu LAMP za pomocą DSC.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
* Maszyna wirtualna usługi Azure Resource Manager (nie klasycznej) z systemem Red Hat Enterprise Linux, CentOS lub Oracle Linux. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Create your first Linux virtual machine in the Azure portal](../virtual-machines/linux/quick-create-portal.md) (Tworzenie pierwszej maszyny wirtualnej systemu Linux w witrynie Azure Portal)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="onboard-a-virtual-machine"></a>Dołączanie maszyny wirtualnej
Istnieje wiele różnych metod dołączania maszyny i włączania konfiguracji żądanego stanu. Ten Szybki start obejmuje dołączanie za pomocą konta automatyzacji. Możesz dowiedzieć się więcej o innych metodach dołączania maszyn do konfiguracji żądanego stanu, czytając artykuł o [dołączaniu](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding).

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Konta automatyzacji**. Jeśli nie widać jej w okienku po lewej stronie, kliknij pozycję **Wszystkie usługi** i wyszukaj ją w wynikowym widoku.
1. Na liście wybierz konto automatyzacji.
1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**.
2. Kliknij pozycję **Dodaj**, aby otworzyć stronę wybierania maszyny wirtualnej.
3. Znajdź maszynę wirtualną, dla której chcesz włączyć DSC. Aby znaleźć określoną maszynę wirtualną, możesz użyć opcji pola wyszukiwania i filtra.
4. Kliknij maszynę wirtualną, a następnie wybierz pozycję **Połącz**
5. Wybierz ustawienia DSC odpowiednie dla maszyny wirtualnej. Jeśli konfiguracja została już przygotowana, możesz określić to jako *Nazwa konfiguracji węzła*. Możesz ustawić [tryb konfiguracji](https://docs.microsoft.com/powershell/dsc/metaconfig), aby sterować zachowaniem konfiguracji maszyny.
6. Kliknij przycisk **OK**.

![Dołączanie maszyny wirtualnej platformy Azure do konfiguracji DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

Podczas wdrażania rozszerzenia DSC w maszynie wirtualnej, wyświetla ona komunikat *Łączenie.*

## <a name="import-modules"></a>Importowanie modułów

Moduły zawierają zasoby DSC i wiele z nich znajduje się na [galerii programu PowerShell](https://www.powershellgallery.com). Wszystkie zasoby, które są używane w konfiguracjach, należy zaimportować przed skompilowaniem na konto automatyzacji. W tym samouczku jest wymagany moduł o nazwie **nx**.

1. W lewym okienku konta automatyzacji wybierz pozycję **Galeria modułów** (w obszarze udostępnionych zasobów).
1. Wyszukaj moduł, który chcesz zaimportować, wpisując część nazwy: *nx*
1. Kliknięcie modułu, który chcesz zaimportować
1. Kliknij pozycję **Importuj**

![Importowanie modułu DSC](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importowanie konfiguracji

Ta opcja szybkiego startu używa konfiguracji DSC, która konfiguruje programy Apache HTTP Server, MySQL i PHP na maszynie.

Aby uzyskać informacje o konfiguracjach DSC, zobacz [Konfiguracje DSC](https://docs.microsoft.com/powershell/dsc/configurations).

W edytorze tekstów wpisz poniższe i zapisz go lokalnie, jako `LAMPServer.ps1`.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Aby zaimportować konfigurację:

1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**, a następnie kliknij kartę **Konfiguracje**.
2. Kliknij pozycję **+ Dodaj**
3. Wybierz *Plik konfiguracji* zapisany w poprzednim kroku
4. Kliknij przycisk **OK**.

## <a name="compile-a-configuration"></a>Kompilacja konfiguracji

Konfiguracja DSC musi zostać skompilowana do konfiguracji węzła (dokument MOF) przed przypisaniem do węzła. Kompilacja weryfikuje konfigurację i pozwala na wprowadzanie wartości parametrów. Aby dowiedzieć się więcej na temat kompilacji konfiguracji, zobacz: [Kompilowanie konfiguracji w usłudze Azure Automation DSC](https://docs.microsoft.com/azure/automation/automation-dsc-compile)

Aby skompilować konfigurację:

1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**, a następnie kliknij kartę **Konfiguracje**.
1. Wybierz konfigurację zaimportowaną w poprzednim kroku „LAMPServer”
1. W opcjach menu kliknij pozycję **Kompiluj**, a następnie **Tak**
1. W widoku konfiguracji zobaczysz w kolejce nowe *zadanie kompilacji*. Po pomyślnym zakończeniu zadania możesz przejść do następnego kroku. Jeśli są jakiekolwiek błędy, możesz kliknąć zadanie kompilacji, aby uzyskać więcej informacji.

## <a name="assign-a-node-configuration"></a>Przypisywanie konfiguracji węzła

Skompilowaną *konfigurację węzła* można przypisać do węzłów DSC. Przypisanie powoduje zastosowanie konfiguracji do maszyny i monitoruje (lub automatycznie koryguje) dowolne odstępstwo od tej konfiguracji.

1. W okienku po lewej stronie konta usługi Automation wybierz pozycję **Konfiguracja stanu (DSC)**, a następnie kliknij kartę **Węzły**.
1. Wybierz węzeł, do którego chcesz przypisać konfigurację
1. Kliknij pozycję **Przypisywanie konfiguracji węzła**
1. Wybierz pozycje *Konfiguracja węzła* - **LAMPServer.localhost** — aby przypisać i kliknij przycisk **OK**
1. Skompilowana konfiguracja jest teraz przypisana do węzła, a stan węzła zmienia się na *Oczekujący*. Podczas następnej okresowej kontroli węzeł pobiera konfigurację, stosuje ją i zwrotnie zgłasza raport. Pobieranie konfiguracji dla węzła może potrwać do 30 minut w zależności od ustawień węzła. Aby wymusić natychmiastowe sprawdzenie, możesz lokalnie wykonać następujące polecenie na maszynie wirtualnej systemu Linux: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Przypisywanie konfiguracji węzła](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="viewing-node-status"></a>Wyświetlanie stanu węzła

Stan wszystkich węzłów zarządzanych można wyświetlić, wybierając pozycję **Konfiguracja stanu (DSC)**, a następnie kartę **Węzły** na koncie usługi Automation. Możesz filtrować wyświetlane dane według stanu, konfiguracji węzła lub nazwy wyszukiwania.

![Stan węzła DSC](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start maszyna wirtualna systemu Linux została dołączona do DSC, została utworzona konfiguracja dla stosu LAMP i wdrożona na maszynie wirtualnej. Aby dowiedzieć się, jak można użyć konfiguracji DSC usługi Automation w celu włączenia ciągłego wdrażania, przejdź do artykułu:

> [!div class="nextstepaction"]
> [Continuous deployment to a VM using DSC and Chocolatey](./automation-dsc-cd-chocolatey.md) (Ciągłe wdrażanie na maszynie wirtualnej za pomocą DSC i Chocolatey)

* Aby dowiedzieć się więcej na temat konfiguracji DSC programu PowerShell, zobacz [PowerShell Desired State Configuration Overview](https://docs.microsoft.com/powershell/dsc/overview) (Omówienie środowiska PowerShell żądanego stanu konfiguracji).
* Aby dowiedzieć się więcej o zarządzaniu Konfiguracją DSC usługi Automation z programem PowerShell, zobacz [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.automation/) (Program Azure PowerShell)
* Aby dowiedzieć się, jak przekazywać raporty DSC do dzienników usługi Azure Monitor w celu raportowania i przekazywania alertów, zobacz [Przekazywanie raportów DSC do dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/automation/automation-dsc-diagnostics) 

