---
title: Dodawanie narzędzi migracji w obszarze Migracji platformy Azure
description: Dowiedz się, jak dodać narzędzia migracji w usłudze Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185959"
---
# <a name="add-migration-tools"></a>Dodawanie narzędzi migracji

W tym artykule opisano sposób dodawania narzędzi migracji w [usłudze Azure Migrate](migrate-overview.md).

Usługa Azure Migrate udostępnia centrum narzędzi do oceny i migracji na platformę Azure. Zawiera natywne narzędzia, narzędzia dostarczane przez inne usługi platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV).

Jeśli chcesz dodać narzędzie migracji i nie skonfigurowałeś jeszcze projektu migracji platformy Azure, postępuj zgodnie z tym [artykułem](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego niezależnego

Jeśli wybierzesz narzędzie niezależnego [niezależnego użytkownika](migrate-services-overview.md#isv-integration) do migracji, możesz zacząć od uzyskania licencji lub zarejestrowania się w celu bezpłatnej wersji próbnej, zgodnie z zasadami niezależnego systemu isv. W każdym narzędziu istnieje opcja, aby połączyć się z usługi Azure Migrate. Wdrażanie narzędzia i postępuj zgodnie z instrukcjami narzędzia i dokumentacji, aby połączyć obszar roboczy narzędzia z usługi Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Wybieranie scenariusza migracji

1. W projekcie migracji platformy Azure kliknij pozycję **Przegląd**.
2. Wybierz scenariusz migracji, którego chcesz użyć:

    - Aby przeprowadzić migrację maszyn i obciążeń na platformę Azure, wybierz pozycję **Oceń i zmigruj serwery**.
    - Aby przeprowadzić migrację lokalnych maszyn SQL, wybierz pozycję **Oceń i migruj bazy danych**.
    - Aby przeprowadzić migrację lokalnych aplikacji sieci Web, wybierz pozycję **Oceń i migruj aplikacje internetowe**.
    - Aby przeprowadzić migrację dużych ilości danych lokalnych na platformę Azure w trybie offline, wybierz pozycję **Zamów pole danych**.

    ![Scenariusz oceny](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Wybieranie narzędzia do migracji serwera

1. Kliknij **pozycję Oceń i migruj serwery**.
2. W **obszarze Migracja platformy Azure — serwery**, jeśli nie dodano jeszcze narzędzi migracji, w obszarze **Narzędzia migracji**wybierz pozycję Kliknij **tutaj, aby dodać narzędzie do migracji**. Jeśli dodano już narzędzia do migracji, w obszarze **Dodaj więcej narzędzi migracji**wybierz pozycję **Zmień**.

    > [!NOTE]
    > Jeśli chcesz przejść do innego projektu, w **usłudze Azure Migrate - Servers**, obok **pozycji Zobacz szczegóły dotyczące innego projektu migracji,** kliknij przycisk **Kliknij tutaj**.

3. W **obszarze Migracja platformy Azure**wybierz narzędzie migracji, którego chcesz użyć.
    - Jeśli używasz migracji serwera migracji usługi Azure, można skonfigurować i uruchomić migracje bezpośrednio w projekcie migracji platformy Azure.
    - Jeśli używasz narzędzia do oceny innej firmy, przejdź do łącza dostarczonego dla dostawcy zewnętrznego dostawcy reklamy i uruchom migrację zgodnie z instrukcjami, które zapewniają.

## <a name="select-a-database-migration-tool"></a>Wybieranie narzędzia do migracji bazy danych

1. Kliknij **pozycję Oceniaj i migruj bazy danych**
2. W **pozycji Bazy danych**kliknij pozycję Dodaj **narzędzia**.
3. W obszarze Dodaj narzędzie > **Wybierz narzędzie migracji**wybierz narzędzie, którego chcesz użyć do migracji bazy danych.

## <a name="select-a-web-app-migration-tool"></a>Wybieranie narzędzia do migracji aplikacji sieci Web

1. Kliknij **pozycję Oceń i migruj aplikacje internetowe**.
2. Kliknij łącze do narzędzia migracji dla usługi Azure App Service. Użyj narzędzia migracji, aby:

    - **Oceniaj aplikacje online:** za pomocą Asystenta migracji usługi Azure App Service można oceniać i migrować aplikacje za pomocą publicznego adresu URL w trybie online.
    - **.NET/PHP**: W przypadku wewnętrznych aplikacji .NET i PHP można pobrać i uruchomić Asystenta migracji.

## <a name="order-an-azure-data-box"></a>Zamów skrzynkę danych platformy Azure

Aby przeprowadzić migrację dużych ilości danych na platformę Azure, można zamówić skrzynkę DAta platformy Azure do transferu danych w trybie offline.

1. Kliknij **pozycję Zamów pole danych**.
2. W **obszarze Wybierz pole danych platformy Azure**określ subskrypcję. 
3. Transfer będzie importem na platformę Azure. Określ źródło danych i miejsce docelowe regionu platformy Azure dla danych.

## <a name="next-steps"></a>Następne kroki

Wypróbuj migrację przy użyciu migracji serwera migracji usługi Azure dla maszyn [wirtualnych funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [VMware.](tutorial-migrate-vmware.md)
