---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: W tym samouczku przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na danych telemetrycznych w aplikacji usługi Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d8a5ca6285624720e23a4986917ab5e715f6ebfa
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768017"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Samouczek: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central (nowy projekt interfejsu użytkownika)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

W ramach tego samouczka jest tworzona reguła, która powoduje wysyłanie wiadomości e-mail, gdy temperatura w połączonym klimatyzatorze przekroczy 90&deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodawanie akcji

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć samouczek [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), aby utworzyć szablon urządzenia **Połączony klimatyzator** umożliwiający dalszą pracę.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na danych telemetrycznych do aplikacji, w menu nawigacji po lewej stronie wybierz pozycję **Szablony urządzeń**:

    ![Strona Szablony urządzeń](media/tutorial-configure-rules-experimental/templatespage1.png)

    Zostanie wyświetlony szablon urządzenia **Połączony klimatyzator (1.0.0)** utworzony w ramach poprzedniego samouczka.

2. Aby dostosować szablon urządzenia, kliknij szablon **Połączony klimatyzator** utworzony w ramach poprzedniego samouczka.

3. Aby dodać regułę opartą na telemetrii, w widoku **Reguły** wybierz opcję **Reguły**, kliknij przycisk **+ Nowa reguła**, a następnie wybierz opcję **Telemetria**:

    ![Widok Reguły](media/tutorial-configure-rules-experimental/newrule.png)

5. Aby zdefiniować regułę, użyj informacji w poniższej tabeli:

    | Ustawienie                                      | Wartość                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Nazwa)                                         | Alert temperatury klimatyzatora |
    | Włącz regułę dla wszystkich urządzeń z tego szablonu | Włączone                                |
    | Warunek                                    | Temperatura jest wyższa niż 90    |
    | Agregacja                                  | Brak                              |

    ![Warunek reguły temperatury](media/tutorial-configure-rules-experimental/temperaturerule.png)

    Następnie kliknij przycisk **Save** (Zapisz).

## <a name="add-an-action"></a>Dodawanie akcji

W ramach definiowania reguły definiuje się także akcję uruchamianą, gdy warunki reguły są spełnione. W tym samouczku utworzysz regułę z akcją, która będzie wysyłać wiadomość e-mail z powiadomieniem.

1. Aby dodać **akcję**, najpierw **zapisz** regułę, a następnie przewiń w dół panel **Konfigurowanie reguły telemetrii**. Wybierz znak **+** obok pozycji **Akcje**, a następnie wybierz opcję **Adres e-mail**:

    ![Akcja reguły temperatury](media/tutorial-configure-rules-experimental/addaction.png)

2. Aby zdefiniować akcję, użyj informacji w poniższej tabeli:

    | Ustawienie   | Wartość                          |
    | --------- | ------------------------------ |
    | Do        | Twój adres e-mail             |
    | Uwagi     | Temperatura klimatyzatora przekracza wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja temperatury](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. Kliknij pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

![Akcja wiadomości e-mail](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodawanie akcji

Teraz, gdy zdefiniowano regułę opartą na wartości progowej, sugerowanym następnym krokiem jest [dostosowanie widoków operatora](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

Aby dowiedzieć się więcej na temat różnych typów reguł w usłudze Azure IoT Central i parametryzowania definicji reguły, zobacz:
* [Tworzenie reguły telemetrii i konfigurowanie powiadomień](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Tworzenie reguły zdarzeń i konfigurowanie powiadomień](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

<!-- Next tutorials in the sequence -->
