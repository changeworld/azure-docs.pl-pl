---
title: Dodawanie narzędzia oceny/migracji w usłudze Azure Migrate
description: W tym artykule opisano sposób tworzenia projektu migracji platformy Azure i dodawania narzędzia oceny/migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185951"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Dodawanie narzędzia oceny/migracji po raz pierwszy

W tym [artykule](migrate-overview.md) opisano sposób dodawania narzędzia oceny lub migracji do projektu migracji platformy Azure po raz pierwszy.  
Usługa Azure Migrate zapewnia centralne centrum do śledzenia odnajdowania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmury prywatnej/publicznej na platformę Azure. Centrum udostępnia narzędzia migracji platformy Azure do oceny i migracji, a także inne narzędzia i [oferty](migrate-services-overview.md#isv-integration) niezależnych dostawców oprogramowania (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Tworzenie projektu i dodawanie narzędzia

Skonfiguruj nowy projekt migracji platformy Azure w ramach subskrypcji platformy Azure i dodaj narzędzie.

- Projekt migracji platformy Azure służy do przechowywania metadanych odnajdywania, oceny i migracji zebranych ze środowiska, które oceniasz lub migrujesz. 
- W projekcie można śledzić odnalezione zasoby oraz aranżować ocenę i migrację.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.

    ![Konfigurowanie migracji platformy Azure](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. W obszarze **Przegląd** kliknij pozycję **Ocena i migracja serwerów**.
4. W obszarze **Odnajdowanie oceniaj i migruj serwery**kliknij pozycję **Oceń i migruj serwery**.

    ![Odkrywanie i ocenianie serwerów](./media/how-to-add-tool-first-time/assess-migrate.png)

1. W obszarze **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Dodaj narzędzia**.
2. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
3. W **obszarze Szczegóły projektu**określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. 

    ![Tworzenie projektu migracji platformy Azure](./media/how-to-add-tool-first-time/migrate-project.png)

    Można utworzyć projekt migracji platformy Azure w dowolnym z tych obszarów geograficznych.

   **Geografia** | **Region lokalizacji magazynu**
    --- | ---
    Azja   | Azja Południowo-Wschodnia lub Azja Wschodnia
    Europa | Europa Północna lub Europa Zachodnia
    Japonia  | Japonia Wschodnia lub Japonia Zachodnia
    Wielka Brytania | Wielka Brytania Południowa lub Wielka Brytania Zachód
    Stany Zjednoczone | Środkowe stany USA lub zachodnie stany USA 2
    Kanada | Kanada Środkowa
    Indie  | Indie Środkowe lub Indie Południowe
    Australia | Australia PołudniowyEast

    Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy dla rzeczywistej migracji.

    Jeśli chcesz określić określony region w obrębie lokalizacji geograficznej do wdrażania projektu migracji i skojarzonych z nim zasobów (ograniczenia zasad w subskrypcji mogą zezwalać na wdrażanie zasobów platformy Azure tylko w określonym regionie platformy Azure), można użyć poniższego interfejsu API, aby utworzyć projekt migracji. Określ identyfikator subskrypcji, nazwę grupy zasobów, nazwę projektu migracji wraz z lokalizacją (dowolny z regionów platformy Azure wymienionych w tabeli, w której jest wdrażany program Azure Migrate).

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Kliknij **przycisk Dalej**i dodaj narzędzie oceny lub migracji.

    > [!NOTE]
    > Podczas tworzenia projektu należy dodać co najmniej jedno narzędzie oceny lub migracji.

5. W **obszarze Wybierz narzędzie oceny**dodaj narzędzie oceny. Jeśli nie potrzebujesz narzędzia do oceny, wybierz **opcję Pomiń dodanie narzędzia oceny na razie** > **Dalej**. 
2. W **obszarze Wybierz narzędzie migracji**dodaj narzędzie migracji zgodnie z wymaganiami. Jeśli nie potrzebujesz teraz narzędzia do migracji, wybierz **opcję Pomiń dodanie narzędzia migracji na teraz** > **Dalej**.
3. W **obszarze Recenzja + dodawanie narzędzi**przejrzyj ustawienia i kliknij pozycję Dodaj **narzędzia**.

Po utworzeniu projektu można wybrać dodatkowe narzędzia do oceny i migracji serwerów i obciążeń, baz danych i aplikacji sieci web.

## <a name="create-additional-projects"></a>Tworzenie dodatkowych projektów

W pewnych okolicznościach może być konieczne utworzenie dodatkowych projektów migracji platformy Azure. Na przykład, jeśli masz centra danych w różnych regionach geograficznych lub musisz przechowywać metadane w innej lokalizacji geograficznej. Utwórz dodatkowy projekt w następujący sposób:

1. W bieżącym projekcie migracji platformy Azure kliknij pozycję **Serwery** lub **Bazy danych**.
2. W prawym górnym rogu kliknij pozycję **Zmień** obok bieżącej nazwy projektu.
3. W **obszarze Ustawienia**wybierz pozycję Kliknij **tutaj, aby utworzyć nowy projekt**.
4. Utwórz nowy projekt i dodaj nowe narzędzie zgodnie z opisem w poprzedniej procedurze.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dodatkowe narzędzia [oceny](how-to-assess.md) i [migracji.](how-to-migrate.md) 
