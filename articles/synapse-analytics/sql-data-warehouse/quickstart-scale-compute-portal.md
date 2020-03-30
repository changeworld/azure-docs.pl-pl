---
title: Skalowanie obliczeń dla puli SQL Synapse (portal Azure)
description: Można skalować obliczenia dla puli synapse SQL (magazyn danych) przy użyciu witryny Azure portal.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350834"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Szybki start: skalowanie obliczeń dla puli SQL Synapse za pomocą witryny Azure portal

Można skalować obliczenia dla puli synapse SQL (magazyn danych) przy użyciu witryny Azure portal. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Można skalować pulę SQL, która już masz lub użyć [przewodnika Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md) w celu utworzenia puli SQL o nazwie **mySampleaDataWarehouse**. W tym przewodniku Szybki start jest skalowana baza danych **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Pula SQL musi być w trybie online, aby skalować. 

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

Zasoby obliczeniowe puli SQL można skalować przez zwiększenie lub zmniejszenie jednostek magazynu danych. W ramach przewodnika Szybki start tworzenia i łączenia za pomocą portalu utworzono bazę danych **mySampleDataWarehouse** i zainicjowano ją z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić liczbę jednostek magazynu danych:

1. Kliknij **pozycję Usługa Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie witryny Azure portal.
2. Wybierz **mySampleDataWarehouse** ze strony **usługi Azure Synapse Analytics (dawniej SQL DW).** Zostanie otwarta pula SQL.
3. Kliknij przycisk **Skaluj**.

    ![Klikanie przycisku Skaluj](./media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu Skalowanie przesuń suwak w lewo lub w prawo, aby zmienić ustawienie jednostek DWU. Następnie wybierz skalę.

    ![Przesuwanie suwaka](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o puli SQL, przejdź do samouczka [Załaduj dane do puli SQL.](load-data-from-azure-blob-storage-using-polybase.md) 
