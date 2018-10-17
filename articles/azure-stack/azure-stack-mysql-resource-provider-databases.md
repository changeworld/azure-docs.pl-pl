---
title: Używanie baz danych, dostarczone przez jednostkę Uzależnioną karty MySQL na AzureStack | Dokumentacja firmy Microsoft
description: Jak utworzyć i zarządzać nimi aprowizowane za pomocą dostawcy zasobów MySQL karty baz danych MySQL
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
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 78c4e7c6cdc8cf118a351d9030416318e51c69af
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365105"
---
# <a name="create-mysql-databases"></a>Tworzenie bazy danych MySQL

Można tworzyć i zarządzać samoobsługowego baz danych w aplikacji portal użytkowników. Użytkownik usługi Azure Stack wymaga subskrypcji przy użyciu oferta, która obejmuje usługę bazy danych MySQL.

## <a name="test-your-deployment-by-creating-a-mysql-database"></a>Przetestować wdrożenie, tworząc bazę danych MySQL

1. Zaloguj się do aplikacji portal użytkowników usługi Azure Stack.
2. Wybierz **+ Utwórz zasób** > **dane + magazyn** > **bazy danych MySQL** > **Dodaj**.
3. W obszarze **tworzenie bazy danych MySQL**, wprowadź nazwę bazy danych i skonfiguruj inne ustawienia zgodnie z wymaganiami dla danego środowiska.

    ![Utwórz test bazy danych MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. W obszarze **Create Database**, wybierz opcję **jednostki SKU**. W obszarze **wybierz jednostkę SKU MySQL**, wybierz jednostki SKU dla bazy danych.

    ![Wybierz jednostkę SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Ponieważ serwery hostingu są dodawane do usługi Azure Stack, są przydzielone jednostki SKU. Bazy danych są tworzone w puli serwerów w jednostce SKU hosta.

5. W obszarze **logowania**, wybierz opcję ***Skonfiguruj wymagane ustawienia***.
6. W obszarze **wybierz identyfikator logowania**, możesz wybrać istniejącą nazwę logowania lub wybierz **+ Utwórz nowe dane logowania** skonfigurować nową nazwę logowania.  Wprowadź **logowania do bazy danych** nazwy i **hasło**, a następnie wybierz pozycję **OK**.

    ![Utwórz nowe nazwy logowania bazy danych](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Długość nazwy logowania bazy danych nie może przekraczać 32 znaków MySQL 5.7. We wcześniejszych wersjach nie może przekraczać 16 znaków.

7. Wybierz **Utwórz** do zakończenia konfigurowania bazy danych.

Po wdrożeniu bazy danych, zwróć uwagę na **parametry połączenia** w obszarze **Essentials**. Można użyć tego ciągu w każdej aplikacji, która wymaga dostępu do bazy danych MySQL.

![Pobieranie parametrów połączenia dla bazy danych MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Zaktualizuj hasło administracyjne

Hasło można zmodyfikować, zmieniając go na wystąpienie serwera MySQL.

1. Wybierz **zasoby administracyjne** > **hostowania serwerów MySQL**. Wybierz serwer hostingu.
2. W obszarze **ustawienia**, wybierz opcję **hasło**.
3. W obszarze **hasło**, wprowadź nowe hasło, a następnie wybierz **Zapisz**.

![Zaktualizuj hasło administratora](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Kolejne kroki

[Aktualizowanie dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-update.md)
