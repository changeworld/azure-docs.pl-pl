---
title: Tworzenie przepływów pracy z łącznikiem usługi IoT Central w usłudze Azure Logic Apps | Dokumentacja firmy Microsoft
description: Korzystania z łącznika IoT Central w usłudze Azure Logic Apps, aby wyzwalać przepływy i tworzenie, aktualizowanie i usuwanie urządzeń w przepływach pracy.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 635c8d0f149895798eece8cf3b48712ab74374ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887136"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Tworzenie przepływów pracy z łącznikiem usługi IoT Central w usłudze Azure Logic Apps

*Ten temat dotyczy konstruktorów i administratorów.*

Użyj usługi Azure Logic Apps do tworzenia automatycznych skalowalnych przepływów pracy, które integrują aplikacje i dane między usługami w chmurze i systemach lokalnych. Usługa Azure Logic Apps oferuje wiele łączników w wielu usługach platformy Azure, usług firmy Microsoft i innych produktów Software-As-A-Service (SaaS). Za pomocą łącznika usługi IoT Central w usłudze Azure Logic Apps, może wyzwalać przepływy pracy po wyzwoleniu reguły w IoT Central. Umożliwia także akcje w łączniku usługi IoT Central utworzenie urządzenia, aktualizowanie właściwości urządzenia i ustawienia lub usunąć urządzenie.

Za pomocą łącznika usługi IoT Central w Microsoft Flow. Usługi Azure Logic Apps i Microsoft Flow są usługi integracji najpierw Projektant, lecz nieco innych docelowych odbiorców. Przepływ biurowemu dowolnej kompilacji biznesowe przepływy pracy, które są im potrzebne. Usługa Logic Apps umożliwia specjalistom IT tworzenie integracji są konieczne jest połączenie danych. Porównanie usług Flow i Logic Apps [tutaj](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Dowiedz się więcej o tym, jak tworzyć przepływy pracy z łącznikiem usługi IoT Central w Microsoft Flow [tutaj](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja płatność za rzeczywiste użycie
- Konto platformy Azure i subskrypcję, aby utworzyć i zarządzanie aplikacjami logiki

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Wyzwalanie przepływu pracy po wyzwoleniu reguły

W tej sekcji pokazano, jak Opublikuj wiadomość w usłudze Microsoft Teams po wyzwoleniu reguły. Możesz skonfigurować przepływ pracy na potrzeby innych łączników wykonywanie czynności takich jak wysyłanie zdarzeń do Centrum zdarzeń, Utwórz nowy element roboczy DevOps platformy Azure lub wstawić nowy wiersz w programie SQL server.

1. Rozpocznij od [tworzenia reguły w IoT Central](howto-create-telemetry-rules.md). Po zapisaniu warunki reguły wybierz **usługi Azure Logic Apps** Kafelek jako nową akcję. Wybierz **Utwórz w witrynie Azure portal**. Nastąpi przekierowanie do witryny Azure portal utworzyć nową aplikację logiki. Może być konieczne Zaloguj się do konta platformy Azure.

1. Wprowadź wymagane informacje, aby utworzyć nową aplikację logiki. Możesz wybrać subskrypcję platformy Azure, aby zainicjować obsługę nowej aplikacji logiki do. Nie ma być ta sama subskrypcja, użytym do utworzenia aplikacji IoT Central. Wybierz pozycję **Utwórz**.

    ![Tworzenie aplikacji logiki w witrynie Azure portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Po pomyślnym utworzeniu aplikacji logiki są automatycznie wyświetlana strona wskazywana Projektant aplikacji logiki. Wybierz **pusta aplikacja logiki**. 

    ![Tworzenie pustej aplikacji logiki](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. W projektancie, wyszukaj "iot central", a następnie wybierz **po wyzwoleniu reguły** wyzwalacza. Zaloguj się do łącznika przy użyciu konta, którego zalogujesz się do Twojej aplikacji IoT Central, za pomocą.

    ![Zaloguj się do łącznika IoT Central](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Po pomyślnym zalogowaniu powinny być widoczne pola są wyświetlane. Wybierz **aplikacji** i **reguły** z list rozwijanych.

    ![Wybierz aplikację i reguły](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Dodaj nową akcję. Wyszukaj **Opublikuj komunikat zespoły** i wybierz polecenie **Opublikuj wiadomość w** z łącznika programu Microsoft Teams. Zaloguj się do łącznika przy użyciu konta, którego używasz w Microsoft Teams.

1. W akcji, wybierz **zespołu** i **kanału**. Wypełnij **komunikat** pole co chcesz każdą wiadomość mówią. Możesz uwzględnić *zawartości dynamicznej* z reguły IoT Central, przekazując wzdłuż ważne informacje, takie jak nazwa urządzenia i sygnatura czasowa do powiadomienia.
    > [!NOTE]
    > Wybierz **Zobacz więcej** tekst w oknie zawartości dynamicznej można pobrać miary i wartości właściwości, które wyzwolona reguła.

    ![Otwórz edycji akcji aplikacji logiki za pomocą okienka dynamicznej](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Po zakończeniu edycji akcję, wybierz **Zapisz**.

1. Po powrocie do aplikacji IoT Central, zobaczysz, że ta reguła zawiera akcję usługi Azure Logic Apps, w obszarze akcji.

Zawsze możesz rozpocząć tworzenie przepływu pracy przy użyciu łącznika usługi IoT Central w usłudze Logic Apps w witrynie Azure Portal. Możesz wybrać aplikacji, która IoT Central i reguły, które można połączyć się i nadal działa tak samo. Nie ma potrzeby początek strony reguł IoT Central każdorazowo.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Tworzenie, aktualizowanie i usuwanie urządzeń w przepływie pracy

Podczas tworzenia przepływu pracy w aplikacji logiki możesz dodać akcję przy użyciu łącznika usługi IoT Central. Dostępne są akcje **utworzenie urządzenia**, **aktualizacji urządzenia**, i **usunąć urządzenie**.

> [!NOTE]
> Dla **aktualizacji urządzenia** i **usunąć urządzenie**, musisz mieć identyfikator istniejącego urządzenia, które chcesz zaktualizować lub usunąć. Możesz uzyskać identyfikator urządzenia IoT Central w **Device Explorer**

![Identyfikator urządzenia w Eksploratorze urządzenia IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz, jak tworzyć przepływy pracy za pomocą Microsoft Flow, sugerowane następnym krokiem jest [zarządzania urządzeniami](howto-manage-devices.md).
