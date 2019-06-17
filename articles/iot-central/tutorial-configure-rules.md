---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: W tym samouczku przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na danych telemetrycznych w aplikacji usługi Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 56ced4f5e2fd0fbf829f72cff2413998398a7a09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066017"
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

1. Aby dodać nowej reguły na podstawie danych telemetrycznych aplikacji, w menu nawigacji po lewej stronie wybierz **szablonów urządzeń**:

    ![Strona Szablony urządzeń](media/tutorial-configure-rules/templatespage1.png)

    Zostanie wyświetlony szablon urządzenia **Połączony klimatyzator (1.0.0)** utworzony w ramach poprzedniego samouczka.

2. Aby dostosować szablon urządzenia, wybierz opcję **połączone klimatyzacyjne** szablon utworzony w poprzednim samouczku.

3. Można dodać reguły na podstawie danych telemetrycznych w **reguły** widoku, wybierz opcję **reguły**, wybierz opcję **+ Nowa reguła**, a następnie wybierz **Telemetrii**:

    ![Widok Reguły](media/tutorial-configure-rules/newrule.png)

5. Aby zdefiniować regułę, użyj informacji w poniższej tabeli:

    | Ustawienie                                      | Wartość                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Nazwa)                                         | Alert temperatury klimatyzatora |
    | Włącz regułę dla wszystkich urządzeń z tego szablonu | Włączone                                |
    | Warunek                                    | Temperatura jest wyższa niż 90    |
    | Agregacja                                  | Brak                              |

    ![Warunek reguły temperatury](media/tutorial-configure-rules/temperaturerule.png)

    Następnie wybierz pozycję **Zapisz**.

## <a name="add-an-action"></a>Dodawanie akcji

W ramach definiowania reguły definiuje się także akcję uruchamianą, gdy warunki reguły są spełnione. W tym samouczku utworzysz regułę z akcją, która będzie wysyłać wiadomość e-mail z powiadomieniem.

1. Aby dodać **akcję**, najpierw **zapisz** regułę, a następnie przewiń w dół panel **Konfigurowanie reguły telemetrii**. Wybierz znak **+** obok pozycji **Akcje**, a następnie wybierz opcję **Adres e-mail**:

    ![Akcja reguły temperatury](media/tutorial-configure-rules/addaction.png)

2. Aby zdefiniować akcję, użyj informacji w poniższej tabeli:

    | Ustawienie   | Wartość                          |
    | --------- | ------------------------------ |
    | Do        | Twój adres e-mail             |
    | Uwagi     | Temperatura klimatyzatora przekracza wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja temperatury](media/tutorial-configure-rules/temperatureaction.png)

3. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Kolejne kroki

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