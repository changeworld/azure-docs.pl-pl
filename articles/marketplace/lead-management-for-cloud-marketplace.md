---
title: Doprowadzić zarządzanie w portalu Marketplace w chmurze | Portal Azure Marketplace i AppSource
description: Omówienie różne tematy związane z publikacji oferty i artefaktów technicznych do witryny Azure Marketplace i AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 810298fc45becf132da6f082df7ad33e7af828aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769893"
---
# <a name="lead-management-for-cloud-marketplace"></a>Zarządzanie potencjalnych klientów dla portalu marketplace w chmurze


Klienci są Centrum wszystkich firm dobre. Podczas przekształcania pozyskanie produktu dzisiejsza sprzedawcy muszą skoncentrować się na nawiązanie bezpośredniego połączenia z klientami i tworzenie relacji. Jest to, dlaczego generowania potencjalnych klientów wysokiej jakości to kluczowe narzędzie dla cyklu sprzedaży. Po ofercie oferty [portalu Cloud Partner](https://cloudpartner.azure.com/), są włączone na otrzymywanie informacji kontaktowych klienta programowo, natychmiast po klient wyraża zainteresowania lub wdraża swojego produktu w narzędzia Portal Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Co to są potencjalnych klientów w witrynie marketplace?

Potencjalni klienci są od klientów, którzy są zainteresowani lub wdrażanie produkty z witryny Marketplace. Czy produkt jest wymieniony w portalu Azure Marketplace lub w usłudze AppSource, będzie otrzymywać potencjalnych klientów po jego jest prawidłowo skonfigurowany z CRM do Twojej aukcji w portalu Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak połączyć CRM system z portalu cloud partner

Aby rozpocząć pobieranie potencjalnych klientów, łącznika doprowadzić zarządzanie w portalu Cloud Partner jest zaprojektowana tak, że można łatwo podłączyć się przy użyciu danych CRM na listę dostępnych systemu CRM. Teraz można łatwo korzystać z potencjalnymi klientami, wygenerowany przez Portal marketplace bez znaczących nakładów pracy inżynierów do integracji z systemem zewnętrznym.

Poniżej przedstawiono instrukcje krok po kroku do łączenia z każdego miejsca docelowe możliwe potencjalnego klienta:

**Dynamics CRM Online** - [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) Aby uzyskać instrukcje dotyczące sposobu konfigurowania usługi Dynamics CRM Online w celu uzyskania potencjalnych klientów.

**Marketo** - [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) Aby uzyskać instrukcje dotyczące konfigurowania usługi Marketo prowadzić konfiguracji można pobrać potencjalnych klientów.

**SalesForce** - [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) Aby uzyskać instrukcje dotyczące konfigurowania wystąpienia usługi Salesforce, można pobrać potencjalnych klientów.

**Usługa Azure Table** — [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) Aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure storage w celu uzyskania potencjalnych klientów w tabeli platformy Azure.

**Punkt końcowy HTTPS** — [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) Aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego Https można pobrać potencjalnych klientów.

Po zostały poprawnie skonfigurowane w lokalizacji docelowej potencjalnych klientów i serwisem publikowania w ofercie, firma Microsoft sprawdzanie poprawności połączenia i wysyłać realizacji testu. Podczas przeglądania oferty przed przejściem na żywo możesz przetestować połączenie z potencjalnymi klientami, próbując uzyskać samodzielnie ofertę w środowisku w wersji zapoznawczej. Należy się upewnić, że swoje aktualne pobytu ustawienia potencjalnych klientów tak, aby nie utracić potencjalnych klientów, więc upewnij się, że aktualizowanie tych połączeń, zawsze wtedy, gdy coś, co zmieniło się na swojej stronie.


### <a name="what-are-the-next-steps"></a>Co to są kolejne kroki?

Po technicznych, skonfiguruj znajduje się w miejscu, należy dołączyć potencjalni klienci w bieżącej sprzedaży i strategii marketingowej i procesów operacyjnych. Jesteśmy zainteresowani lepsze zrozumienie procesu ogólną sprzedaży i aby ściśle współpracować z Tobą na dostarczaniu wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, które zapewnią Ci sukces. Chętnie poznamy Twoją opinię, jak firma Microsoft optymalizacji i ulepszanie potencjalnych klientów, który możemy wysłać dodatkowe dane, aby sprawić, że ci klienci pomyślnie. Daj nam znać, jeśli interesują Cię zapewnianie opinii i sugestii, aby ułatwić zespołowi sprzedaży korzystać z portalu Marketplace potencjalnych klientów.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Typowe błędy konfiguracji potencjalnego klienta podczas publikowania w portalu cloud partner 

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM. Sprawdź ustawienia konta Dynamics CRM. LastCRMError: Nie można się zalogować do usługi Dynamics CRM, LastCRMException:** 

> Jeśli wybrano opcję uwierzytelniania usługi O365, sprawdź, czy konto użytkownika i hasła jest prawidłowa. Jeśli wybrano usługi AAD, sprawdź, czy identyfikator dzierżawy, identyfikator aplikacji i aplikacji wpisu tajnego klucza dopasowuje co zostało skonfigurowane na usługi AAD. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Jeśli nazwa użytkownika/hasło konta jest prawidłowa, upewnij się, ma dostęp do Dynamics 365 i ma przypisaną licencję (kroki 11-15, jeśli używasz usługi Azure Active Directory lub ustawienia zabezpieczeń, jeśli za pomocą użytkownika usługi Office). 

 
**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM. Użytkownik nie masz uprawnień do tworzenia atrybutu leadsourcecode w jednostce potencjalnego klienta** 

> Brak role zabezpieczeń do usługi składnika zapisywania programu Microsoft Marketplace potencjalnego klienta aplikacji/użytkownika. Wykonaj kroki 11-15, jeśli używasz usługi Azure Active Directory lub ustawienia zabezpieczeń, jeśli za pomocą użytkownika usługi Office [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Nie znaleziono dzierżawy. To wystąpienie może się zdarzyć, jeśli istnieją aktywne subskrypcje dla dzierżawy.**  

> Identyfikator katalogu podane w sekcji Zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Uzyskaj identyfikator katalogu, zgodnie z instrukcjami w kroku 2 (w obszarze Azure Active Directory z [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM. LastCRMError: Nie powiodło się — SecLib::RetrievePrivilegeForUser żadnych ról są przypisane do użytkownika.**  

> Rozwiązanie: Przypisywanie roli zabezpieczeń do usługi składnika zapisywania programu Microsoft Marketplace potencjalnego klienta. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) w obszarze Ustawienia zabezpieczeń 

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Nie można odnaleźć aplikacji o identyfikatorze w katalogu** 

> Identyfikatora aplikacji podanego w sekcji Zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Uzyskaj identyfikator katalogu, zgodnie z instrukcjami w kroku 8 (w obszarze Azure Active Directory z [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Identyfikator żądanej dzierżawy nie jest format prawidłowych i nie domeny zewnętrznej** 

> Identyfikator katalogu podane w sekcji Zarządzanie potencjalnymi klientami nie jest prawidłowym katalogiem. Uzyskaj identyfikator katalogu, zgodnie z instrukcjami w kroku 2 (w obszarze Azure Active Directory z [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM przy użyciu usługi AAD. Wyjątek:: Błąd walidacji poświadczeń.: Podano nieprawidłowy klucz tajny.** 

> Rozwiązanie: Sprawdź, jeśli klucz aplikacji jest zgodny, co znajduje się w portalu Cloud Partner, zaloguj się do witryny Azure Portal. Wygeneruj hasło oparte na instrukcji w kroku 10 (w obszarze Azure Active Directory), z [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta do usługi Dynamics CRM. LastCRMError: Kanał żądania przekroczył limit czasu podczas oczekiwania na odpowiedź po 00:02:00. Zwiększ wartość limitu czasu podawaną do wywołania na żądanie, lub zwiększ wartość właściwości SendTimeout w powiązaniu. Czas przydzielony na tę operację mógł stanowić część większego limitu czasu.**  

> Rozwiązanie: Zaloguj się do portalu Cloud Partner, sprawdź szczegóły w sklepie >> docelowego potencjalny klient >> adresu URL, sprawdź, czy jest prawidłowe wystąpienie dynamicznego CRM

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Jakie są potencjalnymi klientami i dlaczego są one ważne do mnie jako wydawcę w portalu Marketplace?** 

Potencjalni klienci to klientów, którzy wdrażają produkty z witryny Marketplace. Czy produktu znajduje się na [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) lub [AppSource](https://appsource.microsoft.com/), będzie można odbierać potencjalnych klientów, którzy są zainteresowani produktu, jeśli skonfigurowano docelowy potencjalnego klienta w ofercie.  


**Gdzie można uzyskać pomoc w konfigurowaniu Moje docelowego potencjalnego klienta** 

Można znaleźć w tej dokumentacji: [Pozyskiwanie potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) lub Wyślij bilet pomocy technicznej za pośrednictwem typu oferty wybierz aka.ms/marketplacepublishersupport i zarządzanie potencjalnymi klientami. 



**Jestem wymagane do skonfigurowania docelowego potencjalnego klienta w celu opublikowania oferty w portalu Marketplace?**

Tak, w przypadku publikowania aplikacji kontakt ze mną SaaS lub usług doradczych.  
 


**Jak można potwierdzić, czy konfiguracja potencjalnego klienta jest poprawna?**

Po skonfigurowaniu Twoją ofertę i docelowy potencjalny klient, opublikuj swoją ofertę. Na etapie weryfikacji potencjalnego klienta Marketplace wyśle potencjalnego klienta testowego do docelowego potencjalny klient skonfigurowany w ofercie. 


**Jak znaleźć potencjalnego klienta testowego**


Wyszukaj "MSFT_TEST" w lokalizacji docelowej potencjalny klient, poniżej przedstawiono przykładowe dane potencjalnego klienta testowego: 

company = MSFT_TEST_636573304831318844 

Kraj = USA 

description = MSFT_TEST_636573304831318844 

e-mail = MSFT_TEST_636573304831318844@test.com

kodowanie = UTF-8 

kodowanie = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name> 

Identyfikator OID = 00Do0000000ZHog 

Telefon = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Mam na żywo oferty, ale nie widzę żadnych potencjalnych klientów?**

Poszczególnymi potencjalnymi klientami będzie mieć danych przekazanych w polach w lokalizacji docelowej potencjalnego klienta, potencjalni klienci pojawią się w następującym formacie: **Akcja źródłowa | Oferty** 

  *Źródła:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Akcje:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Oferuje:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Poniżej przedstawiono przykładowe dane, informacje o kliencie*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Dowiedz się więcej w obszarze [prowadzić informacje](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Jako lokalizację docelową Moje potencjalny klient, dlaczego nie widzę potencjalnego klienta skonfigurowano obiektów BLOB platformy Azure?** 

Potencjalny klient pobiera zapisać tylko po wybraniu usługi Azure BLOB storage jako lokalizacji docelowej potencjalnego klienta. Przełącz się do tabeli platformy Azure do odbierania w czasie rzeczywistym potencjalnego klienta 


**Otrzymuję wiadomość e-mail z witryny Marketplace, dlaczego nie mogę znaleźć potencjalnego klienta w mojej CRM?**  

Istnieje możliwość, że domena poczty e-mail przez użytkownika końcowego pochodzi z .edu. W celu zachowania prywatności nie przekazanie danych wrażliwych danych osobowych z .edu domeny. Wyślij bilet pomocy technicznej za pośrednictwem aka.ms/marketplacepublishersupport 


 **Skonfigurowano Azure tabeli/obiektów BLOB platformy Azure jako miejsce docelowe Moje potencjalny klient, jak wyświetlić potencjalnych klientów?** 

Będą mieć dostęp obiektów blob lub tabel, z witryny Azure Portal lub pobrać i zainstalować [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) bezpłatnie, aby wyświetlić tabele/obiekty BLOB konta magazynu platformy Azure. 


**Skonfigurowano usługi Azure Table jako miejsce docelowe Moje potencjalnych klientów, można otrzymywać powiadomienia przy każdym wysłaniu nowego potencjalnego klienta w portalu Marketplace?** 

Tak, postępuj zgodnie z instrukcjami, aby ustawić Konfigurowanie usługi Azure Table i funkcji o dokumentacji [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Salesforce skonfigurowano jako miejsce docelowe Moje potencjalny klient, dlaczego nie mogę znaleźć potencjalnych klientów?** 

Sprawdź, czy sieci web, aby prowadzić formularza jest wymaganym polem, w oparciu o listę wyboru. Jeśli tak, przełącz się na polu pole tekstowe nieobowiązkowa.  
 

**Wystąpił problem z lokalizacją docelową Moje potencjalnych klientów i można pominąć niektóre potencjalnych klientów. Czy można mieć ich wysyłane do mnie wiadomość e-mail?** 

Ze względu na zasady dane osobowe (prywatne informacje użytkownika) firma Microsoft nie może udostępniać informacje o potencjalnym kliencie za pośrednictwem niezabezpieczonych wiadomości e-mail. 



**Skonfigurowano usługi Azure Storage (BLOB/Table) jako miejsce docelowe Moje potencjalny klient, ile będzie kosztować?** 

Brakuje danych prowadzić ogólnego (< 1 GB dla prawie wszystkich wydawców). Koszt będzie zależeć od liczby potencjalnych klientów odebrane, odebranie 1000 potencjalnych klientów w ciągu miesiąca koszt wynosi około 50 centów. 
