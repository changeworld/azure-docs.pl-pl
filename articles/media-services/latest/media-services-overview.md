---
title: Azure Media Services wersja 3 — Omówienie
titleSuffix: Azure Media Services
description: Ogólne omówienie Azure Media Services v3 z linkami do przewodników Szybki Start, samouczków i przykładów kodu.
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
ms.openlocfilehash: c17f1094e562960e851204b40281f5b04c1d40ce
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186056"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services wersja 3 — Omówienie

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiąganie jakości odpowiedniej do emisji w przypadku przesyłania strumieniowego wideo, zwiększanie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Bez względu na to, czy jesteś deweloperem aplikacji, działem obsługi, agencją rządową, czy firmą rozrywkową, Media Services pomaga w tworzeniu aplikacji, które zapewniają obsługę multimediów dla dużych odbiorców na współczesnych najpopularniejszych urządzeniach przenośnych i przeglądarek.

Zestawy SDK Media Services V3 są oparte na [specyfikacji Media Services v3 openapi (Swagger)](https://aka.ms/ams-v3-rest-sdk).

> [!NOTE]
> Obecnie nie można używać Azure Portal do zarządzania zasobami v3. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Co mogę zrobić za pomocą usługi Media Services?

Media Services pozwala tworzyć różne przepływy pracy multimedialnej w chmurze. Poniżej przedstawiono kilka przykładów tego, co można zrobić z Media Services:

* Dostarczaj wideo w różnych formatach, aby można je było odtworzyć w różnych przeglądarkach i na różnych urządzeniach. W przypadku przesyłania strumieniowego na żądanie i na żywo do różnych klientów (urządzenia przenośne, Telewizja, komputer i tak dalej) zawartość wideo i audio musi być odpowiednio zakodowana i opakowana. Aby poznać sposób dostarczania i strumieniowego przesyłania takiej zawartości, zobacz [Szybki start: kodowanie i przesyłanie strumieniowe plików](stream-files-dotnet-quickstart.md).
* Przesyłaj strumieniowo wydarzenia sportowe na żywo do dużych odbiorców w trybie online, takich jak piłka nożna, siatkówki, szkoły i duże szkoły, i nie tylko.
* Emitowanie publicznych spotkań i wydarzeń, takich jak korytarze miasta, spotkania Rady miasta i treści prawne.
* Analizuj nagrania wideo lub zawartość audio. Aby na przykład osiągnąć wyższe zadowolenie klientów, organizacje mogą wyodrębnić mowę jako tekst i tworzyć indeksy wyszukiwania oraz pulpity nawigacyjne. Następnie mogą oni wyodrębnić informacje dotyczące typowych skarg, źródeł skarg i inne odpowiednie dane.
* Utwórz serwis wideo subskrypcji i przesyłaj strumieniowo zawartość chronioną przez technologię DRM, gdy klient (na przykład studio filmowe) chce ograniczyć dostęp i używanie zastrzeżonych prac objętych prawami autorskimi.
* Dostarczaj zawartość offline do odtwarzania w samolotach, pociągach i samochodach. Klient może pobrać zawartość do odtworzenia na swój telefon lub tablet, jeśli przewiduje, że zostanie odłączony od sieci.
* Zaimplementuj platformę wideo edukacyjnej uczenia maszynowego za pomocą Azure Media Services i [platformy Azure interfejsy API usług Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) na potrzeby podpisania mowy i tekstu, tłumaczenia na wiele języków i tak dalej.
* Dzięki usłudze Azure Media Services oraz [interfejsom API usług Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) możesz dodawać napisy i podpisy do filmów, aby spełnić potrzeby szerszego grona odbiorców (na przykład osób z wadami słuchu lub osób chcących jednocześnie czytać w innym języku).
* Włącz usługę Azure CDN, aby osiągnąć wysoką skalowalność w celu zapewnienia lepszej obsługi błyskawicznego zwiększenia obciążenia (na przykład na początku wydarzenia prezentującego nowy produkt).

## <a name="how-can-i-get-started-with-v3"></a>Jak można zacząć korzystać z wersji 3? 

Dowiedz się, jak kodować i spakować zawartość, przesyłać strumieniowo filmy wideo na żądanie, emitować na żywo i analizować filmy wideo za pomocą Media Services v3. Samouczki, dokumentacja interfejsu API i inne dokumenty przedstawiające sposób zapewnienia bezpiecznej transmisji strumieniowej materiałów wideo lub audio na żywo lub na żądanie, którą można skalować na potrzeby milionów użytkowników.

> [!TIP]
> Przed rozpoczęciem opracowywania, przejrzyj:<br/>* [podstawowe koncepcje](concepts-overview.md) (incudes ważne pojęcia, takie jak pakowanie, kodowanie i ochrona)<br/>* [Programowanie przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itd.)

### <a name="sdks"></a>Zestawy SDK

Rozpocznij programowanie przy użyciu [zestawów SDK klienta Azure Media Services v3](media-services-apis-overview.md#sdks).

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

Przewodniki z poradnikami zawierają przykłady kodu, które pokazują, jak wykonać zadanie. W tej sekcji znajdziesz wiele przykładów. Oto kilka z nich:

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

