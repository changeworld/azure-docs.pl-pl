---
title: Grupowanie maszyn do oceny za pomocą usługi Azure Migrate | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób grupowania maszyn przed uruchomieniem oceny za pomocą usługi Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301706"
---
# <a name="create-a-group-for-assessment"></a>Tworzenie grupy do oceny

W tym artykule opisano sposób tworzenia grup maszyn do oceny za pomocą usługi Azure Migrate: Server Assessment.

[Usługa Azure Migrate](migrate-services-overview.md) ułatwia migrację na platformę Azure. Usługa Azure Migrate udostępnia scentralizowane centrum do śledzenia odnajdowania, oceny i migracji lokalnej infrastruktury, aplikacji i danych na platformę Azure. Centrum udostępnia narzędzia platformy Azure do oceny i migracji, a także oferty niezależnych dostawców oprogramowania innych firm (ISV). 

## <a name="grouping-machines"></a>Maszyny grupujące

Zbierasz maszyny do grup, aby ocenić, czy nadają się do migracji na platformę Azure i uzyskać dla nich rozmiary platformy Azure i szacowanie kosztów. Istnieje kilka sposobów tworzenia grup:

- Jeśli znasz maszyny, które muszą być migrowane razem, można ręcznie utworzyć grupę w usłudze Azure Migrate.
- Jeśli nie masz pewności co do maszyn, które muszą być zgrupowane, można użyć funkcji wizualizacji zależności w usłudze Azure Migrate do tworzenia grup. 

> [!NOTE]
> Funkcja wizualizacji zależności nie jest dostępna na platformie Azure Government.

## <a name="create-a-group-manually"></a>Ręczne tworzenie grupy

Grupę można utworzyć w tym samym czasie, w której [jest tworzęna ocena](how-to-create-assessment.md).

Jeśli chcesz utworzyć grupę ręcznie poza tworzeniem oceny, wykonaj następujące czynności:

1. W **przeglądzie**> projektu migracji platformy Azure kliknij pozycję **Oceń i migruj serwery**. W **usłudze Azure Migrate: Ocena serwera**kliknij pozycję **Grupy**
    - Jeśli nie dodano jeszcze narzędzia Do oceny migracji platformy Azure: Serwer, kliknij, aby go dodać. [Dowiedz się więcej](how-to-assess.md).
    - Jeśli projekt migracji platformy Azure nie został jeszcze utworzony, [dowiedz się więcej](how-to-add-tool-first-time.md).

    ![Zaznaczanie grup](./media/how-to-create-a-group/select-groups.png)

2. Kliknij ikonę **Grupuj.**
3. W **obszarze Tworzenie grupy**określ nazwę grupy, a w nazwie **urządzenia**wybierz urządzenie migracji platformy Azure używane do odnajdowania komputera.
1. Z listy maszyn wybierz maszyny, które chcesz dodać do grupy > **Utwórz**.

    ![Tworzenie grupy:](./media/how-to-create-a-group/create-group.png)

Teraz można użyć tej grupy podczas [tworzenia oceny](how-to-create-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Uściślanie grupy z mapowaniem zależności

Mapowanie zależności ułatwia wizualizację zależności między komputerami. Mapowanie zależności jest zazwyczaj używane, gdy chcesz ocenić grupy komputerów o wyższym poziomie zaufania.
- To pomaga do krzyżowego sprawdzania zależności komputera, przed uruchomieniem oceny. 
- Pomaga również skutecznie zaplanować migrację na platformę Azure, zapewniając, że nic nie pozostaje w tyle, a tym samym unikając niespodziewanych awarii podczas migracji.
- Można odkryć współzależne systemy, które muszą migrować razem i określić, czy uruchomiony system nadal obsługuje użytkowników, czy jest kandydatem do likwidacji zamiast migracji.

Jeśli masz już [skonfigurowane mapowanie zależności](how-to-create-group-machine-dependencies.md)i chcesz uściślić istniejącą grupę, wykonaj następujące czynności:

1. Na karcie **Serwery** w obszarze **Azure Migrate: Server Assessment (Migracja serwera:** — kafelek — grupa danych) kliknij pozycję **Grupy**.
2. Kliknij grupę, którą chcesz zawęzić.
    - Jeśli nie skonfigurowałeś jeszcze mapowania zależności, kolumna **Zależności** będzie wyświetlać stan **wymaga instalacji.** Dla każdej maszyny Wirtualnej, dla której chcesz wizualizować zależności, kliknij pozycję **Wymaga instalacji**. Zainstaluj kilka agentów na każdej maszynie wirtualnej, zanim będzie można mapować zależności komputera. [Dowiedz się więcej](how-to-create-group-machine-dependencies.md).

        ![Dodawanie mapowania zależności](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Jeśli masz już skonfigurowane mapowanie zależności, na stronie grupy kliknij pozycję **Wyświetl zależności,** aby otworzyć mapę zależności grupy.

3. Po kliknięciu przycisku **Wyświetl zależności**mapa zależności grupy pokazuje następujące elementy:

    - Połączenia TCP przychodzące (klientów) i wychodzące (serwery) do i ze wszystkich komputerów w grupie, na których są zainstalowane agenci zależności.
    - Maszyny zależne, które nie mają zainstalowanych agentów zależności, są pogrupowane według numerów portów.
    - Maszyny zależne z zainstalowanymi agentami zależności są wyświetlane jako oddzielne pola.
    - Procesy uruchomione wewnątrz maszyny. Rozwiń każde pole maszyny, aby wyświetlić procesy.
    - Właściwości komputera (w tym FQDN, system operacyjny, adres MAC). Kliknij każde pole maszyny, aby wyświetlić szczegóły.

4. Aby wyświetlić zależności w wybranym przedziale czasu, zmodyfikuj zakres czasu (domyślnie godzinę), określając daty rozpoczęcia i zakończenia lub czas trwania.

    > [!NOTE]
    > Zakres czasu może wynosić do godziny. Jeśli potrzebujesz dłuższego zasięgu, użyj [usługi Azure Monitor, aby zbadać dane zależne](how-to-create-group-machine-dependencies.md) przez dłuższy okres.

5. Po zidentyfikowaniu zależności, które chcesz dodać lub usunąć z grupy, można zmodyfikować grupę. Użyj klawiszy Ctrl+Kliknij, aby dodać lub usunąć maszyny z grupy.

    - Można dodawać tylko maszyny, które zostały odkryte.
    - Dodawanie i usuwanie maszyn unieważnia wcześniejsze oceny dla grupy.
    - Opcjonalnie można utworzyć nową ocenę podczas modyfikowania grupy.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować i używać [mapowania zależności](how-to-create-group-machine-dependencies.md) do tworzenia grup wysokiego zaufania.

