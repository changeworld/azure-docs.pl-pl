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
ms.date: 05/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 4fb42014e2c5389cf2a849ddf5114ab1f59c9e28
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414028"
---
# <a name="azure-media-services-v3-overview"></a>Omówienie usługi Azure Media Services v3

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiąganie jakości odpowiedniej do emisji w przypadku przesyłania strumieniowego wideo, zwiększanie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Niezależnie od tego, czy jesteś deweloperem aplikacji, biurem obsługi, agencją rządową, czy też firmą rozrywkową, usługa Media Services pomaga tworzyć aplikacje, które zapewniają środowisko multimedialne o najwyższej jakości dużej liczbie odbiorców na obecnie najpopularniejszych urządzeniach przenośnych i przeglądarkach. 

> [!NOTE]
> Obecnie nie można zarządzać zasobami w wersji 3 z witryny Azure Portal. Użyj [interfejsu API REST](https://aka.ms/ams-v3-rest-ref), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref) lub jednego z obsługiwanych [zestawów SDK](developers-guide.md).

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
> Sprawdź przed rozpoczęciem tworzenia:<br/>* [Podstawowe pojęcia](concepts-overview.md) (incudes ważnych pojęć: pakowanie, kodowania, ochrona, itp.)<br/>* [Tworzenie aplikacji za pomocą usługi Media Services v3 API](media-services-apis-overview.md) (w tym informacji na temat uzyskiwania dostępu do interfejsów API, konwencje nazewnictwa, itp.)

### <a name="quickstarts"></a>Przewodniki Szybki start  

Szybki Start pokazano, podstawowe instrukcje 1 dzień nowi klienci będą mogli szybko wypróbować usługę Media Services.

* [Pliki wideo Stream — .NET](stream-files-dotnet-quickstart.md)
* [Pliki wideo Stream — interfejs wiersza polecenia](stream-files-cli-quickstart.md)
* [Pliki wideo Stream — Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Samouczki 

W samouczkach pokazano oparte na scenariuszach procedury dla niektóre z najważniejszych zadań usługi Media Services.

* [Kodowanie pliku zdalnego i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumienia wideo — .NET](stream-files-tutorial-with-api.md)
* [Stream na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie filmu wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie AES-128 - .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Przewodniki z instrukcjami

Artykuły zawierają przykłady kodu, które pokazują sposób wykonania zadania. W tej sekcji znajdziesz wiele przykładów, poniżej przedstawiono kilka z nich:

* [Tworzenie konta — interfejs wiersza polecenia](create-account-cli-how-to.md)
* [Dostęp do interfejsów API — interfejs wiersza polecenia](access-api-cli-how-to.md)
* [Rozpoczynanie programowania z użyciem zestawów SDK](developers-guide.md)
* [Kodowanie za pomocą protokołu HTTPS jako dane wejściowe — zadanie platformy .NET](job-input-from-http-how-to.md)  
* [Monitor zdarzeń — Portal](monitor-events-portal-how-to.md)
* [Dynamiczne szyfrowanie przy użyciu technologii multi-DRM — .NET](protect-with-drm.md) 
* [Jak kodować z przekształcenie niestandardowe — interfejs wiersza polecenia](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Prześlij opinię i pobieranie aktualizacji

Zapoznaj się z [społeczności usługi Azure Media Services](media-services-community.md) artykuł, aby wyświetlić różne sposoby zadawaj pytania, Prześlij opinię i pobrać aktualizacje o usłudze Media Services.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o podstawowych pojęciach](concepts-overview.md)

