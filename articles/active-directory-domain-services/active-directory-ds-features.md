---
title: 'Azure Active Directory Domain Services: Funkcje | Microsoft Docs'
description: Funkcje Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: e0f38ed8367f076c9f9c4da77440bb3e9603d514
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234211"
---
# <a name="features-provided-by-azure-active-directory-domain-services"></a>Funkcje zapewniane przez Azure Active Directory Domain Services

Poniższe funkcje są dostępne w Azure AD Domain Services domenach zarządzanych.

* **Proste środowisko wdrażania:** Azure AD Domain Services można włączyć dla katalogu usługi Azure AD za pomocą zaledwie kilku kliknięć. Twoja domena zarządzana zawiera konta użytkowników tylko w chmurze i konta użytkowników synchronizowane z katalogiem lokalnym.
* **Obsługa przyłączania do domeny:** Można łatwo przyłączać komputery w sieci wirtualnej platformy Azure, która jest dostępna w domenie zarządzanej. Środowisko do przyłączania do domeny w systemach operacyjnych klienta i serwera systemu Windows współpracuje bezproblemowo z domenami, które są obsługiwane przez Azure AD Domain Services. Możesz również użyć automatycznych przyłączania do domeny dla takich domen.
* **Jedno wystąpienie domeny dla katalogu usługi Azure AD:** Dla każdego katalogu usługi Azure AD można utworzyć pojedynczą domenę Active Directory.
* **Utwórz domeny z nazwami niestandardowymi:** Można tworzyć domeny z nazwami niestandardowymi (na przykład "contoso100.com") przy użyciu Azure AD Domain Services. Można użyć zweryfikowanych lub niezweryfikowanych nazw domen. Opcjonalnie możesz również utworzyć domenę z wbudowanym sufiksem domeny (czyli "*. onmicrosoft.com") oferowanym przez katalog usługi Azure AD.
* **Integracja z usługą Azure AD:** Nie trzeba konfigurować ani zarządzać replikacją do Azure AD Domain Services. Konta użytkowników, członkostwa w grupach i poświadczenia użytkowników (hasła) z katalogu usługi Azure AD są automatycznie dostępne w Azure AD Domain Services. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawy usługi Azure AD lub katalogu lokalnego są automatycznie synchronizowane z Azure AD Domain Services.
* **Uwierzytelnianie NTLM i Kerberos:** Dzięki obsłudze uwierzytelniania NTLM i Kerberos można wdrażać aplikacje korzystające z uwierzytelniania zintegrowanego z systemem Windows.
* **Użyj poświadczeń/haseł firmowej:** Hasła użytkowników w dzierżawie usługi Azure AD działają z Azure AD Domain Services. Użytkownicy mogą używać swoich poświadczeń firmowych do przyłączania do domeny komputerów, logować się interaktywnie lub za pośrednictwem pulpitu zdalnego i uwierzytelniać się w domenie zarządzanej.
* **Obsługa odczytu & LDAP powiązania LDAP:** Aplikacje korzystające z powiązań LDAP można używać do uwierzytelniania użytkowników w domenach, które są obsługiwane przez Azure AD Domain Services. Ponadto aplikacje używające operacji odczytu protokołu LDAP do wykonywania zapytań dotyczących atrybutów użytkownika/komputera z katalogu mogą również współdziałać z Azure AD Domain Services.
* **Secure LDAP (LDAPs):** Dostęp do katalogu można włączyć za pośrednictwem protokołu Secure LDAP (LDAPs). Secure LDAP dostęp jest domyślnie dostępny w sieci wirtualnej. Można jednak opcjonalnie włączyć bezpieczny dostęp do protokołu LDAP za pośrednictwem Internetu.
* **Zasady grupy:** Można użyć jednego wbudowanego obiektu zasad grupy dla kontenerów Użytkownicy i komputery, aby wymusić zgodność z wymaganymi zasadami zabezpieczeń dla kont użytkowników i komputerów przyłączonych do domeny. Możesz również utworzyć własne niestandardowe obiekty zasad grupy i przypisać je do niestandardowych jednostek organizacyjnych, aby [zarządzać zasadami grup](manage-group-policy.md).
* **Zarządzanie systemem DNS:** Członkowie grupy "Administratorzy usługi AAD DC" mogą zarządzać usługą DNS dla domeny zarządzanej przy użyciu znanych narzędzi administracyjnych systemu DNS, takich jak przystawka programu MMC do administrowania usługą DNS.
* **Utwórz niestandardowe jednostki organizacyjne (OU):** Członkowie grupy "Administratorzy usługi AAD DC" mogą tworzyć niestandardowe jednostki organizacyjne w domenie zarządzanej. Ci użytkownicy otrzymują pełne uprawnienia administracyjne względem niestandardowych jednostek organizacyjnych, dzięki czemu mogą dodawać/usuwać konta usług, komputery, grupy itp. w ramach tych niestandardowych jednostek organizacyjnych.
* **Dostępne w wielu regionach globalnych platformy Azure:** Ze strony zawierającej [usługi platformy Azure uporządkowane według regionów](https://azure.microsoft.com/regions/#services/) dowiesz się, w których regionach platformy Azure jest dostępna usługa Azure AD Domain Services.
* **Wysoka dostępność:** Azure AD Domain Services zapewnia wysoką dostępność dla domeny. Ta funkcja oferuje gwarancję większego czasu działania usługi i odporność na awarie. Wbudowane monitorowanie kondycji oferuje automatyczne korygowanie błędów dzięki wykorzystaniu nowych wystąpień w celu zastąpienia wystąpień zakończonych niepowodzeniem i zapewnienia ciągłej usługi dla domeny.
* **Ochrona blokady konta usługi AD:** Konta użytkowników są zablokowane przez 30 minut, jeśli pięć nieprawidłowych haseł zostanie użytych w ciągu 2 minut. Konta są automatycznie odblokowywane po 30 minutach.
* **Korzystaj ze znanych narzędzi do zarządzania:** Do administrowania domenami zarządzanymi można używać znanych narzędzi do zarządzania Active Directory systemu Windows Server, takich jak Centrum administracyjne usługi Active Directory lub Active Directory PowerShell.
