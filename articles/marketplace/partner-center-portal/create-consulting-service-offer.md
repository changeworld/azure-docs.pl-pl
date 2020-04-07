---
title: Tworzenie oferty usług konsultingowych w Centrum partnerów — Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi konsultingowej w portalu Azure Marketplace lub AppSource przy użyciu Centrum partnerów.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ce8df36d3417417a5f70a5385aa94d9c8c7ff0cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674282"
---
# <a name="consulting-service-creation-overview"></a>Omówienie tworzenia usług konsultingowych

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami usług konsultingowych z Cloud Partner Portal do Centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [usłudze konsultingowej Platformy Azure i dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) dla portalu cloud partner portal, aby zarządzać ofertami.

W tym artykule opisano sposób publikowania oferty usługi konsultingowej w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) lub [AppSource.](https://appsource.microsoft.com/) Lista ofert usług konsultingowych opartych na microsoft [dynamics 365](https://dynamics.microsoft.com/) i power platform na AppSource. Lista ofert usług konsultingowych opartych na platformie Microsoft Azure w portalu Azure Marketplace.

## <a name="publishing-benefits"></a>Korzyści związane z publikowaniem

Korzyści z publikowania na rynku komercyjnym:

- Promuj swoją firmę, korzystając z marki Microsoft.
- Potencjalnie dotrzeć do ponad 100 milionów użytkowników usługi Office 365 i Dynamics 365 w usłudze AppSource i ponad 200 000 organizacji za pośrednictwem portalu Azure Marketplace.
- Otrzymuj wysokiej jakości potencjalnych klientów z tych rynków.
- Niech Twoje usługi będą promowane przez zespoły firmy Microsoft i telesprzedaży

## <a name="requirements"></a>Wymagania

### <a name="business-requirements"></a>Wymagania biznesowe

W przypadku ofert, w których platforma Azure jest wybierana jako produkt podstawowy, oferta musi zawierać co najmniej jedną z następujących w pełni zdobytych kompetencji:

- Projektowanie aplikacji
- Integracja aplikacji
- Zarządzanie cyklem życia aplikacji
- Platforma chmurowa
- Analiza danych
- Centrum danych
- Platforma danych
- DevOps

W przypadku ofert z jedną z następujących opcji wybranych jako produkt podstawowy, należy spełnić odpowiednie wymagania kwalifikacyjne wymienione lub mieć ofertę co-sell dla produktu podstawowego, z którą jest powiązana oferta usługi.

**Aplikacje zaangażowania klientów**

- **Dotyczy:** Dynamics 365 Sales, Dynamics 365 Marketing, Dynamics 365 Customer Service, Dynamics 365 Field Service, Dynamics 365 Human Resources

- **Kryteria:** Musi być certyfikat Gold lub Silver w [kompetencji Cloud Business Applications](https://partner.microsoft.com/membership/cloud-business-applications-competency) dla customer engagement opcji.

**Aplikacje finansowe i operacyjne**

- **Dotyczy:** Dynamics 365 Finance, Dynamics 365 Operations, Dynamics 365 Commerce, Dynamics 365 Human Resources, Dynamics 365 Project Service Automation

- **Kryteria:** Musi być certyfikat Gold lub Silver w [konkurencyjności Cloud Business Applications](https://partner.microsoft.com/membership/cloud-business-applications-competency) dla ujednoliconej operacji.

**Dynamics 365 Customer Insights**

- **Kryteria:** Musi mieć co najmniej jedną pomyślną implementację w produkcji [Dynamics 365 Customer Insights](https://dynamics.microsoft.com/ai/customer-insights/) z co najmniej pięcioma miarami i pięcioma segmentami.

**Dynamics 365 Business Central**

- **Kryteria:** Musi być certyfikowany jako Złoty lub Srebrny w [kompetencji Planowania zasobów przedsiębiorstwa](https://partner.microsoft.com/membership/enterprise-resource-planning-competency) i obsługiwać co najmniej trzech klientów lub opublikować aplikację Business Central w usłudze Microsoft AppSource.

**Power BI**

- **Kryteria:** Musi być wymieniony w [prezentacji partnera usługi Power BI](https://powerbi.microsoft.com/partner-showcase/).

**Power Apps**

- **Kryteria:** Muszą kwalifikować się do uzyskania korzyści zaawansowaną w programie [Power Apps Partnership.](https://aka.ms/PowerAppsPartner)

Aby uzyskać szczegółowe informacje na temat spełniania tych wymagań wstępnych, zobacz [wymagania wstępne usługi konsultingowej](consulting-service-prerequisites.md).

### <a name="logistical-requirements"></a>Wymagania logistyczne

Aby utworzyć ofertę usług konsultingowych w usługach konsultingowych azure marketplace lub AppSource, musisz [najpierw mieć konto wydawcy w Centrum partnerów,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)a twoje konto musi być zarejestrowane w komercyjnym programie marketplace.

## <a name="create-a-new-consulting-service-offer-in-partner-center"></a>Tworzenie nowej oferty usług konsultingowych w Centrum partnerskim

Po spełnienie wymagań opisanych powyżej, wykonaj następujące kroki, aby utworzyć ofertę usług konsultingowych.

1. Zaloguj się do [Centrum partnerów,](https://partner.microsoft.com)a następnie wybierz **pozycję Pulpit nawigacyjny** z górnego menu.
2. Na pasku po lewej stronie nawigacyjnym wybierz pozycję **Rynek komercyjny**, a następnie wybierz pozycję **Przegląd**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Ilustruje menu dla rynku komercyjnego":::

3. Wybierz **+ Nowa oferta**, a następnie wybierz opcję Usługa **konsultingowa**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Ilustruje przycisk, aby utworzyć nową ofertę.":::

4. Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

    - Ten identyfikator jest widoczny dla klientów w adresie internetowym oferty marketplace.
    - Używaj tylko małych liter, cyfr, kresek i podkreśleń, ale bez spacji. Długość jest ograniczona do 50 znaków. Na przykład, jeśli wprowadzisz **test-offer-1**, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres URL oferty będzie .
    - Identyfikator oferty może&#39;nie zostać zmieniony po wybraniu opcji **Utwórz**.

5. Wprowadź **alias oferty**. Jest to nazwa używana do odwoływania się do oferty w Centrum partnerów.

    - Ta nazwa jest&#39;t używana w portalu marketplace. Różni&#39;od nazwy oferty i innych wartości, które są wyświetlane klientom. To pole służy do przypisywania nazwy do oferty, która jest bardziej przydatna do wewnętrznej identyfikacji oferty; nie jest wyświetlany klientom.
    - Alias oferty może&#39;nie zostać zmieniony po wybraniu opcji **Utwórz**.

Po wprowadzeniu tych dwóch wartości wybierz pozycję **Utwórz,** aby przejść do strony **Ustawienia oferty.**

## <a name="offer-setup"></a>Konfiguracja oferty

Po wprowadzeniu aliasu Identyfikator oferty i Oferta Centrum partnerów tworzy ofertę roboczą i wyświetla stronę **Ustawienia oferty.** Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="connect-lead-management"></a>Łączenie zarządzania potencjalnymi klientami

Publikując ofertę na rynku za pomocą Centrum partnerów, _należy_ połączyć ją z systemem zarządzania relacjami z klientami (CRM) lub automatyzacją marketingu. Dzięki temu możesz otrzymywać informacje kontaktowe klienta, gdy tylko ktoś wyrazi zainteresowanie produktem lub z niego korzysta.

1. Wybierz **połącz,** aby określić, gdzie chcesz, abyśmy wysyłali potencjalnych klientów. Centrum partnerskie obsługuje następujące systemy:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Jeśli system CRM jest&#39;t wymienione powyżej, użyj [tabeli Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów, a następnie wyeksportować dane do systemu CRM.

2. Połącz ofertę z głównym miejscem docelowym podczas publikowania w Centrum partnerów.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest poprawnie skonfigurowane. Po opublikowaniu go w Centrum partnerów,&#39;potwierdzić połączenie i wysłać potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej wyświetleniem można również przetestować połączenie potencjalnego klienta, próbując samodzielnie kupić ofertę w środowisku podglądu.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta pozostaje zaktualizowane, aby nie&#39;utracić potencjalnych klientów, aby nie utracić żadnych potencjalnych klientów.

Oto kilka dodatkowych zasobów zarządzania potencjalnymi klientami:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że bloker wyskakujących wyskakujących jest wyłączony)

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Właściwości.

### <a name="properties"></a>Właściwości

Ta strona umożliwia ustawienie produktu podstawowego, który usługa konsultingowa oferuje najlepsze okładki, ustawienie typu usługi konsultingowej i wybranie odpowiednich produktów.

1. Wybierz **produkt podstawowy** z listy rozwijanej.
2. Wybierz **typ usługi konsultingowej** z listy rozwijanej:

    - **Ocena** : Ocena środowiska&#39;klienta w celu określenia zastosowania rozwiązania i zapewnienia szacunkowych kosztów i terminów.
    - **Briefing:** Wprowadzenie do rozwiązania lub usługi konsultingowej, aby przyciągnąć zainteresowanie klientów przy użyciu struktur, wersji demonstracyjnych i przykładów klientów.
    - **Wykonanie** : Kompletna instalacja, która powoduje w pełni działające rozwiązanie. Limit do rozwiązań, które mogą być realizowane w ciągu dwóch tygodni lub mniej.
    - **Dowód koncepcji:** Implementacja o ograniczonym zakresie w celu ustalenia, czy rozwiązanie spełnia wymagania klienta.
    - **Warsztaty** : Interaktywne zaangażowanie prowadzone na terenie klienta&#39;s. Może obejmować szkolenia, briefingi, oceny lub pokazy oparte na danych klienta&#39;s lub środowisku.

1. Jeśli wybrano podstawowy produkt **platformy Azure,** wybierz maksymalnie trzy **obszary rozwiązania**. Ułatwiają one klientom w portalu Azure Marketplace znalezienie oferty. Jeśli nie&#39;wybierzesz platformy Azure, pomiń ten krok.
2. Jeśli wybrano produkt podstawowy _inny_ niż Azure, wybierz maksymalnie trzy **produkty odpowiednie**. Ułatwiają one klientom w uźródle appsource znalezienie oferty. Aby uzyskać szczegółowe informacje, zobacz [Wskazówki dotyczące wyświetlania ofert usług konsultingowych firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Wybierz maksymalnie sześć **branż,** których dotyczy twoja oferta. Ułatwi to klientom znalezienie oferty.
4. Dodaj do trzech **kompetencji,** które twoja firma zdobyła, aby wyświetlić je na liście ofert usług konsultingowych. Wymagana jest co najmniej jedna kompetencja z wyjątkiem usług Azure Expert MSP&#39;s i usługi Azure Networking MSP&#39;s.

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Zaoferuj aukcję.

## <a name="offer-listing"></a>Oferta aukcji

W tym miejscu&#39;zdefiniujesz szczegóły oferty wyświetlane w portalu Marketplace. Obejmuje to nazwę oferty, opis, obrazy i tak dalej. Podczas konfigurowania tej oferty należy przestrzegać zasad opisanych na [stronie zasad firmy Microsoft&#39;s.](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)

> [!NOTE]
> Szczegóły oferty są&#39;t muszą być w języku angielskim, jeśli &quot;opis oferty zaczyna się od wyrażenia, Ta aplikacja jest dostępna tylko w [w języku nieanglojęzycznym]. &quot;&#39;również w porządku, aby udostępnić przydatny link do oferowania treści w języku, który&#39;różni się od tego, który jest używany w szczegółach aukcji oferty.

### <a name="name"></a>Nazwa

Nazwa, którą tu wprowadzisz, jest wyświetlana jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Może być znakiem towarowym (i możesz zawierać znaki towarowe lub symbole praw autorskich).
- Może&#39;t mieć więcej niż 50 znaków.
- Może&#39;t zawierać emotikony.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Podaj krótki opis swojej oferty. Może to mieć do 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="description"></a>Opis

Podaj dłuższy opis swojej oferty, do 3000 znaków. Jest to wyświetlane klientom w przeglądzie aukcji w portalu marketplace.

W opisie należy uwzględnić co najmniej jedną z następujących opcji:

- Wartość i kluczowe korzyści, jakie zapewnia Twoja oferta
- Kategorie lub stowarzyszenia branżowe, lub oba
- Możliwości zakupu w aplikacji
- Wszelkie wymagane informacje

Oto kilka wskazówek dotyczących pisania opisu:

- Wyraźnie opisz wartość swojej oferty w pierwszych kilku zdaniach opisu. Dołącz następujące elementy:
  - Opis oferty.
  - Typ użytkownika, który korzysta z oferty.
  - Klient potrzebuje lub wydaje adresy ofert.
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.
- Nie&#39;polegać na funkcjach i funkcjach, aby sprzedawać swój produkt. Zamiast tego skup się na wartości, która zapewnia twoja oferta.
- Spróbuj użyć słownictwa branżowego lub sformułowania opartego na korzyściach.

Aby opis był bardziej atrakcyjny, użyj edytora tekstu sformatowania opisu. Edytor tekstu sformatowego umożliwia dodawanie liczb, punktorów, pogrubienia, kursywy i wcjęcia, aby opis był bardziej czytelny.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ilustruje edytor tekstu sformatowego, aby napisać opis oferty." border="false":::

### <a name="keywords"></a>Słowa kluczowe

Wprowadź maksymalnie trzy słowa kluczowe wyszukiwania, które są istotne dla podstawowego produktu i usługi konsultingowej. Ułatwi to znalezienie oferty.

### <a name="duration"></a>Czas trwania

Ustaw oczekiwany czas trwania tego zaangażowania z klientem.

### <a name="contact-information"></a>Informacje kontaktowe

Należy podać nazwę, adres e-mail i numer telefonu kontaktu **podstawowego** i **pomocniczego.** Te informacje są&#39;wyświetlane klientom. Jest on dostępny dla firmy Microsoft i może być dostarczany partnerom dostawcy rozwiązań w chmurze (CSP).

### <a name="supporting-documents"></a>Dokumenty uzupełniające

Dodaj do trzech (ale co najmniej jeden) dokumentów obsługujących plik PDF dla swojej oferty.

### <a name="marketplace-images"></a>Obrazy z Marketplace

Podaj logo i obrazy do wykorzystania w swojej ofercie. Wszystkie obrazy muszą być w formacie png. Rozmazane obrazy zostaną odrzucone.

#### <a name="store-logos"></a>Logo sklepu

Podaj pliki .png swojej oferty&#39;logo s w każdym z następujących rozmiarów pikseli:

- **Mały (48 x 48)**
- **Duży (216 x 216)**

Wszystkie logo są wymagane i są używane w różnych miejscach na liście marketplace.

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj do pięciu zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy musi mieć rozmiar 1280 x 720 pikseli i w formacie png.

#### <a name="videos-optional"></a>Filmy (opcjonalnie)

Dodaj do czterech filmów, które pokazują twoją ofertę. Wprowadź nazwę&#39;wideo, jego adres internetowy (URL) i miniaturę obrazu .png wideo o wymiarach 1280 x 720 pikseli.

Wybierz **pozycję Zapisz pochyłą pozycję przed** przejściem do następnej sekcji Cennik i dostępność.

## <a name="pricing-and-availability"></a>Cennik i dostępność

W tym miejscu zdefiniujesz elementy, takie jak ceny, rynek i klucz prywatny.

1. **Rynek**: Ustaw rynek, na który będzie dostępna Twoja oferta. Możesz wybrać tylko jeden rynek na ofertę.
    1. W przypadku rynków w Stanach Zjednoczonych lub Kanadzie wybierz **pozycję Edytuj stany** (lub **prowincje),** aby określić, gdzie twoja oferta będzie dostępna.
2. **Podgląd odbiorców:** Skonfiguruj **klucz ukrywania** używany do ustawiania prywatnej grupy odbiorców dla oferty.
3. **Ceny:** Określ, czy Twoja oferta jest ofertą **bezpłatną,** czy **płatną.**

    > [!NOTE]
    > Oferty usługi konsultingowej są tylko dla aukcji. Wszelkie transakcje będą miały miejsce bezpośrednio, poza rynkiem komercyjnym.

4. W przypadku oferty płatnej określ **cenę i walutę** oraz to, czy cena jest **stała** czy **szacowana**. Jeśli szacowane, należy określić w opisie, jakie czynniki będą miały wpływ na cenę.
5. Wybierz **pozycję Zapisz pochówek**.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po wypełnić wszystkie wymagane sekcje oferty, można przesłać ofertę do przeglądu i opublikowania.

1. Gdy będziesz gotowy do opublikowania oferty usług konsultingowych, kliknij przycisk **Przejrzyj i opublikuj**.
2. Przejrzyj szczegóły na stronie ostatecznego przesyłania.
3. Jeśli to konieczne, napisz notatkę do zespołu certyfikacyjnego, jeśli uważasz, że którykolwiek ze szczegółów oferty wymaga wyjaśnienia.
4. Gdy będziesz gotowy, wybierz pozycję **Prześlij**.
5. Strona **Przegląd oferty** pokazuje, na jakim etapie publikowania znajduje się twoja oferta.

Aby uzyskać więcej informacji o tym, jak długo można oczekiwać, że oferta będzie znajdować się na każdym etapie publikowania, zobacz [Sprawdzanie stanu publikowania oferty w portalu Commercial Marketplace.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)

## <a name="update-your-existing-consulting-service-offers"></a>Aktualizowanie istniejących ofert usług konsultingowych

- [Aktualizowanie istniejącej oferty na rynku komercyjnym](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
