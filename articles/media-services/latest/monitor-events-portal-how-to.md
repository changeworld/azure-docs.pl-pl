---
title: Monitorowanie zdarzeń usługi Azure Media Services za pomocą usługi Event Grid za pomocą portalu
description: W tym artykule pokazano, jak subskrybować usługę Event Grid w celu monitorowania zdarzeń usługi Azure Media Services.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509227"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Tworzenie i monitorowanie zdarzeń usługi Media Services za pomocą usługi Event Grid przy użyciu witryny Azure Portal

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Ta usługa używa subskrypcji zdarzeń do kierowania [komunikatów](../../event-grid/concepts.md#event-subscriptions) zdarzeń do subskrybentów. Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować zdarzenie usługi Media Services, ponieważ właściwość eventType zaczyna się od "Microsoft.Media.". Aby uzyskać więcej informacji, zobacz [Schematy zdarzeń usługi Media Services](media-services-event-schemas.md).

W tym artykule za pomocą witryny Azure Portal do subskrybowania zdarzeń dla konta usługi Azure Media Services. Następnie wyzwalasz zdarzenia, aby wyświetlić wynik. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. W artykule wysyłamy zdarzenia do aplikacji sieci web, która zbiera i wyświetla wiadomości.

Po zakończeniu przekonasz się, że dane zdarzenia zostały wysłane do aplikacji internetowej.

## <a name="prerequisites"></a>Wymagania wstępne 

* Mieć aktywną subskrypcję platformy Azure.
* Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowanie zdarzeń dla konta usługi Media Services, utwórzmy punkt końcowy dla komunikatu o zdarzeniu. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. W tym artykule można wdrożyć [wstępnie szesnastą aplikację sieci web,](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniu. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Jeśli przełączysz się do witryny "Azure Event Grid Viewer", zobaczysz, że nie ma jeszcze żadnych zdarzeń.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Subskrybowanie zdarzeń usługi Media Services

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane.

1. W portalu wybierz swoje konto usługi Media Services i wybierz pozycję **Zdarzenia**.
1. W celu wysłania zdarzeń do aplikacji przeglądarki użyj elementu webhook w punkcie końcowym. 

   ![Wybieranie elementu webhook](./media/monitor-events-portal/select-web-hook.png)

1. Subskrypcja zdarzenia jest wstępnie wypełniona wartościami dla konta usługi Media Services. 
1. Wybierz opcję "Web Hook" dla **typu punktu końcowego**.
1. W tym temacie pozostawiamy **Subscribe do wszystkich typów zdarzeń zaznaczone.** Można jednak odznaczyć go i filtrować dla określonych typów zdarzeń. 
1. Kliknij łącze **Wybierz punkt końcowy.**

    Dla punktu końcowego elementu webhook podaj adres URL aplikacji internetowej i dodaj element `api/updates` do adresu URL strony głównej. 

1. Naciśnij **przycisk Potwierdź zaznaczenie**.
1. Kliknij przycisk **Utwórz**.
1. Nadaj swojej subskrypcji nazwę.

   ![Wybieranie dzienników](./media/monitor-events-portal/create-subscription.png)

1. Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. 

    Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Punkt końcowy musi `validationResponse` być `validationCode`ustawiony na . Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](../../event-grid/security-authentication.md). Możesz wyświetlić kod aplikacji sieci web, aby zobaczyć, jak sprawdza poprawność subskrypcji.

Teraz wyzwalajmy zdarzenia, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Zdarzenia dla konta usługi Media Services można wyzwolić, uruchamiając zadanie kodowania. Możesz wykonać [ten szybki start,](stream-files-dotnet-quickstart.md) aby zakodować plik i rozpocząć wysyłanie zdarzeń. Jeśli subskrybujesz wszystkie zdarzenia, zobaczysz ekran podobny do następującego:

> [!TIP]
> Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Nie odświeżaj strony, jeśli chcesz wyświetlić wszystkie zdarzenia.

![Wyświetlanie zdarzenia subskrypcji](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Następne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe](stream-files-tutorial-with-api.md)
