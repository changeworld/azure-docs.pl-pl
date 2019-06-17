---
title: Wprowadzenie do usługi Azure Automation stanu konfiguracji
description: Objaśnienie i przykłady typowych zadań w usługi Azure Automation stanu Configuration (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 60cd2d21167739e824489e30ebd187a5fc0cc12d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61074574"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Wprowadzenie do usługi Azure Automation stanu konfiguracji

W tym artykule opisano sposób wykonywania typowych zadań przy użyciu usługi Azure Automation stan konfiguracji, takie jak tworzenie, importowanie i kompilowanie konfiguracji i dołączania maszyn do zarządzania i wyświetlania raportów. To omówienie konfigurację stan usługi Azure Automation, zobacz [Przegląd konfiguracji stan automatyzacji Azure](automation-dsc-overview.md). Aby uzyskać dokumentację Desired State Configuration (DSC), zobacz [Windows PowerShell Desired State Configuration Overview](/powershell/dsc/overview).

Ten artykuł zawiera przewodnik krok po kroku, aby za pomocą usługi Azure Automation stan konfiguracji. Jeśli chcesz, aby środowisko próbki, które jest już skonfigurowany bez wykonanie kroków opisanych w tym artykule, można użyć następującego szablonu usługi Resource Manager: [Szablon usługi Azure Automation węźle zarządzanym](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ten szablon ustawia ukończone środowiska usługi Azure Automation stan konfiguracji, w tym maszynie Wirtualnej platformy Azure, który jest zarządzany przez usługi Azure Automation stan konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było ukończyć przykłady przedstawione w tym artykule, wymagane jest spełnienie następujących:

- Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
- Maszyna wirtualna usługi Azure Resource Manager (nie klasycznej) uruchomiona [obsługiwanym systemie operacyjnym](automation-dsc-overview.md#operating-system-requirements). Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Tworzenie konfiguracji DSC

Tworzenie prostego [konfiguracji DSC](/powershell/dsc/configurations) temu obecności lub braku **serwera sieci Web** Windows funkcji (IIS), w zależności od tego, jak przypisać węzłów.

1. Rozpocznij [VSCode](https://code.visualstudio.com/docs) (lub dowolnego edytora tekstów).
1. Wpisz następujący tekst:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Zapisz plik jako `TestConfig.ps1`.

Ta konfiguracja wywołuje jeden zasób w każdym bloku węzła [zasób WindowsFeature](/powershell/dsc/windowsfeatureresource), który zapewnia, że obecności lub braku **serwera sieci Web** funkcji.

## <a name="importing-a-configuration-into-azure-automation"></a>Importowanie konfiguracji do usługi Azure Automation

Następnie możesz zaimportować konfigurację na konto usługi Automation.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** wybierz opcję **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **konfiguracje** , a następnie kliknij **+ Dodaj**.
1. Na **Konfiguracja importu** strony, przejdź do `TestConfig.ps1` pliku na komputerze.

   ![Zrzut ekranu przedstawiający ** importowania konfiguracji ** bloku](./media/automation-dsc-getting-started/AddConfig.png)

1. Kliknij przycisk **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Wyświetlanie konfiguracji w usłudze Azure Automation

Po zaimportowaniu konfiguracji, możesz go wyświetlić w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** wybierz opcję **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **konfiguracje** , a następnie kliknij **TestConfig** (jest to nazwa konfiguracji importowane w ciągu poprzednich procedury).
1. Na **konfiguracji TestConfig** kliknij **Wyświetl źródło konfiguracji**.

   ![Zrzut ekranu przedstawiający blok konfiguracji TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

   A **źródło konfiguracji TestConfig** stronie otwiera się i wyświetla kod programu PowerShell dla konfiguracji.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilowanie konfiguracji w usłudze Azure Automation

Przed zastosowaniem żądanego stanu do węzła konfiguracji DSC, definiując tego stanu musi być skompilowany w co najmniej jedna konfiguracja węzła (dokument MOF), a umieszczone na serwerze ściągania usługi Automation DSC. Aby uzyskać bardziej szczegółowy opis kompilowanie konfiguracji w konfiguracji stan automatyzacji platformy Azure, zobacz [kompilowanie konfiguracji w konfiguracji stanu automatyzacji Azure](automation-dsc-compile.md).
Aby uzyskać więcej informacji na temat kompilacji konfiguracji, zobacz [konfiguracje DSC](/powershell/dsc/configurations).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **konfiguracje** , a następnie kliknij **TestConfig** (nazwa poprzednio zaimportowanego konfiguracji).
1. Na **konfiguracji TestConfig** kliknij **skompilować**, a następnie kliknij przycisk **tak**. Spowoduje to uruchomienie zadania kompilacji.

   ![Zrzut ekranu przedstawiający stronę Konfiguracja TestConfig Wyróżnienie przycisku kompilacji](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Podczas kompilowania konfiguracji w usłudze Azure Automation automatycznie wdraża żadnej konfiguracji węzła utworzone pliki MOF na serwerze ściągania.

## <a name="viewing-a-compilation-job"></a>Wyświetlanie zadania kompilacji

Po uruchomieniu kompilacji, można je wyświetlić w **zadania kompilacji** Kafelek w **konfiguracji** strony. **Zadania kompilacji** Kafelek pokazuje aktualnie uruchomione ukończone oraz zadania zakończone niepowodzeniem. Po otwarciu strony zadania kompilacji, pokazuje informacje o danym zadaniu, w tym wszelkie błędy lub ostrzeżenia napotkał, parametry wejściowe używane w konfiguracji i kompilacja dzienników.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **konfiguracje** , a następnie kliknij **TestConfig** (nazwa poprzednio zaimportowanego konfiguracji).
1. W obszarze **zadania kompilacji**, wybierz zadanie kompilacji, którą chcesz wyświetlić. A **zadanie kompilacji** z datą, które zostało uruchomione zadanie kompilacji zostanie otwarta strona.

   ![Zrzut ekranu przedstawiający stronę zadanie kompilacji](./media/automation-dsc-getting-started/CompilationJob.png)

1. Kliknij dowolny Kafelek w **zadanie kompilacji** strony, aby zobaczyć więcej szczegółowych informacji o zadaniu.

## <a name="viewing-node-configurations"></a>Wyświetlanie konfiguracji węzła

Pomyślne wykonanie zadań kompilacji tworzy jeden lub więcej nowe konfiguracje węzłów. Konfiguracja węzła jest dokument MOF, który jest wdrożony na serwerze ściągania i jest gotowa i stosowane przez co najmniej jeden węzeł. Konfiguracje węzłów można wyświetlić na koncie usługi Automation w **State configuration (DSC)** strony. Konfiguracja węzła o nazwie z formularzem *ConfigurationName*. *NodeName*.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** bloku kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **skompilowany konfiguracje** kartę.

   ![Zrzut ekranu przedstawiający kartę skompilowany konfiguracji](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Dołączanie maszyny Wirtualnej platformy Azure do zarządzania za pomocą usługi Azure Automation stanu konfiguracji

Za pomocą usługi Azure Automation stanu konfiguracji do zarządzania maszynami wirtualnymi platformy Azure (wersja klasyczna i usługi Resource Manager), lokalnych maszyn wirtualnych, maszyn systemu Linux, maszyn wirtualnych usług AWS i fizycznych komputerów w środowisku lokalnym. W tym artykule dowiesz się, jak dołączyć tylko usługi Azure Resource Manager maszyny wirtualne. Informacji na temat przechodzenia do innych typów maszyn wirtualnych dla [dołączanie maszyn w celu zarządzania usługi Azure Automation stan konfiguracji](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Dołączyć Maszynę wirtualną platformy Azure Resource Manager do zarządzania usługi Azure Automation stanu konfiguracji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** bloku kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** strony, podczas gdy na **węzłów** kliknij pozycję **+ Dodaj**.

   ![Zrzut ekranu przedstawiający stronę węzłów DSC, wyróżnienie przycisku Dodaj maszynę Wirtualną platformy Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na **maszyn wirtualnych** stronie, wybierz maszynę Wirtualną.
1. Na **maszyny wirtualnej** strony szczegółów, kliknij przycisk **+ Connect**.

   > [!IMPORTANT]
   > Musi to być maszyna wirtualna Azure Resource Manager działają [obsługiwanym systemie operacyjnym](automation-dsc-overview.md#operating-system-requirements).

2. W **rejestracji** wybierz Nazwa konfiguracji węzła, który chcesz zastosować do maszyny Wirtualnej w **Nazwa konfiguracji węzła** pole. W tym momencie podanie nazwy jest opcjonalne. Można zmienić konfiguracji przypisanym węźle po dołączeniu węzła.
   Sprawdź **ponowny rozruch węzła, w razie potrzeby**, następnie kliknij przycisk **OK**.

   ![Zrzut ekranu przedstawiający blok rejestracji](./media/automation-dsc-getting-started/RegisterVM.png)

   Konfiguracja węzła określone, są stosowane do maszyny Wirtualnej w odstępach czasu określonego przez **częstotliwość trybu konfiguracji**, i maszyna wirtualna sprawdza dostępność aktualizacji do konfiguracji węzła w odstępach czasu określonego przez **odświeżania Częstotliwość**. Aby uzyskać więcej informacji na temat używania tych wartości, zobacz [Konfigurowanie programu Local Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).

Azure rozpoczyna się proces dołączania maszyny Wirtualnej. Po zakończeniu, maszyna wirtualna pojawia się w **węzłów** karcie **State configuration (DSC)** strony na koncie usługi Automation.

## <a name="viewing-the-list-of-managed-nodes"></a>Wyświetlanie listy węzłów zarządzanych

Można wyświetlić listę wszystkich maszyn, które zostały dołączone w celu zarządzania na koncie usługi Automation w **węzłów** karcie **State configuration (DSC)** strony.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** bloku kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **węzłów** kartę.

## <a name="viewing-reports-for-managed-nodes"></a>Wyświetlanie raportów dotyczących zarządzanych węzłów

Każdorazowo, Konfiguracja stanu automatyzacji Azure przeprowadza sprawdzanie spójności w węźle zarządzanym węźle wysyła raport o stanie z powrotem do serwera ściągania. Na stronie dla tego węzła, mogą wyświetlać te raporty.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** bloku kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **węzłów** kartę. Tutaj można zobaczyć, omówienie stanu konfiguracji i szczegóły dla każdego węzła.

   ![Zrzut ekranu z węzła strony](./media/automation-dsc-getting-started/NodesTab.png)

1. W **węzłów** kartę, kliknij przycisk Nagraj węzła do otwierania raportowanie. Kliknij raport, który chcesz wyświetlić dodatkowe szczegóły raportowania.

   ![Zrzut ekranu przedstawiający blok raportu](./media/automation-dsc-getting-started/NodeReport.png)

Na blok dla konkretnego raportu można wyświetlić następujące informacje o stanie dla odpowiedniego Sprawdzanie spójności:

- Stan raportu — ten węzeł jest "Zgodne" Konfiguracja "Niepowodzenie", czy ten węzeł jest "Niezgodne" (gdy węzeł jest w **ApplyandMonitor** tryb i na komputerze nie jest w żądanym stanie).
- Godzina rozpoczęcia Sprawdzanie spójności.
- Całkowitego czasu wykonania sprawdzania spójności.
- Typ sprawdzania spójności.
- Błędy wraz z kodem błędu oraz komunikat o błędzie.
- Wszystkie zasoby DSC, używane w konfiguracji i stanu każdego zasobu (czy węzeł jest w żądanym stanie dla tego zasobu) — możesz kliknąć każdy zasób, aby uzyskać bardziej szczegółowe informacje dla tego zasobu.
- Nazwa, adres IP i tryb konfiguracji węzła.

Możesz również kliknąć **Wyświetl nieprzetworzony raport** Aby wyświetlić rzeczywiste dane, które węzeł wysyła do serwera.
Aby uzyskać więcej informacji o korzystaniu z tych danych, zobacz [używanie serwera raportów DSC](/powershell/dsc/reportserver).

Może upłynąć trochę czasu, po węzeł jest dołączona, zanim pierwszy raport jest dostępny. Może być konieczne upływie do 30 minut, zanim pierwszego raportu można dołączyć węzła.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Ponowne przypisywanie węzła do innego węzła konfiguracji

Możesz przypisać węzła na potrzeby konfiguracji węzła innego niż ten, który początkowo przypisana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** bloku kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **węzłów** kartę.
1. Na **węzłów** kartę, kliknij nazwę węzła, który chcesz ponownie przypisać.
1. Na stronie dla tego węzła, kliknij przycisk **przypisywanie konfiguracji węzła**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła wyróżnianie przycisk Przypisz konfigurację węzła](./media/automation-dsc-getting-started/AssignNode.png)

1. Na **przypisywanie konfiguracji węzła** konfiguracji węzła, do którego chcesz przypisać węzeł, a następnie kliknij przycisk Wybierz **OK**.

    ![Zrzut ekranu przedstawiający stronę przypisywanie konfiguracji węzła](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Wyrejestrowywanie węzła

Jeśli nie ma już węzeł, aby były zarządzane przez usługi Azure Automation DSC, można je wyrejestrować.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** bloku kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją**.
1. Na **State configuration (DSC)** kliknij **węzłów** kartę.
1. Na **węzłów** kartę, kliknij nazwę węzła chcesz wyrejestrować.
1. Na stronie dla tego węzła, kliknij przycisk **Wyrejestruj**.

    ![Zrzut ekranu przedstawiający stronę szczegółów węzła Wyróżnienie przycisku Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Powiązane artykuły

- [Omówienie konfiguracji stanu usługi Azure Automation](automation-dsc-overview.md)
- [Dołączanie maszyn w celu zarządzania usługi Azure Automation stanu konfiguracji](automation-dsc-onboarding.md)
- [Program Windows PowerShell Desired State Configuration — omówienie](/powershell/dsc/overview)
- [Polecenia cmdlet konfiguracji stanu usługi Azure Automation](/powershell/module/azurerm.automation/#automation)
- [Cennik konfiguracji stanu usługi Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
