---
title: Administrowanie Azure IoT centralnej aplikacji | Dokumentacja firmy Microsoft
description: Jako administratora jak zarządzać aplikacją Azure IoT centralnej
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: b60b9e851a3b6612964e67e7764ad8d43d606b4e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-administer-your-application"></a>Jak zarządzać aplikacją

Po utworzeniu aplikacji Microsoft Azure IoT centralnej, można użyć **administracji** sekcji Azure IoT centralnej interfejsu użytkownika do administrowania nim. Aby przejść do **administracji** wybierz **administracji** w menu nawigacji po lewej stronie.

**Administracji** sekcja umożliwia:

- Zarządzanie użytkownikami

- Zarządzaj rolami

- Wyświetlanie informacji dotyczących rozliczeń

- Zarządzaj ustawieniami aplikacji

- Rozszerzanie bezpłatnej wersji próbnej

W **administracji** są menu nawigacji dodatkowej wraz z łączami do różnych zadań administracyjnych.

Dostęp do użycia **administracji** sekcji, musisz być w **administratora** role w aplikacji Azure IoT centralna. Jeśli tworzysz aplikację Azure IoT centralnej, można automatycznie przypisanych do **administratora** roli dla tej aplikacji. *Zarządzanie użytkownikami* w tym artykule opisano bardziej dotyczące sposobu przypisywania **administratora** roli do innych użytkowników.

## <a name="change-application-name"></a>Zmień nazwę aplikacji

Aby zmienić nazwę aplikacji, użyj menu nawigacji dodatkowej, aby przejść do **ustawienia aplikacji** strony **administracji** sekcji.

Na **ustawienia aplikacji** strony, wprowadź nazwę wybraną w **Nazwa aplikacji** pola, a następnie wybierz pozycję **zapisać**.

## <a name="change-the-application-url"></a>Zmień adres URL aplikacji

Aby zmienić adres URL aplikacji, użyj menu nawigacji dodatkowej, aby przejść do **ustawienia aplikacji** strony **administracji** sekcji.

![Strony ustawień aplikacji](media\howto-administer\image0-a.png)

Na **ustawienia aplikacji** strony, wprowadź adres URL w wybranym **adres URL** pola, a następnie wybierz pozycję **zapisać**. Adres URL może mieć maksymalnie 200 znaków. Jeśli adres URL nie jest dostępny, zostanie wyświetlony błąd sprawdzania poprawności

> [!Note]
> Jeśli musisz zmienić adres URL, adres URL starego może zostać pobrany przez innego klienta Azure IoT centralnej. W takim przypadku go nie jest już dostępny do użycia. Po zmianie adres URL starego adresu URL nie będzie działać i należy powiadomić użytkowników o nowy adres URL do użycia.

## <a name="change-the-application-image"></a>Zmień obraz aplikacji

Aby uzyskać więcej informacji o używaniu obrazów w aplikacji Azure IoT centralnej, zobacz [przygotowanie i przekazywanie obrazów do aplikacji Azure IoT centralnej](howto-prepare-images.md).

## <a name="delete-an-application"></a>Usuwanie aplikacji

Aby usunąć aplikację, użyj menu nawigacji dodatkowej, aby przejść do **ustawienia aplikacji** strony **administracji** sekcji.

Wybierz **usunąć**.

> [!Note]
> Usunięcie aplikacji bezpowrotnie usuwa wszystkie dane skojarzone z aplikacją. Aby usunąć aplikację, musi również mieć praw do usuwania zasobów w subskrypcji platformy Azure została wybrana opcja utworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [Use Role-Based kontroli dostępu, aby zarządzać dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Role w środkowej IoT Azure

Role umożliwiają do kontrolowania, kto w organizacji, można wykonywać różne zadania Azure IoT centralnej. Azure IoT centralnego ma trzy role, które można przypisać do użytkowników aplikacji. Role są przypisywane przez każdej aplikacji. Ten sam użytkownik może mieć inne role w innych aplikacjach. Tego samego użytkownika mogą można przypisać do wielu ról w aplikacji.

### <a name="administrator"></a>Administrator

Użytkownicy w **administratora** rola ma dostęp do wszystkich funkcji w aplikacji Azure IoT centralnej.

Użytkownik tworzący aplikacji zostanie automatycznie przypisany do **administratora** roli. Zawsze musi być co najmniej jednego użytkownika w **administratora** roli.

### <a name="application-builder"></a>Konstruktor aplikacji

Użytkownicy w **Konstruktor aplikacji** roli może robić wszystko w aplikacji Azure IoT centralną, ale administrowania aplikacji.

### <a name="application-operator"></a>Operator aplikacji

Użytkownicy w **operatora aplikacji** rola nie ma dostępu do **Konstruktor aplikacji** strony. Nie można ich administrować aplikacji.

## <a name="manage-users"></a>Zarządzanie użytkownikami

Administratorzy aplikacji można przypisać użytkowników do ról w aplikacji.

### <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika przed ich Zaloguj się i uzyskać dostęp do aplikacji Azure IoT centralnej. Microsoft Accounts (MSA) i Azure Active Directory (AAD) są obsługiwane w Azure IoT centralnej. Azure grup usługi Active Directory nie są obecnie obsługiwane w Azure IoT centralnej.

Aby dowiedzieć się więcej, zobacz [pomocy konta Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) i [Szybki Start: dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać konto użytkownika do aplikacji Azure IoT centralnej, użyj menu nawigacji dodatkowej można przejść do **użytkowników** strony **administracji** sekcji.

    ![Lista użytkowników](media\howto-administer\image1.png)

1. Na **użytkowników** wybierz pozycję **+ Dodaj użytkownika** Aby dodać użytkownika.

    ![Dodaj użytkownika](media\howto-administer\image2.png)

1. Podczas dodawania użytkownika do aplikacji Azure IoT centralnej, wybierz rolę dla użytkownika z **roli** listy rozwijanej. Dowiedz się więcej o rolach w *role w programie Azure IoT centralnej* sekcji tego artykułu.

    ![Wybór roli](media\howto-administer\image3.png)

    > [!NOTE]
    >  Aby dodać użytkowników zbiorczo, wpisz nazwy użytkowników wszystkich użytkowników, które chcesz dodać rozdzielone średnikami. Wybierz rolę z **roli** listy rozwijanej i wybierz polecenie **zapisać**.

1. Po dodaniu użytkownika, pojawi się wpis dla tego użytkownika na **użytkowników** strony.

    ![Lista użytkowników](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Edytuj role przypisane do użytkowników

Role nie może być już zmieniane po assinged. Aby zmienić od roli przypisanej do użytkownika, Usuń użytkownika, a następnie dodaj go ponownie z inną rolą.

### <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, sprawdź co najmniej jeden element checkboxes na **użytkowników** strony, a następnie wybierz pozycję **usunąć**.

## <a name="view-your-bill"></a>Wyświetl rachunku

Aby wyświetlić rachunek, przejdź do **rozliczeń** strony **administracji** sekcji i wybierz polecenie **rozliczeń**. Otwiera stronę rozliczeń platformy Azure na nowej karcie, aby zobaczyć BOM dla poszczególnych aplikacji Azure IoT centralnej.

## <a name="convert-your-trial-to-a-paid-application"></a>Konwertuj Twojej wersji próbnej na płatną aplikacji

Po zostało obliczone centralnego IoT, okresu próbnego można przekonwertować na płatną aplikacji. Aby ukończyć ten proces samoobsługowego, wykonaj następujące kroki:

1. Użyj menu nawigacji dodatkowej, aby przejść do **rozliczeń** strony **administracji** sekcji. Jeśli nie zostało rozszerzone okresu próbnego, strony wygląda następująco:

    ![Bezpłatna wersja próbna stanu](media/howto-administer/freetrial.png)

2. Kliknij przycisk **Konwertuj do płatnej**. Jeśli nie zostało rozszerzone okresu próbnego, oknie podręcznym wygląda następująco:

    W oknie podręcznym wybierz odpowiednie dzierżawy usługi Azure Active Directory, a następnie subskrypcji Azure, która ma być używany dla aplikacji IoT centralnego.

    ![Rozszerzanie bezpłatnej wersji próbnej](media/howto-administer/extend.png)

3. Po kliknięciu **przekonwertować**, wersja próbna jest konwertowana na płatną aplikacji i rozpoczęciu pobierania rozliczane.

## <a name="extend-your-free-trial"></a>Rozszerzenie z bezpłatnej wersji próbnej

Domyślnie wszystkie bezpłatnych wersji próbnych są dostępne przez 7 dni. Jeśli chcesz zwiększyć do 30 dni okresu próbnego, możesz wykonaj następujące czynności:

1. Użyj menu nawigacji dodatkowej, aby przejść do **rozliczeń** strony **administracji** sekcji:

1. Kliknij przycisk **przedłużyć okres próbny**. W oknie podręcznym wybierz odpowiednie dzierżawy usługi Azure Active Directory i następnie subskrypcji platformy Azure na potrzeby aplikacji IoT środkowe:

1. Następnie kliknij przycisk **Rozszerz**. Wersja próbna teraz jest ważny przez 30 dni.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz administrowanie Azure IoT centralnej aplikacji, w tym miejscu jest sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Konfigurowanie szablonu urządzenia](howto-set-up-template.md)