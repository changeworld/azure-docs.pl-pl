---
title: Dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central | Microsoft Docs
description: Jako operator możesz dodać rzeczywiste urządzenie do aplikacji usługi Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878173"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Samouczek: Dodawanie symulowanego urządzenia do aplikacji usługi Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak dodać i skonfigurować symulowane urządzenie do aplikacji IoT Central Microsoft Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowego symulowanego urządzenia
> * Używanie symulowanego urządzenia w środowisku kompilacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, Konstruktor powinien ukończyć pierwszy samouczek konstruktora, aby utworzyć aplikację usługi Azure IoT Central:

* [Definiowanie nowego typu urządzenia](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (wymagany)

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Aby dodać rzeczywiste urządzenie do aplikacji, należy użyć szablonu urządzenia **czujnika środowiska** utworzonego w samouczku [Definiowanie nowego typu urządzenia](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **urządzenia** w menu nawigacji po lewej stronie. Karta **urządzenia** przedstawia **wszystkie urządzenia** i szablon urządzenia **czujnika środowiska** .

1. Aby dodać symulowane urządzenie czujnika środowiska, wybierz pozycję **+ Nowy**. Użyj sugerowanego **identyfikatora urządzenia** lub wprowadź swój własny **Identyfikator**w postaci małych liter. Możesz również wprowadzić nazwę nowego urządzenia. Przełącz **symulowany** przełącznik na wartość **włączone** , a następnie wybierz pozycję **Utwórz**.

    ![Symulowane urządzenie](./media/tutorial-add-device-pnp/simulated-device.png)

Teraz można korzystać z widoków, które zostały utworzone przez konstruktora dla szablonu urządzenia przy użyciu symulowanych danych.

## <a name="use-a-simulated-device-to-improve-views"></a>Ulepszanie widoków przy użyciu symulowanego urządzenia

Po utworzeniu nowego symulowanego urządzenia, Konstruktor może używać tego urządzenia do dalszego ulepszania i kompilowania widoków dla szablonu urządzenia.

1. W widoku urządzenia Skopiuj **Identyfikator urządzenia** symulowanego urządzenia, które zostało utworzone.

1. Wybierz kartę **Szablony urządzeń** w menu nawigacji po lewej stronie i wybierz szablon **czujnika środowiska** .

1. Wybierz dowolne z widoków, które chcesz edytować, lub Utwórz nowy widok. Kliknij pozycję **Konfiguruj Podgląd urządzenia**. W tym miejscu możesz wybrać opcję braku urządzenia do wyświetlania wersji zapoznawczej przy użyciu rzeczywistego urządzenia, które można skonfigurować do testowania, lub z istniejącego urządzenia dodanego do IoT Central.

1. Wybierz **opcję Wybierz z uruchomionego urządzenia** i wprowadź **Identyfikator urządzenia** symulowanego urządzenia, które zostały skopiowane.

1. Wybierz pozycję **Zastosuj**. Teraz można zobaczyć to samo symulowane urządzenie w widokach szablonów urządzeń. Ten widok jest szczególnie przydatny w przypadku wykresów i innych wizualizacji.

    ![Konfigurowanie urządzenia w wersji zapoznawczej](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Dodawanie nowego symulowanego urządzenia
* Używanie symulowanego urządzenia w środowisku kompilacji

Teraz, gdy urządzenie symulowane zostało połączone z aplikacją IoT Central platformy Azure, Oto kilka sugerowanych następnych kroków.

Jako operator możesz dowiedzieć się, jak wykonywać następujące działania:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Jako deweloper urządzenia możesz dowiedzieć się, jak wykonywać następujące działania:

> [!div class="nextstepaction"]
> [Łączenie urządzenia zestawu deweloperskiego IoT DevKit z aplikacją usługi Azure IoT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



