---
title: Pakiet Cortana intelligence | Dokumentacja firmy Microsoft
description: Publikowanie oferty analizy Cortany.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: f12a15a0d739ae6e98be3871fa8bb4104f49565c
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810955"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Publikowanie oferty pakietu Cortana Intelligence, za pomocą portalu Cloud Partner

W tym artykule opisano, jak opublikować ofertę pakietu Cortana Intelligence przy użyciu portalu Cloud Partner.

## <a name="prerequisites"></a>Wymagania wstępne

Cloud Partner Portal obsługuje opartej na rolach dostęp do portalu, który umożliwia współautorów do współpracy nad stawiane ofertom. Aby uzyskać więcej informacji, zobacz [chmurze Portal Zarządzanie użytkownikami](./cloud-partner-portal-manage-users.md).

Można było opublikować ofertę w imieniu wydawcy konta, jedną z osób z \"właściciela\" konieczność roli zobowiązuje się do przestrzegania [warunki użytkowania](https://azure.microsoft.com/support/legal/website-terms-of-use/), [poufności informacji firmy Microsoft](http://www.microsoft.com/privacystatement/default.aspx), i [Umowa dotycząca programu certyfikat platformy Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Aby rozpocząć tworzenie oferty Cortana Inteligence

Gdy spełniono wszystkie wymagania wstępne, możesz gotowe do rozpoczęcia tworzenia oferty Inteligence Cortany.

1.  Zaloguj się do [portalu cloud partner](http://cloudpartner.azure.com/).
2.  Na pasku nawigacyjnym po lewej stronie wybierz **+ nowa oferta**.
3. Wybierz **pakietu Cortana Intelligence**.
4. Wybierz **edytora** karcie **nowa oferta** widok, aby skonfigurować następujące opcje:
    -   Ustawienia oferty
    -   Informacje techniczne
    -   Szczegóły sklepu
    -   Kontakty

    Każda z tych opcji wyświetlania formularza, które możesz wypełnić.  Wymagane pola dla każdego formularza są oznaczone czerwoną gwiazdką (\*).

## <a name="to-configure-offer-settings"></a>Aby skonfigurować ustawienia oferty

Ustawienia oferty zawiera podstawowe informacje na temat tej oferty, takie jak identyfikator oferty, identyfikator wydawcy i nazwa oferty.

### <a name="offer-id"></a>Identyfikator oferty

Jest to unikatowy identyfikator oferty w ramach profilu wydawcy.
Ten identyfikator jest widoczny w adresach URL produktu. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. 
>[!Note]
>To pole jest zablokowane, gdy oferty przechodzi na żywo.

**Przykład:**

Jeśli wydawca **contoso** tworzy oferty z Identyfikatorem oferty *przykładowe Cortana Intelligence*, pojawi się w usłudze AppSource, jako "https://appsource.microsoft.com/marketplace/apps/**contoso**. *Przykładowe Cortana Intelligence*? tab = — omówienie ".

### <a name="publisher-id"></a>Identyfikator wydawcy

Lista rozwijana lista profilów wydawcy. Służy do wybierania profilu wydawcy, który chcesz opublikować tę ofertę, w obszarze.

>[!Note]
>To pole jest zablokowane, gdy oferty przechodzi na żywo.

### <a name="name"></a>Name (Nazwa)

Nazwa wyświetlana oferty. Ta nazwa będzie wyświetlana w [AppSource](https://appsource.microsoft.com). Może zawierać maksymalnie 50 znaków.

Po zakończeniu realizacji informacji potrzebnych w ustawieniach oferty, wybierz **Zapisz** przejść do części informacji technicznych oferty. Opcja.

## <a name="to-configure-technical-info"></a>Aby skonfigurować informacje techniczne

Użyj tego widoku, aby zapewnić informacje techniczne, która będzie wyświetlana na stronie oferty. Następujące pola mają zastosowanie do tego widoku.

### <a name="partner-mpn-id"></a>Identyfikator MPN partnera

Jeśli jesteś partnerem firmy Microsoft zarejestrowanych, przejdź do [witryny sieci Web partnerów](https://partners.microsoft.com/) i logowanie w celu pobierania swojego identyfikatora organizacji partnera. Podaj ten identyfikator **partnerów identyfikator MPN**.

### <a name="analytics-components-used"></a>Analiza składniki używane

Wybierz wszystkie składniki, które korzysta z ofertą. Wybierz co najmniej 1 składnik. Wybierz **Microsoft R** tylko wtedy, gdy używasz któregokolwiek z licencji PANI SQL 2016 R services, HDInsight Premium lub PANI działające na maszynach wirtualnych.

### <a name="additional-components-used"></a>Dodatkowe składniki używane

Wybierz wszystkich komponentów, które korzysta z rozwiązania.

### <a name="customer-name-using-solution"></a>Nazwa klienta za pomocą rozwiązania

Podaj nazwę klienta, który za pomocą tego rozwiązania w środowisku produkcyjnym. 

>[!Note]
>Te informacje nie są publikowane w usłudze AppSource. Służy ono wyłącznie do oceny rozwiązania.

### <a name="azure-consumption-requirement-met"></a>Spełnione wymaganie użycia platformy Azure?

Wskazuje, czy rozwiązania generuje co najmniej $1 K na miesiąc dla każdej klienta składników zestawu Azure lub jeśli rozwiązanie używa R. firmy Microsoft

>[!Note]
>Te informacje nie są publikowane w usłudze AppSource. Służy ono wyłącznie do oceny rozwiązania.

### <a name="demo-video-url"></a>Adres URL filmu wideo pokaz

Podaj adres URL pokaz wideo dla rozwiązanie/aplikację, że przedsprzedaży zespołu można wyświetlić klientów. 

>[!Note]
>Te informacje nie są publikowane w usłudze AppSource. Służy ono wyłącznie do oceny rozwiązania.

#### <a name="demo-video-guidelines"></a>Pokaz wideo wytyczne

- Wideo długość powinna wynosić mniej niż 15 minut.
- Film wideo powinny być edytowane w co najmniej, rejestrowanie funkcji tego rozwiązania. 
- Film wideo wyróżnia kluczowych aspektów funkcji przeznaczonych dla użytkowników i koncentruje się na integrację zaawansowanej analizy. 
- Pokaz wideo **nie będzie** udostępniane publicznie dla klientów, ale powinny być reprezentatywna jak rozwiązanie *będzie* pokazano do potencjalnego klienta. Jako takie powinny one być inicjowane przez skrypty i powtarzalne.
- Użyj dowolnego narzędzia do nagrywania ekranu eksportujący standardowy format wideo (na przykład MPEG) do utworzenia filmu wideo. 

Poniższe instrukcje przedstawiają sposób tworzenia pliku wideo za pomocą programu Skype dla firm. 

1. [Rozpocznij spotkania](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Udostępnianie pulpitu](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Rozpocznij nagrywanie](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. Po zatrzymaniu nagrywania, [publikować rejestrowanie za pomocą Menedżera rejestrowania](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Przekaż swoje nagrania wideo do usługi, która umożliwia generowanie adresu URL udostępnionych. Na przykład [łącze gościa w usłudze OneDrive lub Sharepoint](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Obsługiwane regiony

Wybierz wszystkie regiony, które obsługuje rozwiązania. Wybierz region, co najmniej 1.

### <a name="power-bi-desktop-file-pbix"></a>Plik pulpitu usługi Power BI (pbix)

Jeśli to konieczne, należy przekazać plik pbix. Upewnij się, że dane są importowane do pliku, nie odwołuje się zewnętrznie. Utworzymy osadzonego raportu dla Ciebie.

### <a name="solution-architecture"></a>Architektura rozwiązania

Przekaż dokument ze szczegółami, architektura tego rozwiązania. Do przekazywania wskazówką diagram architektury rozwiązania, zobacz [szablonu przepływu pracy zaawansowanych rozwiązań analizy](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Te informacje nie są publikowane w usłudze AppSource. Służy ono wyłącznie do oceny rozwiązania.

### <a name="select-segments"></a>Wybierz segmentów

Wybierz wszystkie segmentach branży powiązane. Jeśli Twoja aplikacja jest każdego segmentu nieznajdujące się na liście, wprowadź nazwę segmentu w **innych** pola. Są ograniczone do trzech słów segmentu.

### <a name="select-business-processes"></a>Wybierz procesy biznesowe

Wybierz procesy biznesowe, które najlepiej opisują rozwiązania. Jeśli aplikacja znajduje się na wszystkie procesy nieznajdujące się na liście, wprowadź nazwę procesu w **innych** pola. Są ograniczone do trzech słów dla procesu.

### <a name="trial-info"></a>Informacje o wersji próbnej

-   **Adres URL wersja próbna SaaS:** wprowadź adres URL aplikacji Wersja próbna.
-   **Testuj adres URL wersji próbnej dysku:** wprowadź adres URL środowiska wersji testowej Twojej aplikacji.

Aby uzyskać więcej informacji na temat wersji próbnych, zobacz **typ aplikacji** w następnej sekcji tego artykułu.

Po zakończeniu realizacji informacji potrzebnych w informacje techniczne, wybierz **Zapisz** przejść do części szczegółów Storefront oferty. 

## <a name="to-configure-storefront-details"></a>Aby skonfigurować szczegóły sklepu

### <a name="offer-summary"></a>Podsumowanie oferty

Jest to podsumowanie Twoja oferta korzyści. Pojawi się na stronie wyszukiwania swojej oferty. Maksymalna długość podsumowania to 100 znaków.

### <a name="offer-description"></a>Opis oferty

Jest to opis, który będzie wyświetlany na stronie szczegółów aplikacji.
Maksymalna długość opisu to 1300 znaków.

Należy pamiętać, że to pole ma html zawartości przy użyciu tagów, takich jak \<p\>, \<h1\>, \<h2\>, \<li\>, itp., co pozwala na znacznie bardziej oznaczanie zawartości zawartości. Publikowanie portalu zespołu działa na dodanie funkcji do umożliwiających wyświetlenie podglądu ich szczegóły storefront się interakcyjnie zawartość więcej zawartości — w tym czasie możesz użyć dowolnej można użyć dowolnego narzędzia online w czasie rzeczywistym html, takie jak [ http://htmledit.squarefree.com ](http://htmledit.squarefree.com/) aby zobaczyć, jak będzie wyglądać opis.

Sugerowanego formatu opisu jest mają dzielenia tekstu na sekcje podrzędnych na podstawie wartości propositions, każdy z wyróżnioną pozycją podrzędnych. Osoby odwiedzające zwykle Przegląd przez pole "oferować summary" i nagłówków podrzędnych, aby uzyskać gist adresy aplikacji i dlaczego ich rozważyć aplikacji szybkiego dostępu. Tak, jest ważne, aby pobrać użytkownika\'uwagi s ciągowych przyczynę Czytaj dalej, aby uzyskać szczegółowe informacje.

#### <a name="partner-examples"></a>Przykłady partnera

- [Optymalizacja magazynu Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Plexure handlu detalicznego, personalizacji](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [Usług dla obywateli — AvePoint](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Branże

Wybierz branżę, który aplikacji najlepiej jest wyrównany do. Jeśli aplikacja odnosi się do wielu branżach, pozostaw to pole puste.

### <a name="categories"></a>Kategorie

Wybierz kategorie, które są istotne dla twojej aplikacji. Wybierz maksymalnie dwie kategorie.

### <a name="app-type"></a>Typ aplikacji

Wybierz typ wersję próbną, że Twoja aplikacja będzie obsługiwać w usłudze AppSource. Wybierz na następujących prób:
- **Bezpłatne** oznacza, że Twoja aplikacja jest bezpłatna.
- **Wersja próbna** oznacza, że klienci mogą wypróbować usługi w podanym okresie.
- **Żądanie dotyczące wersji próbnej** oznacza, że klienci mogą poprosić o z wersji próbnej usługi aplikacji.

Partnerzy można przekazać dwa rodzaje środowisk wersji próbnej w usłudze AppSource.

- **Wersji próbnej** opcji, nazywany również **klientów doprowadziły prób (CLT)** może być jedną z następujących typów: 
    - Wersja próbna SaaS
        - Klienta można przejść do adresu Url, który użytkownik lub Twojego partnera. Klient przechodzi przez federacyjnych usługi AAD, że logowanie Jednokrotne, aby mieć czas opakowany wersja próbna.
        - Jest to aplikacja SaaS przy użyciu wielu dzierżawców, który izoluje użytkownika/dane konfiguracji od innych użytkowników. Czy to środowisko stanowi tylko podzestaw, który używa operacji tylko do odczytu.

        **Przykłady:**

        - [Wykonanie handlu detalicznego POP AFS](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [Usług dla obywateli AvePoint](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). (Ta aplikacja zapewnia wyselekcjonowanych ze ścieżkami wyczyść wybrany zestaw osób użytkownika.)

     - Wersja testowa
        - Usługi (lub usługi partnera) rozwiązania lub ich część można spakować przy użyciu szablonów usługi Azure Resource Manager, które usługi AppSource, można utworzyć wystąpienie i. Usługa AppSource można zarządzać aplikacji w ramach subskrypcji partnera z czasem konwersja boxing i utrzymywanie gorące i zimne pulę wystąpień itp.
        - Oferujemy szablonów i przykładowy kod, aby pomóc, jeśli ta opcja jest wybrana.

        **Przykłady:**

        - [Optymalizacja magazynu Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- **Wniosek o wersję próbną** (**prób prowadzone partnera / czas ładowania strony**) opcja wymaga od klientów do wypełnienia formularza informacje kontaktowe monitującą partnera. Partner następuje i zapewnia demonstracyjnym lub wersji próbnej usługi aplikacji. Aby uzyskać więcej informacji, zobacz [wskazówki wersji próbnej środowiska usługi AppSource](http://aka.ms/trialexperienceforwebapps) wideo, aby uzyskać ogólne omówienie.

>[!Note]
>Dane pokazują, że **klientów doprowadziły prób** mieć wyższej generacji potencjalnego klienta, potencjalne niż **prób prowadzone partnera**.


### <a name="help-link-for-your-app"></a>Link Pomoc dla aplikacji

Podaj adres URL do strony zawierającej informacje o aplikacji.

### <a name="supported-countriesregions"></a>Obsługiwane kraje/regiony

To pole określa krajów/regionów, w której oferty będzie dostępna w wersji próbnej.

![Obsługiwane kraje/regiony](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Obsługiwane języki

Wybierz języki, które obsługuje aplikacja. Jeśli aplikacja obsługuje języki, które nie znajdują się na tej liście, opublikować ofertę i wiadomość e-mail na adres: <appsource@microsoft.com> aby dać nam znać o obsługę innych języków.

### <a name="app-version"></a>Wersja aplikacji

Wprowadź numer wersji aplikacji.

### <a name="products-your-app-works-with"></a>Produkty, których Twoja aplikacja współpracuje ze

Lista określonych produktów, które Twoja aplikacja współpracuje ze. Możesz wyświetlić listę maksymalnie 3 produktów. Aby wyświetlić listę produktów, wybierz **znak plus** (obok nowych) i pole tekstowe open jest tworzona automatycznie. Wprowadź nazwę produktu, który Twoja aplikacja współpracuje ze.

### <a name="hide-key"></a>Ukrywanie klucza

Jest to klucz, w połączeniu z oferty wersji zapoznawczej adres URL, aby ukryć oferty z widoku publicznego. Nie jest hasłem. Można wprowadzić dowolny ciąg alfanumeryczny.

### <a name="offer-logo-small"></a>Logo oferty (mała liczba godzin)

To pojawia się na stronie wyszukiwania w swojej aplikacji. Format obrazu png jest jedynym formatem, jaki jest dozwolony. Rozmiar obrazu jest pikseli 48 x 48 pikseli.

### <a name="offer-logo-large"></a>Logo oferty (duża liczba godzin)

To jest wyświetlana na stronie szczegółów aplikacji. Format obrazu png jest jedynym formatem, jaki jest dozwolony. Rozmiar obrazu to 48 pikseli x 48 pikseli.

### <a name="video"></a>Połączenia wideo

Możesz przekazać maksymalnie cztery filmów wideo. Dla każdego pliku wideo, który chcesz przekazać:
- Podaj nazwę wideo
- Podaj adres URL (w usłudze YouTube lub Vimeo tylko)
- Podaj miniaturę, aby skojarzyć z filmu wideo. Miniatury, należy użyć formatu obrazu png, a jego rozmiar musi być pikseli 1280 X 720 pikseli. 

Aby dodać nowe wideo, wybierz **+ nowy**, jak pokazano w następnym zrzucie ekranu.

![Dodaj nowy film wideo](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Dokument

Możesz przekazać maksymalnie trzy dokumenty. Każdy dokument muszą używać formatu plików PDF. Aby dodać nowy dokument:

- Wybierz **+ nowe**
- Wprowadź nazwę dokumentu
- Wybierz **przekazywanie** do przekazania dokumentu.

![Dodaj nowy dokument.](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Zasady ochrony prywatności

Wprowadź adres URL dla swojej aplikacji, zasady zachowania poufności informacji

### <a name="terms-of-use"></a>Warunki użytkowania

Wprowadź warunki użytkowania Twojej aplikacji. Klienci usługi AppSource są wymagane do je zaakceptować, zanim użytkownik podejmie próbę Twojej aplikacji.

>[!Note]
>To pole akceptuje zawartości przy użyciu języka HTML tagów, takie jak zawartość html < p\>, < h1\>, i < li\>. Tych tagów można użyć do formatowania zawartości. 

### <a name="lead-destination"></a>Prowadzić docelowego

Wybierz system CRM, gdzie będą przechowywane potencjalnymi klientami. 

Wybierz **Azure Table** Jeśli używasz jednego z tych systemów CRM: Salesforce, Marketo i Microsoft Dynamics CRM. 

Aby uzyskać więcej informacji na temat systemu CRM, dla których chcesz użyć, wybierz jedną z następujących linków dla obsługiwanych systemów.

-   [Tabela platformy Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
