---
title: Azure Media Services wysoka dostępność
description: Dowiedz się, jak przełączyć się w tryb failover na pomocnicze konto Media Services, jeśli wystąpi awaria lub niepowodzenie centrum danych.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202288"
---
# <a name="azure-media-services-high-availability-guidance"></a>Wskazówki dotyczące Azure Media Services wysokiej dostępności

Usługa kodowania Azure Media Services jest regionalną platformą przetwarzania wsadowego i nie jest obecnie przeznaczona do wysokiej dostępności w ramach jednego regionu. Usługa kodowania obecnie nie zapewnia natychmiastowej pracy awaryjnej usługi, jeśli istnieje regionalna awaria centrum danych lub niepowodzenie podstawowego składnika lub usług zależnych (takich jak Storage, SQL itp.).  W tym artykule opisano sposób wdrażania Media Services w celu utrzymania architektury wysokiej dostępności z trybem failover i zapewnienia optymalnej dostępności aplikacji. 

Postępując zgodnie z wytycznymi i najlepszymi rozwiązaniami opisanymi w artykule, należy obniżyć ryzyko błędów, opóźnień i skrócić czas odzyskiwania w przypadku wystąpienia awarii w jednym regionie.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Jak utworzyć system kodowania między różnymi regionami

* [Utwórz](create-account-cli-how-to.md) co najmniej dwa konta Azure Media Services.
* Subskrybuj wiadomości **JobStateChange** na poszczególnych kontach.

    * W Media Services v3 odbywa się za pośrednictwem Azure Event Grid. Aby uzyskać więcej informacji, zobacz:
    
       * [Przykłady Event Grid](../../event-grid/receive-events.md) 
       * [Schematy Azure Event Grid dla zdarzeń Media Services](media-services-event-schemas.md), 
       * [Rejestrowanie zdarzeń za pośrednictwem Azure Portal lub interfejsu wiersza polecenia](reacting-to-media-services-events.md) (można go również uzyskać za pomocą zestawu SDK EventGrid Management)
       * [Microsoft. Azure. EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (który obsługuje zdarzenia Media Services natywnie). 
       
        Możesz również wykorzystać zdarzenia Event Grid za pośrednictwem Azure Functions.
    * W Media Services V2 jest to realizowane za pośrednictwem [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* Podczas [tworzenia zadania](transforms-jobs-concept.md):

    * Losowo wybierz konto z listy obecnie używanych kont (Ta lista będzie zazwyczaj zawierać oba konta, ale w przypadku wykrycia problemów może ono zawierać tylko jedno konto). Jeśli lista jest pusta, Zgłoś alert, aby operator mógł zbadać.
    * Ogólne wskazówki to wymagana jedna [Jednostka zarezerwowana multimediów](media-reserved-units-cli-how-to.md) na zadanie lub [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (chyba że używasz [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) w wersji 3).
    * Pobierz liczbę [jednostek zarezerwowanych multimediów](media-reserved-units-cli-how-to.md) (MRUs) dla wybranego konta. Jeśli bieżąca liczba **jednostek zarezerwowanych multimediów** nie jest jeszcze wartością maksymalną, należy dodać liczbę MRUs wymaganych przez zadanie i zaktualizować usługę. Jeśli szybkość przesyłania zadań jest wysoka i często trwa badanie MRUs w celu znalezienia maksymalnego limitu, użyj rozproszonej pamięci podręcznej dla wartości z rozsądnym limitem czasu.
    * Zachowaj liczbę zadań numerów porządkowych określających.
* Gdy program obsługi JobStateChange pobiera powiadomienie o osiągnięciu zaplanowanego stanu zadania, należy zarejestrować czas wprowadzania stanu harmonogramu oraz używanego regionu/konta.    
* Gdy program obsługi JobStateChange pobiera powiadomienie, że zadanie osiągnęło stan przetwarzania, Oznacz rekord dla zadania jako przetwarzanie.
* Gdy program obsługi JobStateChange pobiera powiadomienie, że zadanie osiągnęło stan zakończono/błąd/Anulowano, Oznacz rekord dla zadania jako końcowy i zmniejsz liczbę zadań numerów porządkowych określających. Pobierz liczbę jednostek zarezerwowanych multimediów dla wybranego konta i porównaj bieżący numer MRU z liczbą zadań numerów porządkowych określających. Jeśli liczba numerów porządkowych określających jest mniejsza niż liczba MRU, Zmniejsz ją i zaktualizuj usługę.
* Mają oddzielny proces, który okresowo przegląda rekordy zadań. Jeśli masz zadania w stanie zaplanowanym, które nie zostały zaawansowane do stanu przetwarzania w rozsądnym czasie dla danego regionu, Usuń ten region z listy obecnie używanych kont.

    * W zależności od wymagań firmy można zdecydować, aby anulować te zadania od razu i przesłać je ponownie na inne konto. Możesz też udostępnić im więcej czasu, aby przejść do następnego stanu.   
    * Po upływie tego czasu należy dodać konto z powrotem do listy aktualnie używanych (z założeniem, że region został odzyskany).
    
Jeśli okaże się, że liczba MRU jest migotanie w górę i w dół, Przenieś logikę zmniejszania do zadania okresowego. Zapoznaj się z logiką przesyłania przed zadaniami Porównaj liczbę numerów porządkowych określających z bieżącą liczbą MRU, aby sprawdzić, czy wymaga ona aktualizacji MRUs.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Jak kompilować strumieniowe przesyłanie wideo na żądanie 

* Przesyłanie strumieniowe między różnymi regionami wideo obejmuje duplikowanie [zasobów](assets-concept.md), [zasad kluczy zawartości](content-key-policy-concept.md) (jeśli są używane), [zasad przesyłania strumieniowego](streaming-policy-concept.md)i [lokalizatorów przesyłania strumieniowego](streaming-locators-concept.md). 
* Należy utworzyć zasady w obu regionach i zachować ich aktualność. 
* Podczas tworzenia lokalizatorów przesyłania strumieniowego należy użyć tej samej wartości identyfikatora lokalizatora, wartości identyfikatora ContentKey i wartości ContentKey.  
* W przypadku kodowania zawartości należy zakodować zawartość w regionie A i opublikować ją, a następnie skopiować zakodowaną zawartość do regionu B i opublikować ją przy użyciu takich samych wartości jak z regionu A.
* Usługi Traffic Manager można używać na nazwach hostów dla źródła i usług dostarczania kluczy (w konfiguracji Media Services będzie to wyglądać jak niestandardowy adres URL serwera kluczy).

## <a name="next-steps"></a>Następne kroki

* [Create an account](create-account-cli-how-to.md) (Tworzenie konta)
* Zapoznaj się z [przykładami kodu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
