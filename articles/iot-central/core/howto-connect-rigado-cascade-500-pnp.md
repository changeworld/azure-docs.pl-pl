---
title: Łączenie Rigado kaskadowego 500 na platformie Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak połączyć Rigado kaskadowe urządzenie bramy 500 z aplikacją IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: 8727de7b13b6ac036e714ac5aca7ae017200097c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951226"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Podłączanie Rigado kaskadowego urządzenia bramy 500 do aplikacji IoT Central platformy Azure


W tym artykule opisano, jak w ramach konstruktora rozwiązań można podłączyć urządzenie bramy Rigado Kaskada 500 do aplikacji IoT Central Microsoft Azure. 

## <a name="what-is-cascade-500"></a>Co to jest Kaskada 500?

Kaskada 500 IoT Gateway to Oferta sprzętowa z Rigado, która jest dołączana jako część rozwiązania do tworzenia kaskadowych rozwiązań jako usługi. Udostępnia ona komercyjne projekty IoT i zespoły produktów o elastycznej mocy obliczeniowej, niezawodne środowisko aplikacji w kontenerze oraz szeroką gamę opcji łączności z urządzeniami bezprzewodowymi, w tym Bluetooth 5, LTE, & Wi-Fi.

Kaskada 500 jest wstępnie certyfikowana dla usługi Azure IoT Plug and Play (PnP), co umożliwia naszym konstruktorom rozwiązań łatwe dołączanie urządzenia do kompleksowych rozwiązań. Brama kaskadowa umożliwia bezprzewodową łączenie się z różnymi czujnikami monitorowania warunków, które znajdują się w pobliżu urządzenia bramy. Czujniki te można dołączyć do IoT Central za pośrednictwem urządzenia bramy.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać czynności opisane w tym przewodniku, potrzebujesz następujących zasobów:

* Urządzenie kaskadowe Rigado 500. Aby uzyskać więcej informacji, odwiedź stronę [Rigado](https://www.rigado.com/).
* Aplikacja IoT Central platformy Azure utworzona na podstawie jednego z szablonów aplikacji w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Tworzenie nowej aplikacji](https://docs.microsoft.com/azure/iot-central/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

W celu dołączenia urządzenia bramy kaskadowej 500 do wystąpienia aplikacji platformy Azure IoT Central należy skonfigurować odpowiedni szablon urządzenia w aplikacji.

Aby dodać kaskadowy szablon urządzenia 500: 

1. Przejdź do karty ***Szablony urządzeń*** w lewym okienku, wybierz pozycję **+ Nowy**: ![utwórz nowy szablon urządzenia](./media/howto-connect-rigado-cascade500-pnp/device-template-new.png)
1. Na stronie można ***utworzyć szablon niestandardowy*** lub ***użyć wstępnie skonfigurowanego szablonu urządzenia***
1. Wybierz szablon urządzenia C500 z listy wstępnie skonfigurowanych szablonów urządzeń, jak pokazano poniżej: ![Wybieranie szablonu urządzenia C500](./media/howto-connect-rigado-cascade500-pnp/device-template-preconfigured.png)
1. Wybierz pozycję ***Dalej: Dostosuj*** , aby przejść do następnego kroku. 
1. Na następnym ekranie wybierz pozycję ***Utwórz*** , aby dołączyć szablon urządzenia C500 do aplikacji IoT Central.

## <a name="retrieve-application-connection-details"></a>Pobierz szczegóły połączenia z aplikacją

Teraz musisz pobrać **Identyfikator zakresu** i **klucz podstawowy** aplikacji IoT Central platformy Azure, aby połączyć urządzenie kaskadowe 500. 

1. W lewym okienku przejdź do obszarze **Administracja** , a następnie kliknij pozycję **połączenie z urządzeniem**. 
2. Zanotuj **Identyfikator zakresu** dla aplikacji IoT Central.
Identyfikator zakresu aplikacji ![](./media/howto-connect-rigado-cascade500-pnp/app-scope-id.png)
3. Teraz kliknij przycisk **Wyświetl klucze** i zanotuj **klucz podstawowy**
![klucz podstawowy](./media/howto-connect-rigado-cascade500-pnp/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Skontaktuj się z Rigado, aby połączyć bramę 

Aby podłączyć urządzenie kaskadowo 500 do aplikacji IoT Central, musisz skontaktować się z usługą Rigado i udostępnić je szczegółowym informacjom dotyczącym połączenia aplikacji z powyższych kroków. 

Gdy urządzenie jest połączone z Internetem, Rigado będzie w stanie wypchnąć aktualizację konfiguracji na urządzenie z bramą kaskadową 500 za pośrednictwem bezpiecznego kanału. 

Ta aktualizacja zastosuje IoT Central szczegóły połączenia na urządzeniu kaskadowym 500 i pojawi się na liście urządzeń. 

![Klucz podstawowy](./media/howto-connect-rigado-cascade500-pnp/devices-list-c500.png)  

Teraz możesz przystąpić do korzystania z urządzenia C500 w aplikacji IoT Central!

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć Rigado kaskadowo 500 z aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Tworzenie aplikacji do analizy w sklepie w](../retail/tutorial-in-store-analytics-create-app-pnp.md) celu utworzenia kompleksowego rozwiązania. 