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
ms.openlocfilehash: 30bfd3b119b3ae4badbda50747158e8b08a9dd08
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875522"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Samouczek: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W ramach tego samouczka jest tworzona reguła, która powoduje wysyłanie wiadomości e-mail, gdy temperatura w połączonym klimatyzatorze przekroczy 90&deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodaj akcję

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć samouczek [Definiowanie nowego typu urządzenia w aplikacji](tutorial-define-device-type.md), aby utworzyć szablon urządzenia **Połączony klimatyzator** umożliwiający dalszą pracę.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na telemetrii do aplikacji, w menu nawigacji po lewej stronie wybierz pozycję **Szablony urządzeń**:

    ![Strona Szablony urządzeń](media/tutorial-configure-rules/templatespage1.png)

    Zostanie wyświetlony szablon urządzenia **Połączony klimatyzator (1.0.0)** utworzony w ramach poprzedniego samouczka.

2. Aby dostosować szablon urządzenia, wybierz szablon **warunki połączonego środowiska AIR** utworzony w poprzednim samouczku.

3. Aby dodać regułę opartą na telemetrii w widoku **reguły** , wybierz pozycję **reguły**, wybierz pozycję **+ Nowa reguła**, a następnie wybierz pozycję **Telemetria**:

    ![Widok Reguły](media/tutorial-configure-rules/newrule.png)

5. Aby zdefiniować regułę, użyj informacji w poniższej tabeli:

    | Ustawienie                                      | Wartość                             |
    | -------------------------------------------- | ------------------------------    |
    | Name                                         | Alert temperatury klimatyzatora |
    | Włącz regułę dla wszystkich urządzeń z tego szablonu | Włączone                                |
    | Warunek                                    | Temperatura jest wyższa niż 90    |
    | Agregacja                                  | Brak                              |

    ![Warunek reguły temperatury](media/tutorial-configure-rules/temperaturerule.png)

    Następnie wybierz pozycję **Zapisz**.

## <a name="add-an-action"></a>Dodaj akcję

W ramach definiowania reguły definiuje się także akcję uruchamianą, gdy warunki reguły są spełnione. W tym samouczku utworzysz regułę z akcją, która będzie wysyłać wiadomość e-mail z powiadomieniem.

1. Aby dodać **akcję**, najpierw **zapisz** regułę, a następnie przewiń w dół panel **Konfigurowanie reguły telemetrii**. Wybierz znak **+** obok pozycji **Akcje**, a następnie wybierz opcję **Adres e-mail**:

    ![Akcja reguły temperatury](media/tutorial-configure-rules/addaction.png)

2. Aby zdefiniować akcję, użyj informacji w poniższej tabeli:

    | Ustawienie   | Value                          |
    | --------- | ------------------------------ |
    | Zadanie        | Twój adres e-mail             |
    | Uwagi     | Temperatura klimatyzatora przekracza wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja temperatury](media/tutorial-configure-rules/temperatureaction.png)

3. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodaj akcję

Teraz, gdy zdefiniowano regułę opartą na wartości progowej, sugerowanym następnym krokiem jest [dostosowanie widoków operatora](tutorial-customize-operator.md).

Aby dowiedzieć się więcej na temat różnych typów reguł w usłudze Azure IoT Central i parametryzowania definicji reguły, zobacz:
* [Tworzenie reguły telemetrii i konfigurowanie powiadomień](howto-create-telemetry-rules.md).
* [Tworzenie reguły zdarzeń i konfigurowanie powiadomień](howto-create-event-rules.md).

<!-- Next tutorials in the sequence -->