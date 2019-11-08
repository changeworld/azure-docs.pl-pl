---
title: Zarządzanie potencjalnymi klientami w chmurze Marketplace | Azure Marketplace i AppSource
description: Omówienie różnych tematów związanych z publikowaniem ofert i artefaktami technicznymi w witrynie Azure Marketplace i AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/05/2018
ms.author: pabutler
ms.openlocfilehash: 047ffd983cecd9f9bd971ee3f97e15734d14ce66
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813275"
---
# <a name="lead-management-for-cloud-marketplace"></a>Zarządzanie potencjalnymi klientami w chmurze Marketplace


Klienci są centrum wszelkich dobrych firm. W przypadku przekształcania nabytych produktów dzisiejszych, rynki muszą skupić się bezpośrednio na łączeniu się z klientami i utworzyć relację. Dlatego generowanie wysokiej jakości potencjalnych klientów jest istotnym narzędziem dla cyklu sprzedaży. Po wystawieniu oferty w [Portal Cloud partner](https://cloudpartner.azure.com/)dostępne są narzędzia umożliwiające programowe otrzymywanie informacji kontaktowych klienta bezpośrednio po wprowadzeniu zainteresowania przez klienta lub wdrożeniu produktu w portalu Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>Co to są potencjalni klienci w portalu Marketplace?

Potencjalni klienci pochodzą od klientów zainteresowanych lub wdrażających produkty z portalu Marketplace. Bez względu na to, czy Twój produkt znajduje się w witrynie Azure Marketplace, czy AppSource, będziesz mieć możliwość otrzymywania potencjalnych klientów po ich poprawnym skonfigurowaniu z poziomu programu CRM do Twoich list w portal Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak połączyć system CRM z portalem Cloud partner

Aby rozpocząć pobieranie potencjalnych klientów, łącznik zarządzania potencjalnym klientem na portal Cloud Partner został zaprojektowany tak, aby można było łatwo podłączyć się z informacjami o programie CRM do listy dostępnych systemów CRM. Teraz można łatwo korzystać z potencjalnych klientów wygenerowanych przez platformę Marketplace bez znacznego wysiłku inżynieryjnego do integracji z systemem zewnętrznym.

Poniżej przedstawiono instrukcje krok po kroku dotyczące sposobu łączenia poszczególnych możliwych miejsc docelowych potencjalnych klientów:

**Dynamics CRM online** - [kliknij tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) , aby uzyskać instrukcje dotyczące sposobu konfigurowania usługi Dynamics CRM Online na potrzeby uzyskiwania potencjalnych klientów.

**Marketo** - [kliknij tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) , aby uzyskać instrukcje dotyczące konfigurowania konfiguracji lidera programu Marketo w celu uzyskania potencjalnych klientów.

 - Salesforce [kliknij tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) , aby uzyskać instrukcje dotyczące konfigurowania wystąpienia usługi Salesforce w celu uzyskania potencjalnych klientów.

**Azure table** - [kliknij tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) , aby uzyskać instrukcje dotyczące konfigurowania konta usługi Azure Storage na potrzeby uzyskiwania potencjalnych klientów w tabeli platformy Azure.

**Punkt końcowy https** - [kliknij tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) , aby uzyskać instrukcje dotyczące konfigurowania punktu końcowego HTTPS w celu uzyskania potencjalnych klientów.

Po poprawnym skonfigurowaniu miejsca docelowego potencjalnego klienta i wybraniu pozycji Opublikuj w swojej ofercie zostanie zweryfikowane połączenie i zostanie wysłany klient testowy. Gdy przeglądasz ofertę przed rozpoczęciem pracy, możesz również przetestować połączenie z liderem, próbując uzyskać swoją ofertę w środowisku wersji zapoznawczej. Ważne jest, aby upewnić się, że Twoje ustawienia lidera są aktualne, dzięki czemu nie utracisz żadnych potencjalnych klientów, więc pamiętaj, aby zaktualizować te połączenia, gdy coś się zmieniło.


### <a name="what-are-the-next-steps"></a>Jakie są następne kroki?

Po zakończeniu konfigurowania technicznego należy uwzględnić te potencjalni klienci w obecnej strategii marketingowej i procesów operacyjnych & sprzedaży. Chcielibyśmy lepiej zrozumieć swój ogólny proces sprzedaży i chcemy ściśle współpracować z udostępnianiem wysokiej jakości potencjalnych klientów i wystarczającej ilości danych, aby zapewnić pomyślne działanie. Będziemy nam poznamy Twoją opinię na temat sposobu, w jaki możemy zoptymalizować i zwiększyć liczbę potencjalnych klientów, którzy wyślą Ci dodatkowe dane w celu pomyślnego przeprowadzenia tych klientów. Daj nam znać, Jeśli interesuje Cię przekazywanie opinii i sugestii, aby umożliwić zespołowi sprzedaży lepsze pomyślną pracę z potencjalnymi klientami portalu Marketplace.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Typowe błędy konfiguracji potencjalnych klientów podczas publikowania w portalu Cloud partner 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Sprawdź ustawienia konta programu Dynamics CRM. LastCRMError: nie można zalogować się do programu Dynamics CRM, LastCRMException:** 

> Jeśli wybrano opcję uwierzytelnianie usługi O365, sprawdź, czy konto użytkownika i hasło są prawidłowe. W przypadku wybrania usługi AAD Sprawdź, czy identyfikator dzierżawy, identyfikator aplikacji i klucz tajny aplikacji są zgodne z informacjami skonfigurowanymi w usłudze AAD. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Jeśli nazwa użytkownika/hasło konta jest prawidłowe, upewnij się, że ma ona dostęp do usługi Dynamics 365 i ma przypisaną licencję (kroki 11-15 w przypadku używania Azure Active Directory lub ustawień zabezpieczeń, jeśli używasz użytkownika pakietu Office). 

 
**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. Użytkownik nie ma uprawnień do tworzenia dla atrybutu leadsourcecode w jednostce potencjalnego klienta.** 

> W aplikacji/użytkowniku brakuje roli zabezpieczeń, aby Microsoft Marketplace składnik zapisywania klienta. Wykonaj kroki 11-15 w przypadku używania Azure Active Directory lub ustawień zabezpieczeń, jeśli używasz [tutaj](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)użytkownika pakietu Office.

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: nie znaleziono dzierżawy. To wystąpienie może wystąpić, jeśli nie ma aktywnych subskrypcji dla dzierżawy.**  

> Identyfikator katalogu podany w sekcji Zarządzanie potencjalnym liderem nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu zgodnie z instrukcjami w kroku 2 (w obszarze Azure Active Directory, w [tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser nie powiodła się — żadne role nie są przypisane do użytkownika.**  

> Rozwiązanie: Przypisz rolę zabezpieczeń do Microsoft Marketplace zapisywania klienta. Postępuj [zgodnie z instrukcjami w obszarze](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) ustawienia zabezpieczeń 

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: aplikacja z identyfikatorem nie została znaleziona w katalogu** 

> Identyfikator aplikacji podany w sekcji zarządzania potencjalnym liderem nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu zgodnie z instrukcjami w kroku 8 (w obszarze Azure Active Directory, w [tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: żądany identyfikator dzierżawy jest nieprawidłowy i nie jest prawidłowym formatem domeny zewnętrznej** 

> Identyfikator katalogu podany w sekcji Zarządzanie potencjalnym liderem nie jest prawidłowym katalogiem. Pobierz identyfikator katalogu zgodnie z instrukcjami w kroku 2 (w obszarze Azure Active Directory, w [tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta w usłudze Dynamics CRM przy użyciu usługi AAD. Wyjątek:: błąd podczas walidacji poświadczeń.: podano nieprawidłowy klucz tajny klienta.** 

> Rozwiązanie: Zaloguj się do witryny Azure Portal i sprawdź, czy klucz aplikacji jest zgodny z tym, co znajduje się w portal Cloud Partner. Wygeneruj hasło na podstawie instrukcji w kroku 10 (w obszarze Azure Active Directory), w [tym miejscu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Nie można zapisać potencjalnego klienta w programie Dynamics CRM. LastCRMError: upłynął limit czasu kanału żądania podczas oczekiwania na odpowiedź po 00:02:00. Zwiększ wartość limitu czasu przekazaną do wywołania żądania lub Zwiększ wartość właściwości SendTimeout dla powiązania. Czas przydzielony na tę operację mógł być częścią dłuższego limitu czasu.**  

> Rozwiązanie: Zaloguj się do portal Cloud Partner, sprawdź szczegóły witryny w sklepie > > docelowy lider > > URL, sprawdź, czy jest to prawidłowe dynamiczne wystąpienie programu CRM

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Co to są potencjalni klienci i dlaczego są ważne dla mnie jako Wydawca w portalu Marketplace?** 

Potencjalni klienci to klienci, którzy wdrażają produkty z portalu Marketplace. Bez względu na to, czy Twój produkt znajduje się w witrynie [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) , czy na [AppSource](https://appsource.microsoft.com/), będziesz mieć możliwość otrzymywania potencjalnych klientów, którzy interesują Twój produkt, jeśli skonfigurowano miejsce docelowe potencjalnego klienta w ofercie.  


**Gdzie mogę uzyskać pomoc w konfigurowaniu miejsca docelowego potencjalnego klienta?** 

Dokumentację można znaleźć tutaj: [Uzyskaj potencjalnych](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) klientów lub Prześlij bilet pomocy technicznej za pomocą aka.MS/marketplacepublishersupport wybierz typ oferty i zarządzanie potencjalnymi klientami. 



**Czy muszę skonfigurować miejsce docelowe potencjalnego klienta w celu opublikowania oferty w portalu Marketplace?**

Tak, w przypadku publikowania aplikacji kontakt ze mną SaaS lub usług konsultingowych.  
 


**Jak upewnić się, że konfiguracja lidera jest poprawna?**

Po skonfigurowaniu oferty i przejściu do potencjalnego klienta Opublikuj swoją ofertę. Na etapie weryfikacji potencjalnego klienta Portal Marketplace wyśle test do miejsca docelowego potencjalnego klienta skonfigurowanego w ofercie. 


**Jak mogę znaleźć potencjalnego klienta testowego?**


Wyszukaj ciąg "MSFT_TEST" w miejscu docelowym potencjalnego klienta, poniżej przedstawiono przykładowe dane potencjalnego klienta testu: 

Firma = MSFT_TEST_636573304831318844 

kraj = US 

Opis = MSFT_TEST_636573304831318844 

adres e-mail = MSFT_TEST_636573304831318844@test.com

kodowanie = UTF-8 

kodowanie = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 | Nazwa oferty\<> 

Identyfikator OID = 00Do0000000ZHog 

numer telefonu = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Mam ofertę na żywo, ale nie widzę żadnych potencjalnych klientów?**

Każdy potencjalny klient będzie miał dane przekazaną w polach w wybranym miejscu docelowym potencjalnego klienta, a potencjalni klienci będą mieli następujący format: **Źródło-akcja | Oferta** 

  *Źródeł*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Wykonane*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Otrzymane*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Poniżej przedstawiono przykładowe dane dotyczące informacji o kliencie*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Więcej informacji znajduje się w obszarze [Informacje o potencjalnym kliencie](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Mam skonfigurowany obiekt BLOB platformy Azure jako miejsce docelowe potencjalnego klienta, dlaczego nie widzę potencjalnego klienta?** 

Potencjalny klient zostanie zapisany tylko w przypadku wybrania usługi Azure BLOB Storage jako miejsca docelowego potencjalnego klienta. Przełącz się do tabeli platformy Azure, aby otrzymać rzeczywisty czas trwania potencjalnego klienta 


**Dlaczego nie mogę znaleźć potencjalnego klienta w portalu Marketplace?**  

Istnieje możliwość, że domena poczty e-mail użytkownika końcowego pochodzi z. edu. Z przyczyn zachowania poufności informacji nie są przekazywane dane OSOBowe z domeny. edu. Prześlij bilet pomocy technicznej za pomocą aka.ms/marketplacepublishersupport 


 **Mam skonfigurowaną usługę Azure Table/Azure BLOB jako lokalizację docelową mojego potencjalnego klienta, jak mogę wyświetlić potencjalnych klientów?** 

Możesz uzyskać dostęp do obiektu BLOB lub tabeli z witryny Azure Portal albo możesz pobrać i zainstalować [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) bezpłatnie, aby wyświetlić tabele/obiekty blob konta usługi Azure Storage. 


**Mam skonfigurowaną tabelę platformy Azure jako lokalizację docelową mojego potencjalnego klienta, czy mogę otrzymywać powiadomienia za każdym razem, gdy nowy potencjalny klient zostanie wysłany przez portal Marketplace?** 

Tak, postępuj zgodnie z instrukcjami, aby skonfigurować funkcję Azure Table + na [tej dokumentacji.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) 



**Mam skonfigurowaną usługi Salesforce jako lokalizację docelową mojego potencjalnego klienta, dlaczego nie mogę znaleźć potencjalnych klientów?** 

Sprawdź, czy formularz sieci Web do realizacji jest polem obowiązkowym opartym na liście wyboru. Jeśli tak, przełącz się na pole do nieobowiązkowego pola tekstowego.  
 

**Wystąpił problem z miejscem docelowym potencjalnego klienta i pominięto kilka potencjalnych klientów. Czy mogę je wysłać do mnie w wiadomości e-mail?** 

Ze względu na zasady danych osobowych (informacji prywatnych) nie można udostępniać informacji o potencjalnych klientach za pośrednictwem niezabezpieczonej poczty e-mail. 



**Mam skonfigurowany magazyn platformy Azure (obiekt BLOB/tabela) jako miejsce docelowe potencjalnego klienta, ile będzie kosztować?** 

Dane gen potencjalnego klienta są niskie (< 1 GB dla niemal wszystkich wydawców). Koszt będzie zależeć od liczby odebranych potencjalnych klientów, jeśli w ciągu miesiąca otrzymasz 1 000 potencjalnych klientów, koszty związane z tym obejmują około 50 centów. 
