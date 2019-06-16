---
title: Jak utworzyć Dynamics 365 for Operations oferty za pośrednictwem portalu Cloud Partner
description: Jak utworzyć Dynamics 365 for Operations oferty za pośrednictwem portalu Cloud Partner
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a9ada25641e2a56beb9083b145a507c8fd41a46f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935107"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Jak utworzyć Dynamics 365 for Operations oferty za pośrednictwem portalu Cloud Partner

Portal wydawców umożliwia opartej na rolach dostęp do portalu, dzięki czemu wiele osób móc współpracować kierunku stawiane ofertom. Zobacz [chmurze Portal Zarządzanie użytkownikami](./cloud-partner-portal-manage-users.md) Aby uzyskać więcej informacji.

Można było opublikować ofertę w imieniu wydawcy konta, jedną z osób z \"właściciela\" konieczność roli zobowiązuje się do przestrzegania [warunki użytkowania](https://azure.microsoft.com/support/legal/website-terms-of-use/), [poufności informacji firmy Microsoft](https://www.microsoft.com/privacystatement/default.aspx), i [Umowa dotycząca programu certyfikat platformy Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Sposób tworzenia nowego Dynamics 365 for Operations oferty

Po zostały spełnione wszystkie wymagania wstępne, jesteś gotowy do uruchomienia usługi Dynamics 365 dla oferty operacji tworzenia.

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. Na pasku nawigacyjnym po lewej stronie kliknij pozycję \"+ nowa oferta\" i wybierz \"Dynamics 365 for Operations\".
3. Nowa oferta \"edytora\" teraz otworzyć widoku dla Ciebie i jesteśmy gotowi rozpocząć tworzenie.
4. \"Formularzy\" które muszą być wypełnione są widoczne po lewej stronie w obrębie \"edytora\" widoku. Każdy \"formularza\" zawiera zestaw pól, które mają zostać wypełnione. Wymagane pola są oznaczone czerwoną gwiazdką (\*).

Istnieją cztery główne formularze na potrzeby tworzenia Dynamics 365 w celu skorzystania z oferty operacji:

- Ustawienia oferty
- Informacje techniczne
- Szczegóły sklepu
- Kontakty

## <a name="how-to-fill-out-the-offer-settings-form"></a>Jak wypełnić formularz ustawień oferty

Formularz ustawień oferty jest podstawowej postaci, aby określić ustawienia oferty. Poniżej opisano różne pola.

### <a name="offer-id"></a>Identyfikator oferty

Jest to unikatowy identyfikator oferty w ramach profilu wydawcy. Ten identyfikator będzie widoczny w adresach URL produktu. Może składać się tylko z małych znaków alfanumerycznych i łączników (-). Identyfikator nie może kończyć się kreską i nie może przekraczać 50 znaków. To pole jest zablokowane, gdy oferty przechodzi na żywo.

na przykład, jeśli wydawca contoso wydawca tworzy oferty z identyfikator oferty *usług dynamics365 próbki dla operacji*, pojawi się w usłudze AppSource jako `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\`.

### <a name="publisher-id"></a>Identyfikator wydawcy

Tej listy rozwijanej można wybrać profil wydawcy, który chcesz opublikować tę ofertę, w obszarze. To pole jest zablokowane, gdy oferty przechodzi na żywo.

Name (Nazwa)

Jest to nazwa wyświetlana oferty. Jest to nazwa, która będzie wyświetlana w [AppSource](https://appsource.microsoft.com). Może zawierać maksymalnie 50 znaków.

Kliknij pozycję \"Zapisz\" Aby zapisać postęp. Następnym krokiem powinno być Wypełnij informacje techniczne dotyczące oferty.

## <a name="how-to-fill-out-the-technical-info-form"></a>Sposób wypełniania formularza informacje techniczne

Formularz informacje techniczne zawiera informacje, która będzie wyświetlana na stronie oferty. Instrukcje dotyczące różnych pól są opisane poniżej.

![Nowy ekran oferty](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identyfikator rozwiązania

Najpierw jest identyfikator Twojego rozwiązania.

1. Aby znaleźć ten identyfikator, przejdź do usługi cyklu życia, a następnie wybierz rozwiązanie zarządzanie.
2. Po wybraniu odpowiedniego rozwiązania, zostanie wyświetlony identyfikator rozwiązania w ramach przeglądu pakietu. \*\*Jeśli identyfikator jest pusty, wybierz opcję Edytuj i ponownie opublikować pakiet dla identyfikatora rozwiązania się pojawić.

### <a name="validation-assets"></a>Sprawdzanie poprawności zasobów

Przekaż samochód (Raporty analizy dostosowywania) tutaj.

### <a name="does-solution-enable-translations"></a>Czy rozwiązanie umożliwia tłumaczenia?

Wybierz \'tak\' lub \'nie\'.

### <a name="does-solution-include-localizations"></a>Rozwiązanie zawiera Localization(s)?

Wybierz \'tak\' lub \'nie\'.

### <a name="product-version"></a>Wersja produktu

Wybierz nowy AX. Na koniec kliknij przycisk Zapisz.

## <a name="how-to-fill-out-storefront-details-form"></a>Jak Wypełnij formularz szczegółów w sklepie.

Najpierw jest szczegóły oferty.

1. **Podsumowanie oferty**

    \- Podaj krótkie podsumowanie rozwiązania (100 maksymalna liczba znaków).

2. **Opis oferty**

    \- Wprowadź krótki opis rozwiązania. Opis powinien mieć śladu funkcjonalności rozwiązania i bezpośrednio należy wyrównać za pomocą biblioteki BPM. na przykład, jeśli powiesz, że masz funkcje x, y, z w zawartości marketingowej, podczas ostateczny Przegląd podejmiemy się, że te są udokumentowane w bibliotece BPM wewnątrz LCS.

![Ekran szczegółów StoreFront](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Szczegółowe informacje dotyczące listy

![Ekran szczegółów StoreFront](./media/publish_d365_new_offer/storefront_details.png)

1. **Branże** — Sprawdź maksymalnie dwóch branż z danymi opcjami.
2. **Kategorie** — Sprawdź maksymalnie trzy kategorie z danymi opcjami.
3. **Typ aplikacji** — wybierz z danymi opcjami.
4. **Łącze pomocy dla aplikacji** -wprowadź link pomocy dla Twojego rozwiązania.
5. **Obsługiwane kraje/regiony** — zapoznaj się z danymi opcjami.
6. **Obsługiwane języki** — zapoznaj się z danymi opcjami.
7. **Wersja aplikacji** -wprowadź wersję rozwiązania, który został wydany. (na przykład: 1.0.0.0)
8. **Data wydania aplikacji** — wprowadź wersji Data solution(mm/dd/yyyy) usługi.
9. **Twoja aplikacja współpracuje z produktami** -specyficzne produkty, które Twoja aplikacja współpracuje ze. Listę można wyświetlić maksymalnie trzy produkty. Aby wyświetlić listę produktów, kliknij znak plus (obok nowy), a nowe pole tekstowe Otwórz zostanie utworzona dla Ciebie, wprowadź nazwę produktu, który Twoja aplikacja współpracuje ze.
10. **Wyszukiwanie słów kluczowych** -wprowadź typowe terminy, użytkownicy mogą skorzystać w celu znalezienia rozwiązania podczas wyszukiwania. \*\*Tych słów kluczowych nie będą wyświetlane w portalu marketplace.
11. **Ukrywanie klucza** — jest to jakie klucz, który może być łączone z oferty (wersja zapoznawcza) adres URL, aby je ukryć z widoku publicznego. Nie jest hasłem. Można wprowadzić dowolny ciąg, w tym miejscu.

### <a name="marketing-artifacts"></a>Marketing artefaktów

1. Następnie przekazuje użytkownika **logo**, **zrzuty ekranu**. \*\*Należy pamiętać, rozmiary każde przekazanie i wszystkie obrazy powinna mieć format PNG.
2. **Pokaz wideo** -kliknąć \"+ nowe\". Przekazywanie wideo pokaz rozwiązania (tylko łącza w usłudze YouTube lub Vimeo). \*\*. Należy pamiętać, że należy przekazać miniatury o określonym rozmiarze, aby film wideo są wyświetlane w środowisku tymczasowym.
3. **Dokumenty** — Przekaż wszystkie dokumenty powiązane rozwiązania i pamiętaj, aby wprowadzić nazwę dokumentu.

### <a name="legal"></a>Informacje prawne

Te informacje połączy się do zasad ochrony prywatności i warunki użytkowania. Wprowadź rozwiązania adres URL zasad ochrony prywatności i warunki użytkowania. \*\*Odbiorcy będą mogli zobaczyć tych zasad w portalu.

### <a name="customer-support"></a>Obsługa klienta

Adres URL pomocy technicznej będą widoczne w portalu użytkowników.

### <a name="leads-management"></a>Zarządzanie potencjalnymi klientami

Wybierz system CRM, gdzie można prowadzić będą przechowywane. Wybierz \"Azure Table\" tutaj, jeśli masz jedną z następujących systemów CRM: Salesforce, Marketo, Microsoft Dynamics CRM. System CRM, tutaj jest, gdzie będzie zapisywać szczegóły użytkownicy końcowi, którzy spróbują aplikacji w usłudze AppSource (potencjalnych klientów). W zależności od systemu CRM, którą wybierzesz kliknij odpowiedni adres URL poniżej informacje na temat sposobu ukończenia kolejny zbiór pól.

![Szczegóły zarządzania potencjalnego klienta](./media/publish_d365_new_offer/leads.png)

1. Tabela platformy Azure można znaleźć [tutaj](https://aka.ms/leadsettingforazuretable)
2. Dynamics CRM online, można znaleźć [tutaj](https://aka.ms/leadsettingfordynamicscrm)
3. Dla usługi Marketo [tutaj](https://aka.ms/leadsettingformarketo)
4. Salesforce można znaleźć [tutaj](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Sposób wypełniania formularza kontaktów.

Te informacje zostaną użyte do firmy Microsoft i klientów pomocy technicznej. Wprowadź kontakt techniczny i pomocy technicznej Twojej firmy, a adres URL pomocy technicznej dla Twojego rozwiązania. Te informacje będą obowiązywać, jako pojedynczy punkt kontaktu, jeśli firma Microsoft ma pytania dotyczące rozwiązania, a także przez dział pomocy technicznej.

![Ekran kontakty oferty](./media/publish_d365_new_offer/Contacts.png)
