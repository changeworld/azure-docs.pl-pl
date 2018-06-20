---
title: 'Usług domenowych Azure Active Directory: Funkcje | Dokumentacja firmy Microsoft'
description: Funkcje usług domenowych w usłudze Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 5dd3cde69c6aa36c3d9cb3060dc6deb59ff74a5a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214971"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Funkcje
Następujące funkcje są dostępne w domenach zarządzanych usług domenowych Azure AD.

* **Środowisko proste wdrożenie:** można włączyć usługi domenowe Azure AD dla katalogu usługi Azure AD za pomocą kilku kliknięć. Domeny zarządzanej zawiera konta użytkowników tylko w chmurze i kont synchronizowanych z katalogu lokalnego.
* **Obsługa przyłączenie do domeny:** możesz z łatwością przyłączenie do domeny komputerów w Twojej domeny zarządzanej, jest dostępny w sieci wirtualnej platformy Azure. Środowisko przyłączenie do domeny, na kliencie systemu Windows i działa systemy operacyjne serwera bezproblemowo względem domeny obsługiwanych przez usługi domenowe Azure AD. Można również użyć zautomatyzowane sprzężenie narzędzi względem tych domen.
* **Wystąpienia jednej domeny na katalog usługi Azure AD:** można utworzyć pojedynczej domeny usługi Active Directory dla każdego katalogu usługi Azure AD.
* **Tworzenia domen z niestandardowych nazw:** można utworzyć domeny niestandardowej nazwy (na przykład "contoso100.com") przy użyciu usług domenowych Azure AD. Można używać nazw albo zweryfikowane i niezweryfikowane domeny. Opcjonalnie można również utworzyć domenę z sufiksem domeny wbudowanych (to znaczy "*. onmicrosoft.com") oferowane przez katalog usługi Azure AD.
* **Zintegrowane z usługą Azure AD:** jest konieczne konfigurowanie lub zarządzać replikacją usług domenowych Azure AD. Konta użytkowników, członkostwa w grupach i poświadczeń użytkownika (hasła) z katalogiem Azure AD są automatycznie dostępne w usługach domenowych Azure AD. Nowi użytkownicy, grupy lub zmiany atrybutów z dzierżawy usługi Azure AD lub lokalnego katalogu są synchronizowane automatycznie do usług domenowych Azure AD.
* **Uwierzytelnianie NTLM i Kerberos:** z obsługę uwierzytelniania NTLM i Kerberos, można wdrażać aplikacje korzystające z uwierzytelniania Windows-Integrated.
* **Użyj poświadczeń firmowych/hasła:** haseł dla użytkowników usługi Azure AD dzierżawcy korzystają z usług domenowych Azure AD. Użytkownicy mogą Użyj swoich poświadczeń firmowych do przyłączania do domeny komputerów, logowania interakcyjnego lub za pośrednictwem pulpitu zdalnego i uwierzytelniania względem domeny zarządzanej.
* **Wiązanie LDAP & LDAP odczytu pomocy technicznej:** mogą używać aplikacji, które opierają się na wiązania LDAP do uwierzytelniania użytkowników w domenach obsługiwanych przez usługi domenowe Azure AD. Ponadto aplikacje, które umożliwia atrybuty użytkownika i komputera kwerendy LDAP operacji odczytu z katalogu może również współpracować z usługami domenowymi Azure AD.
* **Bezpieczny protokół LDAP (LDAPS):** można włączyć dostęp do katalogu za pośrednictwem bezpiecznego protokołu LDAP (LDAPS). Bezpieczny dostęp LDAP jest dostępny w sieci wirtualnej domyślnie. Można jednak również włączyć bezpieczny dostęp LDAP, za pośrednictwem Internetu.
* **Zasady grupy:** pojedynczy wbudowany GPO można użyć dla użytkowników i komputerów kontenerów, aby wymuszał zgodność z wymaganych zasad zabezpieczeń dla kont użytkowników i komputerów przyłączonych do domeny. Można także tworzenie własnych niestandardowych obiektów zasad grupy i przypisać je do niestandardowych jednostek organizacyjnych [Zarządzanie zasadami grupy](active-directory-ds-admin-guide-administer-group-policy.md).
* **Zarządzanie DNS:** członków grupy "Administratorzy kontrolera domeny usługi AAD" można zarządzać DNS dla domeny zarządzanej przy użyciu znanych narzędzi administracyjnych DNS, takie jak przystawka programu MMC administracji DNS.
* **Tworzenie niestandardowych jednostek organizacyjnych (OU):** członków grupy "Administratorzy usługi AAD kontrolera domeny" można tworzyć niestandardowe jednostek organizacyjnych w domenie zarządzanej. Te użytkownicy mają prawo pełne uprawnienia administracyjne za pośrednictwem niestandardowych jednostek organizacyjnych, więc ich można usunąć konta usług, komputerów, grup itp. w ramach tych niestandardowych jednostek organizacyjnych.
* **Dostępne w wielu regionach globalne platformy Azure:** zobacz [usług Azure według regionu](https://azure.microsoft.com/regions/#services/) stronę, aby wiedzieć, regiony platformy Azure, w których są dostępne usługi domenowe Azure AD.
* **Wysoka dostępność:** usługi domenowe Azure AD oferują wysoką dostępność domeny. Ta funkcja oferuje gwarancji wyższej czas działania usługi i odporność na awarie. Monitorowania oferuje wbudowane kondycji automatycznego korygowania z błędami przez Obracająca się nowych wystąpień, aby zastąpić wystąpień nie powiodło się, a także aby zapewnić ciągłość usługi dla domeny.
* **Ochrona blokady konta AD:** konta użytkowników są zablokowane na 30 minut Jeśli pięć nieudanych prób podania hasła są używane w ciągu 2 minut. Konta są odblokowany automatycznie po 30 minutach.
* **Użyj narzędzi zarządzania znanych:** znanych narzędzi zarządzania usługi Active Directory systemu Windows Server, takich jak Centrum administracyjne usługi Active Directory lub środowiska PowerShell usługi Active Directory służy do administrowania domen zarządzanych.
