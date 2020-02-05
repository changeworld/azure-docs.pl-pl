---
title: Tworzenie przepływów pracy za pomocą łącznika IoT Central w Azure Logic Apps | Microsoft Docs
description: Użyj łącznika IoT Central w Azure Logic Apps, aby wyzwalać przepływy pracy oraz tworzyć, aktualizować i usuwać urządzenia w przepływach pracy.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3625d7374090bca73e2f054f7da4b58e951e7719
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990270"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Tworzenie przepływów pracy za pomocą łącznika IoT Central w programie Azure Logic Apps

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj Azure Logic Apps, aby utworzyć zautomatyzowane, skalowalne przepływy pracy, które integrują aplikacje i dane w usługach w chmurze i systemach lokalnych. Azure Logic Apps ma wiele łączników w wielu usługach platformy Azure, usługach firmy Microsoft i innych produktach typu "oprogramowanie jako usługa" (SaaS). Korzystając z łącznika IoT Central w Azure Logic Apps, można wyzwalać przepływy pracy, gdy reguła jest wyzwalana w IoT Central. Możesz również użyć akcji w łączniku IoT Central, aby utworzyć urządzenie, zaktualizować właściwości i ustawienia urządzenia lub usunąć urządzenie.

Możesz użyć łącznika IoT Central w Microsoft Flow. Zarówno Azure Logic Apps, jak i Microsoft Flow są usługami integracji w pierwszej kolejności, ale są ukierunkowane na nieco różnych odbiorców. Przepływ umożliwia pracownikom pakietu Office Tworzenie przepływów pracy, których potrzebują. Logic Apps umożliwia informatykom tworzenie integracji potrzebnych do łączenia danych. Porównaj przepływ i Logic Apps [tym miejscu](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Dowiedz się więcej na temat sposobu kompilowania przepływów pracy za pomocą łącznika IoT Central w Microsoft Flow [tym miejscu](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja utworzona przy użyciu standardowego planu cenowego
- Konto platformy Azure i subskrypcja do tworzenia aplikacji logiki i zarządzania nimi

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Wyzwalanie przepływu pracy w przypadku wyzwolenia reguły

W tej sekcji przedstawiono sposób publikowania wiadomości w usłudze Microsoft Teams, gdy reguła jest wyzwalana. Przepływ pracy można skonfigurować tak, aby korzystał z innych łączników, takich jak wysyłanie zdarzenia do centrum zdarzeń, tworzenie nowego elementu roboczego usługi Azure DevOps lub wstawianie nowego wiersza w programie SQL Server.

1. Zacznij od [utworzenia reguły w IoT Central](howto-create-telemetry-rules.md). Po zapisaniu warunków reguły wybierz kafelek **Azure Logic Apps** jako nową akcję. Wybierz pozycję **Utwórz w Azure Portal**. Nastąpi przekierowanie do Azure Portal, aby utworzyć nową aplikację logiki. Może być konieczne zalogowanie się do konta platformy Azure.

1. Wprowadź wymagane informacje, aby utworzyć nową aplikację logiki. Możesz wybrać subskrypcję platformy Azure, aby zainicjować obsługę nowej aplikacji logiki w usłudze. Nie musi to być ta sama subskrypcja, w której aplikacja IoT Central została utworzona. Wybierz pozycję **Utwórz**.

    ![Tworzenie aplikacji logiki w Azure Portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Po pomyślnym utworzeniu aplikacji logiki zostanie automatycznie przejściu do projektanta Logic Apps. Wybierz pozycję **pusta aplikacja logiki**. 

    ![Tworzenie pustej aplikacji logiki](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. W projektancie Wyszukaj ciąg "IoT Central" i wybierz wyzwalacz, **gdy zostanie wyzwolona reguła** . Zaloguj się do łącznika przy użyciu konta, za pomocą którego logujesz się do aplikacji IoT Central.

    ![Zaloguj się do IoT Central łącznika](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Po pomyślnym zalogowaniu powinny pojawić się pola. Wybierz **aplikację** i **regułę** z listy rozwijanej.

    ![Wybierz aplikację i regułę](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Dodaj nową akcję. Wyszukaj **zespoły komunikatów post** i wybierz pozycję **Opublikuj wiadomość** z łącznika usługi Microsoft Teams. Zaloguj się do łącznika przy użyciu konta używanego w usłudze Microsoft Teams.

1. W akcji wybierz **zespół** i **kanał**. Wypełnij pola **komunikat** informacjami o tym, co każdy komunikat powinien powiedzieć. Możesz dołączyć *zawartość dynamiczną* z reguły IoT Central, przekazując ważne informacje, takie jak nazwa urządzenia i sygnatura czasowa do powiadomienia.
    > [!NOTE]
    > Wybierz pozycję **Zobacz więcej** tekstu w oknie zawartości dynamicznej, aby uzyskać wartości pomiaru i właściwości, które wywołały regułę.

    ![Akcja edytowania aplikacji logiki z otwartym okienkiem dynamicznym](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Po zakończeniu edycji akcji wybierz pozycję **Zapisz**.

1. Jeśli wrócisz do aplikacji IoT Central, zobaczysz, że ta reguła ma akcję Azure Logic Apps w obszarze Akcje.

Możesz zawsze rozpocząć Kompilowanie przepływu pracy za pomocą łącznika IoT Central w Logic Apps w Azure Portal. Następnie możesz IoT Central wybrać aplikację, z którą ma zostać nawiązane połączenie, a następnie tak samo działa. Za każdym razem nie ma potrzeby uruchamiania na stronie reguł IoT Central.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Tworzenie, aktualizowanie i usuwanie urządzenia w przepływie pracy

Podczas tworzenia przepływu pracy w aplikacji logiki można dodać akcję przy użyciu łącznika IoT Central. Dostępne akcje to **Tworzenie urządzenia**, **Aktualizowanie urządzenia**i **Usuwanie urządzenia**.

> [!NOTE]
> Aby **zaktualizować urządzenie** i **usunąć urządzenie**, należy potrzebować identyfikatora istniejącego urządzenia, które chcesz zaktualizować lub usunąć. Identyfikator urządzenia IoT Central można uzyskać w **Device Explorer**

![IoT Central identyfikator urządzenia w Eksploratorze urządzeń](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać Microsoft Flow do kompilowania przepływów pracy, sugerowanym następnym krokiem jest [Zarządzanie urządzeniami](howto-manage-devices.md).
