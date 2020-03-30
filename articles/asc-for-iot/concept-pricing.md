---
title: Poznaj usługę Azure Security Center pod kątem kosztów IoT| Dokumenty firmy Microsoft
description: Dowiedz się więcej o kosztach związanych z usługą Azure Security Center dla IoT i o tym, jak nimi kontrolować.
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
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71348519"
---
# <a name="pricing-and-associated-costs"></a>Cennik i powiązane koszty

W tym artykule opisano model cenowy usługi Azure Security Center for IoT, podsumowuje wszystkie związane z tym koszty i wyjaśniono, jak nimi zarządzać.

## <a name="pricing"></a>Cennik

Model cenowy Usługi Azure Security Center for IoT składa się z dwóch części i jest rozliczany po włączeniu usługi IoT Hub w [usłudze](quickstart-onboard-iot-hub.md) Azure Security Center dla IoT:

- Koszt po urządzeniu — wbudowane funkcje zabezpieczeń oparte na analizie dzienników usługi IoT Hub.

- Koszt według wiadomości — ulepszone funkcje zabezpieczeń oparte na komunikatach zabezpieczających z usługi IoT Edge lub urządzeń typu liść.


Aby uzyskać więcej informacji, zobacz [Ustalanie cen w centrum zabezpieczeń](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Koszty powiązane

Usługa Azure Security Center for IoT ma powiązane koszty, które nie są częścią cen bezpośrednich:


- Koszty przechowywania usługi Log Analytics

Koszty związane z tym można zmniejszyć, rezygnując z niektórych funkcji rozwiązania. Zrezygnuj, zmieniając ustawienia.

Aby zmienić ustawienia:

1. Otwórz Centrum IoT.

2. W obszarze **Zabezpieczenia**kliknij pozycję **Przegląd**.

3. Kliknij przycisk **Ustawienia**.

Poniższa tabela zawiera podsumowanie powiązanych kosztów i implikacji każdej opcji.

|     | Sposób użycia | Komentarz |
| --- | --- | --- |
| **Magazyn usługi Log Analytics** |  |
| Zalecenia dotyczące urządzeń i alerty| Zalecenia dotyczące zabezpieczeń i alerty generowane przez usługę | Nie opcjonalne |
| Surowe dane zabezpieczające| Surowe dane zabezpieczające z urządzeń IoT, zbierane przez agentów zabezpieczeń | Wyłączanie _zdarzeń zabezpieczeń nieprzetworzonych urządzeń_ |
|

>[!Important]
> Rezygnacja ma poważne konsekwencje dla dostępności funkcji zabezpieczeń Usługi Azure Security Center dla IoT. 
  
| Zrezygnować | Implikacje |
| --- | --- |
| _Podwójna kolekcja metadanych_ | Wyłączanie [alertów niestandardowych](quickstart-create-custom-alerts.md) |
| | Wyłączanie zaleceń manifestu ioT Edge |
| | Wyłączanie zaleceń i alertów opartych na tożsamości urządzenia |
| _Przechowywanie nieprzetworzonych zdarzeń zabezpieczeń urządzeń_ | Szczegóły dotyczące zaleceń dotyczących planu bazowego systemu operacyjnego urządzenia nie są dostępne |
| | Szczegóły dotyczące dochodzeń w sprawie [wpisów](concept-security-alerts.md) i [rekomendacji](concept-recommendations.md) nie są dostępne |
|


## <a name="see-also"></a>Zobacz też

- Dostęp do [nieprzetworzonych danych zabezpieczających](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Zrozumienie i eksplorowanie [zaleceń dotyczących zabezpieczeń](concept-recommendations.md)
- Opis i eksplorowanie [alertów zabezpieczeń](concept-security-alerts.md)
