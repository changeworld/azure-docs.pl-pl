---
title: Konfigurowanie aplikacji w portalu publikowania
description: Instrukcje dotyczące konfigurowania aplikacji w portalu publikowania w chmurze.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280205"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Konfigurowanie aplikacji w portalu publikowania

Teraz możesz skonfigurować aplikację w portalu publikowania.

## <a name="login-and-create-a-new-offer"></a>Zaloguj się i stwórz nową ofertę

1. Zaloguj się do [portalu cloud partner](https://cloudpartner.azure.com/).
2. Na lewym pasku nawigacyjnym kliknij "+ Nowa oferta" i wybierz "Dynamics 365 for Customer Engagement".

![Wybór nowej oferty](./media/CRMScreenShot14.png)

1. Nowa oferta "Edytor" widok jest teraz otwarty dla Ciebie, i jesteśmy gotowi, aby rozpocząć tworzenie.

![Ekran Nowa oferta](./media/CRMScreenShot15.png)

1. "Formularze", które muszą być wypełnione są widoczne po lewej stronie w widoku "Edytor". Każdy "formularz" składa się z zestawu pól, które mają być wypełnione. Wymagane pola są oznaczone czerwoną gwiazdką (\*).

Istnieją cztery główne formularze do tworzenia oferty Dynamics 365 for Customer Engagement

* Ustawienia oferty
* Informacje techniczne
* Szczegóły witryny sklepowej
* Kontakty

## <a name="fill-out-the-offer-settings-form"></a>Wypełnij formularz Ustawienia oferty

Formularz ustawień oferty jest podstawowym formularzem określającym ustawienia oferty. Poniżej opisano różne pola.

### <a name="offer-id"></a>Identyfikator oferty

Jest to unikatowy identyfikator oferty w profilu wydawcy. Ten identyfikator będzie widoczny w adresach URL produktów. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się kreską i może mieć maksymalnie 50 znaków. To pole jest zablokowane po uruchomieniu oferty.

na przykład, jeśli wydawca **"contoso"** wydawca tworzy ofertę o identyfikatorze oferty **"sample-WebApp",** pojawi\/się w AppSource jako "https: /appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>Identyfikator wydawcy

Ta rozwijana umożliwia wybranie profilu wydawcy, pod którego chcesz opublikować tę ofertę. To pole jest zablokowane po uruchomieniu oferty.

### <a name="name"></a>Nazwa

Jest to nazwa wyświetlana oferty. Jest to nazwa, która pojawi się w [AppSource](https://appsource.microsoft.com/). Może zawierać maksymalnie 50 znaków.

Kliknij na "Zapisz", aby zapisać swoje postępy. Następnym krokiem będzie dodanie informacji technicznych dla oferty.

## <a name="fill-out-the-technical-info-form"></a>Wypełnij formularz Informacje techniczne


Formularz informacji technicznych to miejsce, w którym należy wypełnić informacje specyficzne dla rozwiązania Dynamics 365 for Customer Engagement. Najechanie kursorem na spowoduje, że będziesz miał więcej informacji. Zobacz przykład poniżej.

![Ekran informacji technicznych](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informacje o aplikacji

Większość wydawców pozostawi te pola z wartościami domyślnymi, Użytkownik, Nie, Nie i pusty adres URL konfiguracji aplikacji, jak na powyższym zrzucie ekranu.

### <a name="crm-package"></a>Pakiet CRM

![Informacje o pakiecie CRM](./media/CRMScreenShot17.png)

Oto wyjaśnienie dla tych pól:

* Nazwa pliku pakietu: Nazwa pliku utworzona w powyższym kroku podczas tworzenia pliku zip, który jest pakietem crm appsource. W powyższym przykładzie jest\_to "Microsoft SamplePackage.zip".
* Adres URL lokalizacji pakietu: jest to adres URL konta usługi Azure Storage, który zawiera nazwę pliku pakietu określoną powyżej. Jest to adres URL utworzony w kroku 9 powyższej sekcji.
* Czy w pliku pakietu znajduje się więcej niż jeden pakiet crm: Wybierz tak **TYLKO,** jeśli obsługujesz wiele wersji crm z różnymi pakietami. Dla większości partnerów będzie to "Nie". Jeśli wybierzesz Tak, musisz utworzyć pakiety AppSource dla każdej wersji rozwiązania. _Uwaga: Nie jest to pytanie, czy masz wiele plików **zip.** Jeśli masz wiele plików solution.zip, ale tylko jedną wersję, nadal należy wybrać "Nie". Narzędzie do pakowania połączy je automatycznie._

### <a name="crm-package-availability"></a>Dostępność pakietów CRM

W tej sekcji wybierz, w jakich regionach programu CRM pakiet zostanie udostępniony. Aby uzyskać informacje o tym, które regiony CRM obsługują kraje/regiony, można znaleźć pod linkiem:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Uwaga: Wdrażanie w Niemczech "Sovereign and US Gov Cloud" Sovereign wymaga specjalnego zezwolenia i walidacji podczas certyfikacji

## <a name="storefront-details"></a>Szczegóły witryny sklepowej

### <a name="offer-summary"></a>Podsumowanie oferty

Jest to podsumowanie propozycji wartości oferty. Pojawi się na stronie wyszukiwania oferty. Powinno to być maksymalnie 100 znaków.

### <a name="offer-description"></a>Opis oferty

Jest to opis, który pojawi się na stronie szczegółów aplikacji. Maksymalna dozwolona wartość to 1300 znaków

### <a name="industries"></a>Branże

Wybierz branżę, do których aplikacja jest najlepiej dopasowana. Jeśli aplikacja ma związek z wieloma branżami, możesz pozostawić to puste.

### <a name="categories"></a>Kategorie

Wybierz kategorie, które są istotne dla twojej aplikacji. Wybierz maksymalnie 3.

### <a name="app-type"></a>Typ aplikacji

Wybierz typ wersji próbnej, który aplikacja włączy w u źródła usług AppSource. "Za darmo" oznacza, że aplikacja jest bezpłatna. "Wersja próbna" oznacza, że klienci mogą wypróbować aplikację przez krótki okres w uźródle aplikacji. "Żądanie wersji próbnej" nie jest obsługiwane w aplikacjach Dynamics 365 for Customer Engagement. Nie należy wybierać tej opcji.

### <a name="help-link-for-your-app"></a>Link pomocy dla aplikacji

Wprowadź adres URL strony zawierającej informacje o pomocy dla aplikacji.

### <a name="supported-countriesregions"></a>Obsługiwane kraje/regiony

To pole określa kraje/regiony, w których oferta będzie dostępna w wersji próbnej.

### <a name="supported-languages"></a>Obsługiwane języki

Wybierz języki, które obsługuje aplikacja. Jeśli Twoja aplikacja obsługuje dodatkowe języki, których nie ma na tej liście, kontynuuj publikowanie oferty i wyślij do nas wiadomość e-mail na adres: [appsource@microsoft.com](mailto:appsource@microsoft.com) aby nas o tym powiadomić.

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji aplikacji

### <a name="app-release-date"></a>Data wydania aplikacji

Wprowadź datę wydania aplikacji

### <a name="products-your-app-works-with-max-3"></a>Produkty, z w które współpracuje twoja aplikacja (Maks. 3)

Lista produktów specyficznych dla aplikacji, z którymi współpracuje aplikacja. Można wyświetlić maksymalnie trzy produkty. Aby wyświetlić listę produktów, kliknij znak plus (obok nowego), a zostanie utworzone nowe otwarte pole tekstowe, aby wprowadzić nazwę produktu, z którą działa aplikacja.

### <a name="search-keywords-max-3"></a>Szukaj słów kluczowych (Maks. 3)

AppSource umożliwia klientowi wyszukiwanie na podstawie słów kluczowych. Możesz wprowadzić zestaw słów kluczowych, dla których aplikacja będzie wyświetlana klientom.

Na przykład, jeśli aplikacja jest "Moja usługa e-mailowania" E-maile, Korespondencja, Usługa poczty może być niektóre słowa kluczowe. Wybierz słowa, których użytkownicy będą prawdopodobnie używać do wyszukiwania aplikacji w polu wyszukiwania appsource.

### <a name="hide-key"></a>Ukryj klucz

Jest to klucz, który zostanie połączony z adresem URL podglądu oferty, aby ukryć go przed widokiem publicznym. Nie jest to hasło. W tym miejscu można wprowadzić dowolny ciąg znaków.

### <a name="offer-logo-png-format-48x48"></a>Logo oferty (format png, 48x48)

Pojawi się na stronie wyszukiwania aplikacji. **Dozwolony jest tylko format png.** Prześlij obraz png o\*rozdzielczości 48PX 48PX

### <a name="offer-logo-png-format-216x216"></a>Logo oferty (format png, 216x216)

Pojawi się na stronie szczegółów aplikacji. **Dozwolony jest tylko format png.** Prześlij obraz png o rozdzielczości\*216PX 216PX

### <a name="videos"></a>Filmy wideo

Możesz przesłać maksymalnie cztery filmy. Dla każdego filmu, który chcesz przesłać, musisz wpisać nazwę filmu, adres URL (tylko YouTube lub Vimeo) i miniaturę, aby skojarzyć go z filmem. Miniatura musi być w formacie png i\*musi być 1280PX 720PX. Aby dodać nowe filmy, kliknij znak plus. Miniatury filmów pojawią się na stronie szczegółów aplikacji.

### <a name="documents"></a>Dokumenty

W formacie PDF można przesłać maksymalnie trzy dokumenty. Dla każdego dokumentu, który chcesz przekazać, musisz podać nazwę dokumentu i przekazać dokument. Dokument musi być w formacie pdf.

Aby dodać nowe dokumenty, kliknij znak plus

### <a name="screenshots"></a>Zrzuty ekranu

Są to zrzuty ekranu, które pojawią się na stronie szczegółów usługi AppSource dla aplikacji.

### <a name="privacy-policy"></a>Zasady ochrony prywatności

Wprowadzanie adresu URL do zasad ochrony prywatności aplikacji

### <a name="terms-of-use"></a>Warunki użytkowania

Wprowadź warunki korzystania z aplikacji. Klienci usługi AppSource muszą zaakceptować te warunki, zanim będą mogli wypróbować aplikację

### <a name="support-url"></a>Adres URL pomocy technicznej

Wprowadź adres URL pomocy technicznej dla aplikacji.

### <a name="lead-destination"></a>Miejsce docelowe potencjalnego klienta

Wybierz system CRM, w którym będzie przechowywany potencjalny klient. Wybierz "Tabela platformy Azure" w tym miejscu, jeśli masz jeden z następujących systemów CRM: Salesforce, Marketo, Microsoft Dynamics CRM. System CRM, który wybierzesz tutaj, to miejsce, w którym będziemy pisać szczegóły użytkowników końcowych, którzy próbują aplikacji na AppSource (potencjalnych klientów). W zależności od wybranego systemu CRM kliknij odpowiedni adres URL poniżej, aby uzyskać informacje o tym, jak ukończyć następny zestaw pól

* [Tabela platformy Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Szczegóły witryny sklepowej

Dane kontaktowe są używane tylko do komunikacji wewnętrznej między partnerem a firmą Microsoft. Uwaga: Ważne jest, aby używać adresu e-mail, który jest monitorowany w tych polach. Użyjemy tej wiadomości e-mail do komunikowania się z Tobą na temat twoich postępów w publikowaniu w uźródle Aplikacji. Tylko adres URL pomocy technicznej będzie widoczny dla klientów.
