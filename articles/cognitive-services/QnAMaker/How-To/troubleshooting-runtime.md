---
title: Rozwiązywanie problemów — QnAMaker
titleSuffix: Azure Cognitive Services
description: Qna Maker przechowuje wszelką obejmuje składniki hostowane w konta platformy Azure. Debugowanie może wymagać od użytkowników do manipulowania zasobami QnAMaker Azure lub zapewniają QnAMaker zespołem pomocy technicznej z dodatkowymi informacjami dotyczącymi ich konfiguracji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559920"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Wskazówki dotyczące rozwiązywania problemów do obsługi usługi QnA Maker i środowiska uruchomieniowego
Qna Maker przechowuje wszelką obejmuje składniki hostowane w konta platformy Azure. Debugowanie może wymagać od użytkowników do manipulowania zasobami QnAMaker Azure lub zapewniają QnAMaker zespołem pomocy technicznej z dodatkowymi informacjami dotyczącymi ich konfiguracji.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak uzyskać najnowsze aktualizacje środowiska uruchomieniowego QnAMaker
Qna Maker przechowuje wszelką środowiska uruchomieniowego jest częścią usługi Azure App Service wdrożone, kiedy należy [Tworzenie usługi qna Maker przechowuje wszelką](./set-up-qnamaker-service-azure.md) w witrynie Azure portal. Aktualizacje są okresowo wprowadzane do środowiska uruchomieniowego. App Service QnA Maker jest w trybie aktualizacji, Opublikuj nasze wydanie rozszerzenia witryny APR 2019 (wersja 5 +). Ta opcja została już zaprojektowana, aby zadbać o ZERO przestoju podczas uaktualniania. Bieżącą wersję można sprawdzić pod adresem https://www.qnamaker.ai/UserSettings. Jeśli wersja jest starsza niż wersja 5. x, należy ponownie uruchomić App Service, aby zastosować najnowsze aktualizacje do zastosowania do konfiguracji QnAMaker.

1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów platformy QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kliknij w usłudze App Service, a następnie otwórz sekcję — Omówienie

     ![Usługa qna Maker przechowuje wszelką App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Uruchom ponownie usługi App service. Powinno się zakończyć w ciągu kilku sekund. Należy zauważyć, że aplikacje podrzędne/botów kompilacji w ramach tej usługi qna Maker przechowuje wszelką będzie niedostępna dla użytkowników końcowych w tym okresie ponownego uruchomienia.

    ![Ponowne uruchomienie usługi App Service interfejsu QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak uzyskać nazwa hosta usługi qna Maker przechowuje wszelką
Nazwa hosta usługi qna Maker przechowuje wszelką jest przydatny do celów debugowania podczas skontaktuj się z działem pomocy technicznej QnAMaker lub UserVoice. Nazwa hosta jest tej postaci adresu URL: https:// *{nazwa hosta}* . azurewebsites.net.
    
1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów QnAMaker Azure w witrynie Azure portal](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Kliknij w usłudze App Service

     ![Wybierz usługę aplikacji interfejsu QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Nazwa hosta adresu URL jest dostępny w sekcji Przegląd

    ![Nazwa hosta w interfejsie QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ulepsz pytania dotyczące usługi Knowledge Base z aktywną nauką](./improve-knowledge-base.md)
