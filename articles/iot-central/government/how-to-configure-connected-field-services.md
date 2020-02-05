---
title: Łączenie aplikacji IoT Central platformy Azure z usługami pól Dynamics 365 Microsoft Docs
description: Dowiedz się, jak utworzyć kompleksowe rozwiązanie dzięki usłudze Azure IoT Central i usłudze pola Dynamics 365
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ae266495db1d6b94a43aa962a3e9b63a8115c526
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017671"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Tworzenie kompleksowego rozwiązania przy użyciu usług Azure IoT Central i Dynamics 365 Field Service 



Jako Konstruktor można włączyć integrację aplikacji IoT Central platformy Azure z innymi systemami biznesowymi. 


Na przykład w rozwiązaniu do zarządzania odpadami można zoptymalizować wysyłanie ciężarówek z kolekcji kosz. Optymalizację można wykonać w oparciu o dane czujników IoT z połączonych pojemników. W [IoT Central połączonej aplikacji do zarządzania odpadami](./tutorial-connected-waste-management.md) można skonfigurować reguły i akcje, a następnie skonfigurować je do wyzwalania tworzenia alertów w usłudze pola Dynamics. Ten scenariusz jest realizowany za pomocą Microsoft Flow, który zostanie skonfigurowany bezpośrednio w IoT Central do automatyzacji przepływów pracy między aplikacjami i usługami. Ponadto w oparciu o działania usług w usłudze Field Information można wysłać z powrotem do usługi Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Jak połączyć aplikację IoT Central platformy Azure z usługami pól Dynamics 365 

Poniższe procesy integracji można łatwo zaimplementować na podstawie czystego środowiska konfiguracji:
* Usługa Azure IoT Central może wysyłać informacje o anomaliach urządzeń do usługi połączonych pól (jako alert IoT) na potrzeby diagnostyki.
* Usługa pole połączone może tworzyć przypadki lub zlecenia robocze wywoływane z anomalii urządzeń.
* Usługa pole połączone może zaplanować inspekcje, aby zapobiec zdarzeniom przestoju.
* Pulpit nawigacyjny urządzenia IoT Central platformy Azure można zaktualizować przy użyciu odpowiednich informacji dotyczących usługi i planowania.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [IoT Central szablonów dla instytucji rządowych](./overview-iot-central-government.md)
* Dowiedz się więcej o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Dowiedz się więcej o [usługach pól Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
