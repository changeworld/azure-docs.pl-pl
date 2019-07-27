---
title: Rozwiązywanie problemów z zestawem SDK urządzeń mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech Devices SDK.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: 9a757402360b4b69e760315a809482be9b4e04d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558898"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Rozwiązywanie problemów z zestawem Speech Devices SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z zestawu Speech Devices SDK.

## <a name="certificate-failures"></a>Błędy certyfikatów

Jeśli podczas korzystania z usług mowy wystąpią błędy certyfikatów, upewnij się, że urządzenie ma poprawną datę i godzinę:

1. Przejdź do **ustawienia**. W obszarze **systemu**, wybierz opcję **daty i godziny**.

    ![W obszarze Ustawienia wybierz datę i godzinę](media/speech-devices-sdk/qsg-12.png)

1. Zachowaj **automatycznej daty i godziny** wybraną opcją. W obszarze **wybierz strefę czasową**, wybierz strefę czasową.

    ![Wybierz opcje daty i strefy czasowej](media/speech-devices-sdk/qsg-13.png)

    Gdy pojawi się, że czas zestawu dla deweloperów odpowiada czas na komputerze z systemem, zestaw deweloperski jest połączony z Internetem.

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
