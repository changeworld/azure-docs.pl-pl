---
title: Wymagania dotyczące przez typ oferty | Azure
description: W tym artykule opisano kryteria kwalifikacyjne i publikowania partnerów wymagania próby dowiedzieć się, jak publikować aplikacje w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: e45346ce8bd23ea8431faf8a123d57ecdee71606
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937888"
---
# <a name="requirements-by-listing-type"></a>Wymagania dotyczące przez typ oferty  
Wymagania techniczne i marketingowe zawartości zależą od storefront, typ oferty i typ oferty. Przejrzyj następujące specyfikacje, aby sprawdzić zgodność.  
1. StoreFront wymagania:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Wyświetlanie typów i wymagania dotyczące typu oferty:  
    *   Aby uzyskać więcej informacji na temat typów i typów ofert odwiedź Określ typ wyświetlania listy dla strony rozwiązania znajdujących się na [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>StoreFront wymagania: AppSource  
W poniższej tabeli opisano wymagania wstępne do publikowania w usłudze AppSource.  

| Wymaganie | Szczegóły | Wymagane czy Zalecane |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Aplikacja musi zezwalać na usługi Azure Active Directory federacyjnego logowania jednokrotnego (Azure AD Federacyjna usługa rejestracji Jednokrotnej) za zgodą włączone.<ul> <li>Aby dowiedzieć się więcej o włączenie usługi Azure AD Federacyjna usługa rejestracji Jednokrotnej, można znaleźć Konfigurowanie logowania jednokrotnego do aplikacji, które nie są dostępne w usłudze Active Directory platformy Azure, strona galerii aplikacji znajdującym się w [docs.microsoft.com/azure/active-directory/ aktywny katalogu — — niestandardowe — aplikacje saas](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Wymagane |   
| ***Integracja z usługami w chmurze firmy Microsoft*** | Aplikację należy zintegrować z innych usług Microsoft Cloud, takich jak usługi Microsoft Power BI, pakiet Cortana Intelligence lub Microsoft Azure.<ul> <li>Przykład usługi Microsoft Cloud to Internetu rzeczy.</li> </ul> | Zalecane |  
| ***Grupy odbiorców*** | Aplikacja musi być dla użytkowników line-of-business i właściciele firm. | Wymagane | 
| ***Oprogramowanie jako usługa (SaaS) aplikacji dla firm*** | Aplikacja musi spełniać następujące wymagania.<ul> <li>Aplikacja SaaS line-of-business</li> <li>Proces biznesowy fokus</li> <li>Przeznaczone dla przedsiębiorstw</li> <li>Umożliwienie użytkownikom używania ich poświadczeń firmowych, aby zalogować się, takie jak nazwa użytkownika i hasło</li> </ul> | Wymagane |  
| ***Okres bezpłatnej wersji próbnej i wersja próbna*** | Aplikacja musi zawierać jeden następujące opcje w kolejności, klient może używać aplikacji bezpłatnie przez ograniczony czas.<ul> <li>Podaj `try` metody, dzięki czemu klienci mogą rozpocząć okres próbny aplikacji w usłudze AppSource</li> <li>Podaj `request trial` opcji w usłudze AppSource, dzięki czemu klienci mogą poprosić o wersję próbną aplikacji</li> </ul>Bezpłatna wersja próbna, który podasz muszą zaoferować klientowi przez wstępnie określony czas, aby wypróbować aplikację, bez dodatkowych kosztów. | Wymagane |  
| ***Można je łatwo konfigurować, gotowych do użycia rozwiązanie*** | Aplikacja musi być łatwo i szybko skonfigurować i skonfigurować za pomocą dostosowanie nie jest wymagane. | Wymagane |  
| ***Zarządzanie potencjalnymi klientami*** | Włączanie usługi CRM, aby zaakceptować danych potencjalnego klienta, zanim otrzymasz potencjalnych klientów w sklepie.<ul> <li>Przykłady CRM czy Marketo, Microsoft Dynamics i Salesforce</li> </ul> | Wymagane |  
| ***Zasady ochrony prywatności i warunki użytkowania*** | Aplikację należy podać link do strony zasad ochrony prywatności przy użyciu publicznego adresu URL. Warunki użytkowania, musi być podana podczas publikowania jako tekst. | Wymagane |  
| ***Pomoc techniczna*** | Aplikację należy podać link do strony pomocy technicznej klienta przy użyciu publicznego adresu URL. Jeśli Twoja aplikacja jest korzystania z wersji próbnej, następnie musi obsługiwać bez dodatkowych opłat w okresie próbnym. | Wymagane |  

## <a name="storefront-requirements-azure-marketplace"></a>StoreFront wymagania: Azure Marketplace  
Poniżej przedstawiono wymagania wstępne, aby uzyskać listę typów w portalu Azure Marketplace.  

| Wymaganie | Szczegóły | Typ oferty |  
|:--- |:--- |:--- |  
| ***— Zasady udziału*** | Aplikacja musi być zgodna z zasadami uczestnictwa w portalu Azure Marketplace.<ul> <li>Aby uzyskać więcej informacji na temat zasad uczestnictwa, odwiedź witrynę Azure Marketplace — zasady udziału strony znajduje się w [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Przeprowadzaj transakcje<br />trial |  
| ***Integracja z usługą firmy Microsoft*** | Oferty należy użyć lub rozszerzyć typów usługi Microsoft Azure, takich jak obliczeniowych, sieci i magazynu. Oferty należy wyrównać do istniejącej kategorii portalu Azure Marketplace, takich jak bazy danych, zabezpieczeń lub sieci.<ul> <li>Aby uzyskać więcej informacji na temat oferty w witrynie Marketplace można znaleźć aplikacje z portalu Marketplace strony znajduje się w [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | list<br />Przeprowadzaj transakcje<br />trial |  
| ***Grupy odbiorców*** | Oferty muszą być dla specjalistów IT, deweloperów rozwiązań w chmurze lub innych ról customer Technical Preview. | list<br />Przeprowadzaj transakcje<br />trial |  
| ***Zarządzanie potencjalnymi klientami*** | Włącz CRM (programu Marketo, Microsoft Dynamics lub Salesforce) do akceptowania danych potencjalnego klienta, zanim otrzymasz potencjalnych klientów w sklepie. | list<br />Przeprowadzaj transakcje<br />trial |  
| ***Zasady ochrony prywatności i warunki użytkowania*** | Aplikację należy podać link do strony zasad ochrony prywatności przy użyciu publicznego adresu URL. Warunki użytkowania, musi być podana podczas publikowania jako tekst. | list<br />Przeprowadzaj transakcje<br />trial |  
| ***Pomoc techniczna*** | Oferty, musisz podać link do strony pomocy technicznej klienta przy użyciu publicznego adresu URL. Oferty w przypadku korzystania z wersji próbnej, następnie musi obsługiwać bez dodatkowych opłat w okresie próbnym. | Przeprowadzaj transakcje<br />trial |    

## <a name="non-transact-listings"></a>Transact list  
W tej sekcji opisano wszystkie typy oferty, które nie korzystają z instrukcji Transact typ oferty. 

### <a name="list"></a>List  
Listy Typ oferty w portalu marketplace dostępne są następujące typy oferty na sklepów.  

| Typ oferty | Witryna sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Usługi doradcze | AppSource | Wymagania: AppSource: List: Usługi doradcze |  
| Usługi doradcze | Azure Marketplace | Wymagania: Azure Marketplace: List: Usługi doradcze |  
| Kontakt ze mną | AppSource | [](#) |  
| Kontakt ze mną | Azure Marketplace | Wymagania: AppSource: List: Kontakt ze mną |  

#### <a name="requirements-appsource-list-consulting-service"></a>Wymagania: AppSource: List: Usługa DORADCZA  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Charakterystyki oferty usługi | Usługi doradcze musi spełniać następujące kryteria.<ul> <li>Dostarczaj zaangażowania ustalony zakres, czas, stałej cenie (lub wolne).</li> <li>Orient przede wszystkim dla przedsprzedaży.</li> <li>Ogranicz do jednego klienta.</li> <li>Należy przeprowadzić w lokacji.</li> </ul> |  
| Partner wymagania dotyczące usług doradczych | Spełniasz kryteria, które w obszarze odpowiednie dla Twojej usługi.<table><tr><th>Obszar rozwiązania</th><th>Kryteria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Mieć kompetencję na poziomie Silver lub Gold zarządzania relacjami z klientami chmury.</td></tr><tr><td>Dynamics 365 for Finance and Operations, wersja Enterprise</td><td>Mieć kompetencję na poziomie Silver lub Gold Planowanie zasobów przedsiębiorstwa i przychody od operacji w chmurze w końcu 12 miesięcy najmniej 25 000 USD.</td></tr><tr><td>Dynamics 365 for Finance and Operations, wersja Business</td><td>Służą jako dostawca usług chmury (CSP) lub cyfrowego partnera z rekordu (jako uprawniony partner CYFROWY) dla jednego lub więcej klientów.</td></tr><tr><td>Power BI</td><td>Spełnia kryteria partnera z zakresu rozwiązań.</td></tr><tr><td>PowerApps</td><td>Ma to rozwiązanie pokaz partnerów.</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat zarządzania relacjami z klientami, odwiedź stronę chmury zarządzania relacjami z klientami strony znajduje się w [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Aby uzyskać więcej informacji o planowaniu zasobów, odwiedź stronę planowania zasobów przedsiębiorstwa strony znajduje się w [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Aby uzyskać więcej informacji o dostawcy usług Kryptograficznych, odwiedź witrynę dostawcy usług w chmurze strony znajduje się w [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Aby uzyskać więcej informacji na temat jako uprawniony partner CYFROWY, odwiedź stronę Digital Partner of Record i skojarzenie partnera strony znajduje się w [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Aby uzyskać więcej informacji na temat rozwiązania partnera kryteria, odwiedź stronę Partner — Przegląd rozwiązania i zachęty dokumencie znajdującym się w [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Aby uzyskać więcej informacji na temat pokaz partnerów można znaleźć pokaz partnerów strony znajduje się w [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Wymagania: Azure Marketplace: List: Usługa DORADCZA  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Charakterystyki oferty usługi | Usługi doradcze musi spełniać następujące kryteria.<ul> <li>Dostarczaj zaangażowania ustalony zakres, czas, stałej cenie (lub wolne).</li> <li>Orient przede wszystkim dla przedsprzedaży.</li> <li>Ogranicz do jednego klienta.</li> <li>Należy przeprowadzić w lokacji.</li> </ul> |  
| Partner wymagania dotyczące usług doradczych | Musi mieć na poziomie silver lub gold w jednym z następujących zaangażowanych w obszarze odpowiednie dla Twojej usługi. <table><tr><th>Obszar rozwiązania</th><th>Kompetencja</th></tr><td>Platforma usług w chmurze i infrastruktury</td><td>Platforma usług w chmurze<br />Centrum danych</td><tr><td>Tworzenie aplikacji i niezależnych dostawców oprogramowania</td><td>Opracowywanie aplikacji<br />Integracja aplikacji<br />DevOps</td></tr><tr><td>Zarządzanie danymi i analiza</td><td>Analiza danych<br />Platforma danych</td></tr></table><ul> <li>Więcej informacji na temat umiejętności na ten temat można znaleźć w kompetencji za pomocą Microsoft Partner Network strony znajduje się w [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Aby uzyskać więcej informacji na temat listy odwiedź witrynę Azure Marketplace usług doradczych dotyczących strony znajduje się w [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Wersja próbna  

| Typ oferty | Witryna sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Bezpłatne / wersja próbna SaaS | AppSource | Wymagania dotyczące typu listy: Wersja próbna |  
| Bezpłatne / wersja próbna SaaS | Azure Marketplace | Wymagania: Azure Marketplace: Trial: Bezpłatna wersja próbna / wersja próbna SaaS |  
| Interaktywna demonstracja | AppSource | Wymagania dotyczące typu listy: Wersja próbna |  
| Interaktywna demonstracja | Azure Marketplace | [Wymagania: Azure Marketplace: Trial: Interaktywna demonstracja](#requirements-azure-marketplace-trial-interactive-demo) |  
| Wersja testowa | AppSource | Wymagania dotyczące typu listy: Wersja próbna |  
| Wersja testowa | Azure Marketplace | [Wymagania: Azure Marketplace: Trial: Wersja testowa](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Wymagania: Azure Marketplace: Wersja próbna  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Okres bezpłatnej wersji próbnej i wersja próbna | Klient może używać aplikacji bezpłatnie przez ograniczony czas.<br /><br />Klient nie jest wymagane płacisz wszelkich opłat licencji lub subskrypcji oferty sieci Web lub aplikacji. Klient nie jest wymagana do zapłacenia za podstawowych produktów firmy Microsoft lub usługi. Wszystkie opcje wersji próbnej są wdrażane do subskrypcji platformy Azure. Masz wyłączną kontrolę wersji próbnej w optymalizacji kosztów i zarządzania.<br /><br />Możesz wybrać bezpłatną wersję próbną, pokaz interaktywny lub wersja testowa. Niezależnie od tego, możesz wybrać Twoja bezpłatna wersja próbna muszą zaoferować klientowi za pomocą wstępnie ustawionych ilości czasu na wypróbowanie aplikacji bez ponoszenia dodatkowych kosztów.<ul> <li>Aby rozpocząć proces tworzenia wersji testowej, Wyślij wiadomość e-mail do [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Uwaga: Azure środowiska próbne SaaS w portalu Marketplace musi umożliwiają klientom Zaloguj się przy użyciu poświadczeń służbowych.<ul> <li>Aby uzyskać więcej informacji, odwiedź stronę usługi AppSource, wersja próbna środowiska znajdującej się na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Można je łatwo konfigurować, gotowych do użycia rozwiązanie | Aplikacja musi być łatwo i szybko skonfigurować i skonfigurować. |  
| Dostępność / czas pracy | Twoja aplikacja SaaS lub platformy musi mieć czas działania co najmniej 99,9%. |  
| Usługa Azure Active Directory | Oferty muszą zezwalać na, że usługi Azure Active Directory (Azure AD) federacyjnego logowania jednokrotnego (SSO) (Azure AD Federacyjna usługa rejestracji Jednokrotnej), za zgodą włączone. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Wymagania: Azure Marketplace: Trial: Bezpłatna wersja próbna / wersja próbna SaaS  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi do wypróbowania produktu przed zakupy z zautomatyzowaną metodę konwersji do użycia. Umożliwia także dowody koncepcji dla klienta i wspólne zaangażowanie z zespołami sprzedaży firmy Microsoft. | Twoje rozwiązanie jest maszyny wirtualnej lub szablonu rozwiązania.<br /><br />Twoje rozwiązanie jest SaaS, oferty i oferują wielodostępne produktów SaaS.<br /><br />Masz pierwszego uruchomienia komputera do uruchomienia klienta i szybko.<br /><br />Masz pojedynczej dzierżawy, ale są dodawania klientów jako użytkowników-gości. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Wymagania: Azure Marketplace: Trial: Interaktywna demonstracja  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientom w taki sposób zobaczyć w działaniu, bez złożoności konfigurowania rozwiązania. | Rozwiązanie wymaga złożonych ustawień, które byłyby trudne do osiągnięcia w okresie próbnym. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Wymagania: Azure Marketplace: Trial: Wersja testowa  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi do wypróbowania produktu, zanim zakupu.<br /><br />Zapewnia środowisko z przewodnikiem rozwiązania za pomocą wstępnie skonfigurowanych ustawień.<br /><br />Poniżej przedstawiono dodatkowe korzyści, korzystając z wersji testowej.<ul> <li>27% wyszukiwania użytkowników w portalu marketplace są udoskonalane przez użytkowników tylko Pokaż oferty z stacje testu.</li> <li>Oferty z stacje testu Generowanie potencjalnych 38% więcej niż oferuje bez.</li> <li>36% nowe pozyskanie klientów w witrynie marketplace pochodzą od klientów, które miały wersji testowej.</li> <li>Wersje testowe umożliwiają sprzedawców Microsoft lepiej zrozumieć produktu dla wysiłków wspólnej sprzedaży.</li> </ul> | Rozwiązanie jest maszyna wirtualna, szablon rozwiązania usługi lub aplikacji SaaS za pomocą pojedynczej dzierżawy lub jest skomplikowane aprowizację. <br /><br />Nie masz metodę konwersji z wersji próbnej do oferty płatnej. |  

---

## <a name="transact-specific-listings"></a>Przeprowadzaj transakcje dotyczące list

### <a name="transact"></a>Transakcja  

| Typ oferty | Witryna sklepu | Szczegóły |   
|:---        |:---        | :--- |  
| Aplikacje platformy Azure: Aplikacje zarządzane | Azure Marketplace | Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Aplikacje platformy Azure: Aplikacje zarządzane |  
| Aplikacje platformy Azure: Szablon rozwiązania | Azure Marketplace | Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Aplikacje platformy Azure: Szablon rozwiązania |  
| Containers | Azure Marketplace | [Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Kontener](#requirements-azure-marketplace-transact-container) |  
| Aplikacja SaaS  | Azure Marketplace | [Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Aplikacja SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Maszyna wirtualna | Azure Marketplace | [Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Maszyna wirtualna](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Kontener  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczeń oraz metod pomiarów | Albo bezpłatną pomoc techniczną lub model rozliczeń w ramach opcji BYOL. |  
| Obrazów platformy docker | Obraz kontenera musi być oparta na format obrazu platformy Docker i muszą zostać pobrane z rejestrów kontenerów platformy Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Aplikacja SaaS  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczeń oraz metod pomiarów | Twoja oferta jest rozliczana miesięczne stałej stawki. Na podstawie użycia cen i opartej na użyciu *wartość true w górę* opcje nie są obsługiwane w tej chwili. |  
| Unieważnieniu | Twoja oferta jest cancelable przez klienta w dowolnym momencie. |  
| Strona docelowa transakcji | Hostowanie strony docelowej platformy Azure markami transakcji. Stroną docelową umożliwia klientom tworzenie i zarządzanie kontem usługi SaaS. |  
| Subskrypcja SaaS interfejsu API | Zapewnia to usługa, która wchodzi w interakcję z tą subskrypcją SaaS na tworzenie, aktualizowanie i usuwanie planu usługi oraz konta użytkownika. Wszystkie krytyczne zmiany interfejsu API muszą być obsługiwane w ciągu 24 godzin. Wszystkie niekrytyczne zmiany interfejsu API są okresowo aktualizowane. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Wymagania: Azure Marketplace: Przeprowadzaj transakcje: Maszyna wirtualna  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczeń oraz metod pomiarów | Maszyna wirtualna musi obsługiwać comiesięcznym rozliczeniem BYOL i płatność za rzeczywiste użycie. |  
| Wirtualny dysk twardy (VHD) zgodnych z platformą Azure | Maszyny wirtualne muszą zostać skompilowane na systemie Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego systemu Linux, zobacz [dystrybucje systemu Linux zalecanych dla na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Aby uzyskać więcej informacji na temat tworzenia wirtualnego dysku twardego Windows, zobacz [utworzyć wirtualny dysk twardy zgodnych z platformą Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Kolejne kroki
*   Odwiedź stronę [portalu Azure Marketplace i AppSource — przewodnik dla wydawcy](./marketplace-publishers-guide.md) strony.  

