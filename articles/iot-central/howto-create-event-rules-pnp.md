---
title: Tworzenie reguł zdarzeń i zarządzanie nimi w aplikacji usługi Azure IoT Central | Microsoft Docs
description: Reguły zdarzeń IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym oraz automatyczne wywoływanie akcji, takich jak wysyłanie wiadomości e-mail, gdy reguła jest wyzwalana.
author: dominicbetts
ms.author: dobett
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1a7fc2b38e8354fb29255bb7f9d6b2c03ed53725
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879957"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application-preview-features"></a>Utwórz regułę zdarzenia i Skonfiguruj powiadomienia w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Za pomocą usługi Azure IoT Central można zdalnie monitorować połączone urządzenia. Reguły IoT Central platformy Azure umożliwiają monitorowanie urządzeń w czasie niemal rzeczywistym i automatyczne wywoływanie akcji, na przykład wysłanie wiadomości e-mail. Wystarczy kilka kliknięć, aby zdefiniować warunek monitorowania danych urządzenia i konfigurowania odpowiedniej akcji. W tym artykule wyjaśniono, jak utworzyć reguły monitorowania zdarzeń wysyłanych przez urządzenie.

Urządzenia mogą używać pomiaru zdarzeń do wysyłania ważnych lub informacyjnych zdarzeń urządzeń. Reguła zdarzenia wyzwala, gdy wybrane zdarzenie urządzenia jest zgłaszane przez urządzenie.

## <a name="create-an-event-rule"></a>Tworzenie reguły zdarzeń

Aby utworzyć regułę zdarzenia, szablon urządzenia musi mieć zdefiniowany co najmniej jeden pomiar zdarzenia. W tym przykładzie używane jest urządzenie z systemem chłodzenia w stanie chłodnym, które zgłasza zdarzenie błędu silnika wentylatorowego. Reguła monitoruje zdarzenie zgłoszone przez urządzenie i wysyła wiadomość e-mail za każdym razem, gdy zdarzenie jest zgłaszane.

1. Przejdź do strony **reguły** .

1. Jeśli nie utworzono jeszcze żadnych reguł, zobaczysz następujący ekran:

   ![Nie ma jeszcze reguł](media/howto-create-event-rules-pnp/rules-landing-page1.png)

1. Wybierz pozycję **+ Nowa reguła** , aby wyświetlić typy reguł, które możesz utworzyć.

1. Wybierz pozycję **zdarzenie** , aby utworzyć regułę monitorowania zdarzeń.

   ![Typy reguł](media/howto-create-event-rules-pnp/rule-types1.png)

1. Wprowadź nazwę, która pomoże zidentyfikować regułę i naciśnij klawisz ENTER.

1. Wybierz definicję urządzenia, do której ma zostać określony zakres, w sekcji **zakresy** . Na tym ekranie można również filtrować urządzenia, do których odnosi się reguła przy użyciu programu **+ Filter**. Reguła zostanie automatycznie zastosowana do wszystkich urządzeń objętych szablonem urządzenia. Aby wyłączyć regułę, wybierz przycisk **Wyłącz** w nagłówku.

### <a name="configure-the-rule-conditions"></a>Skonfiguruj warunki reguły

Warunek definiuje kryteria, które są monitorowane przez regułę.

1. Określ, czy chcesz **ustawić agregację** jako włączony, czy wyłączony.

   - Bez agregacji reguła wyzwala dla każdego punktu danych zdarzenia, który spełnia warunek. Na przykład, jeśli skonfigurujesz warunek reguły do wyzwalania, gdy wystąpi zdarzenie **błędu silnika wentylator** , reguła jest wyzwalana niemal natychmiast po zaraportowaniu tego zdarzenia przez urządzenie.
   - Jeśli **Liczba** jest używana jako funkcja agregująca, należy podać **wartość** i przedział **czasu** , w którym należy oszacować warunek. W takim przypadku liczba zdarzeń jest agregowana i reguła jest wyzwalana tylko wtedy, gdy zagregowana liczba zdarzeń dopasowuje wartość.

1. Wybierz zdarzenie, które chcesz monitorować, z listy rozwijanej **miara** . W tym przykładzie wybrano zdarzenie **błędu silnika wentylator** .

1. Opcjonalnie można również ustawić **liczbę** jako agregację i podać wartość, przy której reguła będzie wyzwalana.

     Jeśli na przykład chcesz otrzymywać alerty w przypadku wystąpienia więcej niż trzech zdarzeń dotyczących urządzeń w ciągu 5 minut, wybierz zdarzenie i ustaw funkcję agregującą jako **Count**, operator jako **większy niż**, a **wartość** 3. Ustaw **przedział czasu** jako **5 minut**. Reguła jest wyzwalana, gdy urządzenie zostanie wysłane więcej niż trzy zdarzenia w ciągu 5 minut. Częstotliwość oceny reguł jest taka sama jak przedział **czasu**, co oznacza, że w tym przykładzie reguła jest szacowana co 5 minut.

 ![Warunek](media/howto-create-event-rules-pnp/aggregate-condition-filled-out1.png)

> [!NOTE]
> W warunku można dodać więcej niż jeden pomiarzdarzenia. W przypadku określenia wielu warunków wszystkie warunki muszą być spełnione, aby reguła była wyzwalana. Każdy warunek jest przyłączony przez klauzulę "i" niejawnie. W przypadku korzystania z agregacji każdy pomiar musi być zagregowany.

### <a name="configure-actions"></a>Skonfiguruj akcje

W tej sekcji pokazano, jak skonfigurować akcje do wykonania, gdy reguła jest wyzwalana. Akcje są wywoływane, gdy wszystkie warunki określone w regule mają wartość true.

1. Kliknij **akcję +** w sekcjach **Akcja** . W tym miejscu zostanie wyświetlona lista dostępnych akcji.  

   ![Dodaj akcję](media/howto-create-event-rules-pnp/add-action1.png)

1. Wybierz akcję **e-mail** , wprowadź nazwę wyświetlaną dla akcji, prawidłowy adres e-mail w polu **do** i podaj notatkę, która będzie wyświetlana w treści wiadomości e-mail, gdy reguła jest wyzwalana.

   > [!NOTE]
   > Wiadomości e-mail są wysyłane tylko do użytkowników, którzy zostali dodani do aplikacji i zarejestrowali się co najmniej raz. Dowiedz się więcej na temat [zarządzania użytkownikami](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) w usłudze Azure IoT Central.

   ![Konfiguruj akcję](media/howto-create-event-rules-pnp/configure-action1.png)

1. Aby zapisać akcję, wybierz pozycję **gotowe**.

1. Aby zapisać regułę, wybierz pozycję **Zapisz**. Reguła przechodzi w ciągu kilku minut i zaczyna monitorować zdarzenia wysyłane do aplikacji. Gdy warunek określony w regule zostanie spełniony, reguła wyzwala skonfigurowaną akcję poczty e-mail.

## <a name="parameterize-the-rule"></a>Sparametryzuj regułę

Reguły mogą wyprowadzać pewne geograficznej z **Właściwości urządzenia** jako parametry. Używanie parametrów jest przydatne w scenariuszach, w których progi zdarzeń różnią się w zależności od różnych urządzeń. Podczas tworzenia reguły wybierz właściwość urządzenia, która określa próg, taki jak **Maksymalny idealny próg**, zamiast podać wartość bezwzględną, taką jak 3 zdarzenia. Gdy reguła jest wykonywana, dopasowuje zdarzenia urządzenia z wartością ustawioną we właściwości urządzenia.

Używanie parametrów jest efektywnym sposobem na zmniejszenie liczby reguł do zarządzania.

Akcje można również skonfigurować za pomocą **Właściwości urządzenia** jako parametru. Jeśli adres e-mail jest przechowywany jako właściwość urządzenia, można go użyć podczas definiowania adresu **do** .

## <a name="delete-a-rule"></a>Usuwanie reguły

Jeśli reguła nie jest już potrzebna, usuń ją, otwierając regułę i wybierając pozycję **Usuń**. Usunięcie reguły spowoduje usunięcie jej z szablonu urządzenia i wszystkich skojarzonych urządzeń.

## <a name="enable-or-disable-a-rule"></a>Włączanie lub wyłączanie reguły

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Przełącz przycisk **Włącz** lub **Wyłącz** w regule, aby włączyć lub wyłączyć regułę dla wszystkich urządzeń, które są w zakresie dla reguły.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Włączanie lub wyłączanie reguły dla urządzenia

Wybierz regułę, którą chcesz włączyć lub wyłączyć. Dodaj filtr w sekcji **zakresy** , aby dołączyć lub wykluczyć określone urządzenie w szablonie urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć reguły w aplikacji IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z [tematem zarządzanie urządzeniami](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .
