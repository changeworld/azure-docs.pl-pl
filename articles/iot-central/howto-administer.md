---
title: Administrowanie aplikacją usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako administratora jak zarządzać aplikacją usługi Azure IoT Central
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a43febf1e78f80451b6aeed19e095b2c313d3216
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284857"
---
# <a name="how-to-administer-your-application"></a>Jak zarządzać aplikacją

Po utworzeniu aplikacji Microsoft Azure IoT Central, można użyć **administracji** części interfejsu użytkownika usługi Azure IoT Central, zarządzania nim. Aby przejść do **administracji** wybierz pozycję **administracji** w menu nawigacji po lewej stronie.

**Administracji** sekcja umożliwia:

- Zarządzanie użytkownikami

- Zarządzanie rolami

- Wyświetlanie informacji dotyczących rozliczeń

- Zarządzaj ustawieniami aplikacji

- Rozszerzanie bezpłatnej wersji próbnej

W **administracji** sekcji znajduje się menu Nawigacja drugorzędna wraz z łączami do różnych zadań administracyjnych.

Aby uzyskać dostęp do **administracji** sekcji, musisz mieć **administratora** roli w aplikacji usługi Azure IoT Central. Możesz utworzyć aplikację usługi Azure IoT Central, możesz automatycznie przypisywany do **administratora** roli dla tej aplikacji. *Zarządzanie użytkownikami* sekcję w tym artykule opisano bardziej dotyczące sposobu przypisywania **administratora** rolę do innych użytkowników.

## <a name="change-application-name"></a>Zmienianie nazwy aplikacji

Aby zmienić nazwę aplikacji, użyj menu Nawigacja drugorzędna, aby przejść do **ustawienia aplikacji** strony w **administracji** sekcji.

Na **ustawienia aplikacji** strony, wprowadź nazwę wybranego w **Nazwa aplikacji** pola, a następnie wybierz **Zapisz**.

## <a name="change-the-application-url"></a>Zmień adres URL aplikacji

Aby zmienić adres URL aplikacji, użyj menu Nawigacja drugorzędna można przejść do **ustawienia aplikacji** strony w **administracji** sekcji.

![Strona ustawień aplikacji](media\howto-administer\image0-a.png)

Na **ustawienia aplikacji** strony, wprowadź adres URL wybranego w **adresu URL** pola, a następnie wybierz **Zapisz**. Adres URL może mieć maksymalnie 200 znaków. Jeśli adres URL jest niedostępny, zostanie wyświetlony błąd sprawdzania poprawności

> [!Note]
> Jeśli zmienisz adres URL, stary adres URL może zostać pobrany przez innego klienta usługi Azure IoT Central. W takim przypadku nie jest dostępne do użycia. Po zmianie adresu URL stary adres URL nie działa, i należy powiadomić użytkowników o nowy adres URL do użycia.

## <a name="change-the-application-image"></a>Zmień obraz aplikacji

Aby uzyskać więcej informacji na temat korzystania z obrazów w aplikacji usługi Azure IoT Central, zobacz [przygotowywania i przekazywania obrazów do usługi Azure IoT Central aplikacji](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopiuj aplikację

Można utworzyć kopię dowolnej aplikacji, bez żadnych wystąpień urządzeń, Historia danych z urządzeń i danych użytkowników. Kopia zostanie umieszczona płatnej aplikacji, która zostanie naliczona opłata. Aplikacja w wersji próbnej nie można utworzyć, kopiując innej aplikacji.

Aby skopiować aplikację, przejdź do **ustawienia aplikacji** strony, a następnie kliknij przycisk **kopiowania** przycisku.

![Strona ustawień aplikacji](media\howto-administer\appCopy1.png)

Klikając **kopiowania** przycisku spowoduje otwarcie okna dialogowego, w którym można wybrać nazwę, adres URL, AAD katalogu, subskrypcji i regionu platformy Azure dla nowej aplikacji, która zostanie utworzona przez skopiowanie aplikacji. Wybierz wartości dla każdego z tych pól, a następnie kliknij przycisk **kopiowania** przycisk, aby upewnić się, że chcesz kontynuować. Dowiedz się więcej o tym, co należy wprowadzić w przypadku tych wartości w artykule o [sposób tworzenia aplikacji](howto-create-application.md).

![Strona ustawień aplikacji](media\howto-administer\appCopy2.png)

Po operacji kopiowania aplikacji zakończy się powodzeniem, będzie można przejść do nowej aplikacji, która została utworzona przez skopiowanie aplikację, klikając link, który pojawia się na **ustawienia aplikacji** strony.

![Strona ustawień aplikacji](media\howto-administer\appCopy3.png)

> [!Note]
> Kopiowanie aplikacji skopiuje definicji reguły lub akcji. Jednak ponieważ użytkowników, którzy mają dostęp do pierwotnej aplikacji nie są kopiowane do skopiowanych aplikacji, musisz ręcznie dodawać użytkowników do akcji, takich jak wiadomości e-mail, do którego użytkownicy mają jako warunek wstępny.

## <a name="delete-an-application"></a>Usuwanie aplikacji

Aby usunąć aplikację, użyj menu Nawigacja drugorzędna, aby przejść do **ustawienia aplikacji** strony w **administracji** sekcji.

Wybierz **Usuń**.

> [!Note]
> Usunięcie aplikacji nieodwracalnie usuwa wszystkie dane skojarzone z aplikacją. Aby usunąć aplikację, musi również mieć uprawnień do usunięcia zasobów w subskrypcji platformy Azure została wybrana opcja utworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [Use Role-Based kontroli dostępu w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Role w usłudze Azure IoT Central

Role umożliwiają kontrolowanie, kto w organizacji, można wykonywać różne zadania usługi Azure IoT Central. Usługa Azure IoT Central ma trzy role, które można przypisać do użytkowników aplikacji. Role są przypisywane przez poszczególne aplikacje. Ten sam użytkownik może mieć różne role w różnych aplikacjach. Tego samego użytkownika mogą można przypisać do wielu ról w ramach aplikacji.

### <a name="administrator"></a>Administrator

Użytkownicy w **administratora** rola ma dostęp do wszystkich funkcji w aplikacji usługi Azure IoT Central.

Użytkownik tworzący aplikacji jest automatycznie przypisywana do **administratora** roli. Zawsze musi istnieć co najmniej jeden użytkownik w **administratora** roli.

### <a name="application-builder"></a>Konstruktor aplikacji

Użytkownicy w **Konstruktor aplikacji** roli można zrobić wszystko, co w aplikacji usługi Azure IoT Central, z wyjątkiem administrowania aplikacją.

### <a name="application-operator"></a>Operator aplikacji

Użytkownicy w **operatora aplikacji** rola nie ma dostępu do **Konstruktor aplikacji** strony. Nie można ich administrowania aplikacją.

## <a name="manage-users"></a>Zarządzanie użytkownikami

Administratorzy aplikacji można przypisać użytkowników do ról w aplikacji.

### <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, zanim Zaloguj się i uzyskać dostęp do aplikacji usługi Azure IoT Central. Accounts firmy Microsoft (MSA) i Azure Active Directory (AAD) są obsługiwane w usłudze Azure IoT Central. Grupy usługi Azure Active Directory nie są obecnie obsługiwane w usłudze Azure IoT Central.

Aby dowiedzieć się więcej, zobacz [pomocy konta firmy Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) i [Szybki Start: dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać konto użytkownika z aplikacją usługi Azure IoT Central, użyj menu Nawigacja drugorzędna, aby przejść do **użytkowników** strony w **administracji** sekcji.

    ![Lista użytkowników](media\howto-administer\image1.png)

1. Na **użytkowników** wybierz **+ Dodaj użytkownika** dodawania użytkownika.

    ![Dodaj użytkownika](media\howto-administer\image2.png)

1. Po dodaniu użytkownika do aplikacji usługi Azure IoT Central, wybierz rolę dla użytkownika z **roli** listy rozwijanej. Dowiedz się więcej o rolach w *ról w usłudze Azure IoT Central* dalszej części tego artykułu.

    ![Wybór roli](media\howto-administer\image3.png)

    > [!NOTE]
    >  Aby dodać użytkowników zbiorczo, wpisz nazwy użytkowników dla wszystkich użytkowników, które chcesz dodać, rozdzielając je średnikami. Wybierz rolę z **roli** listy rozwijanej i wybierz polecenie **Zapisz**.

1. Po dodaniu użytkownika, pojawi się wpis dla danego użytkownika na **użytkowników** strony.

    ![Lista użytkowników](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Edytuj role przypisane do użytkowników

Role nie można zmienić raz assinged. Aby zmienić roli przypisanej do użytkownika, Usuń użytkownika i dodać go ponownie przy użyciu innej roli.

### <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, zaznacz przynajmniej jedno pole wyboru na **użytkowników** strony, a następnie wybierz **Usuń**.

## <a name="view-your-bill"></a>Wyświetl rachunek

Aby wyświetlić swój rachunek, przejdź do **rozliczeń** strony w **administracji** sekcji, a następnie wybierz **rozliczeń**. Stronę rozliczeń platformy Azure zostanie otwarty na nowej karcie, a zobaczysz rachunek dla każdej aplikacji w usłudze Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Konwertuj wersję próbną do płatnej aplikacji

Po została oceniona IoT Central, możesz przekonwertować wersję próbną do płatnej aplikacji. Aby ukończyć ten samoobsługowy proces, wykonaj następujące kroki:

1. Użyj menu Nawigacja drugorzędna, aby przejść do **rozliczeń** strony w **administracji** sekcji. Jeśli jeszcze nie przedłużony okres próbny, strona wygląda podobnie do poniższego:

    ![Bezpłatna wersja próbna stanu](media/howto-administer/freetrial.png)

2. Kliknij przycisk **konwersji do wersji płatnej**. Jeśli jeszcze nie przedłużony okres próbny, oknie podręcznym wygląda podobnie do poniższego:

    W oknie podręcznym wybierz odpowiednią dzierżawy usługi Azure Active Directory, a następnie subskrypcji platformy Azure, który chcesz użyć dla aplikacji IoT Central.

    ![Rozszerzanie bezpłatnej wersji próbnej](media/howto-administer/extend.png)

3. Po kliknięciu **przekonwertować**, Twoja wersja próbna jest konwertowana do płatnej aplikacji i rozpoczęciem rozliczanych.

## <a name="extend-your-free-trial"></a>Rozszerzanie Twoja bezpłatna wersja próbna

Domyślnie wszystkie bezpłatne wersje próbne są dostępne przez 7 dni. Jeśli chcesz zwiększyć swoją wersję próbną do 30 dni, należy wykonać następujące kroki:

1. Użyj menu Nawigacja drugorzędna, aby przejść do **rozliczeń** strony w **administracji** sekcji:

1. Kliknij przycisk **Rozszerz wersję próbną**. W oknie podręcznym wybierz odpowiednią dzierżawy usługi Azure Active Directory, a następnie subskrypcji platformy Azure do użytku aplikacji IoT Central:

1. Następnie kliknij przycisk **Rozszerz**. Twój okres próbny teraz jest ważne przez 30 dni.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak zarządzać aplikacją usługi Azure IoT Central, poniżej przedstawiono sugerowany następnego kroku:

> [!div class="nextstepaction"]
> [Konfigurowanie szablonu urządzenia](howto-set-up-template.md)