---
title: Wdrażanie modułów z poziomu Azure Portal-Azure IoT Edge | Microsoft Docs
description: Używanie Azure Portal do wdrażania modułów na urządzeniu IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4c2adc8ef0d426617dc85dd507907d612bbdabaa
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964918"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Wdróż moduły Azure IoT Edge z Azure Portal

Po utworzeniu modułów IoT Edge za pomocą logiki biznesowej należy wdrożyć je na urządzeniach, aby działały na brzegu. Jeśli masz wiele modułów, które współpracują ze sobą w celu zbierania i przetwarzania danych, możesz wdrożyć je wszystkie jednocześnie i zadeklarować reguły routingu, które je łączą.

W tym artykule pokazano, jak Azure Portal prowadzi użytkownika przez proces tworzenia manifestu wdrażania i wypychania wdrożenia na urządzenie IoT Edge. Aby uzyskać informacje na temat tworzenia wdrożenia, które jest przeznaczone dla wielu urządzeń na podstawie ich udostępnionych tagów, zobacz [wdrażanie i monitorowanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Wymagania wstępne

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.
* [Urządzenie IoT Edge](how-to-register-device.md#register-in-the-azure-portal) z zainstalowanym IoT Edge środowiska uruchomieniowego.

## <a name="select-your-device"></a>Wybierz urządzenie

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu.
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń.
1. Wybierz pozycję **Ustaw moduły**.

## <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrożenia

Manifest wdrożenia to dokument JSON, który opisuje moduły do wdrożenia, sposób przepływu danych między modułami i żądane właściwości modułu bliźniaczych reprezentacji. Aby uzyskać więcej informacji na temat działania manifestów wdrożenia i sposobu ich tworzenia, zobacz [Opis sposobu używania, konfigurowania i ponownego użycia modułów IoT Edge](module-composition.md).

Azure Portal zawiera kreatora, który przeprowadzi Cię przez proces tworzenia manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON. Składa się z trzech kroków: **Dodawanie modułów**, **Określanie tras**i **przeglądanie wdrożenia**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **ustawienia Container Registry** na stronie podaj poświadczenia, aby uzyskać dostęp do wszelkich prywatnych rejestrów kontenerów zawierających obrazy modułu.

1. W sekcji **moduły wdrażania** strony wybierz pozycję **Dodaj**.

1. Przyjrzyj się typom modułów z listy rozwijanej:

   * **IoT Edge module** — opcja domyślna.
   * **Azure Stream Analytics modułów tylko modułowych** wygenerowanych na podstawie obciążenia Azure Stream Analyticsowego.
   * **Azure Machine Learning** obrazów modeli opartych na module, które są generowane na podstawie Azure Machine Learning obszaru roboczego.

1. Wybierz **moduł IoT Edge**.

1. Podaj nazwę modułu, a następnie określ obraz kontenera. Na przykład:

   * **Nazwa** — SimulatedTemperatureSensor
   * **Identyfikator URI obrazu** — MCR.Microsoft.com/azureiotedge-Simulated-temperature-sensor:1.0

1. Wypełnij pola opcjonalne w razie potrzeby. Aby uzyskać więcej informacji o opcjach tworzenia kontenera, zasadach ponownego uruchamiania i żądanym stanie, zobacz [EdgeAgent wymagane właściwości](module-edgeagent-edgehub.md#edgeagent-desired-properties). Aby uzyskać więcej informacji na temat sznurka modułu, zobacz [Definiowanie lub aktualizowanie żądanych właściwości](module-composition.md#define-or-update-desired-properties).

1. Wybierz pozycję **Zapisz**.

1. Powtórz kroki 2-6, aby dodać kolejne moduły do wdrożenia.

1. Wybierz pozycję **dalej** , aby przejść do sekcji trasy.

### <a name="specify-routes"></a>Określ trasy

Domyślnie Kreator udostępnia trasę o nazwie **trasa** i zdefiniowana jako **z/* do $Upstream * *, co oznacza, że wszystkie komunikaty przesyłane przez wszystkie moduły są wysyłane do centrum IoT Hub.  

Dodaj lub zaktualizuj trasy z informacjami z [deklaracji trasy](module-composition.md#declare-routes), a następnie wybierz pozycję **dalej** , aby przejść do sekcji Przegląd.

### <a name="review-deployment"></a>Przegląd wdrożenia

Sekcja Przegląd przedstawia manifest wdrożenia JSON, który został utworzony na podstawie wybranych opcji w poprzednich dwóch sekcjach. Należy zauważyć, że istnieją dwa moduły zadeklarowane jako niedodane: **$edgeAgent** i **$edgeHub**. Te dwa moduły składają się na [IoT Edge środowisko uruchomieniowe](iot-edge-runtime.md) i są wymagane wartości domyślne w każdym wdrożeniu.

Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**.

## <a name="view-modules-on-your-device"></a>Wyświetlanie modułów na urządzeniu

Po wdrożeniu modułów na urządzeniu można wyświetlić wszystkie z nich na stronie **szczegóły urządzenia** w portalu. Ta strona zawiera nazwę każdego wdrożonego modułu, a także przydatne informacje, takie jak stan wdrożenia i kod zakończenia.

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

Dowiedz się [, jak wdrażać i monitorować moduły IoT Edge w odpowiedniej skali](how-to-deploy-monitor.md)
