---
title: Tworzenie i zarządzanie Azure bazy danych dla serwera MySQL przy użyciu portalu Azure
description: W tym artykule opisano, jak szybko utworzyć nową bazę danych Azure dla serwera MySQL i zarządzanie serwerem przy użyciu portalu Azure.
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 4b52cb9e42e582d42424c2814e2e30f764a8679b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Tworzenie i zarządzanie Azure bazy danych dla serwera MySQL przy użyciu portalu Azure
W tym temacie opisano, jak szybko utworzyć nową bazę danych Azure dla serwera MySQL. Zawiera także informacje o sposobie zarządzania serwerem przy użyciu portalu Azure. Zarządzanie serwerem obejmuje, wyświetlanie szczegółów serwera i bazy danych, zresetuj hasło, skalowania zasobów i usuwanie serwera.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL
Wykonaj następujące kroki, aby utworzyć bazę danych Azure MySQL serwera o nazwie "mydemoserver."

1. Kliknij przycisk **Utwórz zasób** przycisk znajduje się w lewym górnym rogu portalu Azure.

2. Na nowej stronie, wybierz **baz danych**, a następnie na stronie baz danych, wybierz **bazy danych Azure dla programu MySQL**.

    > Baza danych Azure dla serwera MySQL jest tworzony z zdefiniowanego zestawu [obliczeniowej i pamięci masowej](./concepts-pricing-tiers.md) zasobów. Baza danych została utworzona w ramach grupy zasobów platformy Azure i w bazie danych Azure dla serwera MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Wypełnianie bazy danych Azure dla formularza MySQL, korzystając z następujących informacji:

    | **Pole formularza** | **Opis pola** |
    |----------------|-----------------------|
    | *Nazwa serwera* | mydemoserver (nazwa serwera jest globalnie unikatowa) |
    | *Subskrypcja* | mysubscription (wybierz z menu rozwijanego) |
    | *Grupa zasobów* | myresourcegroup (Utwórz nową grupę zasobów lub użyć istniejącego) |
    | *Wybierz źródło* | Puste (utworzyć pusty serwer MySQL) |
    | *Identyfikator logowania administratora serwera* | myadmin (skonfiguruj nazwę konta administratora) |
    | *Hasło* | Ustaw hasło konta administratora |
    | *Potwierdź hasło* | potwierdź hasło konta administratora |
    | *Lokalizacja* | Południowo-Wschodnia, Azja (wybór między Europa Północna, Europa i zachodnie stany USA) |
    | *Wersja* | 5.7 (Wybierz bazy danych Azure w wersji server MySQL) |

4. Kliknij przycisk **warstwa cenowa** umożliwia określenie poziomu wydajności i warstwy usługi dla nowego serwera. Wybierz **ogólnego przeznaczenia** kartę. *Generacja 4*, *2 rdzenie wirtualne*, *5 GB* oraz *7 dni* to wartości domyślne opcji **Generowanie obliczeń**, **Rdzeń wirtualny**, **Magazyn** oraz **Okres przechowywania kopii zapasowej**. Te suwaki możesz zostawić bez zmian. Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** w pozycji **Opcje nadmiarowości kopii zapasowej**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Kliknij przycisk **Utwórz**, aby aprowizować serwer. Aprowizacja zajmuje kilka minut.

    > Wybierz **Przypnij do pulpitu nawigacyjnego** opcję, aby umożliwić łatwe monitorowanie wdrożeń.

## <a name="update-an-azure-database-for-mysql-server"></a>Aktualizacja bazy danych Azure dla serwera MySQL
Po zainicjowano nowy serwer, użytkownik ma kilka sposobów konfigurowania istniejącego serwera, w tym zresetowanie hasła administratora i skalowanie w górę lub w dół serwera, zmieniając vCore lub pamięci masowej.

### <a name="change-the-administrator-user-password"></a>Zmień hasło użytkownika administratora
1. Z serwera **omówienie**, kliknij przycisk **resetowania hasła** do wyświetlenia okna resetowania hasła.

   ![overview](./media/howto-create-manage-server-portal/overview.png)

2. Wprowadź nowe hasło i Potwierdź hasło w oknie, jak pokazano:

   ![Zresetuj hasło](./media/howto-create-manage-server-portal/reset-password.png)

3. Kliknij przycisk **OK** można zapisać nowego hasła.

### <a name="scale-vcores-updown"></a>VCores skalowania w górę lub w dół

1. Polecenie **warstwa cenowa**, który znajduje się w obszarze **ustawienia**.

2. Zmień **vCore** ustawienie za pomocą suwaka na żądaną wartość.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kliknij przycisk **OK**, aby zapisać zmiany.

### <a name="scale-storage-up"></a>Magazynu skalowania w górę

1. Polecenie **warstwa cenowa**, który znajduje się w obszarze **ustawienia**.

2. Zmień **magazynu** ustawienie za pomocą suwaka na żądaną wartość.

    ![magazynu w skali](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="delete-an-azure-database-for-mysql-server"></a>Usuwanie bazy danych Azure dla serwera MySQL

1. Z serwera **omówienie**, kliknij przycisk **usunąć** przycisk, aby otworzyć monit o potwierdzenie usunięcia.

    ![usuwanie](./media/howto-create-manage-server-portal/delete.png)

2. Wpisz nazwę serwera w polu wejściowy o potwierdzenie dwa razy.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Kliknij przycisk **usunąć** przycisk, aby potwierdzić usunięcie serwera. Poczekaj do pop "pomyślnie usunięto server MySQL" są wyświetlane na pasku powiadomień.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista Azure bazy danych dla baz danych MySQL
Z serwera **omówienie**, przewiń w dół, aż zostanie wyświetlony Kafelek w dolnej części bazy danych. Wszystkie bazy danych na serwerze są wyświetlane w tabeli.

   ![Pokaż-baz danych](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Pokaż szczegóły bazy danych MySQL serwera Azure
Polecenie **właściwości**, który znajduje się w obszarze **ustawienia** Aby wyświetlić szczegółowe informacje o serwerze.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Kolejne kroki

[Szybki Start: Tworzenie bazy danych platformy Azure dla serwera MySQL przy użyciu portalu Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)