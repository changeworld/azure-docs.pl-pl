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
ms.date: 02/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 1911b851e4e219ec4c6d2d4872b75e9c18706feb
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893327"
---
# <a name="what-is-azure-media-services-v3"></a>Co to jest usługa Azure Media Services w wersji 3?

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiąganie jakości odpowiedniej do emisji w przypadku przesyłania strumieniowego wideo, zwiększanie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Niezależnie od tego, czy jesteś deweloperem aplikacji, biurem obsługi, agencją rządową, czy też firmą rozrywkową, usługa Media Services pomaga tworzyć aplikacje, które zapewniają środowisko multimedialne o najwyższej jakości dużej liczbie odbiorców na obecnie najpopularniejszych urządzeniach przenośnych i przeglądarkach. 

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

## <a name="v3-capabilities"></a>Możliwości wersji 3

Wersja 3 opiera się na ujednoliconej powierzchni interfejsu API, która udostępnia zarówno funkcje zarządzania, jak i operacji oparte na usłudze Azure Resource Manager. 

Ta wersja oferuje następujące możliwości:  

* **Przekształcenia**, które pomagają definiować proste przepływy pracy zadań przetwarzania lub analizy multimediów. Przekształcenie to przepis na przetwarzanie plików audio i wideo. Następnie możesz wielokrotnie je stosować, aby przetworzyć wszystkie pliki w Twojej bibliotece zawartości, przesyłając zadania do przekształcenia.
* **Zadania** przetwarzania (kodowania lub analizowania) Twoich plików wideo. Zawartość wejściową można określić dla zadania przy użyciu adresów URL HTTPS, adresów URL sygnatury dostępu współdzielonego lub ścieżek do plików znajdujących się w magazynie obiektów blob platformy Azure. Obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu przez adresy URL HTTPS.
* **Powiadomienia**, które monitorują postęp zadania lub stany albo uruchomienie/zatrzymanie kanału na żywo i błędy. Powiadomienia są zintegrowane z systemem powiadomień usługi Azure Event Grid. Możesz łatwo subskrybować zdarzenia dla kilku zasobów w usłudze Azure Media Services. 
* Szablony usługi **Azure Resource Management** mogą służyć do tworzenia i wdrażania przekształceń, punktów końcowych przesyłania strumieniowego, kanałów itd.
* **Kontrola dostępu oparta na rolach** może zostać ustawiona na poziomie zasobów, co umożliwia zablokowanie dostępu do określonych zasobów, takich jak przekształcenia, kanały i inne.
* **Zestawy SDK klientów** w wielu językach: .NET, .NET Core, Python, Go, Java i Node.js.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID). 

Nazwy zasobów usługi Media Services nie mogą zawierać znaków „<”, „>”, „%”, „&”, „:”, „&#92;”, „?”, „/”, „*”, „+”, „.”, pojedynczych cudzysłowów ani żadnych znaków sterujących. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków. 

Aby uzyskać więcej informacji na temat nazewnictwa w usłudze Azure Resource Manager zobacz: [Wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [Konwencje nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="media-services-v3-api-design-principles"></a>Zasady projektowania interfejsów API usługi Media Services w wersji 3

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. Interfejsy API w wersji 3 nie zwracają wpisów tajnych ani poświadczeń w operacji **Get** lub **List**. Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Należy wywołać oddzielną metodę akcji w celu pobrania wpisów tajnych lub poświadczeń. Oddzielne akcje umożliwiają ustawienie różnych uprawnień zabezpieczeń RBAC w przypadku, gdy niektóre interfejsy API pobierają/wyświetlają wpisy tajne, podczas gdy inne interfejsy API tego nie robią. Aby uzyskać informacje na temat zarządzania dostępem przy użyciu funkcji RBAC, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Przykłady obejmują: 

* niezwracanie wartości ContentKey w operacji Get elementu StreamingLocator, 
* niezwracanie kluczy ograniczeń w operacji get elementu ContentKeyPolicy, 
* niezwracanie części ciągu zapytania adresu URL (aby usunąć podpis) dla wejściowych adresów URL HTTP zadań.

Zobacz przykład [Get content key policy — .NET](get-content-key-policy-dotnet-howto.md) (Pobieranie zasad dotyczących klucza zawartości — .NET).

## <a name="how-can-i-get-started-with-v3"></a>Jak można zacząć korzystać z wersji 3?

Jako deweloper możesz użyć [interfejsu API REST](https://go.microsoft.com/fwlink/p/?linkid=873030) usługi Media Services lub bibliotek klienckich, które umożliwiają interakcję z interfejsem API REST, aby łatwo tworzyć i utrzymywać niestandardowe przepływy pracy multimediów oraz zarządzać nimi. Interfejs API usługi Media Services w wersji 3 opiera się na [specyfikacji interfejsu OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) (wcześniej znanej jako struktura Swagger).

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) to narzędzie dostępne dla użytkowników systemu Windows, którzy chcą dowiedzieć się więcej o usłudze Media Services. Narzędzie AMSE to aplikacja Winforms/C# obsługująca przekazywanie, pobieranie, kodowanie oraz przesyłanie strumieniowe wideo na żądanie i na żywo zawartości za pomocą usługi Media Services. Narzędzie AMSE jest przeznaczone dla klientów, którzy chcą przetestować usługę Media Services bez konieczności pisania jakiegokolwiek kodu. Kod AMSE jest dostarczany jako zasób dla klientów, którzy chcą tworzyć aplikacje za pomocą usługi Media Services.

Narzędzie AMSE to projekt typu Open Source, w przypadku którego pomoc techniczna jest świadczona przez społeczność (problemy można zgłaszać pod adresem https://github.com/Azure/Azure-Media-Services-Explorer/issues). W tym projekcie przyjęto [Kodeks postępowania oprogramowania Open Source firmy Microsoft](https://opensource.microsoft.com/codeofconduct/). Aby uzyskać więcej informacji, zobacz [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Często zadawane pytania dotyczące kodeksu postępowania) lub wyślij wiadomość e-mail na adres opencode@microsoft.com w przypadku jakichkolwiek dodatkowych pytań lub komentarzy.
 
Usługa Azure Media Services obsługuje poniższe biblioteki klienckie: 

|Dokumentacja interfejsu API|Zestawy SDK/narzędzia|Przykłady|
|---|---|---|---|
|[Dokumentacja stylu REST](https://aka.ms/ams-v3-rest-ref)|[Zestaw SDK REST](https://aka.ms/ams-v3-rest-sdk)|[Przykłady kolekcji Postman REST](https://github.com/Azure-Samples/media-services-v3-rest-postman)<br/>[Interfejs API REST bazujący na usłudze Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)|
|[Dokumentacja interfejsu wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli-ref)|[Interfejs wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli)|[Przykłady interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)||
|[Dokumentacja platformy .NET](https://aka.ms/ams-v3-dotnet-ref)|[Zestaw SDK platformy .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Przykłady dla platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)||
||[Zestaw SDK platformy .NET Core](https://aka.ms/ams-v3-dotnet-sdk) (wybierz kartę **Interfejs wiersza polecenia platformy .NET**)|[Przykłady dla platformy .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)||
|[Dokumentacja języka Java](https://aka.ms/ams-v3-java-ref)|[Zestaw SDK Java](https://aka.ms/ams-v3-java-sdk)||
|[Dokumentacja środowiska Node.js](https://aka.ms/ams-v3-nodejs-ref)|[Zestaw SDK dla platformy Node.js](https://aka.ms/ams-v3-nodejs-sdk)|[Przykłady dla platformy Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)||
|[Dokumentacja języka Python](https://aka.ms/ams-v3-python-ref)|[Zestaw SDK dla języka Python](https://aka.ms/ams-v3-python-sdk)||
|[Dokumentacja języka Go](https://aka.ms/ams-v3-go-ref)|[Zestaw SDK dla języka Go](https://aka.ms/ams-v3-go-sdk)||
|Ruby|[Zestaw SDK dla języka Ruby](https://aka.ms/ams-v3-ruby-sdk)||

## <a name="next-steps"></a>Następne kroki

Aby sprawdzić, jak łatwo rozpocząć kodowanie i strumieniowe przesyłanie plików wideo, zobacz [Strumieniowe przesyłanie plików](stream-files-dotnet-quickstart.md). 

