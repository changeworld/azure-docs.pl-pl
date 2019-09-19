---
title: Omówienie usługi Azure Media Services w wersji 3 | Microsoft Docs
description: Ten artykuł zawiera ogólne omówienie usługi Media Services i zapewnia linki do artykułów zawierających więcej szczegółów.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: a752d7759386828e916f68e5903115220812f3e0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123038"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services wersja 3 — Omówienie

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiąganie jakości odpowiedniej do emisji w przypadku przesyłania strumieniowego wideo, zwiększanie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Niezależnie od tego, czy jesteś deweloperem aplikacji, biurem obsługi, agencją rządową, czy też firmą rozrywkową, usługa Media Services pomaga tworzyć aplikacje, które zapewniają środowisko multimedialne o najwyższej jakości dużej liczbie odbiorców na obecnie najpopularniejszych urządzeniach przenośnych i przeglądarkach. 

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Co mogę zrobić za pomocą usługi Media Services?

Usługa Media Services umożliwia tworzenie różnych przepływów pracy multimediów w chmurze, a poniżej przedstawiono kilka przykładów tego, co można uzyskać za pomocą usługi Media Services.  

* Dostarczaj wideo w różnych formatach, aby można je było odtworzyć w różnych przeglądarkach i na różnych urządzeniach. Aby umożliwić zarówno dostarczanie na żądanie, jak i transmitowanie strumieniowo na żywo do różnych klientów (urządzeń przenośnych, telewizorów, komputerów itp.), treści wideo i audio muszą być odpowiednio zakodowane oraz zapakowane. Aby poznać sposób dostarczania i strumieniowego przesyłania takiej zawartości, zobacz [Szybki start: kodowanie i przesyłanie strumieniowe plików](stream-files-dotnet-quickstart.md).
* Przesyłaj strumieniowo do wielu odbiorców online imprezy sportowe, takie jak mecze piłki nożnej i baseballa, zawody międzyuczelniane lub międzyszkolne itd. 
* Prowadź emisję publicznych spotkań i wydarzeń, np. odbywających się w urzędach miejskich, albo spotkań rady miejskiej czy komisji.
* Analizuj nagrania wideo lub zawartość audio. Aby na przykład osiągnąć wyższe zadowolenie klientów, organizacje mogą wyodrębnić mowę jako tekst i tworzyć indeksy wyszukiwania oraz pulpity nawigacyjne. Następnie mogą oni wyodrębnić informacje dotyczące typowych skarg, źródeł skarg i inne odpowiednie dane.
* Utwórz serwis wideo subskrypcji i przesyłaj strumieniowo zawartość chronioną przez technologię DRM, gdy klient (na przykład studio filmowe) chce ograniczyć dostęp i używanie zastrzeżonych prac objętych prawami autorskimi.
* Dostarczaj zawartość offline do odtwarzania w samolotach, pociągach i samochodach. Klient może pobrać zawartość do odtworzenia na swój telefon lub tablet, jeśli przewiduje, że zostanie odłączony od sieci.
* Wdróż edukacyjną platformę wideo ze szkoleniami internetowymi za pomocą usługi Azure Media Services i [interfejsów API usług Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) w celu przekształcania mowy na tekst podpisów, tłumaczenia na wiele języków, itp. 
* Dzięki usłudze Azure Media Services oraz [interfejsom API usług Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) możesz dodawać napisy i podpisy do filmów, aby spełnić potrzeby szerszego grona odbiorców (na przykład osób z wadami słuchu lub osób chcących jednocześnie czytać w innym języku).
* Włącz usługę Azure CDN, aby osiągnąć wysoką skalowalność w celu zapewnienia lepszej obsługi błyskawicznego zwiększenia obciążenia (na przykład na początku wydarzenia prezentującego nowy produkt). 

## <a name="how-can-i-get-started-with-v3"></a>Jak można zacząć korzystać z wersji 3? 

Dowiedz się, jak kodować zawartość i tworzyć jej pakiety, przesyłać strumieniowo wideo na żądanie, prowadzić emisję na żywo i analizować wideo za pomocą usługi Media Services w wersji 3. Samouczki, dokumentacja interfejsu API i inne dokumenty przedstawiające sposób zapewnienia bezpiecznej transmisji strumieniowej materiałów wideo lub audio na żywo lub na żądanie, którą można skalować na potrzeby milionów użytkowników.

> [!TIP]
> Przed rozpoczęciem opracowywania, przejrzyj:<br/>* [Podstawowe pojęcia](concepts-overview.md) (incudes ważne pojęcia: pakowanie, kodowanie, ochrona itp.)<br/>* [Programowanie przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (obejmuje informacje dotyczące uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itp.)

### <a name="quickstarts"></a>Przewodniki Szybki start  

Przewodniki Szybki Start pokazują podstawowe instrukcje dotyczące pierwszego dnia dla nowych klientów, aby szybko wypróbować Media Services.

* [Przesyłanie strumieniowe plików wideo — .NET](stream-files-dotnet-quickstart.md)
* [Pliki wideo strumieniowego — interfejs wiersza polecenia](stream-files-cli-quickstart.md)
* [Przesyłanie strumieniowe plików wideo — Node. js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Samouczki 

W samouczkach przedstawiono procedury oparte na scenariuszu dla niektórych najważniejszych zadań Media Services.

* [Kodowanie zdalnego pliku i wideo strumieniowego — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumienia wideo — .NET](stream-files-tutorial-with-api.md)
* [Przesyłanie strumieniowe na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie dynamiczne AES-128 — .NET](protect-with-aes128.md)
    
### <a name="samples"></a>Przykłady

Użyj [tej przeglądarki przykładów](https://docs.microsoft.com/samples/browse/?products=azure-media-services) do przeglądania przykładów kodu Azure Media Services.

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

Artykuły zawierają przykłady kodu, które pokazują, jak wykonać zadanie. W tej sekcji znajdziesz wiele przykładów, Oto kilka z nich:

* [Tworzenie konta — interfejs wiersza polecenia](create-account-cli-how-to.md)
* [Dostęp do interfejsów API — interfejs wiersza polecenia](access-api-cli-how-to.md)
* [Koduj przy użyciu protokołu HTTPS jako dane wejściowe zadania — .NET](job-input-from-http-how-to.md)  
* [Monitorowanie zdarzeń — Portal](monitor-events-portal-how-to.md)
* [Szyfrowanie dynamiczne za pomocą technologii wielowątkowości platformy .NET](protect-with-drm.md) 
* [Jak kodować przy użyciu niestandardowego przekształcenia interfejsu wiersza polecenia](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Poznaj podstawowe koncepcje](concepts-overview.md)

