---
title: Często zadawane pytania dotyczące usługi Azure Data Catalog
description: Często zadawane pytania dotyczące usługi Azure Data Catalog, w tym możliwości odnajdowania źródeł danych, adnotacji i zarządzania.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7dbb1b4a8b85350b8bf8a6df0c9035a19055444c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409023"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Azure Data Catalog
Ten artykuł zawiera odpowiedzi na często zadawane pytania związane z usługą Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Co to jest usługa Azure Data Catalog?
Wykaz danych to w pełni zarządzana usługa, hostowana na platformie Microsoft Azure, która służy jako system rejestracji i odnajdowania dla źródeł danych przedsiębiorstwa. Dzięki wykazowi danych każdy użytkownik, od analityków po analityków i deweloperów, może rejestrować, wykrywać, rozumieć i wykorzystywać źródła danych.

## <a name="what-customer-challenges-does-it-solve"></a>Jakie wyzwania dla klientów rozwiązuje?
Wykaz danych rozwiązuje wyzwania związane z odnajdywaniem źródeł danych i "ciemnymi danymi", dzięki czemu użytkownicy mogą wykrywać i rozumieć źródła danych przedsiębiorstwa.

## <a name="what-are-its-target-audiences"></a>Jakie są docelowe grupy odbiorców?
Katalog danych jest przeznaczony dla użytkowników technicznych i nietechnicznych, w tym:

* Deweloperzy danych oraz specjaliści bi i analityki: osoby odpowiedzialne za tworzenie treści danych i analiz dla innych użytkowników.
* Administratorzy danych: osoby, które mają wiedzę na temat danych, ich znaczeń i sposobu ich wykorzystania.
* Konsumenci danych: osoby, które muszą być w stanie łatwo odkrywać, rozumieć i łączyć się z danymi, których potrzebują do wykonywania swojej pracy, za pomocą wybranego przez siebie narzędzia.
* Centralna usługa IT: osoby, które muszą wykrywać setki źródeł danych przez użytkowników biznesowych i które muszą zachować nadzór nad tym, w jaki sposób dane są wykorzystywane i przez kogo.

## <a name="what-is-its-availability-by-region"></a>Jaka jest jego dostępność według regionu?
Usługi wykazu danych są obecnie dostępne w następujących centrach danych:

* Zachodnie stany USA
* Wschodnie stany USA
* Europa Zachodnia
* Europa Północna
* Australia Wschodnia
* Azja Południowo-Wschodnia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Jakie są jej limity dotyczące liczby zasobów danych?
Bezpłatna edycja wykazu danych jest ograniczona do 5000 zarejestrowanych zasobów danych.

Standardowa wersja wykazu danych obsługuje do 100 000 zarejestrowanych zasobów danych.

Każdy obiekt zarejestrowany w wykazie danych, taki jak tabele, widoki, pliki i raporty, jest liczony jako zasób danych.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Jakie są obsługiwane źródła danych i typy zasobów?
Aby uzyskać listę aktualnie obsługiwanych źródeł danych, zobacz [DsR wykazu danych](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Jak poprosić o pomoc techniczną dla innego źródła danych?
Aby przesłać żądania funkcji i inne opinie, przejdź do [wykazu danych na forach opinii platformy Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Dlaczego podczas próby utworzenia nowego katalogu jest już wyświetlony komunikat o błędzie *Katalog?*

W przypadku zakupu usługi Office 365 E5 z licencją usługi Power BI Pro firma Microsoft automatycznie tworzy katalog domyślny w regionie subskrypcji. Ten katalog korzysta z bezpłatnej jednostki SKU. Licencja użytkownika usługi Office 365 / Power BI jest zarządzana na stronie Administracja usługą Office 365. 

Jednak ten typ katalogu danych nie ma **opcji administratora** i nie jest widoczny w **witrynie Azure portal**. Nie można usunąć tego typu wykazu danych. Podobnie nie można zmienić nazwy katalogu danych i nie można przenieść go do innego regionu. 

Konta użytkowników, którym przypisano automatyczną licencję usługi Power BI Pro, mają dostęp do katalogu danych z powodu umowy licencyjnej, gdy zarejestrowali się w usłudze Office 365 E5 za pomocą licencji Usługi Power BI Pro. Ten typ użytkownika ma pełny dostęp do zasobów wykazu danych bez uprawnień administracyjnych. Ten rodzaj użytkownika *nie* jest częścią roli **użytkownika wykazu** w usłudze Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Jak rozpocząć pracę z katalogiem danych?
Najlepszym sposobem na rozpoczęcie pracy jest przejście do wprowadzenie do [wykazu danych](data-catalog-get-started.md). Ten artykuł jest kompleksowym omówieniem możliwości w usłudze.

## <a name="how-do-i-register-my-data"></a>Jak zarejestrować swoje dane?
Aby zarejestrować dane w wykazie danych:
1. W portalu usługi Azure Data Catalog w obszarze **Publikowanie** uruchom narzędzie rejestracji usługi Azure Data Catalog. 
2. W narzędziu rejestracji źródła danych wykazu danych zaloguj się przy użyciu tych samych poświadczeń, których używasz do uzyskiwania dostępu do portalu wykazu danych.
3. Wybierz źródło danych i określone zasoby, które chcesz zarejestrować.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Jakie właściwości wyodrębnia dla zasobów danych, które są zarejestrowane?
Określone właściwości różnią się od źródła danych do źródła danych, ale ogólnie usługa publikowania wykazu danych wyodrębnia następujące informacje:

* Nazwa zasobu
* Typ środka trwałego
* Opis środka trwałego
* Nazwy atrybutów/kolumn
* Typy danych atrybutu/kolumny
* Opis atrybutu/kolumny

> [!IMPORTANT]
> Rejestrowanie zasobów danych w wykazie danych nie powoduje przeniesienia ani skopiowania danych do chmury. Rejestrowanie zasobów ze źródła danych kopiuje metadane zasobów na platformę Azure, ale dane pozostają w istniejącej lokalizacji źródła danych. Wyjątkiem od tej reguły jest, jeśli zdecydujesz się przekazać rekordy podglądu lub profil danych podczas rejestracji zasobów. Po dodaniu wersji zapoznawczej z każdego zasobu jest kopiowanych do 20 rekordów i przechowywanych jako migawka w wykazie danych. Po dołączeniu profilu danych zagregowane informacje są obliczane i uwzględniane w metadanych przechowywanych w katalogu. Informacje agregowane mogą obejmować rozmiar tabel, procent wartości null na kolumnę lub minimalne, maksymalne i średnie wartości dla kolumn. 
>
>

> [!NOTE]
> W przypadku źródeł danych, takich jak SQL Server Analysis Services, które mają właściwość **Description** pierwszej klasy, narzędzie rejestracji źródła danych wykazu danych wyodrębnia tę wartość właściwości. W przypadku *lokalnych* relacyjnych baz danych programu SQL Server, które nie mają właściwości **Description** pierwszej klasy, narzędzie do rejestracji źródła danych wykazu danych wyodrębnia wartość z **właściwości rozszerzonej ms_description** dla obiektów i kolumn. Ta właściwość nie jest obsługiwana dla platformy SQL Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z właściwości rozszerzonych w obiektach bazy danych](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Jak długo należy czekać, aby nowo zarejestrowane zasoby pojawiły się w katalogu?
Po zarejestrowaniu zasobów w wykazie danych może u danych okres od 5 do 10 sekund, zanim pojawią się one w portalu wykazu danych.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Jak dodawać adnotacje i wzbogacać metadane zarejestrowanych zasobów danych?
Najprostszym sposobem dostarczenia metadanych dla zarejestrowanych zasobów jest wybranie zasobu w portalu wykazu danych, a następnie wprowadzenie wartości w okienku właściwości lub okienku schematu dla zaznaczonego obiektu.

Podczas procesu rejestracji można również podać niektóre metadane, takie jak eksperci i tagi. Wartości podane w usłudze publikowania wykazu danych dotyczą wszystkich zasobów zarejestrowanych w tym czasie. Aby wyświetlić ostatnio zarejestrowane obiekty w portalu w celu uzyskania dodatkowych adnotacji, wybierz przycisk **Wyświetl portal** na ostatnim ekranie narzędzia do rejestracji źródła danych wykazu danych.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Jak usunąć zarejestrowane obiekty danych?
Obiekt można usunąć z wykazu danych, zaznaczając obiekt w portalu, a następnie klikając przycisk **Usuń.** Usunięcie obiektu powoduje usunięcie jego metadanych z wykazu danych, ale nie wpływa na bazowe źródło danych.

## <a name="what-is-an-expert"></a>Co to jest ekspert?
Ekspert to osoba, która ma świadomą perspektywę obiektu danych. Obiekt może mieć wielu ekspertów. Ekspert nie musi być "właścicielem" obiektu, ale jest po prostu kimś, kto wie, jak dane mogą i powinny być używane.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Jak udostępnić informacje zespołowi wykazu danych w przypadku wystąpienia problemów?
Aby zgłaszać problemy, udostępniać informacje i zadawać pytania, przejdź do [forum usługi Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Czy katalog działa z innym źródłem danych, które mnie interesuje?
Aktywnie pracujemy nad dodawaniem większej liczby źródeł danych do wykazu danych. Jeśli chcesz zobaczyć obsługiwane źródło danych, zaproponuj to (lub użycz swoje wsparcie, jeśli zostało już zasugerowane), przechodząc do [wykazu danych na forach opinii platformy Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Jakie uprawnienia są potrzebne do rejestrowania zasobów w wykazie danych?
Aby uruchomić narzędzie rejestracji wykazu danych, potrzebujesz uprawnień do źródła danych, które umożliwia odczytywanie metadanych ze źródła. Aby również dołączyć podgląd, musisz mieć uprawnienia, które umożliwiają odczyt danych z zarejestrowanych obiektów.

Wykaz danych umożliwia również administratorom wykazu ograniczanie użytkowników i grup, którzy mogą dodawać metadane do katalogu. Aby uzyskać dodatkowe informacje, zobacz [Jak zabezpieczyć dostęp do katalogu danych i zasobów danych](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Czy wykaz danych zostanie udostępniony również do wdrożenia lokalnego?
Wykaz danych to usługa w chmurze, która może współpracować z chmurowymi i lokalnymi źródłami danych w celu dostarczenia hybrydowego rozwiązania do odnajdowania źródeł danych. Obecnie nie ma żadnych planów dla wersji usługi wykazu danych, która działa lokalnie.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Czy mogę wyodrębnić więcej lub bogatsze metadane ze źródeł danych, które rejestruję?
Aktywnie pracujemy nad rozszerzeniem możliwości wykazu danych. Jeśli chcesz wyodrębnić dodatkowe metadane ze źródła danych podczas rejestracji, zaproponuj (lub zagłosuj na nie, jeśli zostało już zasugerowane) w [wykazie danych na forach opinii platformy Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Jeśli chcesz dołączyć metadane kolumny/schematu, podglądy lub profile danych dla źródeł danych, w których te metadane nie są wyodrębniane przez narzędzie do rejestracji źródła danych, możesz użyć interfejsu API wykazu danych, aby dodać te metadane. Aby uzyskać dodatkowe informacje, zobacz [Interfejs API REST usługi Azure Data Catalog](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Jak ograniczyć widoczność zarejestrowanych zasobów danych, aby tylko niektóre osoby mogły je odnajdyć?
Wybierz zasoby danych w wykazie danych, a następnie kliknij przycisk **Przejmij na własność.** Właściciele zasobów danych w wykazie danych mogą zmienić ustawienia widoczności, aby umożliwić wszystkim użytkownikom odnajdowanie posiadanych zasobów lub ograniczyć widoczność do określonych użytkowników. Aby uzyskać dodatkowe informacje, zobacz [Zarządzanie zasobami danych w usłudze Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Jak zaktualizować rejestrację zasobu danych, aby zmiany w źródle danych były odzwierciedlane w katalogu?
Aby zaktualizować metadane dla zasobów danych, które są już zarejestrowane w katalogu, po prostu ponownie zarejestruj źródło danych, które zawiera zasoby. Wszelkie zmiany w źródle danych, takie jak kolumny dodawane lub usuwane z tabel lub widoków, są aktualizowane w katalogu, ale wszelkie adnotacje dostarczane przez użytkowników są zachowywane.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Na moje pytanie nie ma tutaj odpowiedzi. Gdzie mogę szukać odpowiedzi?
Przejdź do [forum usługi Azure Data Catalog](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Zadane tam pytania znajdą drogę tutaj.
