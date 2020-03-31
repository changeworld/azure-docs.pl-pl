---
title: Poproś o zwiększenie kwoty
description: Na tej stronie opisano sposób tworzenia żądania pomocy technicznej w celu zwiększenia przydziałów dla pojedynczych baz danych, serwerów i wystąpień zarządzanych usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586159"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Zwiększenie przydziału żądania dla bazy danych SQL usługi Azure

W tym artykule wyjaśniono, jak zażądać zwiększenia przydziału dla usługi Azure SQL Database dla pojedynczych baz danych, serwerów i wystąpień zarządzanych. Wyjaśniono również, jak włączyć dostęp do subskrypcji do regionu.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Tworzenie nowego żądania pomocy technicznej

Skorzystaj z poniższych kroków, aby utworzyć nowe żądanie pomocy technicznej z witryny Azure portal for SQL Database.

1. W menu [portalu platformy Azure](https://portal.azure.com) wybierz pozycję Pomoc + pomoc **techniczna**.

   ![Łącze Pomoc + pomoc](./media/quota-increase-request/help-plus-support.png)

1. W **pomocy + pomocy ,** wybierz nowe żądanie pomocy **.**

    ![Tworzenie nowego żądania pomocy technicznej](./media/quota-increase-request/new-support-request.png)

1. W obszarze **Typ problemu**wybierz pozycję **Limity usługi i subskrypcji (przydziały).**

   ![Wybieranie typu problemu](./media/quota-increase-request/select-quota-issue-type.png)

1. W przypadku **opcji Subskrypcja**wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybieranie subskrypcji dla zwiększonego przydziału](./media/quota-increase-request/select-subscription-support-request.png)

1. W przypadku **typu przydziału**wybierz jeden z następujących typów przydziałów:

   - **Baza danych SQL** dla pojedynczej bazy danych i przydziałów puli elastycznej.
   - **Wystąpienie zarządzane bazy danych SQL** dla wystąpień zarządzanych.

   Następnie wybierz **przycisk Dalej: Rozwiązania >>**.

   ![Wybieranie typu przydziału](./media/quota-increase-request/select-quota-type.png)

1. W oknie **Szczegóły** wybierz pozycję **Podaj szczegóły,** aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/quota-increase-request/provide-details-link.png)

Kliknięcie **przycisku Podaj szczegóły** powoduje wyświetlenie okna **Szczegóły przydziału,** które umożliwia dodawanie dodatkowych informacji. W poniższych sekcjach opisano różne opcje dla typów przydziałów przydziałów **bazy danych SQL** i wystąpienia **zarządzanego bazy danych SQL.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Typy przydziałów bazy danych SQL

W poniższych sekcjach opisano trzy opcje zwiększenia przydziału dla typów przydziałów **bazy danych SQL:**

- Jednostki transakcji bazy danych (DTU) na serwer
- Serwery na subskrypcję
- Włączanie dostępu do subskrypcji do regionu

### <a name="database-transaction-units-dtus-per-server"></a>Jednostki transakcji bazy danych (DTU) na serwer

Poniższe kroki można wykonać, aby zażądać zwiększenia liczby procesorów DTU na serwer.

1. Wybierz **jednostki transakcji bazy danych (DTU) dla typu przydziału serwera.**

1. Na liście **Zasób** wybierz zasób docelowy.

1. W polu **Nowy przydział** wprowadź nowy wymagany limit DTU.

   ![Szczegóły przydziału DTU](./media/quota-increase-request/quota-details-dtus.png)

Aby uzyskać więcej informacji, zobacz [Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) i [limitów zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Serwery na subskrypcję

Skorzystaj z poniższych kroków, aby zażądać zwiększenia liczby serwerów na subskrypcję.

1. Wybierz typ **przydziału serwerów dla subskrypcji.**

1. Na liście **Lokalizacja** wybierz region platformy Azure do użycia. Przydział jest na subskrypcję w każdym regionie.

1. W polu **Nowy przydział** wprowadź żądanie dotyczące maksymalnej liczby serwerów w tym regionie.

   ![Szczegóły przydziału serwerów](./media/quota-increase-request/quota-details-servers.png)

Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL i zarządzanie zasobami](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Włączanie dostępu do subskrypcji do regionu

Niektóre typy ofert nie są dostępne w każdym regionie. Może pojawić się błąd, taki jak następujący:

`This location is not available for subscription`

Jeśli subskrypcja wymaga dostępu w określonym regionie, użyj opcji **Inne żądanie przydziału,** aby zażądać dostępu. W swoim żądaniu określ szczegóły oferty i jednostki SKU, które chcesz włączyć dla regionu. Aby zapoznać się z opcjami oferty i jednostek SKU, zobacz [Cennik bazy danych SQL Usługi Azure](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Inne szczegóły dotyczące kontyngentu](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Typ przydziału wystąpienia zarządzanego

W przypadku typu przydziału **wystąpienia zarządzanego programu SQL Server** należy wykonać następujące kroki:

1. Na liście **Region** wybierz region platformy Azure do docelowego.

1. Wprowadź nowe limity wymagane **Subnet** dla podsieci i **bramki wirtualnej**.

   ![Szczegóły przydziału wystąpienia zarządzanego](./media/quota-increase-request/quota-details-managed-instance.png)

Aby uzyskać więcej informacji, zobacz [Omówienie limitów zasobów zarządzanych wystąpień usługi Azure SQL Database](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Prześlij swoją prośbę

Ostatnim krokiem jest wypełnienie pozostałych szczegółów żądania przydziału bazy danych SQL. Następnie wybierz **przycisk Dalej: Przejrzyj + utwórz>>**, a po przejrzeniu szczegółów żądania kliknij przycisk **Utwórz,** aby przesłać żądanie.

## <a name="next-steps"></a>Następne kroki

Po przesłaniu wniosku zostanie ono rozpatrzone. Skontaktujemy się z Tobą z odpowiedzią na podstawie informacji podanych w formularzu.

Aby uzyskać więcej informacji na temat innych limitów platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
