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
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876339"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Wskazówki dotyczące rozwiązywania problemów do obsługi usługi QnA Maker i środowiska uruchomieniowego

QnAMaker obejmuje zasoby hostowane w ramach subskrypcji platformy Azure użytkownika. Debugowanie może wymagać od użytkowników manipulowania zasobami usługi Azure QnAMaker lub udostępnienia zespołowi pomocy technicznej QnAMaker z dodatkowymi informacjami na temat ich konfiguracji.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak uzyskać najnowsze aktualizacje środowiska uruchomieniowego QnAMaker

Środowisko uruchomieniowe QnAMaker jest częścią Azure App Service wdrożone podczas [tworzenia usługi QnAMaker](./set-up-qnamaker-service-azure.md) w programie Azure Portal. Aktualizacje są okresowo wprowadzane do środowiska uruchomieniowego. QnA Maker App Service jest w trybie aktualizacji z aktualizacją Update 2019 (wersja 5 +). Ta opcja została już zaprojektowana, aby zadbać o ZERO przestoju podczas uaktualniania. 

Bieżącą wersję można sprawdzić pod adresem https://www.qnamaker.ai/UserSettings. Jeśli wersja jest starsza niż wersja 5. x, należy ponownie uruchomić App Service, aby zastosować najnowsze aktualizacje.

1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów platformy QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Kliknij w usłudze App Service, a następnie otwórz sekcję — Omówienie

     ![Usługa qna Maker przechowuje wszelką App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Uruchom ponownie usługi App service. Powinno się zakończyć w ciągu kilku sekund. Należy pamiętać, że wszystkie aplikacje zależne lub botów korzystające z tej usługi QnAMaker będą niedostępne dla użytkowników końcowych w trakcie tego okresu ponownego uruchomienia.

    ![Ponowne uruchomienie usługi App Service interfejsu QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak uzyskać nazwa hosta usługi qna Maker przechowuje wszelką
Nazwa hosta usługi qna Maker przechowuje wszelką jest przydatny do celów debugowania podczas skontaktuj się z działem pomocy technicznej QnAMaker lub UserVoice. Nazwa hosta jest adresem URL w tej formie: https:// *{hostname}* . azurewebsites.NET.
    
1. Przejdź do usługi qna Maker przechowuje wszelką (Grupa zasobów) [witryny Azure portal](https://portal.azure.com)

    ![Grupa zasobów QnAMaker Azure w witrynie Azure portal](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz App Service skojarzoną z zasobem QnA Maker. Zazwyczaj nazwy są takie same.

     ![Wybierz usługę aplikacji interfejsu QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Nazwa hosta adresu URL jest dostępny w sekcji Przegląd

    ![Nazwa hosta w interfejsie QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ulepsz pytania dotyczące usługi Knowledge Base z aktywną nauką](./improve-knowledge-base.md)
