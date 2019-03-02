---
title: Tworzenie przepływów pracy za pomocą usługi Azure IoT Central łącznik w programie Microsoft Flow | Dokumentacja firmy Microsoft
description: Korzystania z łącznika IoT Central w Microsoft Flow, aby wyzwalać przepływy tworzenie, aktualizowanie i usuwanie urządzeń w przepływach pracy.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: ba6b8c76b31f09eb8f04097f935f865728a4e893
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217499"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Tworzenie przepływów pracy z łącznikiem usługi IoT Central w Microsoft Flow

*Ten temat dotyczy konstruktorów i administratorów.*

Microsoft Flow umożliwia automatyzowanie przepływów pracy między wiele aplikacji i usług, które zależą od użytkowników biznesowych. Za pomocą łącznika usługi IoT Central w Microsoft Flow, może wyzwalać przepływy pracy po wyzwoleniu reguły w IoT Central. W przepływie pracy wyzwalany przez IoT Central lub innych aplikacji można użyć działania w łączniku usługi IoT Central utworzenie urządzenia, aktualizowanie właściwości urządzenia i ustawienia lub usunąć urządzenie. Zapoznaj się z [tych szablonów Microsoft Flow](https://aka.ms/iotcentralflowtemplates) IoT Central połączone z innymi usługami, takimi jak powiadomień na telefon komórkowy i Microsoft Teams.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja płatność za rzeczywiste użycie
- Microsoft osobistym lub służbowym lub konta służbowego do logowania się do usługi Flow ([Dowiedz się więcej na temat planów Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Wyzwalacz przepływu pracy

W tej sekcji dowiesz się, jak wyzwalać powiadomienie na urządzenie przenośne w aplikacji mobilnej Flow, gdy wyzwolenie reguły w IoT Central.

1. Rozpocznij od [tworzenia reguły w IoT Central](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Po zapisaniu warunki reguły kliknij **akcji Microsoft Flow** jako nową akcję. Nowa karta lub okna powinna zostać otwarta w przeglądarce, uwzględniając Microsoft Flow.

    ![Utwórz nową akcję Microsoft Flow](media/howto-add-microsoft-flow-experimental/createflowaction.png)

1. Zaloguj się do Microsoft Flow. Nie musi to być to samo konto, którego używasz w IoT Central. Zostanie wyświetlona się na stronie Przegląd pokazujący łącznika usługi IoT Central nawiązywania połączenia z akcji niestandardowej.

1. Zaloguj się do łącznika usługi IoT Central, a następnie kliknij przycisk **Kontynuuj**. Nastąpi przekierowanie do projektanta Microsoft Flow do utworzenia przepływu pracy. Przepływ pracy ma wyzwalacz IoT Central, który zawiera aplikację i reguła już wypełnione.

1. Wybierz **+ nowy krok** i **Dodaj akcję**. W tym momencie można dodawać żadnych działań do przepływu pracy. Na przykład możemy wysłać powiadomienie na urządzenie przenośne. Wyszukaj **powiadomień**i wybierz polecenie **powiadomienia — Wyślij mi powiadomienie na urządzenie przenośne**.

1. W akcji Wypełnij pole tekstowe o co chcesz zgłoszeniu powiedzieć. Możesz uwzględnić *zawartości dynamicznej* z reguły IoT Central, przekazując wzdłuż ważne informacje, takie jak nazwa urządzenia i sygnatura czasowa do powiadomienia.

    > [!NOTE]
    > Kliknij pozycję "Zobacz więcej" tekst w oknie zawartości dynamicznej miary i wartości właściwości, które spowodowały wyzwolenie reguły.

    ![Przepływ edycji akcji z otwartym okienkiem dynamiczne](./media/howto-add-microsoft-flow-experimental/flowdynamicpane.png)

1. Po zakończeniu edycji akcję, kliknij **Zapisz**. Nastąpi przekierowanie do strony Przegląd Twój przepływ pracy. Tutaj można zobaczyć historię uruchamiania i udostępniać go współpracownikom.

    > [!NOTE]
    > Aby inni użytkownicy w Twojej aplikacji IoT Central, aby edytować tę regułę, należy go udostępnisz je w Microsoft Flow. Dodaj ich konta Microsoft Flow jako właścicieli w przepływie pracy.

1. Po powrocie do aplikacji IoT Central, zobaczysz, że ta reguła zawiera akcję Microsoft Flow, w obszarze akcji.

Zawsze możesz rozpocząć tworzenie przepływu pracy przy użyciu łącznika usługi IoT Central w Microsoft Flow. Następnie możesz wybrać aplikacji, która IoT Central i które zasadę, aby nawiązać połączenie.

## <a name="create-a-device-in-a-workflow"></a>Utwórz urządzenie w przepływie pracy

W tej sekcji dowiesz się, jak utworzyć nowe urządzenie w IoT Central o naciśnięcie przycisku na urządzeniu przenośnym przy użyciu aplikacji mobilnej Microsoft Flow. Tę akcję w usłudze Flow służy do integrowania systemów ERP, takich jak Dynamics z IoT Central, tworząc nowe urządzenie, gdy urządzenie jest dodawany w innym miejscu.

1. Rozpocznij od utworzenia pustego przepływu pracy w Microsoft Flow.

1. Wyszukaj **przycisk przepływu dla urządzeń przenośnych** jako wyzwalacz.

1. W tym wyzwalacza, Dodaj wprowadzanie tekstu o nazwie **nazwy urządzenia**. Zmień tekst opisu, jako **wprowadź nazwę nowego urządzenia**.

1. Dodaj nową akcję. Wyszukaj **Azure IoT Central — Utwórz urządzenie** akcji.

1. Wybierz aplikację, a następnie wybierz szablon urządzenia, aby utworzyć urządzenie z na listach rozwijanych. Zobaczysz akcji rozszerzyć, aby wyświetlić wszystkie właściwości i ustawień urządzenia.

1. Wybierz pole nazwy urządzenia. W okienku zawartości dynamicznej wybierz **nazwy urządzenia**. Ta wartość jest przekazywany z danych wejściowych użytkownika przechodzi przez aplikację mobilną, a to nazwa nowe urządzenie IoT Central. W tym przykładzie jedyne wymagane pole jest nazwa urządzenia, wskazywanym przez czerwona gwiazdka. Inny szablon urządzenie może mieć wiele wymagane pola, które należy wypełnić, aby utworzyć nowe urządzenie.

    ![Przepływ utworzenie okienka dynamicznej akcji urządzenia](./media/howto-add-microsoft-flow-experimental/flowcreatedevice.png)

1. (Opcjonalnie) Wypełnić inne pola, zgodnie z potrzebami dla tworzenia nowych urządzeń.

1. Na koniec Zapisz przepływ pracy.

1. Spróbuj przepływu pracy w aplikacji mobilnej Microsoft Flow. Przejdź do **przyciski** kartę w aplikacji. Powinien zostać wyświetlony przycisku -> Utwórz nowy przepływ pracy urządzenia. Wprowadź nazwę urządzenia i obejrzyj pojawiają się w IoT Central!

    ![Przepływ utworzenie urządzenia przenośne zrzut ekranu](./media/howto-add-microsoft-flow-experimental/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Zaktualizuj urządzenie w przepływie pracy

W tej sekcji dowiesz się, jak zaktualizować ustawień i właściwości w IoT Central o naciśnięcie przycisku na urządzeniu przenośnym przy użyciu aplikacji mobilnej Microsoft Flow.

1. Rozpocznij od utworzenia pustego przepływu pracy w Microsoft Flow.

1. Wyszukaj **przycisk przepływu dla urządzeń przenośnych** jako wyzwalacz.

1. W tym wyzwalacza, Dodaj dane wejściowe np. **lokalizacji** wprowadzania tekstu, który odpowiada na ustawienie lub właściwości, aby zmienić. Zmień tekst opisu.

1. Dodaj nową akcję. Wyszukaj **Azure IoT Central — aktualizacja urządzenia** akcji.

1. Wybierz aplikację z listy rozwijanej. Teraz należy identyfikator istniejącego urządzenia, które chcesz zaktualizować. Możesz uzyskać identyfikator urządzenia IoT Central z **Device Explorer**.

    ![Identyfikator urządzenia w Eksploratorze urządzenia IoT Central](./media/howto-add-microsoft-flow-experimental/iotcdeviceid.png)

1. Można zaktualizować nazwy urządzenia. Do aktualizacji właściwości urządzenia i ustawienia, należy wybrać szablon urządzenia urządzenia, które chcesz zaktualizować w **szablon urządzenia** listy rozwijanej. Kafelek akcji rozwijany, aby wyświetlić wszystkie właściwości i ustawienia mogą być aktualizowane.

    ![Przepływ pracy usługi Flow aktualizacji urządzenia](./media/howto-add-microsoft-flow-experimental/flowupdatedevice.png)

1. Zaznacz wszystkie właściwości i ustawienia, które chcesz zaktualizować. W okienku zawartości dynamicznej wybierz odpowiednie dane wejściowe z wyzwalacza. W tym przykładzie wartość lokalizacji są propagowane w dół do aktualizacji właściwości lokalizacji urządzenia.

1. Na koniec Zapisz przepływ pracy.

1. Spróbuj przepływu pracy w aplikacji mobilnej Microsoft Flow. Przejdź do **przyciski** kartę w aplikacji. Powinien zostać wyświetlony przycisku -> Aktualizacja przepływu pracy urządzenia. Wprowadź dane wejściowe, a urządzenie zostaje zaktualizowana w IoT Central widoczne!

## <a name="delete-a-device-in-a-workflow"></a>Usuwanie urządzenia w przepływie pracy

Możesz usunąć urządzenia za pomocą jego Identyfikatora urządzenia **Azure IoT Central — Usuń urządzenia z systemem** akcji. Oto przykładowy przepływ pracy, który służy do usuwania urządzenia o naciśnięcie przycisku w aplikacji mobilnej Microsoft Flow.

   ![Przepływ pracy urządzenia usuwanie przepływu](./media/howto-add-microsoft-flow-experimental/flowdeletedevice.png)

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

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak tworzyć przepływy pracy za pomocą Microsoft Flow, sugerowane następnym krokiem jest [zarządzania urządzeniami](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

