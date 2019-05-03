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
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026158"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Rozwiązywanie problemów z Speech Devices SDK

Ten artykuł zawiera informacje ułatwiające rozwiązanie problemów, które można napotkać, korzystając z zestawu Speech Devices SDK.

## <a name="certificate-failures"></a>Błędy certyfikatów

Jeśli pojawi się błędy certyfikatów, korzystając z usług przetwarzania mowy, upewnij się, że urządzenie ma poprawną datę i godzinę:

1. Przejdź do obszaru **Settings** (Ustawienia). W obszarze **systemu**, wybierz opcję **daty i godziny**.

    ![W obszarze Ustawienia wybierz datę i godzinę](media/speech-devices-sdk/qsg-12.png)

1. Zachowaj **automatycznej daty i godziny** wybraną opcją. W obszarze **wybierz strefę czasową**, wybierz strefę czasową.

    ![Wybierz opcje daty i strefy czasowej](media/speech-devices-sdk/qsg-13.png)

    Gdy pojawi się, że czas zestawu dla deweloperów odpowiada czas na komputerze z systemem, zestaw deweloperski jest połączony z Internetem.

## <a name="next-steps"></a>Kolejne kroki

* [Zapoznaj się z informacjami o wersji](devices-sdk-release-notes.md)
