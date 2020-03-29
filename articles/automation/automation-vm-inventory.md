---
title: Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu | Microsoft Docs
description: Zarządzanie maszyną wirtualną z wykorzystaniem zbierania spisu
services: automation
ms.subservice: change-inventory-management
keywords: spis, automatyzacja, zmiana, śledzenie
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d0324038b8a38d7eba84e5472b8f90439b0322c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844823"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu

Istnieje możliwość włączenia śledzenia z użyciem spisu dla maszyny wirtualnej platformy Azure na stronie zasobów tej maszyny wirtualnej. Na komputerach można zbierać i wyświetlać następujące informacje o spisie:

- Oprogramowanie Windows (aplikacje windows i aktualizacje systemu Windows), usługi, pliki i klucze rejestru
- Demony oprogramowania (pakietów) Linuksa i pliki

Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce przeznaczony do instalowania i konfigurowania zbierania spisu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

W tym artykule założono, że masz maszynę wirtualną do skonfigurowania rozwiązania. Jeśli nie masz maszyny wirtualnej platformy Azure, utwórz [maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Włączanie zbierania spisu na stronie zasobów maszyny wirtualnej

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Maszyny wirtualne**.
2. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
3. W menu **Zasób** w obszarze **Operacje**wybierz pozycję **Zapasy**.
4. Wybierz obszar roboczy usługi Log Analytics do przechowywania dzienników danych.
    Jeśli w tym regionie nie ma dostępnego dla Ciebie obszaru roboczego, pojawi się monit o utworzenie domyślnego obszaru roboczego i konta usługi Automation.
5. Aby rozpocząć dołączanie swojego komputera, kliknij pozycję **Włącz**.

   ![Widok opcji dołączania](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Pojawi się pasek stanu z informacją o tym, że trwa włączanie rozwiązania. Ten proces może potrwać do 15 minut. W tym czasie można zamknąć okno lub zachować go otworzyć i powiadamia, gdy rozwiązanie jest włączone. Stan wdrożenia możesz monitorować w okienku powiadomień.

   ![Widok rozwiązania spisu natychmiast po dołączeniu](./media/automation-vm-inventory/inventory-onboarded.png)

Po zakończeniu wdrażania pasek stanu zniknie. System nadal będzie zbierać dane spisu i dane mogą być jeszcze niewidoczne. Zebranie pełnych danych może zająć 24 godziny.

## <a name="configure-your-inventory-settings"></a>Konfigurowanie ustawień spisu

Domyślnie na potrzeby zbierania skonfigurowane są oprogramowanie, usługi systemu Windows oraz demony systemu Linux. Aby zbierać spis dla rejestru systemu Windows i plików, skonfiguruj ustawienia zbierania spisu.

1. W widoku **Zapasy** wybierz przycisk **Edytuj ustawienia** w górnej części okna.
2. Aby dodać nowe ustawienie zbierania, przejdź do kategorii ustawienia, które chcesz dodać, wybierając karty **Rejestr systemu Windows**, **Pliki systemu Windows** oraz **Pliki systemu Linux**.
3. Wybierz odpowiednią kategorię i kliknij przycisk **Dodaj** w górnej części okna.

Poniższe tabele zawierają informacje o każdej właściwości, które mogą być skonfigurowane dla różnych kategorii.

### <a name="windows-registry"></a>Rejestr systemu Windows

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
|Grupa     | Nazwa grupy do logicznego grupowania plików        |
|Klucz rejestru systemu Windows   | Ścieżka do sprawdzania pliku, na przykład: „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Pliki systemu Windows

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
|Grupa     | Nazwa grupy do logicznego grupowania plików        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „c:\temp\mojplik.txt”

### <a name="linux-files"></a>Pliki Linuksa

|Właściwość  |Opis  |
|---------|---------|
|Enabled (Włączony)     | Określa, czy ustawienie jest stosowane        |
|Nazwa elementu     | Przyjazna nazwa pliku, który ma być śledzony        |
|Grupa     | Nazwa grupy do logicznego grupowania plików        |
|Wprowadzanie ścieżki     | Ścieżka do sprawdzania pliku, na przykład: „/etc/*.conf”       |
|Typ ścieżki     | Typ elementu, który ma być monitorowany; możliwe wartości to Plik i Katalog        |
|Rekursja     | Określa, czy podczas wyszukiwania elementu, który ma być śledzony, ma być używana rekursja.        |
|Użyj polecenia Sudo     | To ustawienie określa, czy podczas sprawdzania elementu jest używane polecenie sudo.         |
|Linki     | To ustawienie określa, w jaki sposób są obsługiwane linki symboliczne podczas przechodzenia między katalogami.<br> **Ignoruj** — ignoruje linki symboliczne i nie uwzględnia plików/katalogów, do których się odwołują<br>**Śledź** — śledzi linki symboliczne podczas rekursji i uwzględnia również pliki/katalogi, do których się odwołują<br>**Zarządzaj** — śledzi linki symboliczne i umożliwia obsługę zwracanej zawartości      |

## <a name="manage-machine-groups"></a>Zarządzanie grupami maszyn

Magazyn umożliwia tworzenie i wyświetlanie grup maszyn w dziennikach usługi Azure Monitor. Grupy maszyn są kolekcjami maszyn zdefiniowanych przez kwerendę w dziennikach usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby wyświetlić grupy maszyn, wybierz kartę **Grupy maszyn** na stronie Zapasy.

![Wyświetlanie grup maszyn na stronie zapasów](./media/automation-vm-inventory/inventory-machine-groups.png)

Wybranie grupy maszyn z listy powoduje otwarcie strony Grupy maszyn. Na tej stronie są wyświetlane szczegółowe informacje o grupie maszyn. Te szczegóły obejmują kwerendę analizy dziennika, która jest używana do definiowania grupy. U dołu strony znajduje się stronicowona lista maszyn, które są częścią tej grupy.

![Wyświetlanie strony grupy maszyn](./media/automation-vm-inventory/machine-group-page.png)

Kliknij przycisk **+ Klonuj,** aby sklonować grupę maszyn. W tym miejscu należy nadać grupie nową nazwę i alias grupy. Definicję można zmienić w tej chwili. Po zmianie kwerendy naciśnij **klawisze Validate query,** aby wyświetlić podgląd maszyn, które zostaną wybrane. Po zadowoleniu z grupy kliknij przycisk **Utwórz,** aby utworzyć grupę maszyn

Jeśli chcesz utworzyć nową grupę maszyn, wybierz opcję **+ Utwórz grupę maszyn**. Ten przycisk otwiera **stronę Utwórz grupę maszyn,** na której można zdefiniować nową grupę. Kliknij pozycję **Utwórz**, aby utworzyć grupę.

![Tworzenie nowej grupy maszyn](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odłączanie maszyny wirtualnej od zarządzania

Aby usunąć maszynę wirtualną z zarządzania spisem:

1. W okienku po lewej stronie w witrynie Azure Portal kliknij pozycję **Log Analytics**, a następnie wybierz kliknięciem obszar roboczy, który był używany podczas dołączania danej maszyny wirtualnej.
2. W oknie **Log Analytics** w menu **Zasób** i kategorii **Źródła danych obszaru roboczego** wybierz pozycję **Maszyny wirtualne**.
3. Z listy wybierz maszynę wirtualną, którą chcesz odłączyć. Maszyna wirtualna ma zielony znacznik wyboru obok pozycji **Ten obszar roboczy** w kolumnie **Połączenie OMS**.

   >[!NOTE]
   >Usługa OMS jest teraz określana jako dzienniki usługi Azure Monitor.
   
4. U góry następnej strony wybierz pozycję **Rozłącz**.
5. W oknie potwierdzenia wybierz pozycję **Tak**.
    Ta akcja odłączy maszynę od zarządzania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zarządzaniu zmianami w plikach i ustawieniach rejestru na maszynach wirtualnych, zobacz [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Śledzenie zmian oprogramowania w środowisku za pomocą rozwiązania do śledzenia zmian).
* Aby dowiedzieć się więcej o zarządzaniu systemem Windows i aktualizacjami pakietów na maszynach wirtualnych, zobacz [Rozwiązanie do zarządzania aktualizacjami](../operations-management-suite/oms-solution-update-management.md)na platformie Azure .

