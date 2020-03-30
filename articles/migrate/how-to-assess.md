---
title: Dodawanie narzędzi oceny w obszarze Migracji platformy Azure
description: Dowiedz się, jak dodać narzędzia oceny w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185908"
---
# <a name="add-assessment-tools"></a>Dodawanie narzędzi oceny

W tym artykule opisano sposób dodawania narzędzi oceny w [usłudze Azure Migrate](migrate-overview.md).

Usługa Azure Migrate udostępnia centrum narzędzi do oceny i migracji na platformę Azure. Zawiera narzędzia migracji platformy Azure, a także inne narzędzia i oferty niezależnego dostawcy oprogramowania (ISV).

Jeśli chcesz dodać narzędzie do oceny i nie masz jeszcze projektu migracji platformy Azure, postępuj zgodnie z tym [artykułem](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Wybieranie narzędzia

Jeśli wybierzesz narzędzie migracji nienawiązane do platformy Azure do oceny, zacznij od uzyskania licencji lub zarejestrowania się w celu uzyskania bezpłatnej wersji próbnej, zgodnie z zasadami narzędzi. Narzędzia mają możliwość łączenia się z usługą Azure Migrate. Postępuj zgodnie z instrukcjami i dokumentacją, aby połączyć narzędzie z usługą Azure Migrate. [Dowiedz się więcej](migrate-services-overview.md) o narzędziach.


## <a name="select-an-assessment-scenario"></a>Wybieranie scenariusza oceny

1. W projekcie migracji platformy Azure kliknij pozycję **Przegląd**.
2. Wybierz scenariusz oceny, którego chcesz użyć:

    - Aby odnajdować i ocenić maszyny i obciążenia do migracji na platformę Azure, wybierz pozycję **Oceń i migruj serwery**.
    - Aby ocenić lokalne maszyny SQL, wybierz pozycję **Oceń i migruj bazy danych**.
    - Aby ocenić lokalne aplikacje internetowe, wybierz pozycję **Oceń i migruj aplikacje internetowe**.

    ![Scenariusz oceny](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Wybieranie narzędzia do oceny serwera 

1. Kliknij **pozycję Oceń i migruj serwery**.
2. W **obszarze Migracja platformy Azure — serwery**, jeśli nie dodano narzędzia do oceny, w obszarze **Narzędzia oceny**wybierz pozycję Kliknij **tutaj, aby dodać narzędzie do oceny**. Jeśli dodano już narzędzia oceny, w obszarze **Dodaj więcej narzędzi oceny**wybierz pozycję **Zmień**.

    > [!NOTE]
    > Jeśli chcesz przejść do innego projektu, w **usłudze Azure Migrate - Servers**, obok **pozycji Zobacz szczegóły dotyczące innego projektu migracji,** kliknij przycisk **Kliknij tutaj**.

3. W **obszarze Migracja platformy Azure**wybierz narzędzie oceny, którego chcesz użyć.

    - Jeśli używasz oceny serwera migracji platformy Azure, możesz skonfigurować, uruchomić i wyświetlić oceny bezpośrednio w projekcie migracji platformy Azure.
    - Jeśli korzystasz z innego narzędzia do oceny, przejdź do łącza dostarczonego dla ich witryny i uruchom ocenę zgodnie z instrukcjami, które zapewniają.


## <a name="select-a-database-assessment-tool"></a>Wybieranie narzędzia do oceny bazy danych

1. Kliknij **pozycję Oceniaj i migruj bazy danych**
2. W **pozycji Bazy danych**kliknij pozycję Dodaj **narzędzia**.
3. W obszarze Dodaj narzędzie > **Wybierz narzędzie oceny**wybierz narzędzie, którego chcesz użyć do oceny bazy danych.

## <a name="select-a-web-app-assessment-tool"></a>Wybieranie narzędzia do oceny aplikacji sieci Web

1. Kliknij **pozycję Oceń i migruj aplikacje internetowe**.
2. Kliknij łącze do narzędzia migracji dla usługi Azure App Service. Użyj narzędzia migracji, aby:

    - **Oceniaj aplikacje online:** możesz oceniać aplikacje za pomocą publicznego adresu URL w trybie online, korzystając z Asystenta migracji usługi Azure App Service.
    - **.NET/PHP**: W przypadku wewnętrznych aplikacji .NET i PHP można pobrać i uruchomić Asystenta migracji.



## <a name="next-steps"></a>Następne kroki

Wypróbuj ocenę przy użyciu oceny serwera migracji platformy Azure dla maszyn wirtualnych [VMware,](tutorial-prepare-vmware.md) [funkcji Hyper-V](tutorial-prepare-hyper-v.md)lub [serwerów fizycznych](tutorial-prepare-physical.md)
