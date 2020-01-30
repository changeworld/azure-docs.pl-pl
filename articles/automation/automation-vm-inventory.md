---
title: Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu | Microsoft Docs
description: Zarządzanie maszyną wirtualną z wykorzystaniem zbierania spisu
services: automation
ms.subservice: change-inventory-management
keywords: spis, automatyzacja, zmiana, śledzenie
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d0324038b8a38d7eba84e5472b8f90439b0322c1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844823"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu

Istnieje możliwość włączenia śledzenia z użyciem spisu dla maszyny wirtualnej platformy Azure na stronie zasobów tej maszyny wirtualnej. Na komputerach można zbierać i przeglądać następujące informacje dotyczące spisu:

- Oprogramowanie systemu Windows (aplikacje systemu Windows i aktualizacje systemu Windows), usługi, pliki i klucze rejestru
- Demony oprogramowania (pakiety) systemu Linux i pliki

Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce przeznaczony do instalowania i konfigurowania zbierania spisu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

W tym artykule założono, że masz maszynę wirtualną, na której ma zostać skonfigurowane rozwiązanie. Jeśli nie masz maszyny wirtualnej platformy Azure, utwórz [maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [portalu Azure](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Włączanie zbierania spisu na stronie zasobów maszyny wirtualnej

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Maszyny wirtualne**.
2. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
3. W menu **zasób** w obszarze **operacje**wybierz pozycję **spis**.
4. Wybierz obszar roboczy Log Analytics do przechowywania dzienników danych.
    Jeśli w tym regionie nie ma dostępnego dla Ciebie obszaru roboczego, pojawi się monit o utworzenie domyślnego obszaru roboczego i konta usługi Automation.
5. Aby rozpocząć dołączanie swojego komputera, kliknij pozycję **Włącz**.

   ![Widok opcji dołączania](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Pojawi się pasek stanu z informacją o tym, że trwa włączanie rozwiązania. Ten proces może potrwać do 15 minut. W tym czasie możesz zamknąć okno lub pozostawić je otwarte i powiadomić Cię, gdy rozwiązanie zostanie włączone. Stan wdrożenia możesz monitorować w okienku powiadomień.

   ![Widok rozwiązania spisu natychmiast po dołączeniu](./media/automation-vm-inventory/inventory-onboarded.png)

Po zakończeniu wdrażania pasek stanu zniknie. System nadal będzie zbierać dane spisu i dane mogą być jeszcze niewidoczne. Zebranie pełnych danych może zająć 24 godziny.

## <a name="configure-your-inventory-settings"></a>Konfigurowanie ustawień spisu

Domyślnie na potrzeby zbierania skonfigurowane są oprogramowanie, usługi systemu Windows oraz demony systemu Linux. Aby zbierać spis dla rejestru systemu Windows i plików, skonfiguruj ustawienia zbierania spisu.

1. W widoku **spisu** wybierz przycisk **Edytuj ustawienia** w górnej części okna.
2. Aby dodać nowe ustawienie zbierania, przejdź do kategorii ustawienia, które chcesz dodać, wybierając karty **Rejestr systemu Windows**, **Pliki systemu Windows** oraz **Pliki systemu Linux**.
3. Wybierz odpowiednią kategorię, a następnie kliknij przycisk **Dodaj** w górnej części okna.

Poniższe tabele zawierają informacje dotyczące poszczególnych właściwości, które można skonfigurować dla różnych kategorii.

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

### <a name="linux-files"></a>Pliki systemu Linux

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

Spis umożliwia tworzenie i przeglądanie grup maszyn w dziennikach Azure Monitor. Grupy maszyn to kolekcje maszyn zdefiniowane przez zapytanie w Azure Monitor dziennikach.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Aby wyświetlić grupy maszyn, wybierz kartę **grupy maszyn** na stronie spisu.

![Wyświetlanie grup maszyn na stronie spisu](./media/automation-vm-inventory/inventory-machine-groups.png)

Wybranie grupy maszyn z listy spowoduje otwarcie strony grupy maszyn. Na tej stronie są wyświetlane szczegółowe informacje o grupie maszyn. Te szczegóły obejmują zapytanie usługi log Analytics, które jest używane do definiowania grupy. W dolnej części strony jest stronicowaną listę maszyn, które są częścią tej grupy.

![Wyświetl stronę grupy maszyn](./media/automation-vm-inventory/machine-group-page.png)

Kliknij przycisk **+ Klonuj** , aby sklonować grupę maszyn. W tym miejscu należy nadać grupie nową nazwę i alias dla grupy. W tej chwili można zmienić definicję. Po zmianie zapytania naciśnij przycisk **Sprawdź poprawność zapytania** , aby wyświetlić podgląd wybranych maszyn. Gdy pozostaniesz zadowolony z grupy, kliknij przycisk **Utwórz** , aby utworzyć grupę maszyn

Jeśli chcesz utworzyć nową grupę maszyn, wybierz pozycję **+ Utwórz grupę maszyn**. Ten przycisk otwiera **stronę Tworzenie grupy maszyn** , na której można zdefiniować nową grupę. Kliknij przycisk **Utwórz** , aby utworzyć grupę.

![Utwórz nową grupę maszyn](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odłączanie maszyny wirtualnej od zarządzania

Aby usunąć maszynę wirtualną z zarządzania spisem:

1. W okienku po lewej stronie w witrynie Azure Portal kliknij pozycję **Log Analytics**, a następnie wybierz kliknięciem obszar roboczy, który był używany podczas dołączania danej maszyny wirtualnej.
2. W oknie **Log Analytics** w menu **Zasób** i kategorii **Źródła danych obszaru roboczego** wybierz pozycję **Maszyny wirtualne**.
3. Z listy wybierz maszynę wirtualną, którą chcesz odłączyć. Maszyna wirtualna ma zielony znacznik wyboru obok pozycji **Ten obszar roboczy** w kolumnie **Połączenie OMS**.

   >[!NOTE]
   >Pakiet OMS jest teraz nazywany dziennikami Azure Monitor.
   
4. U góry następnej strony wybierz pozycję **Rozłącz**.
5. W oknie potwierdzenia wybierz pozycję **Tak**.
    Ta akcja odłączy maszynę od zarządzania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o zarządzaniu zmianami w plikach i ustawieniach rejestru na maszynach wirtualnych, zobacz [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Śledzenie zmian oprogramowania w środowisku za pomocą rozwiązania do śledzenia zmian).
* Aby dowiedzieć się więcej na temat zarządzania aktualizacjami systemu Windows i pakietów na maszynach wirtualnych, zobacz [rozwiązanie Update Management na platformie Azure](../operations-management-suite/oms-solution-update-management.md).

