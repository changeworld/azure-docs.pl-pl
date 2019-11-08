---
title: Konfigurowanie potencjalnych klientów | Portal Azure Marketplace
description: Skonfiguruj potencjalnych klientów w komercyjnym portalu Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812650"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Potencjalni klienci z oferty portalu Marketplace

Potencjalni klienci są zainteresowani lub wdrażają oferty z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com) lub z [AppSource](https://appsource.microsoft.com). Po opublikowaniu oferty w portalu Marketplace otrzymasz potencjalnych klientów. W tym artykule wyjaśniono:

* Jak oferta w witrynie Marketplace generuje potencjalnych klientów, dzięki czemu nie można pominąć możliwości biznesowe. 
* Połącz program CRM z ofertą, aby móc zarządzać potencjalnymi klientami w jednej centralnej lokalizacji.
* Zapoznaj się z informacjami o potencjalnym potencjale potencjalnego klienta, dzięki czemu możesz wykonać kolejne czynności w odniesieniu do klientów.

## <a name="generate-customer-leads"></a>Generowanie potencjalnych klientów

Poniżej znajdują się miejsca, w których generowany jest potencjalny klient:

1. Gdy klient wyraża zgodę na udostępnianie informacji po wybraniu pozycji "kontakt ze mną" w portalu Marketplace. Ten **potencjalny klient jest potencjalnym liderem** , gdzie udostępniamy informacje o kliencie, który wyraził zainteresowanie uzyskaniem produktu. Potencjalny klient to Góra lejka pozyskiwania.

      ![Dynamics 365 kontakt ze mną](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Gdy klient wybierze opcję "Pobierz teraz" lub "Utwórz" (w [Azure Portal](https://portal.azure.com/)) w celu uzyskania oferty, ten potencjalny klient jest **aktywnym liderem**, gdzie udostępniamy informacje o kliencie, który rozpoczął wdrażanie produktu.

    ![Pobierz teraz](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![System Windows Server — Tworzenie](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Klient przyjmuje "dysk testowy" lub uruchamia "bezpłatną wersję próbną" oferty. W przypadku dysków testowych lub bezpłatnych wersji próbnych można szybko udostępnić swoją firmę potencjalnym klientom bez żadnych przeszkód związanych z wprowadzaniem.

    ![Dysk testowy Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dysk testowy Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Nawiązywanie połączenia z systemem CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informacje o potencjalnych klientach

Każdy potencjalny klient, który otrzymasz w procesie pozyskiwania klientów, ma dane w określonych polach. Pierwsze pole, dla którego ma zostać wyszukane, to pole `LeadSource`, które jest zgodne z następującym formatem: **Source-Action** | **Oferta**.

**Źródła**: wartość tego pola jest wypełniana na podstawie rynku, który wygenerował potencjalnego klienta. Możliwe wartości to `"AzureMarketplace"`, `"AzurePortal"`i `"AppSource (SPZA)"`.

**Akcje**: wartość dla tego pola jest wypełniana na podstawie akcji podjętej przez klienta w witrynie Marketplace, która wygenerowała potencjalnego klienta. 

Możliwe wartości:

- "INS" — instalacja. Ta akcja znajduje się w witrynie Azure Marketplace lub AppSource, gdy klient kupuje produkt.
- "PLT" — oznacza próbę przetestowania partnera. Ta akcja jest włączona w AppSource, gdy klient korzysta z opcji kontakt ze mną.
- "DNC" — nie należy kontaktować się z. Ta akcja jest włączona w witrynie AppSource, gdy Partner, który został krzyżowo wymieniony na stronie Twojej aplikacji, otrzymuje prośbę o kontakt. Udostępniamy swoje nagłówki, że ten klient został krzyżowo wymieniony w Twojej aplikacji, ale nie trzeba się z nimi skontaktować.
- "Utwórz" — Ta akcja jest dostępna tylko wewnątrz Azure Portal i jest generowana, gdy klient zakupił ofertę na swoje konto.
- "StartTestDrive" — Ta akcja dotyczy tylko dysków testowych i jest generowana, gdy klient uruchamia ich dysk testowy.

**Oferty**: w portalu Marketplace może być wiele ofert. Wartość tego pola jest wypełniana na podstawie oferty, która wygenerowała potencjalny klient. IDENTYFIKATOR wydawcy i identyfikator oferty są wysyłane w tym polu i są wartościami podanymi podczas opublikowania oferty w portalu Marketplace.

W poniższych przykładach pokazano przykładowe wartości w oczekiwanym formacie `publisherid.offerid`: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informacje o kliencie

Informacje o kliencie są wysyłane za pośrednictwem wielu pól. W poniższym przykładzie przedstawiono informacje o klientach, które znajdują się w potencjalnym kliencie.

- Imię: Jan
- LastName: Kowalski
- Poczta e-mail: jsmith\@microsoft.com
- Telefon: 1234567890
- Kraj: US
- Firma: Microsoft
- Tytuł: dyrektor ds

>[!Note]
>Nie wszystkie dane w poprzednim przykładzie są zawsze dostępne dla każdego potencjalnego klienta. Ponieważ będziesz otrzymywać potencjalni klienci z wielu kroków, jak wspomniano w sekcji potencjalni klienci, najlepszym sposobem obsługi potencjalnych klientów jest cofnięcie duplikatów rekordów i spersonalizowanie kolejnych działań. W ten sposób każdy klient otrzymuje odpowiedni komunikat i tworzysz unikatową relację.

## <a name="best-practices-for-lead-management"></a>Najlepsze rozwiązania dotyczące zarządzania potencjalnymi klientami

1. *Proces* — Zdefiniuj czysty proces sprzedaży z punktami kontrolnymi, wskaźnikami KPI i jasno własnością zespołu.
2. *Kwalifikacje* — Zdefiniuj wymagania wstępne, które wskazują, czy potencjalny klient został w pełni kwalifikowany. Upewnij się, że przedstawiciele ds. sprzedaży i marketingu kwalifikują się uważnie, Zanim przeprowadzisz je przez cały proces sprzedaży.
3. *Obserwuj* Niemniej — nie zapomnij kontynuować działania, oczekiwano, że typowa transakcja wymaga od 5 do 12 wywołań kolejnych
4. *Informacyjna* informacyjna potencjalni klienci w celu uzyskania lepszego marginesu zysków.

## <a name="leads-frequently-asked-questions"></a>Potencjalni klienci często zadawane pytania

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?

Możesz znaleźć [tutaj](#connect-to-your-crm-system) dokumentację lub przesłać bilet pomocy technicznej za pomocą aka.MS/marketplacepublishersupport, a następnie wybrać pozycję **"Tworzenie oferty"** → **Typ oferty** → **"Konfiguracja zarządzania liderem".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Czy muszę skonfigurować miejsce docelowe potencjalnego klienta w celu opublikowania oferty w portalu Marketplace?

Odpowiedź zależy od typu publikowanej oferty. SaaS i Dynamics 365 dla ofert związanych z zaangażowaniem klientów, które można wyświetlić jako "kontakt ze mną", wszystkie oferty usługi Dynamics 365 dla operacji, wszyscy użytkownicy programu Dynamics 365 w firmie, a wszystkie oferty usług konsultingowych wymagają połączenia z miejscem docelowym potencjalnego klienta. Jeśli typu oferty nie ma na liście, nie jest to wymagane. Zaleca się jednak skonfigurowanie miejsca docelowego potencjalnego klienta, aby nie przegapić możliwości biznesowe.

### <a name="how-can-i-find-the-test-lead"></a>Jak mogę znaleźć potencjalnego klienta testowego?

Wyszukaj `"MSFT_TEST"` w miejscu docelowym potencjalnego klienta, Oto przykładowy test, który prowadzi od firmy Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Mam ofertę na żywo, ale nie widzę żadnych potencjalnych klientów?

Upewnij się, że połączenie z miejscem docelowym potencjalnego klienta jest prawidłowe. Po osiągnięciu oferty w centrum partnerskim wyślemy Ci lidera testowego. Jeśli zobaczysz potencjalnego klienta testowego, połączenie jest prawidłowe. Możesz również przetestować połączenie z liderem, próbując uzyskać podgląd oferty w kroku wersji zapoznawczej, klikając pozycję "Pobierz teraz", "kontakty mi" lub "bezpłatna wersja próbna" na liście w portalu Marketplace.

Upewnij się również, że szukasz odpowiednich danych. Zawartość w sekcji Informacje o [potencjalnych klientach](#understand-lead-data) w tym dokumencie opisuje dane potencjalnych klientów, które wysyłamy do miejsca docelowego potencjalnego klienta.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Mam skonfigurowany obiekt BLOB platformy Azure jako miejsce docelowe potencjalnego klienta, dlaczego nie widzę potencjalnego klienta?

Miejsce docelowe lidera obiektów blob platformy Azure nie jest już obsługiwane, więc nie masz żadnych potencjalnych klientów wygenerowanych przez ofertę. Przełącz się do dowolnego z opcji innych elementów [docelowych potencjalnego klienta](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Dlaczego nie mogę znaleźć potencjalnego klienta w portalu Marketplace?

Istnieje możliwość, że domena poczty e-mail użytkownika końcowego pochodzi z. edu. Ze względów związanych z prywatnością nie przekazujemy danych osobowych z domeny. edu. Prześlij bilet pomocy technicznej za pomocą aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Mam skonfigurowaną tabelę platformy Azure jako lokalizację docelową mojego potencjalnego klienta, jak mogę wyświetlić potencjalnych klientów?

Dostęp do danych potencjalnych klientów przechowywanych w tabeli platformy Azure można uzyskać z witryny Azure Portal. Możesz też pobrać i zainstalować [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) bezpłatnie, aby wyświetlić dane tabel konta usługi Azure Storage.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Mam skonfigurowaną tabelę platformy Azure jako lokalizację docelową mojego potencjalnego klienta, czy mogę otrzymywać powiadomienia za każdym razem, gdy nowy potencjalny klient zostanie wysłany przez portal Marketplace?

Tak, postępuj zgodnie z instrukcjami w celu skonfigurowania usługi Microsoft Flow, która wysyła wiadomość e-mail, jeśli potencjalny klient zostanie dodany do tabeli platformy Azure w [tej dokumentacji.](./commercial-marketplace-lead-management-instructions-azure-table.md)

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Mam skonfigurowaną usługi Salesforce jako lokalizację docelową mojego potencjalnego klienta, dlaczego nie mogę znaleźć potencjalnych klientów?

Sprawdź, czy formularz "sieć do realizacji" jest polem obowiązkowym opartym na liście wyboru. Jeśli tak, przełącz się na pole do nieobowiązkowego pola tekstowego.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Wystąpił problem z miejscem docelowym potencjalnego klienta i pominięto kilka potencjalnych klientów. Czy mogę je wysłać do mnie w wiadomości e-mail?

Ze względu na zasady danych osobowych (informacji prywatnych) nie można udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonej poczty e-mail.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Mam skonfigurowaną tabelę platformy Azure jako lokalizację docelową mojego potencjalnego klienta, ile będzie kosztować?

Dane gen potencjalnego klienta są niskie (< 1 GB dla niemal wszystkich wydawców). Koszt będzie zależeć od liczby odebranych potencjalnych klientów, jeśli w ciągu miesiąca otrzymasz 1 000 potencjalnych klientów, koszty związane z tym obejmują około 50 centów. Aby uzyskać więcej informacji na temat cennika usługi Storage, zobacz [Cennik usługi Storage](https://azure.microsoft.com/pricing/details/storage/).

Jeśli Twoje pytanie nadal nie odpowiada, skontaktuj się z pomocą techniczną, korzystając z aka.ms/marketplacepublishersupport, a następnie wybierz pozycję **"Tworzenie oferty"** → **Typ oferty** → **"Konfiguracja zarządzania liderem".** 

## <a name="next-steps"></a>Następne kroki

Po zakończeniu konfigurowania technicznego należy uwzględnić te potencjalni klienci w obecnej strategii marketingowej i procesów operacyjnych & sprzedaży. Chcielibyśmy lepiej zrozumieć swój ogólny proces sprzedaży i chcemy ściśle współpracować z udostępnianiem wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby zapewnić pomyślne działanie. Będziemy nam poznamy Twoją opinię na temat sposobu, w jaki możemy zoptymalizować i zwiększyć liczbę potencjalnych klientów, którzy wyślą Ci dodatkowe dane w celu pomyślnego przeprowadzenia tych klientów. Daj nam znać, Jeśli interesuje Cię [przekazywanie opinii](mailto:AzureMarketOnboard@microsoft.com) i sugestii, aby umożliwić zespołowi sprzedaży lepsze pomyślną pracę z potencjalnymi klientami portalu Marketplace.
