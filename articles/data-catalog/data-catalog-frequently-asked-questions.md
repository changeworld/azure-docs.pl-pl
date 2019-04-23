---
title: Usługa Azure Data Catalog — często zadawane pytania
description: Często zadawane pytania dotyczące usługi Azure Data Catalog, w tym możliwości zarządzania, adnotacji i odnajdowanie źródeł danych.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 7c5241b9df23bb0334a39f2c684fd1bdff40b4c2
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998458"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Usługa Azure Data Catalog — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Co to jest usługa Azure Data Catalog?
Data Catalog to w pełni zarządzanej usługi hostowanej na platformie Microsoft Azure, która służy jako system rejestracji i odnajdywania firmowych źródeł danych. Usługa Data Catalog każdy użytkownik, od analityków do analitykom danych i deweloperom, można rejestrować, odnajdywanie, zrozumienie i używanie źródeł danych.

## <a name="what-customer-challenges-does-it-solve"></a>Jakie klienta wyzwań zrobi to rozwiązuje?
Wykaz danych adresów wyzwań związanych z źródła danych, odnajdowanie i "ciemnych dane", użytkownicy mogą odnaleźć i zrozumieć użycie źródeł danych przedsiębiorstwa.

## <a name="what-are-its-target-audiences"></a>Jakie są jej innych docelowych odbiorców?
Data Catalog jest przeznaczona dla użytkowników Pomoc techniczna i nietechniczna, w tym:

* Dane i deweloperów i specjalistów i analizy Biznesowej: Osoby, które są odpowiedzialne za tworzenie zawartości danych i analizy korzystać inne osoby.
* Stewards danych: Osoby, które mają wiedzę na temat danych, co oznacza i jak jest przeznaczony do użycia.
* Konsumenci danych: Ludzi, którzy będą mogli łatwo wykryć, zrozumienie i łączenie z danymi, których potrzebują do wykonania swojej pracy za pomocą narzędzia wybranych przez nich.
* Centralna IT: Ludzie, którzy muszą wprowadzić setkami źródeł danych stała się wykrywalna przez użytkowników biznesowych i którzy muszą utrzymywać nadzoru nad sposobu użycia danych i przez kogo.

## <a name="what-is-its-availability-by-region"></a>Co to jest jej dostępność według regionu?
Usługi wykaz danych jest obecnie dostępna w następujących centrach danych:

* Zachodnie stany USA
* Wschodnie stany USA
* Europa Zachodnia
* Europa Północna
* Australia Wschodnia
* Azja Południowo-Wschodnia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Jakie są jej ograniczenia na liczbę zasobów danych?
Wersja bezpłatna usługi Data Catalog jest ograniczona do 5000 zarejestrowanych zasobów danych.

Standard Edition usługi Data Catalog obsługuje do 100 000 zarejestrowanych zasobów danych.

Dowolny obiekt zarejestrowany w usłudze Data Catalog, takie jak tabele, widoki, pliki i raporty, traktowany jak zasobu danych.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Jakie są jej typy źródła i elementu zawartości obsługiwane dane?
Aby uzyskać listę aktualnie obsługiwanych źródeł danych, zobacz [DSR wykazu danych](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Jak żądanie obsługi dotyczące innego źródła danych?
Aby przesłać sugestie funkcji i inne opinie, przejdź do [wykazu danych na forum opinii platformy Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Jak rozpocząć pracę z usługą Data Catalog?
Najlepszym sposobem na rozpoczęcie pracy jest, przechodząc do [rozpoczęcie korzystania z wykazu danych](data-catalog-get-started.md). W tym artykule przedstawiono omówienie end-to-end możliwości usługi.

## <a name="how-do-i-register-my-data"></a>Jak zarejestrować Moje dane?
Aby zarejestrować swoje dane w usłudze Data Catalog:
1. W portalu usługi Azure Data Catalog w **Publikuj** obszaru, uruchomić narzędzie rejestracji usługi Azure Data Catalog. 
2. W narzędziu rejestracji źródła danych Data Catalog Zaloguj się przy użyciu tych samych poświadczeń umożliwia dostęp do portalu usługi Data Catalog.
3. Wybierz źródło danych i określonych zasobów, które chcesz zarejestrować.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Właściwości powoduje ona rozpakowania zasobów danych, które są zarejestrowane?
Określone właściwości ze źródła danych różnią się od źródła danych, ale ogólnie rzecz biorąc, Usługa publikowania w wykazie danych wyodrębnia następujące informacje:

* Nazwa elementu zawartości
* Typ elementu zawartości
* Opis elementu zawartości
* Nazwy atrybutu/kolumny
* Atrybut/Column — typy danych
* Opis atrybutu/kolumny

> [!IMPORTANT]
> Rejestrowanie zasobów danych w usłudze Data Catalog nie przenoszenie lub kopiowanie danych do chmury. Rejestrowanie zasobów ze źródła danych kopiuje metadane zasobów na platformie Azure, ale dane pozostają w istniejącej lokalizacji źródła danych. Wyjątkiem od tej reguły jest, jeśli użytkownik chce przekazać rekordów (wersja zapoznawcza) lub profil danych podczas rejestrowania zasoby. Jeśli dołączysz (wersja zapoznawcza), maksymalnie 20 rekordów są kopiowane z poszczególnych zasobów i przechowywane jako migawka w usłudze Data Catalog. Po dołączeniu profil danych agregują informacje jest obliczany i zawartych w metadanych, który jest przechowywany w katalogu. Zbiorcze informacje może zawierać rozmiaru tabel, procent wartości null dla kolumny lub wartości minimalna, maksymalna i średnia dla kolumn. 
>
>

> [!NOTE]
> W przypadku źródeł danych, takich jak SQL Server Analysis Services, który ma najwyższej jakości **opis** właściwości narzędzia rejestracji źródła danych wykazu danych wyodrębnia wartości tej właściwości. Dla relacyjnych baz danych programu SQL Server, w którym braku najwyższej jakości **opis** właściwości narzędzia rejestracji źródła danych wykazu danych wyodrębnianie wartości z **ms_description** rozszerzone właściwości obiekty i kolumn. Aby uzyskać więcej informacji, zobacz [przy użyciu właściwości rozszerzone na obiekty bazy danych](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Jak długo powinien upłynąć dla nowo zarejestrowanych zasobów pojawią się w katalogu?
Po zarejestrowaniu zasoby z usługą Data Catalog może być przez okres 5 – 10 sekund, zanim pojawią się w portalu usługi Data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Jak dodawać adnotacje i wzbogacanie metadanych dla mojego zarejestrowanych zasobów danych
Najprostszym sposobem na dostarczanie metadanych dla zarejestrowanych zasobów jest wybierz element zawartości w portalu usługi Data Catalog, a następnie wprowadź wartości w okienku właściwości lub okienku schematu dla wybranego obiektu.

Możesz także podać niektóre metadane, takie jak eksperci i tagi, podczas procesu rejestracji. Wartości podane w Usługa publikowania w wykazie danych mają zastosowanie do wszystkich zasobów, które są zarejestrowane w tym czasie. Zaznacz, aby wyświetlić ostatnio zarejestrowane obiekty w portalu, aby uwagi dodatkowe **Wyświetl Portal** przycisk na ostatnim ekranie narzędzia rejestracji źródła danych wykazu danych.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Jak usunąć dane zarejestrowane obiekty?
Możesz usunąć obiekt z wykazu danych, zaznaczając ten obiekt w portalu, a następnie klikając polecenie **Usuń** przycisku. Usuwanie obiektu usuwa jego metadane z wykazu danych, ale nie ma wpływu na źródle danych.

## <a name="what-is-an-expert"></a>Co to jest eksperta?
Ekspert jest osoba, która ma świadome perspektywy dotyczące obiektu danych. Każdy obiekt może mieć wiele ekspertów. Ekspert nie musi być użytkownikiem "owner" dla obiektu, ale jest po prostu kogoś, kto wie, jak i powinny być używane dane.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Jak udostępnić informacje z zespołem usługi Data Catalog I wystąpienia problemów?
Zgłaszanie problemów, udostępnianie informacji i zadawaj pytania, przejdź do [forum usługi Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Katalog działa z innym źródłem danych, które interesują mnie?
Aktywnie pracujemy nad dodaniem większą liczbą źródeł danych do usługi Data Catalog. Jeśli chcesz zobaczyć określonego źródła danych obsługiwane, nim znać (lub dźwięku dział pomocy technicznej Twojej, jeśli już zostały zaproponowane), przechodząc do [wykazu danych na forum opinii platformy Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Jak usługa Azure Data Catalog dotyczy wykaz danych w usłudze Power BI dla usługi Office 365?
Usługa Azure Data Catalog można traktować jako unowocześnienia funkcji wykazu danych w usłudze Power BI. Począwszy od platformy spring 2017 usługi Azure Data Catalog umożliwia Włącz udostępnianie i odnajdywanie zapytań w programie Excel 2016 i dodatku Power Query dla programu Excel. Możliwości usługi Data Catalog w programie Excel są dostępne dla użytkowników z licencjami usługi Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Jakie uprawnienia należy rejestrować zasoby z usługą Data Catalog?
Aby uruchomić narzędzie rejestracji wykazu danych, musisz mieć uprawnienia, w źródle danych, która pozwala na odczyt metadane ze źródła. Aby dołączyć również w wersji zapoznawczej, musi mieć uprawnienia umożliwiające odczyt danych z obiektów jest zarejestrowany.

Wykaz danych umożliwia także Administratorzy wykazu ograniczyć, którzy użytkownicy i grupy może dodawać metadane do wykazu. Aby uzyskać więcej informacji, zobacz [sposób zabezpieczania dostępu do wykazu danych i zasobów danych](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Wykaz danych będą dostępne dla lokalnego wdrożenia?
Data Catalog to usługa w chmurze, która może pracować w chmurze i lokalnych źródeł danych w celu dostarczenia rozwiązania hybrydowego źródło danych odnajdywania. Obecnie nie ma żadnych planów, wersja usługi Data Catalog, który jest uruchamiany w środowisku lokalnym.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Czy można wyodrębnić więcej lub bardziej rozbudowane metadane, ze źródeł danych, które można zarejestrować?
Aktywnie pracujemy nad rozszerzyć możliwości usługi Data Catalog. Jeśli chcesz mieć dodatkowych metadanych wyodrębnionych ze źródła danych podczas rejestracji, zasugerować jej (lub Zagłosuj na go, jeśli już zostały zaproponowane) w [wykazu danych na forum opinii platformy Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Jeśli chcesz uwzględnić kolumny/schematu metadanych, wersje zapoznawcze lub dane profilów dla źródeł danych, w którym metadanych nie jest wyodrębniony przez narzędzia rejestracji źródła danych, można użyć interfejsu API wykazu danych można dodać te metadane. Aby uzyskać więcej informacji, zobacz [interfejsu API REST usługi Azure Data Catalog](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Jak ograniczyć widoczność zasobów danych zarejestrowanych, tak aby tylko wybrani użytkownicy mogą odnajdywać?
Wybierz zasoby danych w usłudze Data Catalog, a następnie kliknij przycisk **Przejmij na własność** przycisku. Właściciele zasobów danych w usłudze Data Catalog można zmienić ustawień widoczności albo umożliwia wszystkim użytkownikom odnajdywać zasoby należące do firmy lub ograniczyć widoczność do określonych użytkowników. Aby uzyskać więcej informacji, zobacz [zarządzać zasobami danych w usłudze Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Jak zaktualizować rejestracja zasobu danych, tak aby zmiany w źródle danych są uwzględniane w katalogu?
Aby zaktualizować metadane zasobów danych, które zostały już zarejestrowane w wykazie, po prostu ponownie zarejestrować źródła danych, który zawiera zasoby. Wszelkie zmiany w źródle danych, takich jak kolumny jest dodane lub usunięte z tabel lub widoków, są aktualizowane w wykazie, ale adnotacji dostarczone przez użytkowników są przechowywane.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Moje pytanie nie ma tutaj odpowiedzi. Gdzie mam szukać odpowiedzi?
Przejdź do [forum usługi Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Pytania zadawane znajdzie swój sposób, w tym miejscu.
