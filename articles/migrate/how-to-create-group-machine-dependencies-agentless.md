---
title: Konfigurowanie analizy zależności bez agenta w usłudze Azure Migrate Server Assessment
description: Konfigurowanie analizy zależności bez agenta w usłudze Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455710"
---
# <a name="set-up-agentless-dependency-visualization"></a>Konfigurowanie wizualizacji zależności bez agenta 

W tym artykule opisano sposób konfigurowania analizy zależności bez agenta w usłudze Azure Migrate:Server Assessment. [Analiza zależności](concepts-dependency-visualization.md) ułatwia identyfikowanie i rozumienie zależności między komputerami, które chcesz ocenić i przeprowadzić migrację na platformę Azure.


> [!IMPORTANT]
> Wizualizacja zależności bez agenta jest obecnie w wersji zapoznawczej tylko dla maszyn wirtualnych VMware, odnalezionych za pomocą narzędzia Azure Migrate:Server Assessment.
> Funkcje mogą być ograniczone lub niekompletne.
> Ta wersja zapoznawcza jest objęta obsługą klienta i może być używana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej o](concepts-dependency-visualization.md#agentless-analysis) analizie zależności bez agenta.
- [Przejrzyj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) wymagania wstępne i wymagania dotyczące pomocy technicznej dotyczące konfigurowania wizualizacji zależności bez agenta dla maszyn wirtualnych VMware
- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt migracji platformy Azure.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) narzędzie oceny migracji:serwer platformy Azure.
- Upewnij się, że skonfigurowałeś [urządzenie migracji platformy Azure](migrate-appliance.md) do odnajdywanie komputerów lokalnych. Dowiedz się, jak skonfigurować urządzenie dla maszyn wirtualnych [VMware.](how-to-set-up-appliance-vmware.md) Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do usługi Azure Migrate:Server Assessment.


## <a name="current-limitations"></a>Bieżące ograniczenia

- W tej chwili nie można dodać lub usunąć serwera z grupy w widoku analizy zależności.
- Mapa zależności dla grupy serwerów nie jest obecnie niedostępna.
- Obecnie danych zależności nie można pobrać w formacie tabelarycznym.

## <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika do odnajdowania

Skonfiguruj konto użytkownika, aby ocena serwera mogła uzyskać dostęp do maszyny Wirtualnej w celu odnajdywania. [Dowiedz się więcej](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) o wymaganiach dotyczących konta.


## <a name="add-the-user-account-to-the-appliance"></a>Dodawanie konta użytkownika do urządzenia

Dodaj konto użytkownika do urządzenia.

1. Otwórz aplikację do zarządzania urządzeniami. 
2. Przejdź do panelu **Szczegóły funkcji Podaj vCenter.**
3. W **discover aplikacji i zależności na maszynach wirtualnych**kliknij przycisk Dodaj **poświadczenia**
3. Wybierz **system operacyjny**, podaj przyjazną nazwę konta i **nazwę**/użytkownika**Hasło**
6. Kliknij przycisk **Zapisz**.
7. Kliknij **przycisk Zapisz i rozpocznij odnajdowanie**.

    ![Dodawanie konta użytkownika maszyny Wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Rozpoczynanie odnajdywania zależności

Wybierz maszyny, na których chcesz włączyć odnajdowanie zależności.

1. W **obszarze Migracja platformy Azure: Ocena serwera**kliknij pozycję **Odnalezione serwery**.
2. Kliknij ikonę **Analiza zależności.**
3. Kliknij **pozycję Dodaj serwery**.
3. Na stronie **Dodaj serwery** wybierz urządzenie, które odnajduje odpowiednie maszyny.
4. Z listy maszyn wybierz maszyny.
5. Kliknij **pozycję Dodaj serwery**.

    ![Rozpoczynanie odnajdywania zależności](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Można wizualizować zależności około sześciu godzin po rozpoczęciu odnajdywania zależności.

## <a name="visualize-dependencies"></a>Wizualizuj zależności

1. W **obszarze Migracja platformy Azure: Ocena serwera**kliknij pozycję **Odnalezione serwery**.
2. Wyszukaj maszynę, którą chcesz wyświetlić.
3. W kolumnie **Zależności** kliknij pozycję **Wyświetl zależności**
4. Zmień okres, dla którego chcesz wyświetlić mapę, korzystając z listy rozwijanej **Czas trwania.**
5. Rozwiń **grupę Klient,** aby wyświetlić listę komputerów z zależnością od wybranego komputera.
6. Rozwiń grupę **Port,** aby wyświetlić listę komputerów, które mają zależność od wybranego komputera.
7. Aby przejść do widoku mapy dowolnej z zależnych maszyn, kliknij nazwę komputera > **Załaduj mapę serwera**

    ![Rozwiń grupę portów serwera i załaduj mapę serwera](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozwiń grupę klientów ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozwiń wybrany komputer, aby wyświetlić szczegóły na poziomie procesu dla każdej zależności.

    ![Rozwiń serwer, aby wyświetlić procesy](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informacje o procesie zależności nie zawsze są dostępne. Jeśli nie jest dostępna, zależność jest przedstawiona z procesem oznaczonym jako "Nieznany proces".

## <a name="stop-dependency-discovery"></a>Zatrzymywanie odnajdywania zależności

Wybierz maszyny, na których chcesz zatrzymać odnajdowanie zależności.

1. W **obszarze Migracja platformy Azure: Ocena serwera**kliknij pozycję **Odnalezione serwery**.
2. Kliknij ikonę **Analiza zależności.**
3. Kliknij pozycję **Usuń serwery**.
3. Na stronie **Usuń serwery** wybierz **urządzenie,** które odnajduje maszyny wirtualne, na których chcesz zatrzymać odnajdowanie zależności.
4. Z listy maszyn wybierz maszyny.
5. Kliknij pozycję **Usuń serwery**.


## <a name="next-steps"></a>Następne kroki

[Zgrupuj maszyny do](how-to-create-a-group.md) oceny.
