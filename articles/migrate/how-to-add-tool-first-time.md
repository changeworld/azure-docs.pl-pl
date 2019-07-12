---
title: Dodaj narzędzie ocena/migracja po raz pierwszy w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć projekt usługi Azure Migrate i Dodaj narzędzie ocena/migracja.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812027"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Dodaj narzędzie ocena/migracja po raz pierwszy

W tym artykule opisano sposób dodawania z narzędzia do oceny i migracji [usługi Azure Migrate](migrate-overview.md) projekt po raz pierwszy.  
Usługa Azure Migrate oferuje głównego Centrum do śledzenia wykrywania, oceny i migracji aplikacji lokalnych i obciążeń i chmury prywatne/publiczne maszyn wirtualnych na platformie Azure. Centrum zapewnia usługa Azure Migrate narzędzia do oceny i migracji, a także Niezależnym dostawcą oprogramowania innych firm, niezależne [ofert](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Utwórz projekt i Dodaj narzędzie

Skonfiguruj nowy projekt usługi Azure Migrate w subskrypcji platformy Azure i Dodaj narzędzie.

- Projekt usługi Azure Migrate służy do przechowywania wykrywania, oceny i migracji metadanych zebranych ze środowiska, w przypadku oceny lub migracji. 
- W projekcie można śledzić odnalezionych zasobów i organizowania, oceny i migracji.

1. W witrynie Azure portal > **wszystkich usług**, wyszukaj **usługi Azure Migrate**.
2. W obszarze **usług**, wybierz opcję **usługi Azure Migrate**.

    ![Konfigurowanie usługi Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. W **Przegląd**, kliknij przycisk **oceny i migracji serwerów**.
4. W obszarze **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **oceny i migracji serwerów**.

    ![Odnajdywanie i ocenianie serwerów](./media/how-to-add-tool-first-time/assess-migrate.png)

1. W **Odkrywaj, ocenianie i Migrowanie serwerów**, kliknij przycisk **Dodawanie narzędzi**.
2. W **projektu migracji**, a następnie wybierz swoją subskrypcję platformy Azure i Utwórz grupę zasobów, jeśli nie masz.
3. W **Project Details**, określ nazwę projektu i lokalizacji geograficznej, w której chcesz utworzyć projekt. 

    ![Utwórz projekt usługi Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Projekt usługi Azure Migrate można utworzyć w dowolnym z tych obszarów geograficznych.

    **Lokalizacja geograficzna** | **Region lokalizacja magazynu**
    --- | ---
    Azja | Azja południowo-wschodnia i Azja Wschodnia
    Europa | Południowo-Europa lub Europa Zachodnia
    Zjednoczone Królestwo | Południowe Zjednoczone Królestwo i zachodnie Zjednoczone Królestwo
    Stany Zjednoczone | Środkowe stany USA i zachodnie stany USA 2

    Geografia projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych. Można wybrać dowolny region docelowy, aby uzyskać rzeczywistą migrację.

4. Kliknij przycisk **dalej**i Dodaj narzędzie do migracji lub ocenę.

    > [!NOTE]
    > Podczas tworzenia projektu należy dodać co najmniej jedno narzędzie do migracji lub ocenę.

5. W **narzędzia do oceny wybierz**, Dodaj narzędzie do oceny. Jeśli narzędzie do oceny wydajności nie jest konieczne, zaznacz **pominąć Dodawanie narzędzie do oceny teraz** > **dalej**. 
2. W **narzędzie do migracji wybierz**, Dodaj narzędzie do migracji, zgodnie z potrzebami. Jeśli nie potrzebujesz teraz narzędzie do migracji, zaznacz **pominąć Dodawanie narzędzia migracji teraz** > **dalej**.
3. W **Przejrzyj + Dodaj narzędzia**, przejrzyj ustawienia i kliknij przycisk **Dodawanie narzędzi**.

Po utworzeniu projektu można wybrać dodatkowych narzędzi do oceny i migracji serwerów i obciążeń, baz danych i aplikacji sieci web.

## <a name="create-additional-projects"></a>Utwórz dodatkowe projekty

W niektórych sytuacjach może być konieczne utworzenie dodatkowych projektów usługi Azure Migrate. Na przykład jeśli masz centrów danych w różnych lokalizacjach geograficznych lub potrzebujesz do przechowywania metadanych w innej lokalizacji geograficznej. Utwórz dodatkowy projekt w następujący sposób:

1. W bieżącym projekcie usługi Azure Migrate kliknij **serwerów** lub **baz danych**.
2. W prawym górnym rogu kliknij **zmiany** obok nazwy bieżącego projektu.
3. W **ustawienia**, wybierz opcję **kliknij tutaj, aby utworzyć nowy projekt**.
4. Utwórz nowy projekt i dodać nowe narzędzie zgodnie z opisem w poprzedniej procedurze.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dodatkowe [oceny](how-to-assess.md) i [migracji](how-to-migrate.md) narzędzia. 
