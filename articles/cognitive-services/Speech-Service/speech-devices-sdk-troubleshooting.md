---
title: Rozwiązywanie problemów z zestaw Speech Devices SDK — usługi mowy
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera informacje ułatwiające rozwiązanie problemów, które można napotkać, korzystając z zestawu Speech Devices SDK.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606296"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Rozwiązywanie problemów z zestawem Speech Devices SDK

Ten artykuł zawiera informacje ułatwiające rozwiązanie problemów, które można napotkać, korzystając z zestawu Speech Devices SDK.

## <a name="certificate-failures"></a>Błędy certyfikatów

Jeśli pojawi się błędy certyfikatów, korzystając z usług przetwarzania mowy, upewnij się, że urządzenie ma poprawną datę i godzinę:

1. Przejdź do **ustawienia**. W obszarze **systemu**, wybierz opcję **daty i godziny**.

    ![W obszarze Ustawienia wybierz datę i godzinę](media/speech-devices-sdk/qsg-12.png)

1. Zachowaj **automatycznej daty i godziny** wybraną opcją. W obszarze **wybierz strefę czasową**, wybierz strefę czasową.

    ![Wybierz opcje daty i strefy czasowej](media/speech-devices-sdk/qsg-13.png)

    Gdy pojawi się, że czas zestawu dla deweloperów odpowiada czas na komputerze z systemem, zestaw deweloperski jest połączony z Internetem.

## <a name="next-steps"></a>Kolejne kroki

* [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
