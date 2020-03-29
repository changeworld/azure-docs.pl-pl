---
title: Rozwiązywanie problemów z sdk urządzeń mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z sdk urządzeń mowy.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815567"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Rozwiązywanie problemów z zestawem Speech Devices SDK

Ten artykuł zawiera informacje ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas korzystania z sdk urządzeń mowy.

## <a name="certificate-failures"></a>Błędy certyfikatów

Jeśli podczas korzystania z usługi Mowy wystąpią błędy certyfikatów, upewnij się, że urządzenie ma poprawną datę i godzinę:

1. Przejdź do obszaru **Settings** (Ustawienia). W obszarze **System**wybierz **pozycję Data & godziną**.

    ![W obszarze Ustawienia wybierz pozycję Data & godziną](media/speech-devices-sdk/qsg-12.png)

1. Zachowaj opcję **Automatyczna data & czas** zaznaczona. W obszarze **Wybierz strefę czasową**wybierz bieżącą strefę czasową.

    ![Wybieranie opcji daty i strefy czasowej](media/speech-devices-sdk/qsg-13.png)

    Gdy zobaczysz, że czas zestawu deweloperów odpowiada czasowi na komputerze, zestaw deweloperski jest połączony z Internetem.

## <a name="next-steps"></a>Następne kroki

* [Przejrzyj informacje o wersji](devices-sdk-release-notes.md)
