---
title: 'Usługi Azure Active Directory Domain Services: Synchronizacja w domenach zarządzanych | Dokumentacja firmy Microsoft'
description: Zrozumienie synchronizacji w domenie zarządzanej usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 2449c8178f726eacad089debeae6cf1db56cc67a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698445"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronizacja w domenie zarządzanej usług domenowych Azure AD
Na poniższym diagramie przedstawiono, jak działa synchronizacji w w usługach domenowych Azure AD w domenach zarządzanych.

![Synchronizacja w usługach domenowych Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronizacja z katalogu lokalnego do dzierżawy usługi Azure AD
Synchronizacja programu Azure AD Connect umożliwia synchronizowanie kont użytkowników, członkostw i skróty poświadczeń do dzierżawy usługi Azure AD. Atrybuty użytkownika konta, takie jak nazwa UPN i lokalnymi identyfikatora SID (identyfikator zabezpieczeń) są synchronizowane. Jeśli korzystasz z usług domenowych Azure AD, do dzierżawy usługi Azure AD synchronizowane są również skrótów starszych poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos.

Jeśli skonfigurujesz zapisu są synchronizowane lokalnej usługi Active Directory zmiany zachodzące w katalogu usługi Azure AD. Na przykład, jeśli zmienisz hasło przy użyciu usługi Azure AD hasła samoobsługowego zarządzania zmiany hasła jest aktualizowany w lokalnej domeny usługi AD.

> [!NOTE]
> Zawsze używać najnowszej wersji programu Azure AD Connect, aby upewnić się, że poprawki dla wszystkich znanych błędów.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronizacja z dzierżawy usługi Azure AD do domeny zarządzanej
Konta użytkowników, członkostwa w grupach i skrótów poświadczeń są synchronizowane z dzierżawy usługi Azure AD do domeny zarządzanej usług domenowych Azure AD. Ten proces synchronizacji odbywa się automatycznie. Nie ma potrzeby konfigurowania, monitorowania lub zarządzania tym procesem synchronizacji. Początkowa synchronizacja może zająć od kilku godzin do kilku dni zależnie od liczby obiektów w katalogu usługi Azure AD. Po ukończeniu synchronizacji początkowej, trwa około 20-30 minut zmian wprowadzonych w usłudze Azure AD do zaktualizowania w domeny zarządzanej. Ten interwał synchronizacji ma zastosowanie do zmiany hasła lub zmiany atrybutów wprowadzone w usłudze Azure AD.

Proces synchronizacji jest również jednorazowe-way/jednokierunkowe charakter. Domeny zarządzanej jest przede wszystkim tylko do odczytu z wyjątkiem wszystkich niestandardowych jednostkach organizacyjnych, należy utworzyć. W związku z tym nie można wprowadzić zmian atrybutów użytkowników, hasła użytkownika lub członkostwa w grupach w ramach domeny zarządzanej. W związku z tym jest nie odwrotnej synchronizacja zmian z domeny zarządzanej do dzierżawy usługi Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizacja w środowisku wielu lasów lokalnych
Organizacje często mają dość złożone lokalnymi infrastruktury tożsamości, składające się z wielu lasów kont. Azure AD Connect obsługuje synchronizacji użytkowników, grup i skrótów poświadczeń w środowisku wielu lasów w dzierżawie usługi Azure AD.

Z kolei dzierżawy usługi Azure AD jest znacznie prostsza i płaskiej przestrzeni nazw. Aby umożliwić użytkownikom niezawodny dostęp do aplikacji zabezpieczonej przez usługi Azure AD, należy rozwiązać konflikty UPN między kontami użytkowników w różnych lasach. Twoje posiada domeny zarządzanej usług domenowych Azure AD Zamknij podobieństwa do dzierżawy usługi Azure AD. Zobaczysz płaska struktura jednostek organizacyjnych w domenie zarządzanej. Wszystkie konta użytkowników i grup są przechowywane w kontenerze "AADDC użytkownicy", niezależnie od synchronizowanych z lokalnej różnych domen i lasów. Skonfigurowane hierarchiczna jednostki Organizacyjnej struktury lokalnymi. Domeny zarządzanej nadal ma prosty płaska struktura jednostki Organizacyjnej.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Wykluczenia — co nie jest zsynchronizowany do domeny zarządzanej
Następujące obiektów i atrybutów nie są zsynchronizowane z dzierżawą usługi Azure AD lub do domeny zarządzanej:

* **Wykluczone atrybutów:** można wykluczyć określone atrybuty synchronizowanie do dzierżawy usługi Azure AD z domeny lokalnej za pomocą usługi Azure AD Connect. Te atrybuty wykluczone nie są dostępne w Twojej domeny zarządzanej.
* **Zasady grupy:** skonfigurowane w domenie lokalnych zasad grupy nie są synchronizowane z domeny zarządzanej.
* **Udział Sysvol:** podobnie zawartość udziału Sysvol w domenie lokalnej nie są synchronizowane z domeny zarządzanej.
* **Obiekty komputerów:** obiektów komputera dla komputerów do domeny lokalnej nie są zsynchronizowane z domeny zarządzanej. Te komputery nie mieć relację zaufania z domeną zarządzanych i należeć do Twojej domeny lokalnej. Domeny zarządzanej możesz znaleźć obiektów komputerów wyłącznie dla komputerów, które możesz mieć jawnie przyłączonych do domeny zarządzanej.
* **Atrybuty SidHistory dla użytkowników i grup:** głównego użytkownika i identyfikatory SID z domeny lokalnej grupy podstawowej są synchronizowane z domeny zarządzanej. Jednak istniejące atrybuty SidHistory dla użytkowników i grup nie są synchronizowane z lokalnej domeny do domeny zarządzanej.
* **Struktury jednostek (OU) w organizacji:** jednostek organizacyjnych zdefiniowanych w domenie lokalnej nie Synchronizuj do domeny zarządzanej. Istnieją dwa wbudowane jednostek organizacyjnych w domenie zarządzanej. Domyślnie domeny zarządzanej ma prosty struktury jednostek organizacyjnych. Można jednak wybrać opcję [Tworzenie niestandardowej jednostki Organizacyjnej w domenie zarządzanej](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Jak określone atrybuty są synchronizowane z domeny zarządzanej
Poniższa tabela zawiera niektóre typowe atrybuty oraz w tym artykule opisano, jak są synchronizowane z domeny zarządzanej.

| Atrybut w domeny zarządzanej | Element źródłowy | Uwagi |
|:--- |:--- |:--- |
| Nazwa UPN |Atrybut nazwy UPN użytkownika w dzierżawie usługi Azure AD |Atrybut nazwy głównej użytkownika z dzierżawy usługi Azure AD są synchronizowane, ponieważ jest do domeny zarządzanej. W związku z tym najbardziej niezawodnym sposobem Zaloguj się do domeny zarządzanej korzysta z głównej nazwy użytkownika. |
| sAMAccountName |MailNickname użytkownika atrybut w dzierżawie usługi Azure AD lub generowane automatycznie |Atrybut SAMAccountName pochodzi z atrybutem mailNickname w dzierżawie usługi Azure AD. Jeśli wiele kont użytkowników mają taki sam atrybut mailNickname, SAMAccountName został wygenerowany automatycznie. Jeśli mailNickname lub prefiks nazwy UPN użytkownika jest dłuższa niż 20 znaków, SAMAccountName został wygenerowany automatycznie do zaspokojenia atrybuty SAMAccountName limit 20 znaków. |
| Hasła |Hasło użytkownika z dzierżawy usługi Azure AD |Skrótów poświadczeń wymaganych do uwierzytelniania NTLM lub Kerberos (nazywanych również dodatkowe poświadczenia) są synchronizowane z dzierżawy usługi Azure AD. Jeśli dzierżawy usługi Azure AD jest zsynchronizowanej dzierżawy, te poświadczenia pochodzą z domeny lokalnej. |
| Podstawowy użytkownik/identyfikator SID grupy |Wygenerowany automatycznie |Podstawowy identyfikator SID dla konta użytkownika/grupy jest generowany automatycznie domeny zarządzanej. Ten atrybut jest niezgodna z podstawowy identyfikator SID obiektu w katalogu lokalnym użytkownika/grupy domeny AD. Ta niezgodność jest ponieważ domeny zarządzanej ma inną przestrzeń nazw identyfikatora SID niż domeny lokalnej. |
| Historii identyfikatora SID dla użytkowników i grup |Podstawowy użytkownik lokalny i identyfikator SID grupy |Atrybut SidHistory dla użytkowników i grup w domenie zarządzanej ustawiono odpowiadające odpowiedniego użytkownika podstawowego lub identyfikator SID w domenie lokalnej grupy. Ta funkcja pomaga ułatwić przyrostu i shift aplikacji lokalnych do domeny zarządzanej, ponieważ nie trzeba ponownie ACL zasobów. |

> [!NOTE]
> **Zaloguj się do domeny zarządzanej przy użyciu formatu UPN:** atrybut SAMAccountName mogą być generowane automatycznie dla niektórych kont użytkowników w domenie zarządzanej. Jeśli wielu użytkowników mają taki sam atrybut mailNickname, użytkownicy muszą prefiksy UPN zbyt długo SAMAccountName dla tych użytkowników może być generowane automatycznie. W związku z tym format SAMAccountName (na przykład "CONTOSO100\joeuser") nie zawsze jest to niezawodny sposób, aby zalogować się do domeny. SAMAccountName automatycznie generowanej użytkowników może różnić się od prefiksu ich nazwy UPN. Użyj formatu nazwy UPN (na przykład "joeuser@contoso100.com") do logowania do domeny zarządzanej niezawodnie.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>Mapowanie atrybutu dla kont użytkowników
W poniższej tabeli przedstawiono sposób określonych atrybutów użytkownika obiektów w dzierżawie usługi Azure AD są synchronizowane z odpowiednich atrybutów w domeny zarządzanej.

| Atrybut użytkownika w dzierżawie usługi Azure AD | Atrybut użytkownika w domenie zarządzanej |
|:--- |:--- |
| accountEnabled |kontroli konta użytkownika (ustawia lub czyści ACCOUNT_DISABLED-bitowy) |
| city |l |
| Kraju |co |
| dział |dział |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Stanowisko |tytuł |
| poczta |poczta |
| mailNickname |atrybut msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (czasami może się generowane automatycznie) |
| Telefon komórkowy |Telefon komórkowy |
| Identyfikator obiektu |atrybut msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |historii SID |
| passwordPolicies |kontroli konta użytkownika (ustawia lub czyści DONT_EXPIRE_PASSWORD-bitowy) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |St |
| Adres |Adres |
| nazwisko |SN |
| TelephoneNumber |TelephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapowanie atrybutu dla grup
W poniższej tabeli przedstawiono sposób określonych atrybutów grupy obiektów w dzierżawie usługi Azure AD są synchronizowane z odpowiednich atrybutów w domeny zarządzanej.

| Grupa atrybutów w dzierżawie usługi Azure AD | Atrybut grupy w domenie zarządzanej |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (czasami może się generowane automatycznie) |
| poczta |poczta |
| mailNickname |atrybut msDS-AzureADMailNickname |
| Identyfikator obiektu |atrybut msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |historii SID |
| securityEnabled |GroupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Zagadnienia synchronizacji i zabezpieczeń skrótu hasła
Po włączeniu usług domenowych Azure AD, katalog usługi Azure AD generuje i przechowuje wartości skrótów haseł w formatach zgodne NTLM i Kerberos. 

W przypadku istniejących kont użytkowników chmury ponieważ usługi Azure AD nigdy nie przechowuje swoich haseł w postaci zwykłego tekstu, te wartości skrótu nie można wygenerować automatycznie. Firma Microsoft wymaga [chmury użytkownikom resetowanie/zmienianie haseł](active-directory-ds-getting-started-password-sync.md) w kolejności ich skrótów haseł wygenerowany i przechowywane w usłudze Azure AD. Dla dowolnego konta użytkownika chmurze utworzone w usłudze Azure AD po włączeniu usług domenowych Azure AD skrótów haseł były generowane i przechowywane w formatach zgodne NTLM i Kerberos. 

Dla konta użytkowników są synchronizowane z lokalnej usłudze AD za pomocą usługi Azure AD Sync Connect, musisz [Konfigurowanie usługi Azure AD Connect do synchronizowania skrótów haseł w formatach NTLM i Kerberos zgodny](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Skrótów haseł zgodne NTLM i Kerberos zawsze są przechowywane w sposób zaszyfrowane w usłudze Azure AD. Te skróty są szyfrowane w taki sposób, że tylko usługi domenowe Azure AD ma dostęp do kluczy odszyfrowywania. Nie ma innych usług lub składników w usłudze Azure AD ma dostęp do kluczy odszyfrowywania. Klucze szyfrowania są unikatowe dla usługi Azure AD dzierżawy. Usługi domenowe Azure AD synchronizacji skrótów haseł na kontrolerach domeny dla domeny zarządzanej. Te skróty haseł są przechowywane i zabezpieczane na te kontrolery domeny podobny do sposobu przechowywania haseł i zabezpieczone na kontrolerach domeny systemu Windows Server AD. Dyski dla tych kontrolerów domeny zarządzanej są szyfrowane, gdy.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Obiekty, które nie są zsynchronizowane z dzierżawą usługi Azure AD z domeny zarządzanej
Zgodnie z opisem w poprzedniej sekcji tego artykułu, nie istnieje żadne synchronizacji z domeny zarządzanej do dzierżawy usługi Azure AD. Można wybrać opcję [Tworzenie niestandardowej jednostki organizacyjnej (OU)](active-directory-ds-admin-guide-create-ou.md) w domenie zarządzanej. Ponadto można utworzyć jednostek organizacyjnych, użytkownicy, grupy lub konta usług w ramach tych niestandardowych jednostek organizacyjnych. Brak obiektów utworzonych w ramach jednostkami organizacyjnymi niestandardowe są synchronizowane z powrotem dzierżawy usługi Azure AD. Te obiekty są dostępne do użycia tylko w obrębie domeny zarządzanej. W związku z tym te obiekty nie są widoczne, przy użyciu poleceń cmdlet programu PowerShell usługi Azure AD, Azure AD Graph API lub interfejs użytkownika zarządzania usługi Azure AD.

## <a name="related-content"></a>Powiązana zawartość
* [Funkcje — usługi domenowe Azure AD](active-directory-ds-features.md)
* [Scenariusze wdrażania - usługi domenowe Azure AD](active-directory-ds-scenarios.md)
* [Zagadnienia dotyczące sieci dla usług domenowych Azure AD](active-directory-ds-networking.md)
* [Wprowadzenie do usług domenowych Azure AD](active-directory-ds-getting-started.md)
