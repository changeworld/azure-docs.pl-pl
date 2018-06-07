---
title: Tworzenie i zarządzanie regułami telemetrii w aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Reguły telemetrii w usłudze Azure IoT centralnego umożliwiają monitorowanie urządzeń w najbliższym czasie rzeczywistym i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail, gdy zasada wyzwala.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629817"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Utwórz regułę telemetrii i skonfigurować powiadomienia w aplikacji Azure IoT centralnej

Centrum IoT Azure Microsoft umożliwia zdalne monitorowanie podłączonych urządzeń. Azure IoT centralne zasady umożliwiają monitorowanie urządzeń w najbliższym czasie rzeczywistym i automatycznie wywołują akcje, takie jak wysyłanie wiadomości e-mail, gdy zasada wyzwala. Za pomocą kilku kliknięć można określić warunku do monitorowania danych urządzenia i skonfiguruj akcję do wywołania. W tym artykule opisano reguły telemetrii szczegółowo.

Używa Azure IoT centralnego [pomiary telemetrii](howto-set-up-template.md) do przechwytywania danych urządzenia. Każdy typ miary ma atrybutów klucza, które definiują pomiaru. Można utworzyć reguły do monitorowania każdego typu pomiaru urządzenia i generować alerty, gdy zasada wyzwala. Reguła telemetrii uaktywnia telemetrii wybranego urządzenia przekracza określoną wartość progową.

## <a name="create-a-telemetry-rule"></a>Utwórz regułę telemetrii

W tej sekcji przedstawiono sposób tworzenia reguły telemetrii. W tym przykładzie użyto klimatyzator podłączonego urządzenia, które wysyła dane telemetryczne temperatury i wilgotności. Reguła monitoruje temperatury zgłoszonych przez urządzenia i wysyła wiadomość e-mail, gdy przekracza ona 80 stopni.

1. Przejdź do strony szczegółów urządzenia dla urządzenia, który dodajesz reguły.

1. Jeśli jeszcze nie utworzono żadnych reguł, zostanie wyświetlony następujący ekran:

    ![Jeszcze żadnych reguł](media\howto-create-telemetry-rules\image1.png)

1. Na **reguły** , wybierz pozycję **+ nową regułę** Aby wyświetlić typy reguł można utworzyć.

    ![Typy reguł](media\howto-create-telemetry-rules\image2.png)

1. Wybierz **Telemetrii** Kafelek, aby otworzyć formularz, aby utworzyć regułę.

    ![Reguła telemetrii](media\howto-create-telemetry-rules\image3.png)

1. Wybierz nazwę, która pomaga w identyfikacji reguły w tym szablonie urządzenia.

1. Aby natychmiast reguły dla wszystkich urządzeń, które są tworzone na podstawie tego szablonu, Przełącz **Włącz regułę**.

### <a name="configure-the-rule-condition"></a>Konfigurowanie warunku reguły

W tej sekcji przedstawiono sposób Dodaj warunek do monitorowania danych telemetrycznych temperatury.

1. Wybierz **+** obok **warunku**.

1. Wybierz **temperatury** typ danych telemetrycznych z listy rozwijanej. Następnie wybierz operator i podaj wartość progową. Można dodać wiele warunków telemetrii. Jeśli określono wiele warunków, musi spełniać wszystkie warunki dla reguły do wyzwalania.

    ![Dodaj warunek Telemetrii](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Wybierz co najmniej jednej miary telemetrii podczas definiowania warunek reguły telemetrii.

### <a name="configure-the-action"></a>Konfigurowanie akcji

W tej sekcji przedstawiono sposób określania, jakie reguły wykonuje, gdy warunek zgodny przez dodanie akcji.

1. Wybierz **+** obok **akcje**. Tutaj możesz wyświetlić listę dostępnych akcji. W publicznej wersji zapoznawczej **E-mail** jest jedyną akcją, obsługiwane.

    ![Dodaj akcję](media\howto-create-telemetry-rules\image5.png)

1. Wybierz **E-mail** akcji, wprowadź prawidłowy adres e-mail w **do** pola i podaj Uwaga do występować w treści wiadomości e-mail, gdy zasada wyzwala.

    > [!NOTE]
    > Wiadomości e-mail będą wysyłane tylko do użytkowników, które zostały dodane do aplikacji i zalogowali się co najmniej raz. Dowiedz się więcej o [Zarządzanie użytkownikami](howto-administer.md) w Azure IoT centralnej.

   ![Konfigurowanie akcji](media\howto-create-telemetry-rules\image6.png)

1. Aby zapisać regułę, wybierz **zapisać**. Reguła przechodzi na żywo za kilka minut i rozpoczyna monitorowanie dane telemetryczne są wysyłane do aplikacji. Gdy spełnia warunek określony w regule, zasada wyzwala akcji skonfigurowanych poczty e-mail.

## <a name="parameterize-the-rule"></a>Parametryzacja reguły

Zasady mogą pochodzić niektórych vales z **właściwości urządzenia** jako parametry. Przy użyciu parametrów jest przydatne w scenariuszach, w którym dane telemetryczne progi różnią się dla różnych urządzeń. Po utworzeniu reguły wybierz właściwość urządzenia, która określa próg, takich jak **maksymalny próg idealne**, zamiast podawania wartości bezwzględne, takie jak 80 stopni. Podczas wykonywania reguły, jest on zgodny telemetrii urządzenia z podanej wartości we właściwości urządzenia.

Przy użyciu parametrów jest efektywnym sposobem, aby zmniejszyć liczbę zasad do zarządzania na urządzeniu szablonu.

Akcje można również skonfigurować za pomocą **właściwości urządzenia** jako parametr. Jeśli adres e-mail jest przechowywany jako właściwość urządzenia, a następnie mogą być używane podczas definiowania **do** adres.

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebny, usuń go, otwierając reguły i wybierając pozycję **usunąć**. Usuwanie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkie skojarzone urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Włącza lub wyłącza regułę dla szablonu urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełączanie **Włącz reguły dla wszystkich urządzeń z tego szablonu** przycisk w regule Włącza lub wyłącza reguły dla wszystkich urządzeń skojarzonych z szablonem urządzenia.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włącz lub wyłącz reguły dla urządzenia

Przejdź do urządzenia, a następnie wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz **Włącz regułę dla tego urządzenia** przycisk, aby włączyć funkcję lub wyłączyć regułę dla tego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak edytować reguły w aplikacji Azure IoT centralnej, Oto sugerowane następnego kroku:

> [!div class="nextstepaction"]
> [Jak zarządzać urządzeniami](howto-manage-devices.md).