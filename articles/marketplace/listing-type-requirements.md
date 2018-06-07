---
title: Wymagania dotyczące poszczególnych typ oferty | Azure
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
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: dbb14854f00fb133c3604a1dd529d42120371fd2
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825256"
---
# <a name="requirements-by-listing-type"></a>Wymagania dotyczące poszczególnych typ oferty  
Wymagania techniczne i marketingu zawartości jest zależna od sklepu, typu oferty i typ oferty. Przejrzyj następujące specyfikacje można sprawdzić zgodności.  
1. Wymagania dotyczące Sklepu:  
    *   [AppSource](#storefront-requirements:-appSource)  
    *   [Azure Marketplace](#storefront-requirements:-azure-marketplace)  
2. Wyświetlanie typów i wymagania typu oferty:  
    *   Aby uzyskać więcej informacji na temat typów i typów oferty, odwiedź określić typ wyświetlania dla strony rozwiązania pod adresem [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Wymagania dotyczące Sklepu: AppSource  
W poniższej tabeli opisano wymagania wymagań wstępnych do publikowania na AppSource.  
| Wymaganie | Szczegóły | Wymagane czy Zalecane |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Aplikacji musi umożliwić usługi Azure Active Directory federacyjne logowanie jednokrotne (SSO federacyjnych usługi Azure AD) z zgody włączone.<ul> <li>Dla więcej informacji na temat włączania usługi Azure AD federacyjnego logowania jednokrotnego, odwiedź stronę Konfigurowanie logowania jednokrotnego do aplikacji, które nie znajdują się w usłudze Azure Active Directory strony galerii aplikacji znajduje się w [docs.microsoft.com/azure/active-directory/ Active-directory--niestandardowe — aplikacji saas](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Wymagane |   
| ***Integracja z usługami w chmurze firmy Microsoft*** | Aplikację należy zintegrować z innych usług Microsoft Cloud, takich jak usługi Microsoft Power BI, Cortana Intelligence lub Microsoft Azure.<ul> <li>Przykład usługi Microsoft Cloud to Internetu rzeczy.</li> </ul> | Zalecane |  
| ***grupy odbiorców*** | Aplikacja musi być dla użytkowników z biznesowych i właściciele firm. | Wymagane | 
| ***Oprogramowanie jako usługa (SaaS) aplikację dla firm*** | Aplikacja musi spełniać następujące wymagania.<ul> <li>Aplikacja SaaS — biznesowych</li> <li>Proces biznesowy fokus</li> <li>Przeznaczone dla firm</li> <li>Użytkownicy mogą korzystać z poświadczeń służbowych do logowania, takie jak nazwa użytkownika i hasło</li> </ul> | Wymagane |  
| ***Bezpłatny okres próbny i środowisko wersji próbnej*** | Aplikacja musi zawierać jedną następujące opcje w kolejności, klient może używać aplikacji bezpłatnie przez ograniczony czas.<ul> <li>Podaj `try` metody, więc klienci mogą uruchomić wersji próbnej aplikacji w ramach AppSource</li> <li>Podaj `request trial` opcji w AppSource, więc klienci mogą zażądać wersji próbnej wersji aplikacji</li> </ul>Bezpłatna wersja próbna, który podasz musi zaoferować klientowi wstępnie ustawionymi ilość czasu na wypróbowanie aplikacji bez dodatkowych kosztów. | Wymagane |  
| ***Można łatwo konfigurować, gotowe do użycia rozwiązanie*** | Aplikacja musi być łatwo i szybko do skonfigurowania i z dostosowanie nie jest wymagane. | Wymagane |  
| ***Zarządzanie potencjalnymi klientami*** | Należy włączyć CRM, Zaakceptuj realizacji danych, zanim otrzymasz prowadzi ze sklepu.<ul> <li>Przykłady CRM są Marketo, Microsoft Dynamics lub Salesforce</li> </ul> | Wymagane |  
| ***Zasady zachowania poufności informacji i warunki użytkowania*** | Aplikację należy podać link do strony zasad ochrony prywatności przy użyciu publicznego adresu URL. Należy podać warunków użytkowania podczas publikowania jako tekst. | Wymagane |  
| ***Pomoc techniczna*** | Aplikację należy podać link do sieci przy użyciu publiczny adres URL strony pomocy technicznej. Aplikacji w przypadku korzystania z wersji próbnej, następnie zachodzi potrzeba obsługi bez ponoszenia dodatkowych kosztów podczas okresu próbnego. | Wymagane |  

## <a name="storefront-requirements-azure-marketplace"></a>Sklepu wymagania: Azure Marketplace  
Poniżej przedstawiono wymagania wymagań wstępnych do wyświetlania listy typów w portalu Azure Marketplace.  
| Wymaganie | Szczegóły | Typ oferty |  
|:--- |:--- |:--- |  
| ***Zasady udziału*** | Aplikacji należy wykonać zasady uczestnictwo w programie Azure Marketplace.<ul> <li>Aby uzyskać więcej informacji na temat zasad udziału można znaleźć zasady uczestnictwo w programie Azure Marketplace strony znajduje się w [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | lista<br />Transact<br />wersja próbna |  
| ***Integracja z programem Microsoft*** | Ofertę należy użyć dysku lub Rozszerz typów usług Microsoft Azure, takich jak obliczeniowych, sieci lub magazynu. Ofertę powinno odpowiadać do istniejącej kategorii portalu Azure Marketplace, takie jak bazy danych, zabezpieczeń lub sieci.<ul> <li>Aby uzyskać więcej informacji na temat ofert witryny Marketplace, odwiedź stronę Apps Marketplace strony znajduje się w [azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps).</li> </ul> | lista<br />Transact<br />wersja próbna |  
| ***grupy odbiorców*** | Ofertę musi być dla specjalistów IT, deweloperów chmury lub innych ról technicznej klienta. | lista<br />Transact<br />wersja próbna |  
| ***Zarządzanie potencjalnymi klientami*** | Należy włączyć programu CRM (Marketo, Microsoft Dynamics lub Salesforce) do akceptowania realizacji danych, zanim otrzymasz prowadzi ze sklepu. | lista<br />Transact<br />wersja próbna |  
| ***Zasady zachowania poufności informacji i warunki użytkowania*** | Aplikację należy podać link do strony zasad ochrony prywatności przy użyciu publicznego adresu URL. Należy podać warunków użytkowania podczas publikowania jako tekst. | lista<br />Transact<br />wersja próbna |  
| ***Pomoc techniczna*** | Ofertę podaj łącze do strony pomocy technicznej klienta przy użyciu publicznego adresu URL. Ofertę w przypadku korzystania z wersji próbnej, następnie zachodzi potrzeba obsługi bez ponoszenia dodatkowych kosztów podczas okresu próbnego. | Transact<br />wersja próbna |    

## <a name="non-transact-listings"></a>Transact list  
W tej sekcji opisano wszystkie typy oferty, które nie korzystają z Transact typ oferty. 

### <a name="list"></a>List  
Lista Typ oferty zawiera następujące typy oferta na sklepy w witrynie marketplace.  

| Typ oferty | Witryna sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Usługi konsultingowe | AppSource | [Wymagania dotyczące: AppSource: lista: usługi doradcze](#requirements:-appsource:-list:-consulting-services) |  
| Usługi konsultingowe | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: lista: usługi doradcze](#requirements:-azure-marketplace:-list:-consulting-services) |  
| Kontakt ze mną | AppSource | [](#) |  
| Kontakt ze mną | Azure Marketplace | [Wymagania dotyczące: AppSource: lista: przesyłać do mnie](#requirements:-azure-marketplace:-list:-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>Wymagania dotyczące: AppSource: lista: konsultacji usługi  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Właściwości oferty usługi | Usługi konsultingowe musi spełniać następujące kryteria.<ul> <li>Dostarczanie zaangażowania ustalony zakres, czas, stała cena (lub wolne).</li> <li>Orientacji przede wszystkim dotyczące przedsprzedaży.</li> <li>Ograniczenie do jednego odbiorcy.</li> <li>Należy przeprowadzić w lokacji.</li> </ul> |  
| Partner wymagania dotyczące usługi konsultingowe | Spełniają kryteria w obszarze odpowiednie dla Twojej usługi.<table><tr><th>Obszar rozwiązania</th><th>Kryteria</th></tr><tr><td>Dynamics 365 naboru klienta</td><td>Ma kompetencja Silver lub Gold zarządzanie relacjami z chmury.</td></tr><tr><td>Dynamics 365 Finance oraz w operacjach, Enterprise edition</td><td>W końcu w ciągu 12 miesięcy $ 25 000 lub więcej, należy mieć kompetencja Silver lub Gold planowania zasobów przedsiębiorstwa i przychody z operacji chmury.</td></tr><tr><td>Dynamics 365 Finance oraz w operacjach, Business edition</td><td>Służyć jako dostawcy usługi w chmurze (CSP) lub cyfrowe partnera rekordu (DPOR) dla jednego lub więcej klientów.</td></tr><tr><td>Power BI</td><td>Spełniają kryteria rozwiązań partnerskich.</td></tr><tr><td>PowerApps</td><td>Ma rozwiązania pokazy partnera.</td></tr></table><ul> <li>Aby uzyskać więcej informacji na temat zarządzania relacjami z klientami, odwiedź stronę chmury Customer Relationship Management strony znajduje się w [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Aby uzyskać więcej informacji o planowaniu zasobów, odwiedź stronę planowania zasobów przedsiębiorstwa strony znajduje się w [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Aby uzyskać więcej informacji o dostawcy usług Kryptograficznych, można znaleźć dostawcy usług w chmurze strony znajduje się w [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider).</li> <li>Aby uzyskać więcej informacji na temat DPOR odwiedź cyfrowe partnera Partner of Record i skojarzenie partnera strony znajduje się w [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Aby uzyskać więcej informacji na temat rozwiązania partnerów kryteriów odwiedź Partner — Przegląd rozwiązania i specjalne oferty promocyjne dokument w lokalizacji [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx? SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf).</li> <li>Aby uzyskać więcej informacji na temat pokazy partnera, odwiedź stronę pokazy partnera strony znajduje się w [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Wymagania dotyczące: Azure Marketplace: lista: konsultacji usługi  

| Wymagania | Szczegóły |  
|:--- |:--- |  
| Właściwości oferty usługi | Usługi konsultingowe musi spełniać następujące kryteria.<ul> <li>Dostarczanie zaangażowania ustalony zakres, czas, stała cena (lub wolne).</li> <li>Orientacji przede wszystkim dotyczące przedsprzedaży.</li> <li>Ograniczenie do jednego odbiorcy.</li> <li>Należy przeprowadzić w lokacji.</li> </ul> |  
| Partner wymagania dotyczące usługi konsultingowe | Musi mieć silver lub złota w jednym z następujących uprawnień w obszarze odpowiednie dla Twojej usługi. <table><tr><th>Obszar rozwiązania</th><th>Kompetencja</th></tr><td>Infrastruktura i platformy w chmurze</td><td>Platforma chmury<br />Centrum danych</td><tr><td>Tworzenie aplikacji i niezależnego dostawcy oprogramowania</td><td>Opracowywanie aplikacji<br />Integracja aplikacji<br />DevOps</td></tr><tr><td>Zarządzanie danymi i analiza</td><td>Analiza danych<br />Platforma danych</td></tr></table><ul> <li>Aby uzyskać więcej informacji dotyczących uprawnień, odwiedź stronę umiejętności za pośrednictwem sieci Microsoft Partner Network strony znajduje się w [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Aby uzyskać więcej informacji o liście, odwiedź witrynę Azure Marketplace usługi konsultingowe strony znajduje się w [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>Wersja próbna  

| Typ oferty | Witryna sklepu | Szczegóły |  
|:---        |:---        |:---     |  
| Bezpłatne / wersja próbna SaaS | AppSource | [Lista wymagań dotyczących typu: wersja próbna](#listing-type-requirements:-trial) |  
| Bezpłatne / wersja próbna SaaS | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: wersji próbnej: bezpłatnej wersji próbnej / wersja próbna SaaS](#requirements:-azure-marketplace:-trial:-free-trial-/-saas-trial) |  
| Interaktywna demonstracja | AppSource | [Lista wymagań dotyczących typu: wersja próbna](#listing-type-requirements:-trial) |  
| Interaktywna demonstracja | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: wersja próbna: pokaz interaktywny](#requirements:-azure-marketplace:-trial:-interactive-demo) |  
| Wersja testowa | AppSource | [Lista wymagań dotyczących typu: wersja próbna](#listing-type-requirements:-trial) |  
| Wersja testowa | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: wersja próbna: przetestuj](#requirements:-azure-marketplace:-trial:-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Wymagania dotyczące: Azure Marketplace: wersja próbna  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Bezpłatny okres próbny i środowisko wersji próbnej | Klient może używać aplikacji bezpłatnie przez ograniczony czas.<br /><br />Klient nie jest wymagane nieuiszczenie opłat żadnych licencji lub subskrypcji dla aplikacji lub oferty. Klient nie jest wymagane do opłacać podstawowej produktu firmy Microsoft lub usługi. Wszystkie opcje wersji próbnej są wdrażane do subskrypcji platformy Azure. Masz wyłączną kontrolę wersji próbnej optymalizację kosztów i zarządzania.<br /><br />Możesz wybrać bezpłatnej wersji próbnej, interaktywny pokaz lub przetestuj. Niezależnie od tego, możesz wybrać bezpłatny okres próbny musi zaoferować klientowi wstępnie ustawionymi ilość czasu na wypróbowanie aplikacji bez ponoszenia dodatkowych kosztów.<ul> <li>Aby rozpocząć proces tworzenia dysku testu, Wyślij wiadomość e-mail do [ amp-testdrive@microsoft.com ](mailto:amp-testdrive@microsoft.com).</li> </ul>Uwaga: Środowiska wersji próbnej platformy Azure Marketplace SaaS musi umożliwiają klientom pracy poświadczenia użyte do logowania.<ul> <li>Aby uzyskać więcej informacji, odwiedź stronę AppSource środowiska wersji próbnej znajdującej się na [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Można łatwo konfigurować, gotowe do użycia rozwiązanie | Aplikacja musi być łatwo i szybko do skonfigurowania i. |  
| Dostępność / czas działania | Platformie lub aplikacja SaaS musi mieć przestojów co najmniej 99,9%. |  
| Usługa Azure Active Directory | Ofertę musi dopuszczać usługi Azure Active Directory (Azure AD) federacyjnych rejestracji jednokrotnej (SSO) (logowanie Jednokrotne federacyjnych usługi Azure AD), za zgodą włączone. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Wymagania dotyczące: Azure Marketplace: wersji próbnej: bezpłatnej wersji próbnej / wersja próbna SaaS  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi spróbuj produkt przed kupować przy użyciu zautomatyzowaną metodę do konwertowania na użycie. Umożliwia również dowody koncepcji dla klienta i wspólnego interakcji użytkowników z zespoły firmy Microsoft. | Rozwiązania jest maszyną wirtualną lub szablon rozwiązania.<br /><br />Rozwiązania jest SaaS oferty i oferują wielodostępnym produktów SaaS.<br /><br />Masz pierwszego uruchomienia do uruchomienia klienta i szybko uruchomić.<br /><br />Masz pojedynczej dzierżawy, ale są dodawania klientów jako gości. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Wymagania dotyczące: Azure Marketplace: wersja próbna: pokaz interaktywny  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientom Zobacz rozwiązania w akcji bez złożoności konfigurowania. | Rozwiązanie wymaga złożonych ustawień, które są trudne do osiągnięcia w okresie próbnym. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Wymagania dotyczące: Azure Marketplace: wersja próbna: przetestuj  

| Korzyść | Wymaganie |  
|:--- |:--- |  
| Umożliwia klientowi spróbuj produkt przed zakupu.<br /><br />Zapewnia obsługę z przewodnikiem rozwiązania za pomocą wstępnie skonfigurowanych ustawień.<br /><br />Poniżej przedstawiono dodatkowe korzyści, korzystając z dysku testu.<ul> <li>% 27 wyszukiwań użytkownika w witrynie marketplace są wprowadzono ulepszenia przez użytkowników tylko oferty pokazu napędów testu.</li> <li>Oferty napędów testu Generowanie potencjalnych klientów 38% więcej niż oferty bez.</li> <li>% 36 nowego klienta nabyć w witrynie marketplace pochodzi od klientów, które miały dysku testu.</li> <li>Dyski testu zapewniają Microsoft pola sprzedawców lepiej zrozumieć produktu dla wysiłków wspólnej sprzedaży.</li> </ul> | Rozwiązania jest maszyna wirtualna, szablon rozwiązania lub aplikacji SaaS przy użyciu pojedynczej dzierżawy lub jest skomplikowane, aby udostępnić. <br /><br />Nie masz metodę konwersji okresu próbnego do oferty płatnej. |  

---

## <a name="transact-specific-listings"></a>Transact specyficzne dla listy

### <a name="transact"></a>Transakcja  

| Typ oferty | Witryna sklepu | Szczegóły |   
|:---        |:---        | :--- |  
| Aplikacje platformy Azure: zarządzanej aplikacji | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: aplikacje platformy Azure: zarządzanej aplikacji](#requirements:-azure-marketplace:-transact:-azure-apps:-managed-app) |  
| Aplikacje platformy Azure: szablon rozwiązania | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: aplikacje platformy Azure: szablon rozwiązania](#requirements:-azure-marketplace:-transact:-azure-apps:-solution-template) |  
| Containers | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: kontenera](#requirements:-azure-marketplace:-transact:-container) |  
| Aplikacja SaaS  | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: aplikacji SaaS](#requirements:-azure-marketplace:-transact:-saas-app) |  
| Maszyna wirtualna | Azure Marketplace | [Wymagania dotyczące: Azure Marketplace: Transact: maszyny wirtualne](#requirements:-azure-marketplace:-transact:-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Wymagania dotyczące: Azure Marketplace: Transact: kontenera  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiarów | Obsługuje albo wolnego lub BYOL modelu rozliczeń. |  
| Docker na podstawie obrazu | Kontener obrazu musi być oparta na format obrazu Docker i muszą być pobierane z rejestrów kontenera platformy Azure. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Wymagania dotyczące: Azure Marketplace: Transact: aplikacji SaaS  

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiarów | Ofertę kosztuje szybkością miesięczne płaski. Opartej na użyciu cenową i opartej na użyciu *true-up* opcje nie są obsługiwane w tej chwili. |  
| Anulowanie | Twoja oferta jest można anulować przez klienta w dowolnym momencie. |  
| Strona początkowa transakcji | Host strony docelowej Azure markami transakcji. Strony docelowej umożliwia klientom tworzenie i zarządzanie nimi konta usługi SaaS. |  
| Subskrypcja SaaS interfejsu API | Udostępnianie usługi, która współdziała z subskrypcją SaaS na tworzenie, aktualizowanie i usuwanie planu usługi oraz konta użytkownika. Wszystkie krytyczne zmiany interfejsu API musi być obsługiwany w ciągu 24 godzin. Zmiany interfejsu API niekrytyczne są okresowo aktualizowane. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Wymagania dotyczące: Azure Marketplace: Transact: maszyny wirtualne  

| Wymaganie | Szczegóły |  
|:--- |:--- | 
| Rozliczenia i pomiarów | Maszyna wirtualna musi obsługiwać BYOL lub płatność za rzeczywiste użycie miesięcznych rozliczeń. |  
| Azure zgodnego wirtualnego dysku twardego (VHD) | Maszyny wirtualne muszą zostać skompilowane na systemu Windows lub Linux.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Linux, odwiedź stronę tworzenia dysku VHD Azure zgodnego sekcji (opartych na systemie Linux) znajduje się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2- Create-an-Azure-Compatible-VHD-Linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Aby uzyskać więcej informacji na temat tworzenia dysku VHD systemu Windows, odwiedź stronę tworzenia dysku VHD Azure zgodnego sekcji (z systemem Windows) znajdujący się w [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3- Create-an-Azure-Compatible-VHD-Windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul> |  

## <a name="next-steps"></a>Kolejne kroki
*   Odwiedź stronę [AppSource wydawcy przewodnik i portalu Azure Marketplace](./marketplace-publishers-guide.md) strony.  
 
---  
