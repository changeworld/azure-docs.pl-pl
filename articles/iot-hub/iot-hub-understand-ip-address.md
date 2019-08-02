---
title: Informacje o adresie IP Centrum IoT Hub | Microsoft Docs
description: Zapoznaj się z badaniem adresu IP Centrum IoT i jego właściwości. Adres IP Centrum IoT Hub może ulec zmianie w niektórych scenariuszach, takich jak odzyskiwanie po awarii lub regionalne przełączanie do trybu failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 9c9d02eeead4ccbed372e9a55d9b551da83caa39
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642295"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Informacje o adresie IP Centrum IoT Hub

Adres IP Centrum IoT Hub to ogólnodostępny punkt końcowy o zrównoważonym obciążeniu dla Twojego centrum, a nie dedykowany adres IP. Adres jest określany przez zakres adresów sieciowych w regionie świadczenia usługi Azure, w którym jest wdrożony. Ten adres IP może ulec zmianie bez powiadomienia. Aktualizacje sieci centrum danych, odzyskiwanie po awarii centrum danych lub regionalne przełączanie w tryb failover Centrum IoT mogą zmienić adres IP Centrum IoT. Zobacz [IoT Hub wysokiej dostępności i odzyskiwania po awarii,](iot-hub-ha-dr.md) Aby uzyskać więcej szczegółów na temat platformy Azure IoT Hub regionalnej pracy w trybie failover i dostępności.

Adres IP Centrum IoT zostanie zmieniony po przejściu w tryb failover do innego regionu. Możesz przetestować tę funkcję, wykonując czynności opisane w samouczku [wykonywanie ręcznego przełączania do trybu failover Centrum IoT Hub](tutorial-manual-failover.md).

## <a name="discover-your-iot-hub-ip-address"></a>Odnajdź adres IP Centrum IoT Hub

Adres IP IoT Hub można odnaleźć przy użyciu odwrotnego wyszukiwania DNS w rekordzie CNAME ([*IoT-Hub-Name*]. Azure-Devices.NET). Aby sprawdzić adres IP wystąpienia Centrum IoT Hub, można użyć **polecenia nslookup** :

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Ten adres IP może się zmieniać bez powiadomienia. W przypadku scenariusza odzyskiwania w trybie failover lub awarii należy wykonać sondowanie adresu IP Centrum IoT Hub w regionie pomocniczym.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Reguły zapory dla ruchu wychodzącego dla usługi IoT Hub

Spróbuj utworzyć reguły zapory i filtrowanie na podstawie nazwy lub domeny hosta Centrum IoT Hub. Jeśli można zezwolić tylko na ruch wychodzący do określonych adresów, należy regularnie sondować adres IP Centrum IoT i aktualizować reguły zapory.

