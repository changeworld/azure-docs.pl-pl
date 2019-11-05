---
title: Odkryj aplikacje, role i funkcje zainstalowane na serwerach lokalnych przy użyciu funkcji oceny serwera Azure Migrate
description: Opisuje sposób odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu oceny serwera Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 10/16/2019
ms.author: snehaa
ms.openlocfilehash: e09c430204dea490bace029b809f25304be53a9e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512719"
---
# <a name="discover-machine-apps-roles-and-features"></a>Odkryj aplikacje maszynowe, role i funkcje 

W tym artykule opisano sposób odnajdywania aplikacji, ról i funkcji na serwerach lokalnych przy użyciu Azure Migrate: Ocena serwera.

Odnajdywanie spisu aplikacji, a role/funkcje działające na maszynach lokalnych, ułatwia identyfikowanie i planowanie ścieżki migracji do platformy Azure, która jest dostosowana do obciążeń. 

Odnajdywanie aplikacji przy użyciu Azure Migrate: Ocena serwera jest bez wykorzystania agentów. Niczego nie trzeba instalować na maszynach i maszynach wirtualnych. Funkcja oceny serwera używa urządzenia Azure Migrate do przeprowadzenia odnajdywania wraz z poświadczeniami gościa maszyny. Urządzenie zdalnie uzyskuje dostęp do maszyn przy użyciu usług WMI i wywołań SSH. 

> [!NOTE]
> Funkcja odnajdywania aplikacji jest obecnie obsługiwana tylko w przypadku maszyn wirtualnych VMware i jest ograniczona tylko do odnajdowania. Nie oferujemy jeszcze oceny opartej na aplikacji.  Teraz Ocena serwera umożliwia obecnie ocenianie lokalnych maszyn wirtualnych programu VMware, maszyn wirtualnych funkcji Hyper-V i serwerów fizycznych na poziomie komputera w celu przeprowadzenia migracji w celu przesunięcia i przejścia.


## <a name="before-you-start"></a>Przed rozpoczęciem

1. Zapoznaj się z tematem [ograniczenia dotyczące obsługi](migrate-support-matrix-vmware.md#application-discovery) odnajdywania na poziomie aplikacji.
2. Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
3. Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
4. Sprawdź [wymagania programu VMware](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) dotyczące odnajdywania i oceniania maszyn wirtualnych VMware przy użyciu urządzenia Azure Migrate.
4. Sprawdź [wymagania](migrate-support-matrix-vmware.md#assessment-appliance-requirements) dotyczące wdrażania urządzenia Azure Migrate.

## <a name="prepare-for-app-discovery"></a>Przygotowanie do odnajdywania aplikacji

1. [Przygotowanie do wdrożenia urządzenia](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware). Przygotowanie obejmuje sprawdzenie ustawień urządzenia oraz skonfigurowanie konta, które będzie używane przez urządzenie w celu uzyskania dostępu do vCenter Server.
2. Upewnij się, że masz konto użytkownika (jeden z nich dla serwerów z systemem Windows i Linux) z uprawnieniami administratora dla maszyn, na których mają być odnajdywane aplikacje, role i funkcje.
3. [Wdróż urządzenie VMware,](how-to-set-up-appliance-vmware.md) aby rozpocząć odnajdywanie. Aby wdrożyć urządzenie, należy pobrać i zaimportować szablon komórki jajowe do programu VMware w celu utworzenia urządzenia jako maszyny wirtualnej VMware. Należy skonfigurować urządzenie, a następnie zarejestrować je w Azure Migrate.
2. Podczas wdrażania urządzenia w celu rozpoczęcia wykrywania ciągłego należy określić następujące elementy:
    - Nazwa vCenter Server, z którą chcesz nawiązać połączenie.
    - Poświadczenia utworzone dla urządzenia w celu nawiązania połączenia z vCenter Server.
    - Poświadczenia konta utworzone dla urządzenia w celu nawiązania połączenia z maszynami wirtualnymi z systemem Windows/Linux.

Po wdrożeniu urządzenia i uzyskaniu poświadczeń urządzenie uruchamia ciągłe wykrywanie metadanych maszyn wirtualnych i danych wydajności oraz odnajdywanie aplikacji, funkcji i ról.  Czas odnajdowania aplikacji zależy od liczby posiadanych maszyn wirtualnych. Funkcja odnajdywania aplikacji na maszynach wirtualnych 500 zazwyczaj trwa godzinę.

## <a name="review-and-export-the-inventory"></a>Przeglądanie i eksportowanie spisu

Po zakończeniu odnajdywania, jeśli podano poświadczenia do odnajdowania aplikacji, możesz przejrzeć i wyeksportować spis aplikacji w Azure Portal. 

1. W **Azure Migrate serwery** > **Azure Migrate: Ocena serwera**, kliknij liczbę wyświetlaną, aby otworzyć stronę **odnalezione serwery** .

    > [!NOTE]
    > Na tym etapie można także opcjonalnie skonfigurować mapowanie zależności dla odnalezionych maszyn, aby można było wizualizować zależności między maszynami, które chcesz ocenić. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md).

2. W **odnalezionych aplikacjach**kliknij liczbę wyświetlaną.
3. W **spisie aplikacji**można przejrzeć odnalezione aplikacje, role i funkcje.
4. Aby wyeksportować spis, w **odnalezionych serwerach**kliknij pozycję **Eksportuj spis aplikacji**.

Spis aplikacji jest eksportowany i pobierany w formacie programu Excel. Arkusz **spisu aplikacji** zawiera wszystkie aplikacje odnalezione na wszystkich komputerach.

## <a name="next-steps"></a>Następne kroki

- [Utwórz ocenę](how-to-create-assessment.md) dla podnoszenia i przesunięcia migracji odnalezionych serwerów.
- Ocenianie SQL Server baz danych przy użyciu [Azure Migrate: Ocena bazy danych](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
