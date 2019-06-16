---
title: Instalowanie aplikacji w portalu wydawców
description: Instrukcje w sposób konfigurowania aplikacji w portalu wydawców w chmurze.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6da80bd3b5fe487a44400f4f3c21e8fa49da75d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943556"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Instalowanie aplikacji w portalu wydawców

Teraz można przystąpić do konfigurowania aplikacji w portalu wydawców.

## <a name="login-and-create-a-new-offer"></a>Zaloguj się i Utwórz nową ofertę

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. Na pasku nawigacyjnym po lewej stronie kliknij pozycję "+ nowe oferty" i wybierz pozycję "Dynamics 365 for Customer Engagement."

![Wybierając nową ofertę](./media/CRMScreenShot14.png)

1. Nowa oferta "Editor" widok jest teraz otwarte i jesteśmy gotowi rozpocząć tworzenie.

![Nowy ekran oferty](./media/CRMScreenShot15.png)

1. "Form", które muszą być wypełnione są widoczne po lewej stronie w widoku "Editor". Każdy "form" zawiera zestaw pól, które mają zostać wypełnione. Wymagane pola są oznaczone czerwoną gwiazdką (\*).

Istnieją cztery główne formularze na potrzeby tworzenia Dynamics 365 Customer Engagement oferty

* Ustawienia oferty
* Informacje techniczne
* Szczegóły sklepu
* Kontakty

## <a name="fill-out-the-offer-settings-form"></a>Wypełnij formularz ustawień oferty

Formularz ustawień oferty jest podstawowej postaci, aby określić ustawienia oferty. Poniżej opisano różne pola.

### <a name="offer-id"></a>Identyfikator oferty

Jest to unikatowy identyfikator oferty w ramach profilu wydawcy. Ten identyfikator będzie widoczny w adresach URL produktu. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. To pole jest zablokowane, gdy oferty przechodzi na żywo.

na przykład, jeśli Wydawca **"contoso"** wydawców tworzy oferty z Identyfikatorem oferty **"Przykładowy WebApp"** , pojawi się w usłudze AppSource, jako "https:\//appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>Identyfikator wydawcy

Tej listy rozwijanej można wybrać profil wydawcy, który chcesz opublikować tę ofertę, w obszarze. To pole jest zablokowane, gdy oferty przechodzi na żywo.

### <a name="name"></a>Name (Nazwa)

Jest to nazwa wyświetlana oferty. Jest to nazwa, która będzie wyświetlana w [AppSource](https://appsource.microsoft.com/). Może zawierać maksymalnie 50 znaków.

Kliknij przycisk "Zapisz", aby zapisać postęp. Następnym krokiem powinno być, aby dodać informacje techniczne dla oferty.

## <a name="fill-out-the-technical-info-form"></a>Wypełnij formularz informacje techniczne


Formularz informacji technicznych jest, gdzie możesz wypełni informacje charakterystyczne do usługi Dynamics 365 Customer Engagement rozwiązania. Kursor będzie wyświetlane z większą ilością informacji. Zobacz przykład poniżej.

![Ekran informacje techniczne](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informacje o aplikacji

Większość wydawców spowoduje, że te pola z wartościami domyślnymi, użytkownik nie, nie, a pusty adres URL konfiguracji aplikacji, zgodnie z powyższym zrzucie ekranu.

### <a name="crm-package"></a>Pakiet aplikacji CRM

![Informacje o pakiecie aplikacji CRM](./media/CRMScreenShot17.png)

Oto wyjaśnienia dla tych pól:

* Nazwa pliku pakietu: Nazwa pliku utworzonego w kroku powyżej podczas tworzenia pliku zip, który jest CRM pakietu usługi AppSource. W powyższym przykładzie jest "Microsoft\_SamplePackage.zip".
* Adres URL lokalizacji pakietu: Jest to adres URL konta usługi Azure Storage, która zawiera nazwę pliku pakietu, które są wymienione powyżej. Jest to adres URL utworzony w kroku 9 sekcji powyżej.
* Plik pakietu jest więcej niż jeden pakiet crm: Wybierz pozycję Tak, **tylko** jeśli są obsługiwanie wielu wersji programu crm przy użyciu różnych pakietach. Dla większości partnerów są to "No". Jeśli wybierzesz tak, musisz utworzyć pakiety usługi AppSource dla każdej wersji rozwiązania. _Uwaga: NIE jest to pytanie Jeśli masz wiele **zip** plików. Jeśli masz wiele plików solution.zip, ale tylko jedna wersja, należy nadal wybrać "Nie." Narzędzia pakietu zostanie wyświetlone są ze sobą automatycznie._

### <a name="crm-package-availability"></a>Dostępność pakietu CRM

W tej sekcji należy wybrać jakie regiony CRM, pakiet zostanie udostępniona w celu. Aby uzyskać informacje na które CRM regiony obsługi którego kraje/regiony, zobacz łącze: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Uwaga: Wdrażanie w Niemczech "Suwerennych i chmura rządowa USA" suwerennych wymaga uprawnień specjalnych i sprawdzanie poprawności podczas certyfikacji

## <a name="storefront-details"></a>Szczegóły sklepu

### <a name="offer-summary"></a>Podsumowanie oferty

Jest to podsumowanie Twoja oferta korzyści. Pojawi się na stronie wyszukiwania swojej oferty. Należy się z maksymalnie 100 znaków.

### <a name="offer-description"></a>Opis oferty

Jest to opis, który będzie wyświetlany na stronie szczegółów aplikacji. Maksymalna dozwolona wartość to 1300 znaków

### <a name="industries"></a>Branże

Wybierz branżę, który aplikacji najlepiej jest wyrównany do. Jeśli aplikacja ma odnosi się do wielu branżach, można pozostawić to puste.

### <a name="categories"></a>Categories

Wybierz kategorie, które są istotne dla twojej aplikacji. Wybierz więcej niż 3.

### <a name="app-type"></a>Typ aplikacji

Wybierz typ wersji próbnej, która umożliwi Twojej aplikacji w usłudze AppSource. "Bezpłatnej" oznacza, że Twoja aplikacja jest bezpłatna. "Wersja próbna" oznacza, że klienci mogą wypróbować aplikację przez krótki okres w usłudze AppSource. "Żądanie dotyczące wersji próbnej" nie jest obsługiwana dla Dynamics 365 Customer Engagement aplikacji. Nie należy zaznaczać tej opcji.

### <a name="help-link-for-your-app"></a>Link Pomoc dla aplikacji

Wprowadź adres URL do strony, która ma powiązane informacje pomocy dla swojej aplikacji.

### <a name="supported-countriesregions"></a>Obsługiwane kraje/regiony

To pole określa krajów/regionów, w których Twoja oferta będzie dostępna dla wersji próbnej.

### <a name="supported-languages"></a>Obsługiwane języki

Wybierz języki, które obsługuje aplikacja. Jeśli aplikacja obsługuje dodatkowe języki, które nie znajdują się na tej liście, w dalszym ciągu opublikować ofertę i wiadomość e-mail na adres: [ appsource@microsoft.com ](mailto:appsource@microsoft.com) aby dać nam znać.

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji aplikacji

### <a name="app-release-date"></a>Data wydania aplikacji

Wprowadź datę wydania aplikacji

### <a name="products-your-app-works-with-max-3"></a>Produkty, których Twoja aplikacja współpracuje ze (maksymalna liczba 3)

Specyficzne produkty, których Twoja aplikacja współpracuje ze. Listę można wyświetlić maksymalnie trzy produkty. Aby wyświetlić listę produktów, kliknij znak plus (obok nowy), a nowe pole tekstowe Otwórz zostanie utworzona dla Ciebie, wprowadź nazwę produktu, który Twoja aplikacja współpracuje ze.

### <a name="search-keywords-max-3"></a>Słowa kluczowe (maksymalna liczba 3)

Usługa AppSource umożliwia klientowi wyszukiwanie na podstawie słów kluczowych. Można wprowadzić zbiór słów kluczowych, dla których aplikacja będzie widoczna dla klientów.

Na przykład jeśli aplikacja jest "Mój wysyłanie wiadomości E-mail usługi" wiadomości E-mail, korespondencji, wiadomości E-mail może być kilka słów kluczowych. Wybór słów, które użytkownicy prawdopodobnie będzie używany do wyszukiwania dla aplikacji w polu wyszukiwania w usłudze AppSource.

### <a name="hide-key"></a>Ukrywanie klucza

Jest to klucz, który zostanie połączony z oferty wersji zapoznawczej adres URL, aby je ukryć z widoku publicznego. Nie jest hasłem. Można wprowadzić dowolny ciąg, w tym miejscu.

### <a name="offer-logo-png-format-48x48"></a>Logo oferty (w formacie png, 48 x 48)

To będzie wyświetlane na stronie wyszukiwania Twojej aplikacji. **Dozwolone jest tylko format png.** Przekazywanie obrazu png z rozpoznawaniem 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logo oferty (w formacie png, 216 x 216)

To będą wyświetlane na stronie szczegółów aplikacji. **Dozwolone jest tylko format png.** Przekazywanie obrazu png z rozpoznawaniem 216PX\*216PX

### <a name="videos"></a>Filmy wideo

Możesz przekazać maksymalnie cztery filmów wideo. Dla każdego filmu wideo, który chcesz przekazać należy podać nazwę wideo, adres URL (w usłudze YouTube lub Vimeo tylko) i miniatury do skojarzenia z filmu wideo. Miniatura musi być w formacie png, a musi być 1280PX\*720PX. Aby dodać nowe wideo, kliknij znak plus. Thumbnail(s) filmów wideo będzie wyświetlany na stronie szczegółów Twojej aplikacji.

### <a name="documents"></a>Dokumenty

Możesz przekazać maksymalnie trzy dokumenty w formacie PDF. Dla każdego dokumentu, który chcesz przekazać musisz podać nazwę dokumentu i Przekaż dokument. Dokument musi być w formacie pdf.

Aby dodać nowe dokumenty, kliknij znak plus

### <a name="screenshots"></a>Zrzuty ekranu

Oto zrzuty ekranu, który będzie wyświetlany na stronie szczegółów AppSource dla aplikacji.

### <a name="privacy-policy"></a>Zasady ochrony prywatności

Wprowadź adres URL do swojej aplikacji, zasady zachowania poufności informacji

### <a name="terms-of-use"></a>Warunki użytkowania

Wprowadź warunki użytkowania Twojej aplikacji. Klienci usługi AppSource są proszeni o zaakceptowanie tych warunków, zanim użytkownik podejmie próbę aplikacji

### <a name="support-url"></a>Adres URL pomocy technicznej

Wprowadź adres URL pomocy technicznej dla aplikacji.

### <a name="lead-destination"></a>Prowadzić docelowego

Wybierz system CRM, gdzie można prowadzić będą przechowywane. Należy wybrać to "Usługi Azure Table", jeśli masz jedną z następujących systemów CRM: Salesforce, Marketo, Microsoft Dynamics CRM. System CRM, tutaj jest, gdzie będzie zapisywać szczegóły użytkownicy końcowi, którzy spróbują aplikacji w usłudze AppSource (potencjalnych klientów). W zależności od systemu CRM, którą wybierzesz kliknij odpowiedni adres URL poniżej informacje na temat sposobu ukończenia kolejny zbiór pól

* [Tabela platformy Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Szczegóły sklepu

Szczegółowe dane kontaktowe są używane tylko do komunikacji wewnętrznej między partnerem i firmą Microsoft. Uwaga: Należy użyć adresu e-mail, który jest monitorowany w tych polach. Firma Microsoft użyje tej wiadomości e-mail do komunikowania się z Tobą na swoje postępy w nauce publikowanie w usłudze AppSource. Adres URL pomocy technicznej będą widoczne dla klientów.
