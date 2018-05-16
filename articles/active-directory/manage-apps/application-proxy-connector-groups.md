---
title: Publikowanie aplikacji w odrębnych sieci i lokalizacje przy użyciu łącznika grup w serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Opisano sposób tworzenia i zarządzania grupami łączników w serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 50f17d7b833874cd15ddc9f2e4f93fa7af1700a2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikowanie aplikacji w odrębnych sieci i lokalizacje przy użyciu grup łącznika

Klienci korzystają z serwera Proxy aplikacji usługi Azure AD dla scenariuszy coraz więcej i aplikacji. Dlatego wprowadziliśmy serwera Proxy aplikacji jeszcze bardziej elastyczne przez włączenie więcej topologii. Można utworzyć grupy łącznika serwera Proxy aplikacji, dzięki czemu można przypisać określone łączniki do obsługi określonych aplikacji. Ta funkcja zapewnia więcej kontrolę i sposobów optymalizacji wdrożenia serwera Proxy aplikacji. 

Każdy łącznik serwera Proxy aplikacji jest przypisany do grupy łącznika. Wszystkie łączniki, które należą do tej samej grupy łącznik działa jako osobne jednostki dla wysokiej dostępności i równoważenia obciążenia. Wszystkie łączniki należą do grupy łącznika. Jeśli nie utworzono grup, wszystkie łączniki są grupy domyślnej. Administrator może tworzyć nowe grupy i przypisać im łączników w portalu Azure. 

Wszystkie aplikacje są przypisane do grupy łącznika. Jeśli nie utworzono grup, wszystkie aplikacje są przypisane do domyślnej grupy. Ale łączniki zorganizować w grupy, należy ustawić każdej aplikacji do pracy z grupą określony łącznik. W takim przypadku tylko łączników w tej grupie obsługi aplikacji na żądanie. Ta funkcja jest przydatna, jeśli aplikacji znajdują się w różnych lokalizacjach. Można utworzyć łącznika grup na podstawie lokalizacji, tak, aby aplikacje zawsze są obsługiwane przez łączniki, które są fizycznie bliski je.

>[!TIP] 
>Jeśli masz dużego wdrożenia serwera Proxy aplikacji nie przypisuj aplikacji do domyślnej grupy łącznika. Dzięki temu nowe łączniki nie odbierać ruchu na żywo, dopóki nie zostanie przypisana do grupy łącznika usługi active. Ta konfiguracja umożliwia również umieścić łączników w trybie bezczynności przez przeniesienie ich z powrotem do domyślnej grupy tak, aby można było wykonać konserwacji bez wywierania wpływu na użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne
Aby pogrupować łączników, należy upewnij się, że [zainstalować wiele łączników](application-proxy-enable.md). Po zainstalowaniu nowego łącznika automatycznie dołączeniu **domyślne** grupy łącznika.

## <a name="create-connector-groups"></a>Utwórz grupy dla łącznika
Wykonaj te kroki, aby utworzyć dowolną liczbę grup łącznika. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **serwera proxy aplikacji**.
2. Wybierz **nową grupę łącznika**. Zostanie wyświetlony blok nową grupę łącznika.

   ![Wybierz nową grupę łącznika](./media/application-proxy-connector-groups/new-group.png)

3. Nadaj nazwę nowej grupy łącznika, a następnie użyj menu rozwijanego, aby wybrać łączniki, które należą do tej grupy.
4. Wybierz pozycję **Zapisz**.

## <a name="assign-applications-to-your-connector-groups"></a>Przypisywanie aplikacji do grup łącznika
Wykonaj te czynności dla każdej aplikacji, która została już opublikowana przy użyciu serwera Proxy aplikacji. Najpierw opublikować lub korzystania z tych kroków, aby zmienić przypisanie w dowolnym momencie można przypisać aplikację do grupy łącznika.   

1. Na pulpicie nawigacyjnym zarządzania dla katalogu wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > aplikacji, którą chcesz przypisać grupie łącznika > **serwera Proxy aplikacji**.
2. Użyj **grupy łącznika** menu rozwijane, aby wybrać grupę, która będzie używać aplikacja.
3. Wybierz **zapisać** do zastosowania zmiany.

## <a name="use-cases-for-connector-groups"></a>Przypadki użycia grup łącznika 

Łącznik grupy są przydatne w przypadku różnych scenariuszy, w tym:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Lokacji z wieloma połączonych centrów danych

Organizacje często mają liczba połączonych centrów danych. W takim przypadku chcesz zachować tyle ruchu sieciowego w centrum danych jak to możliwe, ponieważ łącza między datacenter jest kosztowne i wolnego. Można wdrożyć łączników w każde centrum danych do obsługi tylko te aplikacje, które znajdują się w centrum danych. Takie podejście minimalizuje łącza między datacenter i zapewnia całkowicie przezroczysty środowisko dla użytkowników.

### <a name="applications-installed-on-isolated-networks"></a>Aplikacje zainstalowane w izolowanych sieciach

Aplikacje mogą być przechowywane w sieciach, które nie są częścią sieci firmowej głównego. Łącznik grupy służy do instalowania dedykowanego łączników w izolowanych sieciach również Izolowanie aplikacji sieci. Zazwyczaj dzieje się tak, gdy dostawców przechowuje określonej aplikacji w Twojej organizacji. 

Łącznik grupy umożliwiają zainstalować łączniki dedykowane dla tych sieci, które publikują tylko określone aplikacje, łatwiejsze i bezpieczniejsze do zewnętrznej obsługi zarządzania aplikacjami dla dostawców innych firm, co.

### <a name="applications-installed-on-iaas"></a>Aplikacje zainstalowane na IaaS 

Aplikacje zainstalowane na IaaS, aby uzyskać dostęp do chmury łącznik grup Podaj wspólne usługi bezpieczny dostęp do wszystkich aplikacji. Łącznik grup nie utworzyć dodatkowe zależności w sieci firmowej lub fragmentu środowisko aplikacji. Łączniki można zainstalować na każdym centrum danych w chmurze i służyć tylko te aplikacje, które znajdują się w tej sieci. Można zainstalować wiele łączników w celu uzyskania wysokiej dostępności.

Należy podjąć, na przykład czy organizacja ma kilka maszyny wirtualne podłączone do ich własnych IaaS hostowanych sieci wirtualnej. Aby umożliwić pracownikom korzystanie z tych aplikacji, tych sieci prywatnych są połączone z siecią firmową za pomocą sieci VPN typu lokacja lokacja. To zapewnia dobre dla pracowników znajdujących się w siedzibie firmy. Jednak go może nie być idealne w przypadku pracowników zdalnych, ponieważ wymaga infrastruktury lokalnej dodatkowe do rozsyłania dostępu, jak widać na poniższym diagramie:

![AzureAD sieci Iaas](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Za pomocą serwera Proxy aplikacji usługi Azure AD connector grup można włączyć wspólne usługi bezpieczny dostęp do wszystkich aplikacji bez tworzenia dodatkowe zależności w sieci firmowej:

![Dostawców chmury Iaas wielu AzureAD](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Łącznik różnych grup dla każdego lasu z obejmującego wiele lasów

Większość klientów, którzy wdrożyli serwera Proxy aplikacji korzystają z jego możliwości jednokrotnej (SSO), wykonując delegowanie ograniczone protokołu Kerberos (KCD). Aby to osiągnąć, łącznika urządzenia muszą być przyłączone do domeny, który można delegować użytkowników do aplikacji. Delegowanie KCD obsługuje możliwości między lasami. Jednak dla przedsiębiorstw, które mają różne środowiska obejmującego wiele lasów, bez zaufania między nimi, pojedynczy łącznik nie może służyć do wszystkich lasów. 

W takim przypadku określonych łączników można być wdrożone w każdym lesie i ustawiony służyć aplikacji, które zostały opublikowane do obsługi tylko użytkownicy tego określonego lasu. Każda grupa łącznika reprezentuje innego lasu. Gdy dzierżawcy i większość środowisko jest unified dla wszystkich lasów, użytkownicy mogą być przypisani do swoich aplikacji lasu za pomocą grup usługi Azure AD.
 
### <a name="disaster-recovery-sites"></a>Lokacjach odzyskiwania po awarii

Istnieją dwa różne podejścia, które można wykonać z lokacją odzyskiwanie po awarii, w zależności od sposobu implementacji witryn sieci:

* Jeśli witryna odzyskiwania po awarii jest wbudowana w trybie aktywny / aktywny, gdzie jest dokładnie takie same jak lokacji głównej i ma tej samej sieci i ustawienia usług AD, można utworzyć łączniki w lokacji odzyskiwania po awarii w tej samej grupie łącznika jako głównej witryny. Dzięki temu usługi Azure AD do wykrywania przechodzenia w tryb failover.
* Jeśli ta lokacja DR jest oddzielony od lokacji głównej, można utworzyć grupę inny łącznik w lokacji odzyskiwania po awarii, a albo 1) ma aplikacjom kopii zapasowych lub 2) ręcznie przekierowywać istniejącą aplikację do grupy łącznika odzyskiwania po awarii zgodnie z potrzebami.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Obsłużyć wielu firm z pojedynczej dzierżawy

Istnieje wiele różnych sposobów implementowania modelu, w którym pojedynczego usługodawcy wdraża i obsługuje usługę Azure AD związane z usługami dla wielu firm. Łącznik grup ułatwić administratorowi segregowanie łączników i aplikacji w różnych grupach. Jedną z metod, które jest odpowiednie dla małych firm, jest jednym usługi Azure AD dzierżawy podczas różne przedsiębiorstwa mają własne nazwy domeny i sieci. Dotyczy to również M i scenariusze i sytuacji jeśli pojedynczy dzielenia IT służy firm kilka przyczyn przepisami lub pracy. 

## <a name="sample-configurations"></a>Przykładowe konfiguracje

Przykłady, które można zaimplementować obejmują następujące grupy łącznika.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Domyślna konfiguracja — używana dla łącznika grup

Jeśli nie używasz grup łącznika, konfigurację będzie wyglądała następująco:

![AzureAD żadnych grup łącznika](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
Ta konfiguracja jest odpowiednia dla małych wdrożeń i testy. Może ona również działać prawidłowo, jeśli organizacja ma topologii siecią płaską.
 
### <a name="default-configuration-and-an-isolated-network"></a>Domyślna konfiguracja i sieci izolowanej

Ta konfiguracja jest ewolucji domyślny, w którym jest specyficzne dla aplikacji z systemem w sieci izolowanej takich jak IaaS sieci wirtualnej: 

![AzureAD żadnych grup łącznika](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Zalecana konfiguracja — kilku określonych grup i domyślnej grupy dla bezczynności

Zalecana konfiguracja w przypadku dużych i złożonych organizacji ma domyślnej grupy łącznika jako grupa nie obsługiwać wszystkich aplikacji, która jest używana dla łączników bezczynności lub nowo zainstalowany. Wszystkie aplikacje są obsługiwane przy użyciu grup niestandardowych łącznika. Dzięki temu złożoność opisanych powyżej scenariuszy.

W poniższym przykładzie firma ma dwoma centrami danych, A i B, z dwa łączniki, które pełnią każdej lokacji. Każda lokacja ma różne aplikacje, które działają na nim. 

![AzureAD żadnych grup łącznika](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Kolejne kroki

* [Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
* [Włączanie logowania jednokrotnego](application-proxy-single-sign-on.md)


