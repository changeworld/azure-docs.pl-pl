---
title: Uściślij grupę oceny za pomocą mapowania zależności grup w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak uzyskiwać dokładniejsze oceny za pomocą mapowania zależności dla grup w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 09/17/2018
ms.author: raynew
ms.openlocfilehash: 4f5ab4565191b38c07b2071609a57db2525860e3
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733418"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Uściślanie zawartości grupy za pomocą mapowania zależności grupy

W tym artykule opisano Uściślanie zawartości grupy przez wizualizację zależności dla wszystkich maszyn w grupie. Ta metoda jest zazwyczaj używana, gdy zajdzie potrzeba doprecyzowania członkostwa dla istniejącej grupy przez krzyżowe sprawdzanie zależności między grupami, przed uruchomieniem oceny. Rafinacja grupy za pomocą wizualizacji zależności mogą pomóc efektywnego planowania migracji do platformy Azure. Może odnajdywać wszystkie współzależne systemy, które chcesz zmigrować razem. Ułatwia on upewnij się, że nic nie pozostawione awarii dziwnego, że nie występują podczas migracji na platformę Azure.


> [!NOTE]
> Grupy, dla których chcesz zwizualizować zależności nie powinien zawierać więcej niż 10 maszyny. Jeśli masz więcej niż 10 maszyn w grupie, firma Microsoft zaleca, aby podzielić ją na mniejszych grup, aby korzystać z funkcji wizualizacji zależności.


## <a name="prepare-for-dependency-visualization"></a>Przygotowanie do wizualizacji zależności
Usługa Azure Migrate korzysta z rozwiązania mapy usługi w usłudze Log Analytics, aby umożliwić wizualizacja zależności maszyn.

### <a name="associate-a-log-analytics-workspace"></a>Skojarzyć obszar roboczy usługi Log Analytics
Aby korzystać z wizualizacji zależności, należy skojarzyć obszar roboczy usługi Log Analytics, nowej lub istniejącej z projektem migracji platformy Azure. Możesz tworzyć lub dołączyć obszaru roboczego w tej samej subskrypcji, w której jest tworzony projekt migracji.

- Aby dołączyć obszar roboczy usługi Log Analytics do projektu, w **Przegląd**, przejdź do **Essentials** części projektu, kliknij przycisk **wymaga konfiguracji**

    ![Skojarzyć obszar roboczy usługi Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Gdy tworzysz nowy obszar roboczy, musisz określić nazwę obszaru roboczego. Obszar roboczy zostanie utworzony w tej samej subskrypcji co projekt migracji i region, w tym samym [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) jako projekt migracji.
- **Użyj istniejącej** opcji wyświetla tylko te obszary robocze, które są tworzone w regionach, w których jest dostępna usługa Service Map. Jeśli masz obszar roboczy w regionie, w których nie jest dostępna usługa Service Map, nie będzie ono wyświetlane w polu listy rozwijanej.

> [!NOTE]
> Nie można zmienić obszar roboczy skojarzony z projektem migracji.

### <a name="download-and-install-the-vm-agents"></a>Pobieranie i instalowanie agentów maszyny wirtualnej
Aby wyświetlić zależności grupy, musisz pobrać i zainstalować agentów na każdym komputerze w środowisku lokalnym, który jest częścią grupy. Ponadto w przypadku maszyn bez łączności internetowej musisz pobrać i zainstalować [bramy pakietu OMS](../log-analytics/log-analytics-oms-gateway.md) na nich.

1. W **Przegląd**, kliknij przycisk **Zarządzaj** > **grup**, przejdź do wymaganej grupy.
2. Na liście maszyn wirtualnych w **agenta zależności** kolumny, kliknij przycisk **wymaga instalacji** Aby uzyskać instrukcje dotyczące sposobu pobierania i instalowania agentów.
3. Na **zależności** strony, Pobierz i zainstaluj program Microsoft Monitoring Agent (MMA) i agenta zależności na każdej maszynie Wirtualnej, która jest częścią grupy.
4. Skopiuj identyfikator i klucz obszaru roboczego. Będą one potrzebne po zainstalowaniu programu MMA na maszynach w środowisku lokalnym.

### <a name="install-the-mma"></a>Instalowanie programu MMA

Aby zainstalować agenta na komputerze Windows:

1. Kliknij dwukrotnie pobranego agenta.
2. Na **stronie powitalnej** kliknij przycisk **Dalej**. Na stronie **Postanowienia licencyjne** kliknij przycisk **Zgadzam się**, aby zaakceptować warunki licencji.
3. W **Folder docelowy**, zachować lub zmienić domyślny folder instalacji > **dalej**.
4. W **opcje instalacji agenta**, wybierz opcję **usługi Azure Log Analytics** > **dalej**.
5. Kliknij przycisk **Dodaj** Aby dodać nowy obszar roboczy usługi Log Analytics. Wklej identyfikator obszaru roboczego i klucz, który został skopiowany z portalu. Kliknij przycisk **Dalej**.


Aby zainstalować agenta na maszynie z systemem Linux:

1. Przenieś odpowiedni zbiór (x86 lub x64), aby komputer z systemem Linux przy użyciu protokołu scp/sftp.
2. Zainstaluj zbiór przy użyciu argumentu--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>Instalowanie agenta zależności
1. Aby zainstalować agenta zależności na komputerze Windows, kliknij dwukrotnie plik instalacyjny i wykonaj instrukcje kreatora.
2. Aby zainstalować agenta zależności na maszynę z systemem Linux, zainstaluj jako użytkownik główny, używając następującego polecenia:

    ```sh InstallDependencyAgent-Linux64.bin```

Dowiedz się więcej o obsłudze agenta zależności [Windows](../monitoring/monitoring-service-map-configure.md#supported-windows-operating-systems) i [Linux](../monitoring/monitoring-service-map-configure.md#supported-linux-operating-systems) systemów operacyjnych.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Dostosować grupę, w oparciu o wizualizacji zależności
Agenci zainstalowani na wszystkich komputerach w grupie możesz wizualizować zależności grupy i dostosuj je, wykonując poniższe kroki.

1. Na platformie Azure migracji projektu, w obszarze **Zarządzaj**, kliknij przycisk **grup**, a następnie wybierz grupę.
2. Na stronie grupy kliknij **wyświetlanie zależności**, aby otworzyć mapowania zależności grupy.
3. Mapa zależności dla grupy zawiera następujące informacje:
    - Dla ruchu przychodzącego (klientów) oraz połączenia wychodzące TCP (serwery), do i z wszystkich maszyn, które są częścią grupy
        - Zależnych maszyn, które nie mają zainstalowany agent MMA i zależności są pogrupowane według numerów portów
        - Zależnych maszyn, które mają programu MMA i zainstalować agenta zależności są wyświetlane jako oddzielne pola
    - Procesy uruchomione na maszynie, możesz rozwinąć każde pole maszyny do wyświetlania procesów
    - Właściwości, takie jak w pełni kwalifikowanej nazwy domeny, System operacyjny i każdej maszyny, itp. adres MAC, możesz kliknąć pole każdej maszyny w taki sposób, aby wyświetlić te szczegóły

     ![Wyświetlanie zależności grupowych](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Aby wyświetlić bardziej szczegółowe zależności, kliknij zakres czasu, aby go zmodyfikować. Domyślnie zakres jest godzinę. Możesz modyfikować zakres czasu lub określ rozpoczęcia i zakończenia daty i czasu trwania.
4. Sprawdź zależnych maszyn proces uruchomiony na każdej maszynie, a następnie zidentyfikuje maszyny, które powinny zostać dodane lub usunięte z grupy.
5. Użyj klawiszy Ctrl + kliknięcie, aby wybrać maszyny na mapie, aby dodać lub usunąć je z niej.
    - Można dodawać tylko te maszyny, które zostały odnalezione.
    - Dodawanie i usuwanie maszyn w grupie unieważnia ostatnie ocen dla niego.
    - Po zmodyfikowaniu grupy, można opcjonalnie utworzyć nowego rozwiązania do oceny.
5. Kliknij przycisk **OK** można zapisać grupy.

    ![Dodawanie lub usuwanie komputerów](./media/how-to-create-group-dependencies/add-remove.png)

Jeśli chcesz sprawdzić zależności określonej maszyny, która pojawia się na mapie zależności grupy [Konfigurowanie mapowania zależności maszyn](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat sposobu obliczania ocen.
