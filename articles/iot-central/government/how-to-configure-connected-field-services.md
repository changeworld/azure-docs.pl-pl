---
title: Łączenie aplikacji Azure IoT Central z usługami terenowymi Dynamics 365 | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć kompleksowe rozwiązanie dzięki usłudze Azure IoT Central i Dynamics 365 Field Service
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157453"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Tworzenie kompleksowego rozwiązania dzięki usłudze Azure IoT Central i Dynamics 365 Field Service 



Jako konstruktor możesz włączyć integrację aplikacji Azure IoT Central z innymi systemami biznesowymi. 


Na przykład w rozwiązaniu do gospodarowania odpadami podłączonym można zoptymalizować wysyłkę śmieci do zbierania samochodów ciężarowych. Optymalizację można przeprowadzić na podstawie danych czujników IoT z podłączonych pojemników na odpady. W [aplikacji do zarządzania połączonymi odpadami W centrum IoT można](./tutorial-connected-waste-management.md) skonfigurować reguły i akcje oraz ustawić ją tak, aby wyzwalała tworzenie alertów w dynamics field service. Ten scenariusz jest realizowany przy użyciu usługi Microsoft Flow, który zostanie skonfigurowany bezpośrednio w Usłudze IoT Central do automatyzacji przepływów pracy w aplikacjach i usługach. Ponadto na podstawie działań usługi w field service, informacje mogą być wysyłane z powrotem do usługi Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Jak połączyć aplikację Azure IoT Central z usługami terenowymi Dynamics 365 

Poniższe procesy integracji można łatwo wdrożyć w oparciu o czystą konfigurację:
* Usługa Azure IoT Central może wysyłać informacje o anomaliach urządzeń do usługi Connected Field Service (jako alert IoT) w celu zdiagnozowania.
* Usługa connected field service może tworzyć przypadki lub zlecenia pracy wyzwalane z anomalii urządzeń.
* Connected Field Service może zaplanować techników do inspekcji, aby zapobiec wypadkom przestojów.
* Pulpit nawigacyjny urządzenia Usługi Azure IoT Central można zaktualizować o odpowiednie informacje o usłudze i harmonogramie.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [szablonach instytucji rządowych IoT](./overview-iot-central-government.md)
* Dowiedz się więcej o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Dowiedz się więcej o [usługach terenowych Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
