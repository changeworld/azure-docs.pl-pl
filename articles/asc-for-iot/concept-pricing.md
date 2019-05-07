---
title: Omówienie Centrum zabezpieczeń Azure dla IoT koszty (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Więcej informacji na temat kosztów związanych z Centrum zabezpieczeń Azure dla IoT i jak je kontrolować.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 40963e0d81ccff21cc625b799833b996547a13b6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198322"
---
# <a name="pricing-and-associated-costs"></a>Cennik i powiązane koszty

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano usługi Azure Security Center (ASC) dla modelu cen IoT, znajduje się podsumowanie wszystkich powiązanych z nimi kosztów i wyjaśniono, jak nimi zarządzać.

## <a name="pricing"></a>Cennik

ASC dla IoT, model cen składa się z dwóch części i jest naliczana, gdy usługa IoT Hub jest [włączone](quickstart-onboard-iot-hub.md) w ASC IoT:

- Koszt według urządzenia — funkcje wbudowane zabezpieczenia oparty na analizie dzienników usługi IoT Hub.

- Koszt według komunikatu — możliwości zwiększone zabezpieczenia oparty na zabezpieczenia komunikatów z urządzenia usługi IoT Edge lub typu liść.

  >[!Note]
  > Zabezpieczenia komunikatów również spowoduje naliczenie opłaty za użycie przydziału w Centrum IoT Hub.

Aby uzyskać więcej informacji, zobacz [cennik usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Powiązanych z nimi kosztów

ASC IoT udostępnia dwa typy powiązanych z nimi kosztów, które nie są częścią bezpośrednie ceny:

- Użycie limitu przydziału usługi IoT Hub

- Zaloguj się koszty usługi storage Analytics

Można jednocześnie obniżając jej koszty, rezygnując z niektórych funkcji, zmieniając ustawienia.

Aby zmienić swoje ustawienia:

1. Otwórz Centrum IoT Hub.

2. W obszarze **zabezpieczeń**, kliknij przycisk **Przegląd**.

3. Kliknij pozycję **Ustawienia**.

Poniższa tabela zawiera podsumowanie powiązanych z nimi kosztów i skutki każdej z nich.

|     | Sposób użycia | Komentarz |
| --- | --- | --- |
| **Użycie limitu przydziału usługi IoT Hub** |  |
| [Eksportowanie urządzeń](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) zadania (Eksportowanie bliźniaczej reprezentacji) | raz dziennie | Wyłącz _bliźniaczej reprezentacji w kolekcji metadanych_ |
| **Magazyn analizy dzienników** |  |
| Urządzenia zaleceń i alertów| Zalecenie dotyczące zabezpieczeń i alerty wygenerowane przez usługę | Nie jest opcjonalny |
| Dane pierwotne zabezpieczeń| Dane pierwotne zabezpieczeń z urządzeń IoT, zbierane przez agentów zabezpieczeń | Wyłącz _przechowywania zdarzeń zabezpieczeń urządzenia_ |

>[!Important]
> Zrezygnować ma poważny wpływ na dostępnych funkcji zabezpieczeń.
  
| Zrezygnuj | Implikacje |
| --- | --- |
| _Kolekcja metadanych bliźniaczych reprezentacji_ | Wyłącz [alerty niestandardowe](quickstart-create-custom-alerts.md) |
| | Wyłącz zalecenia manifestu usługi IoT Edge |
| | Wyłącz urządzenia na podstawie tożsamości zaleceń i alertów |
| _Zdarzenia zabezpieczeń urządzenie niesformatowane Store_ | Szczegółowe informacje na temat zaleceń dotyczących linii bazowej systemu operacyjnego urządzenia nie są dostępne |
| | Szczegółowe informacje na [alert](concept-security-alerts.md) i [zalecenie](concept-recommendations.md) badania nie są dostępne |


## <a name="see-also"></a>Zobacz także

- Dostęp do Twojego [danych pierwotnych zabezpieczeń](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Poznawanie i eksplorowanie [zalecenia dotyczące zabezpieczeń](concept-recommendations.md)
- Poznawanie i eksplorowanie [alerty zabezpieczeń](concept-security-alerts.md)
