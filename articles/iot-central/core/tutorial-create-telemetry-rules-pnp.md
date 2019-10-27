---
title: Tworzenie reguł i zarządzanie nimi w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym oraz automatyczne wywoływanie akcji, na przykład wysyłania wiadomości e-mail, gdy reguła jest wyzwalana.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94526cc586f9e70be4222597c7d9f44c4e80c281
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955816"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Samouczek: Tworzenie reguły i Konfigurowanie powiadomień w aplikacji Azure IoT Central (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Za pomocą usługi Azure IoT Central można zdalnie monitorować połączone urządzenia. Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym i automatyczne wywoływanie akcji, na przykład wysłanie wiadomości e-mail. Wystarczy kilka kliknięć, aby zdefiniować warunek służący do monitorowania danych telemetrycznych z urządzeń i konfigurowania odpowiedniej akcji. W tym artykule wyjaśniono, jak utworzyć reguły monitorowania danych telemetrycznych wysyłanych przez urządzenie.

Urządzenia używają telemetrii do wysyłania danych liczbowych z urządzenia. Reguła jest wyzwalana, gdy wybrana wartość telemetrii urządzenia przekroczy określony próg.

W tym samouczku utworzysz regułę wysyłania wiadomości e-mail, gdy temperatura na urządzeniu czujnika środowiska przekroczy 80&deg; F.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie reguły
> * Dodaj akcję poczty e-mail

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy ukończyć [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) i [dodać symulowane urządzenie do aplikacji IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) przewodników Szybki Start, aby utworzyć szablon urządzenia **czujnika środowiska** do pracy z programem.

## <a name="create-a-rule"></a>Tworzenie reguły

Aby utworzyć regułę telemetrii, szablon urządzenia musi mieć zdefiniowany co najmniej jeden pomiar telemetrii. W tym samouczku jest używane urządzenie czujnika środowiska, które wysyła dane telemetryczne temperatury i wilgotności. Dodano ten szablon urządzenia i utworzono symulowane urządzenie na stronie [Dodawanie symulowanego urządzenia do aplikacji do IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) przewodnika Szybki Start. Reguła monitoruje temperaturę zgłoszoną przez urządzenie i wysyła wiadomość e-mail, gdy znajdzie się ona powyżej 80 stopni.

1. W lewym okienku wybierz pozycję **reguły**.

1. Jeśli nie utworzono jeszcze żadnych reguł, zobaczysz następujący ekran:

    ![Nie ma jeszcze reguł](media/tutorial-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Wybierz **+** , aby dodać nową regułę.

1. Wprowadź nazwę _monitora temperatury_ , aby zidentyfikować regułę, a następnie naciśnij klawisz ENTER.

1. Wybierz szablon urządzenia **czujnika środowiska** . Domyślnie reguła ma zastosowanie automatycznie do wszystkich urządzeń skojarzonych z szablonem urządzenia. Aby odfiltrować podzestaw urządzeń, wybierz opcję **+ Filtr** i Użyj właściwości urządzenia w celu zidentyfikowania urządzeń. Aby wyłączyć regułę, przełącz przycisk **włączone/wyłączone** w nagłówku reguły:

    ![Filtry i Włącz](media/tutorial-create-telemetry-rules-pnp/device-filters.png)

### <a name="configure-the-rule-conditions"></a>Skonfiguruj warunki reguły

Warunki określają kryteria monitorowane przez regułę. W tym samouczku skonfigurujesz regułę do uruchamiania, gdy temperatura przekracza 80&deg; F.

1. Wybierz pozycję **temperatura** na liście rozwijanej **telemetrii** .

1. Następnie wybierz pozycję **jest większy niż** **Operator** i wprowadź _80_ jako **wartość**.

    ![Warunek](media/tutorial-create-telemetry-rules-pnp/condition-filled-out1.png)

1. Opcjonalnie można ustawić **agregację czasu**. Po wybraniu agregacji czasu należy również wybrać typ agregacji, taki jak Average lub sum z listy rozwijanej agregacji.

    * Bez agregacji reguła wyzwala dla każdego punktu danych telemetrii, który spełnia warunek. Na przykład, jeśli reguła jest skonfigurowana do wyzwalania, gdy temperatura jest większa niż 80, reguła jest wyzwalana niemal natychmiast, gdy urządzenie zgłosi temperaturę > 80.
    * Przy agregacji reguła jest wyzwalana, gdy wartość zagregowana punktów danych telemetrii w przedziale czasu spełnia warunek. Jeśli na przykład zasada jest skonfigurowana do wyzwalania, gdy temperatura jest większa niż 80, agregacja czasu jest ustawiona na 10 minut, a typ agregacji to średnia, reguła wyzwala, gdy urządzenie zgłosi średnią temperaturę > 80, obliczoną w ciągu 10 minut dat.

     ![Warunek agregacji](media/tutorial-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)

Do reguły można dodać wiele warunków, wybierając pozycję **+ warunek**. W przypadku określenia wielu warunków wszystkie warunki muszą być spełnione, aby reguła była wyzwalana. Każdy warunek jest przyłączony przez niejawną klauzulę `AND`. Jeśli używasz agregacji czasu z wieloma warunkami, wszystkie wartości telemetryczne muszą być agregowane.

### <a name="configure-actions"></a>Skonfiguruj akcje

Po zdefiniowaniu warunku należy skonfigurować akcje do wykonania, gdy reguła zostanie wygenerowane. Akcje są wywoływane, gdy wszystkie warunki określone w regule mają wartość true. Obecnie jest to jedyna dostępna akcja.

1. Wybierz pozycję **+ poczta e-mail** w sekcji **Akcje** .

1. Wprowadź _ostrzegawczą temperaturę_ jako nazwę wyświetlaną akcji, adres e-mail w polu **do** i _Sprawdź urządzenie_ . jako notatka, która ma być wyświetlana w treści wiadomości e-mail.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zarejestrowali się co najmniej raz. Dowiedz się więcej na temat [zarządzania użytkownikami](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) w usłudze Azure IoT Central.

   ![Konfiguruj akcję](media/tutorial-create-telemetry-rules-pnp/configure-action1.png)

1. Aby zapisać akcję, wybierz pozycję **gotowe**. Do reguły można dodać wiele akcji.

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i zacznie monitorować dane telemetryczne wysyłane do aplikacji. Gdy warunek określony w regule zostanie spełniony, reguła wyzwala skonfigurowaną akcję poczty e-mail.

Gdy reguła zostanie wyświetlona, otrzymasz wiadomość e-mail z powiadomieniem:

![Przykład wiadomości e-mail](media/tutorial-create-telemetry-rules-pnp/email.png)

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając regułę i wybierając pozycję **Usuń**.

## <a name="enable-or-disable-a-rule"></a>Włączanie lub wyłączanie reguły

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz przycisk **Włącz** lub **Wyłącz** w regule, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń objętych zakresem reguły.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włączanie lub wyłączanie reguły dla urządzenia

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Dodaj filtr w sekcji **zakresy** , aby dołączyć lub wykluczyć określone urządzenie w szablonie urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć reguły w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z [tematem zarządzanie urządzeniami](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie reguły opartej na danych telemetrycznych
* Dodawanie akcji

Po zdefiniowaniu reguły opartej na progach zalecanym następnym krokiem jest zapoznanie się z tematem:

> [!div class="nextstepaction"]
> [Skonfiguruj ciągły eksport danych](./howto-export-data-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).