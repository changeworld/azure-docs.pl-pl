---
title: 'Usługi Azure Active Directory Domain Services: Funkcje | Dokumentacja firmy Microsoft'
description: Funkcje usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: cd4fdb1e59f5b85fdfd8eb7ca17b77d02dcdac05
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157041"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funkcje
Następujące funkcje są dostępne w domenach usług domenowych Azure AD zarządzane.

* **Środowisko proste wdrożenie:** można włączyć usług domenowych Azure AD dla katalogu usługi Azure AD za pomocą kilku kliknięć. Twoja domena zarządzana obejmuje konta użytkowników tylko w chmurze i kont użytkowników synchronizowanych z katalogu lokalnego.
* **Obsługę przyłączania do domeny:** możesz z łatwością przyłączania do domeny komputerów w sieci wirtualnej platformy Azure, Twoja domena zarządzana jest dostępna w. Środowisko przyłączania do domeny Windows klienta i serwera systemów operacyjnych działa bezproblemowo w stosunku do domen obsługiwanych przez usługi domenowe Azure AD. Można również użyć było przyłączenie do domeny zautomatyzowanych narzędzi w stosunku do tych domen.
* **Wystąpienie jedną domenę na katalog usługi Azure AD:** można utworzyć jednej domeny usługi Active Directory dla każdego katalogu usługi Azure AD.
* **Tworzenie domeny przy użyciu niestandardowych nazw:** można utworzyć domeny przy użyciu niestandardowych nazw (na przykład "contoso100.com") przy użyciu usług domenowych Azure AD. Można użyć albo nazw domen zweryfikowane i niezweryfikowane. Opcjonalnie można również utworzyć domenę z sufiksem domeny wbudowane (czyli "*. onmicrosoft.com") oferowane przez katalogu usługi Azure AD.
* **Zintegrowana z usługą Azure AD:** nie trzeba konfigurować ani niczym zarządzać replikacji usług domenowych Azure AD. Konta użytkowników, członkostwa w grupach i poświadczeń użytkownika (hasła) z katalogu usługi Azure AD są automatycznie dostępne w usługach domenowych Azure AD. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawą usługi Azure AD lub katalogu w środowisku lokalnym są automatycznie synchronizowane w usłudze Azure AD Domain Services.
* **Uwierzytelnianie NTLM i Kerberos:** z obsługą uwierzytelniania NTLM i Kerberos, można wdrażać aplikacje, które zależą od uwierzytelniania Windows-Integrated.
* **Użyj firmowych poświadczeń/haseł:** hasła dla użytkowników w usłudze Azure AD dzierżawy współpracują z usługami domenowymi Azure AD. Użytkownicy mogą używają poświadczeń firmowych, do maszyn przyłączania do domeny, zaloguj się interaktywnie lub za pośrednictwem pulpitu zdalnego i uwierzytelniania względem domeny zarządzanej.
* **Powiązanie z protokołem LDAP & LDAP przeczytaj pomocy technicznej:** można użyć aplikacji, które zależą od powiązania LDAP do uwierzytelniania użytkowników w domenach obsługiwanych przez usługi domenowe Azure AD. Ponadto aplikacje, które używają protokołu LDAP, operacje odczytu atrybutów użytkownika i komputera zapytania z katalogu może również współdziałać z usług domenowych Azure AD.
* **Protokół Secure LDAP (LDAPS):** umożliwia dostęp do katalogu za pośrednictwem bezpiecznego protokołu LDAP (LDAPS). Dostęp protokołu Secure LDAP jest dostępna w ramach sieci wirtualnej domyślnie. Jednak możesz również opcjonalnie włączyć dostęp protokołu secure LDAP przez internet.
* **Zasady grupy:** dla użytkowników i komputerów można użyć pojedynczego wbudowanego obiektu zasad grupy Każdy kontenerów w celu wymuszania zgodności przy użyciu wymaganych zasad zabezpieczeń dla kont użytkowników i komputerów przyłączonych do domeny. Możesz również utworzyć własne niestandardowe obiekty zasad grupy i przypisać je do niestandardowej jednostki organizacyjne, aby [Zarządzanie zasadami grupy](active-directory-ds-admin-guide-administer-group-policy.md).
* **Zarządzanie DNS:** członkowie grupy "Administratorzy usługi AAD DC" można zarządzać usługą DNS dla domeny zarządzanej przy użyciu znanych narzędzi administracyjnych DNS, takich jak przystawki MMC administracji DNS.
* **Tworzenie niestandardowej jednostki organizacyjne (OU):** członkowie grupy "Administratorzy usługi AAD DC" można tworzyć niestandardowych jednostkach organizacyjnych w domenie zarządzanej. Tacy użytkownicy otrzymują pełne uprawnienia administracyjne za pośrednictwem niestandardowe jednostki organizacyjne, więc one można dodawać i usuwać konta usług, komputerów, grup itp. w ramach tych niestandardowych jednostkach organizacyjnych.
* **Dostępne w wielu regionach na świecie platformy Azure:** zobacz [usług platformy Azure według regionów](https://azure.microsoft.com/regions/#services/) strony, aby wiedzieć, regiony platformy Azure, w których usługi domenowe Azure AD jest dostępna.
* **Wysoka dostępność:** usługi domenowe Azure AD oferuje wysoką dostępność domeny. Ta funkcja oferuje gwarancji wyższe czas działania usługi i odporność na awarie. Monitorowania oferuje wbudowane kondycji automatycznego korygowania sprawność po awarii, uruchamiając nowe wystąpienia, aby zastąpić wystąpień zakończonych niepowodzeniem, a do zapewnienia ciągłego usługi dla swojej domeny.
* **Ochrona blokady konta usługi AD:** konta użytkowników są zablokowane przez 30 minut, jeśli pięć podania hasła są używane w ciągu 2 minut. Konta są automatycznie odblokowane po 30 minutach.
* **Użyj narzędzi do zarządzania znanych:** dobrze znanych narzędzi zarządzania usługi Active Directory systemu Windows Server, takich jak Centrum administracyjne usługi Active Directory lub środowiska PowerShell usługi Active Directory można użyć do administrowania domeny zarządzane.
