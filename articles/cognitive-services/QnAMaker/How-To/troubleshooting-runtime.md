---
title: Rozwiązywanie problemów z QnAMaker | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak rozwiązywać problemy z QnAMaker środowiska wykonawczego
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 06/04/2018
ms.author: saneppal
ms.openlocfilehash: 23847c81a39ea392b6e64575406c9dc338b852de
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356036"
---
# <a name="qnamaker-troubleshooting"></a>Rozwiązywanie problemów z QnAMaker
QnAMaker obejmuje składniki hostowanych w konta platformy Azure. Debugowanie może wymagać od użytkowników do manipulowania zasobami QnAMaker Azure lub podaj zespołem pomocy technicznej QnAMaker dodatkowe informacje o ich konfiguracji.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Jak uzyskać najnowsze środowisko uruchomieniowe QnAMaker
Środowisko uruchomieniowe QnAMaker jest częścią wdrożone usługi Azure App Service, kiedy należy [Tworzenie usługi QnAMaker](./set-up-qnamaker-service-azure.md) w portalu Azure. Aktualizacje składają okresowo do środowiska wykonawczego. Aby zastosować najnowszych aktualizacji do zastosowania do konfiguracji QnAMaker, należy ponownie uruchomić usługę aplikacji.
1. Przejdź do usługi QnAMaker (grupę zasobów) [portalu Azure](https://portal.azure.com)

    ![Grupa zasobów QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Polecenie usługi App Service i Otwórz w sekcji Przegląd

     ![Usługi aplikacji QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Uruchom ponownie usługę aplikacji. Go powinno zakończyć się w ciągu kilku sekund. Należy pamiętać, że Twoje aplikacje podrzędne/robotów kompilacji w tej usłudze QnAMaker będą niedostępne dla użytkowników końcowych w okresie ponownego uruchomienia.

    ![Ponowne uruchomienie usługi aplikacji QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Jak uzyskać nazwę hosta usługi QnAMaker
Nazwa hosta usługi QnAMaker jest przydatna do celów debugowania skontaktuj się z pomocą techniczną QnAMaker lub UserVoice. Nazwa hosta jest adres URL tego formularza: https://*{nazwa hosta}*. azurewebsites.net.
    
1. Przejdź do usługi QnAMaker (grupę zasobów) [portalu Azure](https://portal.azure.com)

    ![Grupa zasobów QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Polecenie usługi aplikacji

     ![Usługi aplikacji QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Adres URL nazwy hosta jest dostępny w sekcji Przegląd

    ![Nazwa hosta QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Użyj QnAMaker interfejsu API](./upgrade-qnamaker-service.md)
