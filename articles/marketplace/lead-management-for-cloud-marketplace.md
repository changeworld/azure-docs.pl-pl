---
title: Zarządzanie potencjalnymi klientami na rynku chmury | Witryna Azure Marketplace i usługa AppSource
description: Omówienie różnych tematów związanych z publikowaniem ofert i artefaktów technicznych w witrynie Azure Marketplace i appsource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: dsindona
ms.openlocfilehash: 94510d02a28e0364f1c715dbcf9ff641fe2b14fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286136"
---
# <a name="lead-management-for-cloud-marketplace"></a>Zarządzanie potencjalnymi klientami dla platformy handlowej w chmurze


Klienci są centrum każdego dobrego biznesu. W transformacji dzisiejszych przejęć produktów marketerzy muszą skupić się na bezpośrednim nawiązywanie kontaktu z klientami i budowanie relacji. Dlatego generowanie wysokiej jakości potencjalnych klientów jest ważnym narzędziem dla twojego cyklu sprzedaży. Po umieszczeniu oferty w [portalu Cloud Partner Portal](https://cloudpartner.azure.com/)istnieją narzędzia umożliwiające programowe otrzymywanie informacji kontaktowych klienta natychmiast po tym, jak klient wyrazi zainteresowanie lub wdroży produkt na rynku. 



## <a name="what-are-leads-in-the-marketplace"></a>Co to są potencjalni klienci na rynku?

Potencjalni klienci pochodzą od klientów, którzy są zainteresowani lub wdrażają twoje produkty z Marketplace. Niezależnie od tego, czy produkt jest wymieniony w portalu Azure Marketplace, czy w usłudze AppSource, będziesz mógł otrzymywać potencjalnych klientów po jego prawidłowym skonfigurowaniu z programu CRM do aukcji w portalu cloud partnerów 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak połączyć system CRM z portalem partnerów w chmurze

Aby rozpocząć pozyskiwanie potencjalnych klientów, łącznik zarządzania potencjalnymi klientami w portalu Cloud Partner portal został zaprojektowany tak, aby można go było łatwo podłączyć z informacjami CRM do listy dostępnych systemów CRM. Teraz możesz łatwo wykorzystać potencjalnych klientów generowanych przez rynek bez znaczącego wysiłku inżynieryjnego, aby zintegrować się z systemem zewnętrznym.

Oto instrukcje krok po kroku dotyczące łączenia każdego z możliwych miejsc docelowych potencjalnych potencjalnych pracowników:

**Dynamics CRM Online** - [Kliknij tutaj,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) aby uzyskać instrukcje dotyczące konfigurowania Dynamics CRM Online do uzyskiwania potencjalnych klientów.

**Marketo** - [Kliknij tutaj,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) aby uzyskać instrukcje dotyczące konfigurowania Konfiguracji Ołowiu Marketo, aby uzyskać potencjalnych klientów.

**Salesforce** - [Kliknij tutaj,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) aby uzyskać instrukcje dotyczące konfigurowania instancji Salesforce w celu uzyskania potencjalnych klientów.

**Kliknij tabelę** - platformy Azure[Kliknij tutaj,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure storage w celu uzyskania potencjalnych klientów w tabeli platformy Azure.

**Https Endpoint** - [Kliknij tutaj,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego https, aby uzyskać potencjalnych klientów.

Po prawidłowym skonfigurowaniu miejsca docelowego potencjalnego klienta i naciśnięciu przycisku Publikuj w ofercie, potwierdzimy połączenie i wyślemy Ci potencjalnego klienta testowego. Podczas przeglądania oferty przed rozpoczęciem pracy na żywo, można również przetestować połączenie potencjalnego klienta, próbując uzyskać ofertę samodzielnie w środowisku podglądu. Ważne jest, aby upewnić się, że ustawienia potencjalnych klientów są aktualne, aby nie utracić potencjalnych klientów, więc upewnij się, że aktualizujesz te połączenia, gdy coś się zmieni na końcu.


### <a name="what-are-the-next-steps"></a>Jakie są dalsze kroki?

Po skonfigurowaniu technicznym, należy włączyć te przewody do bieżącej sprzedaży & strategii marketingowej i procesów operacyjnych. Jesteśmy zainteresowani lepszym zrozumieniem ogólnego procesu sprzedaży i chcemy ściśle współpracować z Tobą w zakresie dostarczania wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby odnieść sukces. Cieszymy się z Twojej opinii na temat tego, jak możemy zoptymalizować i ulepszyć potencjalnych klientów, które wysyłamy Ci z dodatkowymi danymi, które pomogą ci pomóc tym klientom odnieść sukces. Daj nam znać, jeśli chcesz przekazać opinie i sugestie, aby twój zespół sprzedaży mógł odnieść większy sukces z potencjalnymi klientami marketplace.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Typowe błędy konfiguracji potencjalnych potencjalnych pracowników podczas publikowania w portalu partnerów w chmurze 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Sprawdź ustawienia konta programu Dynamics CRM. LastCRMError: Nie można zalogować się do programu Dynamics CRM, LastCRMException:** 

> Jeśli wybrano uwierzytelnianie O365, sprawdź, czy konto użytkownika i hasło są prawidłowe. Jeśli wybrano usługi AAD, sprawdź, czy identyfikator dzierżawy, identyfikator aplikacji i klucz tajny aplikacji są zgodne z tym, co zostało skonfigurowane w u usług AAD. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Jeśli nazwa użytkownika/hasło konta jest prawidłowa, upewnij się, że ma on dostęp do dynamics 365 i ma przypisaną licencję (kroki 11-15 w przypadku korzystania z usługi Azure Active Directory lub ustawień zabezpieczeń w przypadku korzystania z użytkownika pakietu Office). 

 
**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Użytkownik nie ma uprawnień do tworzenia atrybutu leadsourcecode w encji potencjalnego klienta** 

> W aplikacji/użytkownikowi brakuje ról zabezpieczeń w programie Microsoft Marketplace. Wykonaj kroki 11-15, jeśli używasz usługi Azure Active Directory lub ustawień zabezpieczeń, jeśli w [tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)jest używany użytkownik pakietu Office.

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Nie znaleziono dzierżawy. To wystąpienie może się zdarzyć, jeśli nie ma żadnych aktywnych subskrypcji dla dzierżawy.**  

> Identyfikator katalogu podany w sekcji zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu na podstawie instrukcji w kroku 2 (w obszarze Azure Active Directory, [w tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser nie powiodło się — żadne role nie są przypisane do użytkownika.**  

> Rozwiązanie: Przypisywanie roli zabezpieczeń do głównego modułu zapisującego w portalu Microsoft Marketplace. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) w obszarze Ustawienia zabezpieczeń 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Nie znaleziono aplikacji z identyfikatorem w katalogu** 

> Identyfikator aplikacji podany w sekcji zarządzania potencjalnymi klientami nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu na podstawie instrukcji w kroku 8 (w obszarze Azure Active Directory, [stąd).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Żądany identyfikator dzierżawy jest nieprawidłowy i nieprawidłowy w formacie domeny zewnętrznej** 

> Identyfikator katalogu podany w sekcji zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu na podstawie instrukcji w kroku 2 (w obszarze Azure Active Directory, [stąd).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Błąd sprawdzania poprawności poświadczeń.: Podany jest nieprawidłowy klucz tajny klienta.** 

> Rozwiązanie: Zaloguj się do witryny Azure Portal, sprawdź, czy klucz aplikacji jest zgodny z tym, co jest w portalu partnerów w chmurze. Proszę wygenerować hasło na podstawie instrukcji w kroku 10 (w obszarze Azure Active Directory), [stąd](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: Kanał żądania przesunął limit czasu podczas oczekiwania na odpowiedź po 00:02:00. Zwiększ wartość limitu czasu przekazanych do wywołania do żądania lub zwiększyć wartość SendTimeout na Binding. Czas przydzielony do tej operacji może być częścią dłuższego limitu czasu.**  

> Rozwiązanie: Zaloguj się do portalu cloud partner, sprawdź szczegóły sklepu >> docelowy potencjalny klient >> adres URL, sprawdź, czy jest to prawidłowe wystąpienie programu Dynamic CRM

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Czym są potencjalni klienci i dlaczego są dla mnie jako wydawcy w Marketplace?** 

Potencjalni klienci to klienci, którzy wdrażają twoje produkty z Marketplace. Niezależnie od tego, czy produkt jest wymieniony w [witrynie Azure Marketplace,](https://azuremarketplace.microsoft.com/en-us) czy w [usłudze AppSource,](https://appsource.microsoft.com/)będziesz mógł otrzymywać potencjalnych klientów zainteresowanych twoim produktem, jeśli w ofercie skonfigurowano miejsce docelowe potencjalnego klienta.  


**Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?** 

Dokumentację można znaleźć tutaj: [Uzyskaj potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) lub prześlij bilet pomocy technicznej za pośrednictwem aka.ms/marketplacepublishersupport wybierz typ oferty i zarządzanie potencjalnymi klientami. 



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

    "Create" - This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

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

Dowiedz się więcej w obszarze [Informacje o potencjalnych klientach](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Skonfigurowano usługę Azure BLOB jako miejsce docelowe potencjalnego klienta, dlaczego nie widzę potencjalnego klienta?** 

Potencjalny klient jest zapisywany tylko po wybraniu magazynu obiektów BLOB platformy Azure jako miejsca docelowego potencjalnego klienta. Przełączanie do tabeli platformy Azure w celu odbierania potencjalnego klienta w czasie rzeczywistym 


**Otrzymałem wiadomość e-mail z Marketplace, dlaczego nie mogę znaleźć potencjalnego klienta w moim programie CRM?**  

Jest możliwe, że domena poczty e-mail użytkownika końcowego pochodzi z domeny .edu. Ze względu na prywatność nie przekazujemy danych umożliwiających identyfikację użytkowników z domeny .edu. Prześlij zgłoszenie pomocy technicznej za pośrednictwem aka.ms/marketplacepublishersupport 


 **Skonfigurowano usługę Azure Table/Azure BLOB jako miejsce docelowe potencjalnego klienta, jak mogę wyświetlić potencjalnych klientów?** 

Dostęp do obiektu blob lub tabeli można uzyskać z witryny Azure Portal lub pobrać i zainstalować [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) za darmo, aby wyświetlić tabele/obiekty blob konta usługi Azure storage. 


**Skonfigurowano tabelę platformy Azure jako miejsce docelowe potencjalnego klienta, czy mogę otrzymywać powiadomienia za każdym razem, gdy nowy potencjalny klient jest wysyłany przez marketplace?** 

Tak, postępuj zgodnie z instrukcjami, aby skonfigurować usługę Azure Table + Function w dokumentacji [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Skonfigurowałem Salesforce jako miejsce docelowe potencjalnego klienta, dlaczego nie mogę znaleźć potencjalnych klientów?** 

Sprawdź, czy formularz web to lead jest polem obowiązkowym opartym na liście wyboru. Jeśli tak, przełącz pole na nieokonolikowe pole tekstowe.  
 

**Nie było problemu z moim głównym przeznaczenia, a ja brakowało niektórych prowadzi. Czy mogę je wysłać do mnie w wiadomości e-mail?** 

Ze względu na zasady dotyczące informacji umożliwiających identyfikację użytkownika (prywatne informacje umożliwiające identyfikację użytkownika) nie możemy udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonych wiadomości e-mail. 



**Skonfigurowano usługę Azure Storage (BLOB/Table) jako miejsce docelowe potencjalnego klienta, ile to będzie kosztować?** 

Dane o generacji potencjalnych klienta są niskie (<1 GB dla prawie wszystkich wydawców). Koszt będzie zależeć od liczby otrzymanych potencjalnych klientów, jeśli 1000 potencjalnych klientów zostanie odebranych w ciągu miesiąca, kosztuje około 50 centów. 
