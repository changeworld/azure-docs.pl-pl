---
title: 'Szybki start: Wstrzymywanie i wznawianie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse — Azure portal | Dokumentacja firmy Microsoft'
description: Użyj portalu Azure w celu wstrzymania obliczeń w usłudze Azure SQL Data Warehouse w celu obniżenia kosztów. Gdy wszystko jest gotowe do użycia z hurtowni danych wznowić operacje obliczeniowe.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9c3ed6dd79d6225b38751c910253cfa1f0720d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475601"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Szybki start: Wstrzymywać i wznawiać moc obliczeniową na potrzeby usługi Azure SQL Data Warehouse w witrynie Azure portal

Użyj portalu Azure w celu wstrzymania obliczeń w usłudze Azure SQL Data Warehouse w celu obniżenia kosztów. [Wznowić operacje obliczeniowe](sql-data-warehouse-manage-compute-overview.md) gdy wszystko będzie gotowe do użycia w magazynie danych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Użyj [tworzenie i łączenie — portal](create-data-warehouse-portal.md) Aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Wstrzymaj obliczeń

W celu obniżenia kosztów, można wstrzymywać i wznawiać obliczeniowego zasoby na żądanie. Na przykład jeśli użytkownik nie będzie używany bazy danych w nocy i w weekendy, można wstrzymywać je w tych godzinach i je wznowić w ciągu dnia. Opłata zostanie naliczona za zasoby obliczeniowe, gdy baza danych jest wstrzymany. Jednak będą nadal naliczane za magazyn. 

Wykonaj następujące kroki, aby wstrzymać usłudze SQL data warehouse.

1. Kliknij opcję **Bazy danych SQL** po lewej stronie witryny Azure Portal.
2. Wybierz opcję **mySampleDataWarehouse** ze strony **Bazy danych SQL**. Spowoduje to otworzenie magazynu danych. 
3. Na **mySampleDataWarehouse** strony, zwróć uwagę, **stan** jest **Online**.

    ![Obliczenia w trybie online](media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać w magazynie danych, kliknij przycisk **wstrzymać** przycisku. 
5. Pojawi się pytanie potwierdzenia z pytaniem, jeśli chcesz kontynuować. Kliknij przycisk **Yes** (Tak).
6. Poczekaj chwilę i zwróć uwagę, **stan** jest **wstrzymywanie**.

    ![Wstrzymywanie](media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji wstrzymywania jego stan to **wstrzymana** i przycisk opcji **Start**.
8. Zasoby obliczeniowe dla magazynu danych są teraz w trybie offline. Opłata nie zostanie naliczona za obliczenia, dopóki nie można wznowić działanie usługi.

    ![Obliczenia w trybie offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznawianie obliczeń

Wykonaj następujące kroki, aby wznowić działanie w usłudze SQL data warehouse.

1. Kliknij opcję **Bazy danych SQL** po lewej stronie witryny Azure Portal.
2. Wybierz opcję **mySampleDataWarehouse** ze strony **Bazy danych SQL**. Spowoduje to otworzenie magazynu danych. 
3. Na **mySampleDataWarehouse** strony, zwróć uwagę, **stan** jest **wstrzymana**.

    ![Obliczenia w trybie offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Aby wznowić w magazynie danych, kliknij przycisk **Start**. 
5. Pojawi się pytanie potwierdzenia z pytaniem, jeśli chcesz rozpocząć. Kliknij przycisk **Yes** (Tak).
6. Zwróć uwagę **stan** jest **wznawianie**.

    ![Wznawianie](media/pause-and-resume-compute-portal/resuming.png)

7. Gdy magazyn danych jest wróci do trybu online, stan jest **Online** i przycisk opcji **Wstrzymaj**.
8. Zasoby obliczeniowe dla magazynu danych są teraz w trybie online i korzystać z niej. Opłaty za obliczenia zostały wznowione.

    ![Obliczenia w trybie online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno. 

- Jeśli chcesz przechowywać dane w magazynie wstrzymać obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych. 

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i kliknij swój magazyn danych.

    ![Oczyszczanie zasobów](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, kliknij przycisk **Uruchom**.

2. Aby usunąć magazyn danych i nie płacić za obliczenia oraz magazynowanie, kliknij przycisk **Usuń**.

3. Aby usunąć utworzony serwer SQL, kliknij **mynewserver-20171113.database.windows.net**, a następnie kliknij przycisk **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

4. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.


## <a name="next-steps"></a>Kolejne kroki

Masz teraz wstrzymywanie i wznawianie obliczeń dla magazynu danych. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.

> [!div class="nextstepaction"]
> [Ładowanie danych do magazynu danych SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
