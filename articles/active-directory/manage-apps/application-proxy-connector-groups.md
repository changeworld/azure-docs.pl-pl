---
title: Publikowanie aplikacji w oddzielnych sieciach za pośrednictwem grup łączników — Azure AD
description: Opisano, jak utworzyć i zarządzać grupami łączników serwera Proxy aplikacji usługi Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275562"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników

Klientom korzystanie z serwera Proxy aplikacji usługi Azure AD dla coraz więcej scenariuszy i aplikacji. Dodaliśmy więc serwer Proxy aplikacji jeszcze bardziej elastyczne, należy włączyć więcej topologii. Można utworzyć grupy łączników serwera Proxy aplikacji, dzięki czemu możesz przypisać określone łączniki do obsługi określonych aplikacji. Ta funkcja udostępnia więcej kontroli i sposobów na optymalizację wdrożenia serwera Proxy aplikacji.

Każdy łącznik serwera Proxy aplikacji jest przypisany do grupy łączników. Wszystkie łączniki, które należą do tej samej grupie łącznik działa jako osobne jednostki o wysokiej dostępności i równoważenia obciążenia. Wszystkie łączniki należeć do grupy łączników. Jeśli nie utworzysz grupy, wszystkie łączniki są w grupie domyślnej. Administrator może tworzyć nowe grupy i przypisać im łączników w witrynie Azure portal.

Wszystkie aplikacje są przypisane do grupy łączników. Jeśli nie utworzysz grupy, wszystkie Twoje aplikacje są przypisane do domyślnej grupy. Ale organizujesz swoje łączniki do grup można ustawić każdej aplikacji do pracy z grupą określonego łącznika. W takim przypadku tylko łączników w tej grupie obsługiwać aplikacji na żądanie. Ta funkcja jest przydatna, jeśli aplikacje są hostowane w różnych lokalizacjach. Można utworzyć grupy łączników na podstawie lokalizacji, więc, że aplikacje są zawsze obsługiwane przez łączniki, które fizycznie znajdują się blisko ich.

> [!TIP]
> W przypadku dużego wdrożenia serwera Proxy aplikacji nie przypisuj aplikacji do domyślnej grupy łączników. W ten sposób nowe łączniki nie otrzymasz żadnych aktywny ruch, dopóki nie zostanie przypisana do grupy łącznika usługi active. Ta konfiguracja umożliwia również umieścić łączników w trybie bezczynności, przenosząc je z powrotem do domyślnej grupy, dzięki czemu można wykonać konserwacji bez wywierania wpływu na użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zgrupować łączniki, należy upewnić się, że [zainstalowano wiele łączników](application-proxy-add-on-premises-application.md). Po zainstalowaniu nowego łącznika automatycznie przyłączana jest **Domyślna** Grupa łączników.

## <a name="create-connector-groups"></a>Tworzenie grupy łączników

Użyj tych kroków można utworzyć dowolną liczbę grup łącznika.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **serwerze proxy aplikacji**.
1. Wybierz pozycję **Nowa grupa łączników**. Zostanie wyświetlony blok Nowa grupa łączników.

   ![Wyświetla ekran, aby wybrać nową grupę łączników](./media/application-proxy-connector-groups/new-group.png)

1. Nadaj nazwę nowej grupy łączników, a następnie użyj menu rozwijanego, aby wybrać, które łączniki należą do tej grupy.
1. Wybierz pozycję **Zapisz**.

## <a name="assign-applications-to-your-connector-groups"></a>Przypisywanie aplikacji do grup łącznika

Wykonaj następujące czynności dla każdej aplikacji, które zostały opublikowane za pomocą serwera Proxy aplikacji. Można przypisać aplikacji do grupy łączników, gdy najpierw opublikować lub można użyć następujące kroki, aby zmienić przypisanie zawsze wtedy, gdy chcesz.

1. Na pulpicie nawigacyjnym Zarządzanie katalogiem wybierz pozycję **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > aplikację, którą chcesz przypisać do grupy łączników > **serwerze proxy aplikacji**.
1. Użyj menu rozwijanego **Grupa łączników** , aby wybrać grupę, która ma być używana przez aplikację.
1. Wybierz pozycję **Zapisz** , aby zastosować zmianę.

## <a name="use-cases-for-connector-groups"></a>Zastosowań grupy łączników

Grupy łączników są przydatne w przypadku różnych scenariuszy, w tym:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Lokacje z wielu połączonych ze sobą centrach danych

W wielu organizacjach liczba połączonych centrów danych. W tym przypadku chcesz zachować tak dużej ilości ruchu sieciowego w centrum danych jak to możliwe, ponieważ linki krzyżowe datacenter są kosztowne i bardzo wolno. Możesz wdrożyć łączników w poszczególnych centrach danych, aby obsługiwać tylko te aplikacje, które znajdują się w centrum danych. To podejście minimalizuje linki do wielu centrów danych i zapewnia całkowicie przezroczysty dla użytkowników.

### <a name="applications-installed-on-isolated-networks"></a>Aplikacje zainstalowane w izolowanych sieciach

Aplikacje mogą być hostowane w sieciach, które nie są częścią głównego sieci firmowej. Aby zainstalować łączniki dedykowanych w izolowanych sieciach też Izolowanie aplikacji sieci, można użyć grupy łączników. Dzieje się tak zazwyczaj, gdy dostawców przechowuje konkretnej aplikacji dla Twojej organizacji.

Grupy łączników umożliwić zainstalowanie dedykowanych łączników tych sieci, które publikują tylko konkretnych aplikacji, co umożliwia łatwiejsze i bezpieczniejsze w celu oddelegowania procesów zarządzania aplikacjami dla dostawców innych firm.

### <a name="applications-installed-on-iaas"></a>Aplikacje zainstalowane w infrastrukturze IaaS

W przypadku aplikacji z zainstalowanym IaaS, aby uzyskać dostęp do chmury grupy łączników świadczyć usługę common bezpieczny dostęp do wszystkich aplikacji. Grupy łączników nie utworzyć dodatkowe zależności w sieci firmowej lub fragment środowisko aplikacji. Łączników można zainstalować na każdym centrum danych w chmurze i służą tylko te aplikacje, które znajdują się w tej sieci. Można zainstalować wiele łączników w celu uzyskania wysokiej dostępności.

Należy podjąć, na przykład, że organizacja, która ma kilka maszyny wirtualne podłączone do ich własnych IaaS hostowanych w sieci wirtualnej. Aby pracownicy mogli używać tych aplikacji, tych sieci prywatnych są połączone z siecią firmową za pomocą sieci VPN typu lokacja lokacja. Zapewnia to dobre środowisko dla pracowników, którzy są w środowisku lokalnym. Jednak może nie być idealne w przypadku pracowników zdalnych, ponieważ wymaga dodatkowych lokalnej infrastruktury do rozsyłania dostępu, jak pokazano na poniższym diagramie:

![Diagram przedstawiający sieć usługi Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Za pomocą grup łączników serwera Proxy aplikacji usługi Azure AD należy włączyć wspólne usługi bezpieczny dostęp do wszystkich aplikacji bez konieczności tworzenia dodatkowe zależności w sieci firmowej:

![Usługa Azure AD IaaS wielu dostawców chmury](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Grupy łączników różne dla każdego lasu z obejmującego wiele lasów

Większość klientów, które wdrożyły usługę serwera Proxy aplikacji korzystają z jego możliwości logowanie jednokrotne (SSO), wykonując delegowanie ograniczone protokołu Kerberos (KCD). Aby to osiągnąć, łącznik programu urządzenia muszą być przyłączone do domeny, które można delegować użytkowników do aplikacji. Ograniczonego delegowania protokołu Kerberos obsługuje funkcje między lasami. Jednak dla przedsiębiorstw, które mają różne środowisk wielu lasów, bez zaufania między nimi, pojedynczy łącznik nie może służyć do wszystkich lasów. 

W tym przypadku łączniki dla mogą być wdrożone w każdym lesie i ustaw do obsługi aplikacji, które zostały opublikowane ma obsługiwać tylko użytkowników określonych lasu. Każda grupa łączników reprezentuje innym lesie. Podczas gdy dzierżawy i większość środowiska jest jednolita dla wszystkich lasów, można przypisać użytkowników ze swoimi aplikacjami lasu za pomocą grup usługi Azure AD.

### <a name="disaster-recovery-sites"></a>Lokacjach odzyskiwania po awarii

Istnieją dwa różne podejścia, które można wykonać przy użyciu lokacji odzyskiwania po awarii, w zależności od sposobu implementacji Lokacje:

* Jeśli lokacji odzyskiwania po awarii jest wbudowana w trybie aktywny / aktywny, gdzie jest tak samo jak w lokacji głównej i ma tej samej sieci i ustawienia usług AD, można utworzyć łączniki w lokacji odzyskiwania po awarii w tej samej grupie łącznika lokacji głównej. Dzięki temu usługa Azure AD do wykrywania przejścia w tryb failover.
* Jeśli lokacja odzyskiwania po awarii jest oddzielony od lokacji głównej, można utworzyć grupę inny łącznik w lokacji odzyskiwania po awarii, a jedną (1) aplikacje kopii zapasowej lub 2) ręcznie przekierować istniejących aplikacji do grupy łączników odzyskiwania po awarii zgodnie z potrzebami.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Obsłużyć wiele firm z pojedynczej dzierżawy

Istnieje wiele różnych sposobów implementowania modelu, w którym dostawca pojedynczą usługę wdraża i obsługuje usługę Azure AD powiązane usługi dla wielu firm. Grupy łączników pomoc administratora oddzielenie czynności związanych z łączników i aplikacji w różnych grupach. Jednym ze sposobów, które jest odpowiednie dla małych firm, jest pojedynczej usłudze Azure AD dzierżawy podczas różnych firm mają własne nazwy domeny i sieci. Dotyczy to również M i scenariuszy i sytuacji jeśli pojedynczy dzielenia IT służy kilka przedsiębiorstw ze względów prawnych lub pracy.

## <a name="sample-configurations"></a>Przykładowe konfiguracje

Przykłady, które można zaimplementować: następujące grupy łączników.

### <a name="default-configuration--no-use-for-connector-groups"></a>Domyślna konfiguracja — nie na użytek grupy łączników

Jeśli nie używasz grupy łączników, konfigurację będzie wyglądać następująco:

![Przykładowa usługa Azure AD nie ma grup łączników](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Ta konfiguracja jest wystarczająca dla małych wdrożeń i testy. Może ona również działać dobrze w przypadku, jeśli organizacja dysponuje siecią płaską topologii.

### <a name="default-configuration-and-an-isolated-network"></a>Domyślna konfiguracja i sieci izolowanej

Ta konfiguracja jest unowocześnienia domyślna, w którym znajduje się określoną aplikację działającą w sieci izolowanej, takich jak sieć wirtualna IaaS:

![Przykładowa usługa Azure AD nie ma grup łączników i sieci izolowanej](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Zalecana konfiguracja — kilku określonych grup i grupę domyślną bezczynności (%)

Zalecana konfiguracja w przypadku dużych i złożonych organizacji jest domyślna grupa łączników jako grupa, nie obsługuje żadnych aplikacji, która jest używana do używania bezczynnych lub nowo zainstalowany łączników. Wszystkie aplikacje są obsługiwane przy użyciu grupy łączników niestandardowych. Dzięki temu złożoność scenariuszy opisanych powyżej.

W poniższym przykładzie firma ma dwoma centrami danych, A i B, za pomocą dwa łączniki, które obsługują każdej lokacji. Każda lokacja ma różne aplikacje, które w nich uruchamiane.

![Przykład firmy z 2 centrami danych i 2 łącznikami](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Następne kroki

* [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
* [Włączanie logowania jednokrotnego](what-is-single-sign-on.md)
