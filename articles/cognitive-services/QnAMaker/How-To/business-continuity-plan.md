---
title: Tworzenie planu continuty business usługi Maker — strona główna - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Tworzenie planu ciągłości biznesowej usługi Maker — strona główna
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348608"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Tworzenie planu ciągłości biznesowej usługi Maker — strona główna

Głównym celem plan ciągłości prowadzenia działalności biznesowej jest utworzenie punktu końcowego elastyczne bazy wiedzy knowledge base, który nie czas przestoju Bot lub aplikacji, używające go.

![Plan bcp Maker — strona główna](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Ogólne informacje o tym, jak powyżej jest następujący:

1. Konfigurowanie dwóch równoległe [Maker — strona główna usług](../How-To/set-up-qnamaker-service-azure.md) w [Azure łączyć regionów](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions).

2. Synchronizowanie indeksów podstawowych i pomocniczych Azure search. Użyj przykładu github [tutaj](https://github.com/pchoudhari/QnAMakerBackupRestore) na temat sposobu wykonywania kopii zapasowej i przywracania Azure indeksów.

3. Utwórz kopię zapasową przy użyciu usługi Application Insights [Eksport ciągły](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry).

4. Po stosy podstawowe i pomocnicze są skonfigurowane, użyj [Menedżera ruchu](https://docs.microsoft.com/en-us/azure/traffic-manager/) do skonfigurowania dwa punkty końcowe i metody routingu.

5. Konieczne będzie utworzenie certyfikatu SSL dla punktu końcowego Menedżera ruchu. [Powiąż certyfikat SSL](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl) w usługach aplikacji.

6. Na koniec użyj punktu końcowego Menedżera ruchu w aplikacji lub Bot.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wybierz pojemności dla danego wdrożenia Maker — strona główna](../Tutorials/choosing-capacity-qnamaker-deployment.md)
