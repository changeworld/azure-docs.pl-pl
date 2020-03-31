---
title: Usługi domenowe usługi Azure AD dla dostawców rozwiązań w chmurze | Dokumenty firmy Microsoft
description: Usługi domenowe usługi Active Directory platformy Azure dla dostawców rozwiązań w chmurze platformy Azure.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754437"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Usługi domenowe usługi w usłudze Azure Active Directory (AD) dla dostawców rozwiązań w chmurze (CSP)
W tym artykule wyjaśniono, jak można używać usług domenowych usługi Azure AD w ramach subskrypcji usługi Azure CSP.

## <a name="overview-of-azure-csp"></a>Omówienie usługi Azure CSP
Usługa CSP platformy Azure jest programem dla partnerów firmy Microsoft i zapewnia kanał licencji dla różnych usług w chmurze firmy Microsoft. Usługa CSP platformy Azure umożliwia partnerom zarządzanie sprzedażą, posiadanie relacji rozliczeniowych, zapewnianie pomocy technicznej i obsługi rozliczeń oraz być pojedynczym punktem kontaktowym klienta. Ponadto usługa Azure CSP udostępnia pełny zestaw narzędzi, w tym portal samoobsługowy i towarzyszące interfejsy API. Te narzędzia umożliwiają partnerom usługi CSP łatwe udostępnianie zasobów platformy Azure i zarządzanie nimi oraz zapewnianie rozliczeń klientom i ich subskrypcjom.

Portal [Centrum partnerów](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) działa jako punkt wejścia dla wszystkich partnerów usługi Azure CSP. Zapewnia bogate możliwości zarządzania klientami, zautomatyzowane przetwarzanie i wiele więcej. Partnerzy usługi Azure CSP mogą korzystać z funkcji Centrum partnerów przy użyciu interfejsu użytkownika opartego na sieci Web lub przy użyciu programu PowerShell i różnych wywołań interfejsu API.

Na poniższym diagramie przedstawiono, jak model CSP działa na wysokim poziomie. Contoso ma usługę Azure AD Active Directory. Mają partnerstwo z usługą CSP, która wdraża zasoby w ramach subskrypcji usługi Azure CSP i zarządza nimi. Contoso może również mieć regularne (bezpośrednie) subskrypcje platformy Azure, które są rozliczane bezpośrednio do contoso.

![Omówienie modelu CSP](./media/csp/csp_model_overview.png)

Dzierżawa partnera CSP ma trzy grupy agentów specjalnych - agentów administracyjnych, agentów Helpdesk i agentów sprzedaży. Grupa Agenci administracyjni jest przypisana do roli administratora dzierżawy w katalogu usługi Azure AD firmy Contoso. W rezultacie użytkownik należący do grupy agentów administracyjnych partnera CSP ma uprawnienia administratora dzierżawy w katalogu usługi Azure AD firmy Contoso. Gdy partner usług kryptograficzny apowionuje subskrypcję usługi Azure CSP dla firmy Contoso, ich grupa agentów administracyjnych jest przypisana do roli właściciela dla tej subskrypcji. W rezultacie agenci administratorzy partnera usług kryptograficznych mają wymagane uprawnienia do aprowizowania zasobów platformy Azure, takich jak maszyny wirtualne, sieci wirtualne i usługi domenowe usługi azure ad w imieniu firmy Contoso.

Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Korzyści z korzystania z usług domenowych usługi Azure AD w ramach subskrypcji usługi CSP platformy Azure
Usługi domenowe usługi Azure AD zapewniają usługi zgodne z usługami Windows Server AD na platformie Azure, takie jak uwierzytelnianie LDAP, Kerberos/NTLM, dołączanie do domeny, zasady grupy i system DNS. Przez dziesięciolecia wiele aplikacji zostało zbudowanych do pracy z usługą AD przy użyciu tych funkcji. Wielu niezależnych dostawców oprogramowania (ISV) zbudowało i wdrożyło aplikacje w siedzibie swoich klientów. Te aplikacje są uciążliwe do obsługi, ponieważ często wymaga dostępu do różnych środowisk, w których te aplikacje są wdrażane. Dzięki subskrypcji usługi Azure CSP masz prostszą alternatywę ze skalą i elastycznością platformy Azure.

Usługi domenowe usługi Azure AD domain services obsługują teraz subskrypcje usługi Azure CSP. Teraz można wdrożyć aplikację w subskrypcji usługi Azure CSP powiązanej z katalogiem usługi Azure AD klienta. W rezultacie pracownicy (pracownicy pomocy technicznej) mogą zarządzać maszynami wirtualnymi, na których jest wdrażana aplikacja, zarządzać nimi i obsługiwać je przy użyciu poświadczeń firmowych organizacji. Ponadto można aprowizować domenę zarządzaną usług ad azure dla katalogu usługi Azure AD klienta. Aplikacja jest połączona z domeną zarządzana przez klienta. W związku z tym możliwości w aplikacji, które opierają się na Kerberos/NTLM, LDAP lub [System.DirectoryServices INTERFEJSU API](/dotnet/api/system.directoryservices) działają bezproblemowo w katalogu klienta. Klienci końcowi korzystają znacznie z korzystania z aplikacji jako usługi, bez konieczności martwienia się o utrzymanie infrastruktury, w której aplikacja jest wdrażana.

Wszystkie rozliczenia za zasoby platformy Azure zużywane w tej subskrypcji, w tym usługi domenowe usługi Azure AD, są naliczane z powrotem do Ciebie. Zachowujesz pełną kontrolę nad relacjami z klientem, jeśli chodzi o sprzedaż, rozliczenia, wsparcie techniczne itp. Dzięki elastyczności platformy Azure CSP mały zespół agentów pomocy technicznej może obsługiwać wielu takich klientów, którzy mają wdrożone wystąpienia aplikacji.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modele wdrażania usługi CSP dla usług domen usługi Ad Azure
Istnieją dwa sposoby korzystania z usług domenowych usługi Azure AD domain services z subskrypcją usługi Azure CSP. Wybierz odpowiedni na podstawie bezpieczeństwa i prostoty, jakie mają twoi klienci.

### <a name="direct-deployment-model"></a>Model wdrażania bezpośredniego
W tym modelu wdrażania usługi domenowe usługi ad usługi azure ad jest włączona w sieci wirtualnej należącej do subskrypcji usługi Azure CSP. Agenci administracyjni partnera CSP mają następujące uprawnienia:
* Uprawnienia administratora globalnego w katalogu usługi Azure AD klienta.
* Uprawnienia właściciela subskrypcji w ramach subskrypcji usługi Azure CSP.

![Model wdrażania bezpośredniego](./media/csp/csp_direct_deployment_model.png)

W tym modelu wdrażania agenci administracyjni dostawcy dostawcy usług kryptograficznych mogą administrować tożsamościami klienta. Ci agenci administracyjni mają możliwość inicjowania obsługi administracyjnej nowych użytkowników, grup, dodawania aplikacji w katalogu usługi Azure AD klienta itp. Ten model wdrażania może być odpowiedni dla mniejszych organizacji, które nie mają dedykowanego administratora tożsamości lub wolą, aby partner dostawcy usług kryptograficznych administrował tożsamościami w ich imieniu.


### <a name="peered-deployment-model"></a>Model wdrażania w elementach równorzędnych
W tym modelu wdrażania usługi domenowe usługi ad usługi azure ad jest włączona w sieci wirtualnej należącej do klienta — czyli bezpośredniej subskrypcji platformy Azure opłacane przez klienta. Partner usługi CSP można następnie wdrożyć aplikacje w sieci wirtualnej należącej do subskrypcji CSP klienta. Sieci wirtualne można następnie połączyć za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure. W rezultacie obciążeń/aplikacji wdrożonych przez partnera dostawcy usług kryptograficznych w subskrypcji usługi Azure CSP można połączyć się z domeny zarządzanej klienta aprowizowanych w bezpośredniej subskrypcji platformy Azure klienta.

![Model wdrażania w elementach równorzędnych](./media/csp/csp_peered_deployment_model.png)

Ten model wdrażania zapewnia oddzielenie uprawnień i umożliwia agentom pomocy technicznej partnera usługi CSP administrowanie subskrypcją platformy Azure oraz wdrażanie i zarządzanie zasobami w niej. Jednak agenci pomocy technicznej partnera CSP nie muszą mieć uprawnień administratora globalnego w katalogu usługi Azure AD klienta. Administratorzy tożsamości klienta mogą nadal zarządzać tożsamościami w swojej organizacji.

Ten model wdrażania może być dostosowany do scenariuszy, w których niezależny dostawca oprogramowania (niezależny dostawca oprogramowania) udostępnia hostowanej wersji aplikacji lokalnej, która również musi połączyć się z usługą AD klienta.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrowanie domenami zarządzanymi usługami domenowymi usługi AD azure w subskrypcjach dostawcy usług kryptograficznych
Podczas administrowania domeną zarządzaną w ramach subskrypcji usługi Azure CSP obowiązują następujące ważne zagadnienia:

* **Agenci administracyjni dostawcy usług kryptograficznych mogą aprowizować domenę zarządzaną przy użyciu ich poświadczeń:** Usługi domenowe usługi Azure AD obsługuje subskrypcje usługi Azure CSP. W związku z tym użytkownicy należący do grupy agentów administracyjnych partnera dostawcy usług kryptograficznych mogą aprowizować nową domenę zarządzaną usług domenowych usługi azure ad.

* **Dostawcy usług internetowych mogą skryptować tworzenie nowych domen zarządzanych dla swoich klientów przy użyciu programu PowerShell:** Zobacz, [jak włączyć usługi domenowe usługi Azure AD przy użyciu programu PowerShell,](powershell-create-instance.md) aby uzyskać szczegółowe informacje.

* **Agenci administracyjni dostawcy usług kryptograficznych nie mogą wykonywać bieżących zadań zarządzania w domenie zarządzanej przy użyciu poświadczeń:** Użytkownicy administratorów dostawcy usług kryptograficznych nie mogą wykonywać rutynowych zadań zarządzania w domenie zarządzanej przy użyciu poświadczeń. Ci użytkownicy są zewnętrzni w katalogu usługi Azure AD klienta, a ich poświadczenia nie są dostępne w katalogu usługi Azure AD klienta. W związku z tym usługi domenowe usługi azure ad nie ma dostępu do skrótów haseł Kerberos i NTLM dla tych użytkowników. W rezultacie takich użytkowników nie można uwierzytelnić w domenach zarządzanych usług ad.azure.

  > [!WARNING]
  > **Aby wykonywać bieżące zadania administracyjne w domenie zarządzanej, należy utworzyć konto użytkownika w katalogu klienta.**
  > Nie można zalogować się do domeny zarządzanej przy użyciu poświadczeń administratora dostawcy usług kryptograficznych. Użyj poświadczeń konta użytkownika należącego do katalogu usługi Azure AD klienta, aby to zrobić. Te poświadczenia są potrzebne do zadań, takich jak dołączanie maszyn wirtualnych do domeny zarządzanej, administrowanie systemem DNS, administrowanie zasadami grupy itp.
  >

* **Konto użytkownika utworzone dla bieżącej administracji musi zostać dodane do grupy "Administratorzy kontrolera domeny usługi AAD":** Grupa "Administratorzy kontrolera domeny usługi AAD" ma uprawnienia do wykonywania niektórych delegowanych zadań administracyjnych w domenie zarządzanej. Zadania te obejmują konfigurowanie systemu DNS, tworzenie jednostek organizacyjnych, administrowanie zasadami grupy itp. Aby partner dostawcy usług kryptograficznych wykonywał takie zadania w domenie zarządzanej, konto użytkownika musi zostać utworzone w katalogu usługi Azure AD klienta. Poświadczenia dla tego konta muszą być współużytkowane agentom administracyjnym partnera CSP. Ponadto to konto użytkownika musi zostać dodane do grupy "Administratorzy kontrolera domeny usługi AAD", aby umożliwić wykonywanie zadań konfiguracyjnych w domenie zarządzanej przy użyciu tego konta użytkownika.


## <a name="next-steps"></a>Następne kroki
* [Zarejestruj się w programie Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) i rozpocznij tworzenie firmy za pośrednictwem usługi Azure CSP.
* Przejrzyj listę [usług platformy Azure dostępnych w usłudze Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Włączanie usługi Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md)
* [Wprowadzenie do usług Azure AD Domain Services](tutorial-create-instance.md)
