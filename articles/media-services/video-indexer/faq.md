---
title: Często zadawane pytania na temat usługi Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: Uzyskaj odpowiedzi na często zadawane pytania na temat usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: f20d718d0b1d3bbdf117e502a380897c79a7905f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799502"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat usługi Video Indexer.

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Usługa Video Indexer jest usługą sztucznej inteligencji, która jest częścią usługi Microsoft Azure Media Services. Indeksator wideo umożliwia organizowanie wielu modeli, które umożliwiają łatwe prowadzenie szczegółowe informacje dotyczące filmu wideo uczenia maszynowego. Aby zapewnić zaawansowane i dokładne informacje na temat technologii, indeksator wideo sprawia, że korzystanie z wielu kanałów wideo: audio, mowy i wizualizacji. Informacji szczegółowych indeksatora wideo mogą być używane na wiele sposobów, takich jak poprawa zawartości możliwości odnajdywania i udostępniania, tworzenie nowych szans zysków z aplikacji, lub tworzenie nowych środowisk używanego przez wgląd w dane. Video Indexer udostępnia interfejs sieci web do testowania, konfiguracji i dostosowania modeli w ramach Twojego konta. Deweloperzy mogą używać interfejsu API opartego na interfejsie REST do integracji indeksatora wideo w systemie produkcyjnym. 

### <a name="what-can-i-do-with-video-indexer"></a>Co można zrobić za pomocą indeksatora wideo?

Operacje, które indeksator wideo można wykonywać na pliki multimedialne, należą:

* Identyfikowanie, wyodrębniając mowę i zidentyfikować głośników.
* Identyfikowanie i wyodrębniania wyświetlanymi na ekranie tekst w filmie wideo.
* Wykrywanie obiektów w pliku wideo.
* Identyfikowanie marek (na przykład: Microsoft) od ścieżki audio i na ekranie tekst w filmie wideo.
* Wykrywanie i rozpoznawanie twarzy z bazy danych osobistości i zdefiniowanych przez użytkownika bazy danych twarzy.
* Wyodrębnianie tematy omówione, ale nie koniecznie są wymienione w zawartości audio i wideo.
* Tworzenie napisów lub transkrypcji ze ścieżki audio.

Więcej informacji i większą liczbą funkcji indeksatora wideo, zobacz [Przegląd](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak zacząć korzystać z indeksatora wideo?

Indeksator wideo obejmuje bezpłatna wersja próbna, oferty, który zapewnia 600 minut w interfejsie internetowym i 2400 minut za pomocą interfejsu API. Możesz [logowania do interfejsu opartego na sieci web Video Indexer](https://www.videoindexer.ai/) i wypróbuj ją samodzielnie przy użyciu dowolnej tożsamości w sieci web i bez konieczności konfigurowania subskrypcji platformy Azure. 

Indeks wideo i audio much na dużą skalę możesz nawiązać połączenie Video Indexer płatnej subskrypcji Microsoft Azure. Można znaleźć więcej informacji na temat cen na [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) strony.

Można znaleźć więcej informacji na temat rozpoczynania pracy [wprowadzenie](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Czy muszę mieć kodowanie umiejętności, aby użyć indeksatora wideo?

Interfejs internetowy Video Indexer umożliwia ocenę, konfigurowanie i zarządzanie kontem odbywa się za pomocą **bez konieczności kodowania**.  Gdy wszystko jest gotowe do tworzenia bardziej złożonych aplikacji, możesz użyć [interfejsu API indeksatora wideo](https://api-portal.videoindexer.ai/) nad zintegrowaniem Video Indexer własnej aplikacji, witryn sieci web lub [niestandardowych przepływów pracy przy użyciu technologii bezserwerowej, takich jak Usługi Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) lub usługi Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Czy muszę mieć maszyny learning umiejętności, aby użyć indeksatora wideo?

Nie, Video Indexer umożliwia integrację wielu modelach uczenia w jeden potok maszynowego. Indeksowanie pliku audio lub wideo za pośrednictwem Video Indexer pobiera pełny zestaw szczegółowych danych został wyodrębniony na jedną oś udostępnione bez maszyn uczenia umiejętności i wiedzy na algorytmach wymagane ze strony klienta.

### <a name="what-media-formats-does-video-indexer-support"></a>Formaty multimediów jest Video Indexer pomocy technicznej?

Usługa Video Indexer obsługuje najbardziej typowe formaty multimediów. Zapoznaj się [usługi Azure Media Encoder standard formaty](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) listy, aby uzyskać więcej informacji.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>W jaki sposób należy przekazać nośników do indeksatora wideo?

W portalu sieci web Video Indexer można przekazać pliku multimediów za pomocą okna dialogowego przekazywania pliku lub przez przejście do adresu URL, który bezpośrednio obsługuje pliku źródłowego (zobacz [przykład](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Dowolny adres URL, który hostów multimediów zawartości przy użyciu elementu iFrame i kodu osadzania nie będzie działać (zobacz [przykład](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). Interfejsu API indeksatora wideo wymaga określenia pliku wejściowego za pomocą adresu URL lub tablicą bajtów. Przekazywanie za pośrednictwem adresu URL przy użyciu interfejsu API są ograniczone do 10 GB, ale nie mają limitu czasu trwania. Aby uzyskać więcej informacji, zobacz to [poradnik](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Jak długo trwa indeksatora wideo, aby wyodrębnić szczegółowe informacje z nośnika?

Czas potrzebny do indeksu pliku audio lub wideo, zarówno przy użyciu interfejs API indeksatora wideo i interfejsu opartego na sieci web usługa Video Indexer zależy od wielu parametrów, takich jak plik długość i jakość, liczba insights znajdującą się w pliku, liczba [jednostek zarezerwowanych](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) dostępna oraz tego, czy [punkt końcowy przesyłania strumieniowego](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) jest włączony, czy nie. Zalecamy uruchomienie kilku plików testowych za pomocą własnej zawartości i uzyskać średnią, aby lepiej zrozumieć.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Można tworzyć niestandardowe przepływy pracy służące do automatyzacji procesów za pomocą indeksatora wideo?

Tak, można zintegrować Video Indexer technologii bezserwerowych, takich jak usługi Flow, Logic Apps i [usługi Azure Functions](https://azure.microsoft.com/services/functions/). Więcej szczegółów można znaleźć na [aplikacji logiki](https://azure.microsoft.com/services/logic-apps/) i [przepływu](https://flow.microsoft.com/en-us/) łączniki do indeksatora wideo [tutaj](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>W jakich regionach platformy Azure jest dostępna indeksatora wideo?

Możesz zobaczyć, które regiony platformy Azure jest dostępna na Video Indexer [regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) strony.

### <a name="what-is-the-sla-for-video-indexer"></a>Co to jest umowa SLA dla usługi Video Indexer?

Umowy SLA w usłudze multimediów Azure obejmuje Video Indexer i znajduje się na [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) strony. Umowy SLA stosuje się do usługi Video Indexer płatne konta i tylko nie ma zastosowania do bezpłatnej wersji próbnej.

## <a name="privacy-questions"></a>Pytania dotyczące ochrony prywatności

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Czy plików wideo i audio są indeksowane przez usługę Video Indexer przechowywane?

Tak, o ile nie usuniesz plik przez indeksator wideo, albo za pomocą indeksatora wideo witryny sieci Web lub interfejsu API, są przechowywane pliki audio i wideo. Dla bezpłatnej wersji próbnej, wideo i audio plików, które indeksu są przechowywane w regionie platformy Azure, wschodnie stany USA. W przeciwnym razie pliki audio i wideo są przechowywane na koncie magazynu w swojej subskrypcji platformy Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Czy mogę usunąć pliki, które są przechowywane w portalu indeksatora wideo

Tak, zawsze możesz usunąć filmu wideo i pliki audio, a także wszystkie metadane i szczegółowe informacje wyodrębnione z nich przez usługę Video Indexer. Raz usuwanie pliku z indeksatora wideo, pliku i jego danych i szczegółowych informacji są trwale usuwane z indeksatora wideo. Jednak jeśli zaimplementowano rozwiązania tworzenia kopii zapasowych w usłudze Azure storage, plik pozostaje w magazynie platformy Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Do Moje konto usługi Video Indexer można kontrolować dostęp użytkowników?

Tak, tylko administratorzy kont mogą zapraszać i Anuluj zaproszenie osób do swoich kont, a także przypisać kto ma uprawnienia do edycji i kto ma dostęp tylko do odczytu.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Kto ma dostęp do mojej plików audio i wideo, które mają zostać indeksowane i/lub przechowywane przez indeksator wideo i metadane i szczegółowe informacje, które zostały wyodrębnione?

Każda osoba, która zawiera link do zawartości wideo lub audio i jego szczegółowe informacje można uzyskać dostępu do zawartości wideo lub audio, które mają publiczne jak jego ustawienia prywatności. Zawartości wideo lub audio, które mają prywatny jako jego ustawienia prywatności może zostać oceniony jedynie przez użytkowników, którzy zostali zaproszeni do konta zawartość wideo lub audio. Ustawienia prywatności zawartości również odnosi się do metadanych i szczegółowe informacje, które wyodrębnia Video Indexer. Możesz przypisać ustawienia prywatności podczas przekazywania plików wideo lub audio. Można również zmienić ustawienia prywatności po indeksowania.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Jakiego dostępu firmy Microsoft ma Moje wideo lub audio plików, które mają zostać indeksowane i/lub przechowywane przez indeksator wideo i metadane i szczegółowe informacje, które zostały wyodrębnione?

Na [warunki dotyczące usług Online Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST), całkowicie właścicielem zawartości, a firma Microsoft będzie tylko dostępu do zawartości i metadanych i szczegółowe informacje, które Video Indexer wyodrębnia z zawartości zgodnie z pliku OST i firmy Microsoft Zasady zachowania poufności.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Należą modele niestandardowe, które mogę kompilować Moje konto usługi Video Indexer dostępne dla innych kont?

 Nie, niestandardowe modele, tworzonych w ramach Twojego konta nie są dostępne na inne konto. Usługa Video Indexer umożliwia obecnie Tworzenie niestandardowego [marek](customize-brands-model-overview.md), [języka](customize-language-model-overview.md), i [osoby](customize-person-model-overview.md) modeli w ramach Twojego konta. Modele te są dostępne tylko w ramach konta, w którym zostały utworzone modele.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Zawartość jest indeksowane przez indeksator wideo, przechowywane w regionie platformy Azure, której używam Video Indexer?

Tak, zawartość i jego szczegółowe informacje są przechowywane w obrębie regionu świadczenia usługi Azure, chyba że masz ręcznej konfiguracji w ramach subskrypcji Azure, który używa wielu regionach platformy Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Co to są zasady ochrony prywatności, indeksatora wideo?

Usługa Video Indexer jest objęta [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement). Zasady zachowania poufności wyjaśniają danych osobowych, które firma Microsoft przetwarza, jak firma Microsoft przetwarza je i do jakich celów Microsoft przetwarza je. Aby dowiedzieć się więcej o ochronie prywatności, odwiedź stronę [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Jakie certyfikaty ma indeksatora wideo?

Indeksator wideo ma obecnie certyfikacji SOC. Aby zapoznać się z indeksatora wideo certyfikacji, zapoznaj się [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>API Questions

### <a name="what-apis-does-video-indexer-offer"></a>Interfejsy API oferuje usługa Video Indexer

Interfejsy API Video Indexer umożliwia indeksowanie, wyodrębniając metadane, zarządzanie środkami trwałymi, tłumaczenia, osadzania, dostosowania różnych modeli i inne. Aby uzyskać bardziej szczegółowe informacje na temat korzystania z interfejsu API indeksatora wideo, zobacz [portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Jakie zestawy SDK klientów oferuje usługa Video Indexer

Obecnie nie są klienta, nie udostępniane zestawów SDK. Video Indexer zespół pracuje nad zestawy SDK i plany w celu dostarczania ich wkrótce.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak rozpocząć korzystanie z interfejsu API indeksatora wideo?

Postępuj zgodnie z [samouczek: wprowadzenie do interfejsu API indeksatora wideo](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Jaka jest różnica między interfejsu API indeksatora wideo i interfejsu API w wersji 3 Usługa multimediów Azure?

Obecnie istnieją pewne nakładania się w funkcji oferowanych przez interfejsu API indeksatora wideo i interfejsu API w wersji 3 Usługa multimediów Azure. Więcej informacji na temat sposobu porównywania obie te usługi można znaleźć [tutaj](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Co to jest token dostępu do interfejsu API i dlaczego jest potrzebna?

Interfejsu API indeksatora wideo zawiera autoryzacji interfejsu API i operacje interfejsu API. Interfejs API autoryzacji zawiera wywołania, które zapewniają tokenu dostępu. Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

Tokeny dostępu są niezbędne do używania interfejsów API indeksatora wideo ze względów bezpieczeństwa. Daje to gwarancję, że dowolne wywołania zostaną wprowadzone przez Ciebie lub tych, którzy mają uprawnienia dostępu do Twojego konta. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaka jest różnica między tokenu dostępu do konta, token dostępu użytkownika i token dostępu wideo?

* Poziom konta — tokenów dostępu na poziomie konta pozwalają na wykonywanie operacji na poziomie konta lub wideo. Na przykład przekazywania filmu wideo, listę wszystkich filmów wideo, Pobierz informacjom o filmie wideo.
* Na poziomie użytkownika — tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na poziomie użytkownika. Na przykład uzyskiwanie informacji o skojarzonych kontach.
* Poziom wideo — tokeny dostępu na poziomie wideo umożliwiają wykonywanie operacji na określonym wideo. Na przykład pobranie szczegółowych informacji o wideo, pobranie napisów, pobranie widżetów itp.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak często należy uzyskać nowy token dostępu Kiedy wygaśnie tokenów dostępu?

Tokeny dostępu wygasa co godzinę, więc należy wygenerować nowy token dostępu, co godzinę. 

## <a name="billing-questions"></a>Pytania dotyczące rozliczeń

### <a name="how-much-does-video-indexer-cost"></a>Ile kosztuje indeksatora wideo

Usługa Video Indexer używa prostego zgodnie z rzeczywistym użyciem model cenowy oparty na czasie trwania zawartości danych wejściowych, które można indeksować. Dodatkowe opłaty mogą mieć zastosowanie dla kodowanie, przesyłanie strumieniowe, magazynu, użycia sieci i media użytych jednostek zastrzeżonych. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) strony.

### <a name="when-am-i-billed-for-using-video-indexer"></a>Kiedy są rozliczane za pomocą indeksatora wideo?

Wysyłając wideo do indeksowania, użytkownik definiuje, czy indeksowanie ma być analizą wideo, analizą audio, czy jednym i drugim. Na tej podstawie określa się, za które jednostki SKU jest naliczana opłata. Jeśli podczas przetwarzania wystąpi błąd na poziomie krytycznym, w odpowiedzi zostanie zwrócony kod błędu. W takim przypadku nie są naliczane żadne opłaty.  Błąd krytyczny może być spowodowany usterką w naszym kodzie lub krytyczną awarią w wewnętrznej zależności usługi. Błędy takie jak nieprawidłowa identyfikacja lub ekstrakcja szczegółowych informacji nie są uważane za krytyczne i w takich przypadkach odpowiedź jest zwracana. Za każdym razem, gdy jest zwracana prawidłowa odpowiedź (czyli bez kodu błędu), jest naliczana opłata.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Video Indexer oferuje bezpłatną wersję próbną?

Tak, Video Indexer oferuje bezpłatną wersję próbną, który zapewnia pełną obsługę oraz funkcje interfejsów API. Obowiązuje przydział 600 minut warte filmów wideo dla użytkowników w interfejsie internetowym i 2400 minut dla użytkowników interfejsu API. 

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](video-indexer-overview.md)
