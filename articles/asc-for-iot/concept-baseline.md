---
title: Kontrole według planu bazowego i niestandardowe
description: Dowiedz się więcej o koncepcji usługi Azure Security Center dla linii bazowej IoT.
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
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: c52a3e55e3801eaaac885b9a3c364283f74906ba
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311655"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>Usługa Azure Security Center dla planu bazowego IoT i kontrole niestandardowe

W tym artykule opisano usługę Azure Security Center for IoT baseline i podsumowuje wszystkie skojarzone właściwości kontroli niestandardowych według planu bazowego.

## <a name="baseline"></a>Punkt odniesienia

Linia bazowa ustanawia standardowe zachowanie dla każdego urządzenia i ułatwia ustanawianie nietypowego zachowania lub odchylenia od oczekiwanych norm.

## <a name="baseline-custom-checks"></a>Kontrole niestandardowe według planu bazowego

Kontrole niestandardowe planu bazowego ustanawiają niestandardową listę kontroli dla każdego planu bazowego urządzenia przy użyciu **bliźniaczej reprezentacji tożsamości modułu** urządzenia.

## <a name="setting-baseline-properties"></a>Ustawianie właściwości linii bazowej

1. W centrum IoT Hub znajdź i wybierz urządzenie, które chcesz zmienić.
1. Kliknij na urządzeniu, a następnie kliknij moduł **azureiotsecurity.**
1. Kliknij **pozycję Bliźniacza tożsamości modułu**.
1. Przekaż plik **niestandardowych kontroli linii bazowej** na urządzenie.
1. Dodaj właściwości planu bazowego do modułu zabezpieczeń i kliknij przycisk **Zapisz**.

### <a name="baseline-custom-check-file-example"></a>Przykład niestandardowego pliku wyboru planu bazowego

Aby skonfigurować niestandardowe kontrole według planu bazowego:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Właściwości sprawdzania niestandardowego planu bazowego

| Nazwa| Stan | Prawidłowe wartości| Wartości domyślne| Opis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|linia bazowaCustomChecksEnabled|Wymagane: prawda |Prawidłowe wartości: **wartość logiczna** |Wartość domyślna: **false** |Maksymalny przedział czasu przed wysłaniem wiadomości o wysokim priorytecie.|
|ścieżka linii bazowejCustomChecksFilePath |Wymagane: prawda|Prawidłowe wartości: **Ciąg ,** **null** |Wartość domyślna: **null** |Pełna ścieżka podstawowej konfiguracji xml|
|linia bazowaCustomChecksFileHash |Wymagane: prawda|Prawidłowe wartości: **Ciąg ,** **null** |Wartość domyślna: **null** |`sha256sum`pliku konfiguracyjnego xml. Użyj [sha256sum odniesienia](https://linux.die.net/man/1/sha256sum) dla dodatkowych informacji. |

Aby przejrzeć dodatkowe przykłady planu bazowego, zobacz [przykład niestandardowej linii bazowej -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) i [przykład niestandardowej linii bazowej -2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Następne kroki

- Dostęp do [nieprzetworzonych danych zabezpieczających](how-to-security-data-access.md)
- [Badanie urządzenia](how-to-investigate-device.md)
- Zrozumienie i eksplorowanie [zaleceń dotyczących zabezpieczeń](concept-recommendations.md)
- Opis i eksplorowanie [alertów zabezpieczeń](concept-security-alerts.md)
