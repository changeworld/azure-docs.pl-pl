---
title: Wdrażać moduły z witryny Azure portal — usługa Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby wdrożyć moduły na urządzeniu usługi IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: fab85b74fb53bec07843abf31aac1a8495d0fcfb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093985"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdrożyć moduły usługi Azure IoT Edge w witrynie Azure portal

Po utworzeniu usługi IoT Edge modułów za pomocą logiki biznesowej, należy wdrożyć je na urządzeniach do działania na urządzeniach brzegowych. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, możesz wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy. 

W tym artykule pokazano, jak witryna Azure portal przeprowadzi Cię przez tworzenie manifestu wdrażania, a następnie wypychanie wdrożenia na urządzeniu usługi IoT Edge. Aby uzyskać informacji o tworzeniu wdrożenia, który jest przeznaczony dla wielu urządzeń, na podstawie ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) w subskrypcji platformy Azure. 
* [Urządzenie usługi IoT Edge](how-to-register-device-portal.md) za pomocą zainstalowanego środowiska uruchomieniowego usługi IoT Edge. 

## <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.
2. Wybierz **usługi IoT Edge** z menu.
3. Kliknij identyfikator urządzenie docelowe z listy urządzeń. 
4. Wybierz pozycję **Ustaw moduły**.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości bliźniaczych reprezentacjach modułów. Aby uzyskać więcej informacji dotyczących sposobu manifesty wdrożenia pracy i jak je utworzyć, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

Witryna Azure portal ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrażania, zamiast ręcznego tworzenia dokumentu JSON. Posiada trzy kroki: **Dodaj moduły**, **określić trasy**, i **Przejrzyj wdrożenia**. 

### <a name="add-modules"></a>Dodaj moduły

1. W **ustawień rejestru** części strony, podaj poświadczenia, które mają dostęp do wszelkich rejestry kontenerów prywatnych, zawierających obrazy modułu. 
2. W **moduły wdrożeń** części strony wybierz **Dodaj**. 
3. Przyjrzyj się typów modułów z listy rozwijanej: 
   * **Moduł usługi IoT Edge** -opcją domyślną.
   * **Usługa Azure Stream Analytics modułu** — tylko moduły wygenerowane z obciążenia usługi Azure Stream Analytics. 
4. Wybierz **moduł usługi IoT Edge**.
5. Podaj nazwę dla modułu, a następnie wybierz obraz kontenera. Na przykład: 
   * **Nazwa** -tempSensor
   * **Identyfikator URI obrazu** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. Wypełnij pola opcjonalne, jeśli to konieczne. Więcej informacji na temat kontenerów opcje, zasady ponownego uruchamiania, tworzenia i zobacz żądany stan [EdgeAgent żądane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat bliźniaczą reprezentację modułu zobacz [Definiuj lub aktualizacji żądane właściwości](module-composition.md#define-or-update-desired-properties).
7. Wybierz pozycję **Zapisz**.
8. Powtórz kroki od 2 do 6, aby dodać dodatkowe moduły dla danego wdrożenia. 
9. Wybierz **dalej** aby przejść do sekcji trasy.

### <a name="specify-routes"></a>Określanie tras

Domyślnie Kreator umożliwia trasy o nazwie **trasy** i zdefiniowane jako **FROM /* do $nadrzędne **, oznacza to, że wszystkie komunikaty generowane przez moduły są wysyłane do usługi IoT hub.  

Dodawanie lub aktualizowanie tras przy użyciu informacji z [zadeklarować trasy](module-composition.md#declare-routes), a następnie wybierz **dalej** można przejść do sekcji przeglądu.

### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które możesz wdrożenia JSON manifestu, które utworzono na podstawie dokonanego wyboru w poprzednich sekcjach. Należy zauważyć, że istnieją dwa moduły zadeklarować, że nie zostały dodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się [środowisko uruchomieniowe usługi IoT Edge](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu. 

Przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania**. 

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu z systemem

Po wdrożeniu modułów na urządzeniu, możesz wyświetlać wszystkie z nich w **szczegóły urządzenia** strony portalu. Ta strona wyświetla nazwę każdego modułu wdrożone, a także przydatne informacje, takie jak kod stanu i zakończenia wdrożenia. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md)
