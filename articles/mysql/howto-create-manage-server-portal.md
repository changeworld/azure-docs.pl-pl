---
title: Tworzenie i zarządzanie nimi — Azure Database dla serwera MySQL przy użyciu witryny Azure portal
description: W tym artykule opisano, jak możesz szybko utworzyć nowego serwera Azure Database for MySQL — do niego i zarządzać serwerem przy użyciu witryny Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6d6f24475497382dd9e04d3335fb89d6f0bdd514
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61459522"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Tworzenie i zarządzanie nimi — Azure Database dla serwera MySQL przy użyciu witryny Azure portal
W tym temacie opisano, jak szybko utworzyć nowego serwera Azure Database for MySQL. Zawiera także informacje o sposobie zarządzania serwerem za pomocą witryny Azure portal. Zarządzanie serwerem obejmuje wyświetlanie szczegółów dotyczących serwera i bazy danych, zresetowanie hasła, skalowanie zasobów i usunięcie serwera.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL
Wykonaj następujące kroki, aby utworzyć serwer Azure Database for MySQL o nazwie "mydemoserver."

1. Kliknij przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure portal.

2. Na nowej stronie wybierz **baz danych**, a następnie na stronie bazy danych, wybierz **— Azure Database for MySQL**.

    > Serwer Azure Database for MySQL jest tworzony ze zdefiniowanym zestawem [zasobów obliczeniowych i magazynowych](./concepts-pricing-tiers.md) zasobów. Baza danych jest tworzony w grupie zasobów platformy Azure i usługi Azure Database for MySQL server.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Wypełnij usługi Azure Database for MySQL formularza przy użyciu następujących informacji:

    | **Pole formularza** | **Opis pola** |
    |----------------|-----------------------|
    | *Nazwa serwera* | mydemoserver (nazwa serwera jest globalnie unikatowa) |
    | *Subskrypcja* | mysubscription (wybierz z menu rozwijanego) |
    | *Grupa zasobów* | myresourcegroup (Utwórz nową grupę zasobów lub użyć istniejącego) |
    | *Wybierz źródło* | Puste (Utwórz pusty serwer MySQL) |
    | *Identyfikator logowania administratora serwera* | myadmin (skonfiguruj nazwę konta administratora) |
    | *Hasło* | Ustaw hasło konta administratora |
    | *Potwierdź hasło* | potwierdź hasło konta administratora |
    | *Lokalizacja* | Azja południowo-wschodnia (wybór między Europa Północna i zachodnie stany USA) |
    | *Wersja* | (Wybierz — Azure Database dla MySQL server w wersji) w wersji 5.7 |

4. Kliknij przycisk **warstwa cenowa** do określenia usługi warstwy i poziomu wydajności dla nowego serwera. Wybierz **ogólnego przeznaczenia** kartę. *Generacja 5*, *2 rdzenie wirtualne*, *5 GB* oraz *7 dni* to wartości domyślne opcji **Generacja obliczeń**, **Rdzeń wirtualny**, **Magazyn** oraz **Okres przechowywania kopii zapasowej**. Te suwaki możesz zostawić bez zmian. Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** w pozycji **Opcje nadmiarowości kopii zapasowej**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Kliknij przycisk **Utwórz**, aby aprowizować serwer. Aprowizacja zajmuje kilka minut.

    > Wybierz **Przypnij do pulpitu nawigacyjnego** opcję, aby łatwo śledzić wdrożenia.

## <a name="update-an-azure-database-for-mysql-server"></a>Aktualizacja usługi Azure Database dla serwera MySQL
Po zainicjowaniu obsługi nowego serwera użytkownik ma kilka opcji dotyczących konfigurowania istniejącego serwera, w tym zresetowanie hasła administratora i skalowanie serwera w górę lub w dół, zmieniając (rdzeń wirtualny) lub magazyn.

### <a name="change-the-administrator-user-password"></a>Zmiana hasła użytkownika administratora
1. Z serwera **Przegląd**, kliknij przycisk **Resetuj hasło** do wyświetlenia okna resetowania hasła.

   ![overview](./media/howto-create-manage-server-portal/overview.png)

2. Wprowadź nowe hasło i Potwierdź hasło w oknie, jak pokazano:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Kliknij przycisk **OK** można zapisać nowego hasła.

### <a name="scale-vcores-updown"></a>Rdzenie wirtualne skalowanie w górę/w dół

1. Kliknij pozycję **warstwa cenowa**, który znajduje się w obszarze **ustawienia**.

2. Zmiana **vCore** ustawienie przesuwając suwak na żądaną wartość.

    ![Skalowanie zasobów obliczeniowych](./media/howto-create-manage-server-portal/scale-compute.png)

3. Kliknij przycisk **OK**, aby zapisać zmiany.

### <a name="scale-storage-up"></a>Skalowanie magazynu w górę

1. Kliknij pozycję **warstwa cenowa**, który znajduje się w obszarze **ustawienia**.

2. Zmiana **magazynu** ustawienie przesuwając suwak na żądaną wartość.

    ![scale-storage](./media/howto-create-manage-server-portal/scale-storage.png)

3. Kliknij przycisk **OK**, aby zapisać zmiany.

## <a name="delete-an-azure-database-for-mysql-server"></a>Usuwanie serwera Azure Database for MySQL

1. Z serwera **Przegląd**, kliknij przycisk **Usuń** przycisk, aby otworzyć monit o potwierdzenie usunięcia.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Wpisz nazwę serwera w polu wejściowym o potwierdzenie double.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Kliknij przycisk **Usuń** przycisk, aby potwierdzić usunięcie serwera. Poczekaj do pop "pomyślnie usunięto serwer MySQL" są wyświetlane na pasku powiadomień.

## <a name="list-the-azure-database-for-mysql-databases"></a>Lista usługi Azure Database for MySQL — bazy danych
Z serwera **Przegląd**, przewiń w dół, aż zobaczysz Kafelek w dolnej części bazy danych. Wszystkie bazy danych na serwerze są wyświetlane w tabeli.

   ![bazy danych show](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Pokaż szczegóły serwera Azure Database for MySQL
Kliknij pozycję **właściwości**, który znajduje się w obszarze **ustawienia** Aby wyświetlić szczegółowe informacje o serwerze.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Kolejne kroki

[Szybki start: Tworzenie usługi Azure Database dla serwera MySQL przy użyciu witryny Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)