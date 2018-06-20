---
title: Dostawców rozwiązań w chmurze Azure Active Directory Domain Services dla platformy Azure | Dokumentacja firmy Microsoft
description: Usługi domenowe Azure Active Directory dla dostawców rozwiązań w chmurze Azure.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 362d7226434733ffeaa5be6f988afa4016a7c827
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213876"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Usług domenowych Azure Active Directory (AD) dla dostawców rozwiązań w chmurze Azure (CSP)
W tym artykule opisano, jak można użyć usług domenowych Azure AD w subskrypcji platformy Azure dostawcy usług Kryptograficznych.

## <a name="overview-of-azure-csp"></a>Omówienie usługi Azure dostawcy usług Kryptograficznych
Dostawcy usług w Chmurze Azure to program for Microsoft Partners i udostępnia kanał licencji dla różnych usług w chmurze firmy Microsoft. Dostawcy usług w Chmurze Azure umożliwia partnerom Zarządzanie sprzedaży, właścicielem rozliczeń relacji zapewniają obsługę rozliczeń i problemów technicznych i być klienta pojedynczy punkt kontaktu. Ponadto Azure CSP zapewnia pełny zestaw narzędzi, w tym portal samoobsługowy i towarzyszące interfejsów API. Te narzędzia włączenia dostawcy usług Kryptograficznych partnerom łatwe udostępniania i zarządzania zasobami Azure i podaj rozliczeń dla klientów i ich subskrypcji.

[Portalu Centrum partnerskiego](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) działa jako punkt wejścia dla wszystkich partnerów Azure dostawcy usług Kryptograficznych. Zapewnia klienta bogate możliwości zarządzania, automatyczne przetwarzanie i inne. Azure partnerów dostawcy usług Kryptograficznych skorzystać z możliwości Centrum partnerskiego przy użyciu interfejsu użytkownika sieci web lub za pomocą programu PowerShell i różnych wywołań interfejsu API.

Na poniższym diagramie przedstawiono, jak działa modelu dostawcy usług Kryptograficznych na wysokim poziomie. Firma Contoso ma usługi Azure AD Active Directory. Mają one powiązanie z dostawcy usług Kryptograficznych, który wdraża i zarządza zasobami w swoich subskrypcji Azure dostawcy usług Kryptograficznych. Contoso mogą mieć również regularne (bezpośredni) subskrypcji platformy Azure, które są rozliczane bezpośrednio do firmy Contoso.

![Omówienie modelu dostawcy usług Kryptograficznych](./media/csp/csp_model_overview.png)

Dzierżawy partnera dostawcy usług Kryptograficznych ma trzy grup specjalnych agent - Admin agentów, agenci techniczną oraz agentów sprzedaży. Grupa agentów administratora jest przypisana do roli administrator dzierżawy w katalogu usługi Azure AD firmy Contoso. W związku z tym użytkownik należący do grupy agentów admin partnera dostawcy usług Kryptograficznych ma uprawnienia administratora dzierżawy w katalogu usługi Azure AD firmy Contoso. Gdy przepisy partnera dostawcy usług Kryptograficznych subskrypcji platformy Azure dostawcy usług Kryptograficznych dla Contoso i ich grupy agentów administratora jest przypisany do roli właściciela dla tej subskrypcji. W związku z tym agentów admin partnera dostawcy usług Kryptograficznych ma wymaganych uprawnień do udostępniania zasobów platformy Azure, takich jak maszyn wirtualnych, sieci wirtualnych i usług domenowych Azure AD w imieniu firmy Contoso.

Aby uzyskać więcej informacji, zobacz [dostawcy usług w Chmurze Azure — omówienie](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Korzyści wynikające ze stosowania usług domenowych Azure AD w subskrypcji platformy Azure dostawcy usług Kryptograficznych
Usługi domenowe Azure AD zapewnia zgodne usług AD systemu Windows Server na platformie Azure, takie jak LDAP, uwierzytelnianie Kerberos/NTLM, przyłączanie do domeny, zasady grupy i DNS. Za pośrednictwem dekad wiele aplikacji skompilowano pracy przed AD za pomocą tych funkcji. Wiele niezależnych dostawców oprogramowania (ISV) utworzone i wdrożone aplikacje w swoich klientów lokalnych. Te aplikacje są uciążliwe do obsługi, ponieważ często wymagający dostępu w różnych środowiskach, w których te aplikacje są wdrażane. Z subskrypcją platformy Azure dostawcy usług Kryptograficznych masz prostsze zamiast z skalowalność i elastyczność Azure.

Usługi domenowe Azure AD obsługuje teraz subskrypcji Azure dostawcy usług Kryptograficznych. Teraz można wdrożyć aplikację w subskrypcji platformy Azure dostawcy usług Kryptograficznych związana z katalogu usługi Azure AD Twojego klienta. W związku z tym pracownikom (pracownikami działu pomocy technicznej) można zarządzać, zarządzania i obsługi maszyn wirtualnych, na których wdrażania aplikacji przy użyciu poświadczeń firmowych w Twojej organizacji. Ponadto można udostępnić domeny zarządzanej usług domenowych Azure AD dla katalogu usługi Azure AD Twojego klienta. Aplikacja jest podłączony do domeny zarządzanej klienta. W związku z tym te możliwości w aplikacji, które zależą od protokołu Kerberos/NTLM, LDAP, lub [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) pracy bezproblemowo względem katalogu klienta. Klienci zakończenia znacznie korzystać z używania aplikacji jako usługa, bez konieczności martwić się o zachowaniu infrastruktury, w których aplikacja jest wdrażana na.

Wszystkie rozliczeń dla platformy Azure zasoby w tej subskrypcji, w tym usługi domenowe Azure AD jest opłaty łączone zgodnie z powrotem. Możesz zachować pełną kontrolę nad relacji z klientem, jeśli chodzi o sprzedaży, rozliczeń, działem pomocy technicznej itp. Elastyczność platformy Azure dostawcy usług Kryptograficznych, mały zespół pomocy technicznej agentów może obsłużyć wielu klientów, którzy mają wystąpień aplikacji wdrożone.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modele wdrażania dostawcy usług Kryptograficznych dla usług domenowych Azure AD
Istnieją dwa sposoby, w których można używać usług domenowych Azure AD z subskrypcją platformy Azure dostawcy usług Kryptograficznych. Wybierz właściwy oparte na zagadnienia dotyczące zabezpieczeń i prostota, które ma klientów.

### <a name="direct-deployment-model"></a>Model wdrażania bezpośredniego
W tym modelu wdrażania usług domenowych Azure AD jest włączona w sieci wirtualnej należącymi do subskrypcji platformy Azure dostawcy usług Kryptograficznych. Agenci admin partnera dostawcy usług Kryptograficznych mają następujące uprawnienia:
* Uprawnienia administratora globalnego w katalogu usługi Azure AD przez klienta.
* Uprawnienia właściciela subskrypcji dla tej subskrypcji platformy Azure dostawcy usług Kryptograficznych.

![Model wdrażania bezpośredniego](./media/csp/csp_direct_deployment_model.png)

W tym modelu wdrażania agentów administrator dostawcy usług Kryptograficznych można administrować tożsamości klienta. Agenci administratora ma możliwość obsługi administracyjnej nowych użytkowników, grup, dodać aplikacje w katalogu usługi Azure AD klienta itp. Ten model wdrażania może być odpowiednie dla mniejszych organizacji, które nie poproś administratora o tożsamości dedykowanych lub wybrać dla partnerów dostawcy usług Kryptograficznych zarządzać tożsamościami w ich imieniu.


### <a name="peered-deployment-model"></a>Model wdrażania połączyć za pomocą
W tym modelu wdrażania usług domenowych Azure AD jest włączona w sieci wirtualnej należących do klienta — to znaczy bezpośredniego subskrypcji platformy Azure uregulowaniu płatności przez klienta. Partner dostawcy usług Kryptograficznych można następnie wdrożyć aplikacje w sieci wirtualnej należących do subskrypcji klienta dostawcy usług Kryptograficznych. Następnie można podłączyć sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure. W związku z tym obciążeń aplikacje wdrożone przez partnera dostawcy usług Kryptograficznych w ramach subskrypcji platformy Azure dostawcy usług Kryptograficznych mogą łączyć się klienta domeny zarządzanej udostępniane w subskrypcji platformy Azure bezpośrednio przez klienta.

![Model wdrażania połączyć za pomocą](./media/csp/csp_peered_deployment_model.png)

Ten model wdrażania rozdzielenie uprawnień i umożliwia partnera dostawcy usług Kryptograficznych agentów pomocy technicznej do zarządzania subskrypcją platformy Azure i wdrażania i zarządzania zasobami. Jednak agenci techniczną partnera dostawcy usług Kryptograficznych nie trzeba mieć uprawnienia administratora globalnego katalogu usługi Azure AD przez klienta. Administratorzy tożsamości klienta można kontynuować zarządzanie tożsamościami dla swojej organizacji.

Ten model wdrażania może być odpowiednie do scenariuszy, w którym niezależnego dostawcy oprogramowania (niezależnego dostawcy oprogramowania) zapewnia hostowany wersji ich aplikacji lokalnej, która również powinno zostać połączone z klienta do usługi AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrowanie usługami domenowymi Azure AD zarządzane domen w subskrypcjach dostawcy usług Kryptograficznych
Następujące istotne kwestie administrowanie domeną zarządzaną w subskrypcji platformy Azure dostawcy usług Kryptograficznych:

* **Agenci administrator dostawcy usług Kryptograficznych można obsługiwać przy użyciu swoich poświadczeń domeny zarządzanej:** usługi domenowe Azure AD obsługuje subskrypcji Azure dostawcy usług Kryptograficznych. W związku z tym użytkownicy należący do grupy agentów admin partnera dostawcy usług Kryptograficznych można udostępnić nowej domeny zarządzanej usług domenowych Azure AD.

* **Dostawcy usług kryptograficznych można w skrypcie tworzenia nowych domen zarządzanych dla swoich klientów przy użyciu programu PowerShell:** zobacz [sposób, aby włączyć domenowych Azure AD usług przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md) szczegółowe informacje.

* **Agenci administrator dostawcy usług Kryptograficznych nie można wykonać zadania bieżące zarządzanie na przy użyciu swoich poświadczeń domeny zarządzanej:** użytkownicy Administratorzy dostawcy usług Kryptograficznych nie może wykonywać zadania zarządzania rutynowych w obrębie domeny zarządzanej przy użyciu swoich poświadczeń. Ci użytkownicy są zewnętrzne względem katalogu usługi Azure AD przez klienta i ich poświadczenia nie są dostępne w katalogu usługi Azure AD przez klienta. W związku z tym usługi domenowe Azure AD nie ma dostępu do skrótów haseł protokołu Kerberos i NTLM dla tych użytkowników. W związku z tym takich użytkowników nie można uwierzytelnić w domenach zarządzanych usług domenowych Azure AD.

  > [!WARNING]
  > **Należy utworzyć konto użytkownika w katalogu klienta do wykonywania zadań administracją na domeny zarządzanej.**
  > Nie można Zaloguj się do domeny zarządzanej przy użyciu poświadczeń użytkownika Administrator dostawcy usług Kryptograficznych. Aby to zrobić, należy użyć poświadczeń konta użytkownika należącego do katalogu usługi Azure AD przez klienta. Te poświadczenia są wymagane dla zadań, takich jak przyłączanie maszyn wirtualnych do domeny zarządzanej, administrowanie systemem DNS, administrowania itp zasad grupy.
  >

* **Konto użytkownika utworzone dla administracją musi zostać dodany do grupy "Administratorzy kontrolera domeny usługi AAD":** grupy "Administratorzy kontrolera domeny usługi AAD" ma uprawnienia do wykonania niektórych zadań administracji delegowanej na domeny zarządzanej. Te zadania obejmują konfigurowanie usługi DNS, tworząc jednostki organizacyjne, administrowanie zasadami grupy itp. Dostawca usług Kryptograficznych partnera do wykonywania takich zadań na domeny zarządzanej konto użytkownika musi być utworzony w katalogu usługi Azure AD przez klienta. Poświadczenia dla tego konta muszą zostać udostępnione z agentami admin partnera dostawcy usług Kryptograficznych. Ponadto należy dodać konto tego użytkownika do grupy "Administratorzy kontrolera domeny usługi AAD", aby włączyć zadania konfiguracji na domeny zarządzanej, które można wykonać za pomocą tego konta użytkownika.


## <a name="next-steps"></a>Kolejne kroki
* [Rejestracja w programie Azure dostawcy usług Kryptograficznych](https://partnercenter.microsoft.com/partner/programs) i rozpocząć tworzenie biznesowych za pośrednictwem dostawcy usług w Chmurze Azure.
* Zapoznaj się z listą [usług platformy Azure, które są dostępne w dostawcy usług Kryptograficznych Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Włączanie usługi Azure AD Domain Services przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md)
* [Wprowadzenie do usług domenowych Azure AD](active-directory-ds-getting-started.md)
