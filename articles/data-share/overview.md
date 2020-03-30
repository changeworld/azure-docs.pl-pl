---
title: Co to jest usługa Azure Data Share?
description: Dowiedz się więcej o udostępnianiu danych w prosty i bezpieczny sposób wielu klientom i partnerom korzystającym z usługi Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 363feda1409d2bb54e60d1b2168cba38f2a8a41c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621850"
---
# <a name="what-is-azure-data-share"></a>Co to jest usługa Azure Data Share?

W dzisiejszym świecie dane są postrzegane jako kluczowy atut strategiczny, który wiele organizacji musi w prosty i bezpieczny sposób udostępniać swoim klientom i partnerom. Istnieje wiele sposobów, że klienci to zrobić dzisiaj, w tym za pośrednictwem FTP, e-mail, interfejsów API, aby wymienić tylko kilka. Organizacje mogą łatwo stracić możliwość śledzenia tego, komu udostępniły swoje dane. Udostępnianie danych za pośrednictwem protokołu FTP lub wstawanie własnej infrastruktury interfejsu API jest często kosztowne w udostępnianiu i administrowaniu. Istnieje obciążenie związane z zarządzaniem związane z wykorzystaniem tych metod udostępniania na dużą skalę. 

Wiele organizacji musi odpowiadać za udostępnione dane. Oprócz odpowiedzialności wiele organizacji chce mieć możliwość prostego kontrolowania, zarządzania i monitorowania wszystkich udostępnianych danych. W dzisiejszym świecie, gdzie oczekuje się, że dane będą nadal rosnąć w wykładniczym tempie, organizacje potrzebują prostego sposobu udostępniania dużych zbiorów danych. Klienci żądają najbardziej aktualnych danych, aby upewnić się, że są w stanie uzyskać szczegółowe informacje w odpowiednim czasie.

Usługa Azure Data Share umożliwia organizacjom łatwe i bezpieczne udostępnianie danych wielu klientom i partnerom. Za pomocą kilku kliknięć można aprowizować nowe konto udostępniania danych, dodawać zestawy danych i zapraszać klientów i partnerów do udostępniania danych. Dostawcy danych zawsze kontrolują dane, które udostępnili. Usługa Azure Data Share ułatwia zarządzanie i monitorowanie, jakie dane zostały udostępnione, kiedy i przez kogo. 

Dostawca danych może zachować kontrolę nad tym, jak ich dane są obsługiwane przez określenie warunków użytkowania ich udziału w danych. Uzyskanie danych jest uzależnione od tego, czy konsument danych zaakceptował te warunki. Dostawcy danych mogą określać częstotliwość, z jaką konsumenci danych będą otrzymywać aktualizacje. Dostawca danych może w dowolnym momencie odwołać dostęp do nowych aktualizacji. 

Usługa Azure Data Share pomaga udoskonalić szczegółowe informacje, ułatwiając łączenie danych od stron trzecich w celu wzbogacenia analiz i scenariuszy sztucznej inteligencji. Łatwe korzystanie z możliwości narzędzi analitycznych platformy Azure do przygotowywania, przetwarzania i analizowania danych udostępnionych przy użyciu usługi Azure Data Share. 

Dostawca danych i konsument danych muszą mieć subskrypcję platformy Azure, aby udostępniać i odbierać dane. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenariusze udostępniania danych platformy Azure

Usługa Azure Data Share może być używana w wielu różnych branżach. Na przykład sprzedawca detaliczny może chcieć udostępnić swoim dostawcom najnowsze dane dotyczące punktów sprzedaży. Korzystając z usługi Azure Data Share, sprzedawca detaliczny może skonfigurować udział danych zawierający dane o punktach sprzedaży dla wszystkich dostawców i udostępniać sprzedaż co godzinę lub codziennie. 

Usługa Azure Data Share może również służyć do ustanawiania portalu danych dla określonej branży. Na przykład rząd lub instytucja badawcza, która regularnie udostępnia zanonimizowane dane o wzroście populacji stronom trzecim. 

Innym przypadkiem użycia usługi Azure Data Share jest ustanowienie konsorcjum danych. Na przykład wiele różnych instytucji badawczych może udostępniać dane jednemu zaufanemu organowi. Dane są analizowane, agregowane lub przetwarzane przy użyciu narzędzi analitycznych platformy Azure, a następnie udostępniane zainteresowanym stronom. 

## <a name="how-it-works"></a>Jak to działa

Usługa Azure Data Share oferuje obecnie udostępnianie oparte na migawkach i udostępnianie w miejscu. 

W udostępnianiu opartym na migawkach dane są przenoszone z subskrypcji platformy Azure dostawcy danych i lądują w subskrypcji platformy Azure konsumenta danych. Jako dostawca danych aprowizujesz udostępnianie danych i zapraszasz odbiorców do udziału danych. Konsumenci danych otrzymują zaproszenie do udostępniania danych za pośrednictwem poczty e-mail. Gdy konsument danych zaakceptuje zaproszenie, może wyzwolić pełną migawkę danych udostępnionych im. Te dane są odbierane na konto przechowywania danych konsumentów. Konsumenci danych mogą otrzymywać regularne, przyrostowe aktualizacje danych udostępnionych im, dzięki czemu zawsze mają najnowszą wersję danych. 

Dostawcy danych mogą oferować swoim odbiorcom danych przyrostowe aktualizacje danych udostępnionych im za pośrednictwem harmonogramu migawek. Harmonogramy migawek są oferowane co godzinę lub codziennie. Gdy konsument danych akceptuje i konfiguruje swój udział danych, może subskrybować harmonogram migawki. Jest to korzystne w scenariuszach, w których udostępnione dane są regularnie aktualizowane, a konsument danych potrzebuje najbardziej aktualnych danych. 

![przepływ udziału danych](media/data-share-flow.png)

Gdy konsument danych akceptuje udostępnianie danych, może odbierać dane w wybranym przez siebie magazynie danych. Na przykład jeśli dostawca danych udostępnia dane przy użyciu usługi Azure Blob Storage, konsument danych może odbierać te dane w usłudze Azure Data Lake Store. Podobnie jeśli dostawca danych udostępnia dane z usługi Azure SQL Data Warehouse, konsument danych może wybrać, czy chcą odbierać dane do magazynu usługi Azure Data Lake Store, bazy danych SQL azure lub usługi Azure SQL Data Warehouse. W przypadku udostępniania ze źródeł opartych na języku SQL konsument danych może również wybrać, czy odbiera dane w parkietach czy csv. 

Dzięki udostępnianiu danych w miejscu dostawcy danych mogą udostępniać dane tam, gdzie się znajdują, bez kopiowania danych. Po nawiązaniu relacji udostępniania za pośrednictwem przepływu zaproszenia tworzone jest łącze symboliczne między źródłowym magazynem danych dostawcy danych a docelowym magazynem danych konsumenta danych. Konsument danych może odczytywać i wysyłać zapytania do danych w czasie rzeczywistym przy użyciu własnego magazynu danych. Zmiany w magazynie danych źródłowych są natychmiast dostępne dla konsumenta danych. Udostępnianie w miejscu jest obecnie w wersji zapoznawczej dla Usługi Azure Data Explorer.

## <a name="key-capabilities"></a>Najważniejsze możliwości

Usługa Azure Data Share umożliwia dostawcom danych:

* Udostępnianie danych z listy [obsługiwanych magazynów danych](supported-data-stores.md) klientom i partnerom spoza organizacji

* Śledź, komu udostępniłeś swoje dane

* Wybór migawki lub udostępniania w miejscu

* Jak często twoje dane są odbierane przez konsumentów aktualizacji twoich danych

* Zezwalaj klientom na ściąganie najnowszej wersji danych w razie potrzeby lub zezwalaj im na automatyczne otrzymywanie przyrostowych zmian danych w określonym przez Ciebie odstępach czasu.

Usługa Azure Data Share umożliwia użytkownikom danych: 

* Wyświetlanie opisu typu udostępnianych danych

* Wyświetlanie warunków użytkowania danych

* Akceptowanie lub odrzucanie zaproszenia do udostępniania danych platformy Azure

* Zaakceptuj dane udostępnione do [obsługiwanego magazynu danych](supported-data-stores.md).

* Wyzwalanie pełnej lub przyrostowej migawki udziału danych udostępnionego przez organizację

* Subskrybowanie udziału danych w celu odbierania najnowszej kopii danych za pomocą migawki przyrostowej

Wszystkie kluczowe funkcje wymienione powyżej są obsługiwane za pośrednictwem witryny Azure portal lub za pośrednictwem interfejsów API REST. Aby uzyskać więcej informacji na temat korzystania z usługi Azure Data Share za pośrednictwem interfejsów API REST, zapoznaj się z naszą dokumentacją referencyjną. 

## <a name="security"></a>Zabezpieczenia

Usługa Azure Data Share wykorzystuje podstawowe zabezpieczenia, które oferuje platforma Azure w celu ochrony danych w spoczynku i podczas przesyłania. Dane są szyfrowane w spoczynku, gdzie są obsługiwane przez podstawowy magazyn danych. Dane są również szyfrowane podczas przesyłania. Metadane dotyczące udziału danych są również szyfrowane w spoczynku i podczas przesyłania. 

Formanty dostępu można ustawić na poziomie zasobów usługi Azure Data Share, aby upewnić się, że jest dostępny dla osób, które są autoryzowane. 

Usługa Azure Data Share wykorzystuje tożsamości zarządzane dla zasobów platformy Azure (wcześniej znane jako MSI) do automatycznego zarządzania tożsamościami w usłudze Azure Active Directory. Tożsamości zarządzane dla zasobów platformy Azure są wykorzystywane do dostępu do magazynów danych, które są używane do udostępniania danych. Nie ma wymiany poświadczeń między dostawcą danych a konsumentem danych. Aby uzyskać więcej informacji, zobacz [stronę Tożsamości zarządzane dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Obsługiwane regiony

Aby uzyskać listę regionów platformy Azure, które udostępniają udostępnianie danych platformy Azure, zapoznaj się z [produktami dostępnymi na](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) stronie regionu i wyszukaj udział danych platformy Azure. 

Usługa Azure Data Share nie przechowuje kopii samych danych. Dane są przechowywane w magazynie danych źródłowych, który jest udostępniany. Na przykład jeśli producent danych przechowuje swoje dane na koncie usługi Azure Data Lake Store znajdującym się w zachodnie stany USA, czyli w obszarze przechowywania danych. Jeśli udostępniają dane z kontem usługi Azure Storage znajdującym się w Europie Zachodniej za pomocą migawki, zazwyczaj dane są przesyłane bezpośrednio do konta usługi Azure Storage znajdującego się w Europie Zachodniej.

Usługa Azure Data Share nie musi być dostępna w twoim regionie, aby korzystać z usługi. Na przykład jeśli masz dane przechowywane na koncie usługi Azure Storage znajdującym się w regionie, w którym udostępnianie danych platformy Azure nie jest jeszcze dostępne, nadal możesz wykorzystać tę usługę do udostępniania danych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka udostępniania danych.](share-your-data.md)
