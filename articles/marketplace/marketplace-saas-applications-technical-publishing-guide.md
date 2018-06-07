---
title: Technical aplikacji SaaS witrynę Azure Marketplace Podręcznik publikowania
description: Przewodnik krok po kroku i publikowanie list kontrolnych publikowanie aplikacji SaaS w portalu Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809475"
---
# <a name="saas-applications-technical-publishing-guide"></a>Podręcznik techniczny publikowania aplikacji SaaS

Witamy w portalu Azure Marketplace techniczne aplikacji SaaS Podręcznik publikowania. Ten przewodnik ułatwia candidate i istniejących wydawców, aby wyświetlić listę aplikacji i usług w portalu Azure Marketplace przy użyciu aplikacji SaaS oferty. 

Aby lepiej zrozumieć, jak opublikować ofertę SaaS, w tym przewodniku jest podzielona na następujące sekcje:
* Przegląd oferty
* Wymagania biznesowe
* Wymagania techniczne
* Proces publikowania
* Aby włączyć prób przy użyciu usługi Azure Active Directory
* Poświadczania integracją usługi Azure AD dla witryny Marketplace

## <a name="offer-overview"></a>Przegląd oferty  

W obu sklepy Azure dostępnych aplikacji SaaS w poniższej tabeli opisano bieżący dostępne opcje:

| Opcja sklepu | Wyświetlanie listy | Wersja próbna/Transact |  
| --- | --- | --- |  
| AppSource | Tak (skontaktować się ze mną) | Tak (Power BI/Dynamics) |
| Rynek platformy Azure | Nie | Tak (aplikacji SaaS) |   

**Lista:** opcji publikowania listy składa się z kontaktu mnie typu oferty i jest używany, gdy udział wersji próbnej lub poziomu transakcji nie jest możliwe. Zaletą tej metody jest możliwość wydawców z rozwiązanie na rynek natychmiast rozpocząć odbieranie potencjalnych klientów, które mogą być uwzględniane w transakcji, aby zwiększyć firmy.  
**Wersja próbna/Transact:** klient ma bezpośrednio kupić, czy żądanie korzystania z wersji próbnej dla rozwiązania. Zapewnianie obsługi wersji próbnej powoduje podwyższenie poziomu zaangażowania zaoferowane dla klientów i umożliwia klientom zapoznać się przed zakupem rozwiązania. Środowisko wersji próbnej będzie mieć lepszą szanse podwyższania poziomu w sklepy i należy oczekiwać większej i bardziej rozbudowane potencjalnych klientów z promujących zaangażowanie klienta. Wersje próbne musi zawierać bezpłatna pomoc techniczna co najmniej na czas trwania okresu próbnego.  

| Oferta aplikacji SaaS | Wymagania biznesowe | Wymagania techniczne |  
| --- | --- | --- |  
| **Skontaktuj się z nami** | Yes | Nie |  
| **Usługa Power BI / Dynamics** | Yes | Tak (integracji z usługą Azure AD) |  
| **Aplikacji SaaS**| Yes | Tak (integracji z usługą Azure AD) |     

Aby uzyskać więcej informacji na temat sklepy Marketplace oraz opis poszczególnych opcji publikowania, zobacz [Marketplace wydawca przewodnika](https://aka.ms/sellerguide) i [opcje publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option).

## <a name="business-requirements"></a>Wymagania biznesowe
W modelu SaaS oferują wymagania biznesowe można wykonać równolegle z wymaganiami technicznymi. Większość wymagań biznesowych i informacje są zbierane podczas tworzenia oferty SaaS w portalu dla partnerów chmury. Wymagania biznesowe są następujące: 
* Wyrażenie zgody na uczestnictwo zasad
* Integracja z programem Microsoft 
* Zidentyfikuj odbiorców oferty
* Definiowanie i określić zarządzania potencjalnych klientów do użycia
* Trwa konfigurowanie zasady zachowania poufności informacji i warunki użytkowania
* Definiowanie kontaktów pomocy technicznej  

Aby uzyskać więcej informacji, możesz można znaleźć w temacie [wymagania wstępne dotyczące publikowania witryny marketplace](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>Wymagania techniczne

Wymagania techniczne dla aplikacji SaaS są proste. Wydawcy są wymagane tylko powinny zostać włączone w usłudze Azure Active Directory (Azure AD) do opublikowania. Integracja usługi Azure AD z aplikacjami jest dobrze udokumentowane i firma Microsoft udostępnia wiele zestawów SDK i zasobów, w tym celu.  

Aby rozpocząć, firma Microsoft zaleca mających subskrypcję przeznaczonego do publikowania portalu Azure Marketplace, co umożliwia izolowanie pracy z innych inicjatyw. Gdy jest to realizowane możesz przystąpić do wdrażania aplikacji SaaS w ramach tej subskrypcji do rozpoczęcia pracy programowania.  

Najlepsze dokumentacji usługi Azure Active Directory, przykłady i wskazówki znajdują się w następujących lokalizacjach: 

* [Przewodnik dewelopera usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integracja z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Mapa usługi Azure - bezpieczeństwo i tożsamość](https://azure.microsoft.com/roadmap/?category=security-identity)

Samouczki sprawdź następujące kwestie:

* [Uwierzytelniania usługi Azure Active Directory za pomocą Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Usługa Azure Active Directory tożsamości techniczna widoków — część 1 z 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Usługa Azure Active Directory tożsamości techniczna widoków — część 2 z 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Tworzenie aplikacji z usługą Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure wideo koncentruje się na usłudze Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Bezpłatne szkolenia usługi Azure Active Directory znajduje się w temacie  
* [Microsoft Azure dla serii zawartości specjalistom IT: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Ponadto usługi Azure Active Directory zapewnia lokacji, aby wyszukać aktualizacje usług   
* [Aktualizacje usługi Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

Aby uzyskać pomoc można użyć następujących zasobów:
* [Fora MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>Proces publikowania

SaaS procesu publikowania zawiera kroki techniczne i biznesowe.  Większość pracy, który odbywa się tworzenie i Integrowanie usługi Azure Active Directory może odbywać się równolegle z pracy, wymaganych do spełnienia wymagań biznesowych oferty. Większość wymagań biznesowych są częścią konfiguracji ofertę aplikacji SaaS portalu dla partnerów chmury.  
Na poniższym diagramie przedstawiono, że główne kroki publikowania dla wersji próbnej/Transact zaoferować:  

![Kroki publikowania SaaS](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

W poniższej tabeli opisano poszczególne kroki publikowania główne:  

| Krok publikowania | Opis |   
| --- | --- |  
| **Tworzenie aplikacji SaaS** | Zaloguj się do portalu dla partnerów chmury, wybierz **nowy**, a następnie wybierz **aplikacji SaaS** oferty. |  
| **Utwórz integrację z usługą Azure AD** | Wymagań technicznych opisanych w poprzedniej sekcji do integracji z SaaS oferty z usługą Azure AD. |  
| **Ustawienia oferty**| Wprowadź wszystkie SaaS oferty początkowej informacje. Identyfikator oferty i oferują nazwę chcesz użyć. |     
| **Ustaw informacje techniczne** | Wprowadź informacje techniczne dotyczące oferty. Dla aplikacji SaaS identyfikator URI tego rozwiązania i typ oferty zakupu przycisku (wolne, dziennika lub skontaktuj się z Me) są wymagane. |  
| **Drive(Optional) testu** | To opcjonalny typ wersji próbnej, potrzebne przede wszystkim do innych typów z witryny Marketplace oferty. Umożliwia ona zostały wdrożone w ramach subskrypcji wydawcy lub odbiorcy końcowego wersji próbnej. |  
| **Ustaw materiałów sklepu oferty**| W tej sekcji wydawcy będzie link i przekazać logo, materiałów, dokumenty prawne marketingowych i skonfigurować system zarządzania potencjalnych klientów. |
| **Ustaw kontakty oferty** | Wprowadź Engineering kontakty i informacje kontaktowe pomocy technicznej dla oferty SaaS. |  
| **Sprawdź integracji z usługą AD SaaS aplikacji Azure** | Przed przesłaniem do publikowania aplikacji SaaS, należy sprawdzić, czy aplikacja jest zintegrowana z usługą Azure AD |  
| **Publikuj ofertę**| Po ukończeniu ofertę i zasobów technicznych, możesz przesłać oferty. Spowoduje to uruchomienie procesu publikowania, w którym szablon rozwiązania jest testowana, zweryfikowane, certyfikowane i zatwierdzone do opublikowania. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Aby włączyć prób przy użyciu usługi Azure Active Directory  

Microsoft uwierzytelnia wszyscy użytkownicy portalu Marketplace z usługą Azure AD, dlatego uwierzytelniony użytkownik kliknie za pośrednictwem listy Twojej wersji próbnej w witrynie Marketplace i jest przekierowywany do środowiska wersji próbnej, można udostępnić użytkownika bezpośrednio do korzystania z wersji próbnej, bez konieczności Dodatkowe logowania kroku. Token, który odbiera aplikacji z usługi Azure AD podczas uwierzytelniania zawiera użytkownika cenne informacje, które umożliwia tworzenie konta użytkownika w aplikacji, dzięki któremu można zautomatyzować proces inicjowania obsługi administracyjnej oraz zwiększyć prawdopodobieństwo konwersji. Aby uzyskać więcej informacji o tokenie, zobacz [tokeny próbki](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Używanie programu Azure AD do włączenia uwierzytelniania kliknij 1 do aplikacji lub wersji próbnej wykonuje następujące czynności:  
* Usprawnia wrażenia z witryny Marketplace do wersji próbnej.  
* Przechowuje działania obsługi produktu nawet po zostanie przekierowany użytkownik w witrynie Marketplace do domeny lub środowisko wersji próbnej.  
* Zmniejsza to prawdopodobieństwo porzucania na przekierowanie, ponieważ nie ma dodatkowych czynności logowania.  
* Zmniejsza bariery wdrożenia dla dużej liczby użytkowników usługi Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Poświadczania integracją usługi Azure AD dla witryny Marketplace  

Można certyfikować integracją usługi Azure AD w na kilka różnych sposobów, w zależności od tego, czy aplikacja jest dzierżawy jednym lub wieloma dzierżawcami i dopiero zaczynasz Single federacyjnych usługi Azure AD logowania jednokrotnego (SSO), czy już jego obsługi.  

**W przypadku aplikacji wielodostępne:**  

Jeśli już obsługują usługi Azure AD, wykonaj następujące czynności:
1.  Zarejestrować aplikację w portalu Azure
2.  Włącz funkcję obsługi wielu dzierżawców w usłudze Azure AD, aby uzyskać środowisko wersji próbnej jednego kliknięcia. Bardziej szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Jeśli jesteś nowym użytkownikiem usługi Azure AD federacyjnej usługi logowania jednokrotnego, wykonaj następujące czynności: 
1.  Zarejestrować aplikację w portalu Azure
2.  Opracowywanie logowania jednokrotnego przy użyciu usługi Azure AD [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) lub [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Włącz obsługę wielu dzierżawców funkcji w usłudze AAD, aby uzyskać środowisko wersji próbnej jednego kliknięcia bardziej szczegółowe informacje można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Dla pojedynczej dzierżawy aplikacji należy użyć dowolnego z następujących opcji:**  
* Dodawanie użytkowników do katalogu jako goście przy użyciu [B2B usługi Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Ręcznie Zapewnij prób dla klientów przy użyciu "Skontaktuj się z Me"
* Tworzenie dla klienta "Test dysk"
* Tworzenie wielodostępnych przykładową aplikację pokaz logowania jednokrotnego

