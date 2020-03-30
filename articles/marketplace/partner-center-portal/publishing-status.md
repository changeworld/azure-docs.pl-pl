---
title: Sprawdź stan publikacji oferty w portalu Commercial Marketplace
description: Sprawdź stan kroków sprawdzania poprawności, certyfikacji i wersji zapoznawczej wymaganych do opublikowania oferty za pośrednictwem portalu Marketplace Commercial Marketplace w Centrum partnerów firmy Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275683"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Sprawdź stan publikacji oferty w portalu Commercial Marketplace

You can view your current **Publishing status** on the **Offer overview** tab of the [Commercial Marketplace portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in Partner Center.

Dla każdej oferty powinien być wyświetlany jeden z następujących wskaźników stanu.

| **Stan**    | **Opis**  |
| :---------- | :-------------------|
| **Wersja robocza** | Oferta została utworzona, ale nie jest publikowana. |
| **Publikowanie w toku** | Oferta/ Plan pracuje nad etapami procesu publikowania. |
| **Potrzebna uwaga** | Podczas certyfikacji firmy Microsoft lub któregokolwiek z etapów publikowania wykryto krytyczny problem. |
| **Podgląd** | Oferta została certyfikowana przez firmę Microsoft, a teraz czeka na ostateczną weryfikację przez wydawcę. Wybierz opcję przejdź na żywo, aby oferta była dostępna na żywo. |
| **Na żywo** | Oferta jest na żywo na rynku i może być postrzegana i nabywana przez klientów. |
| **Oczekujące zaprzestanie sprzedaży** | Wydawca wybrał "stop sell" w ofercie lub planie, ale akcja nie została jeszcze zakończona. |
| **Niedostępne na rynku** | Wcześniej opublikowana oferta/plan na rynku została usunięta. |

## <a name="automated-validation"></a>Automatyczna walidacja

Pierwszym krokiem w procesie publikowania jest zestaw automatycznych weryfikacji. Każdy krok sprawdzania poprawności odpowiada funkcji wybranej do włączenia w tworzeniu oferty. Jeśli ta funkcja nie została włączona, sprawdzanie poprawności przeskakuje do następnego kroku publikowania. Każde sprawdzenie poprawności musi zostać zakończone przed zatwierdzeniem stanu publikowania.

- **Konfiguracja przepływu zakupu oferty (<10 min)**

W tym kroku zapewniamy, że oferta może zostać zrealizowana przy zakupie przez klientów za pośrednictwem witryny Azure portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawanych za pośrednictwem firmy Microsoft.

- **Sprawdzanie poprawności danych dysku testowego (~5 min)**

W tym kroku sprawdzamy poprawność danych podanych w sekcji konfiguracji technicznej dysku testowego oferty. Funkcja jazdy testowej jest testowana i zatwierdzana. Ten krok dotyczy tylko ofert z włączoną jazdą próbną.

- **Inicjowanie obsługi administracyjnej dysku testowego (~30 min)**

W tym kroku po sprawdzeniu poprawności danych i funkcji dysku testowego w poprzednim kroku wdrażamy i replikujemy wystąpienia dysku testowego, aby były gotowe do użycia przez klienta.  Ten krok dotyczy tylko ofert z włączoną jazdą próbną.

- **Walidacja i rejestracja zarządzania potencjalnymi klientami (<15 min)**

W tym kroku potwierdzamy, że twój system zarządzania potencjalnymi klientami może odbierać potencjalnych klientów na podstawie szczegółów podanych w konfiguracji oferty. Ten krok ma zastosowanie tylko do ofert z włączonym zarządzaniem potencjalnym klientem.

## <a name="certification"></a>Certyfikacja

Przed opublikowaniem oferty przesłane do portalu Commercial Marketplace w Centrum partnerskim muszą być certyfikowane. Przesłane oferty są poddawane rygorystycznym testom, niektóre zautomatyzowane i inne ręcznie, w tym sprawdzanie [zasad certyfikacji usługi Azure Marketplace.](https://docs.microsoft.com/legal/marketplace/general-policies) Zgłoszenia ofert muszą być oznaczone jako kwalifikujące się do certyfikacji, zanim przejdą do następnego kroku w przepływie publikacji.

### <a name="types-of-validation-that-take-place-during-certification"></a>Rodzaje walidacji, które mają miejsce podczas certyfikacji

Istnieją trzy poziomy walidacji uwzględnione w procesie certyfikacji dla każdej złożonej oferty.

- Uprawnienia firmy wydawcy
- Sprawdzanie poprawności zawartości
- Walidacja techniczna

#### <a name="publisher-business-eligibility"></a>Uprawnienia firmy wydawcy

Każdy typ oferty sprawdza zestaw podstawowych kryteriów kwalifikowalności, które wydawca musi spełnić. Kryteria kwalifikowalności mogą obejmować status MPN wydawcy, posiadane kompetencje, poziomy kompetencji itp.

#### <a name="content-validation"></a>Sprawdzanie poprawności zawartości

Podczas sprawdzania poprawności zawartości informacje wprowadzone podczas tworzenia oferty są sprawdzane pod kątem jakości i trafności. Czeki te będą sprawdzać twoje wpisy pod kątem szczegółów aukcji w portalu marketplace, cen, dostępności, powiązanych planów itp. Aby spełnić kryteria listy w portalu Azure Marketplace i/lub AppSource, firma Microsoft sprawdzi, czy oferta obejmuje:

- tytuł, który dokładnie opisuje ofertę;
- dobrze napisane opisy, które zapewniają dokładny przegląd i propozycję wartości;
- zrzuty ekranu i towarzyszące im filmy; I
- wyjaśnienie, w jaki sposób oferta wykorzystuje platformy i narzędzia firmy Microsoft.

Dowiedz się więcej o kryteriach sprawdzania poprawności zawartości, zapoznając się z [ogólnymi zasadami wyświetlania.](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)

#### <a name="technical-validation"></a>Walidacja techniczna

Podczas sprawdzania poprawności technicznej oferta (pakiet lub binarna) podlega następującym kontrolom.
- Skanowanie w poszukiwaniu złośliwego oprogramowania
- Monitorowane połączenia sieciowe
- Analizowany pakiet
- Dokładne skanowanie rzeczywistej funkcjonalności oferty

Oferta jest testowana na różnych platformach i wersjach w celu zapewnienia, że jest solidna.

Przejrzyj szczegółowe informacje o konfiguracji wymagane dla oferty w sekcji Konfiguracja techniczna tego dokumentu.

### <a name="certification-failure-report"></a>Raport o niepowodzeniu certyfikacji

Po zakończeniu przeglądu, jeśli oferta przeszła certyfikację, przechodzi do następnego kroku w procesie publikowania. Jeśli twoja oferta nie powiodła się, nie powiodła się żadna z aukcji, kontroli technicznej lub zasad lub jeśli nie kwalifikujesz się do przesłania oferty tego typu, zostanie wygenerowany raport o niepowodzeniu certyfikacji i wysłany pocztą e-mail.

Ten raport zawiera opisy wszystkich zasad, które nie powiodły się, wraz z uwagami do przeglądu. Przejrzyj ten raport e-mail, rozwiążesz wszelkie problemy, w razie potrzeby aktualizując ofertę i prześlij ponownie ofertę za pomocą [portalu Marketplace Komercyjnego](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) w Centrum partnerów. (Możesz ponownie przesłać ofertę tyle razy, ile potrzeba, aż do przejścia certyfikacji).

## <a name="preview-creation"></a>Tworzenie w wersji zapoznawczej

W trakcie etapu **tworzenia wersji zapoznawczej** tworzymy wersję oferty dostępną tylko dla odbiorców określonych w sekcji Podgląd oferty.

>[!Note]
> Nie należy używać tego kroku, aby zapewnić osobom spoza organizacji wgląd w ofertę. Zamiast tego użyj opcji **Oferta prywatna.** W tym momencie twoja oferta nie została w pełni przetestowana i zweryfikowana i nie jest gotowa do dystrybucji zewnętrznej.

## <a name="publisher-approval"></a>Zatwierdzenie przez wydawcę

W tym kroku zostanie wysłany e-mail z prośbą o przejrzenie i zatwierdzenie wersji zapoznawczej oferty przed ostatnim krokiem publikowania.

Jeśli wybrano opcję sprzedaży oferty za pośrednictwem firmy Microsoft, będzie można przetestować nabycie i wdrożenie oferty, aby upewnić się, że spełnia ona twoje wymagania na tym etapie zatwierdzania wersji zapoznawczej. Twoja oferta nie będzie jeszcze dostępna na publicznym rynku. Po przetestowaniu i zatwierdzeniu tej wersji zapoznawczej należy wybrać opcję Przejdź na **żywo** na [**pulpicie nawigacyjnym Przegląd oferty.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

Jeśli chcesz wprowadzić zmiany w ofercie na tym etapie podglądu, możesz edytować i ponownie przesłać, aby opublikować nową wersję zapoznawczą. Zobacz artykuł [Aktualizowanie istniejących ofert marketplace,](#update-existing-marketplace-offers) aby uzyskać szczegółowe informacje na temat zmian.

Jeśli Twoja oferta jest już dostępna i jest dostępna publicznie w marketplace, wszelkie aktualizacje, które wprowadzasz, nie będą dostępne, dopóki nie wybierzesz **opcji Przejdź** na żywo na pulpicie nawigacyjnym [**Przegląd oferty.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

### <a name="publish-offer-to-the-public"></a>Publikuj ofertę publicznie

Zaloguj się do Centrum partnerów i uzyskaj dostęp do oferty. Zostaniesz przekierowany na stronę **Przegląd oferty.** W górnej części tej strony zobaczysz opcję **Go live**. Wybierz **opcję Przejdź na żywo,** a po potwierdzeniu oferta zacznie być publikowana publicznie. Otrzymasz powiadomienie e-mail, gdy oferta jest dostępna.

## <a name="publish"></a>Publikowanie

Teraz, gdy wybrałeś **opcję Przejdź na żywo** z ofertą, udostępniając ją na rynku, istnieje seria ostatecznych kontroli poprawności, które zostaną zintensyfikowane, aby upewnić się, że oferta na żywo jest skonfigurowana tak samo, jak wersja zapoznawcza oferty.

- **Konfiguracja przepływu zakupu oferty (>10 min)**

W tym kroku zapewniamy, że oferta może zostać zrealizowana przy zakupie przez klientów za pośrednictwem witryny Azure portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawanych za pośrednictwem firmy Microsoft.

- **Sprawdzanie poprawności danych dysku testowego (~5 min)**

W tym kroku sprawdzamy poprawność danych podanych w sekcji konfiguracji technicznej dysku testowego oferty. Funkcja jazdy testowej jest testowana i zatwierdzana. Ten krok dotyczy tylko ofert z włączoną jazdą próbną.

- **Inicjowanie obsługi administracyjnej dysku testowego (~30 min)**

W tym kroku wdrażamy i replikujemy wystąpienia dysku testowego, aby były gotowe do użycia przez klienta.  Ten krok dotyczy tylko ofert z włączoną jazdą próbną.

- **Walidacja i rejestracja zarządzania potencjalnymi klientami (>15 min)**

W tym kroku potwierdzamy, że twój system zarządzania potencjalnymi klientami może odbierać potencjalnych klientów na podstawie szczegółów podanych w konfiguracji oferty. Ten krok ma zastosowanie tylko do ofert z włączonym zarządzaniem potencjalnym klientem.

- **Publikacja końcowa (>30 minut)**

W tym kroku zapewniamy, że Twoja oferta stanie się publicznie dostępna na rynku.

## <a name="update-existing-marketplace-offers"></a>Aktualizowanie istniejących ofert w portalu marketplace

Jeśli chcesz wprowadzić zmiany w już opublikowanej ofercie, musisz najpierw zaktualizować istniejącą ofertę, a następnie opublikować ją ponownie.

## <a name="next-steps"></a>Następne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace dla zastosowań komercyjnych](./update-existing-offer.md)
