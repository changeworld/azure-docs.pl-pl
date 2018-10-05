---
title: Technical aplikacji SaaS Podręcznik publikowania | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak publikować aplikacje SaaS oferuje odpowiednie portalu marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 9d73040f11549741643d96913c42df49594b8d41
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810290"
---
<a name="saas-application-technical-publishing-guide"></a>Technical aplikacji SaaS Podręcznik publikowania
===========================================

Podręcznik publikowania techniczne aplikacje SaaS — Zapraszamy! Ten przewodnik jest przeznaczony do Release candidate i istniejących wydawców, aby wyświetlić listę swoich aplikacji i usług w usłudze AppSource lub za pomocą aplikacji SaaS, oferty w portalu Azure Marketplace.

Omówienie wszystkich innych ofert z portalu Marketplace można znaleźć [przewodnikiem dotyczącym publikowania w portalu Marketplace](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Jakie są wymagania wstępne dotyczące publikowania aplikacji SaaS?
-------------------------------------------------

Portal wydawców umożliwia opartej na rolach dostęp do portalu, dzięki czemu wiele osób do współpracy w kierunku stawiane ofertom. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami](./cloud-partner-portal-manage-users.md). 

Można było opublikować ofertę w imieniu wydawcy konta, jedną z osób z *właściciela* konieczność roli zobowiązuje się do przestrzegania [warunki użytkowania](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft PrivacyStatement](http://www.microsoft.com/privacystatement/default.aspx), i [ProgramAgreement certyfikat platformy Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Tworzenie oferty
-----------------

W tej sekcji opisano proces tworzenia nowej oferty aplikacji SaaS.

![SaaS oferują — omówienie](media/cpp-creating-saas-offers/saas-offer-overview.png)

Oferty aplikacji SaaS składa się z pięciu sekcji opisane w poniższej tabeli:

| **Sekcja oferty**  | **Opis**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Ustawienia oferty     | Pozwala określić unikatową nazwę i identyfikator dla aplikacji SaaS                                                                         |
| Informacje techniczne     | Ułatwia konfigurowanie typu rozwiązania SaaS i podaj szczegóły połączenia dla aplikacji                            |
| Wersja testowa         | Opcjonalna sekcja, która pozwala na zdefiniowanie to usługa, która umożliwi klientom przetestować oferty przed ich do zakupu. |
| Szczegóły sklepu | Zawiera marketing, informacje prawne, zarządzanie potencjalnymi klientami i listę sekcje:   <br/> -Sekcji Marketing umożliwia wprowadź opis i logo użytkownika wymagane do oferty, które mają być poprawnie wyświetlane w portalu marketplace użytkownika.  <br/> Zarządzanie potencjalnymi klientami umożliwia zdefiniowanie miejsce, w której ma zostać przekierowany użytkownik końcowy potencjalnych klientów, wygenerowane w portalu Azure Marketplace zakończenia użytkownika.  <br/> -Sekcji prawne umożliwia wprowadzanie Dokumentacja prawna termin użytkowania i zasady zachowania poufności informacji.  |
| Kontakt            | Umożliwia wprowadzanie informacji kontaktowych pomocy technicznej oferty.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Tworzenie nowej oferty

Po zalogowaniu się do portalu Cloud Partner, wybierz **nowa oferta** element na pasku menu po lewej stronie, który wyświetla menu dostępnych ofert. Na poniższej ilustracji przedstawiono przykład tych ofert:

![Nowa oferta SaaS](media/cpp-creating-saas-offers/saas-new-offer.png)

Wybierz oferty, możesz zostały zatwierdzone na liście, która zostanie otwarty nowy formularz oferty.

![Nowy formularz oferta SaaS](media/cpp-creating-saas-offers/saas-new-offer-2.png)

W poniższej tabeli opisano pola oferty:

| **Pola oferty** | **Opis**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| Identyfikator oferty         | Unikatowy identyfikator oferty w ramach profilu wydawcy. Ten identyfikator będzie widoczny w adresach URL produktu i raporty rozliczeń. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. Należy pamiętać, że to pole jest zablokowane, gdy oferty przechodzi na żywo. Na przykład jeśli wydawcy, Contoso, publikuje ofertę z maszynami wirtualnymi przykładowy identyfikator oferty, jego zostaną wyświetlone w witrynie Azure marketplace jako: [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| Identyfikator wydawcy     | Identyfikator wydawcy to unikatowy identyfikator użytkownika w portalu Marketplace. Wszystko, czego ofert powinien być dołączony swojego identyfikatora wydawcy. Nie można modyfikować Identyfikatora wydawcy, po zapisaniu tej oferty.                                                                                       |
| Name (Nazwa)             | Jest to nazwa wyświetlana oferty. Jest to nazwa, która będzie wyświetlana w portalu Azure Marketplace i w witrynie Azure Portal. Może zawierać maksymalnie 50 znaków. W tym miejscu są wskazówki do uwzględnienia rozpoznawalną nazwę markę produktu. Nie dołączaj nazwę swojej firmy, chyba że jest, jak jest sprzedawane. Ta oferta jest marketingu w swojej własnej witryny sieci Web, upewnij się, czy nazwa jest dokładnie tak jak zostanie ona wyświetlona w witrynie sieci Web.             |
|  |  |

Kliknij przycisk **Zapisz** Aby zapisać postęp. W następnej sekcji opisano Dodawanie planów do oferty.


### <a name="technical-information"></a>Informacje techniczne

Sekcja informacji technicznych umożliwia wprowadź następujące informacje:

![SaaS oferują informacje techniczne](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

Najważniejszych decyzji to, czy ma list SaaS lub commerce włączone. Jeśli masz listę SaaS, należy wybrać między:

-   Bezpłatne — Podaj adres URL aplikacji SaaS, w którym klienci mogą uzyskać aplikację.
-   Bezpłatna wersja próbna — Podaj adres URL aplikacji SaaS, w którym klienci mogą uruchamiać wersji próbnej, przed ich ofertę zakupu.
-   Kontakt ze mną — jest to istotne tylko w przypadku systemu zarządzania potencjalnymi klientami połączone ta opcja umożliwia klientom chcą otrzymywać i potencjalny klient zostanie Ci udostępniony.

Jeśli masz aplikację SaaS, która znajduje się w portalu Azure Marketplace i chce włączyć commerce za pomocą transakcji firmy Microsoft, wybierz opcję **sprzedawać za pośrednictwem platformy Azure**.  
Aby uzyskać więcej informacji na temat łączenia aplikacji SaaS, zobacz [SaaS — sprzedawać za pośrednictwem platformy Azure](./cloud-partner-portal-saas-offer-subscriptions.md).


### <a name="test-drive"></a>Wersja testowa

Tworzenie wersji próbnej środowisko dla swoich klientów jest najlepszym rozwiązaniem, aby upewnić się, że mogą kupić bez obaw. Z dostępnych opcji wersji próbnej wersji testowej jest najbardziej efektywne, wysokiej jakości generowania potencjalnych klientów i zwiększonej konwersji tych potencjalnych klientów.

Daje klientom z wersją próbną praktyczne, przeprowadzanemu samodzielnie procesowi kluczowe funkcje i korzyści, przedstawione w scenariuszu wdrożenia rzeczywistych swojego produktu.

![Oferta SaaS wersji testowej](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>Jak działa wersji testowej

Potencjalny klient wyszukuje i umożliwia odnalezienie aplikacji w witrynie Marketplace. Klient rejestruje i wyraża zgodę na warunki użytkowania. W tym momencie użytkownik otrzymał wstępnie skonfigurowanego środowiska do wypróbowania funkcji przez określoną liczbę godzin, gdy otrzymasz wysoko wykwalifikowanych potencjalnego klienta do monitowanie.

![Test oferta SaaS dysk 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Niezależnie od tego, jak złożona jest aplikacja wersji testowej firmy Microsoft pomoże Ci Ożyw produktu dla klienta. Istnieją trzy różne rodzaje wersji testowych, każdy na podstawie typu produktu, scenariusza i portalu marketplace, które znajdują się na.

-   **Usługa Azure Resource Manager** -Azure ARM testowej jest szablon wdrożenia, który zawiera wszystkie zasoby platformy Azure, wchodzące w skład rozwiązania tworzona przez wydawcę. Produkty, które Dopasuj ten typ wersji testowej są te, które korzystać tylko zasobów platformy Azure.
-   **Aplikacji logiki** -wersję testową aplikacji logiki jest szablon wdrożenia, który jest przeznaczony do uwzględniający wszystkie architektury złożonych rozwiązań. Wszystkie aplikacje Dynamics lub niestandardowych produktów należy używać tego rodzaju wersji testowej.
-   **Usługa Power BI** -dysku Test usługi Power BI składa się z osadzonych łącze do niestandardowej pulpitu nawigacyjnego. Produkt, który chce pokazują, że Interaktywne wizualizacje usługi Power BI należy używać tego rodzaju wersji testowej. Wszystko, czego potrzebujesz do przekazania jest osadzony adres URL do usługi Power BI.

Główne kroki publikowania, aby dodać test dysku są:

1.  Zdefiniuj Twojemu scenariuszowi wersji testowej
2.  Tworzenie i/lub zmodyfikowanie szablonu usługi Resource Manager
3.  Utwórz szczegółowe podręcznika użytkownika wersji testowej
4.  Ponownie opublikować ofertę

Aby uzyskać więcej informacji, zobacz [wersji testowej](./what-is-test-drive.md).


### <a name="storefront-details"></a>Szczegóły sklepu

Aplikacje SaaS muszą najpierw dwie sekcje Podsumowanie i opis, aby podać o aplikacji.

![StoreFront — szczegóły oferty SaaS](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

W poniższej tabeli przedstawiono oferty\'s Szczegóły sklepu:

| **Pola oferty**        | **Opis**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Podsumowanie oferty           | Podsumowanie Twoja oferta korzyści. Pojawi się na stronie wyszukiwania swojej oferty. Należy się z maksymalnie 100 znaków.   |
| Opis oferty       | Opis, który będzie wyświetlany na stronie szczegółów aplikacji. Maksymalna dozwolona wartość to znaki 1300 *Uwaga* akceptowane w tym polu zawartość HTML z tagami, takich jak & ltp\>, & lth1\>, & lth2\>, & ltli\>, itp., która pozwala na Oznaczanie zawartości jest znacznie więcej zawartości. Publikowanie portalu zespołu działa na dodanie funkcji do umożliwiających wyświetlenie podglądu ich szczegóły storefront się interakcyjnie zawartość więcej zawartości — w tym czasie możesz użyć dowolnej można użyć dowolnego online w czasie rzeczywistym narzędzi HTML, takich jak http://htmledit.squarefree.com się jak będzie wyglądać opis. |
| Branże              | Wybierz przedsiębiorstw, które oferty najlepiej jest wyrównany do. Jeśli aplikacja odnosi się do wielu branżach, możesz wybrać maksymalnie dwóch. |
| Sugerowane kategorie    | Wybierz kategorie, które oferty najlepiej jest wyrównany do. Możesz wybrać maksymalnie trzy kategorie.     |
| Wersja aplikacji     | Wprowadź numer wersji aplikacji                                                                |
| Słowa kluczowe (maks. 3) | Wprowadź maksymalnie trzech słów kluczowych, używanych przez klientów można znaleźć aplikacji w witrynie sieci Web storefront portalu Marketplace. |
|  |  |


### <a name="marketing-artifacts"></a>Marketing artefaktów

Marketing sekcji artefaktów umożliwia definiowanie marketingowych zasobów, takich jak logo, filmy wideo, zrzutów ekranu i dokumentów w witrynie Azure Marketplace:

![SaaS oferują marketingu artefaktów](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

W poniższej tabeli opisano pola Marketing:

| **Pola oferty** | **Opis**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Logo            | Jeśli jesteś **sprzedawać za pośrednictwem platformy Azure** aplikacji SaaS, należy podać wszystkie obrazy logo. Jeśli po prostu listę tylko 2 logo są wymagane. Wszystkie logo, które są przekazywane w portalu Cloud Partner, należy stosować poniższe wskazówki:     <br/> -Niskich numer podstawowy i pomocniczy kolory na logo. Projekt platformy Azure ma prostą paletę kolorów.     <br/> -Należy unikać czarne lub białe jako kolor tła logo. Kolory motywu w witrynie Azure Portal jest czarno biały. Zamiast tego należy użyć niektórych kolorów, która spowodowałaby wprowadzenie logo widocznym w witrynie Azure Portal. Zalecamy proste kolory podstawowe. Jeśli korzystają z przezroczystym tłem, upewnij się, że obrazy logo i tekst są czarny, biały lub niebieski.     <br/> -Nie używaj gradientu tła na logo.     <br/> -Należy unikać wprowadzania tekstu, nawet Twoja firma lub nazwa marki na logo. Wygląd i działanie logo powinno być "płaską" i unikać gradientów.    <br/> -Obraz logo nie powinien być rozciągnięty.                   |
| Filmy wideo           | Umożliwia dodawanie łączy filmów wideo w swojej oferty. Możesz użyć linków do serwisu YouTube lub Vimeo filmów wideo, które są wyświetlane wraz z ofertą dla klientów. Należy również wprowadzić obraz miniatury wideo z obrazem png 1280 x 720 pikseli. Może mieć maksymalnie cztery filmów wideo na ofertę. |
| Dokumenty        | Umożliwia dodawanie dokumentów marketingowych oferty. Wszystkie dokumenty muszą być w formacie PDF i może mieć co najwyżej trzech dokumentów na ofertę.                                                                                                                                                      |
| Zrzuty ekranu      | Umożliwia dodanie zrzutów ekranu oferty. Istnieje więcej niż pięć zrzuty ekranu, które mogą zostać dodane na ofertę. Rozmiar maksymalny obrazu to 1280 x 720 pikseli.                                                                                                                                             |
| Przydatne łącza     | Umożliwia dodanie zewnętrzne adresy URL dla oferty pomocy wskaż diagramów architektury lub innych witryn sieci Web klienta, czy mają być wyświetlane.                                                                                                                                                              |
|  |  |

Na poniższej ilustracji przedstawiono, jak informacje są wyświetlane w wynikach wyszukiwania witryny Marketplace:

![SaaS oferują marketingu informacje o wydawcy](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

Na poniższej ilustracji przedstawiono sposób wyświetlania oferty w portalu Marketplace po odbiorca kliknie mniejszych kafelka oferty:

![SaaS oferują marketingu info2 wydawcy](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Zarządzanie potencjalnych klientów i informacje prawne

Sekcja prawne umożliwia ustawienie Dokumentacja prawna oferty.
Istnieją dwa dokumenty prawne wymagane dla każdej oferty aplikacji SaaS: zasady ochrony prywatności i warunki użytkowania. Aby uzyskać więcej informacji, zobacz  
[Konfigurowanie potencjalnych klientów](./cloud-partner-portal-get-customer-leads.md).

![SaaS oferują marketingu informacje prawne](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

W poniższej tabeli opisano właściwości prawne sekcji:

| **Pola oferty**   | **Opis**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| Adres URL zasad ochrony prywatności | Adres URL zasad ochrony prywatności firmy użytkownika.                                                       |
| Warunki użytkowania       | Warunki użytkowania oferty. Wpisz lub skopiuj i Wklej warunki użytkowania, w tym miejscu. Podstawowe użycia HTML jest dozwolone z takim tego pola przyjmuje html zawartości za pomocą tagów, takich jak & ltp\>, & lth1\>, & lth2\>, & ltli\>itp. *Ważne* zdecydowanie zalecamy przejrzenie i HTML utworzona w przeglądarce przed przesłaniem oferty w wersji zapoznawczej. |
|  |  |


### <a name="contact-information"></a>Informacje kontaktowe

W tej sekcji wprowadź kontaktów pomocy technicznej Twojej firmy będą odpowiedzialni za obsługę klientów, korzystanie z tej oferty.
Istnieją trzy główne obszary: kontakt techniczny, skontaktuj się z pomocy technicznej i adresy URL pomocy technicznej:

![SaaS oferują marketingu informacje kontaktowe](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Skontaktuj się z**         | **Opis**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Kontakt techniczny | Podaj nazwę, adres e-mail i numer telefonu kontakt techniczny, dzięki której firmy Microsoft mogą uzyskać problemów pomocy technicznej i biznesowej. |
| Skontaktuj się z pomocy technicznej     | Podaj nazwę, adres e-mail, numer telefonu i adres URL, do której klienci mogą wówczas, gdy są żądania pomocy technicznej pomocy technicznej.                  |
|  |  |


Gdy Twoja oferta jest gotowa i trafień publikowania, oferty przechodzi przez certyfikacji. Przetestowanie aplikacji SaaS za pomocą ręcznej weryfikacji wokół testowania adres URL, jeśli masz listę lub wybrane punkty końcowe, jeśli masz sprzedawać za pośrednictwem platformy Azure. Podczas ręcznego zatwierdzania możemy również wybrać odpowiedniego sklepu, których aplikacji powinny być widoczne na (AppSource, Azure Marketplace lub obie).

<a name="updating-the-offer"></a>Aktualizowanie oferty
------------------

Istnieją różne rodzaje aktualizacje, które warto wykonać oferty po opublikowaniu i działa. Wszelkie zmiany wprowadzone do nowej wersji oferty należy zapisać i ponownie opublikować, aby odzwierciedlić w portalu Marketplace.

<a name="deleting-an-existing-offer"></a>Usuwanie istniejącej oferty
--------------------------

Użytkownik może zdecydować o usunięciu oferty z portalu Marketplace. Usunięcie oferty zapewnia, dzięki czemu nowi klienci mogą nie jest już zakup lub wdrożenie oferty, ale nie ma ono wpływu na istniejących klientów. Zakończenie oferty to proces zakończenia świadczenia usługi i/lub rozwiązania umowy licencjonowania między Tobą i Twoimi obecnymi klientami.

Wskazówki i zasady dotyczące usuwania i kończenia oferty podlegają umowie wydawcy portalu Microsoft Marketplace (zobacz sekcję 7) i zasad uczestnictwa (patrz sekcja 6.2). Ta sekcja zawiera informacje dotyczące scenariuszy różnych obsługiwanych delete oraz czynności, które można podjąć, ich.

### <a name="delete-the-live-offer"></a>Usuwanie oferty na żywo

Istnieją różne aspekty, które należy uwzględnić Jeśli wniosek o usunięcie oferty na żywo. Wykonaj poniższe kroki, aby uzyskać wskazówki z zespołem pomocy technicznej do usunięcia na żywo oferty z portalu Azure Marketplace:

1.  Zgłosić bilet pomocy technicznej za pomocą tego łącza
2.  Na liście Typ problemu wybierz **Zarządzanie ofertami**, a na liście kategorii wybierz pozycję **modyfikowanie oferty i/lub jednostek SKU już w środowisku produkcyjnym**.
3.  Prześlij żądanie

Zespół pomocy technicznej przeprowadzi Cię przez proces usuwania oferty.

> [!NOTE] 
> Usuwanie oferty nie wpływa na bieżących zakupów tę ofertę. Tych zakupów klientów będą w dalszym ciągu działać tak jak poprzednio.
Jednak oferty nie będą dostępne dla żadnych nowych zakupów po zakończeniu usuwania.
