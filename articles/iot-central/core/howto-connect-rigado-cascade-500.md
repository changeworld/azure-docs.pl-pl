---
title: Łączenie kaskady Rigado Cascade 500 w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak podłączyć urządzenie bramy Rigado Cascade 500 do aplikacji IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: d52366684d772f91b53a1ab385b51ae4f11f0a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158380"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Podłącz urządzenie bramy Rigado Cascade 500 do aplikacji Azure IoT Central


W tym artykule opisano, jak jako konstruktor rozwiązań można połączyć urządzenie bramy Rigado Cascade 500 z aplikacją Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>Co to jest Cascade 500?

Cascade 500 IoT gateway to oferta sprzętowa rigado, która jest częścią ich rozwiązania Cascade Edge-as-a-Service. Zapewnia komercyjne zespoły projektowe IoT i produkty z elastyczną mocą obliczeniową krawędzi, niezawodnym środowiskiem aplikacji konteneryzowanym i szeroką gamą opcji łączności urządzeń bezprzewodowych, w tym Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 jest wstępnie certyfikowany dla platformy Azure IoT Plug and Play (wersja zapoznawcza), dzięki czemu nasi konstruktorzy rozwiązań mogą łatwo dołączać urządzenie do swoich kompleksowych rozwiązań. Brama Kaskadowa umożliwia bezprzewodowe łączenie się z różnymi czujnikami monitorowania stanu, które znajdują się w pobliżu urządzenia bramy. Czujniki te można włączyć do IoT Central za pośrednictwem urządzenia bramy.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz następujących zasobów:

* Urządzenie Rigado Cascade 500. Aby uzyskać więcej informacji, odwiedź [Stronę Rigado](https://www.rigado.com/).
* Aplikacja Azure IoT Central. Aby uzyskać więcej informacji, zobacz [tworzenie nowej aplikacji](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

Aby na pokładzie urządzenia bramy Kaskada 500 do wystąpienia aplikacji usługi Azure IoT Central, należy skonfigurować odpowiedni szablon urządzenia w aplikacji.

Aby dodać szablon urządzenia Kaskada 500: 

1. Przejdź do karty ***Szablony urządzeń*** w lewym okienku, wybierz + **Nowy**: ![Tworzenie nowego szablonu urządzenia](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Strona umożliwia ***utworzenie szablonu niestandardowego*** lub ***użycie wstępnie skonfigurowanego szablonu urządzenia***
1. Wybierz szablon urządzenia C500 z listy wstępnie skonfigurowanych szablonów ![urządzeń, jak pokazano poniżej: Wybierz szablon urządzenia C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Wybierz ***dalej: Dostosuj,*** aby przejść do następnego kroku. 
1. Na następnym ekranie wybierz pozycję ***Utwórz,*** aby pokłować szablon urządzenia C500 do aplikacji IoT Central.

## <a name="retrieve-application-connection-details"></a>Pobieranie szczegółów połączenia aplikacji

Teraz trzeba będzie pobrać **identyfikator zakresu** i **klucz podstawowy** dla aplikacji Azure IoT Central, aby połączyć urządzenie Cascade 500. 

1. Przejdź do **pozycji Administracja** w lewym okienku i kliknij pozycję **Połączenie urządzenia**. 
2. Zanotuj **identyfikator zakresu** aplikacji IoT Central.
![Identyfikator zakresu aplikacji](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Teraz kliknij **przycisk Wyświetl klawisze** i zanotuj **klucz**
![podstawowy](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Skontaktuj się z Rigado, aby podłączyć bramę 

Aby podłączyć urządzenie Cascade 500 do aplikacji IoT Central, należy skontaktować się z Rigado i podać szczegóły połączenia aplikacji z powyższych kroków. 

Gdy urządzenie jest podłączone do Internetu, Rigado będzie mógł przesunąć aktualizację konfiguracji do urządzenia bramy Cascade 500 za pośrednictwem bezpiecznego kanału. 

Ta aktualizacja zastosuje szczegóły połączenia IoT Central na urządzeniu Kaskadowym 500 i pojawi się na liście urządzeń. 

![Klucz podstawowy](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Teraz możesz używać urządzenia C500 w aplikacji IoT Central!

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak połączyć Rigado Cascade 500 z aplikacją Azure IoT Central, sugerowanym następnym krokiem jest dowiedzieć się, jak [utworzyć aplikację analityczną w sklepie,](../retail/tutorial-in-store-analytics-create-app-pnp.md) aby utworzyć kompleksowe rozwiązanie. 