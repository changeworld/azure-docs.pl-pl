---
title: Omówienie usługi Azure Media Services w wersji 3
titleSuffix: Azure Media Services
description: Omówienie wysokiego poziomu usługi Azure Media Services w wersji 3 z łączami do przewodników Szybki start, samouczków i przykładów kodu.
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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: bd3890757377525cf9c178866a2a2fbc0791b9de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79461014"
---
# <a name="azure-media-services-v3-overview"></a>Omówienie usługi Azure Media Services w wersji 3

Azure Media Services to oparta na chmurze platforma umożliwiająca tworzenie rozwiązań pozwalających na osiągnięcie przesyłania strumieniowego wideo w jakości odpowiedniej do emisji, zwiększenie dostępności i dystrybucji, analizowanie zawartości i wykonywanie wielu innych operacji. Niezależnie od tego, czy jesteś programistą aplikacji, call center, agencją rządową czy firmą rozrywkową, usługa Media Services pomaga tworzyć aplikacje zapewniające duże wrażenia z obsługi multimediów dużym odbiorcom na najpopularniejszych urządzeniach mobilnych i przeglądarkach.

Pliki SDK usługi Media Services w wersji 3 są oparte na [specyfikacji OpenAPI usługi Media Services w wersji 3 (Swagger).](https://aka.ms/ams-v3-rest-sdk)

> [!NOTE]
> Obecnie można użyć [witryny Azure Portal](https://portal.azure.com/) do: zarządzania [zdarzeniami na żywo](live-events-outputs-concept.md)usługi Media Services w wersji 3, wyświetlania (nie manage) [zasobów](assets-concept.md)w wersji 3, [uzyskiwania informacji o uzyskiwaniu dostępu do interfejsów API](access-api-portal.md). W przypadku wszystkich innych zadań zarządzania (na przykład [przekształceń i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości)](content-protection-overview.md)użyj [interfejsu API REST,](https://docs.microsoft.com/rest/api/media/) [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z [obsługiwanych pakietów SDK.](media-services-apis-overview.md#sdks)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Zgodność, ochrona prywatności i zabezpieczenia

Jako ważne przypomnienie musisz przestrzegać wszystkich obowiązujących przepisów podczas korzystania z usługi Azure Media Services i nie wolno korzystać z usługi Media Services ani żadnej usługi platformy Azure w sposób, który narusza prawa innych osób lub może być szkodliwy dla innych osób.

Przed przesłaniem jakichkolwiek filmów/obrazów do usługi Media Services Użytkownik musi mieć wszelkie odpowiednie prawa do korzystania z wideo/obrazu, w tym, jeśli jest to wymagane przez prawo, wszystkie niezbędne zgody od osób fizycznych (jeśli istnieją) w filmie/obrazie, do wykorzystania, przetwarzania i przechowywania ich danych w usłudze Media Services i platformie Azure. Niektóre jurysdykcje mogą nakładać specjalne wymogi prawne dotyczące gromadzenia, przetwarzania i przechowywania danych online określonych kategorii danych, takich jak dane biometryczne. Przed skorzystaniem z usługi Media Services i platformy Azure w celu przetwarzania i przechowywania wszelkich danych podlegających specjalnym wymogom prawnym Użytkownik musi zapewnić zgodność z takimi wymogami prawnymi, które mogą mieć zastosowanie do Ciebie.

Aby dowiedzieć się więcej o zgodności, prywatności i bezpieczeństwie w programie Media Services, odwiedź [centrum zaufania](https://www.microsoft.com/trust-center/?rtc=1)firmy Microsoft . W przypadku obowiązków firmy Microsoft w zakresie ochrony prywatności, praktyk w zakresie przetwarzania danych i ich przechowywania, w tym sposobu usuwania danych, należy zapoznać się z [Zasadami zachowania poufności informacji](https://privacy.microsoft.com/PrivacyStatement)firmy Microsoft, [Warunkami korzystania z usług online](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") i [uzupełnieniem dotyczącym przetwarzania danych](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Korzystając z Usługi Media Services, użytkownik wyraża zgodę na przestrzeganie OST, DPA i Oświadczenia o ochronie prywatności.
 
## <a name="what-can-i-do-with-media-services"></a>Co mogę zrobić za pomocą usługi Media Services?

Usługa Media Services umożliwia tworzenie różnych przepływów pracy multimediów w chmurze. Oto kilka przykładów, co można zrobić z usługą Media Services:

* Dostarczaj wideo w różnych formatach, aby można je było odtworzyć w różnych przeglądarkach i na różnych urządzeniach. W przypadku dostarczania strumieniowego na żądanie i na żywo do różnych klientów (urządzeń mobilnych, telewizorów, komputerów i tak dalej) zawartość wideo i audio musi być odpowiednio zakodowana i zapakowana. Aby poznać sposób dostarczania i strumieniowego przesyłania takiej zawartości, zobacz [Szybki start: kodowanie i przesyłanie strumieniowe plików](stream-files-dotnet-quickstart.md).
* Stream wydarzeń sportowych na żywo do dużej publiczności online, takich jak piłka nożna, baseball, kolegium i liceum sportu, i więcej.
* Transmituj spotkania publiczne i wydarzenia, takie jak ratusze, posiedzenia rady miejskiej i organy ustawodawcze.
* Analizuj nagrania wideo lub zawartość audio. Aby na przykład osiągnąć wyższe zadowolenie klientów, organizacje mogą wyodrębnić mowę jako tekst i tworzyć indeksy wyszukiwania oraz pulpity nawigacyjne. Następnie mogą oni wyodrębnić informacje dotyczące typowych skarg, źródeł skarg i inne odpowiednie dane.
* Utwórz serwis wideo subskrypcji i przesyłaj strumieniowo zawartość chronioną przez technologię DRM, gdy klient (na przykład studio filmowe) chce ograniczyć dostęp i używanie zastrzeżonych prac objętych prawami autorskimi.
* Dostarczaj zawartość offline do odtwarzania w samolotach, pociągach i samochodach. Klient może pobrać zawartość do odtworzenia na swój telefon lub tablet, jeśli przewiduje, że zostanie odłączony od sieci.
* Zaimplementuj edukacyjną platformę wideo e-learning z [interfejsami API](https://docs.microsoft.com/azure/?pivot=products&panel=ai) usługi Azure Media Services i usługi Azure Cognitive Services do podpisów między speech-to-text, tłumaczenia na wiele języków itd.
* Dzięki usłudze Azure Media Services oraz [interfejsom API usług Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) możesz dodawać napisy i podpisy do filmów, aby spełnić potrzeby szerszego grona odbiorców (na przykład osób z wadami słuchu lub osób chcących jednocześnie czytać w innym języku).
* Włącz usługę Azure CDN, aby osiągnąć wysoką skalowalność w celu zapewnienia lepszej obsługi błyskawicznego zwiększenia obciążenia (na przykład na początku wydarzenia prezentującego nowy produkt).

## <a name="how-can-i-get-started-with-v3"></a>Jak można zacząć korzystać z wersji 3? 

Dowiedz się, jak kodować i pakować zawartość, przesyłać strumieniowo filmy na żądanie, transmitować na żywo i analizować filmy za pomocą usługi Media Services w wersji 3. Samouczki, dokumentacja interfejsu API i inne dokumenty przedstawiające sposób zapewnienia bezpiecznej transmisji strumieniowej materiałów wideo lub audio na żywo lub na żądanie, którą można skalować na potrzeby milionów użytkowników.

> [!TIP]
> Zanim zaczniesz się rozwijać, przejrzyj:<br/>* [Podstawowe pojęcia](concepts-overview.md) (wywołują ważne pojęcia, takie jak pakowanie, kodowanie i ochrona)<br/>* [Tworzenie za pomocą interfejsów API usługi Media Services w wersji 3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itd.)

### <a name="sdks"></a>Zestawy SDK

Rozpocznij tworzenie za pomocą [zestawów SDK klienta usługi Azure Media Services w wersji 3.](media-services-apis-overview.md#sdks)

### <a name="quickstarts"></a>Przewodniki Szybki start  

Przewodniki Szybki start pokazują podstawowe instrukcje dnia 1 dla nowych klientów, aby szybko wypróbować usługi Media Services.

* [Przesyłanie strumieniowe plików wideo — .NET](stream-files-dotnet-quickstart.md)
* [Przesyłanie strumieniowe plików wideo - CLI](stream-files-cli-quickstart.md)
* [Przesyłanie strumieniowe wideo — Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Samouczki

Samouczki pokazują procedury oparte na scenariuszach dla niektórych z najważniejszych zadań usługi Media Services.

* [Kodowanie pliku zdalnego i strumieniowe przesyłanie wideo — REST](stream-files-tutorial-with-rest.md)
* [Kodowanie przekazanego pliku i strumieniowe przesyłanie wideo — .NET](stream-files-tutorial-with-api.md)
* [Przesyłanie strumieniowe na żywo — .NET](stream-live-tutorial-with-api.md)
* [Analizowanie wideo — .NET](analyze-videos-tutorial-with-api.md)
* [Szyfrowanie dynamiczne AES-128 — .NET](protect-with-aes128.md)

### <a name="samples"></a>Samples

Ta [przeglądarka przykładów](https://docs.microsoft.com/samples/browse/?products=azure-media-services) służy do przeglądania przykładów kodu usługi Azure Media Services.

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

Instrukcje zawierają przykłady kodu, które pokazują, jak wykonać zadanie. W tej sekcji znajdziesz wiele przykładów. Oto kilka z nich:

* [Tworzenie konta — interfejs wiersza polecenia](create-account-cli-how-to.md)
* [Dostęp do interfejsów API — interfejs wiersza polecenia](access-api-cli-how-to.md)
* [Kodowanie z HTTPS jako wejściowe zadania - .NET](job-input-from-http-how-to.md)  
* [Monitorowanie zdarzeń — portal](monitor-events-portal-how-to.md)
* [Dynamiczne szyfrowanie za pomocą multi-DRM - .NET](protect-with-drm.md) 
* [Jak zakodować za pomocą niestandardowego przekształcenia - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, przekazuj opinie, otrzyj aktualizacje

Zapoznaj się z artykułem [społeczności usługi Azure Media Services,](media-services-community.md) aby zobaczyć różne sposoby zadawania pytań, przekazywania opinii i otrzymywać aktualizacje dotyczące usługi Media Services.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o podstawowych pojęciach](concepts-overview.md)
