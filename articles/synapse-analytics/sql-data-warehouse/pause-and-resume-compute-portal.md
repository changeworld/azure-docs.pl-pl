---
title: Wstrzymanie i wznowienie obliczeń w puli SQL Synapse za pośrednictwem portalu Azure
description: Użyj witryny Azure Portal, aby wstrzymać obliczenia dla puli SQL, aby zaoszczędzić koszty. Wznów obliczenia, gdy będziesz gotowy do użycia magazynu danych.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350954"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Szybki start: wstrzymanie i wznowienie obliczeń w puli SQL Synapse za pośrednictwem witryny Azure portal

Za pomocą portalu Azure można wstrzymać i wznowić zasoby obliczeniowe puli SQL Synapse (hurtownia danych). Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użyj [Create and Connect - portal,](create-data-warehouse-portal.md) aby utworzyć pulę SQL o nazwie **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Wstrzymanie obliczeń

Aby zmniejszyć koszty, można wstrzymać i wznowić zasoby obliczeniowe na żądanie. Na przykład jeśli nie będzie korzystać z bazy danych w nocy i w weekendy, można wstrzymać go w tych godzinach i wznowić go w ciągu dnia. 
>[!NOTE]
>Nie zostanie naliczona opłata za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Jednak nadal będzie naliczana opłata za przechowywanie. 

Wykonaj następujące kroki, aby wstrzymać pulę SQL:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Kliknij **pozycję Usługa Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie nawigacji w witrynie Azure portal.
2. Wybierz **mySampleDataWarehouse** ze strony **usługi Azure Synapse Analytics (dawniej SQL DW),** aby otworzyć pulę SQL. 
3. Na stronie **mySampleDataWarehouse,** zawiadomienie **Stan** jest **online**.

    ![Obliczeń online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać pulę SQL, kliknij przycisk **Wstrzymaj.** 
5. Pojawi się pytanie potwierdzające z pytaniem, czy chcesz kontynuować. Kliknij **przycisk Tak**.
6. Poczekaj kilka chwil, a następnie zwróć uwagę na **stan** jest **wstrzymywanie**.

    ![Wstrzymywanie](./media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji pauzy stan jest **wstrzymany,** a przycisk opcji to **Wznów .**
8. Zasoby obliczeniowe dla puli SQL są teraz w trybie offline. Opłata nie zostanie naliczona za obliczenia, dopóki nie wznowisz usługi.

    ![Obliczanie w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznów obliczeń

Wykonaj następujące kroki, aby wznowić pulę SQL.

1. Kliknij **pozycję Usługa Azure Synapse Analytics (dawniej SQL DW)** na lewej stronie witryny Azure portal.
2. Wybierz **mySampleDataWarehouse** ze strony **usługi Azure Synapse Analytics (dawniej SQL DW),** aby otworzyć stronę puli SQL. 
3. Na stronie **mySampleDataWarehouse** powiadomienie **o stanie** jest **wstrzymane**.

    ![Obliczanie w trybie offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Aby wznowić pulę SQL, kliknij przycisk **Wznów**. 
5. Pojawi się pytanie potwierdzające z pytaniem, czy chcesz rozpocząć. Kliknij **przycisk Tak**.
6. Zwróć uwagę, **że stan** jest **wznawianie**.

    ![Wznawianie](./media/pause-and-resume-compute-portal/resuming.png)

7. Gdy pula SQL jest z powrotem w trybie online, stan jest **online,** a przycisk opcji jest **Pause**.
8. Zasoby obliczeniowe dla puli SQL są teraz w trybie online i można z niej korzystać. Opłaty za obliczenia zostały wznowione.

    ![Obliczeń online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno. 

- Jeśli chcesz zachować dane w magazynie, wstrzymaj obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć pulę SQL. 

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij pulę SQL.

    ![Oczyszczanie zasobów](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. 

2. Aby usunąć pulę SQL, aby nie była naliczana opłata za zasoby obliczeniowe lub magazyn, kliknij przycisk **Usuń**.

3. Aby usunąć utworzony serwer SQL, kliknij **przycisk sqlpoolservername.database.windows.net**, a następnie kliknij przycisk **Usuń**.  

   > [!CAUTION]
   > Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

5. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.


## <a name="next-steps"></a>Następne kroki

Teraz wstrzymałeś i wznowiono obliczenia dla puli SQL. Przejdź do następnego artykułu, aby dowiedzieć się więcej o tym, jak [załadować dane do puli SQL](load-data-from-azure-blob-storage-using-polybase.md). Aby uzyskać dodatkowe informacje dotyczące zarządzania możliwościami obliczeniowymi, zobacz [omówienie zarządzania obliczeniami.](sql-data-warehouse-manage-compute-overview.md) 

