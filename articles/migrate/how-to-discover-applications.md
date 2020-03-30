---
title: Odnajdowanie aplikacji, ról i funkcji na serwerach lokalnych za pomocą usługi Azure Migrate
description: Dowiedz się, jak odnajdować aplikacje, role i funkcje na serwerach lokalnych za pomocą usługi Azure Migrate Server Assessment.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453586"
---
# <a name="discover-machine-apps-roles-and-features"></a>Odnajduj aplikacje maszynowe, role i funkcje

W tym artykule opisano sposób odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu usługi Azure Migrate: Server Assessment.

Odnajdowanie spisu aplikacji i ról/funkcji działających na komputerach lokalnych pomaga zidentyfikować i zaplanować ścieżkę migracji do platformy Azure dostosowaną do obciążeń.

> [!NOTE]
> Odnajdowanie aplikacji jest obecnie w wersji zapoznawczej tylko dla maszyn wirtualnych VMware i jest ograniczone tylko do odnajdowania. Nie oferujemy jeszcze oceny opartej na aplikacjach. Ocena oparta na maszynach dla lokalnych maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper V i serwerów fizycznych.

Odnajdowanie aplikacji przy użyciu usługi Azure Migrate: Ocena serwera jest bez agenta. Nic nie jest zainstalowane na maszynach i maszynach wirtualnych. Ocena serwera używa urządzenia migracji platformy Azure do odnajdywania wraz z poświadczeniami gościa komputera. Urządzenie zdalnie uzyskuje dostęp do maszyn VMware przy użyciu interfejsów API VMware.


## <a name="before-you-start"></a>Przed rozpoczęciem

1. Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt migracji platformy Azure.
2. Upewnij się, [added](how-to-assess.md) że dodano narzędzie Do oceny migracji platformy Azure: Serwer do projektu.
4. Sprawdź [wymagania VMware](migrate-support-matrix-vmware.md#vmware-requirements) dotyczące odnajdywania i oceny maszyn wirtualnych VMware za pomocą urządzenia migracji platformy Azure.
5. Sprawdź [wymagania dotyczące](migrate-appliance.md) wdrażania urządzenia migracji platformy Azure.
6. [Sprawdź obsługę i wymagania dotyczące](migrate-support-matrix-vmware.md#application-discovery) odnajdowania aplikacji.

## <a name="prepare-for-app-discovery"></a>Przygotowanie do odnajdowania aplikacji

1. [Przygotuj się do wdrożenia urządzenia](tutorial-prepare-vmware.md). Przygotowanie obejmuje sprawdzanie ustawień urządzenia i konfigurowanie konta, którego urządzenie będzie używać do uzyskiwania dostępu do serwera vCenter Server.
2. Upewnij się, że masz konto użytkownika (po jednym dla serwerów Windows i Linux) z uprawnieniami administratora dla komputerów, na których chcesz odkryć aplikacje, role i funkcje.
3. [Wdrażanie urządzenia migracji platformy Azure,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdowanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon OVA do VMware, aby utworzyć urządzenie jako VMware VMware VM. Skonfiguruj urządzenie, a następnie zarejestruj go za pomocą usługi Azure Migrate.
2. Podczas wdrażania urządzenia, aby rozpocząć ciągłe odnajdowanie, należy określić następujące kwestie:
    - Nazwa serwera vCenter, z którym chcesz się połączyć.
    - Poświadczenia utworzone dla urządzenia w celu nawiązania połączenia z serwerem vCenter Server.
    - Poświadczenia konta utworzone dla urządzenia w celu nawiązania połączenia z maszynami wirtualnymi z systemem Windows/Linux.

Po wdrożeniu urządzenia i poświadczeniu urządzenia rozpocznie ciągłe odnajdowanie metadanych maszyn wirtualnych i danych o wydajności, a także odnajdowanie aplikacji, funkcji i ról.  Czas trwania odnajdowania aplikacji zależy od liczby posiadanych maszyn wirtualnych. Zazwyczaj trwa godzinę dla odnajdowania aplikacji 500 maszyn wirtualnych.

## <a name="review-and-export-the-inventory"></a>Przeglądanie i eksportowanie zapasów

Po zakończeniu odnajdywania, jeśli podano poświadczenia do odnajdowania aplikacji, można przejrzeć i wyeksportować spis aplikacji w witrynie Azure portal.

1. W **usłudze Azure Migrate — serwery** > **Usługi Azure Migrate: Server Assessment**kliknij wyświetlaną liczbę, aby otworzyć stronę **Odnalezione serwery.**

    > [!NOTE]
    > Na tym etapie można również opcjonalnie skonfigurować mapowanie zależności dla odnalezionych maszyn, dzięki czemu można wizualizować zależności między komputerami, które chcesz ocenić. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md).

2. W **aplikacji odnaleziony**kliknij wyświetlaną liczbę.
3. W **spisie aplikacji**można przejrzeć odnalezione aplikacje, role i funkcje.
4. Aby wyeksportować zapasy, w **ynalezionych serwerów**kliknij pozycję **Eksportuj spis aplikacji**.

Spis aplikacji jest eksportowany i pobierany w formacie programu Excel. Arkusz **spisu aplikacji** wyświetla wszystkie aplikacje wykryte na wszystkich komputerach.

## <a name="next-steps"></a>Następne kroki

- [Utwórz ocenę](how-to-create-assessment.md) migracji windy i zmiany wykrytych serwerów.
- Ocenianie baz danych programu SQL Server przy użyciu [programu Azure Migrate: Database Assessment](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
