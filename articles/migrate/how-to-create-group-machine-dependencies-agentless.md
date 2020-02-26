---
title: Konfigurowanie wizualizacji zależności bez agentów w Azure Migrate
description: Skonfiguruj grupy przy użyciu wizualizacji zależności bez agenta w ramach oceny serwera Azure Migrate.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589134"
---
# <a name="set-up-agentless-dependency-visualization"></a>Konfigurowanie wizualizacji zależności bez agenta 

W tym artykule opisano sposób konfigurowania wizualizacji zależności w Azure Migrate: Ocena serwera. [Wizualizacja zależności](concepts-dependency-visualization.md#what-is-dependency-visualization) ułatwia identyfikowanie i zrozumienie zależności między maszynami, które mają być oceniane i migrowane na platformę Azure.

Wizualizacja zależności bez wykorzystania agentów pomaga identyfikować zależności maszyn, nie instalując żadnych agentów na komputerach. Działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona.

> [!IMPORTANT]
> Wizualizacja zależności bez agenta jest obecnie dostępna tylko w wersji zapoznawczej dla maszyn wirtualnych VMware platformy Azure, która została odnaleziona przy użyciu narzędzia do oceny serwera Azure Migrate:.
> Funkcje mogą być ograniczone lub niekompletne.
> Ta wersja zapoznawcza jest objęta wsparciem klienta i może być używana na potrzeby obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Bieżące ograniczenia

- Teraz nie można dodać ani usunąć serwera z grupy w widoku Analiza zależności.
- Mapa zależności dla grupy serwerów nie jest obecnie dostępna.
- Obecnie nie można pobrać danych zależności w formacie tabelarycznym.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Zapoznaj](concepts-dependency-visualization.md#agentless-visualization) się z wymaganiami i kosztami związanymi z wizualizacją zależności bez agenta.
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](migrate-support-matrix-vmware.md#agentless-dependency-visualization) konfiguracji wizualizacji zależności bez agenta.
- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Upewnij się, że skonfigurowano [urządzenie Azure Migrate](migrate-appliance.md) w celu odnajdywania maszyn lokalnych. Dowiedz się, jak skonfigurować urządzenie dla maszyn wirtualnych [VMware](how-to-set-up-appliance-vmware.md) . Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera.


## <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika na potrzeby odnajdywania

Skonfiguruj konto użytkownika, aby Ocena serwera mogła uzyskać dostęp do maszyny wirtualnej w celu odnajdywania. Możesz określić jedno konto użytkownika.

- **Maszyny wirtualne z systemem Windows**: konto użytkownika musi być kontem lokalnym lub administratorem domeny.
- **Maszyny wirtualne z systemem Linux**: uprawnienie główne jest wymagane na koncie. Alternatywnie konto użytkownika wymaga tych dwóch możliwości w plikach/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Dodaj konto użytkownika do urządzenia

Dodaj konto użytkownika do urządzenia.

1. Otwórz aplikację zarządzanie urządzeniami. 
2. Przejdź do panelu **Podaj szczegóły programu vCenter** .
3. W obszarze **Znajdź aplikację i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia** .
3. Wybierz **system operacyjny**, podaj przyjazną nazwę konta i **nazwę użytkownika**/**hasło**
6. Kliknij przycisk **Save** (Zapisz).
7. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**.

    ![Dodaj konto użytkownika maszyny wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Uruchom odnajdywanie zależności

Wybierz maszyny, na których chcesz włączyć odnajdowanie zależności.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij pozycję **Dodaj serwery**.
3. Na stronie **Dodawanie serwerów** wybierz urządzenie, które odnajduje odpowiednie maszyny.
4. Z listy maszyna wybierz maszyny.
5. Kliknij pozycję **Dodaj serwery**.

    ![Uruchom odnajdywanie zależności](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Możesz wizualizować zależności na sześć godzin po rozpoczęciu odnajdywania zależności.

## <a name="visualize-dependencies"></a>Wizualizacja zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Wyszukaj maszynę, którą chcesz wyświetlić.
3. W kolumnie **zależności** kliknij pozycję **Wyświetl zależności.**
4. Zmień okres, dla którego ma zostać wyświetlona mapa, przy użyciu listy rozwijanej czas **trwania** .
5. Rozwiń grupę **klientów** , aby wyświetlić listę maszyn z zależnością na wybranym komputerze.
6. Rozwiń grupę **portów** , aby wyświetlić listę maszyn z zależnością od wybranej maszyny.
7. Aby przejść do widoku mapy dowolnych maszyn zależnych, kliknij nazwę komputera > **Załaduj mapę serwera**

    ![Rozwiń węzeł Grupa portów serwera i Załaduj mapę serwera](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozwiń grupę klientów ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozwiń wybraną maszynę, aby wyświetlić szczegóły poziomu procesu dla każdej zależności.

    ![Rozwiń serwer, aby wyświetlić procesy](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informacje o procesie dla zależności nie są zawsze dostępne. Jeśli ta funkcja jest niedostępna, zależność jest przedstawiana z procesem oznaczonym jako "nieznany proces".

## <a name="stop-dependency-discovery"></a>Zatrzymaj odnajdywanie zależności

Wybierz maszyny, na których chcesz zatrzymać odnajdywanie zależności.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij przycisk **Usuń serwery**.
3. Na stronie **usuwanie serwerów** wybierz **urządzenie** , które odnajduje maszyny wirtualne, na których chcesz zatrzymać odnajdywanie zależności.
4. Z listy maszyna wybierz maszyny.
5. Kliknij przycisk **Usuń serwery**.


## <a name="next-steps"></a>Następne kroki

[Grupuj maszyny](how-to-create-a-group.md) do oceny.
