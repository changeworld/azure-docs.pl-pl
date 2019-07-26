---
title: Co to jest wersja zapoznawcza usługi Azure Data Share
description: Omówienie usługi Azure Data Share w wersji zapoznawczej
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 11f32b1f1349ef0f9826f95832648e6949cc2f8c
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421387"
---
# <a name="what-is-azure-data-share-preview"></a>Co to jest Azure Data Share (wersja zapoznawcza)?

W dzisiejszym świecie dane są wyświetlane jako kluczowy zasób strategiczny, który wiele organizacji musi po prostu i bezpiecznie udostępnić klientom i partnerom. Istnieje wiele sposobów, w których obecnie klienci wykonują te czynności, w tym za pośrednictwem protokołu FTP, poczty e-mail i interfejsów API, aby nazwać kilka. Organizacje mogą łatwo utracić śledzenie osób, w których udostępnili swoje dane. Udostępnianie danych przy użyciu protokołu FTP lub w ramach infrastruktury interfejsu API jest często kosztowne do aprowizacji i administrowania. Istnieją narzuty związane z zarządzaniem za pomocą tych metod udostępniania na dużą skalę. 

Wiele organizacji musi być odpowiedzialnych za udostępnione dane. Oprócz odpowiedzialności wiele organizacji chce mieć w prosty sposób kontrolować i monitorować wszystkie współużytkowanie danych oraz zarządzać nimi. W dzisiejszym świecie, w którym oczekuje się, że dane będą nadal wzrastać w wykładniczym tempie, organizacje muszą mieć prosty sposób udostępniania danych Big Data. Klienci wymagają najnowszych danych, aby mieć pewność, że będą oni mogli uzyskiwać szczegółowe informacje.

Usługa Azure Data Share (wersja zapoznawcza) umożliwia organizacjom proste i bezpieczne udostępnianie danych wielu klientom i partnerom. Wystarczy kilka kliknięć, aby zainicjować nowe konto udostępniania danych, dodać zestawy danych i zaprosić klientów i partnerów do udziału danych. Dostawcy danych zawsze kontrolują udostępnione dane. Udział danych platformy Azure ułatwia zarządzanie i monitorowanie danych, które zostały udostępnione, kiedy i przez kogo. 

Dostawca danych może zachować kontrolę nad sposobem obsługi ich danych, określając warunki użytkowania ich udziału danych. Przed uzyskaniem danych klient danych musi zaakceptować te warunki. Dostawcy danych mogą określać częstotliwość, z jaką odbiorcy danych otrzymują aktualizacje. Dostęp do nowych aktualizacji można odwołać w dowolnym momencie przez dostawcę danych. 

Udział danych platformy Azure ułatwia zwiększenie wglądu w dane, ułatwiając łączenie danych od innych firm w celu wzbogacania scenariuszy analizy i AI. Łatwo twórz, Przetwarzaj i analizuj dane udostępniane za pomocą udziału danych platformy Azure. 

Dostawca danych i odbiorca danych muszą mieć subskrypcję platformy Azure, aby udostępniać i odbierać dane. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenariusze dotyczące udziału danych platformy Azure

Udział danych platformy Azure może być używany w wielu różnych branżach. Na przykład sprzedawca detaliczny może chcieć udostępnić ostatni punkt danych sprzedaży swoim dostawcom. Korzystając z udziału danych platformy Azure, sprzedawca detaliczny może skonfigurować udział danych zawierający punkt danych sprzedaży dla wszystkich dostawców i dzielić się sprzedażą co godzinę lub codziennie. 

Udział danych platformy Azure może być również używany do ustanawiania rynku danych dla konkretnej branży. Na przykład organizacja rządowa lub instytucja badawcza, która regularnie udostępnia dane anonimowe na temat wzrostu populacji innym podmiotom. 

Innym przypadkiem użycia usługi Azure Data Share jest ustanowienie konsorcjum danych. Na przykład niektóre różne instytucje badawcze mogą udostępniać dane za pomocą jednej zaufanej treści. Dane są analizowane, agregowane lub przetwarzane przy użyciu narzędzi analitycznych platformy Azure, a następnie udostępniane zainteresowanym stronom. 

## <a name="how-it-works"></a>Jak to działa

Udział danych platformy Azure używa podejścia do udostępniania opartego na migawce, w którym dane są przenoszone z subskrypcji platformy Azure dostawcy danych i gruntów w ramach subskrypcji platformy Azure klienta danych. Jako dostawca danych zastrzegasz udział danych i zapraszasz odbiorców do udziału danych. Odbiorcy danych otrzymują zaproszenie do udziału danych za pośrednictwem poczty e-mail. Gdy odbiorca danych zaakceptuje zaproszenie, może wyzwolić pełną migawkę udostępnionych im danych. Te dane są odbierane na koncie magazynu odbiorcy danych. Odbiorcy danych mogą odbierać regularne, przyrostowe aktualizacje danych, które są do nich udostępniane, aby zawsze miały najnowszą wersję danych. 

Dostawcy danych mogą oferować klientom danych przyrostowe aktualizacje danych, które są im udostępniane przez harmonogram migawek. Harmonogramy migawek są oferowane co godzinę lub codziennie. Gdy odbiorca danych akceptuje i konfiguruje udział danych, może subskrybować harmonogram migawek. Jest to przydatne w scenariuszach, w których dane udostępnione są regularnie aktualizowane, a odbiorca danych wymaga najnowszych danych. 

![przepływ udostępniania danych](media/data-share-flow.png)

Gdy odbiorca danych akceptuje udział danych, może odbierać dane na koncie magazynu. Na przykład jeśli dostawca danych udostępnia dane za pomocą usługi Azure Blob Storage, odbiorca danych może odbierać te dane w Azure Data Lake Store. 

## <a name="key-capabilities"></a>Najważniejsze możliwości

Udział danych platformy Azure umożliwia dostawcom danych:

* Udostępnianie danych z usługi Azure Storage i Azure Data Lake Store klientom i partnerom spoza organizacji

* Śledź użytkowników, którym udostępniono Twoje dane

* Jak często odbiorcy danych otrzymują aktualizacje danych

* Zezwól klientom na ściąganie najnowszej wersji danych zgodnie z potrzebami lub Zezwól im na automatyczne otrzymywanie zmian przyrostowych danych z interwałem zdefiniowanym przez użytkownika

Udział danych platformy Azure umożliwia użytkownikom danych: 

* Wyświetl opis typu danych, które są udostępniane

* Wyświetl warunki użytkowania danych

* Akceptowanie lub odrzucanie zaproszenia udziału danych platformy Azure

* Wyzwól pełną lub przyrostową migawkę udziału danych, który organizacja udostępniła Tobie

* Zasubskrybuj udział danych, aby otrzymać najnowszą kopię danych za pomocą przyrostowej kopii migawek

* Akceptuj dane udostępnione Tobie w usłudze Azure Blob Storage lub na koncie usługi Azure Data Lake Gen2

Wszystkie najważniejsze możliwości wymienione powyżej są obsługiwane za pośrednictwem platformy Azure lub interfejsów API REST. Aby uzyskać więcej informacji na temat korzystania z usługi Azure Data Share za pośrednictwem interfejsów API REST, zapoznaj się z dokumentacją referencyjną. 

## <a name="security"></a>Bezpieczeństwo

Udział danych platformy Azure wykorzystuje podstawowe zabezpieczenia oferowane przez platformę Azure w celu ochrony danych przechowywanych i przesyłanych. Dane są szyfrowane w spoczynku, gdzie są obsługiwane przez podstawowy mechanizm magazynu. Dane są również szyfrowane podczas przesyłania. Metadane dotyczące udziału danych są również szyfrowane w czasie spoczynku i podczas przesyłania. 

Kontrolę dostępu można ustawić na poziomie zasobów udziału danych platformy Azure, aby upewnić się, że jest on dostępny dla autoryzowanych. 

Udział danych platformy Azure wykorzystuje zarządzane tożsamości dla zasobów platformy Azure (wcześniej znanych jako MSIs) do automatycznego zarządzania tożsamościami w Azure Active Directory. Zarządzane tożsamości dla zasobów platformy Azure są wykorzystywane do uzyskiwania dostępu do kont magazynu używanych na potrzeby udostępniania danych. Nie ma wymiany poświadczeń między dostawcą danych i konsumentem danych. Więcej informacji można znaleźć na [stronie zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Obsługiwane regiony

Listę regionów świadczenia usługi Azure, które udostępniają udział danych platformy Azure, można znaleźć na stronie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/) i wyszukać udział danych platformy Azure. 

Udział danych platformy Azure nie przechowuje żadnych danych. Dane są przechowywane na źródłowych kontach magazynu, które są udostępniane. Na przykład, jeśli producent danych przechowuje swoje dane na koncie Azure Data Lake Store zlokalizowanym w regionie zachodnie stany USA, w którym są przechowywane dane. Jeśli udostępniają dane za pomocą konta usługi Azure Storage znajdującego się w regionie Europa Zachodnia, dane są przekazywane bezpośrednio do konta usługi Azure Storage znajdującego się w Europie Zachodniej. 

Usługa udziału danych platformy Azure nie musi być dostępna w Twoim regionie, aby można było korzystać z usługi. Jeśli na przykład dane są przechowywane na koncie usługi Azure Storage znajdującym się w regionie, w którym udział danych platformy Azure nie jest jeszcze dostępny, można nadal korzystać z usługi do udostępniania danych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
