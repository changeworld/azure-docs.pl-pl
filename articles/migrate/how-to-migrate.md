---
title: Dodaj narzędzia migracji w usłudze Azure Migrate
description: Opisuje sposób dodawania narzędzia migracji w Centrum migracji platformy Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811871"
---
# <a name="add-migration-tools"></a>Dodawanie narzędzi migracji

W tym artykule opisano sposób dodawania narzędzia migracji w [usługi Azure Migrate](migrate-overview.md).

Usługa Azure Migrate udostępnia koncentrator narzędzia do oceny i migracji na platformę Azure. Obejmuje ona natywnych narzędzi Narzędzia udostępniane przez inne usługi platformy Azure i ofert Niezależnym dostawcą oprogramowania niezależnie od innych firm.

Jeśli chcesz dodać narzędzie do migracji, a jeszcze nie skonfigurowano projekt usługi Azure Migrate, postępuj zgodnie z tym [artykułu](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Wybieranie narzędzia niezależnego dostawcy oprogramowania

Jeśli wybierzesz [narzędzie niezależnego dostawcy oprogramowania](migrate-services-overview.md#isv-integration) do migracji, możesz zacząć od uzyskiwania licencji, lub rejestrowanie się w bezpłatnej wersji próbnej zgodnie z zasadami niezależnego dostawcy oprogramowania. Każde narzędzie ma opcję, aby nawiązać połączenie z usługi Azure Migrate. Wdróż narzędzie i wykonaj instrukcje narzędzia i dokumentacji, aby połączyć obszar roboczy narzędzia za pomocą usługi Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Wybierz scenariusz migracji

1. W projekcie usługi Azure Migrate kliknij **Przegląd**.
2. Wybierz scenariusz migracji, do którego chcesz użyć:

    - Aby przeprowadzić migrację maszyn i obciążeń na platformie Azure, wybierz **oceny i migracji serwerów**.
    - Aby przeprowadzić migrację maszyn lokalnych z programem SQL, należy wybrać **oceniania i migrować bazy danych**.
    - Aby przeprowadzić migrację lokalnych aplikacji sieci web, wybierz **oceny i migracji aplikacji sieci web**.
    - Aby przeprowadzić migrację dużych ilości danych lokalnych do platformy Azure w trybie offline, należy wybrać **zamówienie urządzenia Data Box**.

    ![Scenariusz oceny](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Wybierz narzędzia do migracji serwera

1. Kliknij przycisk **ocenianie i Migrowanie serwerów**.
2. W **usługi Azure Migrate — serwery**, jeśli nie dodano jeszcze narzędzia migracji w obszarze **narzędzia migracji**, wybierz opcję **kliknij tutaj, aby dodać narzędzia migracji**. Jeśli dodano już narzędzia migracji w **dodać więcej narzędzi migracji**, wybierz opcję **zmiany**.

    > [!NOTE]
    > Jeśli musisz przejść do innego projektu w **usługi Azure Migrate — serwery**obok pozycji **Zobacz szczegóły dotyczące projektu migracji różnych**, kliknij przycisk **kliknij tutaj,** .

3. W **usługi Azure Migrate**, wybierz narzędzie do migracji chcesz użyć.
    - Jeśli używasz migracji serwera migracji platformy Azure, możesz skonfigurować i uruchom migracje bezpośrednio w projekcie usługi Azure Migrate.
    - Jeśli używasz narzędzia oceny innych firm, przejdź do linku podanego dla niezależnych dostawców oprogramowania i uruchamianie migracji, zgodnie z instrukcjami, które oferuje.

## <a name="select-a-database-migration-tool"></a>Wybierz narzędzie do migracji bazy danych

1. Kliknij przycisk **oceniania i migrować bazy danych**
2. W **baz danych**, kliknij przycisk **Dodawanie narzędzi**.
3. W Dodaj narzędzie > **narzędzie do migracji wybierz**, wybierz narzędzie, którego chcesz użyć do migracji bazy danych.

## <a name="select-a-web-app-migration-tool"></a>Wybierz narzędzie do migracji aplikacji sieci web

1. Kliknij przycisk **oceny i migracji aplikacji sieci web**.
2. Kliknij link do narzędzia do migracji do usługi Azure App Service. Użyj narzędzia migracji:

    - **Oceny aplikacji w trybie online**: Można ocenić i migracji aplikacji za pomocą publicznego adresu URL w trybie online, używając Asystenta migracji systemu Azure App Service.
    - **.NET/PHP**: W przypadku wewnętrznych aplikacji .NET i PHP można pobrać i uruchomić Asystenta migracji.

## <a name="order-an-azure-data-box"></a>Zamówienie urządzenia Azure Data Box

Aby przeprowadzić migrację dużych ilości danych na platformie Azure, może zamówić łączność obejmującą Azure DAta Box za transfer danych w trybie offline.

1. Kliknij przycisk **zamówienie urządzenia Data Box**.
2. W **wybierz urządzenie Azure Data Box**, określ subskrypcję. 
3. Przeniesienie będzie Importuj na platformę Azure. Określ źródło danych i docelowego regionu platformy Azure dla danych.

## <a name="next-steps"></a>Następne kroki

Wypróbuj migrację za pomocą usługi Azure migracji serwera migracji dla [funkcji Hyper-V](tutorial-migrate-hyper-v.md) lub [VMware](tutorial-migrate-vmware.md) maszyn wirtualnych.
