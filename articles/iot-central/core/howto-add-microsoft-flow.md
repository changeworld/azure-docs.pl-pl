---
title: Twórz przepływy pracy za pomocą łącznika usługi Azure IoT Central w Microsoft Flow | Microsoft Docs
description: Korzystając z IoT Central łącznika w Microsoft Flow, można wyzwalać przepływy pracy oraz tworzyć, aktualizować, usuwać i uruchamiać polecenia w przepływach pracy.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 258020c8ccd690c524460873387293e73fc89125
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951733"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Tworzenie przepływów pracy za pomocą łącznika IoT Central w programie Microsoft Flow

*Ten temat dotyczy konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Użyj Microsoft Flow, aby zautomatyzować przepływy pracy dla wielu aplikacji i usług, z których korzystają użytkownicy biznesowi. Korzystając z łącznika IoT Central w Microsoft Flow, można wyzwalać przepływy pracy, gdy reguła jest wyzwalana w IoT Central. W przepływie pracy wyzwalanym przez IoT Central lub dowolną inną aplikację możesz użyć akcji w łączniku IoT Central, aby:
- Tworzenie urządzenia
- Pobierz informacje o urządzeniu
- Aktualizowanie właściwości i ustawień urządzenia
- Uruchamianie polecenia na urządzeniu
- Usuwanie urządzenia

Zapoznaj się z [tymi Microsoft Flow szablonami](https://aka.ms/iotcentralflowtemplates) , które łączą IoT Central z innymi usługami, takimi jak powiadomienia mobilne i Microsoft Teams.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja z opcją płatność zgodnie z rzeczywistym użyciem
- Konto osobiste lub służbowe firmy Microsoft do korzystania z Microsoft Flow ([Dowiedz się więcej o planach Microsoft Flow](https://aka.ms/microsoftflowplans))
- Konto służbowe do korzystania z łącznika usługi Azure IoT Central

## <a name="trigger-a-workflow"></a>Wyzwalanie przepływu pracy

W tej sekcji pokazano, jak wyzwolić powiadomienie mobilne w aplikacji mobilnej Flow, gdy reguła jest wyzwalana w IoT Central. Ten cały przepływ pracy można skompilować w aplikacji IoT Central przy użyciu osadzonego projektanta Microsoft Flow.

1. Zacznij od [utworzenia reguły w IoT Central](howto-create-telemetry-rules.md). Po zapisaniu warunków reguły wybierz **akcję Microsoft Flow** jako nową akcję. Zostanie otwarte okno dialogowe umożliwiające skonfigurowanie przepływu pracy. Konto użytkownika IoT Central, do którego użytkownik jest zalogowany, będzie używane do logowania się do Microsoft Flow.

    ![Utwórz nową akcję Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Zostanie wyświetlona lista przepływów pracy, do których masz dostęp i które są dołączone do tej reguły IoT Central. Kliknij pozycję **Eksploruj szablony** lub **nowe > Utwórz z szablonu** i możesz wybrać dowolny z dostępnych szablonów. 

    ![Dostępne szablony Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Zostanie wyświetlony monit o zalogowanie się do łączników w wybranym szablonie. 

    > [!NOTE]
    > Aby korzystać z łącznika usługi Azure IoT Central, musisz zalogować się przy użyciu konta Azure Active Directory (konto służbowe). Konto osobiste, takie jak abc@outlook.com lub abc@live.com, nie jest obsługiwane przez łącznik usługi Azure IoT Central.

    Po zalogowaniu się do łączników będziesz mieć możliwość tworzenia przepływu pracy przez projektanta. Przepływ pracy zawiera wyzwalacz IoT Central, który ma już wypełnioną aplikację i regułę.

1. Przepływ pracy można dostosować, dostosowując informacje przesyłane do akcji i dodając nowe akcje. W tym przykładzie akcja dotyczy **powiadomień — Wyślij do mnie powiadomienie mobilne**. Możesz dołączyć *zawartość dynamiczną* z reguły IoT Central, przekazując ważne informacje, takie jak nazwa urządzenia i sygnatura czasowa do powiadomienia.

    > [!NOTE]
    > Wybierz pozycję **Zobacz więcej** tekstu w oknie zawartości dynamicznej, aby uzyskać wartości pomiaru i właściwości, które wywołały regułę.

    ![Akcja edycji przepływu z otwartym okienkiem dynamicznym](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Po zakończeniu edycji akcji wybierz pozycję **Zapisz**. Nastąpi przekierowanie do strony omówienia przepływu pracy. W tym miejscu możesz zobaczyć historię uruchamiania i udostępnić ją innym współpracownikom.

    > [!NOTE]
    > Jeśli chcesz, aby inni użytkownicy w aplikacji IoT Central mogli edytować tę regułę, musisz ją udostępnić w Microsoft Flow. Dodaj swoje konta Microsoft Flow jako właściciele w przepływie pracy.

1. Jeśli wrócisz do aplikacji IoT Central, zobaczysz, że ta reguła ma akcję Microsoft Flow w obszarze Akcje.

Możesz również tworzyć przepływy pracy za pomocą łącznika IoT Central bezpośrednio z Microsoft Flow. Następnie możesz wybrać aplikację, z którą IoT Central ma nawiązać połączenie.

## <a name="create-a-device-in-a-workflow"></a>Tworzenie urządzenia w przepływie pracy

W tej sekcji pokazano, jak utworzyć nowe urządzenie w IoT Central na wypchnięciu przycisku na urządzeniu przenośnym przy użyciu aplikacji mobilnej Microsoft Flow. Za pomocą tej akcji w usłudze Flow można zintegrować systemy ERP, takie jak Dynamics z IoT Central, tworząc nowe urządzenie, gdy urządzenie zostanie dodane w innym miejscu.

1. Zacznij od utworzenia pustego przepływu pracy w Microsoft Flow.

1. Wyszukaj **przycisk przepływu dla urządzenia przenośnego** jako wyzwalacz.

1. W tym wyzwalaczu Dodaj dane wejściowe tekstu o nazwie **urządzenia**. Zmień tekst opisu, aby **wprowadzić nazwę urządzenia nowego urządzenia**.

1. Dodaj nową akcję. Wyszukaj **IoT Central platformy Azure — Utwórz akcję urządzenia** .

1. Wybierz aplikację, a następnie wybierz szablon urządzenia, z którego chcesz utworzyć urządzenie na liście rozwijanej. Zostanie wyświetlona akcja rozwiń, aby wyświetlić wszystkie właściwości i ustawienia urządzenia.

1. Wybierz pole Nazwa urządzenia. W okienku Zawartość dynamiczna wybierz pozycję **Nazwa urządzenia**. Ta wartość jest przenoszona z danych wejściowych wprowadzanych przez użytkownika za pośrednictwem aplikacji mobilnej i jest nazwą nowego urządzenia w IoT Central. W tym przykładzie jedyne wymagane pole to nazwa urządzenia wskazywana przez czerwoną gwiazdkę. Inny szablon urządzenia może mieć wiele wymaganych pól, które muszą zostać wypełnione, aby można było utworzyć nowe urządzenie.

    ![Okienko dynamiczne akcji tworzenia urządzenia przepływu](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. Obowiązkowe Wypełnij inne pola, gdy zobaczysz, że są one dopasowane do tworzenia nowych urządzeń.

1. Na koniec Zapisz swój przepływ pracy.

1. Wypróbuj przepływ pracy w aplikacji mobilnej Microsoft Flow. Przejdź do karty **przyciski** w aplikacji. Zobaczysz przycisk, > utworzyć nowy przepływ pracy dla urządzenia. Wprowadź nazwę nowego urządzenia i obejrzyj go w IoT Central!

    ![Zrzut ekranu urządzenia przenośnego tworzenia przepływu](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Aktualizowanie urządzenia w przepływie pracy

W tej sekcji opisano sposób aktualizowania ustawień i właściwości urządzenia w IoT Central na wypchnięciu przycisku na urządzeniu przenośnym przy użyciu aplikacji mobilnej Microsoft Flow.

1. Zacznij od utworzenia pustego przepływu pracy w Microsoft Flow.

1. Wyszukaj **przycisk przepływu dla urządzenia przenośnego** jako wyzwalacz.

1. W tym wyzwalaczu Dodaj dane wejściowe, takie jak **Lokalizacja** wprowadzanie tekstu, które odnoszą się do ustawienia lub właściwości, które chcesz zmienić. Zmień tekst opisu.

1. Dodaj nową akcję. Wyszukaj **IoT Central platformy Azure — zaktualizuj akcję urządzenia** .

1. Wybierz aplikację z listy rozwijanej. Teraz musisz mieć identyfikator istniejącego urządzenia, które chcesz zaktualizować. 

    > [!NOTE] 
    > **Musisz użyć identyfikatora znalezionego w adresie URL** na stronie szczegółów urządzenia na urządzeniu, które chcesz zaktualizować. Identyfikator urządzenia znaleziony na liście urządzeń Eksploratora urządzeń nie jest jednym z nich do użycia w Microsoft Flow.

    ![Identyfikator IoT Central z adresu URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Możesz zaktualizować nazwę urządzenia. Aby zaktualizować dowolne właściwości i ustawienia urządzenia, należy wybrać szablon urządzenia, które chcesz zaktualizować, na liście rozwijanej **szablon urządzenia** . Kafelek akcja rozwija się, aby wyświetlić wszystkie właściwości i ustawienia, które można zaktualizować.

    ![Przepływ pracy urządzenia z aktualizacją przepływu](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Wybierz wszystkie właściwości i ustawienia, które chcesz zaktualizować. W okienku Zawartość dynamiczna wybierz odpowiednie dane wejściowe z wyzwalacza. W tym przykładzie wartość lokalizacji jest propagowana w dół, aby zaktualizować Właściwość lokalizacji urządzenia.

1. Na koniec Zapisz swój przepływ pracy.

1. Wypróbuj przepływ pracy w aplikacji mobilnej Microsoft Flow. Przejdź do karty **przyciski** w aplikacji. Powinieneś zobaczyć przycisk > zaktualizować przepływ pracy urządzenia. Wprowadź dane wejściowe i sprawdź, czy urządzenie zostało zaktualizowane w IoT Central!

## <a name="get-device-information-in-a-workflow"></a>Uzyskiwanie informacji o urządzeniu w przepływie pracy

Informacje o urządzeniu można uzyskać, korzystając z **usługi Azure IoT Central — Pobierz akcję urządzenia** . 
> [!NOTE] 
> **Musisz użyć identyfikatora znalezionego w adresie URL** na stronie szczegółów urządzenia na urządzeniu, które chcesz zaktualizować. Identyfikator urządzenia znaleziony na liście urządzeń Eksploratora urządzeń nie jest jednym z nich do użycia w Microsoft Flow.

Aby przejść do kolejnych akcji w przepływie pracy, można uzyskać informacje takie jak nazwa urządzenia, nazwa szablonu urządzenia, wartości właściwości i wartości ustawień. Oto przykładowy przepływ pracy, który przekazuje się wraz z wartością właściwości Nazwa klienta z urządzenia do usługi Microsoft Teams.

   ![Przepływ pracy pobierania urządzenia przepływu](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Uruchamianie polecenia na urządzeniu w przepływie pracy
Można uruchomić polecenie na urządzeniu określonym przez jego identyfikator przy użyciu **IoT Central platformy Azure — Uruchom akcję polecenia** . 

> [!NOTE] 
> **Musisz użyć identyfikatora znalezionego w adresie URL** na stronie szczegółów urządzenia na urządzeniu, które chcesz zaktualizować. Identyfikator urządzenia znaleziony na liście urządzeń Eksploratora urządzeń nie jest jednym z nich do użycia w Microsoft Flow.
    
Można wybrać polecenie do uruchomienia i przekazać parametry polecenia za pomocą tej akcji. Oto przykładowy przepływ pracy, który uruchamia polecenie ponownego uruchamiania urządzenia z przycisku w aplikacji mobilnej Microsoft Flow.

   ![Przepływ pracy pobierania urządzenia przepływu](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Usuwanie urządzenia w przepływie pracy

Urządzenie można usunąć za pomocą jego identyfikatora przy użyciu **IoT Central platformy Azure — Usuń akcję urządzenia** . 
> [!NOTE] 
> **Musisz użyć identyfikatora znalezionego w adresie URL** na stronie szczegółów urządzenia na urządzeniu, które chcesz zaktualizować. Identyfikator urządzenia znaleziony na liście urządzeń Eksploratora urządzeń nie jest jednym z nich do użycia w Microsoft Flow.

Oto przykładowy przepływ pracy, który usuwa urządzenie przy wypchnięciu przycisku w aplikacji mobilnej Microsoft Flow.

   ![Przepływ pracy usuwania urządzenia przepływu](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz problemy z tworzeniem połączenia z łącznikiem usługi Azure IoT Central, Oto kilka porad, które ułatwią Ci pomoc.

1. Konta osobiste firmy Microsoft (takie jak @hotmail.com, @live.com, domeny @outlook.com) nie są w tej chwili obsługiwane. Musisz użyć konta służbowego Azure Active Directory (AD).

2. Aby można było używać łącznika IoT Central w Microsoft Flow, należy zalogować się do aplikacji IoT Central co najmniej raz. W przeciwnym razie aplikacja nie zostanie wyświetlona na liście rozwijanej aplikacji.

3. Jeśli wystąpi błąd podczas korzystania z konta usługi Azure AD, spróbuj otworzyć program Windows PowerShell i uruchom następujący polecenia cmdlet jako administrator.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać Microsoft Flow do kompilowania przepływów pracy, sugerowanym następnym krokiem jest [Zarządzanie urządzeniami](howto-manage-devices.md).

