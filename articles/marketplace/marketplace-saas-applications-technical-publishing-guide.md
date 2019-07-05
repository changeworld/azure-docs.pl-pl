---
title: Portal Azure Marketplace SaaS Applications technicznych, Podręcznik publikowania
description: Przewodnik krok po kroku i publikowania listy kontrolne dotyczące publikowania aplikacji SaaS w portalu Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: d2b44b89443b5e7e82d303e1447533546d27ec9a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445742"
---
# <a name="saas-applications-offer-publishing-guide"></a>Aplikacje SaaS oferują Podręcznik publikowania

Aplikacje SaaS mogą być publikowane w witrynie marketplace przy użyciu trzech wywołań różnych akcji: "Kontakt ze mną," "opcji Wypróbuj teraz" i "Pobierz teraz." W tym przewodniku wyjaśniono te trzy opcje, łącznie z wymaganiami dla każdego. 

## <a name="offer-overview"></a>Przegląd oferty  

Aplikacje SaaS są dostępne w obu sklepów platformy Azure w poniższej tabeli opisano bieżący dostępne opcje:

| StoreFront — opcja | Wyświetlanie listy | Przeprowadzaj transakcje/wersji próbnej |  
| --- | --- | --- |  
| AppSource | Tak (kontakt ze mną) | Tak (w usłudze Power BI na Dynamics) |
| Rynek platformy Azure | Nie | Tak (aplikacje SaaS) |   

**Lista:**  Opcja publikowania listy składa się z kontakt ze mną typ oferty i jest używany podczas uczestnictwa poziomu wersji próbnej lub transakcji nie jest możliwe. Zaletą tego podejścia jest umożliwienie wydawców za pomocą rozwiązania na rynek od razu zacząć odbierać potencjalnych klientów, które mogą być uwzględniane w transakcji, aby zwiększyć swoją firmę.  
**Wersja próbna/transakcji:**  Klient ma możliwość bezpośrednio kupowania i Zamów wersję próbną rozwiązania. Wersja próbna środowiska pracy zwiększa poziom zaangażowania oferowane klientom i umożliwia klientom do zbadania rozwiązania przed zakupem. W środowisku wersji próbnej będziesz mieć lepszą szanse podwyższania poziomu w sklepów i możesz spodziewać się więcej oraz dokładniejsze potencjalni klienci zaangażowaniu klientów. Wersje próbne musi zawierać bezpłatną pomoc techniczną dotyczącą co najmniej na czas trwania okresu próbnego.  

| Oferty aplikacji SaaS | Wymagania biznesowe | Wymagania techniczne |  
| --- | --- | --- |  
| **Kontakt z nami** | Tak | Nie |  
| **PowerBI / Dynamics** | Tak | Tak (Integracja usługi Azure AD) |  
| **Aplikacje SaaS**| Yes | Tak (Integracja usługi Azure AD) |     

## <a name="saas-list"></a>Lista SaaS

Wywołanie akcji, aby uzyskać listę SaaS z nie udostępniania wersji próbnych i żadne funkcje rozliczeń jest "Skontaktuj się z pomocą Me." 

Nie ma potrzeby konfigurowania usługi Azure Active Directory, aby wyświetlić listę aplikacji SaaS. 

|Wymagania  |Szczegóły  |
|---------|---------|
|Twoja aplikacja jest oferta SaaS  |   Rozwiązanie to usługa SaaS oferty i oferują wielodostępne produktów SaaS.      |


## <a name="saas-trial"></a>Wersja próbna SaaS

Podaj rozwiązania lub aplikacji za pomocą bezpłatnych do try, oprogramowanie jako usługa (SaaS) — na podstawie wersji próbnej. Bezpłatna wersja próbna oferty mogą być przedstawiane jako konto wersji próbnej ograniczonej lub ograniczony czas. 


|Wymagania  |Szczegóły  |
|---------|---------|
|Twoja aplikacja jest oferta SaaS  |   Rozwiązanie to usługa SaaS oferty i oferują wielodostępne produktów SaaS.      |
|Twoja aplikacja jest włączone w usłudze AAD     |   Klient będzie skierowana do domeny, a będzie transact z klientem bezpośrednio       |


## <a name="saas-trial-technical-requirements"></a>Wymagania techniczne wersja próbna SaaS

Wymagania techniczne dla aplikacji SaaS są proste. Wydawcy wystarczy tylko można zintegrować z usługą Azure Active Directory (Azure AD), które zostaną opublikowane. Integracja usługi Azure AD za pomocą aplikacji jest dobrze udokumentowany, a firma Microsoft udostępnia wiele zestawów SDK i zasobów, w tym celu.  

Aby rozpocząć, zaleca się mieć subskrypcję w wersji dedykowanej dla usługi publikowania w portalu Azure Marketplace, umożliwiając izolowania pracy z innymi inicjatywami. Po tej operacji możesz przystąpić do wdrażania aplikacji SaaS w ramach tej subskrypcji do rozpoczęcia pracy programowania.  

Dokumentacja usługi Azure Active Directory najlepsze, przykłady i wskazówki dotyczące znajdują się w następujących witrynach: 

* [Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integracja z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Plan usługi Azure — zabezpieczenia i tożsamość](https://azure.microsoft.com/roadmap/?category=security-identity)

Samouczki sprawdź następujące kwestie:

* [Uwierzytelnianie usługi Azure Active Directory za pomocą Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Usługa Azure Active Directory Identity techniczne widoków — część 1 z 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Usługa Azure Active Directory Identity techniczne widoków — część 2 z 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Tworzenie aplikacji na platformie Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure wideo poświęcone usłudze Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezpłatne szkolenia Azure Active Directory znajduje się w temacie  
* [Platforma Microsoft Azure dla seria specjalistów IT: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Ponadto usługi Azure Active Directory zapewnia lokacji pod kątem aktualizacji usługi   
* [Aktualizacje usługi Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Wersje próbne przy użyciu usługi Azure Active Directory  

Firma Microsoft uwierzytelnia wszystkich użytkowników witryny Marketplace z usługą Azure AD, dlatego gdy uwierzytelniony użytkownik kliknie za pośrednictwem oferty próbnej w witrynie Marketplace i jest przekierowywany do środowiska próbnego, możesz aprowizować użytkownika bezpośrednio do korzystania z wersji próbnej, bez konieczności Dodatkowe logowania krok. Token, który aplikacja otrzymuje z usługi Azure AD podczas uwierzytelniania zawiera informacje o użytkowniku cenne, która umożliwia tworzenie konta użytkownika w swojej aplikacji, dzięki któremu można zautomatyzować proces inicjowania obsługi administracyjnej oraz zwiększyć prawdopodobieństwo konwersji. Aby uzyskać więcej informacji o tokenie zobacz [tokenów przykładowe](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Aby włączyć uwierzytelnianie 1 kliknięcia do aplikacji lub wersji próbnej za pomocą usługi Azure AD zapewnia następujące funkcje:  
* Usprawnia obsługę klientów z witryny Marketplace w wersji próbnej.  
* Przechowuje działania środowiska w ramach produktu nawet w po użytkownik jest przekierowywany z portalu Marketplace na do domeny lub środowisko w wersji próbnej.  
* Zmniejsza prawdopodobieństwo porzucania na przekierowanie, ponieważ nie jest dodatkowy krok logowania.  
* Zmniejsza bariery wdrożenia dla dużych populacji użytkowników usługi Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certyfikowanie integracji usługi Azure AD w portalu Marketplace  

Certyfikowanie integracji usługi Azure AD w na kilka różnych sposobów, w zależności od tego, czy aplikacja jest pojedynczej dzierżawy lub wielu dzierżawców i dopiero zaczynasz do usługi Azure AD federacyjnego pojedynczego logowania jednokrotnego (SSO), czy już pomocy technicznej.  

**W przypadku aplikacji wielodostępnych:**  

Jeśli możesz już obsługuje usługi Azure AD, wykonaj następujące czynności:
1.  Zarejestruj swoją aplikację w witrynie Azure portal
2.  Włącz funkcję obsługi wielu dzierżawców w usłudze Azure AD, aby korzystać z jednego kliknięcia wersji próbnej. Bardziej szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Jeśli jesteś nowym użytkownikiem usługi Azure AD Federacyjna usługa rejestracji Jednokrotnej, wykonaj następujące czynności: 
1.  Zarejestruj swoją aplikację w witrynie Azure portal
2.  Tworzenie logowania jednokrotnego przy użyciu usługi Azure AD przy użyciu [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) lub [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Włącz obsługę wielu dzierżawców funkcji w usłudze AAD, aby uzyskać wersję próbną interfejsu jednego kliknięcia bardziej szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Dla jednej dzierżawy aplikacji użyj dowolnej z następujących opcji:**  
* Dodawanie użytkowników do katalogu jako użytkowników-gości za pomocą [B2B w usłudze Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ręcznie umożliwić prób dla klientów za pomocą "Skontaktuj się z pomocą Me"
* Twórz na klienta "W wersji testowej"
* Tworzenie aplikacji wielodostępnych przykładowe pokaz przy użyciu logowania jednokrotnego

## <a name="saas-subscriptions"></a>Subskrypcje SaaS

Aby włączyć klienta kupować rozwiązania SaaS, technicznego subskrypcji, należy użyć typu oferty aplikacji SaaS. Dla aplikacji SaaS, muszą być spełnione następujące wymagania:
- Ceny i opłata za usługę stałą, miesięczne stawki.
- Udostępnia metody, aby uaktualnić lub anulować usługę w dowolnym momencie.
Microsoft obsługuje transakcji handlowych. Firma Microsoft rozlicza klientów w Twoim imieniu. Aby użyć aplikacji SaaS subskrypcji, należy włączyć możesz interfejs API usługi zarządzania własnej subskrypcji. Interfejs API usługi zarządzania subskrypcji musi komunikować się bezpośrednio za pomocą interfejsów API usługi Azure Resource Manager. Interfejs API usługi zarządzania subskrypcji musi obsługiwać usługi aprowizacji, uaktualnianie i anulowanie.

| Wymaganie | Szczegóły |  
|:--- |:--- |  
|Rozliczeń oraz metod pomiarów | Twoja oferta jest rozliczana miesięczne stałej stawki. Na podstawie użycia cenach i możliwościach "wartość true w górę" na podstawie użycia nie są obsługiwane w tej chwili. |  
|Unieważnieniu | Twoja oferta jest cancelable przez klienta w dowolnym momencie. |  
|Strona docelowa transakcji | Możesz hostować Azure markami transakcji strony docelowej, gdzie użytkownicy mogą tworzyć i zarządzać ich konta usługi SaaS. |   
| Subskrypcja interfejsu API | Należy udostępnić to usługa, która mogą wchodzić w interakcje z subskrypcją SaaS, tworzenie, aktualizowanie i usuwanie planu usługi oraz konta użytkownika. Krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Okresowo będą wydawane niekrytyczne zmiany interfejsu API. |  

>[!Note]
>Dostawcy rozwiązań (CSP) partnera kanału zoptymalizowany pod kątem w chmurze jest teraz dostępna.  Zobacz [dostawców rozwiązań w chmurze](./cloud-solution-providers.md) więcej informacji na temat marketingowych oferty za pośrednictwem programu Microsoft CSP partner kanałów.

## <a name="next-steps"></a>Kolejne kroki
Jeśli użytkownik jeszcze tego nie zrobiono,

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace.

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty.
- Zobacz [oferty aplikacji SaaS usługi Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer) Aby uzyskać więcej informacji.
