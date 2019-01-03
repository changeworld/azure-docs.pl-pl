---
title: Często zadawane pytania na temat usługi Video Indexer — platformy Azure
titlesuffix: Azure Media Services
description: Uzyskaj odpowiedzi na często zadawane pytania na temat usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/19/2018
ms.author: juliako
ms.openlocfilehash: 972858b4bae995b6e9073cf7ee451a317e9f3909
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731319"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania na temat usługi Video Indexer.

## <a name="general-questions"></a>Pytania ogólne

### <a name="what-is-video-indexer"></a>Co to jest Video Indexer?

Usługa Video Indexer jest usługą wyodrębniania metadanych dla plików audio i wideo z usługi Azure Media Services. Używa ułatwia klasyfikowanie, analizowanie i Uzyskaj wgląd w wideo i audio zawartości przy użyciu bogatego zestawu algorytmów uczenia maszynowego [wstępnie zdefiniowane modele](video-indexer-overview.md). Można użyć wglądowi w szczegółowe dane na wiele sposobów, takich jak poprawa odnajdowanie zawartości i dostępności, tworzenie nowych szans zysków z aplikacji lub tworzenie nowych środowisk korzystających z wgląd w dane.

Usługa Video Indexer udostępnia interfejs sieci web do testowania, konfiguracji i dostosowania i interfejs API oparty na protokole REST dla deweloperów zintegrować system produkcyjny.

### <a name="what-can-i-do-with-video-indexer"></a>Co można zrobić za pomocą indeksatora wideo?

Indeksator wideo można wykonywać następujące operacje na plikach nośnika:

* Wykrywanie i wyodrębnianie mowy oraz zidentyfikować głośników.
* Identyfikowanie i Wyodrębnij wyświetlanymi na ekranie tekst w filmie wideo.
* Identyfikowanie i oznaczanie obiektów w pliku wideo.
* Zidentyfikuj marki, takich jak Microsoft ze ścieżki audio i na ekranie tekst w filmie wideo.
* Wykrywanie i rozpoznawanie twarzy z bazy danych osobistości i zdefiniowanych przez użytkownika bazy danych twarzy.
* Wyodrębnianie słów kluczowych z zawartości audio i wideo na podstawie tekstu mówionego i visual.
* Wyodrębnij tematach opisano, ale nie musi jawnie wymienione w zawartości audio i wideo na podstawie tekstu mówionego i visual.
* Utwórz napisów lub transkrypcji na podstawie ścieżki audio.

Aby uzyskać więcej informacji, zobacz [Omówienie](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak zacząć korzystać z indeksatora wideo?

Usługa Video Indexer jest można wypróbować bezpłatnie. Bezpłatna wersja próbna umożliwia 600 minut w interfejsie internetowym i 2400 minut za pomocą interfejsu API.

Indeks wideo i audio much na dużą skalę możesz nawiązać połączenie Video Indexer płatnej subskrypcji Microsoft Azure. Można znaleźć więcej informacji na temat cen na [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) strony.

Można znaleźć więcej informacji na temat rozpoczynania pracy [wprowadzenie](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Czy muszę mieć kodowanie umiejętności, aby użyć indeksatora wideo?

Interfejsy API indeksatora wideo można integrować z potokiem lub możesz użyć portalu indeksatora wideo i nie trzeba pisać kodu w ogóle. 

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Czy muszę mieć maszyny learning umiejętności, aby użyć indeksatora wideo?

Nie można wyodrębnić szczegółowe informacje z zawartości audio i wideo całkowicie bez uczenia umiejętności i wiedzy na algorytmach maszyn. 

### <a name="what-media-formats-does-video-indexer-support"></a>Formaty multimediów jest Video Indexer pomocy technicznej?

Usługa Video Indexer obsługuje najbardziej typowe formaty multimediów. Zapoznaj się z listy standardowych formatów usługi Azure Media Encoder, aby uzyskać więcej informacji.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>W jaki sposób należy przekazać nośników do indeksatora wideo?

W portalu sieci web Video Indexer można przekazać pliku multimediów za pomocą okna dialogowego przekazywania pliku, lub przez przejście do adresu URL, który bezpośrednio Określa plik. Na przykład `http://contoso.cloudapp.net/videos/example.mp4`. Link do strony zawierającej odtwarzacz wideo, takich jak `http://contoso.cloudapp.net/videos` nie będzie działać. Interfejsu API indeksatora wideo wymaga określenia pliku wejściowego za pomocą adresu URL lub tablicą bajtów. Należy zauważyć, że zostanie przesłany za pośrednictwem adresu URL jest ograniczona do 10 GB, ale nie mają limitu czasu trwania. Aby uzyskać więcej informacji, zobacz to [poradnik](upload-index-videos.md).

### <a name="how-long-does-it-take-visual-indexer-to-extract-insights-from-media"></a>Jak długo trwa Visual indeksator do wyodrębniania szczegółowych informacji z nośnika?

Ilość czasu potrzebnego do indeksowania pliku audio lub wideo, zarówno przy użyciu interfejs API indeksatora wideo i interfejsu opartego na sieci web usługa Video Indexer zależy od wielu parametrów, takich jak plik długość i jakość, liczba znaleziono w pliku, a liczba obliczeń szczegółowych informacji łączy w sobie dostępne, i czy jest włączony punkt końcowy przesyłania strumieniowego, czy nie. Dla typów najbardziej zawartości przy założeniu, że jednostki zarezerwowane S3 10 jest włączona, firma Microsoft przewidzieć że indeksowania może potrwać od 1/3 do ½, długości klipu wideo.  Jednak firma Microsoft zaleca uruchomienie kilku plików testowych za pomocą własnej zawartości, a następnie uzyskać średnią, aby lepiej zrozumieć. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>W jakich regionach platformy Azure jest dostępna indeksatora wideo?

Możesz zobaczyć, które regiony platformy Azure jest dostępna na Video Indexer [regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) strony.

### <a name="what-is-the-sla-for-video-indexer"></a>Co to jest umowa SLA dla usługi Video Indexer?

Bezpłatna wersja próbna indeksatora wideo jest objęta umową SLA. Usługa Azure Media umowa SLA obejmuje Video Indexer.

Informacje można znaleźć na [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_0/) strony.

## <a name="billing-questions"></a>Pytania dotyczące rozliczeń

### <a name="how-much-does-video-indexer-cost"></a>Ile kosztuje indeksatora wideo

Usługa Video Indexer używa prostego płatności za rzeczywiste użycie model cenowy oparty na czasie trwania wejściowe treści. Dodatkowe opłaty mogą mieć zastosowanie dla kodowanie, przesyłanie strumieniowe, magazynu, użycia sieci i media użytych jednostek zastrzeżonych. Obecnego modelu cen można znaleźć [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) strony.

### <a name="does-video-indexer-offer-a-free-trial"></a>Video Indexer oferuje bezpłatną wersję próbną?

Tak, Video Indexer oferuje bezpłatną wersję próbną, który znajduje się w regionie wschodnie stany USA Azure i zapewnia pełną obsługę oraz funkcje interfejsów API. Obowiązuje przydział 10 godzin warte filmów wideo dla użytkowników witryny sieci Web i 40 godzin dla użytkowników interfejsu API. 

## <a name="security-questions"></a>Pytania zabezpieczające

### <a name="are-audio-and-video-files-indexed-by-video-indexer-stored"></a>Czy plików audio i wideo są indeksowane przez usługę Video Indexer przechowywane?

Tak, o ile nie usuniesz plik przez indeksator wideo albo za pomocą indeksatora wideo witryny sieci Web lub interfejsu API, będą przechowywane pliki audio i wideo. Dla bezpłatnej wersji próbnej, wideo i audio plików, które indeksu będą przechowywane w regionie platformy Azure, wschodnie stany USA. W przeciwnym razie będą przechowywane pliki audio i wideo na koncie magazynu w swojej subskrypcji platformy Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Czy mogę usunąć pliki, które są przechowywane w portalu indeksatora wideo

Tak, zawsze możesz usunąć pliki audio i wideo, a także szczegółowe dane z Video Indexer. Po usunięciu plików jest trwale usunięte z Video Indexer i wszędzie tam, gdzie Video Indexer przechowywany plik (Azure region wschodnie stany USA do konta wersji próbnej i konto magazynu w subskrypcji platformy Azure, w przeciwnym razie).

### <a name="who-has-access-to-my-audio-and-video-files-and-that-have-been-indexed-andor-stored-by-video-indexer"></a>Kto ma dostęp do moich plików audio i wideo i który indeksowane lub przechowywane przez usługę Video Indexer?

Tak, zawsze możesz usunąć pliki audio i wideo, a także szczegółowe dane z Video Indexer. Po usunięciu plików jest trwale usunięte z Video Indexer i wszędzie tam, gdzie Video Indexer przechowywany plik (Azure region wschodnie stany USA do konta wersji próbnej i konto magazynu w subskrypcji platformy Azure, w przeciwnym razie).

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Czy mogę kontrolować dostęp użytkowników z moim kontem indeksatora wideo?

Tak, możesz wybrać, kto otrzymał zaproszenie do konta.

### <a name="who-has-access-to-the-insights-that-were-extracted-from-my-audio-and-video-files-by-video-indexer"></a>Kto ma dostęp do szczegółowych informacji, które zostały wyodrębnione pliki audio i wideo przez usługę Video Indexer?

Filmy wideo, które mają publiczne, zgodnie z jego ustawieniem prywatności może zostać oceniony przez każdy, kto ma link do filmu wideo i jego szczegółowych informacji. Filmy wideo, które mają prywatny jako jego ustawienia prywatności może zostać oceniony jedynie przez użytkowników, którzy zostali zaproszeni do konta filmu wideo.

### <a name="do-i-still-own-my-content-that-have-been-indexed-and-stored-by-video-indexer"></a>Czy nadal posiadają z Moja zawartość, która indeksowana i przechowywane przez indeksator wideo?

Tak, na [warunki dotyczące usług Online Azure](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), jesteś właścicielem zawartości.

### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Zawartość jest indeksowane przez indeksator wideo, przechowywane w regionie platformy Azure, której używam Video Indexer?

Tak, zawartość i jego szczegółowe informacje będą przechowywane w obrębie regionu świadczenia usługi Azure, chyba że masz ręcznej konfiguracji w ramach subskrypcji Azure, który używa wielu regionach platformy Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Co to są zasady ochrony prywatności, indeksatora wideo?

Usługa Video Indexer jest objęta [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="api-questions"></a>Pytania dotyczące interfejsu API

### <a name="what-are-the-differences-in-capability-on-the-video-indexer-website-versus-the-video-indexer-api"></a>Jakie są różnice funkcji w witrynie sieci Web indeksatora wideo w porównaniu z interfejsu API indeksatora wideo?

Za pomocą [Video Indexer](https://www.videoindexer.com) witryny sieci Web, można wyszukiwania biblioteki filmów wideo, analizować szczegółowe informacje, dostosowywanie modeli, skonfiguruj swoje konto i edytować filmy wideo z łatwością. Za pomocą niezawodnego [interfejsu REST API indeksatora wideo](https://api-portal.videoindexer.ai/), można zintegrować z dowolną infrastrukturą lub osadzanie widżetów bezpośrednio w swojej własnej witryny sieci Web lub aplikacji.

### <a name="what-apis-does-video-indexer-offer"></a>Interfejsy API oferuje usługa Video Indexer

Można znaleźć informacje na temat korzystania z interfejsów API indeksatora wideo na [portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/)

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak rozpocząć korzystanie z interfejsu API indeksatora wideo?

Postępuj zgodnie z [samouczek: wprowadzenie do interfejsu API indeksatora wideo](video-indexer-use-apis.md).

### <a name="what-is-an-access-token"></a>Co to jest token dostępu?

Interfejsu API indeksatora wideo zawiera autoryzacji interfejsu API i operacje interfejsu API. Interfejs API autoryzacji zawiera wywołania, które zapewniają tokenu dostępu. Każde wywołanie interfejsu API operacji powinno być skojarzone z tokenem dostępu zgodnym z zakresem autoryzacji wywołania.

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaka jest różnica między tokenu dostępu do konta, token dostępu użytkownika i token dostępu wideo?

Reprezentują one zakresu autoryzacji wywołania.

* Na poziomie użytkownika — tokeny dostępu na poziomie użytkownika umożliwiają wykonywanie operacji na poziomie użytkownika. Na przykład uzyskiwanie informacji o skojarzonych kontach.
* Poziom konta — tokenów dostępu na poziomie konta pozwalają na wykonywanie operacji na poziomie konta lub wideo. Na przykład przekazanie pliku wideo, zwrócenie listy wszystkich plików wideo, pobranie szczegółowych informacji o wideo itp.
* Poziom wideo — tokeny dostępu na poziomie wideo umożliwiają wykonywanie operacji na określonym wideo. Na przykład pobranie szczegółowych informacji o wideo, pobranie napisów, pobranie widżetów itp.

Można kontrolować, czy tokeny te są przeznaczone tylko do odczytu czy umożliwiają one, edytowanie, określając allowEdit = PRAWDA/FAŁSZ.

### <a name="why-do-i-need-access-tokens-when-using-the-video-indexer-apis"></a>Dlaczego muszę tokenów dostępu przy użyciu interfejsów API indeksatora wideo?

Tokeny dostępu są niezbędne do używania interfejsów API indeksatora wideo ze względów bezpieczeństwa. Daje to gwarancję, że dowolne wywołania zostaną wprowadzone przez Ciebie lub tych, którzy mają uprawnienia dostępu do Twojego konta. 

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak często należy uzyskać nowy token dostępu Kiedy wygaśnie tokenów dostępu?

Tokeny dostępu wygasa co godzinę, więc należy wygenerować nowy token dostępu, co godzinę. 

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](video-indexer-overview.md)
