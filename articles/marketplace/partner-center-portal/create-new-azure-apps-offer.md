---
title: Utwórz nową ofertę usługi Azure Apps w komercyjnej witrynie Marketplace
description: Jak utworzyć nową ofertę usługi Azure Apps do wystawiania lub sprzedawania w witrynie Azure Marketplace, AppSource lub za pośrednictwem programu Cloud Solution Provider (CSP) przy użyciu portalu Marketplace w witrynie Microsoft Partner Center.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 69d7bc81f5adc4797be86f946fdb656b4b6cc682
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283370"
---
# <a name="create-an-azure-application-offer"></a>Tworzenie oferty aplikacji platformy Azure

Kroki publikowania oferty aplikacji platformy Azure w komercyjnej witrynie Marketplace opisano tutaj.

## <a name="azure-application-offer-type"></a>Typ oferty aplikacji platformy Azure

W tym temacie przedstawiono podstawowe informacje o ofertach aplikacji platformy Azure.  Należy zapoznać się z tymi pojęciami przed rozpoczęciem procesu publikowania nowej oferty aplikacji platformy Azure w portalu Marketplace.

### <a name="publishing-overview"></a>Przegląd publikowania

[Tworzenie szablonów rozwiązań wideo i aplikacji zarządzanych dla portalu Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) to wprowadzenie do typu oferty aplikacji platformy Azure:

* Jakie typy ofert są dostępne;
* Jakie zasoby techniczne są wymagane;
* Jak utworzyć szablon Azure Resource Manager;
* Tworzenie i testowanie interfejsu użytkownika aplikacji;
* Jak opublikować ofertę aplikacji;
* Proces przeglądu aplikacji.

### <a name="types-of-azure-application-plans"></a>Typy planów aplikacji platformy Azure

Istnieją dwa rodzaje planów aplikacji platformy Azure, zarządzane aplikacje i szablony rozwiązań.

* **Szablon rozwiązania** jest jednym z głównych sposobów publikowania rozwiązania w portalu Marketplace. Ten typ planu jest używany, gdy rozwiązanie wymaga dodatkowego wdrożenia i automatyzacji konfiguracji poza jedną maszyną wirtualną.  Szablon rozwiązania umożliwia Automatyzowanie udostępniania więcej niż jednego zasobu, w tym maszyn wirtualnych, sieci i zasobów magazynu, aby zapewnić złożone rozwiązania IaaS.  Aby uzyskać więcej informacji na temat tworzenia szablonów rozwiązań, zapoznaj się z dokumentacją [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) .

* **Aplikacja zarządzana** jest podobna do szablonów rozwiązań z jedną różnicą kluczową. Najważniejsza różnica między nimi polega na tym, że w aplikacji zarządzanej zasoby są wdrażane w grupie zasobów zarządzanej przez wydawcę aplikacji. Grupa zasobów znajduje się w subskrypcji klienta, ale tożsamość w dzierżawie wydawcy ma dostęp do tej grupy zasobów. Wydawca określa koszt bieżącej obsługi rozwiązania. Korzystaj z zarządzanych aplikacji, aby łatwo tworzyć i dostarczać klientom w pełni zarządzane aplikacje gotowe.  Aby uzyskać więcej informacji o zaletach i typach zarządzanych aplikacji, zobacz [Omówienie usługi Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/overview).

Wszystkie aplikacje platformy Azure obejmują co najmniej dwa pliki w folderze głównym archiwum `.zip`:

* Plik szablonu Menedżer zasobów o nazwie [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Jest to szablon definiujący zasoby do wdrożenia w ramach subskrypcji platformy Azure klienta.  Przykłady szablonów Menedżer zasobów można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) lub w odpowiedniej repozytorium [szablonów szybkiego startu: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .

* Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  W interfejsie użytkownika należy określić elementy, które umożliwiają klientom podanie wartości parametrów.

Wszystkie nowe oferty aplikacji platformy Azure muszą obejmować [Identyfikator GUID autorstwa klienta na platformie Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="before-you-begin"></a>Przed rozpoczęciem

Zapoznaj się z poniższą dokumentacją dotyczącą aplikacji platformy Azure, która zawiera Przewodniki Szybki Start, samouczki i przykłady.

* [Informacje o szablonach Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Przewodniki Szybki start:

    * [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/)
    * [Szablony szybkiego startu platformy Azure w witrynie GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publikowanie definicji aplikacji](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Wdróż aplikację katalogu usług](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Samouczki:

    * [Utwórz pliki definicji](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publish marketplace application (Publikowanie aplikacji w witrynie Marketplace)](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Badan

    * [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Zarządzane rozwiązania aplikacji](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Podstawy wiedzy technicznej

Projektowanie, kompilowanie i testowanie tych zasobów jest czasochłonne i wymaga znajomości technicznej platformy Azure i technologii używanych do tworzenia oferty.

Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

* Podstawowe informacje na temat [usług platformy Azure](https://azure.microsoft.com/services/).
* [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/).
* Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Wiedza [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Praktyczna wiedza o formacie [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz co najmniej jedno z następujących środowisk skryptów, aby ułatwić zarządzanie aplikacją platformy Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)

Zalecamy dodanie następujących narzędzi do środowiska deweloperskiego:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) z następującymi rozszerzeniami:
    * Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostępne narzędzia można sprawdzić na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/tools/) .  Ponadto, jeśli używasz programu Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Tworzenie oferty aplikacji platformy Azure

Przed utworzeniem oferty aplikacji platformy Azure należy najpierw [utworzyć konto Centrum partnerskiego](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) i otworzyć [komercyjny pulpit nawigacyjny portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)z wybraną kartą **Przegląd** .

>[!Note]
>Po opublikowaniu oferty zmiany wprowadzone w centrum partnerskim zostaną zaktualizowane tylko w systemie i w obszarze sklep po ponownym opublikowaniu.  Upewnij się, że przesyłasz ofertę do publikacji po wprowadzeniu zmian.

### <a name="create-a-new-offer"></a>Tworzenie nowej oferty

Wybierz przycisk **+ Nowa oferta** , a następnie wybierz element menu **aplikacji platformy Azure** . Zostanie wyświetlone okno dialogowe **Nowa oferta** .

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

* **Identyfikator oferty**: unikatowy identyfikator dla każdej oferty na Twoim koncie. Ten identyfikator będzie widoczny dla klientów w adresie URL dla oferty witryny Marketplace i szablonów Azure Resource Manager (jeśli dotyczy). <br> <br> Identyfikator oferty musi zawierać małe litery alfanumeryczne (w tym łączniki i znaki podkreślenia, ale nie odstępy). Jest ono ograniczone do 50 znaków i nie można go zmienić po wybraniu opcji Utwórz. <br> <br> Jeśli na przykład wprowadzisz tutaj `test-offer-1`, adres URL oferty zostanie `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Alias oferty**: nazwa używana do odwoływania się do oferty w centrum partnerskim. Ta nazwa nie będzie używana w portalu Marketplace i różni się od nazwy oferty i innych wartości, które będą widoczne dla klientów. Tej wartości nie można zmienić po wybraniu opcji **Utwórz**.

Po wprowadzeniu **identyfikatora oferty** i **aliasu oferty**wybierz pozycję **Utwórz**. Następnie będzie można korzystać ze wszystkich innych części oferty.

## <a name="offer-setup"></a>Konfiguracja oferty

Na stronie **Konfiguracja oferty** są wyświetlane poniższe informacje. Pamiętaj, aby po zakończeniu tych pól wybrać opcję **Zapisz** .

### <a name="test-drive"></a>Wersja testowa

Test jest doskonałym sposobem na pokazanie oferty potencjalnym klientom, dając im możliwość "Wypróbuj przed zakupem", co spowodowało zwiększenie konwersji i generowanie wysoce wykwalifikowanych klientów. [Dowiedz się więcej o testowaniu dysków.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Aby włączyć stację testową, zaznacz pole wyboru **Włącz dysk testowy** . Następnie należy skonfigurować środowisko demonstracyjne w [konfiguracji technicznej na dysku testowym](#types-of-azure-application-plans) , aby umożliwić klientom próbę skorzystania z oferty przez ustalony okres. 

>[!Note]
>Ze względu na to, że wszystkie aplikacje platformy Azure są implementowane przy użyciu szablonu Azure Resource Manager, jedynym typem dysku testowego, który można skonfigurować dla aplikacji platformy Azure, jest [dysk testowy oparty na Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Dodatkowe zasoby dotyczące dysku testowego

- [Testowanie najlepszych rozwiązań technicznych](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Testowanie najlepszych rozwiązań marketingowych](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Testowanie dysku — Omówienie jednego modułu stronicowania](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Zarządzanie potencjalnymi klientami

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie potencjalnymi klientami — Omówienie](./commercial-marketplace-get-customer-leads.md).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

## <a name="properties"></a>Właściwości

Strona **Właściwości** umożliwia definiowanie kategorii i branż używanych do grupowania oferty w witrynie Marketplace, wersji aplikacji oraz umów prawnych wspierających Twoją ofertę. Wybierz pozycję **Zapisz** po zakończeniu tej strony.

### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie trzy kategorie, które będą używane do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu Marketplace. Pamiętaj, aby dowiedzieć się, jak Twoja oferta obsługuje te kategorie w opisie oferty. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla komercyjnego portalu Microsoft Marketplace

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Lista oferty

Na stronie z listą ofert są wyświetlane Języki, w których zostanie wyświetlona oferta. Obecnie tylko w **języku angielskim (Stany Zjednoczone)** jest jedyną dostępną opcją.

Musisz zdefiniować szczegóły witryny Marketplace (nazwę oferty, opis, obrazy itp.) dla każdego języka/rynku. Wybierz nazwę języka/rynku, aby podać te informacje.

> [!NOTE]
> Oferta zawartości oferty (na przykład opis, dokumenty, zrzuty ekranu, warunki użytkowania itp.) nie jest wymagana w języku angielskim, tak długo, jak opis oferty zaczyna się od frazy "Ta aplikacja jest dostępna tylko w języku innym niż angielski]". Można także zapewnić *przydatny adres URL linku* do oferowania zawartości w języku innym niż ten, który jest używany w ofercie dotyczącej oferty.

### <a name="name"></a>Name (Nazwa)

Nazwa wprowadzona w tym miejscu będzie wyświetlana klientom jako tytuł oferty. To pole jest wstępnie wypełniane tekstem wprowadzonym dla **aliasu oferty** podczas tworzenia oferty, ale można zmienić tę wartość. Ta nazwa może być znakiem towarowym (i może zawierać znaki towarowe lub autorskie). Nazwa nie może być dłuższa niż 50 znaków i nie może zawierać żadnych znaków emoji.

### <a name="summary"></a>Podsumowanie

Podaj krótki opis oferty (do 100 znaków), która może być używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Podaj dłuższy opis oferty (do 256 znaków). Opis może być używany w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis oferty (do 3 000 znaków). Ten opis będzie wyświetlany klientom na liście przeglądów w portalu Marketplace. Uwzględnij swoją propozycję oferty, najważniejsze zalety, kategorie i/lub branżowe skojarzenia, szanse zakupu w aplikacji oraz wszelkie wymagane informacje. 

Niektóre porady dotyczące pisania opisu:  

- Jasno opisz swoją wartość oferty w pierwszych kilku zdaniach opisu. Uwzględnij następujące elementy na swojej pozycji wartości:
  - Opis produktu
  - Typ użytkownika, który korzysta z produktu
  - Klienci muszą lub cierpią adresy produktów
- Należy pamiętać, że pierwsze niektóre zdania mogą być wyświetlane w wynikach wyszukiwania.  
- Nie należy polegać na funkcjach i funkcjach, aby sprzedawać produkt. Zamiast tego należy skoncentrować się na dostarczanej wartości.  
- Korzystaj z specyficznych dla branży słownictwa lub takich słów, jak to możliwe. 
- Rozważ użycie tagów HTML, aby sformatować swój opis i zwiększyć jego atrakcyjność.

### <a name="search-keywords"></a>Słowa kluczowe wyszukiwania

Opcjonalnie możesz wprowadzić do trzech słów kluczowych wyszukiwania, aby pomóc klientom w znalezieniu oferty w portalu Marketplace. Aby uzyskać najlepsze wyniki, spróbuj użyć tych słów kluczowych również w opisie.

### <a name="support-urls"></a>Adresy URL pomocy technicznej

Ta sekcja zawiera linki pomagające klientom w dowiedzieć się więcej o ofercie.

#### <a name="privacy-policy-url"></a>Adres URL zasad ochrony prywatności

Wprowadź adres URL zasad zachowania poufności informacji organizacji. Użytkownik jest odpowiedzialny za zapewnienie zgodności aplikacji z przepisami i przepisami dotyczącymi ochrony prywatności oraz w celu zapewnienia prawidłowych zasad zachowania poufności informacji.

#### <a name="useful-links"></a>Przydatne łącza

Podaj opcjonalne uzupełniające dokumenty online dotyczące Twojego rozwiązania.  Dodaj dodatkowe przydatne linki, klikając pozycję **+ Dodaj link**.

### <a name="contacts"></a>Kontakty

W tej sekcji należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu z pomocą techniczną** i **kontaktu inżynieryjnego**. Te informacje nie są widoczne dla klientów, ale będą dostępne dla firmy Microsoft i mogą być udostępniane partnerom programu CSP.

W sekcji **skontaktuj się z pomocą techniczną** należy również podać **adres URL pomocy technicznej** , w której partnerzy CSP mogą znaleźć obsługę oferty.

### <a name="marketplace-images"></a>Obrazy z witryny Marketplace

W tej sekcji można podać logo i obrazy, które będą używane podczas wyświetlania oferty dla klienta. Wszystkie obrazy muszą mieć format PNG.

#### <a name="store-logos"></a>Logo sklepu

Udostępnij logo swojej oferty w trzech rozmiarach: **małe (48 x 48)** , **średnie (90 x 90)** i **duże (216 x 216)** .

#### <a name="hero"></a>Hero

Obraz Hero jest opcjonalny. Jeśli postanowisz jeden, musi on mierzyć 815 x 290 pikseli.

#### <a name="screenshots"></a>Zrzuty ekranu

Dodaj zrzuty ekranu pokazujące, jak działa Twoja oferta. Można dodać maksymalnie pięć zrzutów ekranu. Wszystkie zrzuty ekranu muszą mieć 1280 x 720 pikseli.

#### <a name="videos"></a>Filmy wideo

Opcjonalnie możesz dodać maksymalnie pięć filmów wideo, które demonstrują Twoją ofertę. Te filmy wideo powinny być hostowane w usłudze YouTube i/lub Vimeo. Dla każdej z nich wprowadź nazwę filmu wideo, jego adres URL i obraz miniatury filmu wideo (1280 x 720 pikseli).

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące wyświetlania w portalu Marketplace

- [Najlepsze rozwiązania dotyczące aukcji z ofertą Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Wersja zapoznawcza

Na karcie **Podgląd** można zdefiniować ograniczonej **odbiorców w wersji zapoznawczej** , aby sprawdzić poprawność oferty przed opublikowaniem oferty w szerszym gronie odbiorców w portalu Marketplace.

> [!IMPORTANT]
> Musisz wybrać pozycję **Przejdź na żywo** , aby Twoja oferta została opublikowana na żywo dla odbiorców publicznych w witrynie Marketplace po sprawdzeniu oferty w wersji zapoznawczej.

Odbiorca w wersji zapoznawczej jest identyfikowany przez identyfikatory GUID identyfikatora subskrypcji platformy Azure, sparowany z opcjonalnym opisem dla każdego z nich.  Żadne z tych pól nie są widoczne dla klientów.

Ręcznie Dodaj do 10 identyfikatorów subskrypcji platformy Azure lub do 100 w przypadku przekazywania pliku CSV.  Dodając te subskrypcje, definiujesz odbiorców, którzy będą mogli uzyskiwać dostęp do wersji zapoznawczej do oferty przed jej opublikowaniem w całości.  Jeśli Twoja oferta jest już aktywna, możesz nadal zdefiniować odbiorcę w wersji zapoznawczej do testowania wszelkich zmian lub aktualizacji oferty.

>[!Note]
>Odbiorcy wersji zapoznawczej różnią się od odbiorców prywatnych. Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty *przed* opublikowaniem jej na żywo w portalu Marketplace. Możesz również utworzyć plan i udostępnić go tylko prywatnym odbiorcom (za pomocą karty dostępność planu).  Odbiorcy wersji zapoznawczej będą mogli zobaczyć i zweryfikować wszystkie plany, w tym plany, które będą dostępne tylko dla odbiorców prywatnych, gdy oferta zostanie w pełni opublikowana w portalu Marketplace.

## <a name="plan-overview"></a>Przegląd planu

Karta **Przegląd planu** umożliwia udostępnianie różnych opcji planu w ramach tej samej oferty. Plany te (określane jako jednostki SKU w portal Cloud Partner) mogą różnić się w zależności od typu planu (szablonu rozwiązania a aplikacji zarządzanej), zysków lub odbiorców.  Skonfiguruj co najmniej jeden plan w celu wyświetlenia oferty w portalu Marketplace.

Po utworzeniu zobaczysz nazwy planu, identyfikatory, typ planu, dostępność (Public lub private), bieżący stan publikowania i wszystkie dostępne akcje na tej karcie.

**Akcje** dostępne w **przeglądzie planu** różnią się w zależności od bieżącego stanu planu i mogą obejmować:

* Jeśli plan ma stan **wersja robocza** — Usuń wersję roboczą.
* Jeśli plan ma stan **Live** — Zatrzymaj sprzedawanie lub zsynchronizuj prywatnych odbiorców.

### <a name="create-new-plan"></a>Utwórz nowy plan

***Identyfikator planu*** — Utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie URL produktu.  Używaj tylko małych liter, znaków alfanumerycznych, łączników lub podkreśleń. Dla tego identyfikatora planu można używać maksymalnie 50 znaków. Tego identyfikatora nie można zmienić po wybraniu pozycji Utwórz.

***Nazwa planu*** — klienci będą widzieli tę nazwę podczas wybierania planu do wyboru w ramach oferty. Utwórz unikatową nazwę oferty dla każdego planu w tej ofercie. Nazwa planu służy do rozróżniania planów oprogramowania, które mogą być częścią tej samej oferty (np. Nazwa oferty: system Windows Server; plany: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Konfigurowanie planu

Karta **ustawienia planu** służy do ustawiania konfiguracji wysokiego poziomu dla typu planu, bez względu na to, czy ponownie używa pakietów z innego planu, a także jakie chmure ma być dostępny w ramach planu.  Odpowiedzi na tej karcie wpłyną na to, które pola są wyświetlane na innych kartach dla tego samego planu.

#### <a name="plan-type"></a>Typ planu

Jak opisano w [typach planów aplikacji platformy Azure](#types-of-azure-application-plans), wybierz, czy plan będzie zawierać szablon rozwiązania czy zarządzaną aplikację.

#### <a name="this-plan-reuses-packages"></a>Ten plan ponownie używa pakietów

Jeśli masz więcej niż jeden plan tego samego typu, a pakiety są identyczne między nimi, możesz wybrać **ten plan ponownie używa pakietów z innego planu**.  Po wybraniu tej opcji będziesz mieć możliwość wybrania jednego z innych planów tego samego typu dla tej oferty, aby ponownie użyć pakietów z programu. 

>[!Note]
>Po ponownym użyciu pakietów z innego planu cała karta konfiguracja techniczna zostanie usunięta z tego planu.  Szczegóły konfiguracji technicznej z innego planu, w tym wszelkie aktualizacje wprowadzone w przyszłości, zostaną użyte również dla tego planu. <br> <br> Ponadto nie można zmienić tego ustawienia po opublikowaniu tego planu.

#### <a name="cloud-availability"></a>Dostępność w chmurze

Ten plan należy udostępnić w co najmniej jednej chmurze. 

Wybierz opcję **publicznej platformy Azure** , aby umożliwić wdrażanie rozwiązań klientom we wszystkich publicznych regionach świadczenia usługi Azure z integracją z portalu Marketplace.  Dowiedz się więcej o [dostępności geograficznej](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Wybierz opcję **Azure Government Cloud** , aby umożliwić wdrożenie rozwiązania w [chmurze Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), chmurę dla instytucji rządowych z dostępem kontrolowanym dla klientów z federalnych Stanów Zjednoczonych, statusu, lokalnego lub plemienne oraz partnerów uprawnionych do udostępniania tych jednostek.  Ponieważ Wydawca jest odpowiedzialny za wszelkie kontrole zgodności, miary zabezpieczeń i najlepsze rozwiązania w zakresie obsłużenia tej społeczności chmurowej.  Azure Government używa fizycznie wyizolowanych centrów danych i sieci (znajdujących się tylko w Stanach Zjednoczonych).  Przed opublikowaniem w [Azure Government](https://aka.ms/azuregovpublish)firma Microsoft zaleca przetestowanie i zweryfikowanie rozwiązania w środowisku, ponieważ niektóre punkty końcowe mogą się różnić. Aby przygotować i przetestować rozwiązanie, zażądaj konta próbnego z tego [linku](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Po opublikowaniu planu jako dostępnego w określonej chmurze nie można usunąć tej chmury.

**Azure Government certyfikaty w chmurze**

Ta opcja jest widoczna tylko w przypadku wybrania opcji **Azure Government Cloud** w obszarze **dostępność w chmurze**.

Usługi Azure Government Services obsługują dane, które podlegają określonym przepisom obowiązującym dla instytucji rządowych, takich jak FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD P4 i CJIS.  Aby zapewnić świadomość certyfikatów dla tych programów, możesz udostępnić do 100 linków opisujących certyfikaty.  Linki te mogą zawierać linki do listy bezpośrednio w programie lub linki do opisów zgodności z ich własnymi witrynami sieci Web.  Te linki będą widoczne tylko dla Azure Government klientów w chmurze.

## <a name="plan-listing"></a>Zaplanuj listę

Na karcie **lista planów** wyświetlane są informacje o wykorzystaniu planu, które mogą być różne dla różnych planów dla tej samej oferty.

### <a name="name"></a>Name (Nazwa)

Wstępnie wypełnione na podstawie nazwy przypisanej do planu podczas jego tworzenia.  Ta nazwa będzie wyświetlana jako tytuł tego "planu oprogramowania" wyświetlanego w portalu Marketplace.  Może zawierać do 100 znaków.

### <a name="summary"></a>Podsumowanie

Podaj krótkie podsumowanie planu oprogramowania.  Może zawierać do 100 znaków.

### <a name="description"></a>Opis

Ten opis umożliwia wyjaśnienie, co sprawia, że ten plan oprogramowania jest unikatowy i wszelkie różnice między innymi planami oprogramowania w ramach oferty. Może zawierać do 2 000 znaków.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .

## <a name="availability"></a>Dostępność

Karta **dostępność** jest widoczna tylko dla planów szablonów rozwiązań.  Można sprawić, aby plan był widoczny dla wszystkich, tylko dla określonych klientów (odbiorców prywatnych) i zdecydować, czy plan ma być ukryty do użytku przez inny szablon rozwiązania, czy tylko aplikacje zarządzane.

### <a name="plan-audience"></a>Planowanie odbiorców

Istnieje możliwość skonfigurowania każdego planu, który będzie widoczny dla wszystkich użytkowników lub tylko do określonych odbiorców. Członkostwo w tych ograniczonych odbiorcach można przypisywać przy użyciu identyfikatorów subskrypcji platformy Azure.

**Prywatność/to jest plan prywatny** (opcjonalne pole wyboru) — zaznacz to pole wyboru, aby plan był prywatny i widoczny tylko dla wybranych odbiorców z ograniczeniami. Po opublikowaniu jako planu prywatnego możesz zaktualizować odbiorców lub wybrać opcję udostępnienia planu wszystkim użytkownikom. Gdy plan zostanie opublikowany jako widoczny dla wszystkich użytkowników, musi pozostać widoczny dla wszystkich. (Nie można ponownie skonfigurować planu jako planu prywatnego).

**Odbiorcy z ograniczeniami (identyfikatory subskrypcji platformy Azure)** — Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego. Dostęp jest przypisywany przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji platformy Azure. Jeśli importuje plik arkusza kalkulacyjnego. csv, można dodać maksymalnie 10 identyfikatorów subskrypcji lub 20 000 klientów.  Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a litery muszą być małymi literami.

>[!Note]
>Prywatni odbiorcy (lub odbiorcy z ograniczeniami) różnią się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie [**Podgląd**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty *przed* opublikowaniem jej w portalu Marketplace. Gdy oznaczenie odbiorców prywatnych odnosi się tylko do określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

### <a name="hide-plan"></a>Ukryj plan

Jeśli szablon rozwiązania jest przeznaczony do wdrożenia tylko pośrednio, gdy istnieje odwołanie do innego szablonu rozwiązania lub aplikacji zarządzanej, zaznacz to pole wyboru, aby opublikować szablon rozwiązania, ale ukryć go od klientów wyszukiwania bezpośrednio i przeglądania.

## <a name="pricing-and-availability"></a>Cennik i dostępność

Karta **ceny i dostępność** jest widoczna tylko dla zarządzanych planów aplikacji.  Można skonfigurować rynki, w których będzie dostępny ten plan, cenę za miesiąc zarządzania rozwiązaniem oraz zdecydować, czy plan ma być widoczny dla wszystkich, czy tylko dla określonych klientów (odbiorców prywatnych).

### <a name="markets"></a>Wprowadza

Każdy plan musi być dostępny na co najmniej jednym rynku. Zaznacz pole wyboru dla dowolnej lokalizacji rynkowej, w której chcesz udostępnić ten plan. Pole wyszukiwania i przycisk służący do wybierania krajów "z przekazaniem podatku", w których firma Microsoft dokonuje zakupu sprzedaży i używania podatków w Twoim imieniu, są dołączane do pomocy.

Jeśli już ustawisz ceny dla planu w Stany Zjednoczone dolarów (USD) i dodasz kolejną lokalizację na rynku, cena nowego rynku zostanie obliczona zgodnie z bieżącymi stawkami za wymianę. Zawsze sprawdzaj ceny poszczególnych rynków przed opublikowaniem. Cennik można przejrzeć przy użyciu linku "Eksport cen (xlsx)" po zapisaniu zmian.

### <a name="pricing"></a>Ceny

Podaj cenę za miesiąc dla tego planu.  Ta cena jest uzupełnieniem usługi Azure Infrastructure lub kosztów związanych z płatność zgodnie z rzeczywistym użyciem w przypadku zasobów wdrożonych w ramach tego rozwiązania.

Ceny ustawione w walucie lokalnej (USD = Stany Zjednoczone dolara) są konwertowane na walutę lokalną wszystkich wybranych rynków przy użyciu bieżących stawek wymiany dostępnych podczas instalacji. Sprawdź te ceny przed opublikowaniem, eksportując arkusz kalkulacyjny z cennikiem i sprawdzając cenę na każdym rynku. Jeśli chcesz ustawić ceny niestandardowe na indywidualnym rynku, zmodyfikuj i zaimportuj arkusz cen. 

>[!Note]
>Najpierw musisz zapisać zmiany cen, aby włączyć eksportowanie danych cen.

Uważnie Przejrzyj ceny przed opublikowaniem, ponieważ istnieją pewne ograniczenia dotyczące tego, co można zmienić po opublikowaniu planu.  

>[!Note]
>Po opublikowaniu ceny na rynku planu nie można jej później zmienić.

### <a name="plan-audience"></a>Planowanie odbiorców

Istnieje możliwość skonfigurowania każdego planu, który będzie widoczny dla wszystkich użytkowników lub tylko do określonych odbiorców. Członkostwo w tych ograniczonych odbiorcach można przypisywać przy użyciu identyfikatorów subskrypcji platformy Azure.

**Prywatność/to jest plan prywatny** (opcjonalne pole wyboru) — zaznacz to pole wyboru, aby plan był prywatny i widoczny tylko dla wybranych odbiorców z ograniczeniami. Po opublikowaniu jako planu prywatnego możesz zaktualizować odbiorców lub wybrać opcję udostępnienia planu wszystkim użytkownikom. Gdy plan zostanie opublikowany jako widoczny dla wszystkich użytkowników, musi pozostać widoczny dla wszystkich. (Nie można ponownie skonfigurować planu jako planu prywatnego).

**Odbiorcy z ograniczeniami (identyfikatory subskrypcji platformy Azure)** — Przypisz odbiorców, którzy będą mieli dostęp do tego planu prywatnego. Dostęp jest przypisywany przy użyciu identyfikatorów subskrypcji platformy Azure z opcją dołączenia opisu każdego przypisanego identyfikatora subskrypcji platformy Azure. Jeśli importuje plik arkusza kalkulacyjnego. csv, można dodać maksymalnie 10 identyfikatorów subskrypcji lub 20 000 klientów.  Identyfikatory subskrypcji platformy Azure są reprezentowane jako identyfikatory GUID, a litery muszą być małymi literami.

>[!Note]
>Prywatni odbiorcy (lub odbiorcy z ograniczeniami) różnią się od odbiorców w wersji zapoznawczej zdefiniowanej na karcie [**Podgląd**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Odbiorca wersji zapoznawczej uzyskuje dostęp do oferty *przed* opublikowaniem jej w portalu Marketplace. Gdy oznaczenie odbiorców prywatnych odnosi się tylko do określonego planu, odbiorcy wersji zapoznawczej mogą wyświetlić wszystkie plany (prywatne lub nie) do celów weryfikacji.

## <a name="technical-configuration"></a>Konfiguracja techniczna 

Karta **konfiguracja techniczna** umożliwia przekazanie pakietu wdrożeniowego, który umożliwi klientom wdrożenie planu.

>[!Note]
>Ta karta nie będzie widoczna, jeśli ten plan został skonfigurowany tak, aby ponownie używał pakietów z innego planu na karcie **Konfiguracja planu** .

### <a name="package-details"></a>Szczegóły pakietu

**Szczegóły pakietu** subtab umożliwiają edycję wersji roboczej konfiguracji technicznej.

***Wersja*** — Przypisz bieżącą wersję konfiguracji technicznej.  Zwiększ tę wersję przy każdej publikacji zmiany na tej stronie. Wersja musi mieć format `{integer}.{integer}.{integer}`.

***Plik pakietu*** (`.zip`) — ten pakiet zawiera wszystkie pliki szablonów potrzebne dla tego planu, a także dodatkowe zasoby, spakowane jako plik `.zip`.

Wszystkie pakiety planu aplikacji platformy Azure muszą zawierać te dwa pliki w folderze głównym archiwum `.zip`:

* Plik szablonu Menedżer zasobów o nazwie [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Ten szablon automatyzuje wdrażanie zasobów w subskrypcji platformy Azure dla klientów.  Przykłady szablonów Menedżer zasobów można znaleźć w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) lub w odpowiedniej repozytorium [szablonów szybkiego startu: Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .

* Definicja interfejsu użytkownika dla środowiska tworzenia aplikacji platformy Azure o nazwie [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Wszystkie nowe oferty aplikacji platformy Azure muszą również obejmować identyfikator GUID [przypisywania użycia klienta przez partnera platformy Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

### <a name="previously-published-packages"></a>Poprzednio opublikowane pakiety 

**Wcześniej opublikowane pakiety** subtab umożliwiają wyświetlanie wszystkich opublikowanych wersji konfiguracji technicznej.

## <a name="technical-configuration-managed-application-plans-only"></a>Konfiguracja techniczna (tylko plany zarządzanej aplikacji)

Plany aplikacji zarządzanych mają dodatkową złożoność na karcie **konfiguracja techniczna** poza polami **wersji** i **pliku pakietu** opisanymi powyżej. 

### <a name="enable-just-in-time-jit-access"></a>Włącz dostęp just-in-Time (JIT)

Wybierz tę opcję, aby włączyć dostęp just-in-Time (JIT) dla tego planu.  Dostęp JIT umożliwia żądanie dostępu z podwyższonym poziomem uprawnień do zasobów aplikacji zarządzanej w celu rozwiązywania problemów lub konserwacji. Zawsze masz dostęp tylko do odczytu do zasobów, ale przez określony okres możesz mieć większy dostęp.  Aby uzyskać więcej informacji, zobacz [Włączanie i żądanie dostępu just in Time do Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Aby wymagać, aby klienci aplikacji zarządzanej mogli udzielić stałego dostępu do konta, pozostaw tę opcję niezaznaczone.

>[!Note]
>Pamiętaj o zaktualizowaniu pliku `createUiDefinition.json` w celu obsługi tej funkcji.  

### <a name="deployment-mode"></a>Tryb wdrożenia

Wybierz, czy podczas wdrażania tego planu ma zostać skonfigurowany **pełny** , czy **przyrostowy tryb wdrażania** : 

* W **trybie kompletnym**ponowne wdrożenie aplikacji przez klienta spowoduje usunięcie zasobów w zarządzanej grupie zasobów, jeśli zasoby nie są zdefiniowane w `mainTemplate.json`. 
* W **trybie przyrostowym**ponowne wdrożenie aplikacji pozostawia istniejące zasoby bez zmian.

Aby dowiedzieć się więcej na temat trybów wdrażania, zobacz [Azure Resource Manager trybami wdrożenia](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>Adres URL punktu końcowego powiadomienia

Określ punkt końcowy elementu webhook HTTPS, aby otrzymywać powiadomienia o wszystkich operacjach CRUD na wystąpieniach aplikacji zarządzanej tej wersji planu.

### <a name="customize-allowed-customer-actions"></a>Dostosuj dozwolone akcje klienta

Wybierz tę opcję, aby określić akcje, które klienci mogą wykonywać na zarządzanych zasobach oprócz akcji "`*/read`", które są dostępne domyślnie. 

Utwórz listę dodatkowych akcji, które chcesz umożliwić klientowi wykonywanie w tym miejscu, oddzielając je średnikami.  Aby uzyskać więcej informacji, zobacz [Omówienie przypisań Odmów dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Aby uzyskać dostępne akcje, zobacz [Azure Resource Manager operacje dostawcy zasobów](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Aby na przykład umożliwić klientom ponowne uruchomienie maszyn wirtualnych, należy dodać `Microsoft.Compute/virtualMachines/restart/action` do akcji dozwolonych.

### <a name="global-azure--azure-government-cloud"></a>Globalna Chmura platformy Azure/Azure Government

Wskaż, kto powinien mieć dostęp do aplikacji zarządzanej w każdej obsługiwanej chmurze.  Użytkownicy, grupy lub aplikacje, którym chcesz nadać uprawnienia do zarządzanej grupy zasobów, są identyfikowane przy użyciu tożsamości usługi Azure Active Directory (AAD).

***Azure Active Directory identyfikator dzierżawy*** — identyfikator dzierżawy usługi AAD (znany również jako identyfikator katalogu) zawierający tożsamości użytkowników, grup lub aplikacji, do których chcesz udzielić uprawnień.  Identyfikator dzierżawy usługi AAD można znaleźć na Azure Portal w obszarze [właściwości Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autoryzacje*** — Dodaj identyfikator obiektu Azure Active Directory użytkownika, grupy lub aplikacji, którym chcesz nadać uprawnienia do zarządzanej grupy zasobów. Zidentyfikuj użytkownika według identyfikatora podmiotu zabezpieczeń, który można znaleźć w [bloku Azure Active Directory użytkownicy na Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Dla każdego podmiotu zabezpieczeń wybierz jedną z wbudowanych ról usługi Azure AD z listy (właściciela lub współautora). Wybrana rola zostanie opisywana przez podmiot zabezpieczeń do zasobów w ramach subskrypcji klienta. Aby uzyskać więcej informacji, zobacz temat [Wbudowane role dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach (RBAC), zobacz [Rozpoczynanie pracy z funkcją RBAC w Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Mimo że użytkownik może dodać do 100 autoryzacji na chmurę, łatwiej jest utworzyć Active Directory grupę użytkowników i określić jej identyfikator w "Identyfikator podmiotu zabezpieczeń".  Umożliwi to dodanie większej liczby użytkowników do grupy zarządzania po wdrożeniu planu i zmniejszenie potrzeb aktualizacji planu tylko w celu dodania większej liczby autoryzacji.

### <a name="policy-settings"></a>Ustawienia zasad

Zastosuj [Zasady platformy Azure](https://docs.microsoft.com/azure/governance/policy/overview) do aplikacji zarządzanej, aby określić wymagania dotyczące zgodności dla wdrożonego rozwiązania.  Definicje zasad i format wartości parametrów podano w artykule [Przykłady dla usługi Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).  Można skonfigurować maksymalnie pięć zasad i tylko jedno wystąpienie poszczególnych zasad.  Niektóre zasady wymagają dodatkowych parametrów.  Dla zasad inspekcji wymagana jest standardowa jednostka SKU.  Nazwa zasad jest ograniczona do 50 znaków.

## <a name="co-sell"></a>Sprzedawanie

Przekazywanie informacji na karcie sprzedaż jest całkowicie opcjonalne w przypadku publikowania oferty. Jest to wymagane do osiągnięcia gotowej do rozsprzedażu i gotowego do zakupu stanu do współdziałania. Te informacje będą używane przez zespoły sprzedaży firmy Microsoft, aby dowiedzieć się więcej na temat rozwiązania podczas oceny jego dopasowania do potrzeb klientów. Nie jest on dostępny bezpośrednio dla klientów.

Aby uzyskać więcej informacji na temat kończenia tej karty, zobacz [sprzedawanie opcji w centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Wersja testowa

Na karcie **dysk testowy** można skonfigurować pokaz (lub "dysk testowy"), co umożliwi klientom wypróbowanie oferty przed zatwierdzeniem jej zakupu. Dowiedz się więcej w artykule [co to jest dysk testowy?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Jeśli nie chcesz już podawać dysku testowego dla swojej oferty, Wróć do strony **Konfiguracja oferty** i usuń zaznaczenie pola wyboru **Włącz dysk testowy**.

### <a name="technical-configuration"></a>Konfiguracja techniczna

Aplikacje platformy Azure z własnym użyciem Azure Resource Manager typ dysku testowego.  Aby uzyskać więcej informacji, zobacz [konfiguracja techniczna dla Azure Resource Manager dysku testowego](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) .

### <a name="deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia

Aby wdrożyć dysk testowy w Twoim imieniu, Utwórz i podaj oddzielną, unikatową subskrypcję platformy Azure. (Niewymagane w przypadku Power BI dysków testowych).

- **Identyfikator subskrypcji platformy Azure** (wymagany dla Azure Resource Manager i aplikacji logiki): Wprowadź identyfikator subskrypcji, aby udzielić dostępu do usług konta platformy Azure na potrzeby raportowania użycia zasobów i rozliczeń. Zalecamy [utworzenie oddzielnej subskrypcji platformy Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , która ma być używana na potrzeby dysków testowych, jeśli jeszcze jej nie masz. Identyfikator subskrypcji platformy Azure można znaleźć, logując się do [Azure Portal](https://portal.azure.com/) i przechodząc do karty **subskrypcje** w menu po lewej stronie. Wybranie karty spowoduje wyświetlenie identyfikatora subskrypcji (np. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Identyfikator dzierżawy usługi Azure AD** (wymagany): wprowadź [Identyfikator dzierżawy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)usługi Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję * * Właściwości, a następnie wyszukaj numer **identyfikatora katalogu** na liście (np. 50c464d3-4930-494c-963c-1e951d15360e). Możesz również wyszukać identyfikator dzierżawy w organizacji przy użyciu adresu URL nazwy domeny w: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nazwa dzierżawy usługi Azure AD** (wymagana dla dynamicznego 365): wprowadź nazwę Azure Active Directory (AD). Aby znaleźć tę nazwę, zaloguj się do [Azure Portal](https://portal.azure.com/), w prawym górnym rogu nazwa dzierżawy zostanie wyświetlona w polu Nazwa konta.

- **Identyfikator aplikacji usługi Azure AD** (wymagane): wprowadź [Identyfikator aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Aby znaleźć ten identyfikator, zaloguj się do [Azure Portal](https://portal.azure.com/), wybierz kartę Active Directory w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wyszukaj numer **identyfikatora aplikacji** (np. 50c464d3-4930-494c-963c-1e951d15360e).

- **Wpis tajny klienta aplikacji usługi Azure AD** (wymagane): wprowadź [wpis tajny klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikacji usługi Azure AD. Aby znaleźć tę wartość, zaloguj się do [Azure Portal](https://portal.azure.com/). Wybierz kartę **Azure Active Directory** w menu po lewej stronie, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację testową. Następnie wybierz pozycję **Certyfikaty i wpisy tajne**, wybierz pozycję **Nowy wpis tajny klienta**, wprowadź opis, wybierz pozycję **nigdy nie** w obszarze **wygaśnięcie**, a następnie wybierz pozycję **Dodaj**. Należy pamiętać o skopiowaniu wartości. (Nie opuszczaj strony przed skopiowaniem wartości lub w przeciwnym razie nie będziesz mieć dostępu do wartości).

Pamiętaj, aby **zapisać** przed przejściem do następnej sekcji.

### <a name="test-drive-listings-optional"></a>Aukcje na dysku testowym (opcjonalnie)

Opcja **aukcje na dysku testowym** znajduje się na karcie **dysk testowy** zawiera języki (i rynki), w których jest dostępny dysk testowy. obecnie w języku angielskim (Stany Zjednoczone) jest jedyną dostępną lokalizacją. Ponadto na tej stronie jest wyświetlany stan listy charakterystycznej dla języka oraz Data/godzina dodania. Należy zdefiniować szczegóły stacji testowej (opis, Podręcznik użytkownika, wideo itp.) dla każdego języka/rynku.

- **Opis** (wymagane): opisz swój dysk testowy, co zostanie przedstawione, w jaki sposób użytkownik może eksperymentować z programem, funkcje do eksplorowania i wszelkie istotne informacje, które ułatwią użytkownikowi ustalenie, czy ma zostać pozyskana oferta. W tym polu można wprowadzić do 3 000 znaków tekstu. 

- **Informacje o dostępie** (wymagane dla Azure Resource Manager i logicznych dysków testowych): Wyjaśnij, co musi wiedzieć klient, aby uzyskać dostęp do tego dysku testowego i korzystać z niego. Zapoznaj się z scenariuszem dotyczącym korzystania z oferty i dokładnie tego, co klient powinien znać, aby uzyskać dostęp do funkcji na całym dysku testowym. W tym polu można wprowadzić do 10 000 znaków tekstu.

- **Podręcznik użytkownika** (wymagane): szczegółowy przewodnik dotyczący środowiska testowego. Podręcznik użytkownika powinien obejmować dokładnie te informacje, które klient ma uzyskać, z dysku testowego i służy jako odwołanie do wszelkich pytań, które mogą mieć. Plik musi być w formacie PDF i mieć nazwę (maksymalnie 255 znaków) po przekazaniu.

- **Wideo: Dodaj wideo** (opcjonalnie): klipy wideo można przekazać do usługi YouTube lub Vimeo i przywoływane w tym miejscu za pomocą linku i miniatury (533 x 324 pikseli), aby klient mógł wyświetlić szczegółowe informacje ułatwiające lepsze zrozumienie dysku testowego, w tym jak pomyślnie korzystać z funkcji oferty i zrozumieć scenariusze, które wyróżnią swoje korzyści.
  - **Nazwa** (wymagana)
  - **Adres URL (tylko w usłudze YouTube lub Vimeo)** (wymagany)
  - **Miniatura (533 x 324 pikseli)** : plik obrazu musi być w formacie PNG.

Po zakończeniu tych pól wybierz pozycję **Zapisz** .

## <a name="publish"></a>Publikowanie

### <a name="submit-offer-to-preview"></a>Prześlij ofertę do wersji zapoznawczej

Po zakończeniu wszystkich wymaganych sekcji oferty wybierz pozycję **Publikuj** w prawym górnym rogu portalu. Nastąpi przekierowanie do strony **Recenzja i publikowanie** . 

Jeśli po raz pierwszy publikujesz tę ofertę, możesz:

- Zobacz stan ukończenia dla każdej sekcji oferty.
    - *Nie uruchomiono* — oznacza, że sekcja nie została dotknięcia i należy ją ukończyć.
    - *Niekompletne* — oznacza, że sekcja zawiera błędy, które muszą zostać naprawione lub wymaga podania więcej informacji. Wróć do sekcji i zaktualizuj ją.
    - *Gotowe* — oznacza, że sekcja została ukończona, wszystkie wymagane dane zostały dostarczone i nie występują żadne błędy. Wszystkie sekcje oferty muszą być w stanie kompletnym, zanim będzie możliwe przesłanie oferty.
- Podaj instrukcje dotyczące testowania dla zespołu certyfikacji, aby upewnić się, że aplikacja została prawidłowo przetestowana, a także wszelkich dodatkowych notatek przydatnych w zrozumieniu swojej aplikacji.
- Prześlij ofertę do opublikowania, wybierając pozycję **Prześlij**. Wyślemy Ci wiadomość e-mail z prośbą o udostępnienie wersji zapoznawczej oferty, którą można przejrzeć i zatwierdzić. Wróć do Centrum partnerskiego i wybierz pozycję **Przejdź na żywo** , aby uzyskać ofertę opublikowania oferty na publiczną (lub w przypadku prywatnej oferty dla odbiorców prywatnych).

### <a name="errors-and-review-feedback"></a>Błędy i przejrzyj opinię

Krok **ręcznego walidacji** w procesie publikowania reprezentuje obszerny przegląd oferty i skojarzonych z nią zasobów technicznych (szczególnie Azure Resource Manager szablon), jednak problemy są zwykle przedstawiane jako linki żądania ściągnięcia. Informacje na temat sposobu wyświetlania tych żądań ściągnięcia i reagowania na nie można znaleźć w temacie [Obsługa informacji zwrotnych](./azure-apps-review-feedback.md).

Jeśli wystąpią błędy w co najmniej jednym z kroków publikacji, należy je poprawić i ponownie opublikować ofertę.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
