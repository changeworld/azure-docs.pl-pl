---
title: Grupuj maszyny w Azure Migrate przy użyciu wizualizacji zależności bez agenta
description: Opisuje sposób tworzenia grup przy użyciu zależności maszyn w sposób bezzależny od agenta.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: b13ad375adf20a606b48755a6d57851abf89e549
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454652"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Konfigurowanie wizualizacji zależności bez agenta na potrzeby oceny

W tym artykule opisano sposób konfigurowania mapowania zależności bez agenta w Azure Migrate: Ocena serwera. Ta funkcja jest obecnie dostępna w wersji zapoznawczej dla maszyn VMware odnalezionych za pomocą urządzenia Azure Migrate. 

> [!IMPORTANT]
> Wizualizacja zależności bez agenta jest obecnie dostępna w wersji zapoznawczej dla maszyn wirtualnych VMware platformy Azure odnalezionych za pomocą urządzenia Azure Migrate.
> Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Ta wersja zapoznawcza jest objęta wsparciem klienta i może być używana na potrzeby obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>Mapowanie zależności — informacje

Mapowanie zależności ułatwia wizualizację zależności między maszynami, które mają zostać poddane ocenie i przeprowadzeniem migracji. Mapowanie zależności zazwyczaj jest stosowane, gdy chcesz ocenić komputery z wyższym poziomem zaufania.

- W Azure Migrate: Ocena serwera umożliwia zebranie maszyn w grupach w celu oceny. Grupy zwykle składają się z maszyn, które mają zostać poddane migracji, a mapowanie zależności ułatwia sprawdzanie zależności między maszynami, dzięki czemu można dokładnie grupować maszyny.
- Korzystając z mapowania, można odnajdywać systemy zależne, które muszą zostać zmigrowane wspólnie. Możesz również określić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidacji zamiast migracji.
- Wizualizacja zależności pomaga upewnić się, że nic nie zostało pozostawione, i uniknąć awarii podczas migracji.

## <a name="about-agentless-visualization"></a>Informacje o wizualizacji bez agentów

Wizualizacja zależności bez agenta nie wymaga instalowania agentów na komputerach. Działa przez przechwytywanie danych połączenia TCP z maszyn, dla których jest włączona.

- Po rozpoczęciu odnajdywania zależności urządzenie zbiera dane z maszyn z interwałem sondowania równym pięć minut.
- Zbierane są następujące dane:
    - Połączenia protokołu TCP
    - Nazwy procesów, które mają aktywne połączenia
    - Nazwy zainstalowanych aplikacji, które uruchamiają powyższe procesy
    - Nie. wykrytych połączeń podczas każdego interwału sondowania

## <a name="current-limitations"></a>Bieżące ograniczenia

- Wizualizacja zależności bez agenta jest obecnie dostępna tylko dla maszyn wirtualnych VMware.
- Teraz nie można dodać ani usunąć serwera z grupy w widoku Analiza zależności.
- Mapa zależności grupy serwerów jest obecnie niedostępna.
- Obecnie nie można pobrać danych zależności w formacie tabelarycznym.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Analiza zależności bez agenta jest obecnie dostępna tylko dla maszyn VMware.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Upewnij się, że maszyny wirtualne zostały odnalezione w Azure Migrate; w tym celu można skonfigurować urządzenie Azure Migrate dla programu [VMware](how-to-set-up-appliance-vmware.md). Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera. [Dowiedz się więcej](migrate-appliance.md).
- Upewnij się, że maszyny wirtualne VMware są obsługiwane dla wizualizacji zależności bez agenta, jak przedstawiono w poniższej tabeli.


### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane systemy operacyjne dla wizualizacji zależności bez agenta są następujące.

**Typ** | **Obsługiwane systemy operacyjne**
--- | ---
**Windows** | Microsoft Windows Server 2016 <br/> System Microsoft Windows Server 2012 R2 <br/> Microsoft Windows Server 2012 <br/> Microsoft Windows Server 2008 R2 (64-bit)
**Linux** | Red Hat Enterprise Linux 7, 6, 5 <br/> Ubuntu Linux 14,04, 16,04 <br/> Debian 7, 8 <br/> Oracle Linux 6, 7 <br/> CentOS 5, 6, 7  


## <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika na potrzeby odnajdywania

Skonfiguruj konto użytkownika, które ma wymagane uprawnienia, aby Ocena serwera mogła uzyskać dostęp do maszyny wirtualnej w celu odnajdywania. Możesz określić jedno konto użytkownika.

- **Wymagane uprawnienie na maszynach wirtualnych z systemem Windows**: konto użytkownika wymaga dostępu "Gość".
- **Wymagane uprawnienie na maszynach wirtualnych z systemem Linux**: wymagane jest uprawnienie główne na tym koncie. Alternatywnie konto użytkownika wymaga tych dwóch możliwości w plikach/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Dodaj konto użytkownika do urządzenia

Musisz dodać konto użytkownika do urządzenia.

Dodaj konto w następujący sposób:

1. Otwórz aplikację zarządzanie urządzeniami. Przejdź do panelu **Podaj szczegóły programu vCenter** .
2. W sekcji **odnajdywanie aplikacji i zależności dla maszyn wirtualnych** kliknij pozycję **Dodaj poświadczenia** .
3. Wybierz **system operacyjny**.
4. Podaj przyjazną nazwę dla konta.
5. Podaj **nazwę użytkownika** i **hasło**
6. Kliknij pozycję **Zapisz**.
7. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**.

    ![Dodaj konto użytkownika maszyny wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Uruchom odnajdywanie zależności

Wybierz maszyny, na których chcesz włączyć odnajdowanie zależności.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij pozycję **Rozpocznij odnajdywanie zależności**.
3. Na stronie **wykrywanie zależności uruchamiania** wybierz urządzenie, które odnajduje odpowiednie maszyny.
4. Z listy maszyna wybierz maszyny.
5. Kliknij pozycję **Rozpocznij odnajdywanie zależności**.

    ![Uruchom odnajdywanie zależności](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Po rozpoczęciu odnajdywania zależności będzie można wizualizować zależności 6 godzin.

## <a name="visualize-dependencies"></a>Wizualizowanie zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Wyszukaj maszynę, dla której chcesz wyświetlić mapę zależności.
3. Kliknij pozycję **Wyświetl zależności** w kolumnie **zależności** .
4. Zmień okres, dla którego ma zostać wyświetlona mapa, przy użyciu listy rozwijanej czas **trwania** .
5. Rozwiń grupę **klientów** , aby wyświetlić listę maszyn, które mają zależność na wybranym komputerze.
6. Rozwiń grupę **portów** , aby wyświetlić listę maszyn z zależnością od wybranej maszyny.
7. Aby przejść do widoku mapy dowolnych maszyn zależnych, kliknij nazwę komputera, a następnie kliknij pozycję **Załaduj mapę serwera**

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
3. Kliknij przycisk **Zatrzymaj odnajdywanie zależności**.
3. Na stronie **Zatrzymaj odnajdywanie zależności** wybierz **urządzenie** , które odnajduje maszyny wirtualne, na których chcesz zatrzymać odnajdywanie zależności.
4. Z listy maszyna wybierz maszyny.
5. Kliknij przycisk **Zatrzymaj odnajdywanie zależności**


## <a name="next-steps"></a>Następne kroki

[Grupowanie maszyn](how-to-create-a-group.md)
