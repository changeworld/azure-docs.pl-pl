---
title: Określić typ oferty w portalu Azure Marketplace | Azure
description: W tym artykule opisano kryteriami i publikowania partnerów wymagania próby zrozumienia sposobu publikowania aplikacji w portalu Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 1c7fd3ac1a4cf62dd8f909acc26615ea5f96f32c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309102"
---
# <a name="determine-the-listing-type-for-your-solution"></a>Określić typ oferty dla rozwiązania  
Sklepy obsługują wiele opcji publikowania: Lista typów i oferują typy. Wybierz typ oferty, które najlepiej informują szczegóły rozwiązania i usługi. Wszystkie opcje publikowania zapewniają dostęp do prowadzić do udostępniania.   

| Witryna sklepu | Typ oferty | Typ aplikacji | Wytyczne techniczne |  
|:--- |:--- |:--- |:--- |  
| AppSource | [Lista](#list) | [Usługi konsultingowe](#requirements-appSource-list-consulting-service) | [smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf) |  
| Azure Marketplace | [Lista](#list) | [Usługi konsultingowe](#requirements-azure-marketplace-list-consulting-service) | [](#) |  
| Azure Marketplace | [Lista](#list) | [Przesyłać do mnie](#requirements-azure-marketplace-list-contact-me) | [](#) |  
| Azure Marketplace | [Wersja próbna](#trial) | [Bezpłatna wersja próbna / wersja próbna SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) | [](#) |  
| AppSource |  | Cortana Intelligence | [docs.microsoft.com/Azure/Machine-Learning/Team-Data-Science-Process/cortana-Intelligence-appsource-Publishing-Guide](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) |  
| AppSource |  |Dynamics 365 naboru klienta | [docs.microsoft.com/dynamics365/Customer-Engagement/Developer/publish-App-appsource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) |  
| AppSource |  | Dynamics 365 Finanse i operacje | [docs.microsoft.com/dynamics365/Unified-Operations/dev-ITPro/LCS-Solutions/LCS-Solutions-App-Source](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source) |  
| Azure Marketplace | [Wersja próbna](#trial) | [Pokaz interaktywny](#requirements-azure-marketplace-trial-interactive-demo) | [](#) |  
| Azure Marketplace | [Wersja próbna](#trial) | [Przetestuj](#requirements-azure-marketplace-trial-test-drive) | [](#) |  
| AppSource |  | Office 365 | [docs.microsoft.com/Office/dev/Store/Submit-to-the-Office-Store](https://docs.microsoft.com/office/dev/store/submit-to-the-office-store) |  
| AppSource |  | Power BI | [docs.microsoft.com/Power-BI/Developer/Office-Store](https://docs.microsoft.com/power-bi/developer/office-store) | | Azure Marketplace | [Transact](#transact) | [Aplikacje platformy Azure: zarządzanej aplikacji](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  [docs.microsoft.com/Azure/Managed-Applications/overview](https://docs.microsoft.com/azure/managed-applications/overview) |  
| Azure Marketplace | [Transact](#transact) | [Aplikacje platformy Azure: szablon rozwiązania](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  [](#) |  
| Azure Marketplace | [Transact](#transact) | [Kontener](#requirements-azure-marketplace-transact-container) |  [](#) |  
| Azure Marketplace | [Transact](#transact) | [Aplikacja SaaS](#requirements-azure-marketplace-transact-saas-app) |  [](#) |  
| Azure Marketplace | [Transact](#transact) | [Maszyny wirtualne](#requirements-azure-marketplace-transact-virtual-machine) |  [](#) |   

## <a name="azure-certified-program"></a>Azure Certified program  
Wszystkich maszynach wirtualnych (VM) opublikowane w portalu Azure Marketplace są sprawdzane pod kątem programu certyfikowane Azure.  
*   Aby uzyskać więcej informacji, odwiedź witrynę Azure Microsoft Certified strony znajduje się w [azure.microsoft.com/marketplace/programs/certified](https://azure.microsoft.com/marketplace/programs/certified). 

Program certyfikacji zapewnia następujące korzyści.  
*   Maszyny wirtualnej jest zgodna z platformą Azure i Marketplace sprzedaży modelu.  
*   Maszyna wirtualna jest sprawdzane pod kątem zgodności bezpieczeństwa obrazu w trybie online, w tym wirusów i złośliwego oprogramowania.  
*   Maszyna wirtualna zawiera badging na poziomie oferty. Badging jako rozwiązanie zweryfikowane zwiększa podwyższania poziomu dla przedsiębiorstw firmy Microsoft.   

---   

## <a name="appsource-publishing-by-product"></a>Publikowanie AppSource produktu  
Poniższa tabela zawiera więcej informacji na temat określonych wymagań dotyczących AppSource aplikacje, które rozszerzenia pakietu Office, Dynamics i usługi Power BI.  

| Typ oferty | Typ aplikacji | Wytyczne techniczne |  
|:--- |:--- |:--- |  
| [Lista](#list) | [Usługi konsultingowe](#requirements-appSource-list-consulting-service) | [smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf](https://smp-cdn-prod.azureedge.net/documents/Microsoft%20AppSource%20Partner%20Listing%20Guidelines.pdf) |  
|  | Cortana Intelligence | [docs.microsoft.com/Azure/Machine-Learning/Team-Data-Science-Process/cortana-Intelligence-appsource-Publishing-Guide](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-intelligence-appsource-publishing-guide) |  
|  |Dynamics 365 naboru klienta | [docs.microsoft.com/dynamics365/Customer-Engagement/Developer/publish-App-appsource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource) |  
|  | Dynamics 365 Finanse i operacje | [docs.microsoft.com/dynamics365/Unified-Operations/dev-ITPro/LCS-Solutions/LCS-Solutions-App-Source](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/lcs-solutions/lcs-solutions-app-source) |  
|  | Office 365 | [docs.microsoft.com/Office/dev/Store/Submit-to-the-Office-Store](https://docs.microsoft.com/office/dev/store/submit-to-the-office-store) |  
|  | Power BI | [docs.microsoft.com/Power-BI/Developer/Office-Store](https://docs.microsoft.com/power-bi/developer/office-store) |  

---   

## <a name="azure-marketplace-publishing-by-product"></a>Publikowanie witrynę Azure Marketplace produktu  

![](./media/marketplace-publishers-guide/workflow-azure-marketplace.png)  

Poniższa tabela zawiera oferują więcej informacji na temat określonych wymagań dotyczących portalu Azure Marketplace.  

| Typ oferty | Typ oferty |  Wytyczne techniczne |  
|:--- |:--- |:--- |  
| [Lista](#list) | [Usługi konsultingowe](#requirements-azure-marketplace-list-consulting-service) | [](#) |  
| [Lista](#list) | [Przesyłać do mnie](#requirements-azure-marketplace-list-contact-me) | [](#) |  
| [Wersja próbna](#trial) | [Bezpłatna wersja próbna / wersja próbna SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) | [](#) |  
| [Wersja próbna](#trial) | [Pokaz interaktywny](#requirements-azure-marketplace-trial-interactive-demo) | [](#) |  
| [Wersja próbna](#trial) | [Przetestuj](#requirements-azure-marketplace-trial-test-drive) | [](#) |  
| [Transact](#transact) | [Aplikacje platformy Azure: zarządzanej aplikacji](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  [docs.microsoft.com/Azure/Managed-Applications/overview](https://docs.microsoft.com/azure/managed-applications/overview) |  
| [Transact](#transact) | [Aplikacje platformy Azure: szablon rozwiązania](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  [](#) |  
| [Transact](#transact) | [Kontener](#requirements-azure-marketplace-transact-container) |  [](#) |  
| [Transact](#transact) | [Aplikacja SaaS](#requirements-azure-marketplace-transact-saas-app) |  [](#) |  
| [Transact](#transact) | [Maszyny wirtualne](#requirements-azure-marketplace-transact-virtual-machine) |  [](#) |  

---   

## <a name="non-transact-listings"></a>Transact list  
W tej sekcji opisano wszystkie oferty, które nie korzystają z Transact typ oferty. 

### <a name="list"></a>List  
Lista Typ oferty zawiera następujące typy oferta na sklepy w witrynie marketplace.  

| Typ oferty | Witryna sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Usługi konsultingowe | AppSource | [Wymagania dotyczące: AppSource: lista: usługi doradcze](#requirements-appsource-list-consulting-service) |  
| Usługi konsultingowe | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: lista: usługi doradcze](#requirements-azure-marketplace-list-consulting-service) |  
| Kontakt ze mną | AppSource | [](#) |  
| Kontakt ze mną | Azure Marketplace | [Wymagania dotyczące: AppSource: lista: przesyłać do mnie](#requirements-azure-marketplace-list-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Wymagania dotyczące: AppSource: lista: konsultacji usługi  
Gdy oferta obejmuje głównie profesjonalne usługi, na przykład: ocen, implementacji, zakładów, użyj usługi konsultingowe oferują typu.  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Właściwości oferty usługi | Usługi konsultingowe musi spełniać następujące kryteria.<ul> <li>Dostarczanie zaangażowania ustalony zakres, czas, stała cena (lub wolne).</li> <li>Orientacji przede wszystkim dotyczące przedsprzedaży.</li> <li>Ograniczenie do jednego odbiorcy.</li> <li>Należy przeprowadzić w lokacji.</li> </ul> |  
| Partner wymagania dotyczące usługi konsultingowe | Spełniają kryteria w obszarze odpowiednie dla Twojej usługi.<table><tr><th>Obszar rozwiązania</th><th>Kryteria</th></tr><tr><td>Dynamics 365 naboru klienta</td><td>Ma kompetencja Silver lub Gold zarządzanie relacjami z chmury.</td></tr><tr><td>Dynamics 365 Finance oraz w operacjach, Enterprise edition</td><td>W końcu w ciągu 12 miesięcy $ 25 000 lub więcej, należy mieć kompetencja Silver lub Gold planowania zasobów przedsiębiorstwa i przychody z operacji chmury.</td></tr><tr><td>Dynamics 365 Finance oraz w operacjach, Business edition</td><td>Służyć jako dostawcy usługi w chmurze (CSP) lub cyfrowe partnera rekordu (DPOR) dla jednego lub więcej klientów.</td></tr><tr><td>Power BI</td><td>Spełniają kryteria rozwiązań partnerskich.</td></tr><tr><td>PowerApps</td><td>Ma rozwiązania pokazy partnera.</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat zarządzania relacjami z klientami, odwiedź stronę chmury Customer Relationship Management strony znajduje się w [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Aby uzyskać więcej informacji o planowaniu zasobów, odwiedź stronę planowania zasobów przedsiębiorstwa strony znajduje się w [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Aby uzyskać więcej informacji o dostawcy usług Kryptograficznych, można znaleźć dostawcy usług w chmurze strony znajduje się w [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Aby uzyskać więcej informacji na temat DPOR odwiedź cyfrowe partnera Partner of Record i skojarzenie partnera strony znajduje się w [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Aby uzyskać więcej informacji na temat rozwiązania partnerów kryteriów odwiedź Partner — Przegląd rozwiązania i specjalne oferty promocyjne dokument w lokalizacji [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Aby uzyskać więcej informacji na temat pokazy partnera, odwiedź stronę pokazy partnera strony znajduje się w [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Wymagania dotyczące: Azure Marketplace: lista: konsultacji usługi  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Właściwości oferty usługi | Usługi konsultingowe musi spełniać następujące kryteria.<ul> <li>Dostarczanie zaangażowania ustalony zakres, czas, stała cena (lub wolne).</li> <li>Orientacji przede wszystkim dotyczące przedsprzedaży.</li> <li>Ograniczenie do jednego odbiorcy.</li> <li>Należy przeprowadzić w lokacji.</li> </ul> |  
| Partner wymagania dotyczące usługi konsultingowe | Musi mieć silver lub złota w jednym z następujących uprawnień w obszarze odpowiednie dla Twojej usługi. <table><tr><th>Obszar rozwiązania</th><th>Kompetencja</th></tr><td>Infrastruktura i platformy w chmurze</td><td>Platforma chmury<br />Centrum danych</td><tr><td>Tworzenie aplikacji i niezależnego dostawcy oprogramowania</td><td>Opracowywanie aplikacji<br />Integracja aplikacji<br />DevOps</td></tr><tr><td>Zarządzanie danymi i analiza</td><td>Analiza danych<br />Platforma danych</td></tr></table><ul> <li>Aby uzyskać więcej informacji dotyczących uprawnień, odwiedź stronę umiejętności za pośrednictwem sieci Microsoft Partner Network strony znajduje się w [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Aby uzyskać więcej informacji o liście, odwiedź witrynę Azure Marketplace usługi konsultingowe strony znajduje się w [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

#### <a name="requirements-azure-marketplace-list-contact-me"></a>Wymagania dotyczące: Azure Marketplace: lista: przesyłać do mnie  
Użyj kontaktu mnie oferują typ w przypadku korzystania z wersji próbnej lub transact typ oferty nie jest możliwe. Zaletą kontaktu mnie oferują typu jest konieczna może bezpośrednio odbierać potencjalnych klientów z rozwiązanie na rynek. Potencjalnych klientów należy nurtured do podstawowych transakcji można uruchomić Twojej zamachowym biznesowych. Wadą jest to, że zaangażowanie klientów jest ograniczona, w porównaniu z innych typów ofert.  

>[!IMPORTANT]
>Najlepiej z typów ofert wersji próbnej i Transact jest zaangażowanie klientów. Wartość skontaktować się ze mną typ oferty jest potencjalnego klienta, który pojawi się. Jeśli wybierzesz kontaktu mnie typ oferty, następnie należy sprawdzić, czy skonfigurowano folderu docelowego realizacji i wszystko jest gotowe do zmaksymalizowania Twojej realizacji.  

---   

### <a name="trial"></a>Wersja próbna  
Udostępnia środowisko wersji próbnej powoduje podwyższenie poziomu zaangażowania udostępniane klientom. Poziom zwiększenie zaangażowania powoduje, że bardziej rozbudowane narażenia do rozwiązania. Typ wersja próbna umożliwia klientom Poznaj rozwiązania przed zakupem. W wersji próbnej, typ oferty masz szansę wyższej podwyższania poziomu w sklepy. Należy również oczekiwać większej i bardziej rozbudowane potencjalnych klientów z promujących zaangażowanie klienta.  

Oferty za pomocą wersji próbnej, typ oferty lub aplikacji, na których jest wdrażany środowisko wersji próbnej i subskrypcji platformy Azure. Innych typów będzie można wdrożyć w środowisku lub subskrypcji platformy Azure z klienta. Oferty lub aplikacja powinna być doprowadziły bez dodatkowych wymagań kupić klienta. Oferty lub aplikacji powinien być minimalny, dodatkowego skonfigurowania, aby ukończyć przypadek użycia. Oferty lub aplikacji musi zawierać bezpłatna pomoc techniczna co najmniej na czas trwania okresu próbnego. Klientów należy nurtured i monitorowane w ścieżce oceny zamierzonego celu uzyskania najlepszych wyników. Zachęcamy do użycia potencjalnych klientów w witrynie marketplace i z własną analizy w aplikacji. Potencjalnych klientów należy monitorować i zarządzać nimi klientów.  

Wersja próbna, typ oferty zawiera następujące typy oferta na sklepy w witrynie marketplace.  

| Typ oferty | Witryna sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Bezpłatne / wersja próbna SaaS | AppSource | [Lista wymagań dotyczących typu: wersja próbna](#listing-type-requirements-trial) |  
| Bezpłatne / wersja próbna SaaS | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: wersji próbnej: bezpłatnej wersji próbnej / wersja próbna SaaS](#requirements-azure-marketplace-trial-free-trial-/-saas-trial) |  
| Interaktywna demonstracja | AppSource | [Lista wymagań dotyczących typu: wersja próbna](#listing-type-requirements-trial) |  
| Interaktywna demonstracja | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: wersja próbna: pokaz interaktywny](#requirements-azure-marketplace-trial-interactive-demo) |  
| Wersja testowa | AppSource | [Lista wymagań dotyczących typu: wersja próbna](#listing-type-requirements-trial) |  
| Wersja testowa | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: wersja próbna: przetestuj](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Wymagania dotyczące: Azure Marketplace: wersja próbna  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Bezpłatny okres próbny i środowisko wersji próbnej | Klient spróbować aplikacji bezpłatnie przez ograniczony czas.<br /><br />Klient nie jest wymagane nieuiszczenie opłat żadnych licencji lub subskrypcji dla danej oferty. Klienci nie są wymagane zapłacenia podstawowej produktu firmy Microsoft lub usługi. Wszystkie opcje wersji próbnej są wdrażane do subskrypcji platformy Azure. Masz wyłączną kontrolę nad optymalizację kosztów i zarządzania.<br /><br />Możesz wybrać bezpłatnej wersji próbnej, interaktywny pokaz lub przetestuj. Niezależnie od tego, możesz wybrać bezpłatny okres próbny podać klienta wstępnie ustawionymi ilość czasu, aby spróbować ofertę bez ponoszenia dodatkowych kosztów.<ul> <li>Aby rozpocząć proces tworzenia dysku testu, Wyślij wiadomość e-mail do [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Uwaga: Wszystkie SaaS próbnych w witrynie Azure Marketplace muszą zezwalać na klientowi pracy poświadczenia użyte do logowania.<ul> <li>Aby uzyskać więcej informacji, odwiedź stronę AppSource środowiska wersji próbnej znajdującej się na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Można łatwo konfigurować, gotowe do użycia rozwiązanie | Aplikacja musi być łatwo i szybko do skonfigurowania i. |  
| Dostępność / czas działania | Platformie lub aplikacja SaaS musi mieć przestojów co najmniej 99,9%. |  
| Usługa Azure Active Directory | Ofertę musi dopuszczać usługi Azure Active Directory (Azure AD) federacyjnych rejestracji jednokrotnej (SSO) (logowanie Jednokrotne federacyjnych usługi Azure AD), za zgodą włączone. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Wymagania dotyczące: Azure Marketplace: wersji próbnej: bezpłatnej wersji próbnej / wersja próbna SaaS  
Podaj rozwiązanie lub aplikacji przy użyciu wolnego do try, oprogramowanie jako usługa (SaaS) — na podstawie wersji próbnej. SaaS wersji próbnej oferują dyski typu wysokiej jakości potencjalnych klientów z zainteresowanych klientów, możesz uruchomić zamachowym Twojego biznesowych. Bezpłatna wersja próbna typu mogą być przedstawiane jako konto próbne ograniczonej lub czas trwania ograniczone. Bezpłatna wersja próbna powinna zawierać wywołanie akcji przyspieszenia konwersji na płatną Użyj rozwiązania.  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi spróbuj produkt przed kupować przy użyciu zautomatyzowaną metodę do konwertowania na użycie. Umożliwia również dowody koncepcji dla klienta i wspólnego interakcji użytkowników z zespoły firmy Microsoft. | Rozwiązania jest maszyny wirtualnej lub Azure aplikacji: typ oferty szablon rozwiązania.<br /><br />Rozwiązania jest SaaS oferują typ i podaj wielodostępnym produktów SaaS.<br /><br />Masz pierwszego uruchomienia można pobrać klienta szybko rozpocząć pracę.<br /><br />Mają pojedynczej dzierżawy, ale są dodawania klientów jako goście. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Wymagania dotyczące: Azure Marketplace: wersja próbna: pokaz interaktywny  
Użytkownik otrzymuje niezbędne instrukcje rozwiązania jest zapewnianie klientom przy użyciu interaktywnych pokaz. Zaletą pokaz interaktywny typu oferty jest udostępnić środowisko wersji próbnej bez skomplikowane inicjowania obsługi złożonych rozwiązań. Typ oferty pokaz interaktywny zapewnia klienta wygląd wokół rozwiązania. Typ oferty pokaz interaktywny zapewnia potencjalnych klientów, które powinny Wypracuj podstawowych transakcji można uruchomić Twojej zamachowym biznesowych.  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientom Zobacz rozwiązania w akcji bez złożoności konfigurowania. | Rozwiązanie wymaga złożonych ustawień, które są trudne do osiągnięcia w okresie próbnym. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Wymagania dotyczące: Azure Marketplace: wersja próbna: przetestuj  
Możesz wdrożyć co najmniej jednej maszyny wirtualnej za pomocą infrastructure-as-a-service(IaaS) lub aplikacji SaaS. Zaletą typu oferty dysku testu jest automatyczne Inicjowanie obsługi maszyny wirtualnej lub całego rozwiązania przez u partnera samouczek. Dysk testu umożliwia ocenę bez ponoszenia dodatkowych kosztów do klienta. Klienta nie musi być prowadzi istniejącego klienta Azure w celu zapewnienia wyższej jakości.  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi spróbuj produkt przed zakupem.<br /><br />Zapewnia obsługę z przewodnikiem rozwiązania za pomocą wstępnie skonfigurowanych ustawień.<br /><br />Poniżej przedstawiono dodatkowe korzyści, korzystając z dysku testu.<ul> <li>% 27 wyszukiwań użytkownika w witrynie marketplace są wprowadzono ulepszenia przez klientów do tylko oferty pokazu napędów testu.</li> <li>Oferty napędów testu Generowanie potencjalnych klientów 38% więcej niż oferty bez.</li> <li>% 36 nowego klienta nabyć w witrynie marketplace pochodzi z klientów, którzy trwało dysku testu.</li> <li>Dyski testu zapewniają Microsoft pola sprzedawców lepiej zrozumieć produktu dla wysiłków wspólnej sprzedaży.</li> </ul> | Rozwiązania jest maszyną wirtualną, aplikacje platformy Azure: szablon rozwiązania, aplikacji SaaS przy użyciu pojedynczej dzierżawy, lub jest skomplikowane, aby udostępnić. <br /><br />Nie masz metodę konwersji okresu próbnego do oferty płatnej. |  

---

## <a name="transact-specific-listings"></a>Transact specyficzne dla listy
W tej sekcji opisano wszystkie oferty, korzystających z Transact typ oferty.

### <a name="transact"></a>Transakcja  
Typ oferty Transact zawiera następujące typy oferta na sklepy w witrynie marketplace.  

| Typ oferty | Witryna sklepu | Szczegóły |   
|:---        |:---        | :--- |  
| Aplikacje platformy Azure: zarządzanej aplikacji | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: aplikacje platformy Azure: zarządzanej aplikacji](#requirements-azure-marketplace-transact-azure-apps-managed-app) |  
| Aplikacje platformy Azure: szablon rozwiązania | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: aplikacje platformy Azure: szablon rozwiązania](#requirements-azure-marketplace-transact-azure-apps-solution-template) |  
| Containers | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: kontenera](#requirements-azure-marketplace-transact-container) |  
| Aplikacja SaaS  | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: aplikacji SaaS](#requirements-azure-marketplace-transact-saas-app) |  
| Maszyna wirtualna | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: maszyny wirtualne](#requirements-azure-marketplace-transact-virtual-machine) |  

#### <a name="requirements-azure-marketplace-transact-azure-apps-managed-app"></a>Wymagania dotyczące: Azure Marketplace: Transact: aplikacje platformy Azure: zarządzanej aplikacji  
Przy użyciu aplikacji Azure: zarządzanego typu oferty aplikacji, gdy wymagane są następujące warunki.  
*   Możesz wdrożyć albo subskrypcji rozwiązanie dla klienta przy użyciu maszyny Wirtualnej lub całego rozwiązania opartego na IaaS.  
*   Użytkownik lub klienta wymaga się, że rozwiązanie jest zarządzana przez partnera.  

    >[!NOTE]
    >Na przykład partnera może być SI lub dostawcą usługi zarządzanej (MSP).  
  
*   Lista często zadawane pytania można znaleźć w często zadawanych pytań Marketplace strony znajduje się w [azure.microsoft.com/marketplace/faq](https://azure.microsoft.com/marketplace/faq).  

>[!NOTE]
> Zarządzane aplikacje muszą być możliwe za pośrednictwem portalu Marketplace. Jeśli komunikacji z klientami jest istotny, a następnie użytkownik powinien dotrzeć do klientów zainteresowanych po włączeniu udostępniania realizacji.  

#### <a name="requirements-azure-marketplace-transact-azure-apps-solution-template"></a>Wymagania dotyczące: Azure Marketplace: Transact: aplikacje platformy Azure: szablon rozwiązania  
Przy użyciu aplikacji Azure: rozwiązanie typu oferty szablonu gdy rozwiązanie wymaga dodatkowej automatyzacji wdrażania i konfigurowania poza proste maszyny Wirtualnej. Mogą zautomatyzować Inicjowanie obsługi administracyjnej przynajmniej jednej maszyny wirtualnej za pomocą aplikacji Azure: rozwiązanie szablonów. Może również udostępniać zasoby sieciowe oraz magazynu. Aplikacje platformy Azure: rozwiązanie szablonów oferty, typ zapewnia korzyści automatyzacji dla jednej maszyny wirtualne i całe rozwiązań IaaS.  

#### <a name="requirements-azure-marketplace-transact-container"></a>Wymagania dotyczące: Azure Marketplace: Transact: kontenera  
Kontener typu oferty używać rozwiązania jest obrazem kontenera Docker udostępnione jako usługa na podstawie Kubernetes kontenera platformy Azure.

>[!NOTE]
>Na przykład usługi kontenera platformy Azure na podstawie Kubernetes, takich jak usługa Azure Kubernetes lub wystąpień kontenera platformy Azure, wybór Azure klientów na potrzeby obsługi na podstawie Kubernetes kontenera.  

Firma Microsoft obsługuje obecnie bezpłatne i przełączyć your właścicielem licencji (BYOL) modeli licencjonowania. Więcej opcji commerce zaplanowane w następnych kilku miesięcy, w tym na podstawie zużycia opcje rozliczeń i subskrypcji.  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiarów | Obsługuje albo wolnego lub BYOL modelu rozliczeń. |  
| Zbudowany z plik Dockerfile obrazu | Kontener obrazów muszą być oparte na specyfikacji obrazu Docker i muszą być zbudowane z plik Dockerfile.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia obrazów docker można znaleźć w sekcji użycia znajduje się w [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting w ACR | Kontener obrazów muszą być obsługiwane w repozytorium Azure kontenera rejestru (ACR).<ul> <li>Aby uzyskać więcej informacji na temat pracy z ACR, odwiedź stronę Szybki Start: tworzenie za pomocą strony portalu Azure w lokalizacji rejestru kontenera [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Znakowanie obrazu | Obrazy kontenera musi zawierać co najmniej 1 znaczników (tagów maksymalna: 16).<ul> <li>Aby uzyskać więcej informacji na temat znakowanie obrazu, odwiedź stronę tag docker znajdujący się w [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  


#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Wymagania dotyczące: Azure Marketplace: Transact: aplikacji SaaS  
Użyj typu oferty aplikacji SaaS, aby włączyć klientowi kupić rozwiązania SaaS, techniczne jako subskrypcji. Muszą być spełnione następujące wymagania dla aplikacji SaaS.  
*   Cena i rachunku usługi szybkością prosty, miesięczne.  
*   Udostępnia metody do uaktualniania lub Anuluj usługę w dowolnym momencie.  

Microsoft hostuje transakcję commerce. Microsoft rachunków klienta w Twoim imieniu. Aby użyć aplikacji SaaS w postaci subskrypcji, należy włączyć możesz interfejsu API usługi zarządzania własnej subskrypcji. Interfejs API usługi zarządzania subskrypcji muszą komunikować się bezpośrednio z interfejsów API usługi Azure Resource Manager. Interfejs API usługi zarządzania subskrypcji musi obsługiwać usługi inicjowania obsługi, uaktualniania i zostanie anulowana.  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
|Rozliczenia i pomiarów | Ofertę kosztuje szybkością miesięczne płaski. Opartej na użyciu cennik i możliwości opartej na użyciu "wartość true w górę" nie są obsługiwane w tej chwili. |  
|Anulowanie | Twoja oferta jest można anulować przez klienta w dowolnym momencie. |  
|Strona początkowa transakcji | Możesz udostępniać Azure markami transakcji strony docelowej, gdzie użytkownicy mogą tworzyć i zarządzania swoim kontem usługi SaaS. |   
| Subskrypcja interfejsu API | Naraża usługi, które mogą kontaktować się z subskrypcją SaaS na tworzenie, aktualizowanie i usuwanie planu usługi oraz konta użytkownika. Krytyczne zmiany interfejsu API musi być obsługiwane w ciągu 24 godzin. Niekrytyczny zmiany interfejsu API zostanie wydana okresowo. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Wymagania dotyczące: Azure Marketplace: Transact: maszyny wirtualne  
Użyj typu oferty maszyny wirtualnej, podczas wdrażania urządzenia wirtualnego do subskrypcji skojarzonych z klientem. Maszyny wirtualne są w pełni commerce włączyć za pomocą płatność za rzeczywiste użycie lub Bring-your właścicielem — licencji (BYOL) modeli licencjonowania. Firma Microsoft hostuje transakcję commerce i rachunków klienta w Twoim imieniu. Możesz skorzystać z zalet przy użyciu relacji preferowanych płatności od klienta oraz firmy Microsoft, w tym umowy Enterprise.  

>[!NOTE]
>W tej chwili można było używać względem Azure użycia maszyny wirtualnej, ale nie dla oprogramowania licencjonowania opłaty są zobowiązania pieniężnego skojarzony z umową Enterprise.  

>[!NOTE]
>Mogą ograniczyć odnajdywanie i wdrażanie maszyny wirtualnej do określonej grupy odbiorców wyświetlania obrazu i cenach jako prywatnych oferty. Prywatne oferty odblokować przez tworzenie ofertach dla najbliżej klientów i oferuje dostosowane oprogramowanie oraz postanowienia. Niestandardowe warunki umożliwiają Wyróżnij różnych scenariuszy, w tym doprowadziły pola zajmuje specjalne ceny i warunki także wczesny dostęp do ograniczona wersja oprogramowania. Prywatne oferty Włącz można udzielić określonych cenach lub produktów, które mają ograniczony zestaw klientów przez utworzenie nowej jednostki SKU z tych informacji.  
*   Aby uzyskać więcej informacji o ofertach prywatne, odwiedź oferty prywatnego na stronie znajdujący się w portalu Azure Marketplace [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiarów | Maszyna wirtualna musi obsługiwać BYOL lub płatność za rzeczywiste użycie miesięcznych rozliczeń. |  
| Azure zgodnego wirtualnego dysku twardego (VHD) | Maszyny wirtualne muszą zostać skompilowane na systemu Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Linux, odwiedź stronę tworzenia dysku VHD Azure zgodnego sekcji (opartych na systemie Linux) znajduje się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Windows, odwiedź stronę tworzenia dysku VHD Azure zgodnego sekcji (z systemem Windows) znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Kolejne kroki
*   Odwiedź stronę [AppSource wydawcy przewodnik i portalu Azure Marketplace](./marketplace-publishers-guide.md) strony.  
 
---  
