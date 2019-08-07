---
title: Wdrażać moduły z witryny Azure portal — usługa Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby wdrożyć moduły na urządzeniu usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f27484fd1d47a2e29aa5083a7d440e5c7dba11c1
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839648"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdrożyć moduły usługi Azure IoT Edge w witrynie Azure portal

Po utworzeniu usługi IoT Edge modułów za pomocą logiki biznesowej, należy wdrożyć je na urządzeniach do działania na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, możesz wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy.

W tym artykule pokazano, jak witryna Azure portal przeprowadzi Cię przez tworzenie manifestu wdrażania, a następnie wypychanie wdrożenia na urządzeniu usługi IoT Edge. Aby uzyskać informacji o tworzeniu wdrożenia, który jest przeznaczony dla wielu urządzeń, na podstawie ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure.
* [Urządzenie usługi IoT Edge](how-to-register-device-portal.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge.

## <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
1. Wybierz **usługi IoT Edge** z menu.
1. Kliknij identyfikator urządzenie docelowe z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

Witryna Azure portal ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrażania, zamiast ręcznego tworzenia dokumentu JSON. Składa się z trzech kroków: **Dodaj moduły**, **Określ trasy**i **Przejrzyj wdrożenie**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **ustawienia Container Registry** na stronie podaj poświadczenia, aby uzyskać dostęp do wszelkich prywatnych rejestrów kontenerów zawierających obrazy modułu.

1. W sekcji **moduły wdrażania** strony wybierz pozycję **Dodaj**.

1. Przyjrzyj się typów modułów z listy rozwijanej:

   * **Moduł usługi IoT Edge** -opcją domyślną.
   * **Usługa Azure Stream Analytics modułu** — tylko moduły wygenerowane z obciążenia usługi Azure Stream Analytics.
   * **Azure Machine Learning** obrazów modeli opartych na module, które są generowane na podstawie Azure Machine Learning obszaru roboczego.

1. Wybierz **moduł usługi IoT Edge**.

1. Podaj nazwę dla modułu, a następnie wybierz obraz kontenera. Przykład:

   * **Nazwa** — SimulatedTemperatureSensor
   * **Identyfikator URI obrazu** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Wypełnij pola opcjonalne, jeśli to konieczne. Więcej informacji na temat kontenerów opcje, zasady ponownego uruchamiania, tworzenia i zobacz żądany stan [EdgeAgent żądane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat bliźniaczą reprezentację modułu zobacz [Definiuj lub aktualizacji żądane właściwości](module-composition.md#define-or-update-desired-properties).

1. Wybierz pozycję **Zapisz**.

1. Powtórz kroki od 2 do 6, aby dodać dodatkowe moduły dla danego wdrożenia.

1. Wybierz **dalej** aby przejść do sekcji trasy.

### <a name="specify-routes"></a>Określanie tras

Domyślnie Kreator umożliwia trasy o nazwie **trasy** i zdefiniowane jako **FROM /* do $nadrzędne **, oznacza to, że wszystkie komunikaty generowane przez moduły są wysyłane do usługi IoT hub.  

Dodawanie lub aktualizowanie tras przy użyciu informacji z [zadeklarować trasy](module-composition.md#declare-routes), a następnie wybierz **dalej** można przejść do sekcji przeglądu.

### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które możesz wdrożenia JSON manifestu, które utworzono na podstawie dokonanego wyboru w poprzednich sekcjach. Należy zauważyć, że istnieją dwa moduły zadeklarować, że nie zostały dodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania**.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu z systemem

Po wdrożeniu modułów na urządzeniu, możesz wyświetlać wszystkie z nich w **szczegóły urządzenia** strony portalu. Ta strona wyświetla nazwę każdego modułu wdrożone, a także przydatne informacje, takie jak kod stanu i zakończenia wdrożenia.

## <a name="deploy-modules-from-azure-marketplace"></a>Wdrażanie modułów z witryny Azure Marketplace

Portal Azure Marketplace to rynek aplikacji i usług online, w którym można przeglądać szeroką gamę aplikacji i rozwiązań dla przedsiębiorstw, które są certyfikowane i zoptymalizowane pod kątem działania na platformie Azure, w tym [modułów IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Dostęp do portalu Azure Marketplace można także uzyskać za pomocą Azure Portal w obszarze **Tworzenie zasobu**.

Moduł IoT Edge można zainstalować z witryny Azure Marketplace lub Azure Portal:

1. Znajdź moduł i Rozpocznij proces wdrażania.

   * Azure Portal: Znajdź moduł i wybierz pozycję **Utwórz**.

   * Portal Azure Marketplace:

     1. Znajdź moduł i wybierz pozycję **Pobierz teraz**.
     1. Potwierdź warunki użytkowania dostawcy i zasady ochrony prywatności, wybierając pozycję **Kontynuuj**.

1. Wybierz swoją subskrypcję i IoT Hub, do której jest dołączone urządzenie docelowe.

1. Wybierz pozycję **Wdróż na urządzeniu**.

1. Wprowadź nazwę urządzenia lub wybierz pozycję **Znajdź urządzenie** , aby przeglądać urządzenia zarejestrowane w centrum.

1. Wybierz pozycję **Utwórz** , aby kontynuować standardowy proces konfigurowania manifestu wdrożenia, łącznie z dodaniem innych modułów w razie potrzeby. Szczegóły dotyczące nowego modułu, takie jak identyfikator URI obrazu, opcje tworzenia i żądane właściwości, są wstępnie zdefiniowane, ale można je zmienić.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
