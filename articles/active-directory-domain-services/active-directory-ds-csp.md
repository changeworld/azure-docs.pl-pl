---
title: Dostawców rozwiązań w chmurze Azure Active Directory Domain Services dla platformy Azure | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory Domain Services dla dostawców rozwiązań w chmurze platformy Azure.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: ergreenl
ms.openlocfilehash: 8beba4f66cf24a937eec77e4bfdee2057b417269
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892810"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Usługi domenowe Azure Active Directory (AD) dla dostawców rozwiązań w chmurze platformy Azure (CSP)
W tym artykule wyjaśniono, jak można użyć usług domenowych Azure AD w ramach subskrypcji Azure CSP.

## <a name="overview-of-azure-csp"></a>Omówienie programu Azure CSP
Azure CSP jest programem for Partners firmy Microsoft i udostępnia kanał licencji dla różnych usług w chmurze firmy Microsoft. Usługa Azure CSP umożliwia partnerom Zarządzanie sprzedaży, relacjami związanymi z rozliczeniami i zapewnienia pomocy technicznej i rozliczeń oraz być jedynym punktem kontaktu klienta. Ponadto Azure CSP zapewnia pełny zestaw funkcji wspomagających, w tym portal samoobsługowy i towarzyszące interfejsy API. Te narzędzia Włącz partnerzy programu CSP łatwo aprowizować i zarządzać zasobami platformy Azure i obsługę rozliczeń dla klientów i ich subskrypcjami.

[Portalu Centrum partnerskiego](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) działa jako punkt wejścia dla wszystkich partnerów Azure CSP. Zapewnia możliwości zarządzania sformatowanego klientów i automatyczne przetwarzania. Partnerzy portalu Azure CSP skorzystać z możliwości Centrum partnerskiego przy użyciu interfejsu użytkownika opartego na sieci web lub za pomocą programu PowerShell i różnych wywołań interfejsu API.

Na poniższym diagramie przedstawiono, jak działa model dostawcy usług Kryptograficznych na wysokim poziomie. Firma Contoso ma katalog usługi Azure AD Active Directory. Mają one nawiązanie partnerstwa z firmą dostawcy usług Kryptograficznych, który wdraża i zarządza zasobami w ramach swojej subskrypcji Azure CSP. Firma Contoso może mieć również regularne (bezpośredni) subskrypcji platformy Azure, które są rozliczane bezpośrednio do firmy Contoso.

![Omówienie modelu dostawca usług Kryptograficznych](./media/csp/csp_model_overview.png)

Dzierżawa partner programu CSP ma trzy grupy specjalne agent - Admin agentów, agenci pomocy technicznej i agenci sprzedaży. Grupa agentów administratora jest przypisana do roli administrator dzierżawy w katalogu usługi Azure AD firmy Contoso. W rezultacie użytkownika należącego do grupy agentów administratorów partner programu CSP ma uprawnienia administratora dzierżawy w katalogu usługi Azure AD firmy Contoso. Gdy przepisy partner programu CSP subskrypcji Azure CSP dla Contoso i ich grupy agentów administratora jest przypisany do roli właściciela dla tej subskrypcji. W rezultacie Agent administratora partner programu CSP ma wymaganych uprawnień do aprowizowania zasobów platformy Azure, takie jak maszyny wirtualne, sieci wirtualnych i usług domenowych Azure AD w imieniu firmy Contoso.

Aby uzyskać więcej informacji, zobacz [Omówienie programu Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Korzyści z używania usługi Azure AD Domain Services w ramach subskrypcji Azure CSP
Azure AD Domain Services zapewnia zgodnych usług AD systemu Windows Server na platformie Azure, takich jak LDAP, uwierzytelnianie Kerberos/NTLM, przyłączanie do domeny, zasady grupy i DNS. Za pośrednictwem dekady wiele aplikacji zostały skompilowane do pracy w usłudze AD za pomocą tych funkcji. Wiele niezależnych dostawców oprogramowania (ISV) utworzone i wdrożone aplikacje w siedzibach klientów. Te aplikacje są uciążliwe do obsługi, ponieważ wymaga często dostęp do różnych środowisk, w których te aplikacje są wdrażane. Subskrypcji Azure CSP zapewniają prostszą alternatywę dzięki skali i elastyczności platformy Azure.

Usługi domenowe Azure AD obsługuje teraz subskrypcji Azure CSP. Teraz można wdrożyć aplikację w ramach subskrypcji Azure CSP związany z klienta usługi Azure AD directory. W rezultacie pracowników (działu pomocy technicznej) można zarządzać, administrowania i usługi maszyny wirtualne, na których aplikacja jest wdrażana przy użyciu poświadczeń firmowych w Twojej organizacji. Ponadto można udostępnić domeny zarządzanej usług domenowych Azure AD, dla katalogu usługi Azure AD Twojego klienta. Aplikacja jest podłączony do domeny zarządzanej Twojego klienta. W związku z tym, możliwości aplikacji korzystających z protokołu Kerberos/NTLM, LDAP, lub [System.DirectoryServices API](/dotnet/api/system.directoryservices) działają bezproblemowo względem katalogu Twojego klienta. Klientów końcowych osiągają znaczne korzyści z używania aplikacji jako usługi, bez konieczności martwienia się o konserwacja infrastruktury, w których aplikacja jest wdrażana na.

Wszystkie rozliczeń dla zasobów platformy Azure, wykorzystane w tej subskrypcji, łącznie z usług domenowych Azure AD jest rozliczany według do Ciebie. To Ty masz pełną kontrolę nad relacji z klientem Jeśli chodzi o sprzedaży, rozliczeń, pomoc techniczną itp. Dzięki elastyczności platformy Azure CSP, mały zespół obsługuje agentów może obsłużyć wielu klientów, którzy mają wystąpień aplikacji wdrożone.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modele wdrażania programu CSP dla usług domenowych Azure AD
Istnieją dwa sposoby, w których można użyć usług domenowych Azure AD przy użyciu subskrypcji Azure CSP. Wybierz właściwy, w oparciu o zagadnienia dotyczące zabezpieczeń i prostotę, których klienci mają.

### <a name="direct-deployment-model"></a>Bezpośrednie wdrażanie modelu
W tym modelu wdrażania usługi Azure AD Domain Services jest włączona w ramach sieci wirtualnej należącymi do subskrypcji Azure CSP. Agent administratora partner programu CSP ma następujące uprawnienia:
* Uprawnienia administratora globalnego w katalogu usługi Azure AD przez klienta.
* Uprawnienia właściciela subskrypcji w ramach subskrypcji Azure CSP.

![Bezpośrednie wdrażanie modelu](./media/csp/csp_direct_deployment_model.png)

W tym modelu wdrażania agentów administrator dostawcy CSP mogą administrować tożsamości dla klienta. Agenci administratora mają możliwość aprowizowanie nowych użytkowników, grup, należy dodać aplikacje w ramach klienta usługi Azure AD directory itp. Ten model wdrażania może być odpowiednie dla mniejszych organizacji, które nie mają administratora dedykowanych tożsamość lub na potrzeby partner programu CSP w celu administrowania tożsamości w ich imieniu.


### <a name="peered-deployment-model"></a>Model wdrażania równorzędnych
W tym modelu wdrażania usługi Azure AD Domain Services jest włączona w ramach sieci wirtualnych należących do klienta — oznacza to, bezpośrednie subskrypcji platformy Azure, opłacony przez klienta. Partner programu CSP, następnie można wdrożyć aplikacje w ramach sieci wirtualnej należących do klienta subskrypcji dostawcy CSP. Sieci wirtualne mogą następnie połączone za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure. W rezultacie obciążeń aplikacje wdrożone przez partner programu CSP w taki sposób, w ramach subskrypcji Azure CSP może nawiązać domena zarządzana przez klienta aprowizowane w bezpośrednich subskrypcji platformy Azure przez klienta.

![Model wdrażania równorzędnych](./media/csp/csp_peered_deployment_model.png)

Ten model wdrażania rozdzielenie uprawnień i umożliwia agentów pomocy technicznej partner programu CSP na Zarządzanie subskrypcją platformy Azure i wdrażanie zasobów i zarządzanie nimi w nim. Jednak agentów pomocy technicznej partner programu CSP ma potrzeby mieć uprawnienia administratora globalnego dla katalogu usługi Azure AD przez klienta. Administratorzy tożsamości klienta można kontynuować zarządzanie tożsamościami dla swojej organizacji.

Ten model wdrażania mogą być dostosowane do scenariuszy, w którym ISV (niezależny dostawca oprogramowania) zapewnia hostowany wersję swojej aplikacji w środowisku lokalnym i również musi połączyć się z klientem przez usługi AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrowanie usługą Azure AD Domain Services zarządzanych domen w subskrypcji dostawcy CSP
Następujące ważne kwestie podczas administrowania domeny zarządzanej, w ramach subskrypcji Azure CSP:

* **Agenci administrator dostawcy usług Kryptograficznych można udostępnić domeny zarządzanej przy użyciu swoich poświadczeń:** Usługi domenowe Azure AD obsługuje subskrypcji Azure CSP. W związku z tym użytkownicy należący do grupy agentów administratorów partner programu CSP może aprowizować nowe domeny zarządzanej usług domenowych Azure AD.

* **Dostawcy usług kryptograficznych utworzyć skrypt tworzenia nowych domen zarządzanych dla swoich klientów przy użyciu programu PowerShell:** Zobacz [jak włączyć usługę Azure AD Domain Services przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md) Aby uzyskać szczegółowe informacje.

* **Agenci administrator dostawcy usług Kryptograficznych nie można wykonać zadania ciągłego zarządzania w domenie zarządzanej przy użyciu swoich poświadczeń:** Użytkownicy Administratorzy dostawcy usług Kryptograficznych nie można wykonać zadania zarządzania procedury w ramach domeny zarządzanej przy użyciu swoich poświadczeń. Ci użytkownicy są zewnętrzne w stosunku do katalogu usługi Azure AD przez klienta i ich poświadczenia nie są dostępne w katalogu usługi Azure AD przez klienta. W związku z tym usług domenowych Azure AD nie ma dostępu do skróty haseł Kerberos i NTLM dla tych użytkowników. W wyniku tych użytkowników nie można uwierzytelnić w domenach usług domenowych Azure AD zarządzane.

  > [!WARNING]
  > **Należy utworzyć konto użytkownika w katalogu klienta do wykonywania bieżących zadań administracyjnych w domenie zarządzanej.**
  > Nie możesz się zarejestrować do domeny zarządzanej przy użyciu poświadczeń użytkownika Administrator dostawcy usług Kryptograficznych. Aby to zrobić, należy użyć poświadczeń konta użytkownika należącego do katalogu usługi Azure AD przez klienta. Potrzebujesz tych poświadczeń dla zadań, takich jak przyłączanie maszyn wirtualnych do domeny zarządzanej, administrowanie systemem DNS, administrowanie itp zasad grupy.
  >

* **Konto użytkownika utworzone dla administracją muszą być dodane do grupy "Administratorzy usługi AAD DC":** Grupy "Administratorzy usługi AAD DC" ma uprawnienia do wykonywania określonych zadań jednocześnie na administrację delegowaną w domenie zarządzanej. Te zadania obejmują konfigurowanie usługi DNS, tworzenie jednostki organizacyjnej, administrowanie zasadami grupy itp. Aby partner programu CSP, można wykonywać takie zadania w domenie zarządzanej konto użytkownika musi można utworzyć katalogu usługi Azure AD przez klienta. Poświadczenia dla tego konta muszą zostać udostępnione z agentami administratora partner programu CSP. Ponadto to konto użytkownika musi dodać do grupy "Administratorzy usługi AAD DC", aby włączyć zadania konfiguracji w domenie zarządzanej do wykonania za pomocą tego konta użytkownika.


## <a name="next-steps"></a>Kolejne kroki
* [Zarejestruj się w programie Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) i rozpocząć tworzenie swojej firmy dzięki dostawcy usług Kryptograficznych platformy Azure.
* Przejrzyj listę [usług platformy Azure, które są dostępne w programie Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Włączanie usługi Azure AD Domain Services przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md)
* [Wprowadzenie do usług Azure AD Domain Services](active-directory-ds-getting-started.md)
