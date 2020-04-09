---
title: Zarządzanie danymi usługi Azure Automation
description: Ten artykuł zawiera wiele tematów dotyczących zarządzania środowiskiem automatyzacji platformy Azure.  Obecnie obejmuje przechowywanie danych i tworzenie kopii zapasowych odzyskiwania po awarii usługi Azure Automation w usłudze Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: f917e9c64a932d75fd0f6b14c9e0f35808467355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984661"
---
# <a name="managing-azure-automation-data"></a>Zarządzanie danymi usługi Azure Automation

Ten artykuł zawiera wiele tematów dotyczących zarządzania danymi w środowisku automatyzacji platformy Azure.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="data-retention"></a>Przechowywanie danych

Po usunięciu zasobu w usłudze Azure Automation jest on zachowywany przez kilka dni do celów inspekcji przed trwałym usunięciem. W tym czasie nie można go zobaczyć ani użyć. Ta zasada dotyczy również zasobów należących do usuniętego konta automatyzacji.

W poniższej tabeli podsumowano zasady przechowywania dla różnych zasobów.

| Dane | Zasady |
|:--- |:--- |
| Konta |Konto zostanie trwale usunięte 30 dni po usunięciu go przez użytkownika. |
| Elementy zawartości |Zasób jest trwale usuwany 30 dni po usunięciu go przez użytkownika lub 30 dni po usunięciu konta, na które znajduje się zasób. |
| Węzły DSC |Węzeł DSC jest trwale usuwany 30 dni po wyrejestrowaniu z konta automatyzacji przy użyciu portalu Azure lub polecenia [cmddddddddud owe](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) w programie Windows PowerShell. Węzeł jest również trwale usuwany 30 dni po usunięciu konta, które przechowuje węzeł. |
| Stanowiska |Zadanie jest usuwane i trwale usuwane 30 dni po modyfikacji, na przykład po zakończeniu zadania, jest zatrzymane lub zawieszone. |
| Moduły |Moduł jest trwale usuwany 30 dni po usunięciu go przez użytkownika lub 30 dni po usunięciu przez użytkownika konta, na które znajduje się moduł. |
| Konfiguracje węzłów/pliki MOF |Stara konfiguracja węzła jest trwale usuwana 30 dni po wygenerowaniu nowej konfiguracji węzła. |
| Raporty węzłów |Raport węzła jest trwale usuwany 90 dni po wygenerowaniu nowego raportu dla tego węzła. |
| Elementy Runbook |Element runbook jest trwale usuwany 30 dni po usunięciu zasobu przez użytkownika lub 30 dni po usunięciu konta, na które znajduje się zasób. |

Zasady przechowywania dotyczą wszystkich użytkowników i obecnie nie można ich dostosować. Jeśli jednak chcesz przechowywać dane przez dłuższy czas, możesz [przesyłać dalej dane zadań usługi Azure Automation do dzienników usługi Azure Monitor.](automation-manage-send-joblogs-log-analytics.md)

## <a name="data-backup"></a>Kopia zapasowa danych

Po usunięciu konta automatyzacji na platformie Azure wszystkie obiekty na koncie są usuwane. Obiekty obejmują elementy runbook, moduły, konfiguracje, ustawienia, zadania i zasoby. Nie można ich odzyskać po usunięciu konta. Możesz użyć następujących informacji, aby uzyskać zapas zawartości konta usługi Automation przed jego usunięciem.

### <a name="runbooks"></a>Elementy Runbook

Można wyeksportować pliki runbook do plików skryptów przy użyciu portalu Azure lub polecenia cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) w programie Windows PowerShell. Te pliki skryptów można zaimportować do innego konta automatyzacji, zgodnie z opisem w [artykule Zarządzanie podręcznikami runbook w usłudze Azure Automation](manage-runbooks.md).

### <a name="integration-modules"></a>Moduły integracji

Nie można eksportować modułów integracji z usługi Azure Automation. Należy udostępnić je poza kontem automatyzacji.

### <a name="assets"></a>Elementy zawartości

Nie można eksportować zasobów usługi Azure Automation: certyfikatów, połączeń, poświadczeń, harmonogramów i zmiennych. Zamiast tego można użyć witryny Azure portal i poleceń cmdlet platformy Azure, aby zanotować szczegóły tych zasobów. Następnie użyj tych szczegółów, aby utworzyć wszystkie zasoby, które są używane przez elementy runbook, które można zaimportować do innego konta automatyzacji.

Nie można pobrać wartości zaszyfrowanych zmiennych ani pól haseł poświadczeń przy użyciu poleceń cmdlet. Jeśli nie znasz tych wartości, możesz je pobrać w bieśbiera. Aby uzyskać wartości zmiennych, zobacz [Zmienne zasoby w usłudze Azure Automation](shared-resources/variables.md). Aby dowiedzieć się więcej o pobieraniu wartości [poświadczeń, zobacz Zasoby poświadczeń w usłudze Azure Automation](shared-resources/credentials.md).

 ### <a name="dsc-configurations"></a>Konfiguracje DSC

Konfiguracje DSC można wyeksportować do plików skryptów przy użyciu portalu Azure portal lub polecenia cmdlet [export-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) w programie Windows PowerShell. Można zaimportować i używać tych konfiguracji na innym koncie automatyzacji.

## <a name="geo-replication-in-azure-automation"></a>Replikacja geograficzna w usłudze Azure Automation

Replikacja geograficzna jest standardem na kontach usługi Azure Automation. Podczas konfigurowania konta wybierasz region podstawowy. Wewnętrzna usługa replikacji geograficznej automatyzacji automatycznie przypisuje do konta region pomocniczy. Następnie usługa stale kopii zapasowych danych konta z regionu podstawowego do regionu pomocniczego. Pełną listę regionów podstawowych i pomocniczych można znaleźć w [obszarze Ciągłość działania i odzyskiwanie po awarii (BCDR): Regiony sparowane w parze platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

Kopia zapasowa utworzona przez usługę replikacji geograficznej automatyzacji jest kompletną kopią zasobów automatyzacji, konfiguracji i tym podobnych. Tej kopii zapasowej można użyć, jeśli region podstawowy ustępuje i traci dane. W mało prawdopodobnym przypadku utraty danych dla regionu podstawowego firma Microsoft próbuje je odzyskać. Jeśli firma nie może odzyskać danych podstawowych, używa automatycznego trybu failover i informuje o sytuacji za pośrednictwem subskrypcji platformy Azure. 

Usługa replikacji geograficznej automatyzacji nie jest dostępna bezpośrednio dla klientów zewnętrznych, jeśli wystąpi błąd regionalny. Jeśli chcesz zachować konfigurację automatyzacji i elementy runbook podczas awarii regionalnych:

1. Wybierz region pomocniczy, aby sparować go z regionem geograficznym podstawowego konta automatyzacji.

2. Utwórz konto automatyzacji w regionie pomocniczym.

3. Na koncie podstawowym wyeksportuj pliki runbook jako pliki skryptów.

4. Zaimportuj elementy runbook do konta automatyzacji w regionie pomocniczym.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o bezpiecznych zasobach w usłudze Azure Automation, zobacz [Szyfrowanie bezpiecznych zasobów w usłudze Azure Automation.](automation-secure-asset-encryption.md)

* Aby dowiedzieć się więcej o replikacji geograficznej, zobacz [Tworzenie i używanie aktywnej replikacji geograficznej](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication).