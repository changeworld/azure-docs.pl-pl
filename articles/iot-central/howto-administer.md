---
title: Administrowanie aplikacją usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako administrator jak zarządzać aplikacją usługi Azure IoT Central
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 87ed31836fcda922b085ec951eb6d9d14542db6a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467557"
---
# <a name="administer-your-iot-central-application"></a>Zarządzać aplikacją IoT Central

Po utworzeniu aplikacji IoT Central, możesz przejść do **administracji** sekcji:

- Zarządzaj ustawieniami aplikacji
- Zarządzaj użytkownikami
- Zarządzanie rolami
- Wyświetl rachunek
- Konwertuj wersję próbną na płatność za rzeczywiste użycie
- Eksportuj dane
- Zarządzanie połączenie z urządzeniem
- Używanie tokenów dostępu dla narzędzi dla deweloperów
- Dostosowywanie interfejsu użytkownika aplikacji
- Dostosuj łącza pomocy w aplikacji
- Programowe zarządzanie IoT Central

Aby uzyskać dostęp do **administracji** sekcji, musisz mieć **administratora** roli dla aplikacji usługi Azure IoT Central. Jeśli tworzysz aplikację usługi Azure IoT Central, masz automatycznie przypisaną **administratora** roli dla tej aplikacji. [Zarządzanie użytkownikami](#manage-users) sekcję w tym artykule opisano bardziej dotyczące sposobu przypisywania **administratora** rolę do innych użytkowników.

## <a name="manage-application-settings"></a>Zarządzaj ustawieniami aplikacji

### <a name="change-application-name-and-url"></a>Zmienianie nazwy aplikacji i adres URL
W **ustawienia aplikacji** strony, można zmienić nazwy i adresu URL aplikacji, a następnie wybierz **Zapisz**.

![Strona ustawień aplikacji](media/howto-administer/image0-a.png)

Jeśli administrator tworzy niestandardowy motyw aplikacji, ta strona zawiera opcję, aby ukryć **Nazwa aplikacji** w interfejsie użytkownika. Jest to przydatne, jeśli logo aplikacji w niestandardowy motyw zawiera nazwę aplikacji. Aby uzyskać więcej informacji, zobacz [dostosować usługi Azure IoT Central interfejsu użytkownika](./howto-customize-ui.md).

> [!Note]
> Jeśli zmienisz adres URL, stary adres URL może zostać pobrany przez innego klienta usługi Azure IoT Central. Jeśli tak się stanie, nie jest już dostępne do użycia. Po zmianie adresu URL stary adres URL nie będzie działać, a następnie należy powiadomić użytkowników o nowy adres URL do użycia.

### <a name="prepare-and-upload-image"></a>Przygotowywanie i przekazywanie obrazu
Aby zmienić obraz aplikacji, zobacz [przygotowywania i przekazywania obrazów do usługi Azure IoT Central aplikacji](howto-prepare-images.md).

### <a name="copy-an-application"></a>Kopiuj aplikację
Można utworzyć kopię dowolnej aplikacji, bez żadnych wystąpień urządzeń, Historia danych z urządzeń i danych użytkowników. Kopia jest aplikacją płatność za rzeczywiste użycie, która zostanie naliczona opłata. Nie można utworzyć aplikacji wersji próbnej w ten sposób.

Wybierz **kopiowania**. W oknie dialogowym wprowadź szczegóły nowej aplikacji płatność za rzeczywiste użycie. Następnie wybierz pozycję **kopiowania** aby upewnić się, że chcesz kontynuować. Dowiedz się więcej na temat pól, w tym formularzu w [tworzenia aplikacji](quick-deploy-iot-central.md) Szybki Start.

![Strona ustawień aplikacji](media/howto-administer/appcopy2.png)

Po pomyślnym operacji kopiowania aplikacji, możesz przejść do nowej aplikacji, korzystając z linku.

![Strona ustawień aplikacji](media/howto-administer/appcopy3a.png)

> [!Note]
> Kopiowanie aplikacji kopiuje również definicji reguły i akcje. Jednak ponieważ użytkowników, którzy mają dostęp do pierwotnej aplikacji nie są kopiowane do skopiowanych aplikacji, musisz ręcznie dodawać użytkowników do akcji, takich jak wiadomości e-mail, do którego użytkownicy mają warunek wstępny. Ogólnie rzecz biorąc jest dobry pomysł, aby sprawdzić reguły i akcje, aby upewnić się, że są one na bieżąco w nowej aplikacji.

### <a name="delete-an-application"></a>Usuwanie aplikacji

> [!Note]
> Aby usunąć aplikację, również musi mieć uprawnienia do usuwania zasobów w subskrypcji platformy Azure została wybrana opcja utworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [zarządzanie dostępem do zasobów subskrypcji platformy Azure za pomocą kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Użyj **Usuń** przycisk, aby trwale usunąć aplikację IoT Central. Ta akcja trwale usuwa wszystkie dane, które ma skojarzone z aplikacją.

## <a name="manage-users"></a>Zarządzaj użytkownikami

### <a name="add-users"></a>Dodawanie użytkowników

Każdy użytkownik musi mieć konto użytkownika, zanim Zaloguj się i uzyskać dostęp do aplikacji usługi Azure IoT Central. Accounts Microsoft (MSA) i konta usługi Azure Active Directory (Azure AD) są obsługiwane w usłudze Azure IoT Central. Grupy usługi Azure Active Directory nie są obecnie obsługiwane w usłudze Azure IoT Central.

Aby uzyskać więcej informacji, zobacz [pomocy konta firmy Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) i [Szybki Start: Dodawanie nowych użytkowników do usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Aby dodać użytkownika do aplikacji IoT Central, przejdź do **użytkowników** strony w **administracji** sekcji.

    ![Lista użytkowników](media/howto-administer/image1.png)

1. Aby dodać użytkownika, na **użytkowników** wybierz **+ Dodaj użytkownika**.

1. Wybierz rolę dla użytkownika z **roli** menu rozwijanego. Dowiedz się więcej o rolach w [Zarządzanie rolami](#manage-roles) dalszej części tego artykułu.

    ![Wybór roli](media/howto-administer/image3.png)

    > [!NOTE]
    >  Aby dodać użytkowników zbiorczo, wprowadź identyfikatory wszystkich użytkowników, które chcesz dodać użytkowników, rozdzielając je średnikami. Wybierz rolę z **roli** menu rozwijanego. Następnie wybierz pozycję **Zapisz**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Edytuj role, które są przypisane do użytkowników

Role nie można zmienić po przypisaniu. Zmiana roli, która jest przypisana do użytkownika, Usuń użytkownika, a następnie dodać go ponownie przy użyciu innej roli.

### <a name="delete-users"></a>Usuwanie użytkowników

Aby usunąć użytkowników, wybierz jeden lub więcej pól wyboru na **użytkowników** strony. Następnie wybierz pozycję **Usuń**.

## <a name="manage-roles"></a>Zarządzanie rolami

Role umożliwiają kontrolowanie, kto w organizacji można wykonywać różne zadania w IoT Central. Istnieją trzy role, które można przypisać do użytkowników aplikacji.

### <a name="administrator"></a>Administrator

Użytkownicy w **administratora** rola ma dostęp do wszystkich funkcji w aplikacji.

Użytkownik, który tworzy aplikację, jest automatycznie przypisywana do **administratora** roli. Zawsze musi istnieć co najmniej jeden użytkownik w **administratora** roli.

### <a name="application-builder"></a>Konstruktor aplikacji

Użytkownicy w **Konstruktor aplikacji** roli można zrobić wszystko, co w aplikacji, z wyjątkiem administrowania aplikacją. Konstruktorzy można tworzyć, edytować, usunąć urządzenia szablonów i urządzeń, Zarządzaj zbiorami urządzeń i uruchamiać zadania i analizy. Konstruktory nie mają dostępu do **administracji** części aplikacji.

### <a name="application-operator"></a>Operator aplikacji

Użytkownicy w **operatora aplikacji** roli nie może wprowadzać zmian do szablonów urządzeń i nie można administrować aplikacji. Operatory można dodać i usunąć urządzenia, Zarządzaj zbiorami urządzeń i uruchamiać zadania i analizy. Operatory nie mają dostępu do **Konstruktor aplikacji** i **administracji** stron.

## <a name="view-your-bill"></a>Wyświetl rachunek

Aby wyświetlić swój rachunek, przejdź do **rozliczeń** strony w **administracji** sekcji. Stronę rozliczeń platformy Azure zostanie otwarty na nowej karcie, w którym można zobaczyć rachunek dla każdej aplikacji w usłudze Azure IoT Central.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Konwertuj wersję próbną na płatność za rzeczywiste użycie

Możesz również przekonwertować Twoja wersja próbna aplikacji na aplikację płatność za rzeczywiste użycie. Poniżej przedstawiono różnice między tymi typami aplikacji.

- **Wersja próbna** aplikacje są bezpłatne przez siedem dni, zanim wygasną. W dowolnym momencie przed wygaśnięciem można przekonwertować te aplikacje na typ Płatność zgodnie z rzeczywistym użyciem.
- **Płatność za rzeczywiste użycie** aplikacje są naliczane zgodnie z urządzenia z urządzeniami pierwszych pięć bezpłatnych.

Więcej informacji o cenach znajdziesz na [stronie cennika usługi Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Aby ukończyć ten samoobsługowy proces, wykonaj następujące kroki:

1. Przejdź do **rozliczeń** strony w **administracji** sekcji.

    ![Stan wersji próbnej](media/howto-administer/freetrialbilling.png)

1. Wybierz **przekonwertować płatność za rzeczywiste użycie**.

    ![Konwertuj na wersję próbną](media/howto-administer/convert.png)

1. Wybierz odpowiednie usługi Azure Active Directory, a następnie subskrypcji platformy Azure na potrzeby aplikacji płatność za rzeczywiste użycie.

1. Po wybraniu **przekonwertować**, Twoja aplikacja jest teraz aplikacją płatność za rzeczywiste użycie i rozpoczęciem rozliczanych.

## <a name="export-data"></a>Eksportuj dane

Aby umożliwić **ciągły Eksport danych** do wyeksportowania pomiarów, urządzeń i danych szablonów urządzenie do swojego konta magazynu obiektów Blob platformy Azure. Dowiedz się więcej na temat [eksportowania danych](howto-export-data.md).

## <a name="manage-device-connection"></a>Zarządzanie połączenie z urządzeniem

Łączenie urządzeń na dużą skalę w Twojej aplikacji, w tym miejscu przy użyciu kluczy i certyfikatów. Dowiedz się więcej o [podłączania urządzeń](concepts-connectivity.md).

## <a name="use-access-tokens"></a>Używanie tokenów dostępu

Generowanie tokenów dostępu z nich korzystać w narzędzia dla deweloperów. Obecnie narzędzie tylko dla deweloperów dostępne jest Eksplorator IoT Central do monitorowania komunikatów przesyłanych z urządzenia i zmiany ustawień i właściwości. Dowiedz się więcej o [explorer IoT Central](howto-use-iotc-explorer.md).

## <a name="customize-your-application"></a>Dostosowywanie aplikacji

Aby uzyskać więcej informacji o zmienianiu kolorów i ikon w aplikacji, zobacz [dostosować usługi Azure IoT Central interfejsu użytkownika](./howto-customize-ui.md).

## <a name="customize-help"></a>Dostosowywanie Pomocy

Aby uzyskać więcej informacji na temat dodawania łączy niestandardowych pomocy w aplikacji, zobacz [dostosować usługi Azure IoT Central interfejsu użytkownika](./howto-customize-ui.md).

## <a name="manage-programatically"></a>Zarządzaj programowo

Pakiety zestawu SDK IoT Central usługi Azure Resource Manager są dostępne dla węzła, Python, C#, Ruby, Java i z rzeczywistym użyciem. Te pakiety można użyć do utworzenia, listy, aktualizacji lub usunięcia aplikacji IoT Central. Pakiety zawierają pomocników, aby zarządzać uwierzytelnianiem i obsługą błędów.

Można znaleźć przykłady dotyczące używania zestawów SDK usługi Azure Resource Manager w [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Aby dowiedzieć się więcej, zobacz następujące repozytoria GitHub i pakietów:

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
