---
title: Dodawanie narzędzi do oceny w usłudze Azure Migrate | Dokumentacja firmy Microsoft
description: Opisuje sposób dodawania narzędzi do oceny w Centrum migracji platformy Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811650"
---
# <a name="add-assessment-tools"></a>Dodawanie narzędzi do oceny

W tym artykule opisano sposób dodawania narzędzi do oceny w [usługi Azure Migrate](migrate-overview.md).

Usługa Azure Migrate udostępnia koncentrator narzędzia do oceny i migracji na platformę Azure. Obejmuje ona natywnych narzędzi Narzędzia udostępniane przez inne usługi platformy Azure i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm.

Jeśli chcesz dodać narzędzie do oceny i nie masz jeszcze projekt usługi Azure Migrate, postępuj zgodnie z tym [artykułu](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego dostawcy oprogramowania

Jeśli wybierzesz [narzędzie niezależnego dostawcy oprogramowania](migrate-services-overview.md#isv-integration) dla oceny, możesz zacząć od uzyskiwania licencji, lub rejestrowanie się w bezpłatnej wersji próbnej zgodnie z zasadami niezależnego dostawcy oprogramowania. Każde narzędzie ma opcję, aby nawiązać połączenie z usługi Azure Migrate. Postępuj zgodnie z instrukcjami narzędzie dokumentacją i połączyć obszar roboczy narzędzia za pomocą usługi Azure Migrate. 


## <a name="select-an-assessment-scenario"></a>Wybierz scenariusz oceny

1. W projekcie usługi Azure Migrate kliknij **Przegląd**.
2. Wybierz scenariusz oceny, którego chcesz użyć:

    - Aby odnaleźć i ocenić maszyny i obciążeń na potrzeby migracji na platformę Azure, wybierz **oceny i migracji serwerów**.
    - Aby ocenić maszyny lokalne z programem SQL, należy wybrać **oceniania i migrować bazy danych**.
    - Aby ocenić aplikacje sieci web w środowisku lokalnym, wybierz **oceny i migracji aplikacji sieci web**.

    ![Scenariusz oceny](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Wybierz narzędzia do oceny serwera 

1. Kliknij przycisk **ocenianie i Migrowanie serwerów**.
2. W **usługi Azure Migrate — serwery**, jeśli nie dodano narzędzia do oceny, w obszarze **narzędzi do oceny**, wybierz opcję **kliknij tutaj, aby dodać narzędzie do oceny**. Jeśli dodano już narzędzi do oceny w **dodać więcej narzędzi do oceny**, wybierz opcję **zmiany**.

    > [!NOTE]
    > Jeśli musisz przejść do innego projektu w **usługi Azure Migrate — serwery**obok pozycji **Zobacz szczegóły dotyczące projektu migracji różnych**, kliknij przycisk **kliknij tutaj,** .

3. W **usługi Azure Migrate**, wybierz narzędzie oceny, w której chcesz użyć.

    
    ![Narzędzia do oceny](./media/how-to-assess/assess-tool.png)

    - Użycie oceny Server migracji platformy Azure, można skonfigurować, uruchomić i wyświetlanie oceny bezpośrednio w projekcie usługi Azure Migrate.
    - Jeśli używasz narzędzia oceny innych firm, przejdź do linku podanego dla swojej witryny i ocenę zgodnie z instrukcjami, które oferuje.


## <a name="select-a-database-assessment-tool"></a>Wybierz narzędzie do oceny bazy danych

1. Kliknij przycisk **oceniania i migrować bazy danych**
2. W **baz danych**, kliknij przycisk **Dodawanie narzędzi**.
3. W Dodaj narzędzie > **narzędzia do oceny wybierz**, wybierz narzędzie, którego chcesz użyć do oceny bazy danych.

## <a name="select-a-web-app-assessment-tool"></a>Wybierz narzędzie do oceny aplikacji sieci web

1. Kliknij przycisk **oceny i migracji aplikacji sieci web**.
2. Kliknij link do narzędzia do migracji do usługi Azure App Service. Użyj narzędzia migracji:

    - **Oceny aplikacji w trybie online**: Możesz ocenić aplikacji za pomocą publicznego adresu URL w trybie online, za pomocą Asystenta do migracji usługi Azure App Service.
    - **.NET/PHP**: W przypadku wewnętrznych aplikacji .NET i PHP można pobrać i uruchomić Asystenta migracji.



## <a name="next-steps"></a>Następne kroki

Wypróbuj oceny za pomocą migracji serwera oceny Azure dla [funkcji Hyper-V](tutorial-prepare-hyper-v.md) lub [VMware](tutorial-prepare-vmware.md) maszyn wirtualnych.
