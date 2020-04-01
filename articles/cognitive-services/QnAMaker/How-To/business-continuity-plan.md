---
title: Plan ciągłości działania - QnA Maker
titleSuffix: Azure Cognitive Services
description: Głównym celem planu ciągłości działania jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak przestojów dla bota lub aplikacji, która go zużywa.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 2100426923418104c31933bf9d1406a6441d3841
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410928"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Tworzenie planu ciągłości działania dla usługi QnA Maker

Głównym celem planu ciągłości działania jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak przestojów dla bota lub aplikacji, która go zużywa.

![Plan BCP QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Pomysł wysokiego poziomu przedstawiony powyżej jest następujący:

1. Skonfiguruj dwie równoległe [usługi QnA Maker](../How-To/set-up-qnamaker-service-azure.md) w [sparowanych regionach platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. Synchronizacja podstawowych i pomocniczych indeksów wyszukiwania platformy Azure. Użyj przykładu GitHub [tutaj,](https://github.com/pchoudhari/QnAMakerBackupRestore) aby zobaczyć, jak przywrócić kopię zapasową indeksów platformy Azure.

3. Tworzenie kopii zapasowych usługi Application Insights przy użyciu [ciągłego eksportu](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry).

4. Po skonfigurowaniu stosów podstawowych i pomocniczych użyj [Menedżera ruchu,](https://docs.microsoft.com/azure/traffic-manager/) aby skonfigurować dwa punkty końcowe i skonfigurować metodę routingu.

5. Należy utworzyć certyfikat TLS (Transport Layer Security), wcześniej znany jako Secure Sockets Layer (SSL), certyfikat dla punktu końcowego menedżera ruchu. [Powiąż certyfikat TLS/SSL](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings) w usługach aplikacji.

6. Na koniec użyj punktu końcowego menedżera ruchu w botie lub aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybierz capactiy](./improve-knowledge-base.md)
