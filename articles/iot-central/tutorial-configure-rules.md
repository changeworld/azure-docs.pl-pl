---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: W tym samouczku przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na danych telemetrycznych w aplikacji usługi Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7bdfa1659a0c34b3456ed3e52c72cafafc8be034
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993628"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Samouczek: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

W ramach tego samouczka jest tworzona reguła, która powoduje wysyłanie wiadomości e-mail, gdy temperatura w połączonym klimatyzatorze przekroczy 90&deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodawanie akcji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć samouczek [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md), aby utworzyć szablon urządzenia **Połączony klimatyzator** umożliwiający dalszą pracę.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na danych telemetrycznych do aplikacji, w menu nawigacji po lewej stronie wybierz pozycję **Device Explorer**:

    ![Strona Device Explorer](media/tutorial-configure-rules/explorerpage1.png)

    Zostanie wyświetlony szablon urządzenia **Połączony klimatyzator (1.0.0)** i urządzenie **Połączony klimatyzator-1** utworzone w ramach poprzedniego samouczka.

2. Aby rozpocząć dostosowywanie połączonego klimatyzatora, wybierz urządzenie utworzone w ramach poprzedniego samouczka:

    ![Strona Połączony klimatyzator](media/tutorial-configure-rules/builderdevicelist1.png)

3. Aby rozpocząć dodawanie reguły w widoku **Reguły**, wybierz pozycję **Reguły**, a następnie kliknij opcję **Edytuj szablon**:

    ![Widok Reguły](media/tutorial-configure-rules/builderedittemplate.png)

4. Aby utworzyć regułę telemetrii opartą na wartościach progowych, kliknij przycisk **Nowa reguła**, a następnie **Telemetria**.

    ![Edytowanie szablonu](media/tutorial-configure-rules/buildernewrule.png)

5. Aby zdefiniować regułę, użyj informacji w poniższej tabeli:

    | Ustawienie                                      | Wartość                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Nazwa)                                         | Alert temperatury klimatyzatora |
    | Włącz regułę dla wszystkich urządzeń z tego szablonu | Włączone                                |
    | Włącz regułę na tym urządzeniu                   | Włączone                                |
    | Warunek                                    | Temperatura jest wyższa niż 90    |
    | Agregacja                                  | Brak                              |

    ![Warunek reguły temperatury](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Dodawanie akcji

W ramach definiowania reguły definiuje się także akcję uruchamianą, gdy warunki reguły są spełnione. W tym samouczku jest dodawana akcja wysłania wiadomości e-mail z powiadomieniem o wyzwoleniu reguły.

1. Aby dodać **akcję**, najpierw **Zapisz** regułę, a następnie przewiń w dół panel **Konfigurowanie reguły telemetrii** i wybierz element **+** obok pozycji **Akcje**, a następnie wybierz pozycję **Wiadomość e-mail**:

    ![Akcja reguły temperatury](media/tutorial-configure-rules/builderaddaction1.png)

2. Aby zdefiniować akcję, użyj informacji w poniższej tabeli:

    | Ustawienie   | Wartość                          |
    | --------- | ------------------------------ |
    | Do        | Twój adres e-mail             |
    | Uwagi     | Temperatura klimatyzatora przekracza wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja temperatury w Konstruktorze aplikacji](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**:

    ![Reguły Konstruktora aplikacji](media/tutorial-configure-rules/builderrules1.png)

4. Wybierz przycisk **Gotowe**, aby wyjść z trybu **Edytuj szablon**.
 

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

![Akcja wiadomości e-mail](media/tutorial-configure-rules/email.png)

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej. 

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
