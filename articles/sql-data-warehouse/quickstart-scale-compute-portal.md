---
title: 'Szybki Start: skalowanie obliczeniowe — Azure Portal '
description: Skalowanie obliczeń w puli SQL w Azure Portal. Skalowanie zasobów obliczeniowych w poziomie zapewnia lepszą wydajność, a ich ponowne przeskalowanie pozwala ograniczyć koszty.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200355"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Szybki Start: skalowanie zasobów obliczeniowych w puli SQL usługi Azure Synapse Analytics w Azure Portal

Skalowanie obliczeń w puli SQL w Azure Portal. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a ich ponowne przeskalowanie pozwala ograniczyć koszty. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Azure portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Możesz skalować pulę SQL, która już istnieje, lub użyć [szybkiego startu: Tworzenie i łączenie — Portal](create-data-warehouse-portal.md) , aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**.  W tym przewodniku Szybki start jest skalowana baza danych **mySampleDataWarehouse**.

>[!Note]
>Pula SQL musi być w trybie online, aby można było skalować ją. 

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Zasoby obliczeniowe puli SQL można skalować przez zwiększenie lub zmniejszenie liczby jednostek magazynu danych. W ramach przewodnika Szybki start tworzenia i łączenia za pomocą portalu utworzono bazę danych **mySampleDataWarehouse** i zainicjowano ją z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić jednostki magazynu danych:

1. Kliknij pozycję **Azure Synapse Analytics (wcześniej SQL DW)** na lewej stronie Azure Portal.
2. Wybierz pozycję **mySampleDataWarehouse** na stronie **usługi Azure Synapse Analytics (dawniej SQL DW)** . Zostanie otwarta Pula SQL.
3. Kliknij przycisk **Skaluj**.

    ![Klikanie przycisku Skaluj](media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu Skalowanie przesuń suwak w lewo lub w prawo, aby zmienić ustawienie jednostek DWU. Następnie wybierz pozycję Skala.

    ![Przesuwanie suwaka](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Następne kroki
Teraz wiesz już, jak skalować obliczenia dla puli SQL. Aby dowiedzieć się więcej o puli SQL, przejdź do samouczka dotyczącego ładowania danych.

> [!div class="nextstepaction"]
>[Ładowanie danych do puli SQL](load-data-from-azure-blob-storage-using-polybase.md)
