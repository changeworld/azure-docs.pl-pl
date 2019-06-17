---
title: Monitorowanie zdarzeń dotyczących usługi Azure Media Services za pomocą usługi Event Grid przy użyciu portalu | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób subskrybowania usługi Event Grid, aby można było monitorować zdarzenia usługi Azure Media Services.
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: d4592c93cb7969c45a107d7365a1b9dabf11f412
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326533"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Tworzenie i monitorowanie zdarzeń usługi Media Services za pomocą usługi Event Grid przy użyciu witryny Azure portal

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Ta usługa używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Zdarzenia usługi Media Services można zidentyfikować, ponieważ właściwość Typ zdarzenia, który rozpoczyna się od "Microsoft.Media.". Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

W tym artykule używasz witryny Azure portal do subskrybowania zdarzenia dla swojego konta usługi Azure Media Services. Następnie możesz wyzwolić zdarzeń w celu wyświetlenia wyniku. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. W artykule firma Microsoft wysyła zdarzenia do aplikacji sieci web, która gromadzi i wyświetla komunikaty.

Po zakończeniu przekonasz się, że dane zdarzenia zostały wysłane do aplikacji sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne 

* Mieć aktywną subskrypcję platformy Azure.
* Utworzenie nowego konta usługi Azure Media Services zgodnie z opisem w [tym przewodniku Szybki start](create-account-cli-quickstart.md).

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń dla konta usługi Media Services, Utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. W tym artykule wdrożysz [aplikacji sieci web wstępnie skompilowanych](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Jeśli przełączysz się do witryny "Przeglądarka usługi Azure Event Grid" widoczne jeszcze nie żadnych zdarzeń.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Subskrybowanie zdarzeń usługi Media Services

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane.

1. W portalu wybierz konto usługi Media Services, a następnie wybierz **zdarzenia**.
1. W celu wysłania zdarzeń do aplikacji przeglądarki użyj elementu webhook w punkcie końcowym. 

   ![Wybieranie elementu webhook](./media/monitor-events-portal/select-web-hook.png)

1. Subskrypcja zdarzeń jest wstępnie wartościami dla konta usługi Media Services. 
1. Wybierz pozycję "Webhook dla **typ punktu końcowego**.
1. W tym temacie pozostawimy **Subskrybuj wszystkie typy zdarzeń** zaznaczone. Jednakże możesz usunąć zaznaczenie go i filtr dla określonych typów zdarzeń. 
1. Kliknij pozycję **wybierz punkt końcowy** łącza.

    Dla punktu końcowego elementu webhook podaj adres URL aplikacji internetowej i dodaj element `api/updates` do adresu URL strony głównej. 

1. Naciśnij klawisz **potwierdzenie wyboru**.
1. Kliknij przycisk **Utwórz**.
1. Nadaj swojej subskrypcji nazwę.

   ![Wybieranie dzienników](./media/monitor-events-portal/create-subscription.png)

1. Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. 

    Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Punkt końcowy musi ustawić `validationResponse` do `validationCode`. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](../../event-grid/security-authentication.md). Można wyświetlić kodu aplikacji sieci web, aby zobaczyć, jak sprawdza poprawność subskrypcji.

Teraz wyzwólmy zdarzeń, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego usługi.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Uruchamiając zadania kodowania, możesz wyzwolić zdarzeń dla konta usługi Media Services. Możesz wykonać [ten przewodnik Szybki Start](stream-files-dotnet-quickstart.md) kodowanie pliku i Rozpocznij wysyłanie zdarzeń. Jeśli masz subskrypcję do wszystkich zdarzeń, zostanie wyświetlony ekran podobny do następującego:

> [!TIP]
> Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Nie odświeżaj strony, jeśli chcesz wyświetlić wszystkie zdarzenia.

![Wyświetlanie zdarzenia subskrypcji](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe](stream-files-tutorial-with-api.md)
