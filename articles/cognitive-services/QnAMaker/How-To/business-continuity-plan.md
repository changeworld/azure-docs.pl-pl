---
title: Plan ciągłości działania - QnA Maker
description: Głównym celem planu ciągłości działania jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak przestojów dla bota lub aplikacji, która go zużywa.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887076"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Tworzenie planu ciągłości działania dla usługi QnA Maker

Głównym celem planu ciągłości działania jest utworzenie odpornego punktu końcowego bazy wiedzy, który zapewni brak przestojów dla bota lub aplikacji, która go zużywa.

## <a name="business-continuity-with-traffic-manager"></a>Ciągłość działania z menedżerem ruchu

> [!div class="mx-imgBorder"]
> ![Plan BCP QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Pomysł wysokiego poziomu przedstawiony powyżej jest następujący:

1. Skonfiguruj dwie równoległe [usługi QnA Maker](set-up-qnamaker-service-azure.md) w [sparowanych regionach platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Utwórz kopię zapasową](../../../app-service/manage-backup.md) podstawowej usługi aplikacji QnA Maker i [przywróć](../../../app-service/web-sites-restore.md) ją w konfiguracji dodatkowej. Zapewni to, że obie konfiguracje działają z tą samą nazwa hosta i klucze.

3. Synchronizacja podstawowych i pomocniczych indeksów wyszukiwania platformy Azure. Użyj przykładu GitHub [tutaj,](https://github.com/pchoudhari/QnAMakerBackupRestore) aby zobaczyć, jak przywrócić kopię zapasową indeksów platformy Azure.

4. Tworzenie kopii zapasowych usługi Application Insights przy użyciu [ciągłego eksportu](../../../application-insights/app-insights-export-telemetry.md).

5. Po skonfigurowaniu stosów podstawowych i pomocniczych użyj [Menedżera ruchu,](../../../traffic-manager/traffic-manager-overview.md) aby skonfigurować dwa punkty końcowe i skonfigurować metodę routingu.

6. Należy utworzyć certyfikat TLS (Transport Layer Security), wcześniej znany jako Secure Sockets Layer (SSL), certyfikat dla punktu końcowego menedżera ruchu. [Powiąż certyfikat TLS/SSL](../../../app-service/configure-ssl-bindings.md) w usługach aplikacji.

7. Na koniec użyj punktu końcowego menedżera ruchu w botie lub aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie pojemności](./improve-knowledge-base.md)