---
title: 'Szybki start: Włącz usługę'
description: Dowiedz się, jak włączyć i włączyć usługę zabezpieczeń Usługi IoT usługi Azure Security Center for IoT w centrum Azure IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310750"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Szybki start: wbudowana usługa Azure Security Center dla usługi IoT w centrum IoT Hub

Ten artykuł zawiera wyjaśnienie, jak włączyć usługę Azure Security Center dla usługi IoT w istniejącym centrum IoT Hub. Jeśli nie masz obecnie Centrum IoT, zobacz [Tworzenie centrum IoT Hub przy użyciu witryny Azure portal,](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) aby rozpocząć.

> [!NOTE]
> Usługa Azure Security Center for IoT obsługuje obecnie tylko centra IoT warstwy standardowej.

## <a name="prerequisites-for-enabling-the-service"></a>Wymagania wstępne dotyczące włączania usługi

- Obszar roboczy usługi Log Analytics
  - Dwa typy informacji są domyślnie przechowywane w obszarze roboczym usługi Log Analytics przez usługę Azure Security Center dla IoT; **alertów** i **zaleceń**dotyczących bezpieczeństwa .
  - Można dodać magazyn dodatkowego typu informacji, **zdarzenia nieprzetworzone**. Należy zauważyć, że przechowywanie **nieprzetworzonych zdarzeń** w usłudze Log Analytics wiąże się z dodatkowymi kosztami magazynowania.
- Centrum IoT Hub (warstwa standardowa)
- Spełnij wszystkie [wymagania wstępne usługi](service-prerequisites.md)

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Włączanie usługi Azure Security Center dla usługi IoT w centrum IoT Hub

Aby włączyć zabezpieczenia w centrum IoT Hub:

1. Otwórz centrum **IoT Hub** w witrynie Azure portal.
1. W menu **Zabezpieczenia** kliknij polecenie **Zabezpiecz rozwiązanie IoT**.

Gratulacje! Ukończono włączanie usługi Azure Security Center dla IoT w centrum IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Geolokalizacja i obsługa adresów IP

Aby zabezpieczyć rozwiązanie IoT, adresy IP połączeń przychodzących i wychodzących do i z urządzeń IoT, usługi IoT Edge i IoT Hub są zbierane i przechowywane domyślnie. Te informacje są niezbędne do wykrywania nieprawidłowej łączności z podejrzanych źródeł IP. Na przykład, gdy podejmowane są próby ustanowienia połączeń ze źródła IP znanego botnetu lub ze źródła IP poza geolokalizacją. Usługa Azure Security Center for IoT oferuje elastyczność umożliwiającą włączanie i wyłączanie zbierania danych adresów IP w dowolnym momencie.

Aby włączyć lub wyłączyć zbieranie danych adresów IP:

1. Otwórz centrum IoT Hub, a następnie wybierz **polecenie Omówienie** z menu **Zabezpieczenia.**
1. Wybierz ekran **Ustawienia** i zmodyfikuj ustawienia geolokalizacji i/lub obsługi adresów IP.

### <a name="log-analytics-creation"></a>Tworzenie usługi Log Analytics

Po włączeniu usługi Azure Security Center dla IoT tworzony jest domyślny obszar roboczy usługi Azure Log Analytics do przechowywania nieprzetworzonych zdarzeń zabezpieczeń, alertów i zaleceń dotyczących urządzeń IoT, usługi IoT Edge i Usługi IoT Hub. Każdego miesiąca pierwszych pięć (5) GB danych pojął na klienta do usługi Azure Log Analytics jest bezpłatny. Każdy GB danych przynajmowanych do obszaru roboczego usługi Azure Log Analytics jest zachowywany bezpłatnie przez pierwsze 31 dni. Dowiedz się więcej o cenach [usługi Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Aby zmienić konfigurację obszaru roboczego usługi Log Analytics:

1. Otwórz centrum IoT Hub, a następnie wybierz **polecenie Omówienie** z menu **Zabezpieczenia.**
1. Wybierz ekran **Ustawienia** i zmodyfikuj konfigurację obszaru roboczego ustawień usługi Log Analytics.

### <a name="customize-your-iot-security-solution"></a>Dostosowywanie rozwiązania zabezpieczającego IoT

Domyślnie włączenie rozwiązania Azure Security Center dla IoT automatycznie zabezpiecza wszystkie centra IoT w ramach subskrypcji platformy Azure.

Aby włączyć lub wyłączyć usługę Azure Security Center dla usługi IoT na określonym centrum IoT Hub:

1. Otwórz centrum IoT Hub, a następnie wybierz **polecenie Omówienie** z menu **Zabezpieczenia.**
1. Wybierz ekran **Ustawienia** i zmodyfikuj ustawienia zabezpieczeń dowolnego centrum IoT w ramach subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby skonfigurować rozwiązanie...

> [!div class="nextstepaction"]
> [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
