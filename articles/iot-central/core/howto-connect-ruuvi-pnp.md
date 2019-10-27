---
title: Łączenie RuuviTag na platformie Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak połączyć czujnik środowiska RuuviTag z aplikacją IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: 0a93bedb697a0d5a514fddab55c79b969b2bd77b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954229"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Łączenie czujnika RuuviTag z aplikacją IoT Central platformy Azure

W tym artykule opisano sposób, w jaki Konstruktor rozwiązań umożliwia połączenie czujnika RuuviTag z aplikacją IoT Central Microsoft Azure.

Co to jest tag ruuvi?

RuuviTag to zaawansowana platforma typu "Open Source sensor" służąca do zaspokajania potrzeb klientów, deweloperów, twórców, uczniów i może nawet być używana w domu i w ramach własnych przedsięwzięciachów. Urządzenie jest skonfigurowane tak, aby działało zaraz po jego użyciu i będzie gotowe do wdrożenia w miejscu, w którym będzie potrzebne. Jest to sygnał Bluetooth LE z czujnikiem środowiska i funkcją przyspieszeniomierza wbudowaną w. 

RuuviTag komunikuje się za pośrednictwem sieci (niewielka łączność Bluetooth) i w związku z tym wymaga, aby urządzenie bramy komunikować się z usługą Azure IoT Central. Upewnij się, że masz urządzenie bramy, takie jak Rigado Kaskada 500, Instalator będzie mógł połączyć RuuviTag z IoT Central. 

Postępuj zgodnie z [instrukcjami w tym miejscu](./howto-connect-rigado-cascade-500-pnp.md) , jeśli chcesz skonfigurować urządzenie bramy Rigado z kaskadą 500.

## <a name="prerequisites"></a>Wymagania wstępne
Aby połączyć czujniki RuuviTag, potrzebne są następujące zasoby:

* Czujnik RuuviTag. Aby uzyskać więcej informacji, odwiedź stronę [RuuviTag](https://ruuvi.com/). 
* Urządzenie Rigado kaskadowo 500 lub inną bramę w sieci. Aby uzyskać więcej informacji, odwiedź stronę [Rigado](https://www.rigado.com/).
* Aplikacja IoT Central platformy Azure utworzona na podstawie jednego z szablonów aplikacji w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej aplikacji](https://docs.microsoft.com/azure/iot-central/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="add-a-ruuvitag-device-template"></a>Dodawanie szablonu urządzenia RuuviTag

W celu dołączenia czujnika RuuviTag do wystąpienia aplikacji platformy Azure IoT Central należy skonfigurować odpowiedni szablon urządzenia w aplikacji.

Aby dodać szablon urządzenia RuuviTag: 

1. Przejdź do karty ***Szablony urządzeń*** w lewym okienku, wybierz pozycję **+ Nowy**: ![utwórz nowy szablon urządzenia](./media/howto-connect-ruuvi/devicetemplate-new.png) strona umożliwia ***utworzenie szablonu niestandardowego*** lub ***użycie wstępnie skonfigurowanego szablonu urządzenia***
1. Wybierz szablon urządzenia RuuviTag z listy wstępnie skonfigurowanych szablonów urządzeń, jak pokazano poniżej: ![Wybieranie szablonu urządzenia RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Wybierz pozycję ***Dalej: Dostosuj*** , aby przejść do następnego kroku. 
1. Na następnym ekranie wybierz pozycję ***Utwórz*** , aby dołączyć szablon urządzenia C500 do aplikacji IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Łączenie czujnika RuuviTag

Jak wspomniano powyżej, aby połączyć RuuviTag z aplikacją IoT Central, należy skonfigurować urządzenie bramy. W poniższych krokach przyjęto założenie, że skonfigurowano urządzenie bramy Rigado kaskadowo 500.  

1. Włącz urządzenie kaskadowe Rigado 500 i połącz je z połączeniem sieciowym (za pośrednictwem sieci Ethernet lub sieci bezprzewodowej)
1. Pokaż pozostałą część RuuviTag i pociągnij kartę plastikową, aby zabezpieczyć połączenie z baterią. 
1. Umieść RuuviTag w pobliżu Rigado kaskadowego 500 bramy, która została wcześniej skonfigurowana za pomocą aplikacji IoT Central. 
1. W ciągu zaledwie kilku sekund RuuviTag powinien pojawić się na liście urządzeń w IoT Central.  
![listę urządzeń RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png) możesz teraz używać tego RuuviTag w aplikacji IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Tworzenie symulowanych RuuviTag
Jeśli nie masz RuuviTag fizycznego, możesz utworzyć symulowaną sensor RuuviTag, który będzie używany do testowania w aplikacji IoT Central platformy Azure.

Aby utworzyć symulowaną RuuviTag:

1. Wybierz pozycję **urządzenia > RuuviTag**. 
1. Wybierz pozycję **+ Nowy**. 
1. Określ unikatowy **Identyfikator urządzenia** i przyjazną **nazwę urządzenia**.  
1. Włącz ustawienie **symulowane** .
1. Wybierz pozycję **Utwórz**.  

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć RuuviTag z aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Dostosowywanie aplikacji IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) w celu utworzenia kompleksowego rozwiązania. 