---
title: Wdrażanie modułów z witryny Azure portal — usługa Azure IoT Edge
description: Użyj centrum IoT Hub w witrynie Azure portal do wypychania modułu usługi IoT Edge z centrum IoT hub do urządzenia usługi IoT Edge, zgodnie z manifestem wdrażania.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271449"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdrażanie modułów usługi Azure IoT Edge z witryny Azure portal

Po utworzeniu modułów usługi IoT Edge z logiką biznesową, chcesz wdrożyć je na urządzeniach do pracy na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, można wdrożyć je wszystkie naraz i zadeklarować reguły routingu, które je łączą.

W tym artykule pokazano, jak portal Platformy Azure prowadzi użytkownika przez tworzenie manifestu wdrażania i wypychanie wdrożenia do urządzenia usługi IoT Edge. Aby uzyskać informacje dotyczące tworzenia wdrożenia przeznaczonego dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [Wdrażanie i monitorowanie modułów usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT w](../iot-hub/iot-hub-create-through-portal.md) subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-in-the-azure-portal) z zainstalowanym czasem wykonywania IoT Edge.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Aby uzyskać więcej informacji na temat działania manifestów wdrażania i sposobu ich tworzenia, zobacz Opis sposobu [użycia, skonfigurowania i ponownego użycia modułów usługi IoT Edge.](module-composition.md)

Portal Azure ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON. Ma trzy kroki: **Dodaj moduły,** **Określ trasy**i **Przejrzyj wdrożenie**.

### <a name="select-device-and-add-modules"></a>Wybierz urządzenie i dodaj moduły

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. W lewym okienku wybierz z menu pozycję **IoT Edge.**
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Na górnym pasku wybierz pozycję **Ustaw moduły**.
1. W sekcji **Ustawienia rejestru kontenerów** na stronie podaj poświadczenia, aby uzyskać dostęp do wszystkich rejestrów kontenerów prywatnych, które zawierają obrazy modułów.
1. W sekcji **Moduły krawędzi IoT** na stronie wybierz pozycję **Dodaj**.
1. Spójrz na typy modułów z menu rozwijanego:

   * **Moduł usługi IoT Edge** — należy podać nazwę modułu i identyfikator URI obrazu kontenera. Na przykład identyfikator URI obrazu dla przykładowego modułu SimulatedTemperatureSensor jest `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Jeśli obraz modułu jest przechowywany w rejestrze kontenerów prywatnych, dodaj poświadczenia na tej stronie, aby uzyskać dostęp do obrazu.
   * **Moduł portalu Marketplace** — moduły hostowane w portalu Azure Marketplace. Niektóre moduły portalu Marketplace wymagają dodatkowej konfiguracji, więc przejrzyj szczegóły modułu na liście [modułów usługi Azure Marketplace IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Moduł usługi Azure Stream Analytics** — moduły generowane z obciążenia usługi Azure Stream Analytics.

1. Po dodaniu modułu wybierz nazwę modułu z listy, aby otworzyć ustawienia modułu. W razie potrzeby wypełnij pola opcjonalne. Aby uzyskać więcej informacji na temat opcji tworzenia kontenera, zasad ponownego uruchamiania i żądanego stanu, zobacz [Polecenie EdgeAgent żądane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat bliźniaczej reprezentacji modułu, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).
1. W razie potrzeby powtórz kroki od 5 do 8, aby dodać dodatkowe moduły do wdrożenia.
1. Wybierz **dalej: Trasy,** aby przejść do sekcji tras.

### <a name="specify-routes"></a>Określanie tras

Na **trasy** kartę można zdefiniować, jak wiadomości są przekazywane między modułami i Centrum IoT Hub. Wiadomości są tworzone przy użyciu par nazwy/wartości. Domyślnie trasa jest nazywana **trasą** i definiowana jako **FROM\* /messages/ INTO $upstream**, co oznacza, że wszelkie komunikaty wysyłane przez wszystkie moduły są wysyłane do centrum IoT hub.  

Dodaj lub zaktualizuj trasy informacjami z [opcji Deklarowanie tras](module-composition.md#declare-routes), a następnie wybierz pozycję **Dalej: Przejrzyj + utwórz,** aby przejść do następnego kroku kreatora.

### <a name="review-deployment"></a>Przegląd wdrożenia

Sekcja przeglądu zawiera manifest wdrożenia JSON, który został utworzony na podstawie wybranych w poprzednich dwóch sekcjach. Należy pamiętać, że istnieją dwa moduły zadeklarowane, że nie dodać: **$edgeAgent** i **$edgeHub**. Te dwa moduły tworzą [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu możesz wyświetlić je wszystkie na stronie szczegółów urządzenia usługi IoT Hub. Na tej stronie wyświetlana jest nazwa każdego wdrożonego modułu, a także przydatne informacje, takie jak stan wdrożenia i kod zakończenia.

## <a name="deploy-modules-from-azure-marketplace"></a>Wdrażanie modułów z portalu Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane do pracy na platformie Azure, w tym [moduły Usługi IoT Edge.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)

Moduł usługi IoT Edge można wdrożyć w portalu Azure Marketplace i w centrum IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Wdrażanie z witryny Azure Marketplace

Zapoznaj się z modułami IoT Edge na rynku i po znalezieniu tego, który chcesz wdrożyć, wybierając **pozycję Utwórz** lub **Pobierz teraz**. Postępuj zgodnie z instrukcjami kreatora wdrażania, które mogą się różnić w zależności od wybranego modułu Usługi IoT Edge:

1. Potwierdź warunki użytkowania i politykę prywatności dostawcy, wybierając opcję **Kontynuuj**. Może być najpierw trzeba podać informacje kontaktowe.
1. Wybierz subskrypcję i Centrum IoT, do którego jest dołączone urządzenie docelowe.
1. Wybierz **pozycję Wdrażanie na urządzeniu**.
1. Wprowadź nazwę urządzenia lub wybierz **pozycję Znajdź urządzenie,** aby przeglądać między urządzeniami zarejestrowanymi w centrum.
1. Wybierz **pozycję Utwórz,** aby kontynuować standardowy proces konfigurowania manifestu wdrażania, w tym dodawanie innych modułów w razie potrzeby. Szczegóły dotyczące nowego modułu, takie jak identyfikator URI obrazu, opcje tworzenia i żądane właściwości są wstępnie zdefiniowane, ale można je zmienić.

Sprawdź, czy moduł został wdrożony w centrum IoT Hub w witrynie Azure portal. Wybierz urządzenie, wybierz **zestaw modułów,** a moduł powinien być wymieniony w sekcji **Moduły krawędzi IoT.**

### <a name="deploy-from-azure-iot-hub"></a>Wdrażanie z usługi Azure IoT Hub

Możesz szybko wdrożyć moduł z portalu Azure Marketplace na urządzeniu w centrum IoT w witrynie Azure portal.

1. W witrynie Azure portal przejdź do centrum IoT Hub.
1. W lewym okienku w obszarze **Automatyczne zarządzanie urządzeniami**wybierz pozycję **IoT Edge**.
1. Wybierz urządzenie usługi IoT Edge, które ma odbierać wdrożenie.
1. Na górnym pasku wybierz pozycję **Ustaw moduły**.
1. W sekcji **Moduły krawędzi IoT** kliknij pozycję **Dodaj**i wybierz z menu rozwijanego pozycję **Moduł portalu Marketplace.**

![Dodawanie modułu w Centrum IoT](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Wybierz moduł ze strony **portalu IoT Edge Module Marketplace.** Wybrany moduł jest automatycznie konfigurowany dla subskrypcji, grupy zasobów i urządzenia. Następnie pojawia się na liście modułów IoT Edge. Niektóre moduły mogą wymagać dodatkowej konfiguracji.

> [!TIP]
> Informacje o modułach usługi IoT Edge z usługi Azure IoT Hub są ograniczone. Możesz najpierw dowiedzieć się więcej o [modułach usługi IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) w portalu Azure Marketplace.

Wybierz **przycisk Dalej: Trasy** i kontynuuj wdrażanie zgodnie z [opisem określanie tras](#specify-routes) i [przeglądanie wdrożenia](#review-deployment) wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
