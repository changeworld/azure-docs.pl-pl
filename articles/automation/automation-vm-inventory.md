---
title: Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu | Microsoft Docs
description: Zarządzanie maszyną wirtualną z wykorzystaniem zbierania spisu
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: spis, automatyzacja, zmiana, śledzenie
author: jennyhunter-msft
ms.author: jehunte
ms.date: 02/06/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59f36595e0b6cc8b9d9ea0669c9ecb5be1e74b42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61304154"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Zarządzanie maszyną wirtualną platformy Azure z wykorzystaniem zbierania spisu

Istnieje możliwość włączenia śledzenia z użyciem spisu dla maszyny wirtualnej platformy Azure na stronie zasobów tej maszyny wirtualnej. Możesz zbierać i wyświetlać spis oprogramowania, plików, demonów systemu Linux, usług systemu Windows i kluczy rejestru systemu Windows znajdujących się na Twoich komputerach. Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce przeznaczony do instalowania i konfigurowania zbierania spisu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

W tym artykule przyjęto założenie, że masz maszynę Wirtualną do konfigurowania rozwiązania na. Jeśli nie masz maszyny wirtualnej platformy Azure, utwórz [maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Włączanie zbierania spisu na stronie zasobów maszyny wirtualnej

1. W okienku po lewej stronie witryny Azure Portal wybierz pozycję **Maszyny wirtualne**.
2. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
3. Na **zasobów** menu, w obszarze **operacji**, wybierz opcję **spisu**.
4. Wybierz obszar roboczy usługi Log Analytics do przechowywania danych dzienników.
    Jeśli w tym regionie nie ma dostępnego dla Ciebie obszaru roboczego, pojawi się monit o utworzenie domyślnego obszaru roboczego i konta usługi Automation.
5. Aby rozpocząć dołączanie swojego komputera, kliknij pozycję **Włącz**.

   ![Widok opcji dołączania](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Pojawi się pasek stanu z informacją o tym, że trwa włączanie rozwiązania. Ten proces może potrwać do 15 minut. W tym czasie możesz zamknąć okno lub zostawić je otwarte i powiadomi użytkownika po włączeniu rozwiązania. Stan wdrożenia możesz monitorować w okienku powiadomień.

   ![Widok rozwiązania spisu natychmiast po dołączeniu](./media/automation-vm-inventory/inventory-onboarded.png)

Po zakończeniu wdrażania pasek stanu zniknie. System nadal będzie zbierać dane spisu i dane mogą być jeszcze niewidoczne. Zebranie pełnych danych może zająć 24 godziny.

## <a name="configure-your-inventory-settings"></a>Konfigurowanie ustawień spisu

Domyślnie na potrzeby zbierania skonfigurowane są oprogramowanie, usługi systemu Windows oraz demony systemu Linux. Aby zbierać spis dla rejestru systemu Windows i plików, skonfiguruj ustawienia zbierania spisu.

1. W **spisu** widoku, wybierz opcję **edytowanie ustawień** znajdujący się u góry okna.
2. Aby dodać nowe ustawienie zbierania, przejdź do kategorii ustawienia, które chcesz dodać, wybierając karty **Rejestr systemu Windows**, **Pliki systemu Windows** oraz **Pliki systemu Linux**.
3. Wybierz odpowiednią kategorię, a następnie kliknij przycisk **Dodaj** w górnej części okna.

Poniższe tabele zawierają informacje dotyczące każdej właściwości, które można skonfigurować dla różnych kategorii.

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

## <a name="manage-machine-groups"></a>Zarządzaj grupami maszyn

Spis umożliwia tworzenie i wyświetlanie grupy maszyn w dziennikach w usłudze Azure Monitor. Grupy maszyn są kolekcjami maszynach zdefiniowanych przez zapytanie w dziennikach w usłudze Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Na komputerze grupy wybierz widok **maszyny grupy** karty na stronie spisu.

![Widok grupy maszyn, na stronie spisu](./media/automation-vm-inventory/inventory-machine-groups.png)

Wybór grupy maszyn z listy spowoduje otwarcie strony grupy maszyn. Ta strona zawiera szczegółowe informacje o grupie maszyn. Obejmują one zapytanie analizy dziennika, który jest używany do definiowania grupy. W dolnej części strony jest listą stronicowane maszyn, które należą do tej grupy.

![Wyświetl stronę grupy maszyn](./media/automation-vm-inventory/machine-group-page.png)

Kliknij przycisk **+ klonowania** przycisk, aby sklonować grupy maszyn. W tym miejscu należy nadać grupie nową nazwę i alias dla grupy. Definicja może się zmienić w tej chwili. Po zmianie naciśnij zapytania **zapytanie weryfikacji** nad wersją zapoznawczą maszyn, które będzie można wybrać. Gdy jesteś zadowolony z grupy kliknij **Utwórz** można utworzyć grupy maszyn

Jeśli chcesz utworzyć nową grupę maszyny wybierz **+ Utwórz grupę maszyny**. Ten przycisk otwiera **Utwórz stronę grupy maszyny** gdzie można zdefiniować nowej grupy. Kliknij przycisk **Utwórz** do utworzenia grupy.

![Utwórz nową grupę maszyn](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Odłączanie maszyny wirtualnej od zarządzania

Aby usunąć maszynę wirtualną z zarządzania spisem:

1. W okienku po lewej stronie w witrynie Azure Portal kliknij pozycję **Log Analytics**, a następnie wybierz kliknięciem obszar roboczy, który był używany podczas dołączania danej maszyny wirtualnej.
2. W oknie **Log Analytics** w menu **Zasób** i kategorii **Źródła danych obszaru roboczego** wybierz pozycję **Maszyny wirtualne**.
3. Z listy wybierz maszynę wirtualną, którą chcesz odłączyć. Maszyna wirtualna ma zielony znacznik wyboru obok pozycji **Ten obszar roboczy** w kolumnie **Połączenie OMS**.

   >[!NOTE]
   >Pakiet OMS jest teraz nazywana dzienniki usługi Azure Monitor.
   
4. U góry następnej strony wybierz pozycję **Rozłącz**.
5. W oknie potwierdzenia wybierz pozycję **Tak**.
    Ta akcja odłączy maszynę od zarządzania.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o zarządzaniu zmianami w plikach i ustawieniach rejestru na maszynach wirtualnych, zobacz [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Śledzenie zmian oprogramowania w środowisku za pomocą rozwiązania do śledzenia zmian).
* Aby dowiedzieć się więcej o zarządzaniu Windows i aktualizacjami pakietów na maszynach wirtualnych, zobacz [rozwiązanie do zarządzania aktualizacjami w usłudze Azure](../operations-management-suite/oms-solution-update-management.md).

