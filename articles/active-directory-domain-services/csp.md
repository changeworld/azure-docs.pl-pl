---
title: Azure Active Directory Domain Services dla dostawców rozwiązań w chmurze platformy Azure | Microsoft Docs
description: Azure Active Directory Domain Services dla dostawców rozwiązań w chmurze platformy Azure.
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
ms.openlocfilehash: dc4ad7d8cf9f3267713fd066fa79a4d9d8ab733f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612969"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Usługi domenowe w usłudze Azure Active Directory (AD) dla dostawców rozwiązań w chmurze platformy Azure (CSP)
W tym artykule wyjaśniono, jak można użyć Azure AD Domain Services w ramach subskrypcji dostawcy usług kryptograficznych platformy Azure.

## <a name="overview-of-azure-csp"></a>Omówienie usług Azure CSP
Dostawca CSP platformy Azure jest programem dla partnerów firmy Microsoft i udostępnia kanał licencji dla różnych usług w chmurze firmy Microsoft. Dostawca CSP platformy Azure umożliwia partnerom Zarządzanie sprzedażą, własną relacją rozliczeń, obsługę techniczną i rozliczeniami oraz stanowić pojedynczy punkt kontaktu klienta. Ponadto dostawca CSP platformy Azure oferuje pełny zestaw narzędzi, w tym Portal samoobsługowy i dołączone interfejsy API. Te narzędzia umożliwiają partnerom programu CSP łatwe inicjowanie obsługi zasobów platformy Azure i zarządzanie nimi oraz zapewnianie rozliczeń dla klientów i ich subskrypcji.

[Portal Centrum partnerskiego](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) pełni rolę punktu wejścia dla wszystkich partnerów CSP platformy Azure. Zapewnia ona rozbudowane możliwości zarządzania klientami, automatyczne przetwarzanie i nie tylko. Partnerzy CSP platformy Azure mogą korzystać z funkcji Centrum partnerskiego za pomocą interfejsu użytkownika opartego na sieci Web lub programu PowerShell i różnych wywołań interfejsu API.

Na poniższym diagramie przedstawiono, jak model CSP działa na wysokim poziomie. Firma Contoso ma Active Directory usługi Azure AD. Mają one partnerstwo z dostawcą usług kryptograficznych, który wdraża zasoby i zarządza nimi w ramach subskrypcji dostawcy CSP platformy Azure. Firma Contoso może również mieć regularne (bezpośrednie) subskrypcje platformy Azure, które są rozliczane bezpośrednio do firmy Contoso.

![Omówienie modelu CSP](./media/csp/csp_model_overview.png)

Dzierżawca partnera CSP ma trzy specjalne grupy agentów — agentów administracyjnych, agentów pomocy technicznej i agentów sprzedaży. Grupa agenci administracyjni jest przypisana do roli Administrator dzierżawy w katalogu usługi Azure AD firmy Contoso. W związku z tym użytkownik należący do grupy agentów administracyjnych partnera CSP ma uprawnienia administratora dzierżawy w katalogu usługi Azure AD firmy Contoso. Gdy Partner CSP inicjuje subskrypcję dostawcy usług kryptograficznych platformy Azure dla firmy Contoso, jej grupy agentów administracyjnych są przypisywane do roli właściciela tej subskrypcji. W związku z tym agenci administracyjni dostawcy usług kryptograficznych mają uprawnienia wymagane do udostępniania zasobów platformy Azure, takich jak maszyny wirtualne, sieci wirtualne i Azure AD Domain Services w imieniu firmy Contoso.

Aby uzyskać więcej informacji, zobacz [Omówienie usług kryptograficznych platformy Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Zalety korzystania z Azure AD Domain Services w ramach subskrypcji dostawcy usług kryptograficznych platformy Azure
Azure AD Domain Services udostępnia usługi AD zgodne z systemem Windows Server na platformie Azure, takie jak LDAP, uwierzytelnianie Kerberos/NTLM, przyłączanie do domeny, zasady grupy i system DNS. W ciągu dekad wiele aplikacji zostało skompilowanych do pracy z usługą AD przy użyciu tych funkcji. Wielu niezależnych dostawców oprogramowania (ISV) ma aplikacje skompilowane i wdrożone w siedzibie klientów. Te aplikacje są uciążliwe do obsługi, ponieważ często wymagają dostępu do różnych środowisk, w których te aplikacje są wdrażane. Subskrypcje dostawcy CSP platformy Azure pozwalają uprościć alternatywę od skalowalności i elastyczności platformy Azure.

Azure AD Domain Services teraz obsługuje subskrypcje dostawcy CSP platformy Azure. Teraz możesz wdrożyć aplikację w ramach subskrypcji dostawcy CSP platformy Azure powiązanej z katalogiem usługi Azure AD klienta. W związku z tym pracownicy (personel pomocy technicznej) mogą zarządzać maszynami wirtualnymi, na których wdrożono aplikację i obsługiwać je, korzystając z poświadczeń firmowych w organizacji. Dodatkowo można zainicjować obsługę administracyjną Azure AD Domain Services domeny zarządzanej dla katalogu usługi Azure AD klienta. Aplikacja jest połączona z domeną zarządzaną przez klienta. W związku z tym funkcje w aplikacji korzystające z protokołu Kerberos/NTLM, LDAP lub [interfejsu API System. DirectoryServices](/dotnet/api/system.directoryservices) bezproblemowo pracują z katalogiem klienta. Klienci końcowi korzystają znacznie z używania aplikacji jako usługi, nie trzeba martwić się o utrzymanie infrastruktury, w której aplikacja jest wdrażana.

Wszystkie opłaty za zasoby platformy Azure zużywane w ramach tej subskrypcji, w tym Azure AD Domain Services, są naliczane z powrotem. Zapewniasz pełną kontrolę nad relacją z klientem, gdy chodzi o sprzedaż, rozliczenia, pomoc techniczną itp. Dzięki elastyczności platformy CSP platformy Azure niewielki zespół agentów pomocy technicznej może obsługiwać wielu klientów, którzy mają wdrożone wystąpienia aplikacji.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modele wdrażania CSP dla usług domenowych Azure AD
Istnieją dwa sposoby użycia Azure AD Domain Services z subskrypcją dostawcy usług kryptograficznych platformy Azure. Wybierz jedną z nich na podstawie zagadnień związanych z bezpieczeństwem i prostotą posiadanych przez klientów.

### <a name="direct-deployment-model"></a>Model wdrażania bezpośredniego
W tym modelu wdrażania Azure AD Domain Services jest włączona w ramach sieci wirtualnej należącej do subskrypcji CSP platformy Azure. Agenci administracyjni dostawcy usług kryptograficznych mają następujące uprawnienia:
* Uprawnienia administratora globalnego w katalogu usługi Azure AD klienta.
* Uprawnienia właściciela subskrypcji w ramach subskrypcji dostawcy CSP platformy Azure.

![Model wdrażania bezpośredniego](./media/csp/csp_direct_deployment_model.png)

W tym modelu wdrożenia agenci administracyjni dostawcy usług kryptograficznych mogą administrować tożsamościami dla klienta. Ci agenci administracyjni mogą inicjować obsługę nowych użytkowników, grup, dodawać aplikacje w katalogu usługi Azure AD klienta itd. Ten model wdrażania może być dostosowany do mniejszych organizacji, które nie mają dedykowanego administratora tożsamości ani woli, aby partner CSP mogli administrować tożsamościami w ich imieniu.


### <a name="peered-deployment-model"></a>Model wdrażania równorzędnego
W tym modelu wdrażania Azure AD Domain Services jest włączona w ramach sieci wirtualnej należącej do klienta — oznacza to bezpośrednią subskrypcję platformy Azure płatną przez klienta. Partner programu CSP może następnie wdrożyć aplikacje w ramach sieci wirtualnej należącej do subskrypcji dostawcy CSP klienta. Sieci wirtualne można następnie połączyć za pomocą komunikacji równorzędnej sieci wirtualnej platformy Azure. W związku z tym obciążenia/aplikacje wdrożone przez partnera CSP w ramach subskrypcji dostawcy usług kryptograficznych platformy Azure mogą łączyć się z domeną zarządzaną klienta, która została zainicjowana w bezpośredniej subskrypcji klienta platformy Azure.

![Model wdrażania równorzędnego](./media/csp/csp_peered_deployment_model.png)

Ten model wdrażania umożliwia rozdzielenie uprawnień i umożliwia agentom pomocy technicznej partnera usług kryptograficznych administrowanie subskrypcją platformy Azure oraz wdrażanie zasobów i zarządzanie nimi. Jednak agenci działu pomocy technicznej partnera CSP nie muszą mieć uprawnień administratora globalnego w katalogu usługi Azure AD klienta. Administratorzy tożsamości klienta mogą nadal zarządzać tożsamościami dla swojej organizacji.

Ten model wdrażania może być dostosowany do scenariuszy, w których dostawca ISV (niezależny dostawca oprogramowania) udostępnia hostowaną wersję aplikacji lokalnej, która również musi połączyć się z usługą AD klienta.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrowanie Azure AD Domain Services domenami zarządzanymi w subskrypcjach CSP
W przypadku administrowania domeną zarządzaną w ramach subskrypcji dostawcy CSP platformy Azure obowiązują następujące ważne zagadnienia:

* **Agenci administracyjni dostawcy usług kryptograficznych mogą udostępniać domenę zarządzaną przy użyciu swoich poświadczeń:** Azure AD Domain Services obsługuje subskrypcje dostawcy CSP platformy Azure. W związku z tym użytkownicy należący do grupy agenci administracyjni dostawcy usług kryptograficznych mogą zainicjować obsługę administracyjną nowej Azure AD Domain Services domenie zarządzanej.

* **Dostawcy usług kryptograficznych mogą tworzyć skrypty tworzenia nowych domen zarządzanych dla swoich klientów przy użyciu programu PowerShell:** Aby uzyskać szczegółowe informacje [, zobacz Jak włączyć Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md) .

* **Agenci administracyjni dostawcy usług kryptograficznych nie mogą wykonywać bieżących zadań zarządzania w domenie zarządzanej przy użyciu ich poświadczeń:** Użytkownicy administracyjni dostawcy CSP nie mogą wykonywać rutynowych zadań zarządzania w domenie zarządzanej przy użyciu ich poświadczeń. Ci użytkownicy znajdują się poza katalogiem usługi Azure AD klienta, a ich poświadczenia nie są dostępne w katalogu usługi Azure AD klienta. W związku z tym Azure AD Domain Services nie ma dostępu do skrótów hasła protokołu Kerberos i NTLM dla tych użytkowników. W związku z tym użytkownicy nie mogą być uwierzytelniani w Azure AD Domain Services domenach zarządzanych.

  > [!WARNING]
  > **Musisz utworzyć konto użytkownika w katalogu klienta, aby wykonywać bieżące zadania administracyjne w domenie zarządzanej.**
  > Nie można zalogować się do domeny zarządzanej przy użyciu poświadczeń użytkownika administratora dostawcy usług kryptograficznych. Aby to zrobić, Użyj poświadczeń konta użytkownika należącego do katalogu usługi Azure AD klienta. Te poświadczenia są wymagane do wykonywania zadań, takich jak przyłączanie maszyn wirtualnych do domeny zarządzanej, administrowanie systemem DNS, administrowanie zasady grupy itd.
  >

* **Konto użytkownika utworzone na potrzeby ciągłej administracji należy dodać do grupy "Administratorzy usługi AAD DC":** Grupa "Administratorzy usługi AAD DC" ma uprawnienia do wykonywania niektórych delegowanych zadań administracyjnych w domenie zarządzanej. Te zadania obejmują Konfigurowanie systemu DNS, tworzenie jednostek organizacyjnych, administrowanie zasadami grupy itp. Aby partner programu CSP wykonywał takie zadania w domenie zarządzanej, należy utworzyć konto użytkownika w katalogu usługi Azure AD klienta. Poświadczenia dla tego konta muszą być udostępniane agentom administracyjnym partnera programu CSP. Ponadto to konto użytkownika należy dodać do grupy "Administratorzy domeny usługi AAD", aby umożliwić wykonywanie zadań konfiguracyjnych w domenie zarządzanej za pomocą tego konta użytkownika.


## <a name="next-steps"></a>Następne kroki
* [Zarejestruj się w programie CSP platformy Azure](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) i zacznij tworzyć firmę za pomocą dostawcy usług Azure.
* Zapoznaj się z listą [usług platformy Azure dostępnych w programie CSP platformy Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Włączanie usługi Azure AD Domain Services przy użyciu programu PowerShell](powershell-create-instance.md)
* [Wprowadzenie do Azure AD Domain Services](tutorial-create-instance.md)
