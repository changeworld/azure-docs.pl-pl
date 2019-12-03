---
title: Szybki Start — Konfigurowanie reguł i akcji na platformie Azure IoT Central
description: W tym przewodniku szybki start pokazano, jak za pomocą konstruktora, jak skonfigurować reguły i akcje na podstawie danych telemetrycznych w aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f81171d0f545bbf7f903d54bee761817e9451d9d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706725"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Szybki Start: Konfigurowanie reguł i akcji dla urządzenia w usłudze Azure IoT Central (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym przewodniku szybki start utworzysz regułę, która wysyła wiadomość e-mail, gdy temperatura na urządzeniu czujnika środowiskowego przekracza 90&deg; F.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać dwa poprzednie Przewodniki Szybki Start [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md) i [Dodawanie symulowanego urządzenia do aplikacji IoT Central](./quick-create-pnp-device.md) w celu utworzenia szablonu urządzenia **czujnika środowiska** do pracy z programem.

## <a name="create-a-telemetry-based-rule"></a>Tworzenie reguły opartej na danych telemetrycznych

1. Aby dodać nową regułę opartą na telemetrii do aplikacji, w okienku po lewej stronie wybierz pozycję **reguły**.

1. Aby utworzyć nową regułę, wybierz pozycję **+ Nowy**.

1. Wprowadź **temperaturę środowiska** jako nazwę reguły.

1. W sekcji **urządzenia docelowe** wybierz **czujnik środowiska** jako szablon urządzenia. Ta opcja umożliwia filtrowanie urządzeń, do których odnosi się reguła według typu szablonu urządzenia. Można dodać więcej kryteriów filtrowania, wybierając **+ Filter**.

1. W sekcji **warunki** definiujesz, co wyzwala regułę. Poniższe informacje służą do definiowania warunków opartych na telemetrii temperatury:

    | Pole                                        | Wartość                             |
    | -------------------------------------------- | ------------------------------    |
    | Miara                                  | Temperatura                       |
    | Operator                                     | jest większe niż                   |
    | Wartość                                        | 90                                |

    Aby dodać więcej warunków, wybierz pozycję **+ warunek**.

    ![Utwórz warunek reguły](./media/quick-configure-rules/condition.png)

1. Aby dodać akcję poczty e-mail do uruchomienia, gdy reguła jest wyzwalana, wybierz pozycję **+ poczta e-mail**.

1. Skorzystaj z informacji w poniższej tabeli, aby zdefiniować akcję:

    | Ustawienie   | Wartość                                             |
    | --------- | ------------------------------------------------- |
    | Nazwa wyświetlana | Akcja e-mail operatora                          |
    | Do        | Twój adres e-mail                                |
    | Uwagi     | Temperatura środowiska przekroczyła wartość progową. |

    > [!NOTE]
    > Aby użytkownik mógł otrzymywać wiadomości e-mail z powiadomieniem, adres e-mail musi być [identyfikatorem użytkownika w aplikacji](howto-administer.md), a użytkownik musiał zalogować się do aplikacji co najmniej raz.

    ![Akcja tworzenia reguły](./media/quick-configure-rules/action.png)

1. Wybierz pozycję **Zapisz**. Reguła zostanie wyświetlona na stronie **Reguły**.

## <a name="test-the-rule"></a>Testowanie reguły

Wkrótce po zapisaniu reguły stanie się ona aktywna. Gdy warunki zdefiniowane w regule zostaną spełnione, aplikacja wyśle wiadomość na adres e-mail określony w akcji.

> [!NOTE]
> Po zakończeniu testowania wyłącz regułę, aby zatrzymać wysyłanie wiadomości do Twojej skrzynki odbiorczej.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Aby dowiedzieć się więcej o monitorowaniu urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Użyj usługi Azure IoT Central do monitorowania urządzeń](quick-monitor-devices.md).
