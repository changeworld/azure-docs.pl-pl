---
title: Publikowanie aplikacji w oddzielnych sieciach za pośrednictwem grup łączników — Azure AD
description: Obejmuje sposób tworzenia grup łączników i zarządzania nimi w usłudze Azure AD Application Proxy.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275562"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników

Klienci korzystają z serwera proxy aplikacji usługi Azure AD dla coraz większej liczby scenariuszy i aplikacji. Więc zrobiliśmy App Proxy jeszcze bardziej elastyczne, umożliwiając więcej topologii. Można utworzyć grupy łączników serwera proxy aplikacji, aby można było przypisać określone łączniki do obsługi określonych aplikacji. Ta funkcja zapewnia większą kontrolę i sposoby optymalizacji wdrożenia serwera proxy aplikacji.

Każdy łącznik serwera proxy aplikacji jest przypisany do grupy łączników. Wszystkie łączniki należące do tej samej grupy łączników działają jako oddzielne jednostki dla wysokiej dostępności i równoważenia obciążenia. Wszystkie łączniki należą do grupy łączników. Jeśli nie utworzysz grup, wszystkie łączniki znajdują się w grupie domyślnej. Administrator może tworzyć nowe grupy i przypisywać do nich łączniki w witrynie Azure Portal.

Wszystkie aplikacje są przypisane do grupy łączników. Jeśli nie utworzysz grup, wszystkie aplikacje zostaną przypisane do grupy domyślnej. Ale jeśli organizujesz łączniki w grupy, możesz ustawić każdą aplikację tak, aby działała z określoną grupą łączników. W takim przypadku tylko łączniki w tej grupie obsługują aplikację na żądanie. Ta funkcja jest przydatna, jeśli aplikacje są hostowane w różnych lokalizacjach. Można tworzyć grupy łączników na podstawie lokalizacji, dzięki czemu aplikacje są zawsze obsługiwane przez łączniki, które są fizycznie blisko nich.

> [!TIP]
> Jeśli masz duże wdrożenie serwera proxy aplikacji, nie przypisuj żadnych aplikacji do domyślnej grupy łączników. W ten sposób nowe łączniki nie odbierają żadnego ruchu na żywo, dopóki nie przypiszesz ich do aktywnej grupy łączników. Ta konfiguracja umożliwia również umieszczenie łączników w trybie bezczynności, przenosząc je z powrotem do grupy domyślnej, dzięki czemu można przeprowadzić konserwację bez wpływu na użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zgrupować łączniki, należy upewnić się, że [zainstalowano wiele łączników](application-proxy-add-on-premises-application.md). Po zainstalowaniu nowego łącznika automatycznie dołącza do **domyślnej** grupy łączników.

## <a name="create-connector-groups"></a>Tworzenie grup łączników

Wykonaj te kroki, aby utworzyć dowolną liczbę grup łączników.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz serwer**proxy aplikacji****aplikacji** >  **usługi Azure Active Directory** > Enterprise .
1. Wybierz **pozycję Nowa grupa łączników**. Pojawi się blok Nowa grupa łączników.

   ![Pokazuje ekran, aby wybrać nową grupę łączników](./media/application-proxy-connector-groups/new-group.png)

1. Nadaj nowej grupie łączników nazwę, a następnie użyj menu rozwijanego, aby wybrać łączniki należące do tej grupy.
1. Wybierz **pozycję Zapisz**.

## <a name="assign-applications-to-your-connector-groups"></a>Przypisywanie aplikacji do grup łączników

Wykonaj następujące kroki dla każdej aplikacji opublikowanej za pomocą serwera proxy aplikacji. Aplikację można przypisać do grupy łączników podczas pierwszego publikowania lub można użyć tych kroków, aby zmienić przypisanie w dowolnym momencie.

1. Z pulpitu nawigacyjnego zarządzania katalogu wybierz pozycję **Aplikacje** > przedsiębiorstwa**Wszystkie aplikacje** > aplikacji, którą chcesz przypisać do grupy łączników > **serwer proxy aplikacji**.
1. Użyj menu rozwijanego **Grupa łączników,** aby wybrać grupę, której aplikacja ma używać.
1. Wybierz **pozycję Zapisz,** aby zastosować zmianę.

## <a name="use-cases-for-connector-groups"></a>Przypadki użycia dla grup łączników

Grupy łączników są przydatne w różnych scenariuszach, w tym:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Lokacje z wieloma połączonymi centrami danych

Wiele organizacji ma wiele połączonych ze sobą centrów danych. W takim przypadku chcesz zachować jak najwięcej ruchu w centrum danych, jak to możliwe, ponieważ łącza między centrami danych są kosztowne i powolne. Łączniki można wdrażać w każdym centrum danych, aby obsługiwać tylko aplikacje, które znajdują się w centrum danych. Takie podejście minimalizuje łącza między centrami danych i zapewnia użytkownikom całkowicie przejrzyste środowisko.

### <a name="applications-installed-on-isolated-networks"></a>Aplikacje zainstalowane w sieciach izolowanych

Aplikacje mogą być hostowane w sieciach, które nie są częścią głównej sieci firmowej. Grup łączników służy do instalowania dedykowanych łączników w sieciach izolowanych, aby również izolować aplikacje do sieci. Zwykle dzieje się tak, gdy dostawca zewnętrzny utrzymuje określoną aplikację dla twojej organizacji.

Grupy łączników umożliwiają instalowanie łączników dedykowanych dla tych sieci, które publikują tylko określone aplikacje, co ułatwia i bezpiecznieje zleca zarządzanie aplikacjami dostawcom zewnętrznym.

### <a name="applications-installed-on-iaas"></a>Aplikacje zainstalowane w u. IaaS

W przypadku aplikacji zainstalowanych w usłudze IaaS w celu uzyskania dostępu do chmury grupy łączników zapewniają wspólną usługę zapewniającą dostęp do wszystkich aplikacji. Grupy łączników nie tworzą dodatkowej zależności od sieci firmowej ani nie fragmentuje środowiska aplikacji. Łączniki mogą być instalowane w każdym centrum danych w chmurze i obsługiwać tylko aplikacje, które znajdują się w tej sieci. Można zainstalować kilka łączników, aby osiągnąć wysoką dostępność.

Weźmy za przykład organizację, która ma kilka maszyn wirtualnych podłączonych do własnej sieci wirtualnej hostowanej przez IaaS. Aby umożliwić pracownikom korzystanie z tych aplikacji, te sieci prywatne są połączone z siecią firmową przy użyciu sieci VPN lokacja lokacja. Zapewnia to dobre doświadczenie dla pracowników, którzy znajdują się lokalnie. Ale może nie być idealny dla pracowników zdalnych, ponieważ wymaga dodatkowej infrastruktury lokalnej do kierowania dostępu, jak widać na poniższym diagramie:

![Diagram ilustruje sieć Usługi Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Za pomocą grup łączników serwera proxy aplikacji usługi Azure AD można włączyć wspólną usługę w celu zabezpieczenia dostępu do wszystkich aplikacji bez tworzenia dodatkowych zależności od sieci firmowej:

![Wielu dostawców chmury usługi Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Wielolesowe — różne grupy łączników dla każdego lasu

Większość klientów, którzy wdrożyli serwer proxy aplikacji, korzysta z możliwości logowania jednokrotnego (SSO) przez wykonywanie delegowania ograniczonego protokołu Kerberos (KCD). Aby to osiągnąć, maszyny łącznika muszą być przyłączone do domeny, która może delegować użytkowników do aplikacji. KCD obsługuje możliwości między lasami. Ale dla firm, które mają różne środowiska wielolesowe bez zaufania między nimi, jeden łącznik nie może być używany dla wszystkich lasów. 

W takim przypadku określone łączniki mogą być wdrażane na las i ustawione do obsługi aplikacji, które zostały opublikowane, aby obsługiwać tylko użytkowników tego określonego lasu. Każda grupa łączników reprezentuje inny las. Podczas gdy dzierżawa i większość środowiska jest ujednolicony dla wszystkich lasów, użytkownicy mogą być przypisane do swoich aplikacji lasu przy użyciu grup usługi Azure AD.

### <a name="disaster-recovery-sites"></a>Miejsca odzyskiwania po awarii

Istnieją dwa różne podejścia, które można podjąć w witrynie odzyskiwania po awarii (DR), w zależności od sposobu implementacji witryn:

* Jeśli witryna odzyskiwania po awarii jest zbudowana w trybie aktywnym i aktywnym, gdzie jest dokładnie taka sama jak lokacja główna i ma te same ustawienia sieciowe i ad, można utworzyć łączniki w lokacji odzyskiwania po awarii w tej samej grupie łączników co lokacja główna. Dzięki temu usługa Azure AD do wykrywania pracy awaryjnej dla Ciebie.
* Jeśli lokacja odzyskiwania po awarii jest oddzielona od lokacji głównej, można utworzyć inną grupę łączników w lokacji odzyskiwania po awarii, a 1) mieć aplikacje do tworzenia kopii zapasowych lub 2) ręcznie przekierować istniejącą aplikację do grupy łączników odzyskiwania po awarii, zgodnie z potrzebami.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Obsługa wielu firm z jednego dzierżawy

Istnieje wiele różnych sposobów implementowania modelu, w którym jeden dostawca usług wdraża i utrzymuje usługi związane z usługą Azure AD dla wielu firm. Grupy łączników pomagają administratorowi segregować łączniki i aplikacje na różne grupy. Jednym ze sposobów, który jest odpowiedni dla małych firm, jest posiadanie jednej dzierżawy usługi Azure AD, podczas gdy różne firmy mają własną nazwę domeny i sieci. Dotyczy to również scenariuszy I i sytuacji M&A, w których jeden dział IT obsługuje kilka firm ze względów regulacyjnych lub biznesowych.

## <a name="sample-configurations"></a>Przykładowe konfiguracje

Niektóre przykłady, które można zaimplementować, obejmują następujące grupy łączników.

### <a name="default-configuration--no-use-for-connector-groups"></a>Konfiguracja domyślna – brak użycia dla grup łączników

Jeśli nie używasz grup łączników, konfiguracja będzie wyglądać następująco:

![Przykładowe grupy łączników bez usługi Azure AD](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Ta konfiguracja jest wystarczająca dla małych wdrożeń i testów. Będzie również działać dobrze, jeśli organizacja ma płaską topologię sieci.

### <a name="default-configuration-and-an-isolated-network"></a>Konfiguracja domyślna i odizolowana sieć

Ta konfiguracja jest ewolucją domyślnej, w której istnieje określona aplikacja, która działa w izolowanej sieci, takiej jak sieć wirtualna IaaS:

![Przykład grupy łączników usługi Azure AD bez łączników i odizolowana sieć](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Zalecana konfiguracja – kilka określonych grup i domyślna grupa bezczynnych

Zalecana konfiguracja dla dużych i złożonych organizacji ma mieć domyślną grupę łączników jako grupę, która nie obsługuje żadnych aplikacji i jest używana dla bezczynnych lub nowo zainstalowanych łączników. Wszystkie aplikacje są obsługiwane przy użyciu niestandardowych grup łączników. Umożliwia to całą złożoność scenariuszy opisanych powyżej.

W poniższym przykładzie firma ma dwa centra danych, A i B, z dwoma łącznikami, które obsługują każdą lokację. Każda lokacja ma różne aplikacje, które działają na niej.

![Przykład firmy z 2 centrami danych i 2 złączami](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Następne kroki

* [Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
* [Włączanie logowania jednokrotnego](what-is-single-sign-on.md)
