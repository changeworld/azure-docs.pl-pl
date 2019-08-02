---
title: Informacje dotyczące Azure Security Center kosztów IoT | Microsoft Docs
description: Dowiedz się więcej o kosztach związanych z Azure Security Centerami dla IoT i sposobach ich kontrolowania.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 603df1def011232ad2120c37ad1ba256f2a30526
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596517"
---
# <a name="pricing-and-associated-costs"></a>Cennik i powiązane koszty

W tym artykule wyjaśniono Azure Security Center modelu cen usługi IoT, podsumowano wszystkie powiązane koszty i wyjaśniono, jak zarządzać nimi.

## <a name="pricing"></a>Cennik

Model cen usługi IoT Azure Security Center obejmuje dwie części, a opłaty są naliczane po [włączeniu](quickstart-onboard-iot-hub.md) IoT Hub w Azure Security Center dla IoT:

- Koszt według wbudowanych funkcji zabezpieczeń urządzeń opartych na analizie IoT Hub dzienników.

- Koszt dzięki ulepszonym funkcjom zabezpieczeń opartym na komunikatach zabezpieczeń z urządzeń IoT Edge lub liści.

  >[!Note]
  > Komunikaty o zabezpieczeniach również powodują użycie przydziału na IoT Hub.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Powiązane koszty

W przypadku Azure Security Center IoT istnieją dwa typy powiązanych kosztów, które nie są częścią cen bezpośrednich:

- Użycie limitu przydziału IoT Hub

- Log Analytics koszty magazynu

Aby zmniejszyć koszty związane z niektórymi funkcjami, można zmienić ustawienia.

Aby zmienić ustawienia:

1. Otwórz IoT Hub.

2. W obszarze **zabezpieczenia**kliknij pozycję **Przegląd**.

3. Kliknij pozycję **Ustawienia**.

Poniższa tabela zawiera podsumowanie powiązanych kosztów i implikacje poszczególnych opcji.

|     | Użycie | Komentarz |
| --- | --- | --- |
| **Użycie limitu przydziału IoT Hub** |  |
| [Eksportowanie zadania urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) (eksport z przędzą) | raz dziennie | Wyłącz _zbieranie metadanych_ z przędzy |
| **Magazyn Log Analytics** |  |
| Zalecenia i alerty dotyczące urządzeń| Zalecenia dotyczące zabezpieczeń i alerty wygenerowane przez usługę | Nieopcjonalne |
| Surowe dane zabezpieczeń| Surowe dane zabezpieczeń z urządzeń IoT zebranych przez agentów zabezpieczeń | Wyłącz _zdarzenia związane z zabezpieczeniami magazynu RAW_ |

>[!Important]
> Rezygnacja ma poważne konsekwencje dla dostępnych funkcji zabezpieczeń.
  
| Zrezygnuj | Implikacje |
| --- | --- |
| _Zbieranie metadanych z przędzy_ | Wyłącz [alerty niestandardowe](quickstart-create-custom-alerts.md) |
| | Wyłącz zalecenia dotyczące IoT Edge manifest |
| | Wyłączanie zaleceń i alertów opartych na tożsamości urządzenia |
| _Przechowuj zdarzenia dotyczące zabezpieczeń nieprzetworzonych urządzeń_ | Szczegółowe informacje o zaleceniach bazowych systemu operacyjnego urządzeń nie są dostępne |
| | Szczegóły dotyczące [](concept-security-alerts.md) postępowania z alertami i [zaleceniami](concept-recommendations.md) nie są dostępne |
|


## <a name="see-also"></a>Zobacz także

- Uzyskiwanie dostępu do [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Omówienie i eksplorowanie zaleceń dotyczących [zabezpieczeń](concept-recommendations.md)
- Poznawanie i eksplorowanie [alertów zabezpieczeń](concept-security-alerts.md)
