---
title: Sprawdź stan publikowania oferty komercyjnej witryny Marketplace
description: Sprawdź stan sprawdzania poprawności, certyfikacji i kroki (wersja zapoznawcza) wymagane do opublikowania oferty w witrynie Marketplace w komercyjnych w programie Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474526"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Sprawdź stan publikowania oferty komercyjnej witryny Marketplace

Możesz wyświetlić bieżących **stan publikowania** na **oferują Przegląd** karcie [portal Marketplace komercyjnych](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) Centrum partnerskiego.

Jedną z następujących wskaźników stanu powinien być wyświetlany dla każdej oferty.

| **Stan**    | **Opis**  |
| :---------- | :-------------------|
| **Wersja robocza** | Oferta została utworzona, ale nie są publikowane. |
| **Publikowanie w toku** | Oferta/Plan pracuje w drodze kolejne kroki procesu publikowania. |
| **Uwagi potrzebne** | Krytyczny problem został wykryty podczas certyfikacji przez firmę Microsoft lub dowolną z publikacji czynności. |
| **Wersja zapoznawcza** | Oferta certyfikat firmy Microsoft, a teraz czeka ostatecznej weryfikacji przez wydawcę. Wybierz pozycję przejdź na żywo, aby oferty na żywo. |
| **Live** | Oferta jest aktywna w portalu marketplace i mogą być wyświetlane i uzyskanych przez klientów. |
| **Zatrzymaj oczekiwanie sprzedaży** | Wydawcy wybrane "Zatrzymaj sprzedaży" lub oferty, ale akcji, które nie zostało jeszcze zakończone. |
| **Nie jest dostępna w witrynie marketplace** | Usunięto poprzednio opublikowanych/plan oferty w portalu marketplace. |

## <a name="automated-validation"></a>Automatyczne sprawdzanie poprawności

Pierwszym krokiem w procesie publikowania to zbiór zautomatyzowanych operacji sprawdzania poprawności. Każdego kroku sprawdzania poprawności odpowiada to funkcja, której chcesz włączyć w przypadku tworzenia oferty. Jeśli nie włączono tę funkcję, sprawdzanie poprawności pomija dalej do kolejnego etapu publikowania. Każdy sprawdzenie poprawności, należy wykonać przed zatwierdzeniem stan publikowania.

- **Oferty zakupu przepływu konfiguracji (< 10 min)**

W tym kroku Upewniamy się, że Twoja oferta może zostać spełnione, kupowanych przez klientów za pośrednictwem witryny Azure portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawany przez firmę Microsoft.

- **Test sprawdzania poprawności danych dysku (~ 5 min)**

W tym kroku nam się zweryfikować danych, dostępne w wersji testowej sekcji konfiguracji technicznych oferty. Funkcja dysku testów jest przetestowane i zatwierdzone. Ten krok dotyczy tylko w przypadku ofert za pomocą wersji testowej, włączone.

- **Testowanie dysku inicjowania obsługi administracyjnej (OK. 30 min)**

W tym kroku po upewnieniu się, dane i funkcje wersji testowej w poprzednim kroku firma Microsoft wdrażanie i replikować wystąpienia wersji testowej, tak aby były gotowe do użycia klienta.  Ten krok dotyczy tylko w przypadku ofert za pomocą wersji testowej, włączone.

- **Prowadzić weryfikacji zarządzania i rejestracji (< 15 min)**

W tym kroku będziemy upewnij się, że systemu zarządzania potencjalny klient może otrzymać potencjalnych klientów, w oparciu o informacje zawarte w Instalatorze oferty. Ten krok dotyczy tylko w przypadku ofert z włączonym zarządzaniem potencjalnego klienta.

## <a name="certification"></a>Certyfikacja

Zanim został opublikowany, być certyfikowane oferty przesłane do komercyjnego portalu Marketplace w Centrum partnerskim. Przesłano oferty podlegają rygorystyczne testy, niektóre zautomatyzowane i innych ręcznych, tym wyboru przeciwko [portalu Azure Marketplace certyfikacji zasad](https://docs.microsoft.com/legal/marketplace/general-policies). Oferują zgłoszenia musi być oznaczona kwalifikuje się do certyfikacji przed ich przejść do następnego kroku w procesie publikowania.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typy sprawdzania poprawności, które mają miejsce podczas certyfikacji

Istnieją trzy poziomy uwzględnione w procesie certyfikacji dla każdej oferty, przesłane sprawdzania poprawności.

- Wydawcy firmy uprawnień
- Sprawdzanie poprawności zawartości
- Sprawdzanie poprawności Technical Preview

#### <a name="publisher-business-eligibility"></a>Wydawcy firmy uprawnień

Każdy typ oferty sprawdza zestaw wydawcy muszą spełniać kryteria podstawowy uprawnień. Kryteria kwalifikacyjne może obejmować stan MPN wydawcy, posiadane umiejętności, poziomy kompetencji itp.

#### <a name="content-validation"></a>Sprawdzanie poprawności zawartości

Podczas weryfikacji zawartości informacje wprowadzone podczas tworzenia oferty są sprawdzane pod kątem jakości i istotności. Te sprawdzenia przejrzy wpisy w portalu Marketplace, wyświetlanie szczegółów, ceny, dostępność, plany skojarzone itp. Aby spełnić portalu Azure Marketplace i/lub usługi AppSource, wyświetlanie listy kryteria, sprawdzamy, czy oferta obejmuje:

- tytuł, który dokładnie opisuje oferty;
- dobrze napisane opisy, które zapewniają szczegółowe omówienie i opis korzyści;
- zrzuty ekranu jakość i towarzyszących wideo; i
- wyjaśnienie, jak korzysta z tej oferty platformy firmy Microsoft i narzędzi.

Dowiedz się więcej o kryteria weryfikacji zawartości, czytając [ogólne, wyświetlanie listy zasad](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies).

#### <a name="technical-validation"></a>Sprawdzanie poprawności Technical Preview

Podczas weryfikacji technicznej oferty (pakietu lub binarny) ulega następujące testy.
- Skanowane pod kątem złośliwego oprogramowania
- Wywołania sieciowe monitorowane
- Pakiet analizy
- Szczegółowe skanowanie funkcjonalności rzeczywistego oferty

Oferta jest testowany na różnych platformach i wersjach, aby upewnić się, że jest niezawodne.

Przejrzyj szczegóły konkretnej konfiguracji wymagane do oferty w sekcji konfiguracji technicznej niniejszego dokumentu.

### <a name="certification-failure-report"></a>Raport błędów certyfikacji

Po zakończeniu przeglądu Jeśli Twoja oferta został przekazany certyfikacji następnie przenosi do następnego kroku w procesie publikowania. Jeśli oferty nie powiodło się żadnych testów, lista, technicznych lub zasad lub jeśli nie masz uprawnień w celu przesłania oferty tego typu, raport błędów certyfikacji jest wygenerowany i pocztą e-mail.

Ten raport zawiera wszystkie zasady, które nie powiodły się wraz z uwagi dotyczące recenzowania opisów. Zapoznaj się z tego raportu w wiadomości e-mail, Usuń wszystkie problemy z wprowadzeniem aktualizacji oferty w przypadku, gdy potrzebne i Prześlij ponownie przy użyciu oferty [portal Marketplace komercyjnych](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) Centrum partnerskiego. (Można ponownie przesłać ofertę tyle razy, zgodnie z potrzebami, aż do certyfikacji przekazywanie).

## <a name="preview-creation"></a>Tworzenie (wersja zapoznawcza)

Podczas **tworzenia w wersji zapoznawczej** kroku utworzymy wersję oferty tylko grona użytkowników, określone w sekcji podglądu oferty.

## <a name="publisher-approval"></a>Zatwierdzenie wydawcy

W tym kroku zostanie się pocztą e-mail z żądaniem można przejrzeć i zatwierdzić oferta w wersji zapoznawczej przed ostatnim krokiem publikowania.

Jeśli wybrano sprzedaży oferty firmy Microsoft, będzie można przetestować pozyskiwanie i wdrożenia oferty w taki sposób, aby upewnić się, czy spełnia Twoje wymagania na tym etapie zatwierdzania (wersja zapoznawcza). Oferty nie jeszcze będą dostępne w witrynie marketplace siła. Gdy test i zatwierdzić tej wersji zapoznawczej, będzie konieczne wybranie **gotowej do pracy** na [ **oferują Przegląd** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) pulpitu nawigacyjnego.

Jeśli chcesz wprowadzić zmiany do oferty na tym etapie wersji zapoznawczej, może edytować i ponownie przesłać do publikowania nowej wersji zapoznawczej. Zapoznaj się z artykułem [aktualizacji istniejącej witryny marketplace oferuje](#update-existing-marketplace-offers) szczegółowe informacje na temat kolejnych zmian.

Jeśli Twoja oferta jest już na żywo i dostępne publicznie w witrynie marketplace, wszelkie aktualizacje wprowadzone nie będzie przejdź na żywo, dopóki nie wybierzesz **gotowej do pracy** na [ **oferują Przegląd** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) pulpit nawigacyjny.

### <a name="publish-offer-to-the-public"></a>Publikowanie oferty publicznej

Zaloguj się do Centrum partnerskiego i uzyskać dostęp do oferty. Nastąpi przekierowanie do **oferują Przegląd** strony. W górnej części tej strony, będzie widoczna opcja **emisji na żywo**. Wybierz **emisji na żywo,** i po potwierdzeniu, oferta rozpocznie się pobieranie opublikowane publicznie. Gdy oferta jest dostępna na żywo, otrzymają wiadomość e-mail z powiadomieniem.

## <a name="publish"></a>Publikowanie

Teraz, gdy wybrano **emisji na żywo** z ofertą, udostępniając je w witrynie marketplace są szereg kontroli ostatecznej weryfikacji, które będzie zmieniana za pośrednictwem aby upewnić się, że oferty na żywo jest skonfigurowany tak samo jak korzystania z wersji zapoznawczej Wersja tej oferty.

- **Oferty zakupu przepływu konfiguracji (> 10 min)**

W tym kroku Upewniamy się, że Twoja oferta może zostać spełnione, kupowanych przez klientów za pośrednictwem witryny Azure portal. Ten krok ma zastosowanie tylko w przypadku ofert sprzedawany przez firmę Microsoft.

- **Test sprawdzania poprawności danych dysku (~ 5 min)**

W tym kroku nam się zweryfikować danych, dostępne w wersji testowej sekcji konfiguracji technicznych oferty. Funkcja dysku testów jest przetestowane i zatwierdzone. Ten krok dotyczy tylko w przypadku ofert za pomocą wersji testowej, włączone.

- **Testowanie dysku inicjowania obsługi administracyjnej (OK. 30 min)**

W tym kroku będziemy wdrażanie i replikować wystąpienia wersji testowej, tak aby były gotowe do użycia klienta.  Ten krok dotyczy tylko w przypadku ofert za pomocą wersji testowej, włączone.

- **Prowadzić weryfikacji zarządzania i rejestracji (> 15 min)**

W tym kroku będziemy upewnij się, że systemu zarządzania potencjalny klient może otrzymać potencjalnych klientów, w oparciu o informacje zawarte w Instalatorze oferty. Ten krok dotyczy tylko w przypadku ofert z włączonym zarządzaniem potencjalnego klienta.

- **Końcowe publikowania (> 30 minut)**

W tym kroku Upewniamy się, że Twoja oferta staje się publicznie dostępny w witrynie marketplace.

## <a name="update-existing-marketplace-offers"></a>Aktualizowanie istniejącej oferty w witrynie marketplace

Jeśli chcesz wprowadzić zmiany do oferty, które zostały już opublikowane, należy najpierw zaktualizować istniejące oferty, a następnie opublikuj go ponownie.

## <a name="next-steps"></a>Kolejne kroki

- [Aktualizowanie istniejącej oferty w witrynie Marketplace komercyjnych](./update-existing-offer.md)
