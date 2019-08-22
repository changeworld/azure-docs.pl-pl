---
title: Tworzenie reguł telemetrycznych w aplikacji IoT Central platformy Azure i zarządzanie nimi | Microsoft Docs
description: Reguły telemetrii IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym oraz automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail, gdy reguła jest wyzwalana.
author: ankitgupta
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d6deecf558105701be591c1f08923eca2c1e3bbd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879944"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Tworzenie reguły telemetrii i Konfigurowanie powiadomień w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Za pomocą usługi Azure IoT Central można zdalnie monitorować połączone urządzenia. Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym i automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail lub wyzwalacza Microsoft Flow. Wystarczy kilka kliknięć, aby zdefiniować warunek monitorowania danych urządzenia i konfigurowania odpowiedniej akcji. W tym artykule wyjaśniono, jak utworzyć reguły monitorowania danych telemetrycznych wysyłanych przez urządzenie.

Aby wysyłać dane liczbowe z urządzenia, urządzenia mogą używać pomiarów telemetrii. Reguła telemetrii wyzwala, gdy wybrane dane telemetryczne urządzenia przecinają określony próg.

## <a name="create-a-telemetry-rule"></a>Tworzenie reguły telemetrii

Aby można było utworzyć regułę telemetrii, w definicji urządzenia musi być zdefiniowana co najmniej jedna jednostka Telemetria. W tym przykładzie jest używane urządzenie maszyny z systemem chłodzenia w systemie, które wysyła dane telemetryczne temperatury i wilgotności. Reguła monitoruje temperaturę zgłoszoną przez urządzenie i wysyła wiadomość e-mail, gdy znajdzie się ona powyżej 80 stopni.

1. Przejdź do strony **reguły** .

1. Jeśli nie utworzono jeszcze żadnych reguł, zobaczysz następujący ekran:

    ![Nie ma jeszcze reguł](media/howto-create-telemetry-rules-pnp/rules-landing-page1.png)

1. Wybierz pozycję **+ Nowa reguła** , aby wyświetlić typy reguł, które możesz utworzyć.

1. Wybierz pozycję telemetrię, aby utworzyć regułę służącą do monitorowania danych telemetrycznych urządzenia.

    ![Typy reguł](media/howto-create-telemetry-rules-pnp/rule-types1.png)


1. Wprowadź nazwę, która pomoże zidentyfikować regułę i naciśnij klawisz ENTER.

1. Wybierz definicję urządzenia, do której chcesz określić zakres tej reguły, w sekcji zakresy. Na tym ekranie można również filtrować urządzenia, do których odnosi się reguła przy użyciu programu **+ Filter**. Reguła zostanie automatycznie zastosowana do wszystkich urządzeń objętych szablonem urządzenia. Aby wyłączyć regułę, wybierz przycisk **Wyłącz** w nagłówku.

### <a name="configure-the-rule-conditions"></a>Skonfiguruj warunki reguły

Warunek definiuje kryteria, które są monitorowane przez regułę.

1. Określ, czy chcesz **ustawić agregację** jako włączony, czy wyłączony.

      - Agregacja jest opcjonalna. Bez agregacji reguła wyzwala dla każdego punktu danych telemetrii, który spełnia warunek. Na przykład, jeśli reguła jest skonfigurowana do wyzwalania, gdy temperatura jest większa niż 80, reguła jest wyzwalana niemal natychmiast, gdy urządzenie zgłosi temperaturę > 80.
      - W przypadku wybrania funkcji agregującej, takiej jak Average, min, Max i Count, użytkownik musi podać **przedział czasu** , w którym należy oszacować warunek. Na przykład jeśli ustawisz okres jako "5 minut", a Twoja reguła szuka średniej temperatury powyżej 80, reguła jest wyzwalana, gdy średnia temperatura przekroczy 80 przez co najmniej 5 minut. Częstotliwość oceny reguł jest taka sama jak przedział **czasu**, co oznacza, że w tym przykładzie reguła jest szacowana co 5 minut.

1. Wybierz dane telemetryczne, które chcesz monitorować , z listy rozwijanej miara.

1. Następnie wybierz **operator** i podaj **wartość**.

     ![Warunek](media/howto-create-telemetry-rules-pnp/aggregate-condition-filled-out1.png)


>[!NOTE]
>Można dodać więcej niż jedną miarę telemetrii, wybierając pozycję **+ warunek**. W przypadku określenia wielu warunków wszystkie warunki muszą być spełnione, aby reguła była wyzwalana. Każdy warunek jest przyłączony przez klauzulę "i" niejawnie. W przypadku korzystania z agregacji każdy pomiar musi być zagregowany.

### <a name="configure-actions"></a>Skonfiguruj akcje

W tej sekcji pokazano, jak skonfigurować akcje do wykonania, gdy reguła jest wyzwalana. Akcje są wywoływane, gdy wszystkie warunki określone w regule mają wartość true.

1. Kliknij **akcję +** w sekcjach **Akcja** . W tym miejscu zostanie wyświetlona lista dostępnych akcji.  

    ![Dodaj akcję](media/howto-create-telemetry-rules-pnp/add-action1.png)


1. Wybierz akcję **e-mail** , wprowadź nazwę wyświetlaną dla akcji, prawidłowy adres e-mail w polu **do** i podaj notatkę, która będzie wyświetlana w treści wiadomości e-mail, gdy reguła jest wyzwalana.

    > [!NOTE]
    > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zarejestrowali się co najmniej raz. Dowiedz się więcej na temat [zarządzania użytkownikami](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) w usłudze Azure IoT Central.

   ![Konfiguruj akcję](media/howto-create-telemetry-rules-pnp/configure-action1.png)


1. Aby zapisać akcję, wybierz pozycję **gotowe**.

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła przechodzi na żywo w ciągu kilku minut i zacznie monitorować dane telemetryczne wysyłane do aplikacji. Gdy warunek określony w regule zostanie spełniony, reguła wyzwala skonfigurowaną akcję poczty e-mail.

## <a name="parameterize-the-rule"></a>Sparametryzuj regułę

Reguły mogą wyprowadzać pewne geograficznej z **Właściwości urządzenia** jako parametry. Używanie parametrów jest przydatne w scenariuszach, w których progi telemetrii różnią się w zależności od różnych urządzeń. Podczas tworzenia reguły wybierz właściwość urządzenia, która określa próg, taki jak **Maksymalny idealny próg**, zamiast podać wartość bezwzględną, na przykład 80 stopni. Gdy reguła jest wykonywana, dopasowuje dane telemetryczne urządzenia z wartością ustawioną we właściwości urządzenia.

Używanie parametrów jest efektywnym sposobem na zmniejszenie liczby reguł do zarządzania.

Akcje można również skonfigurować za pomocą **Właściwości urządzenia** jako parametru. Jeśli adres e-mail jest przechowywany jako właściwość, można go użyć podczas definiowania adresu **do** .

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając regułę i wybierając pozycję **Usuń**. Usunięcie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkich skojarzonych urządzeń.

## <a name="enable-or-disable-a-rule"></a>Włączanie lub wyłączanie reguły

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz przycisk **Włącz** lub **Wyłącz** w regule, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń objętych zakresem reguły.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włączanie lub wyłączanie reguły dla urządzenia

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Dodaj filtr w sekcji **zakresy** , aby dołączyć lub wykluczyć określone urządzenie w szablonie urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć reguły w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z [tematem zarządzanie urządzeniami](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .
