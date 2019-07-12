---
title: Co to jest Azure udostępniania danych w wersji zapoznawczej
description: Omówienie udostępniania danych Azure w wersji zapoznawczej
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7d4e51ec9564bfb123cf73d9fe89d040f42fe650
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807542"
---
# <a name="what-is-azure-data-share-preview"></a>Co to jest Azure Data Share (wersja zapoznawcza)?

W dzisiejszym świecie dane są wyświetlane jako zasobów strategicznych klucza, który w wielu organizacjach muszą po prostu i bezpiecznego udostępniania ich klientom i partnerom. Istnieje wiele sposobów, że klienci nie tej dzisiaj, w tym za pośrednictwem protokołu FTP, poczty e-mail, interfejsy API kilka. Organizacje mogą łatwo utratę informacji o który którym z kolei udostępniły swoje dane za pomocą. Udostępnianie danych za pośrednictwem protokołu FTP lub infrastrukturą własnych interfejsów API jest często kosztowne do aprowizacji i administrowania. Jest związana z zarządzaniem związanych z użyciem tych metod udostępniania na dużą skalę. 

W wielu organizacjach trzeba accountable dla danych, które mają wspólne. Oprócz odpowiedzialność wiele organizacji chce mieć możliwość kontrolowania, zarządzanie i monitorowanie wszystkich swoich danych, udostępnianie w prosty sposób. W świecie dzisiaj, gdzie dane oczekuje się, aby kontynuować rośnie w tempie wykładniczym, organizacje muszą prosty sposób udostępniać dane big data. Klienci żądają najbardziej aktualne dane, aby upewnić się, że są w stanie do uzyskiwania szczegółowych informacji w odpowiednim czasie.

Podgląd udostępnianie danych platformy Azure umożliwia organizacjom po prostu i bezpiecznie udostępniać dane wielu klientów i partnerów. Za pomocą kilku kliknięć można aprowizować nowe konto udostępniania danych, Dodaj zestawy danych i zaprosić do udziału danych, klientom i partnerom. Dostawcy danych są zawsze kontrolę nad danymi, które mają wspólne. Udziale danych na platformie Azure upraszcza zarządzanie i monitorowanie, jakie dane został udostępniony, kiedy i przez kogo. 

Dostawca danych może pozostać w kontroli sposobu obsługi danych przez określenie warunków użytkowania przeznaczonych do udziału danych. Odbiorca danych musi je zaakceptować, zanim będzie mógł odbierać dane. Dostawcy danych można określić częstotliwość, w którym konsumenci danych, ich otrzymywać aktualizacje. Dostęp do nowych aktualizacji można odwołać w dowolnym momencie przez dostawcę danych. 

Udział danych platformy Azure pomaga zwiększyć wgląd, co ułatwia łączenie danych z innych firm w celu wzbogacenia analizy i scenariuszy sztucznej Inteligencji. Łatwo narzędzia power ot analiz platformy Azure do przygotowania, przetwarzania i analizowania danych udostępnianych za pomocą udziału danych platformy Azure. 

## <a name="scenarios-for-azure-data-share"></a>Scenariusze dotyczące udostępniania danych na platformie Azure

Udział danych platformy Azure może służyć w wielu różnych branż. Na przykład Sprzedawca może być udostępnianie ostatni punkt danych sprzedaży z ich dostawcami. Za pomocą udostępniania danych platformy Azure, sprzedawca może skonfigurować udział danych zawierającego punkt danych sprzedaży dla wszystkich swoich dostawców i udostępniać sprzedaży na podstawie godzinowo lub dziennie. 

Udział danych platformy Azure można również nawiązać rynku dane dla określonych branż. Na przykład dla instytucji rządowych lub instytucja research, która regularnie udostępnia anonimowe dane dotyczące przyrostu osobom trzecim. 

Przypadek użycia innego udziału danych platformy Azure jest ustanowienie konsorcjum danych. Na przykład szereg różnych badawczymi mogą udostępniać dane do jednej jednostki zaufanych. Dane są analizowane, zagregowane lub przetworzona za pomocą narzędzi analitycznych platformy Azure i następnie udostępniany zainteresowanych stron. 

## <a name="how-it-works"></a>Jak to działa

Udziale na platformie Azure danych użyto opartych na migawkach udostępniania metody, gdzie dane są przenoszone z dostawcy danych subskrypcji platformy Azure i umieszczać swoje dokumenty w subskrypcji platformy Azure dla konsumentów danych. Jako dostawca danych aprowizować udziału danych i Zaproś odbiorców do udziału danych. Konsumenci danych otrzymać zaproszenie do udziału danych za pośrednictwem poczty e-mail. Gdy konsument danych akceptuje zaproszenie, mogą wyzwalać pełna migawka danych udostępnianych udostępnione. Odebrano te dane do konta magazynu danych klientów. Konsumenci danych mogą otrzymać regularnych, przyrostową metodyką zgodną aktualizacje danych udostępniono tak, aby zawsze mieć najnowszą wersję dane. 

Dostawcy danych może oferować swoje dane aktualizacje przyrostowe konsumenci danych udostępnionych z nimi za pomocą harmonogramu migawek. Harmonogramy migawek są oferowane na co godzinę lub codziennie. Gdy konsument danych akceptuje i konfiguruje ich udziału danych, mogą subskrybować harmonogramu migawek. Jest to przydatne w scenariuszach, gdzie udostępnione dane są aktualizowane w regularnych odstępach czasu i danych, musi najbardziej aktualne dane. 

![Przepływ udostępniania danych](media/data-share-flow.png)

Gdy konsument danych akceptuje udziału danych, będą mogły odbierać dane na koncie magazynu, ich zalety. Na przykład jeśli dostawca danych udostępnia dane za pomocą usługi Azure Blob Storage, odbiorcy danych mogą otrzymać te dane w usłudze Azure Data Lake Store. 

## <a name="key-capabilities"></a>Najważniejsze możliwości

Udział danych platformy Azure umożliwiają dostawcom danych:

* Udostępnianie danych z usługi Azure Storage i Azure Data Lake Store z klientami i partnerami spoza organizacji

* Sprawdzaj, z kim udostępnionych danych za pomocą

* Jak często dane klientów są otrzymywanie aktualizacji dotyczących danych

* Klienci będą mieli możliwość pobierania najnowszej wersji usługi danych, zgodnie z potrzebami lub zezwolić im na automatycznie otrzymują przyrostowe zmiany danych w odstępach czasu zdefiniowanych przez użytkownika

Udział danych platformy Azure umożliwia danych: 

* Wyświetl opis typu danych, które są udostępniane

* Wyświetlanie warunków użytkowania dla danych

* Zaakceptuj lub Odrzuć zaproszenie udostępniania danych platformy Azure

* Wyzwalanie pełną lub przyrostową migawkę udziału danych, które organizacja udostępnił Ci

* Subskrybowanie do udziału danych, aby otrzymać najnowszą kopię danych przy użyciu migawek przyrostowych kopii

* Akceptowanie danych udostępnionych Ci na konto usługi Azure Blob Storage lub Azure Data Lake Gen2

Wszystkie kluczowe funkcje wymienione powyżej są obsługiwane za pośrednictwem platformy Azure lub za pośrednictwem interfejsów API REST. Aby uzyskać więcej informacji na temat korzystania z udziału danych platformy Azure za pośrednictwem interfejsów API REST zapoznaj się z naszej dokumentacji. 

## <a name="security"></a>Bezpieczeństwo

Udziale na platformie Azure danych korzysta z podstawowych zabezpieczeń, pozwalającą na platformie Azure w celu ochrony danych magazynowanych i przesyłanych. Dane są szyfrowane w stanie spoczynku, jeśli są obsługiwane przez podstawowego mechanizmu przechowywania. Dane również są szyfrowane podczas przesyłania. Metadane dotyczące udziału danych również są szyfrowane podczas przechowywania i podczas przesyłania. 

Aby upewnić się, że jest on dostępny dla tych, które są autoryzowane na poziomie zasobów udziału danych platformy Azure można ustawić kontroli dostępu. 

Udziale na platformie Azure danych korzysta z tożsamości zarządzanych dla zasobów platformy Azure (wcześniej znane jako plików msi) do zarządzania tożsamościami automatyczne w usłudze Azure Active Directory. Zarządzanych tożsamości dla zasobów platformy Azure są używane do uzyskiwania dostępu do konta magazynu, które są używane do udostępniania danych. Nie ma żadnych wymiany poświadczeń między dostawcą danych i konsumentów danych. Aby uzyskać więcej informacji, zobacz [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 

## <a name="supported-regions"></a>Obsługiwane regiony

Aby uzyskać listę regionów świadczenia usługi Azure, które udostępniają udziału danych platformy Azure, zapoznaj się [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) stronie i wyszukaj udziału danych platformy Azure. 

Udział danych platformy Azure nie przechowuje żadnych danych. Dane są przechowywane w podstawowe konta magazynu, które są udostępniane. Na przykład jeśli producentów danych są przechowywane ich dane na koncie usługi Azure Data Lake Store znajduje się w regionie zachodnie stany USA, to gdy dane są przechowywane. Jeśli są one udostępniania danych z konta usługi Azure Storage znajduje się w regionie Europa Zachodnia, dane są przesyłane bezpośrednio z konta usługi Azure Storage, znajdującego się w regionie Europa Zachodnia. 

Usługa udziału danych platformy Azure nie ma być dostępna w danym regionie, aby korzystać z usługi. Na przykład jeśli masz dane przechowywane na koncie usługi Azure Storage znajduje się w regionie, w którym udostępnianie danych platformy Azure nie jest jeszcze dostępny, można wykorzystywać usługę, aby udostępniać swoje dane. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uruchomić, udostępnianie danych, w dalszym ciągu [udostępnianie danych](share-your-data.md) samouczka.
