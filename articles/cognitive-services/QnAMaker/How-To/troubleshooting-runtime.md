---
title: Rozwiązywanie problemów — QnAMaker
titlesuffix: Azure Cognitive Services
description: Qna Maker przechowuje wszelką obejmuje składniki hostowane w konta platformy Azure. Debugowanie może wymagać od użytkowników do manipulowania zasobami QnAMaker Azure lub zapewniają QnAMaker zespołem pomocy technicznej z dodatkowymi informacjami dotyczącymi ich konfiguracji.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: e607ca75f12cf81ac8ca177083ba8678aed02207
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223803"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Wskazówki dotyczące rozwiązywania problemów do obsługi usługi QnA Maker i środowiska uruchomieniowego
Qna Maker przechowuje wszelką obejmuje składniki hostowane w konta platformy Azure. Debugowanie może wymagać od użytkowników do manipulowania zasobami QnAMaker Azure lub zapewniają QnAMaker zespołem pomocy technicznej z dodatkowymi informacjami dotyczącymi ich konfiguracji.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak uzyskać najnowsze aktualizacje środowiska uruchomieniowego QnAMaker
Qna Maker przechowuje wszelką środowiska uruchomieniowego jest częścią usługi Azure App Service wdrożone, kiedy należy [Tworzenie usługi qna Maker przechowuje wszelką](./set-up-qnamaker-service-azure.md) w witrynie Azure portal. Aktualizacje są okresowo wprowadzane do środowiska uruchomieniowego. Aby zastosować najnowszych aktualizacji do zastosowania do instalacji usługi qna Maker przechowuje wszelką, należy ponownie uruchomić usługi App Service.
1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów platformy QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kliknij w usłudze App Service, a następnie otwórz sekcję — Omówienie

     ![Usługa qna Maker przechowuje wszelką App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Uruchom ponownie usługi App service. Powinno się zakończyć w ciągu kilku sekund. Należy zauważyć, że aplikacje podrzędne/botów kompilacji w ramach tej usługi qna Maker przechowuje wszelką będzie niedostępna dla użytkowników końcowych w tym okresie ponownego uruchomienia.

    ![Ponowne uruchomienie usługi App Service interfejsu QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak uzyskać nazwa hosta usługi qna Maker przechowuje wszelką
Nazwa hosta usługi qna Maker przechowuje wszelką jest przydatny do celów debugowania podczas skontaktuj się z działem pomocy technicznej QnAMaker lub UserVoice. Nazwa hosta jest tej postaci adresu URL: https://*{nazwa hosta}*. azurewebsites.net.
    
1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów QnAMaker Azure w witrynie Azure portal](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kliknij w usłudze App Service

     ![Wybierz usługę aplikacji interfejsu QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Nazwa hosta adresu URL jest dostępny w sekcji Przegląd

    ![Nazwa hosta w interfejsie QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poprawa wiedzy pytań za pomocą aktywne uczenie](./improve-knowledge-base.md)
