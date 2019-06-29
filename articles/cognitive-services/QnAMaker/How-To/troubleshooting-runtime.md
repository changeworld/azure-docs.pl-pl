---
title: Rozwiązywanie problemów — QnAMaker
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 5b65a121e895b4855c7c69d2b67e7055c88ddd08
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466055"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Wskazówki dotyczące rozwiązywania problemów do obsługi usługi QnA Maker i środowiska uruchomieniowego
Qna Maker przechowuje wszelką obejmuje składniki hostowane w konta platformy Azure. Debugowanie może wymagać od użytkowników do manipulowania zasobami QnAMaker Azure lub zapewniają QnAMaker zespołem pomocy technicznej z dodatkowymi informacjami dotyczącymi ich konfiguracji.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak uzyskać najnowsze aktualizacje środowiska uruchomieniowego QnAMaker
Qna Maker przechowuje wszelką środowiska uruchomieniowego jest częścią usługi Azure App Service wdrożone, kiedy należy [Tworzenie usługi qna Maker przechowuje wszelką](./set-up-qnamaker-service-azure.md) w witrynie Azure portal. Aktualizacje są okresowo wprowadzane do środowiska uruchomieniowego. Usługa QnA Maker App Service jest na auto wpis w trybie aktualizacji wersji rozszerzenia witryny 2019 kwi (wersja 5 +). To już zaprojektowano w celu zajmie się przestojów podczas uaktualniania. Możesz sprawdzić bieżącą wersję na https://www.qnamaker.ai/UserSettings. Jeśli używana wersja jest starsza niż wersja 5.x, należy ponownie uruchomić usługi App Service do zastosowania najnowszych aktualizacji do zastosowania do instalacji usługi qna Maker przechowuje wszelką.

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
    

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Poprawa wiedzy pytań za pomocą aktywne uczenie](./improve-knowledge-base.md)
