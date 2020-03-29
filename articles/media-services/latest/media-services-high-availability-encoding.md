---
title: Kodowanie wysokiej dostępności usługi Azure Media Services
description: Dowiedz się, jak przejść do pracy awaryjnej na pomocniczym koncie usługi Media Services w przypadku wystąpienia regionalnej awarii lub awarii centrum danych.
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
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934198"
---
# <a name="media-services-high-availability-encoding"></a>Kodowanie wysokiej dostępności usługi Media Services 

Usługa kodowania usługi Azure Media Services jest regionalną platformą przetwarzania wsadowego i nie jest obecnie zaprojektowana z myślą o wysokiej dostępności w jednym regionie. Usługa kodowania obecnie nie zapewnia natychmiastowego trybu failover usługi, jeśli występuje regionalna awaria centrum danych lub awaria podstawowych składników lub usług zależnych (takich jak magazyn, SQL). W tym artykule wyjaśniono, jak wdrożyć usługi Media Services, aby utrzymać architekturę o wysokiej dostępności z pracy awaryjnej i zapewnić optymalną dostępność dla aplikacji.

Postępując zgodnie z wytycznymi i najlepszymi rozwiązaniami opisanymi w artykule, zmniejszysz ryzyko błędów kodowania, opóźnień i zminimalizuje czas odzyskiwania, jeśli wystąpi awaria w jednym regionie.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Jak zbudować międzyregionajny system kodowania

* [Utwórz](create-account-cli-how-to.md) dwa (lub więcej) kont usługi Azure Media Services.

    Oba konta muszą znajdować się w różnych regionach. Aby uzyskać więcej informacji, zobacz [regiony, w których wdrożono usługę Azure Media Services.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)
* Prześlij multimedia do tego samego regionu, z którego zamierzasz przesłać zadanie. Aby uzyskać więcej informacji na temat uruchamiania kodowania, zobacz [Tworzenie danych wejściowych zadania z adresu URL HTTPS](job-input-from-http-how-to.md) lub [Tworzenie danych wejściowych zadania z pliku lokalnego](job-input-from-local-file-how-to.md).

    Jeśli następnie trzeba ponownie przesłać [zadanie](transforms-jobs-concept.md) do innego regionu, można użyć JobInputHttp lub użyj [Copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) skopiować dane z kontenera zasobów źródłowych do kontenera zasobów w regionie alternatywnym.
* Subskrybuj komunikaty JobStateChange na każdym koncie za pośrednictwem usługi Azure Event Grid. Aby uzyskać więcej informacji, zobacz:

    * [Przykład analizy audio,](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) który pokazuje, jak monitorować zadanie za pomocą usługi Azure Event Grid, w tym dodawanie rezerwowego w przypadku, gdy wiadomości usługi Azure Event Grid są opóźnione z jakiegoś powodu.
    * [Schematy usługi Azure Event Grid dla zdarzeń usługi Media Services](media-services-event-schemas.md)
    * [Zarejestruj się na zdarzenia za pośrednictwem portalu Azure lub interfejsu wiersza polecenia](reacting-to-media-services-events.md) (można to również zrobić za pomocą SDK zarządzania EventGrid)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (który obsługuje zdarzenia usługi Media Services natywnie).

    Można również korzystać ze zdarzeń w usłudze Event Grid za pośrednictwem usługi Azure Functions.
* Podczas tworzenia [zadania:](transforms-jobs-concept.md)

    * Losowo wybierz konto z listy aktualnie używanych kont (lista ta zwykle zawiera oba konta, ale jeśli zostaną wykryte problemy, może zawierać tylko jedno konto). Jeśli lista jest pusta, należy podnieść alert, aby operator mógł zbadać.
    * Ogólne wskazówki są potrzebne jedną [jednostkę zarezerwowaną nośnika](media-reserved-units-cli-how-to.md) na [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (chyba że używasz [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) gdzie 3 nośniki zarezerwowane jednostki na JobOutput jest zalecane).
    * Pobierz liczbę jednostek zarezerwowanych multimediów (MRU) dla wybranego konta. Jeśli bieżąca liczba **jednostek zarezerwowanych nośników** nie jest jeszcze na wartości maksymalnej, dodaj liczbę jednostek MRU wymaganych przez zadanie i zaktualizuj usługę. Jeśli wskaźnik przesyłania zadania jest wysoki i często zapytania MRU, aby znaleźć się na maksimum, użyj rozproszonej pamięci podręcznej dla wartości z rozsądnym limitem czasu.
    * Zachowaj liczbę zadań pokładowych.

* Gdy program obsługi JobStateChange otrzymuje powiadomienie, że zadanie osiągnęło stan zaplanowany, rejestruje czas, w którym wchodzi w stan harmonogramu i używany region/konto.
* Gdy jobstatechange obsługi otrzymuje powiadomienie, że zadanie osiągnęło stan przetwarzania, oznacz rekord dla zadania jako przetwarzania.
* Gdy program obsługi JobStateChange otrzymuje powiadomienie, że zadanie osiągnęło stan Gotowy/Błąd/Anulowano, oznacz rekord zadania jako ostateczny i zmniejsza liczbę zadań pokładowych. Pobierz liczbę jednostek zarezerwowanych nośników dla wybranego konta i porównaj bieżący numer MRU z liczbą zadań pokładowych. Jeśli liczba lotów jest mniejsza niż liczba MRU, a następnie zmniejszyć go i zaktualizować usługę.
* Mieć osobny proces, który okresowo analizuje twoje rekordy zadań
    
    * Jeśli masz zadania w stanie zaplanowanym, które nie zostały zaawansowane do stanu przetwarzania w rozsądnym czasie dla danego regionu, usuń ten region z listy aktualnie używanych kont.  W zależności od wymagań biznesowych możesz natychmiast anulować te zadania i przesłać je ponownie do innego regionu. Możesz też dać im trochę więcej czasu na przejście do następnego stanu.
    * W zależności od liczby jednostek zarezerwowanych nośników skonfigurowanych na koncie i szybkości przesyłania mogą również istnieć zadania w stanie kolejki, których system nie odebrał jeszcze do przetworzenia.  Jeśli lista zadań w stanie kolejki przekracza dopuszczalny limit w regionie, te zadania mogą zostać anulowane i przesłane do innego regionu.  Może to jednak być objawem braku wystarczającej liczby jednostek zarezerwowanych nośników skonfigurowanych na koncie dla bieżącego obciążenia.  W razie potrzeby można zażądać wyższego przydziału jednostki zarezerwowanej multimediów za pośrednictwem pomocy technicznej platformy Azure.
    * Jeśli region został usunięty z listy kont, monitoruj go pod kątem odzyskiwania przed dodaniem go z powrotem do listy.  Kondycja regionalna może być monitorowana za pośrednictwem istniejących zadań w regionie (jeśli nie zostały anulowane i ponownie przesłane), przez dodanie konta z powrotem do listy po pewnym czasie oraz przez operatorów monitorujących komunikację platformy Azure o awariach, które mogą mieć wpływ na awarie, które mogą mieć wpływ Azure Media Services.
    
Jeśli okaże się, że liczba MRU jest thrashing w górę iw dół dużo, przenieść logikę dekrementacji do zadania okresowego. Mieć pre-job submit logiki porównania liczby podczas lotu do bieżącej liczby MRU, aby sprawdzić, czy trzeba zaktualizować MRU.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie przesyłania strumieniowego wideo na żądanie między regionami](media-services-high-availability-streaming.md)
* Wyewidencjonuj [przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
