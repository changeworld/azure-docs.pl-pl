---
title: Wprowadzenie do korzystania z usługi Konfiguracja DSC automatyzacji Azure
description: Objaśnienie i przykłady typowych zadań w żądany stan konfiguracji (Konfiguracja DSC automatyzacji Azure)
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0a00050712aa62f3b12e4af4c3da3a1dc0e60219
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>Wprowadzenie do korzystania z usługi Konfiguracja DSC automatyzacji Azure

W tym artykule opisano sposób wykonywania typowych zadań z żądanego stanu konfiguracji (Konfiguracja DSC automatyzacji Azure), takie jak tworzenie, importowanie i konfiguracje, dołączania urządzenia do zarządzania, kompilowanie i wyświetlania raportów. Omówienie jakie Konfiguracja DSC automatyzacji Azure jest, zobacz [Przegląd usługi Konfiguracja DSC automatyzacji Azure](automation-dsc-overview.md). Dokumentacja usługi Konfiguracja DSC w temacie [żądany stan konfiguracji Omówienie środowiska Windows PowerShell](/powershell/dsc/overview).

Ten artykuł zawiera przewodnik krok po kroku przy użyciu usługi Konfiguracja DSC automatyzacji Azure. Chcąc środowiska próbki, który został już skonfigurowany bez wykonanie kroków opisanych w tym artykule, można użyć następującego szablonu usługi Resource Manager: ten szablon ustawia ukończone środowiska Konfiguracja DSC automatyzacji Azure, łącznie z maszyny Wirtualnej platformy Azure, która jest zarządzane przez Konfiguracja DSC automatyzacji Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć przykłady przedstawione w tym artykule, wymagane są następujące elementy:

* Konto usługi Azure Automation. Aby uzyskać instrukcje dotyczące tworzenia konta Uruchom jako usługi Azure Automation, zobacz [Konto Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md).
* Maszyna wirtualna Azure Resource Manager (nie klasycznego) systemem Windows Server 2008 R2 lub nowszym. Aby uzyskać instrukcje dotyczące tworzenia maszyny wirtualnej, zobacz [Tworzenie pierwszej maszyny wirtualnej z systemem Windows w witrynie Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Tworzenie konfiguracji DSC

Utwórz prostą [konfiguracji DSC](/powershell/dsc/configurations) dzięki temu obecności lub braku **serwera sieci Web** Windows funkcji (IIS), w zależności od tego, jak przypisać węzłów.

1. Uruchom program Windows PowerShell ISE (lub dowolnym edytorze tekstów).
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

Ta konfiguracja wymaga jednego zasobu w każdym węźle bloku, [WindowsFeature zasobów](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), który zapewnia obecności lub braku **serwera sieci Web** funkcji.

## <a name="importing-a-configuration-into-azure-automation"></a>Importowanie konfiguracji usługi Automatyzacja Azure

Następnie należy zaimportować konfigurację do konta automatyzacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
1. Na **konto automatyzacji** wybierz pozycję **konfiguracji DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracji DSC** kliknij przycisk **+ Dodaj konfigurację**.
1. Na **konfigurację importu** pozycję Przeglądaj, aby `TestConfig.ps1` plik na komputerze.

    ![Zrzut ekranu przedstawiający ** importowania konfiguracji ** bloku](./media/automation-dsc-getting-started/AddConfig.png)
1. Kliknij przycisk **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Wyświetlanie konfiguracji w usłudze Automatyzacja Azure

Po zaimportowaniu konfiguracji, można go wyświetlić w portalu Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
1. Na **konto automatyzacji** wybierz pozycję **konfiguracji DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracji DSC** kliknij przycisk **TestConfig** (jest to nazwa konfiguracji zaimportowany w poprzedniej procedurze).
1. Na **konfiguracji TestConfig** kliknij przycisk **źródło konfiguracji widoku**.

    ![Zrzut ekranu przedstawiający blok konfiguracji TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)

    A **źródło konfiguracji TestConfig** zostanie otwarty blok zawierający kod programu PowerShell dla konfiguracji.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilowanie konfiguracji w usłudze Automatyzacja Azure

Przed żądanego stanu można stosować do węzła, konfiguracji DSC definiowanie tego stanu musi być skompilowany w co najmniej jednej konfiguracji węzła (MOF dokumentu) i umieszczone na serwerze ściągania usługi Konfiguracja DSC automatyzacji. Aby uzyskać bardziej szczegółowy opis kompilowania konfiguracji DSC automatyzacji Azure, zobacz [kompilowania konfiguracji DSC automatyzacji Azure](automation-dsc-compile.md). Aby uzyskać więcej informacji o kompilacji konfiguracji, zobacz [konfiguracji DSC](/powershell/dsc/configurations).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
1. Na **konto automatyzacji** kliknij przycisk **konfiguracji DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracji DSC** kliknij przycisk **TestConfig** (nazwa poprzednio zaimportowanego konfiguracji).
1. Na **konfiguracji TestConfig** kliknij przycisk **skompilować**, a następnie kliknij przycisk **tak**. Spowoduje to uruchomienie zadania kompilacji.

    ![Zrzut ekranu przedstawiający stronę konfiguracji TestConfig wyróżnianie przycisk kompilacji](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Podczas kompilowania konfiguracji w automatyzacji Azure automatycznie wdraża żadnej konfiguracji węzła utworzonego za na serwerze ściągania.

## <a name="viewing-a-compilation-job"></a>Wyświetlanie zadania kompilacji

Po rozpoczęciu kompilacji, możesz je wyświetlić w **zadań kompilacji** kafelka w **konfiguracji** bloku. **Zadań kompilacji** zawiera obecnie uruchomiona, zakończone oraz zadania zakończone niepowodzeniem. Po otwarciu bloku zadania kompilacji zawiera informacje dotyczące tego zadania, w tym wszelkie błędy lub ostrzeżenia napotkał, parametry wejściowe używane w konfiguracji i kompilacja dzienników.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
1. Na **konto automatyzacji** kliknij przycisk **konfiguracji DSC** w obszarze **zarządzania konfiguracją**.
1. Na **konfiguracji DSC** kliknij przycisk **TestConfig** (nazwa poprzednio zaimportowanego konfiguracji).
1. W obszarze **zadań kompilacji**, wybierz zadanie kompilacji, który chcesz wyświetlić. A **zadania kompilacji** otwierania z datą rozpoczęcia zadania kompilacji strony.

    ![Zrzut ekranu przedstawiający stronę zadania kompilacji](./media/automation-dsc-getting-started/CompilationJob.png)
1. Kliknij Kafelek w **zadania kompilacji** stronę, aby zobaczyć więcej szczegółowych informacji o zadaniu.

## <a name="viewing-node-configurations"></a>Wyświetlanie konfiguracji węzłów

Pomyślne zakończenie zadania kompilacji tworzy nowy co najmniej jednej konfiguracji węzła. Konfiguracja węzła jest dokumentem MOF, który jest wdrożony na serwerze ściągania i są gotowe do można ściągnąć i stosowane przez co najmniej jeden węzeł. Konfiguracje węzłów można wyświetlić w Twoje konto usługi Automatyzacja w **konfiguracji węzłów DSC** bloku. Konfiguracja węzła ma nazwę w postaci *ConfigurationName*. *NodeName*.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu centralnym kliknij **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
1. Na **konto automatyzacji** bloku, kliknij przycisk **konfiguracji węzłów DSC**.

    ![Zrzut ekranu przedstawiający blok konfiguracji węzłów DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Dołączanie maszyny Wirtualnej platformy Azure do zarządzania z usługi Konfiguracja DSC automatyzacji Azure

Konfiguracja DSC automatyzacji Azure umożliwia zarządzanie maszynach wirtualnych platformy Azure (Classic i Resource Manager), lokalnych maszyn wirtualnych systemu Linux maszyny, usług AWS maszyn wirtualnych i fizycznych komputerach lokalnych. W tym artykule dowiesz się, jak dołączyć tylko usługi Azure Resource Manager maszyn wirtualnych. Uzyskać informacji na temat dołączania innych typów urządzeń, zobacz [dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Dołączyć maszyny Wirtualnej platformy Azure Resource Manager do zarządzania przez Konfiguracja DSC automatyzacji Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
1. Na **konto automatyzacji** kliknij przycisk **węzłów DSC** w obszarze **zarządzania konfiguracją**.
1. W **węzłów DSC** kliknij przycisk **dodać maszyny Wirtualnej Azure**.

    ![Zrzut ekranu przedstawiający stronę węzłów DSC wyróżnianie przycisku Dodaj maszyny Wirtualnej Azure](./media/automation-dsc-getting-started/OnboardVM.png)
1. Na stronie maszyny wirtualne wybierz maszyny Wirtualnej. **Dodawanie maszyn wirtualnych Azure** kliknij przycisk **wybierz maszyny wirtualne do dołączenia**.
1. Kliknij przycisk **Połącz**.

   > [!IMPORTANT]
   > Musi to być maszyny Wirtualnej platformy Azure Resource Manager systemem Windows Server 2008 R2 lub nowszym.

1. W **rejestracji** strony, wprowadź nazwę konfiguracji węzła, który chcesz zastosować do maszyny Wirtualnej w ramach **Nazwa konfiguracji węzła** pole. To musi dokładnie odpowiadać Nazwa konfiguracji węzła, w ramach konta automatyzacji. W tym momencie podanie nazwy jest opcjonalne. Można zmienić konfiguracji węzła przypisanej po dołączania węzła.
   Sprawdź **ponowny rozruch węzła, w razie potrzeby**, a następnie kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający blok rejestracji](./media/automation-dsc-getting-started/RegisterVM.png)

    Konfiguracja węzła określonego są stosowane do maszyny Wirtualnej w odstępach czasu określonych przez **częstotliwość trybu konfiguracji**, i sprawdza, czy maszyna wirtualna aktualizacje konfiguracji węzła w odstępach czasu określonych przez **odświeżania Częstotliwość**. Aby uzyskać więcej informacji na temat korzystania z tych wartości, zobacz [Konfigurowanie lokalny program Configuration Manager](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
1. W **Dodaj maszyny wirtualne Azure** bloku, kliknij przycisk **Utwórz**.

Azure rozpoczyna proces przechodzenia do maszyny Wirtualnej. Po zakończeniu maszyny Wirtualnej zostaną wyświetlone w **węzłów DSC** bloku na koncie automatyzacji.

## <a name="viewing-the-list-of-dsc-nodes"></a>Wyświetlanie listy węzłów DSC

Można wyświetlić listę wszystkich komputerach, które zostały został załadowany do zarządzania na Twoim koncie automatyzacji w **węzłów DSC** bloku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
3. Na **konto automatyzacji** kliknij przycisk **węzłów DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Wyświetlanie raportów węzłów DSC

Zawsze Konfiguracja DSC automatyzacji Azure przeprowadza sprawdzanie spójności na węzeł zarządzany, węzeł wysyła raport o stanie z powrotem do serwera ściągania. Można wyświetlać te raporty na stronie dla tego węzła.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
3. Na **konto automatyzacji** kliknij przycisk **węzłów DSC**.
4. W **węzłów DSC** listy, wybierz węzeł, który chcesz wyświetlić.
5. Na **węzła** kliknij przycisk raport, który chcesz wyświetlić w obszarze **raporty**.

    ![Zrzut ekranu przedstawiający blok raportu](./media/automation-dsc-getting-started/NodeReport.png)

Na bloku dla konkretnego raportu można wyświetlić następujące informacje o stanie dla odpowiedniego Sprawdzanie spójności:

* Stan raportu — węzeł jest "Zgodnych" configuration "Niepowodzenie", czy węzeł jest "Nie są zgodne" (gdy węzeł jest w **applyandmonitor** tryb i na komputerze nie jest w żądanym stanie).
* Godzina rozpoczęcia Sprawdzanie spójności.
* Łącznego czasu wykonywania sprawdzania spójności.
* Typ sprawdzania spójności.
* Błędy, wraz z kodem błędu oraz komunikat o błędzie.
* Zasoby usługi Konfiguracja DSC używane w konfiguracji i stanu każdego zasobu (czy węzeł jest w stanie odpowiednią dla tego zasobu) — możesz kliknąć każdy zasób, aby uzyskać więcej szczegółowych informacji dla tego zasobu.
* Nazwa, adres IP i tryb konfiguracji węzła.

Możesz również kliknąć **wyświetlić raport raw** Aby wyświetlić dane, węzeł wysyła do serwera. Aby uzyskać więcej informacji o korzystaniu z tych danych zobacz [przy użyciu serwera raportu DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Może upłynąć trochę czasu, gdy węzeł został załadowany przed pierwszym raport jest dostępny. Konieczne może być oczekiwania dla pierwszego raportu do 30 minut po dołączeniu węzła.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Ponowne przypisywanie węzła do innego węzła konfiguracji

Można przypisać węzeł, aby użyć konfiguracji węzła innego niż ten, który początkowo przypisana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
3. Na **konto automatyzacji** kliknij przycisk **węzłów DSC**.
4. Na **węzłów DSC** strony, kliknij nazwę węzła chcesz ponownie przypisać.
5. Na stronie dla tego węzła, kliknij przycisk **węzła Przypisz**.

    ![Zrzut ekranu przedstawiający blok węzła wyróżnianie przycisk przypisać węzła](./media/automation-dsc-getting-started/AssignNode.png)
6. Na **przypisać konfiguracji węzła** konfiguracji węzła, do którego chcesz przypisać węzeł, a następnie kliknij przycisk Wybierz **OK**.

    ![Zrzut ekranu przedstawiający blok przypisać konfiguracji węzła](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Wyrejestrowywania węzła

Jeśli nie ma już węzeł ma być zarządzany przez Konfiguracja DSC automatyzacji Azure, możesz go wyrejestrować.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Po lewej stronie, kliknij przycisk **wszystkie zasoby** , a następnie nazwę konta automatyzacji.
3. Na **konto automatyzacji** kliknij przycisk **węzłów DSC**.
4. Na **węzłów DSC** strony, kliknij nazwę węzła chcesz wyrejestrować.
5. Na stronie dla tego węzła, kliknij przycisk **Unregister**.

    ![Zrzut ekranu przedstawiający blok węzła wyróżnianie przycisk Unregister](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Powiązane artykuły

* [Omówienie usługi Konfiguracja DSC automatyzacji Azure](automation-dsc-overview.md)
* [Dołączania komputerów do zarządzania przez Konfiguracja DSC automatyzacji Azure](automation-dsc-onboarding.md)
* [Omówienie stanu konfiguracji żądanego programu Windows PowerShell](https://msdn.microsoft.com/powershell/dsc/overview)
* [Polecenia cmdlet systemu Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Cennik usługi Konfiguracja DSC automatyzacji Azure](https://azure.microsoft.com/pricing/details/automation/)
