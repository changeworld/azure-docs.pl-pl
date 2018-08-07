---
title: Wprowadzenie do usługi Azure Automation DSC
description: Objaśnienie i przykłady typowych zadań w usłudze Azure Automation Desired State Configuration (DSC)
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f2312064e9fb7676d5609ee077d5ed7e02e8f30
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524468"
---
# <a name="getting-started-with-azure-automation-dsc"></a>Wprowadzenie do usługi Azure Automation DSC

W tym artykule opisano sposób wykonywania typowych zadań przy użyciu usługi Azure Automation Desired State Configuration (DSC), takie jak tworzenie, importowanie i kompilowanie konfiguracji, dołączanie maszyn, aby zarządzać i wyświetlania raportów. Aby uzyskać przegląd jakie usługi Azure Automation DSC jest, zobacz [Omówienie usługi Azure Automation DSC](automation-dsc-overview.md). Aby uzyskać dokumentację DSC, zobacz [Windows PowerShell Desired State Configuration Overview](/powershell/dsc/overview).

Ten artykuł zawiera przewodnik krok po kroku do korzystania z usługi Azure Automation DSC. Jeśli chcesz, aby środowisko próbki, które jest już skonfigurowany bez wykonanie kroków opisanych w tym artykule, można użyć następującego szablonu usługi Resource Manager: ten szablon ustawia ukończone środowisko usługi Azure Automation DSC, łącznie z maszyny Wirtualnej platformy Azure, która jest zarządzane przez usługi Azure Automation DSC.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było ukończyć przykłady przedstawione w tym artykule, wymagane jest spełnienie następujących:

* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
* Maszyna wirtualna usługi Azure Resource Manager (nie klasycznej) systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Tworzenie konfiguracji DSC

Tworzenie prostego [konfiguracji DSC](/powershell/dsc/configurations) temu obecności lub braku **serwera sieci Web** Windows funkcji (IIS), w zależności od tego, jak przypisać węzłów.

1. Uruchom program Windows PowerShell ISE (lub dowolnego edytora tekstów).
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

Ta konfiguracja wywołuje jeden zasób w każdym bloku węzła [zasób WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), który zapewnia, że obecności lub braku **serwera sieci Web** funkcji.

## <a name="importing-a-configuration-into-azure-automation"></a>Importowanie konfiguracji do usługi Azure Automation

Następnie możesz zaimportować konfigurację na konto usługi Automation.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** wybierz opcję **konfiguracje DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracje DSC** kliknij **+ Dodaj konfigurację**.
1. Na **Konfiguracja importu** strony, przejdź do `TestConfig.ps1` pliku na komputerze.

    ![Zrzut ekranu przedstawiający ** importowania konfiguracji ** strony](./media/automation-dsc-getting-started/AddConfig.png)
1. Kliknij przycisk **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Wyświetlanie konfiguracji w usłudze Azure Automation

Po zaimportowaniu konfiguracji, możesz go wyświetlić w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** wybierz opcję **konfiguracje DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracje DSC** kliknij **TestConfig** (to jest nazwa konfiguracji zostały zaimportowane w poprzedniej procedurze).
1. Na **konfiguracji TestConfig** kliknij **Wyświetl źródło konfiguracji**.

    ![Zrzut ekranu przedstawiający stronę Konfiguracja TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

    A **źródło konfiguracji TestConfig** stronie otwiera się i wyświetla kod programu PowerShell dla konfiguracji.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilowanie konfiguracji w usłudze Azure Automation

Przed zastosowaniem żądanego stanu do węzła konfiguracji DSC, definiując tego stanu musi być skompilowany w co najmniej jedna konfiguracja węzła (dokument MOF), a umieszczone na serwerze ściągania usługi Automation DSC. Aby uzyskać bardziej szczegółowy opis kompilowanie konfiguracji w usłudze Azure Automation DSC, zobacz [kompilowanie konfiguracji w usłudze Azure Automation DSC](automation-dsc-compile.md). Aby uzyskać więcej informacji na temat kompilacji konfiguracji, zobacz [konfiguracje DSC](/powershell/dsc/configurations).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** kliknij **konfiguracje DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracje DSC** kliknij **TestConfig** (nazwa poprzednio zaimportowanego konfiguracji).
1. Na **konfiguracji TestConfig** kliknij **skompilować**, a następnie kliknij przycisk **tak**. Spowoduje to uruchomienie zadania kompilacji.

    ![Zrzut ekranu przedstawiający stronę Konfiguracja TestConfig Wyróżnienie przycisku kompilacji](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Podczas kompilowania konfiguracji w usłudze Azure Automation automatycznie wdraża żadnej konfiguracji węzła utworzone pliki MOF na serwerze ściągania.

## <a name="viewing-a-compilation-job"></a>Wyświetlanie zadania kompilacji

Po uruchomieniu kompilacji, można je wyświetlić w **zadania kompilacji** Kafelek w **konfiguracji** strony. **Zadania kompilacji** Kafelek pokazuje aktualnie uruchomione ukończone oraz zadania zakończone niepowodzeniem. Po otwarciu strony zadania kompilacji, pokazuje informacje o danym zadaniu, w tym wszelkie błędy lub ostrzeżenia napotkał, parametry wejściowe używane w konfiguracji i kompilacja dzienników.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** kliknij **konfiguracje DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracje DSC** kliknij **TestConfig** (nazwa poprzednio zaimportowanego konfiguracji).
1. W obszarze **zadania kompilacji**, wybierz zadanie kompilacji, którą chcesz wyświetlić. A **zadanie kompilacji** stronie zostanie otwarta, z datą, które zostało uruchomione zadanie kompilacji.

    ![Zrzut ekranu przedstawiający stronę zadanie kompilacji](./media/automation-dsc-getting-started/CompilationJob.png)
1. Kliknij dowolny Kafelek w **zadanie kompilacji** strony, aby zobaczyć więcej szczegółowych informacji o zadaniu.

## <a name="viewing-node-configurations"></a>Wyświetlanie konfiguracji węzła

Pomyślne wykonanie zadań kompilacji tworzy jeden lub więcej nowe konfiguracje węzłów. Konfiguracja węzła jest dokument MOF, który jest wdrożony na serwerze ściągania i jest gotowa i stosowane przez co najmniej jeden węzeł. Konfiguracje węzłów można wyświetlić na koncie usługi Automation w **konfiguracje węzłów DSC** strony. Konfiguracja węzła o nazwie z formularzem *ConfigurationName*. *NodeName*.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu Centrum kliknij pozycję **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** kliknij **State configuration (DSC)**, a następnie wybierz **konfiguracje**.

    ![Zrzut ekranu przedstawiający stronę konfiguracje węzłów DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Dołączanie maszyny Wirtualnej platformy Azure do zarządzania za pomocą usługi Azure Automation DSC

Usługi Azure Automation DSC umożliwia zarządzanie maszyn wirtualnych platformy Azure (wersja klasyczna i usługi Resource Manager), lokalnych maszyn wirtualnych, maszyny systemu Linux, maszyn wirtualnych usług AWS i fizycznych komputerów w środowisku lokalnym. W tym artykule dowiesz się, jak dołączyć tylko usługi Azure Resource Manager maszyny wirtualne. Informacji na temat przechodzenia do innych typów maszyn wirtualnych dla [dołączanie maszyn w celu zarządzania przez usługi Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Dołączyć Maszynę wirtualną platformy Azure Resource Manager do zarządzania usługi Azure Automation DSC

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
1. Na **konta usługi Automation** kliknij **State configuration (DSC)** w obszarze **zarządzania konfiguracją** i wybierz **węzłów**.
1. W **węzłów** kliknij **+ Dodaj**.

    ![Zrzut ekranu przedstawiający stronę węzłów DSC, wyróżnienie przycisku Dodaj maszynę Wirtualną platformy Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. Na stronie maszyn wirtualnych wybierz maszynę Wirtualną. **Dodaj maszyny wirtualne platformy Azure** kliknij **wybierz maszyny wirtualne do dołączenia**.
1. Kliknij przycisk **Połącz**.

   > [!IMPORTANT]
   > Musi to być maszyna wirtualna usługi Azure Resource Manager systemem Windows Server 2008 R2 lub nowszym.

1. W **rejestracji** strony, wprowadź nazwę konfiguracji węzła, który chcesz zastosować do maszyny Wirtualnej w **Nazwa konfiguracji węzła** pole. To musi dokładnie odpowiadać Nazwa konfiguracji węzła w ramach konta usługi Automation. W tym momencie podanie nazwy jest opcjonalne. Można zmienić konfiguracji przypisanym węźle po dołączeniu węzła.
   Sprawdź **ponowny rozruch węzła, w razie potrzeby**, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający stronę rejestracji](./media/automation-dsc-getting-started/RegisterVM.png)

    Konfiguracja węzła określone, są stosowane do maszyny Wirtualnej w odstępach czasu określonego przez **częstotliwość trybu konfiguracji**, i maszyna wirtualna sprawdza dostępność aktualizacji do konfiguracji węzła w odstępach czasu określonego przez **odświeżania Częstotliwość**. Aby uzyskać więcej informacji na temat używania tych wartości, zobacz [Konfigurowanie programu Local Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. W **Dodaj maszyny wirtualne platformy Azure** kliknij **Utwórz**.

Azure rozpoczyna się proces dołączania maszyny Wirtualnej. Po zakończeniu, maszyna wirtualna pojawia się w **węzłów** karcie **State configuration (DSC)** strony na koncie usługi Automation.

## <a name="viewing-the-list-of-dsc-nodes"></a>Wyświetlanie listy węzłów DSC

Można wyświetlić listę wszystkich maszyn, które zostały dołączone w celu zarządzania na koncie usługi Automation w **węzłów** karcie **State configuration (DSC)** strony.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
3. Na **konta usługi Automation** kliknij **State configuration (DSC)**, następnie kliknij przycisk **węzłów** kartę.

## <a name="viewing-reports-for-dsc-nodes"></a>Wyświetlanie raportów węzłów DSC

Każdorazowo, Azure Automation DSC przeprowadza sprawdzanie spójności w węźle zarządzanym węźle wysyła raport o stanie z powrotem do serwera ściągania. Na stronie dla tego węzła, mogą wyświetlać te raporty.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
3. Na **konta usługi Automation** kliknij **State configuration (DSC)** a następnie kliknij przycisk **węzłów** kartę.
4. W **węzłów** , wybierz węzeł, aby wyświetlić na liście.
5. Na **węzła** kliknij raport, którą chcesz wyświetlić w obszarze **raporty**.

    ![Zrzut ekranu przedstawiający stronę z raportem](./media/automation-dsc-getting-started/NodeReport.png)

Na stronie dla konkretnego raportu można wyświetlić następujące informacje o stanie dla odpowiedniego Sprawdzanie spójności:

* Stan raportu — ten węzeł jest "Zgodne" Konfiguracja "Niepowodzenie", czy ten węzeł jest "Niezgodne" (gdy węzeł jest w **applyandmonitor** tryb i na komputerze nie jest w żądanym stanie).
* Godzina rozpoczęcia Sprawdzanie spójności.
* Całkowitego czasu wykonania sprawdzania spójności.
* Typ sprawdzania spójności.
* Błędy wraz z kodem błędu oraz komunikat o błędzie.
* Wszystkie zasoby DSC, używane w konfiguracji i stanu każdego zasobu (czy węzeł jest w żądanym stanie dla tego zasobu) — możesz kliknąć każdy zasób, aby uzyskać bardziej szczegółowe informacje dla tego zasobu.
* Nazwa, adres IP i tryb konfiguracji węzła.

Możesz również kliknąć **Wyświetl nieprzetworzony raport** Aby wyświetlić rzeczywiste dane, które węzeł wysyła do serwera. Aby uzyskać więcej informacji o korzystaniu z tych danych, zobacz [używanie serwera raportów DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Może upłynąć trochę czasu, po węzeł jest dołączona, zanim pierwszy raport jest dostępny. Może być konieczne upływie do 30 minut, zanim pierwszego raportu można dołączyć węzła.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Ponowne przypisywanie węzła do innego węzła konfiguracji

Możesz przypisać węzła na potrzeby konfiguracji węzła innego niż ten, który początkowo przypisana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
3. Na **konta usługi Automation** kliknij **State configuration (DSC)** a następnie kliknij przycisk **węzłów** kartę.
4. Na **węzłów** kartę, kliknij nazwę węzła, który chcesz ponownie przypisać.
5. Na stronie dla tego węzła, kliknij przycisk **przypisywanie konfiguracji węzła**.

    ![Zrzut ekranu przedstawiający stronę węzła wyróżnianie przycisk Przypisz węzła](./media/automation-dsc-getting-started/AssignNode.png)
6. Na **przypisywanie konfiguracji węzła** konfiguracji węzła, do którego chcesz przypisać węzeł, a następnie kliknij przycisk Wybierz **OK**.

    ![Zrzut ekranu przedstawiający stronę przypisywanie konfiguracji węzła](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Wyrejestrowywanie węzła

Jeśli nie ma już węzeł, aby były zarządzane przez usługi Azure Automation DSC, można je wyrejestrować.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** i następnie nazwę konta usługi Automation.
3. Na **konta usługi Automation** kliknij **State configuration (DSC)** a następnie kliknij przycisk **węzłów** tab.git 
4. Na **węzłów** kartę, kliknij nazwę węzła chcesz wyrejestrować.
5. Na stronie dla tego węzła, kliknij przycisk **Wyrejestruj**.

    ![Zrzut ekranu przedstawiający stronę węzła Wyróżnienie przycisku Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Powiązane artykuły

* [Omówienie usługi Azure Automation DSC](automation-dsc-overview.md)
* [Dołączanie maszyn w celu zarządzania przez usługi Azure Automation DSC](automation-dsc-onboarding.md)
* [Program Windows PowerShell Desired State Configuration — omówienie](https://msdn.microsoft.com/powershell/dsc/overview)
* [Polecenia cmdlet usługi Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Cennik usługi Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
