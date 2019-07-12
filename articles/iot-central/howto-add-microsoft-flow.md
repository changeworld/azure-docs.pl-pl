---
title: Tworzenie przepływów pracy za pomocą usługi Azure IoT Central łącznik w programie Microsoft Flow | Dokumentacja firmy Microsoft
description: Korzystania z łącznika IoT Central w Microsoft Flow, aby wyzwalać przepływy i tworzenie, pobieranie, aktualizowanie, usuwanie urządzeń i poleceń w przepływach pracy.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: e8bc8b8d4e3585ea4c0505f2e36abc6d1da7f8eb
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797719"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Tworzenie przepływów pracy z łącznikiem usługi IoT Central w Microsoft Flow

*Ten temat dotyczy konstruktorów i administratorów.*

Microsoft Flow umożliwia automatyzowanie przepływów pracy między wiele aplikacji i usług, które zależą od użytkowników biznesowych. Za pomocą łącznika usługi IoT Central w Microsoft Flow, może wyzwalać przepływy pracy po wyzwoleniu reguły w IoT Central. W przepływie pracy wyzwalany przez IoT Central lub innych aplikacji można użyć działania w łączniku usługi IoT Central, aby:
- Tworzenie urządzenia
- Pobierz informacje o urządzeniu
- Aktualizowanie właściwości urządzenia i ustawienia
- Uruchom polecenie na urządzeniu
- Usuwanie urządzenia

Zapoznaj się z [tych szablonów Microsoft Flow](https://aka.ms/iotcentralflowtemplates) IoT Central połączone z innymi usługami, takimi jak powiadomień na telefon komórkowy i Microsoft Teams.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja płatność za rzeczywiste użycie
- Microsoft osobistym lub służbowym lub konta służbowego do użycia, Microsoft Flow ([Dowiedz się więcej na temat planów Microsoft Flow](https://aka.ms/microsoftflowplans))
- Konto służbowe lub szkolne w celu korzystania z łącznika usługi Azure IoT Central

## <a name="trigger-a-workflow"></a>Wyzwalacz przepływu pracy

W tej sekcji dowiesz się, jak wyzwalać powiadomienie na urządzenie przenośne w aplikacji mobilnej Flow, gdy wyzwolenie reguły w IoT Central. Ta całego przepływu pracy można tworzyć w ramach aplikacji IoT Central przy użyciu osadzonego projektanta Microsoft Flow.

1. Rozpocznij od [tworzenia reguły w IoT Central](howto-create-telemetry-rules.md). Po zapisaniu warunki reguły wybierz **akcji Microsoft Flow** jako nową akcję. Okno dialogowe zostanie otwarty do konfiguracji przepływu pracy. Konto użytkownika IoT Central, które są podpisane w będzie służyć do logowania się do Microsoft Flow.

    ![Utwórz nową akcję Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Zostanie wyświetlona lista przepływów pracy, które mają dostęp do a są dołączone do tej reguły IoT Central. Kliknij przycisk **Eksploruj szablony** lub **nowy > Utwórz z szablonu** i można wybrać jedną z dostępnych szablonów. 

    ![Dostępne szablony Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Zostanie wyświetlony monit logować się do łączników w wybranym szablonie. 

    > [!NOTE]
    > Do korzystania z łącznika usługi Azure IoT Central, musisz zarejestrować się przy użyciu konta usługi Azure Active Directory (konto służbowe). To konto osobiste, takie jak abc@outlook.com lub abc@live.com nie są obsługiwane przez łącznik usługi Azure IoT Central.

    Po zalogowaniu się do łączników, nastąpi przejście do projektanta w celu utworzenia przepływu pracy. Przepływ pracy ma wyzwalacz IoT Central, który zawiera aplikację i reguła już wypełnione.

1. Dostosowywanie informacje przekazane do tej akcji i dodawania nowych akcji można dostosować przepływ pracy. W tym przykładzie to akcja **powiadomienia — Wyślij mi powiadomienie na urządzenie przenośne**. Możesz uwzględnić *zawartości dynamicznej* z reguły IoT Central, przekazując wzdłuż ważne informacje, takie jak nazwa urządzenia i sygnatura czasowa do powiadomienia.

    > [!NOTE]
    > Wybierz **Zobacz więcej** tekst w oknie zawartości dynamicznej można pobrać miary i wartości właściwości, które wyzwolona reguła.

    ![Przepływ edycji akcji z otwartym okienkiem dynamiczne](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Po zakończeniu edycji akcję, wybierz **Zapisz**. Nastąpi przekierowanie do strony Przegląd Twój przepływ pracy. Tutaj można zobaczyć historię uruchamiania i udostępniać go współpracownikom.

    > [!NOTE]
    > Aby inni użytkownicy w Twojej aplikacji IoT Central, aby edytować tę regułę, należy go udostępnisz je w Microsoft Flow. Dodaj ich konta Microsoft Flow jako właścicieli w przepływie pracy.

1. Po powrocie do aplikacji IoT Central, zobaczysz, że ta reguła zawiera akcję Microsoft Flow, w obszarze akcji.

Można również tworzyć przepływy pracy, przy użyciu łącznika usługi IoT Central bezpośrednio z poziomu Microsoft Flow. Następnie możesz która aplikacja IoT Central, aby nawiązać połączenie.

## <a name="create-a-device-in-a-workflow"></a>Utwórz urządzenie w przepływie pracy

W tej sekcji dowiesz się, jak utworzyć nowe urządzenie w IoT Central o naciśnięcie przycisku na urządzeniu przenośnym przy użyciu aplikacji mobilnej Microsoft Flow. Tę akcję w usłudze Flow służy do integrowania systemów ERP, takich jak Dynamics z IoT Central, tworząc nowe urządzenie, gdy urządzenie jest dodawany w innym miejscu.

1. Rozpocznij od utworzenia pustego przepływu pracy w Microsoft Flow.

1. Wyszukaj **przycisk przepływu dla urządzeń przenośnych** jako wyzwalacz.

1. W tym wyzwalacza, Dodaj wprowadzanie tekstu o nazwie **nazwy urządzenia**. Zmień tekst opisu, jako **wprowadź nazwę nowego urządzenia**.

1. Dodaj nową akcję. Wyszukaj **Azure IoT Central — Utwórz urządzenie** akcji.

1. Wybierz aplikację, a następnie wybierz szablon urządzenia, aby utworzyć urządzenie z na listach rozwijanych. Zobaczysz akcji rozszerzyć, aby wyświetlić wszystkie właściwości i ustawień urządzenia.

1. Wybierz pole nazwy urządzenia. W okienku zawartości dynamicznej wybierz **nazwy urządzenia**. Ta wartość jest przekazywany z danych wejściowych użytkownika przechodzi przez aplikację mobilną, a to nazwa nowe urządzenie IoT Central. W tym przykładzie jedyne wymagane pole jest nazwa urządzenia, wskazywanym przez czerwona gwiazdka. Inny szablon urządzenie może mieć wiele wymagane pola, które należy wypełnić, aby utworzyć nowe urządzenie.

    ![Przepływ utworzenie okienka dynamicznej akcji urządzenia](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Opcjonalnie) Wypełnić inne pola, zgodnie z potrzebami dla tworzenia nowych urządzeń.

1. Na koniec Zapisz przepływ pracy.

1. Spróbuj przepływu pracy w aplikacji mobilnej Microsoft Flow. Przejdź do **przyciski** kartę w aplikacji. Powinien zostać wyświetlony przycisku -> Utwórz nowy przepływ pracy urządzenia. Wprowadź nazwę urządzenia i obejrzyj pojawiają się w IoT Central!

    ![Przepływ utworzenie urządzenia przenośne zrzut ekranu](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Zaktualizuj urządzenie w przepływie pracy

W tej sekcji dowiesz się, jak zaktualizować ustawień i właściwości w IoT Central o naciśnięcie przycisku na urządzeniu przenośnym przy użyciu aplikacji mobilnej Microsoft Flow.

1. Rozpocznij od utworzenia pustego przepływu pracy w Microsoft Flow.

1. Wyszukaj **przycisk przepływu dla urządzeń przenośnych** jako wyzwalacz.

1. W tym wyzwalacza, Dodaj dane wejściowe np. **lokalizacji** wprowadzania tekstu, który odpowiada na ustawienie lub właściwości, aby zmienić. Zmień tekst opisu.

1. Dodaj nową akcję. Wyszukaj **Azure IoT Central — aktualizacja urządzenia** akcji.

1. Wybierz aplikację z listy rozwijanej. Teraz należy identyfikator istniejącego urządzenia, które chcesz zaktualizować. 

    > [!NOTE] 
    > **Możesz korzystać z Identyfikatorem znalezionym w adresie URL** na stronie szczegółów urządzenia z urządzenia, które chcesz zaktualizować. Identyfikator urządzenia w programie device explorer listę urządzeń nie jest właściwy do użycia w Microsoft Flow.

    ![Identyfikator IoT Central z adresu URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Można zaktualizować nazwy urządzenia. Do aktualizacji właściwości urządzenia i ustawienia, należy wybrać szablon urządzenia urządzenia, które chcesz zaktualizować w **szablon urządzenia** listy rozwijanej. Kafelek akcji rozwijany, aby wyświetlić wszystkie właściwości i ustawienia mogą być aktualizowane.

    ![Przepływ pracy usługi Flow aktualizacji urządzenia](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Zaznacz wszystkie właściwości i ustawienia, które chcesz zaktualizować. W okienku zawartości dynamicznej wybierz odpowiednie dane wejściowe z wyzwalacza. W tym przykładzie wartość lokalizacji są propagowane w dół do aktualizacji właściwości lokalizacji urządzenia.

1. Na koniec Zapisz przepływ pracy.

1. Spróbuj przepływu pracy w aplikacji mobilnej Microsoft Flow. Przejdź do **przyciski** kartę w aplikacji. Powinien zostać wyświetlony przycisku -> Aktualizacja przepływu pracy urządzenia. Wprowadź dane wejściowe, a urządzenie zostaje zaktualizowana w IoT Central widoczne!

## <a name="get-device-information-in-a-workflow"></a>Pobierz informacje o urządzeniu w przepływie pracy

Możesz uzyskać informacje o urządzeniu za pomocą jego Identyfikatora **Azure IoT Central — Pobierz urządzenie** akcji. 
> [!NOTE] 
> **Możesz korzystać z Identyfikatorem znalezionym w adresie URL** na stronie szczegółów urządzenia z urządzenia, które chcesz zaktualizować. Identyfikator urządzenia w programie device explorer listę urządzeń nie jest właściwy do użycia w Microsoft Flow.

Aby uzyskać informacje, takie jak nazwa urządzenia, nazwę szablonu w urządzeniu, wartości właściwości i wartości ustawień do przekazania do kolejnych akcjach w przepływie pracy. Oto przykładowy przepływ pracy, który przekazuje wartość właściwości nazwy klienta z urządzenia do Microsoft Teams.

   ![Przepływ pracy usługi Flow get urządzenia](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Uruchom polecenie na urządzeniu w przepływie pracy
Można uruchomić polecenie na urządzeniu, określony przez jego identyfikator za pomocą **usługi Azure IoT Central — Uruchom polecenie** akcji. 

> [!NOTE] 
> **Możesz korzystać z Identyfikatorem znalezionym w adresie URL** na stronie szczegółów urządzenia z urządzenia, które chcesz zaktualizować. Identyfikator urządzenia w programie device explorer listę urządzeń nie jest właściwy do użycia w Microsoft Flow.
    
Możesz wybrać polecenie Uruchom i przekaż parametry polecenia za pomocą tej akcji. Oto przykładowy przepływ pracy, który uruchamia polecenie ponownego uruchomienia urządzenia przy użyciu przycisku w aplikacji mobilnej Microsoft Flow.

   ![Przepływ pracy usługi Flow get urządzenia](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Usuwanie urządzenia w przepływie pracy

Możesz usunąć urządzenia za pomocą jego Identyfikatora **Azure IoT Central — Usuń urządzenia z systemem** akcji. 
> [!NOTE] 
> **Możesz korzystać z Identyfikatorem znalezionym w adresie URL** na stronie szczegółów urządzenia z urządzenia, które chcesz zaktualizować. Identyfikator urządzenia w programie device explorer listę urządzeń nie jest właściwy do użycia w Microsoft Flow.

Oto przykładowy przepływ pracy, który służy do usuwania urządzenia o naciśnięcie przycisku w aplikacji mobilnej Microsoft Flow.

   ![Przepływ pracy urządzenia usuwanie przepływu](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy podczas tworzenia połączenia z łącznikiem usługi Azure IoT Central, Oto kilka porad ułatwiających.

1. Osobistych kont Microsoft (takie jak @hotmail.com, @live.com, @outlook.com domen) nie są obsługiwane w tej chwili. Musisz użyć pracy usługi Azure Active Directory (AD) lub konta służbowego.

2. Aby użyć łącznika usługi IoT Central w Microsoft Flow, musisz mieć zarejestrowane do co najmniej raz aplikacji IoT Central. W przeciwnym razie aplikacja nie będzie wyświetlane na listach rozwijanych aplikacji.

3. Jeśli podczas korzystania z konta usługi Azure AD jest komunikat o błędzie, spróbuj otworzyć programu Windows PowerShell i uruchom następujące polecenia cmdlet jako administrator.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz, jak tworzyć przepływy pracy za pomocą Microsoft Flow, sugerowane następnym krokiem jest [zarządzania urządzeniami](howto-manage-devices.md).

