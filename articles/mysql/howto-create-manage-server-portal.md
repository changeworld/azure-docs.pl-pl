---
title: Zarządzanie serwerem — witryna Azure portal — usługa Azure Database for MySQL
description: Dowiedz się, jak zarządzać usługą Azure Database dla serwera MySQL z witryny Azure portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c0bee0b628a49746a19545d14b8b8761d0e880d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062415"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Zarządzanie usługą Azure Database dla serwera MySQL przy użyciu witryny Azure portal
W tym artykule pokazano, jak zarządzać usługą Azure Database dla serwerów MySQL. Zadania zarządzania obejmują skalowanie danych obliczeniowych i magazynowania, resetowanie hasła administratora oraz wyświetlanie szczegółów serwera.

## <a name="sign-in"></a>Logowanie
Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Tworzenie serwera
Odwiedź [przewodnik Szybki start,](quickstart-create-mysql-server-database-using-azure-portal.md) aby dowiedzieć się, jak utworzyć i rozpocząć korzystanie z usługi Azure Database dla serwera MySQL.

## <a name="scale-compute-and-storage"></a>Skalowanie obliczeń i pamięci masowej

Po utworzeniu serwera można skalować warstwy ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci w miarę zmiany potrzeb. Można również skalować obliczenia i pamięć, zwiększając lub zmniejszając liczbę wirtualnych. Magazyn można skalować w górę (jednak nie można skalować w dół).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Skalowanie między warstwami ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci

Można skalować z ogólnego przeznaczenia do zoptymalizowane pod kątem pamięci i odwrotnie. Zmiana na i z warstwy Podstawowa po utworzeniu serwera nie jest obsługiwana. 

1. Wybierz serwer w witrynie Azure portal. Wybierz **warstwę cenową**, znajdującą się w sekcji **Ustawienia.**

2. Wybierz **opcję Ogólne przeznaczenie** lub **Zoptymalizowane pod kątem pamięci**, w zależności od tego, do czego się skalujesz. 

    ![warstwa zmiany cen](./media/howto-create-manage-server-portal/change-pricing-tier.png)

    > [!NOTE]
    > Zmiana warstw powoduje ponowne uruchomienie serwera.

4. Wybierz **przycisk OK,** aby zapisać zmiany.


### <a name="scale-vcores-up-or-down"></a>Skalowanie korli wirtualnych w górę lub w dół

1. Wybierz serwer w witrynie Azure portal. Wybierz **warstwę cenową**, znajdującą się w sekcji **Ustawienia.**

2. Zmień ustawienie **vCore,** przesuwając suwak do żądanej wartości.

    ![obliczenia skali](./media/howto-create-manage-server-portal/scaling-compute.png)

    > [!NOTE]
    > Skalowanie korli wirtualnych powoduje ponowne uruchomienie serwera.

3. Wybierz **przycisk OK,** aby zapisać zmiany.


### <a name="scale-storage-up"></a>Skalowanie pamięci masowej w górę

1. Wybierz serwer w witrynie Azure portal. Wybierz **warstwę cenową**, znajdującą się w sekcji **Ustawienia.**

2. Zmień ustawienie **Magazyn,** przesuwając suwak do żądanej wartości.

    ![magazyn skalny](./media/howto-create-manage-server-portal/scaling-storage.png)

    > [!NOTE]
    > Nie można zmniejszyć pojemności.

3. Wybierz **przycisk OK,** aby zapisać zmiany.


## <a name="update-admin-password"></a>Aktualizacja hasła administratora
Hasło roli administratora można zmienić za pomocą witryny Azure Portal.

1. Wybierz serwer w witrynie Azure portal. W oknie **Przegląd** wybierz **pozycję Resetuj hasło**.

   ![overview](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Wprowadź nowe hasło i potwierdź hasło. Pole tekstowe wyświetli monit o wymagania dotyczące złożoności hasła.

   ![resetowanie hasła](./media/howto-create-manage-server-portal/reset-password.png)

3. Wybierz **przycisk OK,** aby zapisać nowe hasło.


## <a name="delete-a-server"></a>Usuwanie serwera

Serwer można usunąć, jeśli nie jest już potrzebny. 

1. Wybierz serwer w witrynie Azure portal. W oknie **Przegląd** wybierz pozycję **Usuń**.

    ![delete](./media/howto-create-manage-server-portal/overview-delete.png)

2. Wpisz nazwę serwera w polu wejściowym, aby potwierdzić, że jest to serwer, który chcesz usunąć.

    ![potwierdzanie-usuwanie](./media/howto-create-manage-server-portal/confirm-delete.png)

    > [!NOTE]
    > Usunięcie serwera jest nieodwracalne.

3. Wybierz pozycję **Usuń**.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [kopiach zapasowych i przywracaniu serwera](howto-restore-server-portal.md)
- Dowiedz się więcej o [opcjach dostrajania i monitorowania w usłudze Azure Database for MySQL](concepts-monitoring.md)
