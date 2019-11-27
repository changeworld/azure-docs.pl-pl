---
title: Informacje o adresie IP Centrum IoT Hub | Microsoft Docs
description: Zapoznaj się z badaniem adresu IP Centrum IoT i jego właściwości. Adres IP Centrum IoT Hub może ulec zmianie w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalne przełączanie do trybu failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383916"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub adresy IP

Prefiksy adresów IP Centrum IoT Hub są publikowane okresowo w ramach [tagu usługi](../virtual-network/service-tags-overview.md)AzureIoTHub. Aby zapewnić prawidłowe działanie, urządzenia IoT muszą mieć łączność wychodzącą z prefiksami adresów wymienionymi w obszarze Tag usługi *AzureIoTHub* . Usługi aplikacji IoT muszą dodatkowo mieć łączność wychodzącą z prefiksami adresowymi wymienionymi w obszarze Tag usługi *EventHub* .


## <a name="best-practices"></a>Najlepsze praktyki

* Prefiksy adresów IP w usłudze IoT Hub mogą ulec zmianie. Te zmiany są okresowo publikowane za pośrednictwem tagów usługi przed rozpoczęciem działania. W związku z tym ważne jest, aby opracować procesy do regularnego pobierania i używania najnowszych tagów usługi. Ten proces można zautomatyzować za pomocą [interfejsu API odnajdywania tagów usług](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Użyj *AzureIoTHub. [ Nazwa regionu]* tag do identyfikowania prefiksów IP używanych przez punkty końcowe Centrum IoT Hub w określonym regionie. W przypadku odzyskiwania po awarii centrum danych lub [regionalnej pracy w trybie failover](iot-hub-ha-dr.md) upewnij się również, że połączenie z PREFIKSAMI adresów IP w regionie pary geograficznej IoT Hub jest włączone.


## <a name="support-for-ipv6"></a>Obsługa protokołu IPv6 

Protokół IPv6 nie jest obecnie obsługiwany w IoT Hub.