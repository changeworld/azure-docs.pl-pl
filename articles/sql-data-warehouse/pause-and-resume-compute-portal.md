---
title: 'Szybki start: Wstrzymywanie i wznawianie obliczeń w Azure SQL Data Warehouse-Azure Portal | Microsoft Docs'
description: Użyj Azure Portal, aby wstrzymać obliczenia w Azure SQL Data Warehouse w celu oszczędności kosztów. Wznów obliczanie, gdy wszystko jest gotowe do korzystania z magazynu danych.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 46fb1fc6455cd35d8bb1eaf0a1b7ee499f5c1caf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574891"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Szybki start: Wstrzymywanie i wznawianie obliczeń dla Azure SQL Data Warehouse w Azure Portal

Użyj Azure Portal, aby wstrzymać obliczenia w Azure SQL Data Warehouse w celu oszczędności kosztów. [Wznów Obliczanie](sql-data-warehouse-manage-compute-overview.md) , gdy wszystko jest gotowe do korzystania z magazynu danych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Utwórz magazyn danych o nazwie **mySampleDataWarehouse**przy użyciu programu [Create and Connect-Portal](create-data-warehouse-portal.md) . 

## <a name="pause-compute"></a>Wstrzymywanie obliczeń

Aby zaoszczędzić koszty, możesz wstrzymywać i wznawiać zasoby obliczeniowe na żądanie. Na przykład jeśli baza danych nie będzie używana w porze nocnej i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia. Nie będzie naliczana opłata za zasoby obliczeniowe, gdy baza danych jest wstrzymana. Opłata za magazyn będzie jednak nadal naliczana. 

Wykonaj następujące kroki, aby wstrzymać SQL Data Warehouse.

1. Kliknij opcję **Bazy danych SQL** po lewej stronie witryny Azure Portal.
2. Wybierz opcję **mySampleDataWarehouse** ze strony **Bazy danych SQL**. Spowoduje to otworzenie magazynu danych. 
3. Na stronie **MySampleDataWarehouse** **stan** powiadomienia jest w **trybie online**.

    ![Oblicz w trybie online](media/pause-and-resume-compute-portal/compute-online.png)

4. Aby wstrzymać magazyn danych, kliknij przycisk **Wstrzymaj** . 
5. Zostanie wyświetlone pytanie z prośbą o potwierdzenie, czy chcesz kontynuować. Kliknij przycisk **Yes** (Tak).
6. Poczekaj chwilę, a następnie sprawdź, czy **stan** jest **wstrzymywany**.

    ![Wstrzymywanie](media/pause-and-resume-compute-portal/pausing.png)

7. Po zakończeniu operacji wstrzymania stan jest **wstrzymany** i przycisk opcji jest **uruchamiany**.
8. Zasoby obliczeniowe dla hurtowni danych są teraz w trybie offline. Nie zostanie naliczona opłata za obliczenia, dopóki usługa nie zostanie wznowiona.

    ![Oblicz w trybie offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Wznów Obliczanie

Wykonaj następujące kroki, aby wznowić SQL Data Warehouse.

1. Kliknij opcję **Bazy danych SQL** po lewej stronie witryny Azure Portal.
2. Wybierz opcję **mySampleDataWarehouse** ze strony **Bazy danych SQL**. Spowoduje to otworzenie magazynu danych. 
3. Na stronie **MySampleDataWarehouse** **stan** powiadomienia jest **wstrzymany**.

    ![Oblicz w trybie offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Aby wznowić magazyn danych, kliknij przycisk **Uruchom**. 
5. Zostanie wyświetlone pytanie z prośbą o potwierdzenie, czy chcesz zacząć. Kliknij przycisk **Yes** (Tak).
6. Zauważ, że **stan** jest **wznawiany**.

    ![Wznawianie](media/pause-and-resume-compute-portal/resuming.png)

7. Gdy magazyn danych jest w trybie online, stan jest w **trybie online** i przycisk opcji jest **wstrzymany**.
8. Zasoby obliczeniowe dla magazynu danych są teraz w trybie online i można korzystać z tej usługi. Opłaty za obliczenia zostały wznowione.

    ![Oblicz w trybie online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w magazynie danych. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno. 

- Jeśli chcesz zachować dane w magazynie, Wstrzymaj obliczenia.
- Aby uniknąć opłat w przyszłości, możesz usunąć magazyn danych. 

Wykonaj następujące kroki, aby wyczyścić zasoby zgodnie z potrzebami.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i kliknij magazyn danych.

    ![Oczyszczanie zasobów](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Aby wstrzymać obliczenia, kliknij przycisk **Wstrzymaj**. Gdy magazyn danych jest wstrzymany, zobaczysz przycisk **Uruchom**.  Aby wznowić obliczenia, kliknij przycisk **Uruchom**.

2. Aby usunąć magazyn danych i nie płacić za obliczenia oraz magazynowanie, kliknij przycisk **Usuń**.

3. Aby usunąć utworzony serwer SQL, kliknij pozycję **mynewserver-20171113.Database.Windows.NET**, a następnie kliknij pozycję **Usuń**.  Należy zachować ostrożność podczas usuwania, ponieważ usunięcie serwera spowoduje również usunięcie wszystkich baz danych przypisanych do tego serwera.

4. Aby usunąć grupę zasobów, kliknij pozycję **myResourceGroup**, a następnie kliknij pozycję **Usuń grupę zasobów**.


## <a name="next-steps"></a>Następne kroki

Teraz wstrzymane i wznowione obliczenia dla magazynu danych. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.

> [!div class="nextstepaction"]
> [Ładowanie danych do SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
