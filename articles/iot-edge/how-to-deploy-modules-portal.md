---
title: Wdrażanie usługi Azure IoT krawędzi modułów (portal) | Dokumentacja firmy Microsoft
description: Użyj portalu Azure, aby wdrożyć moduły urządzenia IoT
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4082189d451f670c1ae3f76b8ec785d8bd0518b3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036487"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdrażanie modułów krawędzi IoT Azure w portalu Azure

Po utworzeniu krawędzi IoT modułów z logiki biznesowej, którą chcesz wdrożyć je na urządzeniach do działania na krawędzi. Jeśli masz wiele modułów, które współpracują ze sobą do zbierania i przetwarzania danych, można wdrożyć je w całości i zadeklarować reguły routingu, które łączą te elementy. 

W tym artykule opisano, jak portalu Azure prowadzi użytkownika przez proces tworzenia manifest wdrażania i wypychanie wdrażanie do urządzenia IoT. Informacji o tworzeniu wdrożenia, które jest przeznaczony dla wielu urządzeń według ich udostępnionego tagów, zobacz [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w Twojej subskrypcji platformy Azure. 
* [Urządzenia IoT](how-to-register-device-portal.md) ze środowiskiem uruchomieniowym krawędzi IoT zainstalowane. 

## <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [portalu Azure](https://portal.azure.com) i przejdź do Centrum IoT.
2. Wybierz **krawędzi IoT** z menu.
3. Kliknij identyfikator urządzenie docelowe z listy urządzeń. 
4. Wybierz pozycję **Ustaw moduły**.

## <a name="configure-a-deployment-manifest"></a>Skonfiguruj manifest wdrażania

Manifest rozmieszczenia jest dokumentem JSON, który opisuje jakie moduły do wdrożenia, jak dane przepływają między modułami i odpowiednie właściwości twins modułu. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia oraz sposób ich tworzenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

Azure portal ma kreatora, który przeprowadzi Cię przez proces tworzenia manifest wdrażania, zamiast ręcznego tworzenia dokumentu JSON. Składa się z trzech etapów: **Dodaj moduły**, **określić trasy**, i **Przejrzyj wdrożenia**. 

### <a name="add-modules"></a>Dodawanie modułów

1. W **ustawień rejestru** części strony, podaj poświadczenia, które mają dostęp do wszelkich rejestrów Kontener prywatny zawierających obrazy modułu. 
2. W **moduły wdrożeń** części strony, wybierz opcję **Dodaj**. 
3. Wybierz typ modułu z listy rozwijanej: 
   * **Moduł krawędzi IoT** -opcją domyślną.
   * **Azure Stream Analytics modułu** -tylko moduły wygenerowana przez to obciążenie usługi Azure Stream Analytics. 

4. Podaj nazwę dla modułu, a następnie określ obraz kontenera. Na przykład: 
   * **Nazwa** -tempSensor
   * **Identyfikator URI obrazu** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
5. Wypełnij pola opcjonalne, jeśli to konieczne. Więcej informacji na temat kontenera opcje, zasada ponownego uruchamiania, tworzenia i zobacz żądany stan [EdgeAgent żądanego właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat dwie modułu zobacz [Definiuj lub update konieczne właściwości](module-composition.md#define-or-update-desired-properties).
6. Wybierz pozycję **Zapisz**.
7. Powtórz kroki od 2 do 6, aby dodać dodatkowych modułów do wdrożenia. 
8. Wybierz **dalej** aby przejść do sekcji trasy.

### <a name="specify-routes"></a>Określ tras

Domyślnie Kreator pozwala trasa o nazwie **trasy** i zdefiniowane jako **FROM /* do górnej $**, co oznacza, że wszystkie komunikaty wyjściowe przez wszystkie moduły są wysyłane do Centrum IoT.  

Dodaj lub zaktualizuj trasy z informacjami z [zadeklarować tras](module-composition.md#declare-routes), a następnie wybierz pozycję **dalej** w dalszym ciągu w sekcji Przegląd.

### <a name="review-deployment"></a>Przegląd wdrożenia

Przejrzyj sekcję programy, które można wdrożenia JSON manifestu utworzonych na podstawie dokonanego wyboru w poprzednich sekcjach. Należy pamiętać, że istnieją dwa moduły zadeklarować, że nie zostały dodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły tworzą [środowiska uruchomieniowego krawędzi IoT](iot-edge-runtime.md) i są wymagane ustawienia domyślne w każdym wdrożeniu. 

Przejrzyj informacje dotyczące wdrażania, a następnie wybierz **przesyłania**. 

## <a name="view-modules-on-your-device"></a>Widok modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu, można wyświetlić wszystkie z nich w **szczegóły urządzenia** strony portalu. Ta strona wyświetla nazwę każdego modułu wdrożone, a także przydatne informacje, takie jak kod stanu i zakończenia wdrożenia. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę](how-to-deploy-monitor.md)
