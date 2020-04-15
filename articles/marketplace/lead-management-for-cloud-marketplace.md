---
title: Zarządzanie ołowiem na rynku komercyjnym | Witryna Azure Marketplace i usługa AppSource
description: Omówienie różnych tematów związanych z publikowaniem ofert i artefaktów technicznych w witrynie Azure Marketplace i appsource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 09b02d043e970c68cdff0e3dc4f97328c9d74c84
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383779"
---
# <a name="lead-management-for-commercial-marketplace"></a>Zarządzanie ołowiem na rynku komercyjnym


Klienci są centrum każdego dobrego biznesu. W transformacji dzisiejszych przejęć produktów marketerzy muszą skupić się na bezpośrednim nawiązywanie kontaktu z klientami i budowanie relacji. Dlatego generowanie wysokiej jakości potencjalnych klientów jest ważnym narzędziem dla twojego cyklu sprzedaży. Po umieszczeniu oferty w [Centrum partnerów](https://partner.microsoft.com/)istnieją narzędzia umożliwiające programowe otrzymywanie informacji kontaktowych klienta natychmiast po tym, jak klient wyrazi zainteresowanie lub wdroży produkt na rynku. 



## <a name="what-are-leads-in-the-marketplace"></a>Co to są potencjalni klienci na rynku?

Potencjalni klienci pochodzą od klientów, którzy są zainteresowani lub wdrażają twoje produkty z Marketplace. Niezależnie od tego, czy produkt jest wymieniony w portalu Azure Marketplace, czy w usłudze AppSource, będziesz mógł odbierać potencjalnych klientów od klientów po jego prawidłowym skonfigurowaniu z programu CRM do aukcji w Centrum partnerów. 


## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Jak podłączyć system CRM do Centrum partnerskiego

Aby rozpocząć uzyskiwanie potencjalnych klientów, złącze zarządzania potencjalnymi klientami w Centrum partnerskim zostało zaprojektowane tak, aby można je było łatwo podłączyć z informacjami CRM do listy dostępnych systemów CRM. Teraz możesz łatwo wykorzystać potencjalnych klientów generowanych przez rynek bez znaczącego wysiłku inżynieryjnego, aby zintegrować się z systemem zewnętrznym.

Oto instrukcje krok po kroku dotyczące łączenia każdego z możliwych miejsc docelowych potencjalnych potencjalnych pracowników:

**Dynamics CRM Online** — instrukcje dotyczące konfigurowania programu Dynamics CRM Online w celu pozyskiwania potencjalnych klientów można znaleźć w programie [Dynamics](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) CRM Online.

**Marketo** - Zobacz [skonfigurować zarządzanie ołowiu w Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) instrukcje dotyczące konfigurowania Marketo Lead Configuration, aby uzyskać potencjalnych klientów.

**Salesforce** — zobacz [konfigurowanie zarządzania potencjalnymi klientami dla Salesforce,](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) aby uzyskać instrukcje dotyczące konfigurowania wystąpienia Salesforce w celu uzyskania potencjalnych klientów.

**Tabela platformy Azure** — zobacz [konfigurowanie zarządzania potencjalnymi klientami przy użyciu tabeli platformy Azure,](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure storage w celu uzyskania potencjalnych klientów w tabeli platformy Azure.

**Punkt końcowy https** — zobacz [konfigurowanie zarządzania potencjalnymi klientami przy użyciu punktu końcowego HTTPS,](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego https w celu uzyskania potencjalnych klientów.

Po prawidłowym skonfigurowaniu miejsca docelowego potencjalnego klienta i naciśnięciu przycisku Publikuj w ofercie, potwierdzimy połączenie i wyślemy Ci potencjalnego klienta testowego. Podczas przeglądania oferty przed rozpoczęciem pracy na żywo, można również przetestować połączenie potencjalnego klienta, próbując uzyskać ofertę samodzielnie w środowisku podglądu. Ważne jest, aby upewnić się, że ustawienia potencjalnych klientów są aktualne, aby nie utracić potencjalnych klientów, więc upewnij się, że aktualizujesz te połączenia, gdy coś się zmieni na końcu.

### <a name="what-are-the-next-steps"></a>Jakie są dalsze kroki?

Po zakończeniu konfiguracji technicznej należy włączyć te przewody do bieżącej strategii marketingowej & sprzedaży i procesów operacyjnych. Jesteśmy zainteresowani lepszym zrozumieniem ogólnego procesu sprzedaży i chcemy ściśle współpracować z Tobą w zakresie dostarczania wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby odnieść sukces. Cieszymy się z Twojej opinii na temat tego, jak możemy zoptymalizować i ulepszyć potencjalnych klientów, które wysyłamy Ci z dodatkowymi danymi, które pomogą ci pomóc tym klientom odnieść sukces. Daj nam znać, jeśli chcesz przekazać opinie i sugestie, aby twój zespół sprzedaży mógł odnieść większy sukces z potencjalnymi klientami marketplace.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a>Typowe błędy konfiguracji potencjalnych potencjalnych pracowników podczas publikowania w Centrum partnerów

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Sprawdź ustawienia konta programu Dynamics CRM. LastCRMError: Nie można zalogować się do programu Dynamics CRM, LastCRMException:** 

> Jeśli wybrano uwierzytelnianie O365, sprawdź, czy konto użytkownika i hasło są prawidłowe. Jeśli wybrano usługi AAD, sprawdź, czy identyfikator dzierżawy, identyfikator aplikacji i klucz tajny aplikacji są zgodne z tym, co zostało skonfigurowane w u usług AAD. Postępuj zgodnie z instrukcjami [tutaj](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Jeśli nazwa użytkownika/hasło konta jest prawidłowa, upewnij się, że ma on dostęp do dynamics 365 i ma przypisaną licencję (kroki 11-15 w przypadku korzystania z usługi Azure Active Directory lub ustawień zabezpieczeń w przypadku korzystania z użytkownika pakietu Office). 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Użytkownik nie ma uprawnień do tworzenia atrybutu leadsourcecode w encji potencjalnego klienta** 

> W aplikacji/użytkownikowi brakuje ról zabezpieczeń w programie Microsoft Marketplace. Wykonaj kroki 11-15, jeśli używasz usługi Azure Active Directory lub ustawień zabezpieczeń, jeśli w [tym miejscu](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)jest używany użytkownik pakietu Office.

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Nie znaleziono dzierżawy. To wystąpienie może się zdarzyć, jeśli nie ma żadnych aktywnych subskrypcji dla dzierżawy.**  

> Identyfikator katalogu podany w sekcji zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu na podstawie instrukcji w kroku 2 (w obszarze Azure Active Directory, [stąd](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser nie powiodło się — żadne role nie są przypisane do użytkownika.**  

> Rozwiązanie: Przypisywanie roli zabezpieczeń do głównego modułu zapisującego w portalu Microsoft Marketplace. Postępuj zgodnie z instrukcjami [tutaj](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) w obszarze Ustawienia zabezpieczeń.

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Nie znaleziono aplikacji z identyfikatorem w katalogu** 

> Identyfikator aplikacji podany w sekcji zarządzania potencjalnymi klientami nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu na podstawie instrukcji w kroku 8 (w obszarze Azure Active Directory, [stąd).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Żądany identyfikator dzierżawy jest nieprawidłowy i nieprawidłowy w formacie domeny zewnętrznej** 

> Identyfikator katalogu podany w sekcji zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu na podstawie instrukcji w kroku 2 (w obszarze Azure Active Directory, [stąd).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Błąd sprawdzania poprawności poświadczeń.: Podany jest nieprawidłowy klucz tajny klienta.** 

> Rozwiązanie: Zaloguj się do witryny Azure portal, sprawdź, czy klucz aplikacji jest zgodny z tym, co znajduje się w Centrum partnerów. Proszę wygenerować hasło na podstawie instrukcji w kroku 10 (w obszarze Azure Active Directory), [stąd](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: Kanał żądania przesunął limit czasu podczas oczekiwania na odpowiedź po 00:02:00. Zwiększ wartość limitu czasu przekazanych do wywołania do żądania lub zwiększyć wartość SendTimeout na Binding. Czas przydzielony do tej operacji może być częścią dłuższego limitu czasu.**  

> Rozwiązanie: Zaloguj się do Centrum partnerów, sprawdź szczegóły sklepu >> docelowy potencjalny klient >> adres URL, sprawdź, czy jest to prawidłowe wystąpienie programu Dynamic CRM.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Czym są potencjalni klienci i dlaczego są dla mnie jako wydawcy w Marketplace?** 

Potencjalni klienci to klienci, którzy wdrażają twoje produkty z Marketplace. Niezależnie od tego, czy produkt jest wymieniony w [witrynie Azure Marketplace,](https://azuremarketplace.microsoft.com) czy w [usłudze AppSource,](https://appsource.microsoft.com/)będziesz mógł otrzymywać potencjalnych klientów zainteresowanych twoim produktem, jeśli w ofercie skonfigurowano miejsce docelowe potencjalnego klienta.  

**Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?** 

Dokumentację można znaleźć tutaj: [Uzyskaj potencjalnych klientów](./partner-center-portal/commercial-marketplace-get-customer-leads.md) lub prześlij bilet pomocy technicznej za pośrednictwem aka.ms/marketplacepublishersupport wybierz typ oferty i zarządzanie potencjalnymi klientami. 

**Czy muszę skonfigurować miejsce docelowe potencjalnego klienta, aby opublikować ofertę w Marketplace?**

Tak, jeśli publikujesz aplikację Contact Me SaaS lub Usługi konsultingowe.  
 
**Jak mogę potwierdzić, że konfiguracja potencjalnego klienta jest poprawna?**

Po skonfigurowaniu oferty i docelowej potencjalnej docelowych, opublikuj swoją ofertę. Na etapie sprawdzania poprawności potencjalnego klienta marketplace wyśle potencjalnego klienta testowego do miejsca docelowego potencjalnego klienta skonfigurowanego w ofercie. 

**Jak znaleźć potencjalnego klienta testowego?**

Wyszukaj "MSFT_TEST" w miejscu docelowym potencjalnego klienta, oto przykładowe dane potencjalnego klienta testu: 

firma = MSFT_TEST_636573304831318844 

kraj = USA 

opis = MSFT_TEST_636573304831318844 

e-mail =MSFT_TEST_636573304831318844@test.com

kodowanie = UTF-8 

kodowanie = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844| \<Nazwa oferty> 

oid = 00Do000000ZHog 

telefon = 1234567890 

tytuł = MSFT_TEST_636573304831318844 

**Mam ofertę na żywo, ale nie widzę żadnych potencjalnych klientów?**

Każdy potencjalny klient będzie miał dane przekazywane w polach w wybranym miejscu docelowym potencjalnego klienta, potencjalni klienci będą mieli następujące dane w tym formacie: **Źródło-Akcja| Oferta** 

  *Źródeł:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Działania:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Oferuje:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Oto przykładowe dane informacji o klientach*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Dowiedz się więcej w obszarze [Informacje o potencjalnych klientach](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Skonfigurowano usługę Azure BLOB jako miejsce docelowe potencjalnego klienta, dlaczego nie widzę potencjalnego klienta?** 

Potencjalny klient jest zapisywany tylko po wybraniu magazynu obiektów BLOB platformy Azure jako miejsca docelowego potencjalnego klienta. Przełącz się do tabeli platformy Azure, aby otrzymać potencjalnego klienta w czasie rzeczywistym.

**Otrzymałem wiadomość e-mail z Marketplace, dlaczego nie mogę znaleźć potencjalnego klienta w moim programie CRM?**  

Jest możliwe, że domena poczty e-mail użytkownika końcowego pochodzi z domeny .edu. Ze względu na prywatność nie przekazujemy danych osobowych z domeny .edu. Prześlij [zgłoszenie pomocy technicznej](https://aka.ms/marketplacepublishersupport).

**Skonfigurowano usługę Azure Table/Azure BLOB jako miejsce docelowe potencjalnego klienta, jak mogę wyświetlić potencjalnych klientów?** 

Dostęp do obiektu blob lub tabeli można uzyskać z witryny Azure Portal lub pobrać i zainstalować [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) za darmo, aby wyświetlić tabele/obiekty blob konta usługi Azure storage. 

**Skonfigurowano tabelę platformy Azure jako miejsce docelowe potencjalnego klienta, czy mogę otrzymywać powiadomienia za każdym razem, gdy nowy potencjalny klient jest wysyłany przez marketplace?** 

Tak, postępuj zgodnie z instrukcjami, aby skonfigurować usługę Azure Table + Function w dokumentacji [tutaj](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Skonfigurowałem Salesforce jako miejsce docelowe potencjalnego klienta, dlaczego nie mogę znaleźć potencjalnych klientów?** 

Sprawdź, czy formularz web to lead jest polem obowiązkowym opartym na liście wyboru. Jeśli tak, przełącz pole na nieokonolikowe pole tekstowe.  
 
**Nie było problemu z moim głównym przeznaczenia, a ja brakowało niektórych prowadzi. Czy mogę je wysłać do mnie w wiadomości e-mail?** 

Ze względu na politykę prywatności nie możemy udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonych wiadomości e-mail. 

**Skonfigurowano usługę Azure Storage (BLOB/Table) jako miejsce docelowe potencjalnego klienta, ile to będzie kosztować?** 

Dane o generacji potencjalnych klienta są niskie (<1 GB dla prawie wszystkich wydawców). Koszt będzie zależeć od liczby otrzymanych potencjalnych klientów, jeśli 1000 potencjalnych klientów zostanie odebranych w ciągu miesiąca, kosztuje około 50 centów. 
