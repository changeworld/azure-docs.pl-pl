---
title: 'Szybki start: skalowanie w poziomie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse — Azure Portal | Microsoft Docs'
description: Skalowanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse w witrynie Azure Portal. Skalowanie zasobów obliczeniowych w poziomie zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1abafb011a445847757af3efb9e0ea4e6170408d
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285537"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Szybki start: skalowanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse w witrynie Azure Portal

Skalowanie zasobów obliczeniowych w usłudze Azure SQL Data Warehouse w witrynie Azure Portal. [Skalowanie zasobów obliczeniowych w poziomie](sql-data-warehouse-manage-compute-overview.md) zapewnia lepszą wydajność, a zmniejszenie ich skali pozwala ograniczyć koszty. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Skalować można istniejący magazyn danych lub można wykonać czynności opisane w przewodniku [Szybki start: tworzenie i łączenie — portal](create-data-warehouse-portal.md), aby utworzyć magazyn danych o nazwie **mySampleDataWarehouse**.  W tym przewodniku Szybki start jest skalowana baza danych **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Skalowanie zasobów obliczeniowych

W usłudze SQL Data Warehouse można zwiększyć lub zmniejszyć ilość zasobów obliczeniowych przez odpowiednie dostosowanie jednostek magazynu danych. Postępując według czynności opisanych w artykule [Tworzenie i łączenie — portal](create-data-warehouse-portal.md) utworzono bazę danych **mySampleDataWarehouse** z 400 jednostkami DWU. Poniższe kroki umożliwiają dostosowanie liczby jednostek DWU dla bazy danych **mySampleDataWarehouse**.

Aby zmienić liczbę jednostek magazynu danych:

1. Kliknij pozycję **Magazyny danych SQL** po lewej stronie witryny Azure Portal.
2. Wybierz pozycję **mySampleDataWarehouse** ze strony **Magazyny danych SQL**. Zostanie otwarty magazyn danych.
3. Kliknij przycisk **Skaluj**.

    ![Klikanie przycisku Skaluj](media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu Skalowanie przesuń suwak w lewo lub w prawo, aby zmienić ustawienie jednostek DWU.

    ![Przesuwanie suwaka](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij pozycję **tak**, aby potwierdzić, lub **nie**, aby anulować.

    ![Klikanie pozycji Zapisz.](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Następne kroki
Teraz już wiesz, jak skalować zasoby obliczeniowe na potrzeby magazynu danych. Aby dowiedzieć się więcej na temat usługi Azure SQL Data Warehouse, przejdź do samouczka na temat ładowania danych.

> [!div class="nextstepaction"]
>[Ładowanie danych do magazynu danych SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
