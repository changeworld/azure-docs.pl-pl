---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: W tym samouczku przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na danych telemetrycznych w aplikacji usługi Azure IoT Central.
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af2aa8d7b01d973da400808fd3e97d0739693cd2
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236335"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Samouczek: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central

W tym samouczku przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na danych telemetrycznych w aplikacji usługi Microsoft Azure IoT Central.

W ramach tego samouczka jest tworzona reguła, która powoduje wysyłanie wiadomości e-mail, gdy temperatura w połączonym klimatyzatorze przekroczy 90&deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodawanie akcji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć samouczek [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md), aby utworzyć szablon urządzenia **Połączony klimatyzator** umożliwiający dalszą pracę.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na danych telemetrycznych do aplikacji, w menu nawigacji po lewej stronie wybierz pozycję **Device Explorer**:

    ![Strona Device Explorer](media/tutorial-configure-rules/explorerpage.png)

    Zostanie wyświetlony szablon urządzenia **Połączony klimatyzator (1.0.0)** i urządzenie **Połączony klimatyzator-1** utworzone w ramach poprzedniego samouczka.

2. Aby rozpocząć dostosowywanie połączonego klimatyzatora, wybierz urządzenie utworzone w ramach poprzedniego samouczka:

    ![Strona Połączony klimatyzator](media/tutorial-configure-rules/builderdevicelist.png)

3. Aby rozpocząć dodawanie reguły w widoku **Reguły**, wybierz pozycję **Reguły**:

    ![Widok Reguły](media/tutorial-configure-rules/builderrulesview.png)

4. Aby rozpocząć tworzenie reguły telemetrii opartej na wartości progowej, wybierz pozycję **Nowa reguła**, a następnie **Telemetria**.

5. Aby zdefiniować regułę, użyj informacji w poniższej tabeli:

    | Ustawienie     | Wartość                          |
    | ----------- | ------------------------------ |
    | Name (Nazwa)        | Temperatura klimatyzatora    |
    | Włącz regułę | Włączone                             |
    | Warunek   | Temperatura jest wyższa niż 90 |

    ![Warunek reguły temperatury](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>Dodawanie akcji

W ramach definiowania reguły definiuje się także akcję uruchamianą, gdy warunki reguły są spełnione. W tym samouczku jest dodawana akcja wysłania wiadomości e-mail z powiadomieniem o wyzwoleniu reguły.

1. Aby dodać **akcję**, przewiń w dół panel **Konfigurowanie reguły telemetrii** i wybierz element **+** obok pozycji **Akcje**, a następnie wybierz pozycję **Wiadomość e-mail**:

    ![Akcja reguły temperatury](media/tutorial-configure-rules/builderaddaction.png)

2. Aby zdefiniować akcję, użyj informacji w poniższej tabeli:

    | Ustawienie   | Wartość                          |
    | --------- | ------------------------------ |
    | Do        | Twój adres e-mail             |
    | Uwagi     | Temperatura w klimatyzatorze przekracza wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja temperatury w Konstruktorze aplikacji](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**:

    ![Reguły Konstruktora aplikacji](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

![Akcja wiadomości e-mail](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodawanie akcji

Teraz, gdy zdefiniowano regułę opartą na wartości progowej, sugerowanym następnym krokiem jest [dostosowanie widoków operatora](tutorial-customize-operator.md).

Aby dowiedzieć się więcej na temat różnych typów reguł w usłudze Azure IoT Central i parametryzowania definicji reguły, zobacz:
* [Tworzenie reguły telemetrii i konfigurowanie powiadomień](howto-create-telemetry-rules.md).
* [Tworzenie reguły zdarzeń i konfigurowanie powiadomień](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->
