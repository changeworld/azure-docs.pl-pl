---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: W tym samouczku przedstawiono, jak konstruktor konfiguruje reguły i akcje oparte na danych telemetrycznych w aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878152"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Samouczek: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

W tym samouczku utworzysz regułę, która wysyła wiadomość e-mail, gdy temperatura na urządzeniu czujnika środowiska przekroczy 90&deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły opartej na danych telemetrycznych
> * Dodaj akcję

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać instrukcje [Zdefiniuj nowy typ urządzenia w aplikacji](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) samouczek, aby utworzyć szablon urządzenia **czujnika środowiska** do pracy z programem.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na telemetrii do aplikacji, w menu nawigacji po lewej stronie wybierz pozycję **reguły**.

1. Aby utworzyć nową regułę, wybierz pozycję **+ Nowy**. Następnie wybierzpozycję telemetrię.

1. Wprowadź **temperaturę środowiska** jako nazwę reguły.

1. W sekcji **zakres** wybierz **czujnik środowiska** jako szablon urządzenia. Zakres, do którego mają zastosowanie ta reguła. Można dodać więcej kryteriów filtrowania przy użyciu **+ Filter**.

1. W sekcji **warunek** zdefiniujesz, co wyzwala regułę. Poniższe informacje służą do definiowania warunków opartych na telemetrii temperatury:

    | Pole                                        | Value                             |
    | -------------------------------------------- | ------------------------------    |
    | Miara                                  | Temperatura                       |
    | Operator                                     | jest większe niż                   |
    | Value                                        | 90                                |

    Aby dodać więcej warunków, wybierz pozycję **+ warunek**.

    ![Utwórz warunek reguły](./media/tutorial-configure-rules-pnp/condition.png)

1. Aby dodać akcję do uruchomienia podczas wyzwalania reguły, wybierz pozycję **+ Akcja**, a następnie wybierz pozycję **e-mail**.

1. Skorzystaj z informacji w poniższej tabeli, aby zdefiniować akcję:

    | Ustawienie   | Value                                             |
    | --------- | ------------------------------------------------- |
    | Zadanie        | Twój adres e-mail                                |
    | Uwagi     | Temperatura środowiska przekroczyła wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja tworzenia reguły](./media/tutorial-configure-rules-pnp/action.png)

1. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodaj akcję

Po zdefiniowaniu reguły opartej na progach sugerowany następny krok to:

> [!div class="nextstepaction"]
> [Tworzenie reguły zdarzeń i konfigurowanie powiadomień](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
