---
title: Usługi domenowe usługi Azure AD dla dostawców rozwiązań w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączać domeny zarządzane usług domenowych Usług domenowych Active Directory i zarządzać nimi dla dostawców rozwiązań w chmurze azure
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519085"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Wdrażanie usług domenowych Active Directory platformy Azure dla dostawców rozwiązań w chmurze azure

Dostawcy rozwiązań w chmurze azure (CSP) to program dla partnerów firmy Microsoft i zapewnia kanał licencji dla różnych usług w chmurze firmy Microsoft. Usługa CSP platformy Azure umożliwia partnerom zarządzanie sprzedażą, posiadanie relacji rozliczeniowych, zapewnianie pomocy technicznej i obsługi rozliczeń oraz być pojedynczym punktem kontaktowym klienta. Ponadto usługa Azure CSP udostępnia pełny zestaw narzędzi, w tym portal samoobsługowy i towarzyszące interfejsy API. Te narzędzia umożliwiają partnerom usługi CSP łatwe udostępnianie zasobów platformy Azure i zarządzanie nimi oraz zapewnianie rozliczeń klientom i ich subskrypcjom.

Portal [Centrum partnerów](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) jest punktem wejścia dla wszystkich partnerów usługi Azure CSP i zapewnia zaawansowane funkcje zarządzania klientami, zautomatyzowane przetwarzanie i inne. Partnerzy usługi Azure CSP mogą korzystać z funkcji Centrum partnerów przy użyciu interfejsu użytkownika opartego na sieci Web lub przy użyciu programu PowerShell i różnych wywołań interfejsu API.

Na poniższym diagramie przedstawiono, jak model CSP działa na wysokim poziomie. W tym miejscu contoso ma dzierżawę usługi Azure Active Directory (Azure AD). Mają partnerstwo z usługą CSP, która wdraża zasoby w ramach subskrypcji usługi Azure CSP i zarządza nimi. Contoso może również mieć regularne (bezpośrednie) subskrypcje platformy Azure, które są rozliczane bezpośrednio do contoso.

![Omówienie modelu CSP](./media/csp/csp_model_overview.png)

Dzierżawa partnera CSP ma trzy grupy agentów specjalnych - agentów *administracyjnych,* agentów *Helpdesk* i agentów *sprzedaży.*

Grupa Agenci *administracyjni* jest przypisana do roli administratora dzierżawy w dzierżawie usługi Azure AD firmy Contoso. W rezultacie użytkownik należący do grupy agentów administracyjnych partnera CSP ma uprawnienia administratora dzierżawy w dzierżawie usługi Azure AD firmy Contoso.

Gdy partner usług kryptograficzny apowionuje subskrypcję usługi Azure CSP dla firmy Contoso, ich grupa agentów administracyjnych jest przypisana do roli właściciela dla tej subskrypcji. W rezultacie agenci administratorzy partnera usług kryptograficznych mają wymagane uprawnienia do aprowizowania zasobów platformy Azure, takich jak maszyny wirtualne, sieci wirtualne i usługi domenowe usługi azure ad w imieniu firmy Contoso.

Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Korzyści z używania usług Azure AD DS w ramach subskrypcji usługi Azure CSP

Usługi domenowe Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, uwierzytelnianie LDAP, Kerberos/NTLM, które jest w pełni zgodne z Usługami domenowymi Active Directory systemu Windows Server. Przez dziesięciolecia wiele aplikacji zostało zbudowanych do pracy z usługą AD przy użyciu tych funkcji. Wielu niezależnych dostawców oprogramowania (ISV) zbudowało i wdrożyło aplikacje w siedzibie swoich klientów. Te aplikacje są trudne do obsługi, ponieważ często wymagają dostępu do różnych środowisk, w których są wdrażane aplikacje. Dzięki subskrypcji usługi Azure CSP masz prostszą alternatywę ze skalą i elastycznością platformy Azure.

Usługi Azure AD DS obsługuje subskrypcje usługi Azure CSP. Aplikację można wdrożyć w ramach subskrypcji usługi Azure CSP powiązanej z dzierżawą usługi Azure AD klienta. W rezultacie pracownicy (pracownicy pomocy technicznej) mogą zarządzać maszynami wirtualnymi, na których aplikacja jest wdrażana, zarządzać nimi i obsługiwać je przy użyciu poświadczeń firmowych organizacji.

Można również wdrożyć domenę zarządzaną usługą Azure AD DS w dzierżawie usługi Azure AD klienta. Aplikacja jest następnie połączona z domeną zarządzana przez klienta. Możliwości w aplikacji, które opierają się na Kerberos / NTLM, LDAP lub [System.DirectoryServices API](/dotnet/api/system.directoryservices) działają bezproblemowo w stosunku do domeny klienta. Klienci końcowi korzystają z korzystania z aplikacji jako usługi, bez konieczności martwienia się o utrzymanie infrastruktury, na której jest wdrażana aplikacja.

Wszystkie rozliczenia za zasoby platformy Azure, które zużywają w tej subskrypcji, w tym usługi Azure AD DS, są naliczane z powrotem do Ciebie. Zachowujesz pełną kontrolę nad relacjami z klientem, jeśli chodzi o sprzedaż, rozliczenia, wsparcie techniczne itp. Dzięki elastyczności platformy Azure CSP mały zespół agentów pomocy technicznej może obsługiwać wielu takich klientów, którzy mają wdrożone wystąpienia aplikacji.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modele wdrażania usługi CSP dla usług Azure AD DS

Istnieją dwa sposoby, w którym można używać usług Azure AD DS z subskrypcją usługi Azure CSP. Wybierz odpowiedni na podstawie bezpieczeństwa i prostoty, jakie mają twoi klienci.

### <a name="direct-deployment-model"></a>Model wdrażania bezpośredniego

W tym modelu wdrażania usługi Azure AD DS jest włączona w sieci wirtualnej, która należy do subskrypcji usługi Azure CSP. Agenci administracyjni partnera CSP mają następujące uprawnienia:

* *Uprawnienia administratora globalnego* w dzierżawie usługi Azure AD klienta.
* Uprawnienia *właściciela subskrypcji* w ramach subskrypcji usługi Azure CSP.

![Model wdrażania bezpośredniego](./media/csp/csp_direct_deployment_model.png)

W tym modelu wdrażania agenci administracyjni dostawcy dostawcy usług kryptograficznych mogą administrować tożsamościami klienta. Ci agenci administracyjni mogą wykonywać zadania, takie jak inicjowanie obsługi administracyjnej nowych użytkowników lub grup lub dodawać aplikacje w dzierżawie usługi Azure AD klienta.

Ten model wdrażania może być odpowiedni dla mniejszych organizacji, które nie mają dedykowanego administratora tożsamości lub wolą, aby partner dostawcy usług kryptograficznych administrował tożsamościami w ich imieniu.

### <a name="peered-deployment-model"></a>Model wdrażania w elementach równorzędnych

W tym modelu wdrażania usługi Azure AD DS jest włączona w sieci wirtualnej należącej do klienta — bezpośredniej subskrypcji platformy Azure opłaconej przez klienta. Partner usługi CSP może wdrażać aplikacje w sieci wirtualnej należącej do subskrypcji CSP klienta. Sieci wirtualne można następnie połączyć za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure.

Dzięki temu wdrożeniu obciążenia lub aplikacje wdrożone przez partnera dostawcy usług kryptograficznych w ramach subskrypcji usługi Azure CSP mogą łączyć się z domeną zarządza zarządza zarządza zarządzanią klienta aprowizowana w bezpośredniej subskrypcji platformy Azure klienta.

![Model wdrażania w elementach równorzędnych](./media/csp/csp_peered_deployment_model.png)

Ten model wdrażania zapewnia oddzielenie uprawnień i umożliwia agentom pomocy technicznej partnera usługi CSP administrowanie subskrypcją platformy Azure oraz wdrażanie i zarządzanie zasobami w niej. Jednak agenci pomocy technicznej partnera CSP nie muszą mieć uprawnień administratora globalnego w katalogu usługi Azure AD klienta. Administratorzy tożsamości klienta mogą nadal zarządzać tożsamościami w swojej organizacji.

Ten model wdrażania może być dostosowany do scenariuszy, w których niezależny niezależny numer isv udostępnia hostowanej wersji ich aplikacji lokalnej, która również musi połączyć się z usługą Azure AD klienta.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrowanie usługą Azure AD DS w ramach subskrypcji usług CSP

Podczas administrowania domeną zarządzaną w ramach subskrypcji usługi Azure CSP obowiązują następujące ważne zagadnienia:

* **Agenci administracyjni dostawcy usług kryptograficznych mogą aprowizować domenę zarządzaną przy użyciu ich poświadczeń:** Usługi Azure AD DS obsługuje subskrypcje usługi Azure CSP. Użytkownicy należący do grupy agentów administracyjnych partnera dostawcy usług kryptograficznych mogą aprowizować nową domenę zarządzaną usługą Azure AD DS.

* **Dostawcy usług internetowych mogą skryptować tworzenie nowych domen zarządzanych dla swoich klientów przy użyciu programu PowerShell:** Zobacz, [jak włączyć usługi Azure AD DS przy użyciu programu PowerShell, aby](powershell-create-instance.md) uzyskać szczegółowe informacje.

* **Agenci administracyjni dostawcy usług kryptograficznych nie mogą wykonywać bieżących zadań zarządzania w domenie zarządzanej przy użyciu poświadczeń:** Użytkownicy administratorów dostawcy usług kryptograficznych nie mogą wykonywać rutynowych zadań zarządzania w domenie zarządzanej przy użyciu poświadczeń. Ci użytkownicy są zewnętrzni do dzierżawy usługi Azure AD klienta i ich poświadczenia nie są dostępne w dzierżawie usługi Azure AD klienta. Usługi Azure AD DS nie ma dostępu do skrótów haseł Protokołu Kerberos i NTLM dla tych użytkowników, więc użytkownicy nie mogą być uwierzytelnieni w domenach zarządzanych usług Azure AD DS.

  > [!WARNING]
  > Aby wykonywać bieżące zadania administracyjne w domenie zarządzanej, należy utworzyć konto użytkownika w katalogu klienta.
  >
  > Nie można zalogować się do domeny zarządzanej przy użyciu poświadczeń administratora dostawcy usług kryptograficznych. Użyj poświadczeń konta użytkownika należącego do dzierżawy usługi Azure AD klienta, aby to zrobić. Te poświadczenia są potrzebne do zadań, takich jak dołączanie maszyn wirtualnych do domeny zarządzanej, administrowanie systemem DNS lub administrowanie zasadami grupy.

* **Konto użytkownika utworzone dla bieżącej administracji musi zostać dodane do grupy *Administratorzy kontrolera domeny kontrolera domeny usługi AAD:* ** Grupa *Administratorzy kontrolera domeny kontrolera domeny usługi AAD* ma uprawnienia do wykonywania niektórych delegowanych zadań administracyjnych w domenie zarządzanej. Zadania te obejmują konfigurowanie systemu DNS, tworzenie jednostek organizacyjnych i administrowanie zasadami grupy.
    
    Aby partner dostawcy usług kryptograficznych wykonywał te zadania w domenie zarządzanej, należy utworzyć konto użytkownika w dzierżawie usługi Azure AD klienta. Poświadczenia dla tego konta muszą być współużytkowane agentom administracyjnym partnera CSP. Ponadto to konto użytkownika musi zostać dodane do grupy *Administratorzy kontrolera domeny usługi AAD,* aby umożliwić wykonywanie zadań konfiguracyjnych w domenie zarządzanej przy użyciu tego konta użytkownika.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, [zarejestruj się w programie Azure CSP](/partner-center/enrolling-in-the-csp-program). Następnie można włączyć usługi domenowe usługi azure ad przy użyciu [witryny Azure portal](tutorial-create-instance.md) lub [programu Azure PowerShell](powershell-create-instance.md).
