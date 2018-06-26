---
title: MySQL serwerów na stosie Azure hosta | Dokumentacja firmy Microsoft
description: Jak dodać wystąpienia MySQL dla inicjowania obsługi administracyjnej za pośrednictwem dostawcy zasobów karty MySQL
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938455"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Dodawanie serwerów hostingu dla dostawcy zasobów MySQL

Wystąpienie programu MySQL na maszynie wirtualnej (VM) może obsługiwać w [stosu Azure](azure-stack-poc.md), lub na maszynie Wirtualnej poza środowiskiem Azure stosu, tak długo, jak dostawca zasobów MySQL można połączyć się z wystąpieniem.

## <a name="connect-to-a-mysql-hosting-server"></a>Połączenie z serwerem hostingu MySQL

Upewnij się, że masz poświadczenia dla konta z uprawnieniami administratora systemu. Aby dodać serwer hostingu, wykonaj następujące kroki:

1. Zaloguj się do portalu Azure stosu operatora jako administratora usługi.
2. Wybierz **więcej usług**.
3. Wybierz **zasobów administracyjnych** > **MySQL serwerów hosta** > **+ Dodaj**. Spowoduje to otwarcie **dodawania serwera hostingu MySQL** okna dialogowego, przedstawiono w następujących Przechwytywanie ekranu.

   ![Konfigurowanie serwera hostingu](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Podaj szczegóły połączenia z wystąpieniem serwera MySQL.

   * Aby uzyskać **nazwę serwera obsługującego MySQL**, podaj w pełni kwalifikowaną nazwę domeny (FQDN) lub prawidłowy adres IPv4. Nie używaj krótką nazwę maszyny Wirtualnej.
   * Domyślne wystąpienie MySQL nie został podany, dlatego należy określić **rozmiar hostingu serwera w GB**. Wprowadź rozmiar, który znajduje się w pobliżu pojemności serwera bazy danych.
   * Zachowaj ustawienie domyślne **subskrypcji**.
   * Aby uzyskać **grupy zasobów**, Utwórz nową, lub użyj istniejącej grupy.

   > [!NOTE]
   > Jeśli dzierżawcy i administratora usługi Azure Resource Manager można uzyskać dostępu do wystąpienia MySQL, można umieścić pod kontrolą dostawcy zasobów. Jednak wystąpienia MySQL **musi** można przydzielić wyłącznie do dostawcy zasobów.

5. Wybierz **jednostki SKU** otworzyć **utworzyć jednostki SKU** okna dialogowego.

   ![Utwórz MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Jednostka SKU **nazwa** powinien odzwierciedlać właściwości jednostki SKU, dlatego użytkownicy będą mogli wdrażać swoje bazy danych do odpowiednich jednostki SKU.

   >[!IMPORTANT]
   >Znaki specjalne, łącznie ze spacjami i okresów, nie są obsługiwane w **nazwa** lub **warstwy** podczas tworzenia jednostki SKU dla dostawcy zasobów MySQL.

6. Wybierz **OK** można utworzyć jednostki SKU.
7. W obszarze **dodawania serwera hostingu MySQL**, wybierz pozycję **Utwórz**.

W miarę dodawania serwerów, należy je przypisać do nowej lub istniejącej jednostki SKU rozróżnianie oferty usług. Na przykład można mieć wystąpienie enterprise MySQL, które udostępnia zwiększone bazy danych i automatycznego tworzenia kopii zapasowych. Ten serwer wysokiej wydajności dla różnych działów może zarezerwować w Twojej organizacji.

## <a name="increase-backend-database-capacity"></a>Zwiększ wydajność bazy danych zaplecza

Wdrażanie więcej serwerów MySQL w portalu Azure stosu może zwiększyć wydajność bazy danych zaplecza. Dodaj te serwery do nowej lub istniejącej jednostki SKU. Jeśli musisz dodać serwer do istniejącej jednostki SKU, upewnij się, że właściwości serwera są takie same, co pozostałe serwery w jednostki SKU.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Udostępnić użytkownikom serwerów baz danych MySQL

Tworzenie planów i oferty, aby udostępnić użytkownikom serwerów baz danych MySQL. Dodaj usługę Microsoft.MySqlAdapter planu, a następnie Dodaj domyślny limit przydziału lub utworzyć nowy przydział.

![Tworzenie planów i oferty dla baz danych](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>Kolejne kroki

[Utwórz bazę danych MySQL](azure-stack-mysql-resource-provider-databases.md)