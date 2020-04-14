---
title: Tworzenie oferty kontenerów platformy Azure w Centrum partnerów — azure marketplace
description: W tym artykule opisano sposób tworzenia i publikowania oferty kontenera dla portalu Azure Marketplace.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266320"
---
# <a name="create-an-azure-container-offer"></a>Tworzenie oferty kontenera platformy Azure

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami usługi Azure Container z portalu cloud partnerów do centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [witrynie Containers](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) for Cloud Partner Portal, aby zarządzać ofertami.

W tym artykule opisano sposób tworzenia i publikowania oferty kontenera dla portalu Azure Marketplace. Przed rozpoczęciem [tworzenia konta w portalu Marketplace komercyjnego](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Tworzenie nowej oferty

1. Zaloguj się do [Centrum partnerów,](https://partner.microsoft.com/dashboard/home)a następnie z górnego menu wybierz pozycję **Pulpit nawigacyjny**.
2. W menu po lewej stronie wybierz pozycję **Rynek komercyjny**, a następnie **pozycję Przegląd**.
3. Na stronie **Przegląd** wybierz **pozycję + Nowa oferta**, a następnie usługę Azure **Container**. Zostanie wyświetlone okno dialogowe **Nowa oferta.**

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="Zilustrowano stronę Przegląd w Centrum partnerów. Przycisk Nowa oferta i oferta usług konsultingowych są wyróżnione.":::

> [!TIP]
> Po opublikowaniu oferty zmiany wprowadzone w Centrum partnerskim pojawiają się tylko w witrynach sklepowych po ponownym opublikowaniu oferty. Upewnij się, że zawsze ponownie opublikować po dokonaniu zmian.

### <a name="offer-id-and-alias"></a>Identyfikator oferty i alias

Wprowadź identyfikator **oferty**. Jest to unikatowy identyfikator dla każdej oferty na Twoim koncie.

- Ten identyfikator może być widoczny dla klientów w adresie internetowym oferty portalu Marketplace i szablonach usługi Azure Resource Manager, jeśli ma to zastosowanie.
- Użyj tylko małych liter i cyfr. Może zawierać łączniki i podkreślenia, ale bez spacji i jest ograniczona do 50 znaków. Na przykład, jeśli wprowadzisz **test-offer-1**, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adres internetowy oferty będzie .
- Nie można zmienić identyfikatora oferty po wybraniu opcji **Utwórz**.

**Wprowadź alias** **oferty**. Jest to nazwa używana do odwoływania się do oferty w Centrum partnerów.

- Ta nazwa nie jest używana w portalu Marketplace i różni się od nazwy oferty i innych wartości wyświetlanych klientom.
- Nie można tego zmienić po wybraniu opcji **Utwórz**.

Wybierz **pozycję Utwórz** przed kontynuowaniem.

## <a name="offer-overview"></a>Przegląd oferty

Na stronie **Przegląd oferty** przedstawiono wizualną reprezentację kroków wymaganych do opublikowania tej oferty (zarówno ukończonych, jak i nadchodzących) oraz czasu, jaki powinien potrwać każdy krok.

Na tej stronie znajdują się różne linki na podstawie bieżącego stanu oferty. Przykład:

- Jeśli oferta jest wersja robocza — [usuwanie wersji roboczej oferty](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Jeśli oferta jest na żywo - [Przestań sprzedawać ofertę](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Jeśli oferta jest w wersji zapoznawczej - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Jeśli nie zostało zakończone wylogowanie wydawcy — [anulowanie publikowania](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Konfiguracja oferty

Wykonaj następujące kroki, aby skonfigurować ofertę.

### <a name="connect-lead-management--optional"></a>Zarządzanie potencjalnymi klientami – opcjonalnie

Publikując ofertę na rynku za pomocą Centrum partnerów, można połączyć ją z systemem crm (Customer Relationship Management). Dzięki temu możesz otrzymywać informacje kontaktowe klienta, gdy tylko ktoś wyrazi zainteresowanie produktem lub z niego korzysta.

1. **Wybierz miejsce docelowe potencjalnego klienta, w którym chcesz, abyśmy wysyłali potencjalnych klientów.** Centrum partnerskie obsługuje następujące systemy CRM:

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) dla zaangażowania klienta
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [SalesForce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Jeśli systemu CRM nie ma na liście powyżej, użyj [tabeli Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) lub [punktu końcowego https](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) do przechowywania danych potencjalnych klientów, a następnie wyeksportuj dane do systemu CRM.

2. Połącz ofertę z głównym miejscem docelowym podczas publikowania w Centrum partnerów.
3. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest poprawnie skonfigurowane. Po opublikowaniu go w Centrum partnerów, firma Microsoft sprawdź poprawność połączenia i wysłać potencjalnego klienta testowego. Podczas wyświetlania podglądu oferty przed jej wyświetleniem można również przetestować połączenie potencjalnego klienta, próbując samodzielnie kupić ofertę w środowisku podglądu.
4. Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta pozostaje zaktualizowane, aby nie utracić potencjalnych klientów.

Oto kilka dodatkowych zasobów zarządzania potencjalnymi klientami:

- [Omówienie zarządzania potencjalnymi klientami](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Zarządzanie potencjalnymi klientami — często zadawane pytania](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Typowe błędy konfiguracji potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Omówienie zarządzania potencjalnymi klientami](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Upewnij się, że bloker wyskakujących wyskakujących jest wyłączony)

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Właściwości.

### <a name="properties"></a>Właściwości

Ta strona umożliwia zdefiniowanie kategorii używanych do grupowanie oferty na rynku oraz umów prawnych, które obsługują Twoją ofertę.

#### <a name="category"></a>Kategoria

Wybierz co najmniej jedną i maksymalnie pięć kategorii. Te kategorie są używane do umieszczania oferty w odpowiednich obszarach wyszukiwania w portalu marketplace i są wyświetlane na stronie szczegółów oferty. W opisie oferty wyjaśnij, w jaki sposób Twoja oferta obsługuje te kategorie. Kontenery są wyświetlane w obszarze **kontenery,** a następnie **kategorii Obrazy kontenerów.**

#### <a name="legal"></a>Informacje prawne

Musisz podać warunki oferty. Dostępne są dwie opcje:

- Użyj kontraktu standardowego dla komercyjnego rynku firmy Microsoft.
- Podaj własne warunki.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardowa umowa dla komercyjnego rynku firmy Microsoft

Oferujemy szablon kontraktu standardowego, aby ułatwić transakcje na rynku komercyjnym. Możesz zaoferować swoje rozwiązanie w ramach umowy standardowej, którą klienci muszą tylko raz sprawdzić i zaakceptować. Jest to dobra opcja, jeśli nie chcesz tworzyć niestandardowych warunków.

Aby dowiedzieć się więcej o umowie standardowej, zobacz [Umowa standardowa dla komercyjnego rynku firmy Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Możesz również pobrać [standardowy plik](https://go.microsoft.com/fwlink/?linkid=2041178) PDF kontraktu (upewnij się, że bloker wyskakujących wyskakujących wyskakujących jest wyłączony).

Aby użyć kontraktu standardowego, zaznacz pole wyboru **Użyj kontraktu standardowego dla komercyjnego portalu handlowego firmy Microsoft,** a następnie kliknij przycisk **Zaakceptuj**.

> [!NOTE]
> Po opublikowaniu oferty przy użyciu umowy standardowej dla komercyjnego portalu Microsoft nie można używać własnych niestandardowych warunków. Zaoferuj swoje rozwiązanie w ramach umowy standardowej lub na własnych warunkach.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Z ilustruje pole wyboru Korzystanie z umowy standardowej dla komercyjnego portalu handlowego firmy Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Twoje własne warunki

Aby podać własne warunki niestandardowe, wprowadź je w polu **Warunki.** W tym polu można wprowadzić nieograniczoną ilość znaków tekstu. Klienci muszą zaakceptować te warunki, zanim będą mogli wypróbować Twoją ofertę.

Wybierz **pozycję Zapisz pochyłość** przed przejściem do następnej sekcji Zaoferuj aukcję.

## <a name="offer-listing"></a>Oferta aukcji

Ta strona umożliwia zdefiniowanie szczegółów oferty wyświetlanych w rynku komercyjnym. Obejmuje to nazwę oferty, opis i obrazy.

> [!NOTE]
> Szczegóły oferty nie muszą być w języku angielskim, jeśli opis oferty zaczyna się od wyrażenia"Ta aplikacja jest dostępna tylko w języku [nieanglojęzycznym]." Jest również w porządku, aby udostępnić przydatny link do oferowania treści w języku innym niż ten używany w szczegółach aukcji oferty.

### <a name="name"></a>Nazwa

Nazwa, którą tu wprowadzona zostanie, jest wyświetlana jako tytuł oferty. To pole jest wstępnie wypełnione tekstem wprowadzonym w polu **Alias oferty** podczas tworzenia oferty. Tę nazwę można później zmienić.

Nazwa:

- Mogą być znakami towarowymi (i możesz zawierać znaki towarowe i symbole praw autorskich).
- Nie może mieć więcej niż 50 znaków.
- Nie można dołączać emotikonów.

### <a name="search-results-summary"></a>Podsumowanie wyników wyszukiwania

Krótki opis twojej oferty. Może to mieć do 100 znaków i jest używana w wynikach wyszukiwania w portalu Marketplace.

### <a name="long-summary"></a>Długie podsumowanie

Bardziej szczegółowy opis oferty. Może to mieć do 256 znaków i jest używana w wynikach wyszukiwania w marketplace.

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
  - Typ użytkownika, który korzysta z oferty
  - Klient potrzebuje lub wydaje adresy ofert.
- Pamiętaj, że kilka pierwszych zdań może być wyświetlanych w wynikach wyszukiwania.
- Nie polegaj na funkcjach i funkcjach sprzedaży produktu. Zamiast tego skup się na wartości, która zapewnia twoja oferta.
- Spróbuj użyć słownictwa branżowego lub sformułowania opartego na korzyściach.

Aby **opis** oferty był bardziej interesujący, użyj edytora tekstu sformatowego, aby sformatować opis. z numerowania, punktory, pogrubienie, kursywą i wcięcia, aby opis bardziej czytelny.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustruje edytor tekstu sformatacyjnego." border="false" :::

- Ta rozwijana służy do stosowania stylu akapitu do tekstu.

    :::image type="content" source="media/text-editor3.png" alt-text="Ilustruje kontrolę stylu tekstu w edytorze tekstu sformatacyjnego." border="false":::

- Użyj tych ikon, aby zastosować numerowanie lub punktory do tekstu.

     :::image type="content" source="media/text-editor4.png" alt-text="Ilustruje kontrolki listy punktowanej i numerowanej w edytorze tekstu sformatowanego." border="false":::

- Użyj tych ikon, aby dodać lub usunąć wcięcie do lub z tekstu.

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustruje formanty wcięci w edytorze tekstu sformatacyjnego." border="false":::

#### <a name="privacy-policy-link"></a>Link do polityki prywatności

Wprowadź adres internetowy zasad ochrony prywatności organizacji. Użytkownik jest odpowiedzialny za zapewnienie, że Twoja oferta jest zgodna z przepisami i regulacjami dotyczącymi prywatności. Jesteś również odpowiedzialny za opublikowanie ważnej polityki prywatności na swojej stronie internetowej.

#### <a name="useful-links"></a>Przydatne łącza

Podaj dodatkowe dokumenty online dotyczące swojej oferty. Możesz dodać maksymalnie 25 linków. Aby dodać łącze, wybierz **+ Dodaj łącze,** a następnie wypełnij następujące pola:

- **Tytuł** — klienci zobaczą to na stronie szczegółów oferty.
- **Link (URL)** — wprowadź link dla klientów, aby wyświetlić dokument online. Łącze musi zaczynać się od http:// lub https://.

### <a name="contact-information"></a>Informacje kontaktowe

Należy podać nazwę, adres e-mail i numer telefonu dla **kontaktu pomocy technicznej** i kontaktu **inżyniera.** Te informacje nie są wyświetlane klientom, ale są dostępne dla firmy Microsoft. Może być również dostarczony do partnerów dostawcy rozwiązań w chmurze (CSP).

- Kontakt pomocy technicznej (wymagany): w przypadku ogólnych pytań dotyczących pomocy technicznej.
- Kontakt techniczny (wymagany): w przypadku pytań technicznych i zagadnień certyfikacyjnych.
- Kontakt z programem CSP (opcjonalnie): W przypadku pytań odsprzedawców związanych z programem CSP.

W sekcji **Kontakt pomocy technicznej,** podaj **w sieci Web pomocy technicznej,** gdzie partnerzy mogą znaleźć pomoc techniczną dla twojej oferty na podstawie tego, czy oferta jest dostępna w globalnej platformie Azure, usłudze Azure dla instytucji rządowych lub obu.

W sekcji **Kontakt programu CSP** podaj link **(Materiały marketingowe programu CSP),** w którym partnerzy dostawcy CSP mogą znaleźć materiały marketingowe dla Twojej oferty.

#### <a name="additional-marketplace-listing-resources"></a>Dodatkowe zasoby dotyczące aukcji w portalu marketplace

Aby dowiedzieć się więcej o tworzeniu ofert, zobacz [Najlepsze wskazówki dotyczące oferty](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Obrazy z Marketplace

Podaj logo i obrazy do wykorzystania w swojej ofercie. Wszystkie obrazy muszą być w formacie PNG. Rozmazane obrazy zostaną odrzucone.

#### <a name="store-logos"></a>Logo sklepu

 Podaj pliki PNG z logo oferty w każdym z następujących rozmiarów czterech pikseli:

- **Mały** (48 x 48)
- **Średni** (90 x 90)
- **Duży** (216 x 216)
- **Szeroki** (255 X 115)

Wszystkie cztery logo są wymagane i są używane w różnych miejscach na liście marketplace.

#### <a name="screenshots-optional"></a>Zrzuty ekranu (opcjonalnie)

Dodaj do pięciu zrzutów ekranu, które pokazują, jak działa Twoja oferta. Każdy musi mieć rozmiar 1280 x 720 pikseli i w formacie PNG.

#### <a name="videos-optional"></a>Filmy (opcjonalnie)

Dodaj do pięciu filmów, które pokazują twoją ofertę. Wprowadź nazwę filmu, jego adres internetowy i miniaturowy obraz PNG wideo o wymiarach 1280 x 720 pikseli.

#### <a name="offer-examples"></a>Przykłady dotyczące ofert

Poniższe przykłady pokazują, jak pola aukcji oferty są wyświetlane w różnych miejscach oferty.

Spowoduje to **wyświetlenie** strony lista ofert w portalu Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustruje stronę z ofertą w portalu Azure Marketplace." :::

Spowoduje to wyświetlenie wyników wyszukiwania w portalu Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustruje wyniki wyszukiwania w portalu Azure Marketplace.":::

Spowoduje to **wyświetlenie** strony lista ofert w witrynie Azure portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustruje stronę lista ofert w witrynie Azure portal.":::

Spowoduje to wyświetlenie wyników wyszukiwania w witrynie Azure portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustruje wyniki wyszukiwania w witrynie Azure portal.":::

## <a name="preview"></a>Wersja zapoznawcza

Na karcie Podgląd możesz wybrać ograniczoną **grupę odbiorców w wersji zapoznawczej,** aby zweryfikować ofertę przed opublikowaniem jej na żywo.

> [!IMPORTANT]
> Po wyświetleniu oferty w **wersji zapoznawczej**należy wybrać **opcję Przejdź na żywo,** aby opublikować ofertę publicznie.

Określ odbiorców w wersji zapoznawczej przy użyciu identyfikatorów GUID subskrypcji platformy Azure wraz z opcjonalnym opisem dla każdego z nich. Żadne z tych pól nie może być postrzegane przez klientów.

> [!NOTE]
> Identyfikator subskrypcji platformy Azure można znaleźć na stronie Subskrypcje w witrynie Azure portal.

Dodaj co najmniej jeden identyfikator subskrypcji platformy Azure, indywidualnie (do 10) lub przekazując plik CSV (do 100). Dodając te identyfikatory subskrypcji, można określić, kto może wyświetlić podgląd oferty, zanim zostanie opublikowana na żywo. Jeśli twoja oferta jest już dostępna, możesz wybrać grupę odbiorców w wersji zapoznawczej, aby przetestować zmiany lub aktualizacje oferty.

> [!NOTE]
> Grupa odbiorców podglądu różni się od grupy prywatnej. Grupa odbiorców **w wersji zapoznawczej** może zobaczyć i potwierdzić wszystkie plany oferty, zanim będą dostępne na rynku, w tym plany, które zostaną opublikowane tylko **dla odbiorców prywatnych** (ustawione na karcie Dostępność).

Wybierz **pozycję Zapisz pochyłość** przed kontynuowaniem.

### <a name="plan-overview"></a>Omówienie planu

Ta karta umożliwia podanie różnych opcji planu w ramach tej samej oferty. Plany te były wcześniej określane jako jednostki SKU lub jednostki przechowujące zapasy. Plany mogą się różnić pod względem tego, jakie chmury są dostępne, takie jak chmury globalne, chmury rządowe i obraz, do którego odwołuje się plan. Aby wyświetlić ofertę na rynku komercyjnym, należy skonfigurować co najmniej jeden plan.

Po utworzeniu planów na karcie **Przegląd planu** jest wyświetlany:

- Nazwy planów
- Model cen
- Dostępność chmury (globalna lub rządowa)
- Bieżący stan publikowania
- Wszelkie dostępne działania

Działania dostępne w przeglądzie planu różnią się w zależności od bieżącego stanu planu. Obejmują one następujące raporty:

- **Usuń pochówek** — jeśli stan planu to Wersja robocza.
- **Zatrzymaj plan sprzedaży** — jeśli stan planu jest publikowany na żywo.

#### <a name="create-new-plan"></a>Tworzenie nowego planu

Wybierz **pozycję Utwórz nowy plan**. Zostanie wyświetlone okno dialogowe **Nowy plan.**

W polu **Identyfikator planu** utwórz unikatowy identyfikator planu dla każdego planu w tej ofercie. Ten identyfikator będzie widoczny dla klientów w adresie internetowym produktu. Używaj tylko małych liter i cyfr, kresek lub podkreśleń oraz maksymalnie 50 znaków.

> [!NOTE]
> Identyfikator planu nie można zmienić po wybraniu opcji **Utwórz**.

W polu **Nazwa planu** wprowadź nazwę tego planu. Klienci widzą tę nazwę przy podejmowaniu decyzji, który plan wybrać w ramach oferty. Utwórz unikatową nazwę dla każdego planu w tej ofercie. Na przykład można użyć nazwy oferty **systemu Windows Server** z planami Windows Server **2016** i **Windows Server 2019**.

### <a name="plan-setup"></a>Konfiguracja planu

Ta karta umożliwia wybranie chmur, w których plan jest dostępny. Odpowiedzi na tej karcie mają wpływ na to, które pola są wyświetlane na innych kartach.

#### <a name="cloud-availability"></a>Dostępność chmury

Plan musi być dostępny w co najmniej jednej chmurze.

Wybierz opcję **Azure Global,** aby twój plan mógł być używany przez klientów we wszystkich globalnych regionach platformy Azure korzystających z komercyjnego portalu marketplace. Aby uzyskać szczegółowe informacje, zobacz [Dostępność geograficzna i obsługa walut](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Wybierz opcję [**Azure Government Cloud,**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) aby twoje rozwiązanie pojawiło się tutaj. Jest to chmura społeczności rządowej z kontrolowanym dostępem dla klientów z amerykańskich federalnych, stanowych i lokalnych lub plemiennych agencji rządowych, a także partnerów uprawnionych do ich obsługi. Jako wydawca jesteś odpowiedzialny za wszelkie kontrole zgodności, środki zabezpieczeń i najlepsze rozwiązania dla tej społeczności w chmurze. Platforma Azure Government używa fizycznie odizolowanych centrów danych i sieci (zlokalizowanych tylko w Stanach Zjednoczonych).

Przed [opublikowaniem](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) w usłudze Azure Government należy przetestować i potwierdzić rozwiązanie w tym obszarze, ponieważ wyniki mogą być różne. Aby utworzyć i przetestować rozwiązanie, poproś o konto próbne z [wersji próbnej platformy Microsoft Azure dla instytucji rządowych.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Po opublikowaniu planu i udostępnieniu go w określonej chmurze nie można usunąć tej chmury.

#### <a name="azure-government-cloud-certifications"></a>Certyfikaty azure dla instytucji rządowych w chmurze

Ta opcja jest widoczna tylko wtedy, gdy w obszarze **Dostępność chmury wybrano**usługę **Azure Government Cloud** .

Usługi azure dla instytucji rządowych obsługują dane podlegające określonym przepisom i wymaganiom rządowym. Na przykład FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 i CJIS.

Aby wyświetlić certyfikaty dla tych programów, można podać maksymalnie 100 linków, które je opisują. Mogą to być linki do twoich aukcji w programie bezpośrednio lub do własnej strony internetowej. Te łącza są widoczne tylko dla klientów platformy Azure dla instytucji rządowych.

## <a name="plan-listing"></a>Lista planu

Na tej karcie są wyświetlane określone informacje dotyczące każdego innego planu w ramach bieżącej oferty.

### <a name="plan-name"></a>Nazwa planu

Jest to wstępnie wypełnione nazwą, którą nadano planowi podczas jego tworzenia. W razie potrzeby można zmienić tę nazwę. Może mieć do 50 znaków. Ta nazwa jest wyświetlana jako tytuł tego planu w portalu Azure Marketplace i Azure Portal. Jest używany jako domyślna nazwa modułu po plan jest gotowy do użycia.

### <a name="plan-summary"></a>Podsumowanie planu

Krótkie podsumowanie planu oprogramowania (nie oferty). To podsumowanie jest wyświetlane w wynikach wyszukiwania w portalu Azure Marketplace i może zawierać maksymalnie 100 znaków.

### <a name="plan-description"></a>Opis planu

Opisz, co sprawia, że ten plan oprogramowania jest wyjątkowy, a także różnice między planami w ramach oferty. Nie opisuj oferty, tylko plan. Ten opis pojawi się w portalu Azure Marketplace i w witrynie Azure portal na stronie **Lista ofert.** Może to być ta sama zawartość, która została podana w podsumowaniu planu i zawierać do 2000 znaków.

Wybierz **pozycję Zapisz** po wypełnieniu tych pól.

#### <a name="plan-examples"></a>Przykłady planu

Poniższe przykłady pokazują, jak pola listy planu są wyświetlane w różnych widokach.

Są to pola w portalu Azure Marketplace podczas wyświetlania szczegółów planu:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustruje pola wyświetlane podczas wyświetlania szczegółów planu w portalu Azure Marketplace.":::

Oto szczegóły planu w witrynie Azure portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustruje szczegóły planu w witrynie Azure portal.":::

## <a name="plan-availability"></a>Dostępność planu

Jeśli chcesz ukryć opublikowaną ofertę, aby klienci nie mogli jej wyszukiwać, przeglądać ani kupować w portalu marketplace, zaznacz pole wyboru **Ukryj plan** na karcie **Dostępność.**

To pole jest używane, gdy:

- Oferta ma być używana pośrednio, gdy odwołuje się do innej aplikacji.
- Oferty nie należy kupować indywidualnie.
- Plan został użyty do wstępnego testowania i nie jest już istotny.
- Plan był używany do ofert tymczasowych lub sezonowych i nie powinien być już oferowany.

## <a name="technical-configuration"></a>Konfiguracja techniczna

Obrazy kontenerów muszą być hostowane w prywatnym [rejestrze kontenerów platformy Azure.](https://azure.microsoft.com/services/container-registry/) Na karcie **Konfiguracja techniczna** podaj informacje referencyjne dotyczące repozytorium obrazów kontenera wewnątrz rejestru kontenerów platformy Azure.

Po opublikowaniu oferty obraz kontenera jest kopiowany do portalu Azure Marketplace w określonym rejestrze kontenerów publicznych. Wszystkie żądania użycia obrazu kontenera są obsługiwane z rejestru kontenerów publicznych w portalu Azure Marketplace, a nie z prywatnego. Aby uzyskać szczegółowe informacje, zobacz [Przygotowywanie zasobów technicznych usługi Azure Container.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

### <a name="image-repository-details"></a>Szczegóły repozytorium obrazów

Podaj następujące informacje na karcie **Szczegóły repozytorium obrazów.**

**Identyfikator subskrypcji platformy Azure** — podaj identyfikator subskrypcji, w którym jest zgłaszane użycie, a usługi są rozliczane za rejestr kontenerów platformy Azure, który zawiera obraz kontenera. Ten identyfikator można znaleźć na [stronie Subskrypcje](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w witrynie Azure portal.

**Nazwa grupy zasobów platformy Azure** — podaj nazwę grupy [zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) zawierającą rejestr kontenerów platformy Azure z obrazem kontenera. Grupa zasobów musi być dostępna w identyfikatorze subskrypcji (powyżej). Nazwę można znaleźć na stronie [Grupy zasobów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) w witrynie Azure portal.

**Nazwa rejestru kontenerów platformy Azure** — podaj nazwę [rejestru kontenerów platformy Azure,](https://docs.microsoft.com/azure/container-registry/container-registry-intro) który ma obraz kontenera. Rejestr kontenerów musi znajdować się w grupie zasobów platformy Azure, która została podana wcześniej. Podaj tylko nazwę rejestru, a nie pełną nazwę serwera logowania. Pamiętaj, aby pominąć **azurecr.io** z nazwy. Nazwę rejestru można znaleźć na [stronie Rejestry kontenerów](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) w witrynie Azure portal.

**Nazwa użytkownika administratora dla rejestru kontenerów platformy Azure** — podaj [nazwę użytkownika administratora](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) połączonego z rejestrem kontenerów platformy Azure, który ma obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że twoja firma ma dostęp do rejestru. Aby uzyskać nazwę użytkownika i hasło administratora, ustaw właściwość **z włączoną przez administratora** na **True** przy użyciu interfejsu wiersza polecenia platformy Azure (CLI). Opcjonalnie można ustawić **użytkownika administratora,** aby **włączyć** w witrynie Azure portal.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustruje okno dialogowe Aktualizowanie rejestru kontenerów.":::

**Hasło do rejestru kontenerów platformy Azure** — podaj hasło dla nazwy użytkownika administratora skojarzonego z rejestrem kontenerów platformy Azure i ma obraz kontenera. Nazwa użytkownika i hasło są wymagane, aby upewnić się, że twoja firma ma dostęp do rejestru. Hasło można uzyskać z witryny Azure Portal, przechodząc do**kluczy dostępu** rejestru >  **kontenerów**lub za pomocą narzędzia Azure CLI za pomocą [polecenia show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustruje menu Klucz dostępu.":::

**Nazwa repozytorium w rejestrze kontenerów platformy Azure**. Podaj nazwę repozytorium rejestru kontenerów platformy Azure, które ma obraz. Podaj nazwę repozytorium podczas wypychania obrazu do rejestru. Nazwę repozytorium można znaleźć, przechodząc do strony > **Repozytoria** [rejestru kontenerów.](https://azure.microsoft.com/services/container-registry/) Aby uzyskać więcej informacji, zobacz [Wyświetlanie repozytoriów rejestru kontenerów w witrynie Azure portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories).

> [!NOTE]
> Po ustawieniu nazwy nie można jej zmienić. Użyj unikatowej nazwy dla każdej oferty na swoim koncie.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Znaczniki obrazów dla nowych wersji oferty

Klienci muszą mieć możliwość automatycznego uruchamiania aktualizacji z portalu Azure Marketplace podczas publikowania aktualizacji. Jeśli nie chcą aktualizować, muszą mieć możliwość pozostania w określonej wersji obrazu. Można to zrobić, dodając nowe znaczniki obrazu za każdym razem, gdy wprowadzasz aktualizację do obrazu.

### <a name="image-tag"></a>Znacznik obrazu

To pole musi zawierać **najnowszy** znacznik, który wskazuje najnowszą wersję obrazu na wszystkich obsługiwanych platformach. Musi również zawierać tag wersji (na przykład, począwszy od xx.xx.xx, gdzie xx jest liczbą). Klienci powinni używać [znaczników manifestu](https://github.com/estesp/manifest-tool) do kierowania na wiele platform. Wszystkie tagi, do których odwołuje się znacznik manifestu, muszą również zostać dodane, abyśmy mogli je przesłać.

Wszystkie znaczniki manifestu (z wyjątkiem najnowszego tagu) **-** muszą zaczynać się od X.Y lub X.Y.Z- gdzie X, Y i Z są liczbami całkowitymi. Na przykład, jeśli **najnowszy** tag wskazuje na 1.0.1-linux-x64, 1.0.1-linux-arm32 i 1.0.1-windows-arm32, te sześć tagów należy dodać do tego pola. Aby uzyskać szczegółowe informacje, zobacz [Przygotowywanie zasobów technicznych usługi Azure Container.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Pamiętaj, aby dodać znacznik testowy do obrazu, aby można było zidentyfikować obraz podczas testowania.

## <a name="review-and-publish"></a>Przeglądanie i publikowanie

Po wypełnić wszystkie wymagane sekcje oferty, można przesłać go do przeglądu i opublikowania.

W prawym górnym rogu portalu wybierz pozycję **Przejrzyj i** **opublikuj**.

Na stronie recenzji możesz:

- Zobacz stan ukończenia każdej sekcji oferty. Nie możesz opublikować, dopóki wszystkie sekcje oferty nie zostaną oznaczone jako kompletne.
  - **Nie rozpoczęto** — nie został uruchomiony i musi zostać ukończony.
  - **Niekompletne** — ma błędy, które muszą zostać naprawione lub wymaga podania większej ilości informacji. Aby uzyskać pomoc, zobacz sekcje we wcześniejszej części tego dokumentu.
  - **Kompletne** — zawiera wszystkie wymagane dane bez błędów. Przed złożeniem oferty należy wypełnić wszystkie sekcje oferty.
- Przekaż zespołowi certyfikacyjnemu instrukcje testowania, aby upewnić się, że oferta jest poprawnie przetestowana. Ponadto należy przedstawić wszelkie dodatkowe uwagi, które są pomocne w zrozumieniu twojej oferty.

Aby przesłać ofertę publikacji, wybierz pozycję **Publikuj**.

Wyślemy Ci wiadomość e-mail z powiadomieniem, kiedy wersja zapoznawcza oferty będzie dostępna do przejrzenia i zatwierdzenia.

Aby opublikować ofertę publiczną (lub prywatną, dla odbiorców prywatnych), przejdź do Centrum partnerów i wybierz **opcję Przejdź na żywo.**

## <a name="next-step"></a>Następny krok

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
