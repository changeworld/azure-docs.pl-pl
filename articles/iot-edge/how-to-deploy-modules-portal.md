---
title: Wdrażanie modułów z poziomu Azure Portal — Azure IoT Edge
description: Użyj IoT Hub w Azure Portal, aby wypchnąć moduł IoT Edge z IoT Hub do urządzenia IoT Edge zgodnie z konfiguracją manifestu wdrożenia.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7a3280e11d40a361c5a3305d71e58661b37b8bd1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563432"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdrożyć moduły usługi Azure IoT Edge w witrynie Azure portal

Po utworzeniu usługi IoT Edge modułów za pomocą logiki biznesowej, należy wdrożyć je na urządzeniach do działania na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, możesz wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy.

W tym artykule pokazano, jak witryna Azure portal przeprowadzi Cię przez tworzenie manifestu wdrażania, a następnie wypychanie wdrożenia na urządzeniu usługi IoT Edge. Aby uzyskać informacje na temat tworzenia wdrożenia, które jest przeznaczone dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.
* [Urządzenie usługi IoT Edge](how-to-register-device.md#register-in-the-azure-portal) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

Witryna Azure portal ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrażania, zamiast ręcznego tworzenia dokumentu JSON. Posiada trzy kroki: **Dodaj moduły**, **określić trasy**, i **Przejrzyj wdrożenia**.

### <a name="select-device-and-add-modules"></a>Wybierz urządzenie i Dodaj moduły

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
1. W lewym okienku wybierz **IoT Edge** z menu.
1. Kliknij identyfikator urządzenie docelowe z listy urządzeń.
1. Na górnym pasku wybierz pozycję **Ustaw moduły**.
1. W sekcji **ustawienia Container Registry** na stronie podaj poświadczenia, aby uzyskać dostęp do wszelkich prywatnych rejestrów kontenerów zawierających obrazy modułu.
1. W sekcji **IoT Edge modułów** na stronie wybierz pozycję **Dodaj**.
1. Przyjrzyj się typom modułów z menu rozwijanego:

   * **Moduł IoT Edge** — Podaj nazwę modułu i identyfikator URI obrazu kontenera. Na przykład identyfikator URI obrazu dla przykładowego modułu SimulatedTemperatureSensor jest `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Jeśli obraz modułu jest przechowywany w rejestrze kontenera prywatnego, Dodaj poświadczenia na tej stronie, aby uzyskać dostęp do tego obrazu. 
   * Moduły **portalu Marketplace** hostowane w witrynie Azure Marketplace. Niektóre moduły portalu Marketplace wymagają dodatkowej konfiguracji, dlatego Przejrzyj szczegóły modułu na liście [modułów IoT Edge portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * Moduły modułów **Azure Stream Analytics** wygenerowane na podstawie obciążenia Azure Stream Analyticsowego. 

1. Po dodaniu modułu wybierz z listy nazwę modułu, aby otworzyć ustawienia modułu. Wypełnij pola opcjonalne, jeśli to konieczne. Więcej informacji na temat kontenerów opcje, zasady ponownego uruchamiania, tworzenia i zobacz żądany stan [EdgeAgent żądane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat bliźniaczą reprezentację modułu zobacz [Definiuj lub aktualizacji żądane właściwości](module-composition.md#define-or-update-desired-properties).
1. W razie potrzeby powtórz kroki od 5 do 8, aby dodać kolejne moduły do wdrożenia.
1. Wybierz pozycję **Dalej: trasy** , aby przejść do sekcji trasy.

### <a name="specify-routes"></a>Określanie tras

Na karcie **trasy** należy określić sposób przekazywania komunikatów między modułami i IoT Hub. Komunikaty są konstruowane przy użyciu par nazwa/wartość. Domyślnie trasa jest nazywana **trasą** i zdefiniowana jako **z/messages/* na $Upstream * *, co oznacza, że wszystkie komunikaty przesyłane przez wszystkie moduły są wysyłane do centrum IoT Hub.  

Dodaj lub zaktualizuj trasy z informacjami z [deklaracji trasy](module-composition.md#declare-routes), a następnie wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do następnego kroku kreatora.

### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które możesz wdrożenia JSON manifestu, które utworzono na podstawie dokonanego wyboru w poprzednich sekcjach. Należy zauważyć, że istnieją dwa moduły zadeklarować, że nie zostały dodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz**.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu z systemem

Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich na stronie Szczegóły urządzenia w IoT Hub. Ta strona wyświetla nazwę każdego modułu wdrożone, a także przydatne informacje, takie jak kod stanu i zakończenia wdrożenia.

## <a name="deploy-modules-from-azure-marketplace"></a>Wdrażanie modułów z witryny Azure Marketplace

[Portal Azure Marketplace](https://azuremarketplace.microsoft.com/) to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane pod kątem działania na platformie Azure, w tym [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Moduł IoT Edge można wdrożyć z poziomu portalu Azure Marketplace i z IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Wdrażanie z poziomu portalu Azure Marketplace

Zapoznania moduły IoT Edge w portalu Marketplace i po znalezieniu, w którym chcesz ją wdrożyć, wybierz pozycję **Utwórz** lub **Pobierz teraz**. Postępuj zgodnie z krokami kreatora wdrażania, które mogą się różnić w zależności od wybranego modułu IoT Edge:

1. Potwierdź warunki użytkowania dostawcy i zasady ochrony prywatności, wybierając pozycję **Kontynuuj**. Konieczne może być podanie informacji kontaktowych.
1. Wybierz swoją subskrypcję i IoT Hub, do której jest dołączone urządzenie docelowe.
1. Wybierz pozycję **Wdróż na urządzeniu**.
1. Wprowadź nazwę urządzenia lub wybierz pozycję **Znajdź urządzenie** , aby przeglądać urządzenia zarejestrowane w centrum.
1. Wybierz pozycję **Utwórz** , aby kontynuować standardowy proces konfigurowania manifestu wdrożenia, łącznie z dodaniem innych modułów w razie potrzeby. Szczegóły dotyczące nowego modułu, takie jak identyfikator URI obrazu, opcje tworzenia i żądane właściwości, są wstępnie zdefiniowane, ale można je zmienić.

Sprawdź, czy moduł został wdrożony w IoT Hub w Azure Portal. Wybierz urządzenie, wybierz pozycję **Ustaw moduły** , a moduł powinien zostać wyświetlony w sekcji **IoT Edge moduły** .

### <a name="deploy-from-azure-iot-hub"></a>Wdrażanie z poziomu usługi Azure IoT Hub

Możesz szybko wdrożyć moduł z portalu Azure Marketplace na urządzeniu w IoT Hub w Azure Portal.

1. W Azure Portal przejdź do IoT Hub.
1. W okienku po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami**wybierz pozycję **IoT Edge**.
1. Wybierz urządzenie IoT Edge, na którym ma zostać odebrane wdrożenie.
1. Na górnym pasku wybierz pozycję **Ustaw moduły**.
1. W sekcji **IoT Edge modułów** kliknij pozycję **Dodaj**, a następnie wybierz pozycję **moduł Marketplace** z menu rozwijanego.

![Dodawanie modułu w IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Wybierz moduł na stronie **witryny Marketplace modułu IoT Edge** . Wybrany moduł jest automatycznie konfigurowany dla Twojej subskrypcji, grupy zasobów i urządzenia. Zostanie ona wyświetlona na liście modułów IoT Edge. Niektóre moduły mogą wymagać dodatkowej konfiguracji.

> [!TIP]
> Informacje o modułach IoT Edge z usługi Azure IoT Hub są ograniczone. Możesz najpierw dowiedzieć się więcej na temat [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) w portalu Azure Marketplace.

Wybierz kolejno pozycje **Dalej: trasy** i Kontynuuj wdrażanie zgodnie z opisem w temacie [Określanie tras](#specify-routes) i [przeglądanie wdrożenia](#review-deployment) wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
