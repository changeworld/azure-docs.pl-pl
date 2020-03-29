---
title: Urządzenia mowy SDK Roobo Smart Audio Dev Kit v2 - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawu SDK urządzeń mowy Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371578"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Urządzenie: Roobo Smart Audio Dev Kit v2

Ten artykuł zawiera informacje dotyczące urządzenia dla Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>Konfigurowanie zestawu deweloperskiego

1. Zestaw deweloperski posiada dwa złącza micro USB. Lewe złącze jest do zasilania zestawu deweloperskiego i jest wyróżniony jako moc na obrazku poniżej. Po prawej stronie jest kontrolowanie go i jest oznaczony debugowania na obrazie. 
    ![Podłączanie zestawu deweloperskiego](media/speech-devices-sdk/roobo-v2-connections.png)
1. Podłącz zestaw deweloperski za pomocą kabla micro USB do podłączenia portu zasilania do komputera lub zasilacza. Pod górną płytą zaświeć zielony wskaźnik zasilania.
1. Aby sterować zestawem deweloperskim, podłącz port debugowania do komputera za pomocą drugiego kabla micro USB. Konieczne jest użycie kabla wysokiej jakości, aby zapewnić niezawodną komunikację.
1. Orientuj swój zestaw rozwojowy Circularly - Pionowo, z mikrofonami skierowanymi w stronę sufitu, jak pokazano powyżej


## <a name="development-information"></a>Informacje o rozwoju

Aby uzyskać więcej informacji na temat rozwoju, zobacz [przewodnik rozwoju Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Nagrywanie/odtwarzanie dźwięku

Operacje audio DDK2 mogą być wykonywane w następujący sposób:
* Użyj bibliotek open source ALSA i ich aplikacji.
* Użyj appmainprog interfejs do tworzenia aplikacji. DDK2 audio - powiązana struktura oprogramowania wykorzystuje standardową strukturę ALSA, można użyć libasound. Tak więc, aby opracować oprogramowanie bezpośrednio. Więc można użyć arecord ALSA i aplay bezpośrednio do nagrywania i odtwarzania dźwięku.
