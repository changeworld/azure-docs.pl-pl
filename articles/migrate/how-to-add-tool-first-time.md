---
title: Dodaj narzędzie do oceny/migracji po raz pierwszy w Azure Migrate | Microsoft Docs
description: Opisuje sposób tworzenia projektu Azure Migrate i dodawania narzędzia do oceny/migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: a4e66175b20552e632702cb2ba46d0ae6c0956d4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720245"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Dodawanie narzędzia oceny/migracji po raz pierwszy

W tym artykule opisano sposób dodawania narzędzia do oceny lub migracji do projektu [Azure Migrate](migrate-overview.md) po raz pierwszy.  
Azure Migrate udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także inne narzędzia i [oferty](migrate-services-overview.md#isv-integration) niezależnych dostawców oprogramowania (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Tworzenie projektu i Dodawanie narzędzia

Skonfiguruj nowy projekt Azure Migrate w ramach subskrypcji platformy Azure i Dodaj narzędzie.

- Projekt Azure Migrate jest używany do przechowywania metadanych odnajdywania, oceny i migracji zebranych ze środowiska, które oceniasz lub migrujesz. 
- W projekcie można śledzić odnalezione zasoby oraz organizować oceny i migrację.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.

    ![Skonfiguruj Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **odnajdywanie, ocenianie i Migrowanie serwerów**kliknij pozycję **Oceń i Przeprowadź migrację serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/how-to-add-tool-first-time/assess-migrate.png)

1. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
2. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
3. W obszarze **szczegóły projektu**Określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. 

    ![Tworzenie projektu Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Projekt Azure Migrate można utworzyć w dowolnym z tych lokalizacje geograficzne.

   **Lokalizacja geograficzna** | **Region lokalizacji magazynu**
    --- | ---
    Azja   | Azja Południowo-Wschodnia lub Azja Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Japonia  | Japonia Wschodnia lub Japonia Zachodnia
    Wielka Brytania | Południowe Zjednoczone Królestwo lub Zachodnie Zjednoczone Królestwo
    Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2
    Kanada | Kanada Środkowa
    Indie  | Indie Środkowe lub Indie Południowe
    Australia | Australia Południowo-Wschodnia

    Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy dla rzeczywistej migracji.

    Jeśli chcesz określić konkretny region w obszarze geograficznym do wdrażania projektu migracji i skojarzonych z nim zasobów (ograniczenia zasad w subskrypcji mogą zezwalać na wdrażanie zasobów platformy Azure tylko w określonym regionie platformy Azure), można użyć poniższego interfejsu API do Utwórz projekt migracji. Określ Identyfikator subskrypcji, nazwę grupy zasobów, Migruj nazwę projektu wraz z lokalizacją (dowolne regiony platformy Azure wymienione w tabeli, w której wdrożono Azure Migrate).

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Kliknij przycisk **dalej**i Dodaj narzędzie do oceny lub migracji.

    > [!NOTE]
    > Podczas tworzenia projektu należy dodać co najmniej jedno narzędzie do oceny lub migracji.

5. W **narzędziu Wybierz ocenę**Dodaj narzędzie do oceny. Jeśli nie potrzebujesz narzędzia do oceny, wybierz pozycję **Pomiń Dodawanie narzędzia do oceny teraz** > **dalej**. 
2. W obszarze **Wybieranie narzędzia migracji**Dodaj narzędzie do migracji zgodnie z wymaganiami. Jeśli nie potrzebujesz teraz narzędzia migracji, wybierz pozycję **Pomiń Dodawanie narzędzia do migracji dla teraz** > **dalej**.
3. W oknie **Recenzja + Dodawanie narzędzi**przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**.

Po utworzeniu projektu można wybrać dodatkowe narzędzia do oceny i migracji serwerów oraz obciążeń, baz danych i aplikacji sieci Web.

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

W pewnych okolicznościach może być konieczne utworzenie dodatkowych projektów Azure Migrate. Na przykład jeśli masz centra danych w różnych lokalizacje geograficzneach lub musisz przechowywać metadane w innej lokalizacji geograficznej. Utwórz dodatkowy projekt w następujący sposób:

1. W bieżącym projekcie Azure Migrate kliknij pozycję **serwery** lub **bazy danych**.
2. W prawym górnym rogu kliknij pozycję **Zmień** obok bieżącej nazwy projektu.
3. W obszarze **Ustawienia**wybierz **pozycję kliknij tutaj, aby utworzyć nowy projekt**.
4. Utwórz nowy projekt i Dodaj nowe narzędzie zgodnie z opisem w poprzedniej procedurze.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dodatkowe narzędzia do [oceny](how-to-assess.md) i [migracji](how-to-migrate.md) . 
