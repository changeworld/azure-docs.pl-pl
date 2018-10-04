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
ms.openlocfilehash: 1bb0bc0aa7ad6bbbad502832ba8e0a96f36de428
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268310"
---
# <a name="administer-your-iot-central-application"></a>Administrowanie aplikację IOT Central

Po utworzeniu aplikacji Microsoft Azure IoT Central, można użyć **administracji** części interfejsu użytkownika usługi Azure IoT Central, zarządzania nim. Aby przejść do **administracji** zaznacz **administracji** w menu nawigacji po lewej stronie.

**Administracji** sekcja umożliwia:

- Zarządzanie użytkownikami

- Zarządzanie rolami

- Wyświetlanie informacji dotyczących rozliczeń

- Zarządzaj ustawieniami aplikacji

- Oferty bezpłatnej wersji próbnej

W **administracji** sekcji menu Nawigacja drugorzędna zawiera linki do różnych zadań administracyjnych.

Aby uzyskać dostęp do **administracji** sekcji, musisz mieć **administratora** roli dla aplikacji usługi Azure IoT Central. Jeśli tworzysz aplikację usługi Azure IoT Central, masz automatycznie przypisaną **administratora** roli dla tej aplikacji. *Zarządzanie użytkownikami* sekcję w tym artykule opisano bardziej dotyczące sposobu przypisywania **administratora** rolę do innych użytkowników.

## <a name="change-application-name"></a>Zmienianie nazwy aplikacji

Aby zmienić nazwę aplikacji, użyj menu Nawigacja drugorzędna, aby przejść do **ustawienia aplikacji** strony w **administracji** sekcji.

Na **ustawienia aplikacji** strony, wprowadź nazwę wybranego w **Nazwa aplikacji** pola. Następnie wybierz pozycję **Zapisz**.

## <a name="change-the-application-url"></a>Zmień adres URL aplikacji

Aby zmienić adres URL aplikacji, użyj menu Nawigacja drugorzędna można przejść do **ustawienia aplikacji** strony w **administracji** sekcji.

![Strona ustawień aplikacji](media\howto-administer\image0-a.png)

Na **ustawienia aplikacji** strony, wprowadź adres URL wybranego w **adresu URL** , a następnie wybierz opcję **Zapisz**. Adres URL może mieć maksymalnie 200 znaków. Jeśli adres URL nie jest dostępna, zostanie wyświetlony błąd sprawdzania poprawności.

> [!Note]
> Jeśli zmienisz adres URL, stary adres URL może zostać pobrany przez innego klienta usługi Azure IoT Central. Jeśli tak się stanie, nie jest już dostępne do użycia. Po zmianie adresu URL stary adres URL nie będzie działać, a następnie należy powiadomić użytkowników o nowy adres URL do użycia.

## <a name="change-the-application-image"></a>Zmień obraz aplikacji

Aby uzyskać więcej informacji na temat korzystania z obrazów w aplikacji usługi Azure IoT Central, zobacz [przygotowywania i przekazywania obrazów do usługi Azure IoT Central aplikacji](howto-prepare-images.md).

## <a name="copy-an-application"></a>Kopiuj aplikację

Można utworzyć kopię dowolnej aplikacji, bez żadnych wystąpień urządzeń, Historia danych z urządzeń i danych użytkowników. Kopia zostanie umieszczona płatnej aplikacji, które są naliczane opłaty za. Aplikacja w wersji próbnej nie można utworzyć, kopiując innej aplikacji.

Aby skopiować aplikację, przejdź do **ustawienia aplikacji** strony. Następnie wybierz pozycję **kopiowania** przycisku.

![Strona ustawień aplikacji](media\howto-administer\appCopy1.png)

Wybieranie **kopiowania** przycisku powoduje otwarcie okna dialogowego, w którym można wybrać nazwę, adres URL, usługi Azure AD directory, subskrypcji i region platformy Azure dla nowej aplikacji, która zostanie utworzona przez skopiowanie aplikacji. Wybierz wartości dla każdego z tych pól. Następnie wybierz pozycję **kopiowania** przycisk, aby upewnić się, że chcesz kontynuować. Dowiedz się więcej o tym, co należy wprowadzić w przypadku tych wartości w tym artykule o [sposób tworzenia aplikacji](howto-create-application.md).

![Strona ustawień aplikacji](media\howto-administer\appCopy2.png)

Po pomyślnym operacji kopiowania aplikacji, możesz przejść do nowej aplikacji, która została utworzona przez skopiowanie aplikacji. Aby przejść do aplikacji, wybierz link, który pojawia się na **ustawienia aplikacji** strony.

![Strona ustawień aplikacji](media\howto-administer\appCopy3.png)

> [!Note]
> Kopiowanie aplikacji kopiuje również definicji reguły lub akcji. Jednak ponieważ użytkowników, którzy mają dostęp do pierwotnej aplikacji nie są kopiowane do skopiowanych aplikacji, musisz ręcznie dodawać użytkowników do akcji, takich jak wiadomości e-mail, do którego użytkownicy mają jako warunek wstępny.

## <a name="delete-an-application"></a>Usuwanie aplikacji

Aby usunąć aplikację, użyj menu Nawigacja drugorzędna, aby przejść do **ustawienia aplikacji** strony w **administracji** sekcji.

Wybierz **Usuń**.

> [!Note]
> Trwałe usunięcie aplikacji spowoduje usunięcie wszystkich danych, które jest skojarzone z tej aplikacji.  Aby usunąć aplikację, również musi mieć uprawnienia do usuwania zasobów w subskrypcji platformy Azure została wybrana opcja utworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Role w usłudze Azure IoT Central

Role umożliwiają kontrolowanie, kto w organizacji można wykonywać różne zadania usługi Azure IoT Central. Usługa Azure IoT Central ma trzy role, które można przypisać do użytkowników aplikacji. Role są przypisywane przez poszczególne aplikacje. Ten sam użytkownik może mieć różne role w różnych aplikacjach. Tego samego użytkownika można przypisać do wielu ról w ramach aplikacji.

### <a name="administrator"></a>Administrator

Użytkownicy w **administratora** rola ma dostęp do wszystkich funkcji w aplikacji usługi Azure IoT Central.

Użytkownik, który tworzy aplikację, jest automatycznie przypisywana do **administratora** roli. Zawsze musi istnieć co najmniej jeden użytkownik w **administratora** roli.

### <a name="application-builder"></a>Konstruktor aplikacji

Użytkownicy w **Konstruktor aplikacji** roli można zrobić wszystko, co w aplikacji usługi Azure IoT Central, z wyjątkiem administrowania aplikacją.

### <a name="application-operator"></a>Operator aplikacji

Użytkownicy w **operatora aplikacji** rola nie ma dostępu do **Konstruktor aplikacji** strony. Nie można ich administrowania aplikacją.

## <a name="manage-users"></a>Zarządzanie użytkownikami

Administratorzy aplikacji można przypisać użytkowników do ról w aplikacji.

### <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, zanim Zaloguj się i uzyskać dostęp do aplikacji usługi Azure IoT Central. Accounts Microsoft (MSA) i konta usługi Azure Active Directory (Azure AD) są obsługiwane w usłudze Azure IoT Central. Grupy usługi Azure Active Directory nie są obecnie obsługiwane w usłudze Azure IoT Central.

Aby uzyskać więcej informacji, zobacz [pomocy konta firmy Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) i [Szybki Start: dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać konto użytkownika z aplikacją usługi Azure IoT Central, użyj menu Nawigacja drugorzędna, aby przejść do **użytkowników** strony w **administracji** sekcji.

    ![Lista użytkowników](media\howto-administer\image1.png)

1. Aby dodać użytkownika, na **użytkowników** wybierz **+ Dodaj użytkownika**.

    ![Dodawanie użytkownika](media\howto-administer\image2.png)

1. Wybierz rolę dla użytkownika z **roli** menu rozwijanego. Dowiedz się więcej o rolach w *ról w usłudze Azure IoT Central* dalszej części tego artykułu.

    ![Wybór roli](media\howto-administer\image3.png)

    > [!NOTE]
    >  Aby dodać użytkowników zbiorczo, wprowadź identyfikatory wszystkich użytkowników, które chcesz dodać użytkowników, rozdzielając je średnikami. Wybierz rolę z **roli** menu rozwijanego. Następnie wybierz pozycję **Zapisz**.

1. Po dodaniu użytkownika, pojawi się wpis dla danego użytkownika na **użytkowników** strony.

    ![Lista użytkowników](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Edytuj role, które są przypisane do użytkowników

Role nie można zmienić po przypisaniu. Zmiana roli, która jest przypisana do użytkownika, Usuń użytkownika, a następnie dodać go ponownie przy użyciu innej roli.

### <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, wybierz jeden lub więcej pól wyboru na **użytkowników** strony. Następnie wybierz pozycję **Usuń**.

## <a name="view-your-bill"></a>Wyświetl rachunek

Aby wyświetlić swój rachunek, przejdź do **rozliczeń** strony w **administracji** sekcji. Następnie wybierz pozycję **rozliczeń**. Stronę rozliczeń platformy Azure zostanie otwarty na nowej karcie, w którym można zobaczyć rachunek dla każdej aplikacji w usłudze Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Konwertuj wersję próbną do płatnej aplikacji

Po została oceniona IoT Central, możesz przekonwertować wersję próbną do płatnej aplikacji. Aby ukończyć ten samoobsługowy proces, wykonaj następujące kroki:

1. Użyj menu Nawigacja drugorzędna, aby przejść do **rozliczeń** strony w **administracji** sekcji. Jeśli jeszcze nie przedłużony okres próbny, strona wygląda jak poniższy zrzut ekranu:

    ![Bezpłatna wersja próbna stanu](media/howto-administer/freetrial.png)

2. Wybierz **konwersji do wersji płatnej**. Jeśli jeszcze nie przedłużony okres próbny, okno podręczne wygląda jak poniższy zrzut ekranu:

    ![Rozszerzanie bezpłatnej wersji próbnej](media/howto-administer/extend.png)

3. W oknie podręcznym wybierz odpowiednią dzierżawy usługi Azure Active Directory, a następnie subskrypcji platformy Azure na potrzeby aplikacji IoT Central.

3. Po wybraniu **przekonwertować**, Twojej wersji próbnej przekonwertowano do płatnej aplikacji i możesz rozpocząć rozliczanych.

## <a name="extend-your-free-trial"></a>Rozszerzanie Twoja bezpłatna wersja próbna

Domyślnie wszystkie bezpłatne wersje próbne są dostępne przez siedem dni. Jeśli chcesz zwiększyć swoją wersję próbną do 30 dni, wykonaj następujące kroki:

1. Użyj menu Nawigacja drugorzędna, aby przejść do **rozliczeń** strony w **administracji** sekcji.

1. Wybierz **Rozszerz wersję próbną**. W oknie podręcznym wybierz odpowiednie dzierżawy usługi Azure Active Directory, a następnie subskrypcji platformy Azure do użytku aplikacji IoT Central.

1. Następnie wybierz pozycję **Rozszerz**. Twój okres próbny teraz jest ważne przez 30 dni.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Zestawy SDK platformy Azure na użytek operacje warstwy kontroli

Pakiety zestawu SDK IoT Central usługi Azure Resource Manager są dostępne dla węzła, Python, C#, Ruby, Java i z rzeczywistym użyciem. Te biblioteki obsługi operacje warstwy kontroli IoT Central, które umożliwiają tworzenie, listy, aktualizowanie lub usuwanie aplikacji IoT Central. Zapewniają także pomocnicy do radzenia sobie z uwierzytelnianiem i dodanymi komentarzami, które są specyficzne dla każdego języka. 

Można znaleźć przykłady dotyczące używania zestawów SDK usługi Azure Resource Manager w [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Aby dowiedzieć się więcej, zapoznaj się tych pakietów w witrynie GitHub.

| Język | Repozytorium | Pakiet |
| ---------| ---------- | ------- |
| Węzeł | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Przejdź | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak zarządzać aplikacją usługi Azure IoT Central, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Konfigurowanie szablonu urządzenia](howto-set-up-template.md)
