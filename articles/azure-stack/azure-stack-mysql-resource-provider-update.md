---
title: Przy użyciu baz danych dostarczonych przez RP karty MySQL na AzureStack | Dokumentacja firmy Microsoft
description: Jak utworzyć i zarządzać udostępniane przy użyciu dostawcy zasobów programu MySQL karty baz danych MySQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 0a900d75315fd0015633c036877faef84c48d65b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031832"
---
# <a name="create-mysql-databases"></a>Tworzenie baz danych MySQL

Można utworzyć i zarządzać nimi Samoobsługowe baz danych w aplikacji portal użytkowników. Użytkownik Azure stos musi subskrypcji z ofertą, która obejmuje usługi bazy danych MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Przetestować wdrożenie, tworząc bazę danych MySQL

1. Zaloguj się do portalu Azure stosu użytkownika.
2. Wybierz **+ nowy** > **dane i magazyn** > **baza danych MySQL** > **dodać**.
3. W obszarze **tworzenie bazy danych MySQL**, wprowadź nazwę bazy danych i skonfigurować inne ustawienia zgodnie z wymaganiami dla danego środowiska.

    ![Tworzenie nowego testu baza danych MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. W obszarze **Create Database**, wybierz pozycję **SKU**. W obszarze **wybierz MySQL SKU**, wybierz jednostki SKU dla Twojej bazy danych.

    ![Wybierz MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

    >[!Note]
    >Zgodnie z serwerami hostingu są dodawane do stosu Azure, są przydzielone jednostki SKU. Bazy danych są tworzone w puli serwerów w jednostce SKU hosta.

5. W obszarze **logowania**, wybierz pozycję ***Skonfiguruj wymagane ustawienia***.
6. W obszarze **wybierz nazwę logowania**, można wybrać istniejące dane logowania lub wybierz **+ Utwórz nowe dane logowania** skonfigurować nowe dane logowania.  Wprowadź **nazwy logowania bazy danych** nazwy i **hasło**, a następnie wybierz **OK**.

    ![Utwórz nowe nazwy logowania bazy danych](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Długość nazwy logowania bazy danych nie może przekraczać 32 znaków MySQL 5.7. We wcześniejszych wersjach go nie może przekraczać 16 znaków.

7. Wybierz **Utwórz** do zakończenia konfigurowania bazy danych.

Po wdrożeniu bazy danych, zwróć uwagę na **ciąg połączenia** w obszarze **Essentials**. Te parametry można użyć w dowolnej aplikacji, który ma dostęp do bazy danych MySQL.

![Pobierz ciąg połączenia bazy danych MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Zaktualizuj hasło administracyjne

Hasło można modyfikować, zmieniając go na wystąpienie serwera MySQL.

1. Wybierz **zasobów administracyjnych** > **MySQL serwerów hosta**. Wybierz serwer hostingu.
2. W obszarze **ustawienia**, wybierz pozycję **hasło**.
3. W obszarze **hasło**, wprowadź nowe hasło, a następnie wybierz **zapisać**.

![Zaktualizuj hasło administratora](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów MySQL](azure-stack-mysql-resource-provider-maintain.md)