---
title: 'Azure Active Directory Domain Services: Synchronizacja w domenach zarządzanych | Dokumentacja firmy Microsoft'
description: Omówienie synchronizacji w domenie zarządzanej usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 295a991e610e76971413a2abdba1e2fcc5f9eba6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246692"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronizacja w domenie zarządzanej usług domenowych Azure AD
Na poniższym diagramie przedstawiono synchronizacji działanie w usługach domenowych Azure AD domen zarządzanych.

![Synchronizacja usługi domenowe Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronizacja z katalogu lokalnego z dzierżawą usługi Azure AD
Synchronizacja programu Azure AD Connect umożliwia synchronizowanie kont użytkowników, członkostwa w grupach i poświadczeń wyznacza wartość skrótu dla Twojej dzierżawy usługi Azure AD. Atrybuty użytkownika konta, takie jak nazwa UPN i dla lokalnego identyfikatora SID (identyfikator zabezpieczeń) są synchronizowane. Jeśli używasz usług domenowych Azure AD, do dzierżawy usługi Azure AD synchronizowane są również skrótów starszych poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos.

Jeśli skonfigurujesz zapisu, są synchronizowane usługi Active Directory w środowisku lokalnym zmiany zachodzące w katalogu usługi Azure AD. Na przykład, jeśli zmienisz hasło przy użyciu usługi Azure AD Samoobsługowe zarządzanie hasłami, zmiany hasła jest aktualizowana w lokalnej domeny usługi AD.

> [!NOTE]
> Zawsze używaj najnowszej wersji programu Azure AD Connect, aby upewnić się, że poprawki dla wszystkich znanych błędów.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronizacja z dzierżawą usługi Azure AD do domeny zarządzanej
Konta użytkowników, członkostw w grupie i skrótów poświadczeń są synchronizowane z dzierżawą usługi Azure AD do domeny zarządzanej usług domenowych Azure AD. Ten proces — synchronizacja odbywa się automatycznie. Nie ma potrzeby konfigurowania, monitorowania i zarządzania ten proces synchronizacji. Początkowa synchronizacja może potrwać od kilku godzin do kilku dni, zależnie od liczby obiektów w katalogu usługi Azure AD. Po ukończeniu synchronizacji początkowej, trwa około 20 – 30 minut, zanim zmiany wprowadzone w usłudze Azure AD do zaktualizowania w Twojej domeny zarządzanej. Ten interwał synchronizacji ma zastosowanie do zmiany hasła lub zmiany atrybutów wprowadzone w usłudze Azure AD.

Proces synchronizacji jest również jeden-way/jednokierunkowe charakter. Twoja domena zarządzana jest w dużej mierze tylko do odczytu z wyjątkiem wszystkich niestandardowych jednostkach organizacyjnych, możesz utworzyć. W związku z tym nie możesz wprowadzać zmian do atrybutów użytkownika, hasła użytkownika lub członkostwa w grupach w domenie zarządzanej. W rezultacie jest nie synchronizacja odwrotna zmian z domeny zarządzanej, wróć do dzierżawy usługi Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizacja ze środowiska obejmującego wiele lasów w środowisku lokalnym
Wiele organizacji ma dość złożone lokalne infrastruktury tożsamości, składający się z wielu lasów kont. Azure AD Connect obsługuje synchronizowanie użytkownikom, grupom i skrótów poświadczeń z środowisk wielu lasów z dzierżawą usługi Azure AD.

Z kolei dzierżawy usługi Azure AD jest znacznie prostsze i prostego przestrzeni nazw. Aby umożliwić użytkownikom w niezawodny sposób będą uzyskiwać dostęp do aplikacji zabezpieczonej przez usługi Azure AD, należy rozwiązać konflikty UPN na kontach użytkowników w różnych lasach. Twoje polarnych domeny zarządzanej usług domenowych Azure AD Zamknij podobieństwo do dzierżawy usługi Azure AD. Zobaczysz płaskiej struktury jednostek organizacyjnych w domenie zarządzanej. Wszystkie konta użytkowników i grupy są przechowywane w kontenerze "Użytkownicy usługi AAD DC" pomimo są synchronizowane z różnych lokalnych domen i lasów. Skonfigurowano hierarchiczne jednostki Organizacyjnej struktury w środowisku lokalnym. Twoja domena zarządzana środki, nieopłacone prosty płaskiej struktury jednostek organizacyjnych.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Wykluczenia — co nie jest zsynchronizowany z domeną zarządzaną
Następujących obiektów i atrybutów nie są zsynchronizowane z dzierżawą usługi Azure AD lub do domeny zarządzanej:

* **Wykluczone atrybuty:** Można wykluczyć określone atrybuty po nieudanym synchronizowaniu do dzierżawy usługi Azure AD z domeny lokalnej za pomocą usługi Azure AD Connect. Te atrybuty wykluczone nie są dostępne w Twojej domeny zarządzanej.
* **Zasady grupy:** Zasady grupy, które skonfigurowano w domenie lokalnej nie są zsynchronizowane z domeną zarządzaną.
* **Udział Sysvol:** Podobnie zawartość udziału Sysvol w domenie lokalnej nie są zsynchronizowane z domeną zarządzaną.
* **Obiekty komputerów:** Obiekty komputera dla komputery przyłączone do domeny w środowisku lokalnym nie są zsynchronizowane z domeną zarządzaną. Te komputery nie mają ustanowioną relację zaufania z domeny zarządzanej i nie należy do Twojej domeny w środowisku lokalnym. W Twojej domeny zarządzanej możesz znaleźć obiekty komputera tylko w przypadku komputerów, na których możesz mieć jawnie przyłączone do domeny zarządzanej.
* **Atrybuty elementu SidHistory dla użytkowników i grup:** Podstawowy użytkownik i grupa podstawowa identyfikatory SID z Twojej domeny w środowisku lokalnym są synchronizowane z domeną zarządzaną. Jednak istniejące atrybuty historii SID dla użytkowników i grup nie są synchronizowane z lokalnej domeny do domeny zarządzanej.
* **Struktury jednostki Organizacyjnej organizacji:** Jednostek organizacyjnych zdefiniowanych w domenie lokalnej nie są synchronizowane z domeną zarządzaną. Istnieją dwie wbudowane jednostek organizacyjnych w domenie zarządzanej. Domyślnie Twoja domena zarządzana ma płaskiej struktury jednostek organizacyjnych. Można jednak wybrać [Tworzenie niestandardowej jednostki Organizacyjnej w domenie zarządzanej](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>W jaki sposób określone atrybuty są synchronizowane z domeną zarządzaną
Poniższej tabeli wymieniono niektóre typowe atrybuty i w tym artykule opisano, jak są one zsynchronizowane z domeną zarządzaną.

| Atrybut w Twojej domeny zarządzanej | Source | Uwagi |
|:--- |:--- |:--- |
| UPN |Atrybutu nazwy UPN użytkownika w dzierżawie usługi Azure AD |Atrybutu nazwy UPN z dzierżawą usługi Azure AD są synchronizowane, ponieważ ma domeny zarządzanej. W związku z tym najbardziej niezawodnym sposobem, aby zalogować się do domeny zarządzanej używa nazwy UPN. |
| SAMAccountName |MailNickname użytkownika atrybutu w dzierżawie usługi Azure AD lub wygenerowany automatycznie |Atrybutu SAMAccountName pochodzi z atrybut mailNickname w dzierżawie usługi Azure AD. Jeśli wiele kont użytkowników mają ten sam atrybut mailNickname, SAMAccountName został wygenerowany automatycznie. Jeśli mailNickname lub prefiks nazwy UPN użytkownika jest dłuższa niż 20 znaków, SAMAccountName jest generowane automatycznie do zaspokojenia limit 20 znaków atrybuty SAMAccountName. |
| Hasła |Hasło użytkownika z dzierżawy usługi Azure AD |Skrótów poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos (nazywane również dodatkowe poświadczenia) są synchronizowane z dzierżawą usługi Azure AD. Jeśli dzierżawa usługi Azure AD jest dzierżawa zsynchronizowana, te poświadczenia pochodzą z domeny lokalnej. |
| Podstawowy użytkownik/identyfikator SID grupy |Wygenerowany automatycznie |Podstawowy identyfikator SID dla kont użytkownika/grupy jest generowany automatycznie w Twojej domeny zarządzanej. Ten atrybut jest niezgodna z podstawowego użytkownika/grupy SID obiektu w Twojej lokalnej domeny usługi AD. Ta niezgodność jest, ponieważ domena zarządzana ma innej przestrzeni nazw identyfikator SID niż domeny w środowisku lokalnym. |
| Historia SID dla użytkowników i grup |W środowisku lokalnym użytkownika podstawowego i identyfikatora SID grupy |Atrybut SidHistory dla użytkowników i grup w domenie zarządzanej jest ustawiona do dopasowania odpowiedniego użytkownika podstawowego lub identyfikator SID w domenie lokalnej grupy. Ta funkcja pomaga w ułatwienia lift-and-shift aplikacji lokalnych do domeny zarządzanej, ponieważ nie trzeba do listy ACL re zasobów. |

> [!NOTE]
> **Zaloguj się do domeny zarządzanej przy użyciu formatu nazwy UPN:** Atrybutu SAMAccountName może być generowane automatycznie dla niektórych kont użytkowników w Twojej domeny zarządzanej. Jeśli wielu użytkowników ma taki sam atrybut mailNickname lub użytkownicy mają zbyt długich prefiksy nazwy UPN, SAMAccountName dla tych użytkowników może być generowane automatycznie. W związku z tym w formacie SAMAccountName (na przykład "CONTOSO100\joeuser") nie zawsze jest niezawodny sposób, aby zalogować się do domeny. SAMAccountName generowanych automatycznie użytkowników może różnić się od prefiksu ich nazwy UPN. Użyj formatu nazwy UPN (na przykład "joeuser@contoso100.com") do logowania się w domenie zarządzanej niezawodnie.

### <a name="attribute-mapping-for-user-accounts"></a>Mapowanie atrybutów dla kont użytkowników
W poniższej tabeli przedstawiono, jak określone atrybuty dla użytkownika obiektów w dzierżawie usługi Azure AD są synchronizowane z odpowiednich atrybutów w Twojej domeny zarządzanej.

| Atrybut użytkownika w dzierżawie usługi Azure AD | Atrybut użytkownika w domenie zarządzanej |
|:--- |:--- |
| accountEnabled |kontroli konta użytkownika (ustawia lub czyści ACCOUNT_DISABLED-bitowy) |
| city |l |
| Kraj |co |
| Dział |Dział |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Stanowisko |tytuł |
| poczta |poczta |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |Element SAMAccountName (czasami może się wygenerowany automatycznie) |
| Telefon komórkowy |Telefon komórkowy |
| Identyfikator obiektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |historii SID |
| passwordPolicies |kontroli konta użytkownika (ustawia lub czyści DONT_EXPIRE_PASSWORD-bitowy) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |St |
| Adres |Adres |
| nazwisko |numery seryjne |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapowanie atrybutów dla grup
W poniższej tabeli przedstawiono, jak określone atrybuty dla grupy obiektów w dzierżawie usługi Azure AD są synchronizowane z odpowiednich atrybutów w Twojej domeny zarządzanej.

| Atrybut grupy w Twojej dzierżawie usługi Azure AD | Atrybut grupy w domenie zarządzanej |
|:--- |:--- |
| displayName |displayName |
| displayName |Element SAMAccountName (czasami może się wygenerowany automatycznie) |
| poczta |poczta |
| mailNickname |msDS-AzureADMailNickname |
| Identyfikator obiektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |historii SID |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Zagadnienia synchronizacji i zabezpieczeń wyznaczania wartości skrótu hasła
Po włączeniu usług domenowych Azure AD, katalogiem usługi Azure AD generuje i są przechowywane wartości skrótów haseł NTLM i Kerberos zgodny formatów. 

W przypadku istniejących kont użytkowników chmury ponieważ usługa Azure AD nie przechowuje swoich haseł w postaci zwykłego tekstu, te skróty nie można wygenerować automatycznie. Firma Microsoft wymaga [użytkowników chmury, aby Resetowanie/zmiana haseł](active-directory-ds-getting-started-password-sync.md) w kolejności do ich skrótów haseł wygenerowanie konfiguracji węzłów i przechowywane w usłudze Azure AD. Dla wszystkich kont użytkowników chmury utworzone w usłudze Azure AD po włączeniu usług domenowych Azure AD skrótów haseł są generowane i przechowywane w formatach zgodne NTLM i Kerberos. 

Dla kont użytkowników synchronizowanych z lokalnej usługi AD za pomocą usługi Azure AD Connect Sync, musisz [Konfigurowanie usługi Azure AD Connect do synchronizowania skrótów haseł NTLM i Kerberos zgodny format](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Hasło zgodne z skrótów NTLM i Kerberos zawsze są przechowywane w sposób zaszyfrowany w usłudze Azure AD. Te skróty są szyfrowane w taki sposób, że tylko usługi Azure AD Domain Services ma dostęp do kluczy odszyfrowywania. Nie ma innych usług lub składników w usłudze Azure AD ma dostęp do kluczy odszyfrowywania. Klucze szyfrowania są unikatowe dzierżawy usługi AD na platformie Azure. Azure AD Domain Services synchronizuje skrótów haseł do kontrolerów domeny dla domeny zarządzanej. Te skróty haseł są przechowywane i zabezpieczane na te kontrolery domeny, podobnie jak jak hasła są przechowywane i zabezpieczane na kontrolerach domeny systemu Windows Server AD. Dyski te kontrolery domeny zarządzane są szyfrowane, gdy.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Obiekty, które nie są zsynchronizowane z dzierżawą usługi Azure AD z domeny zarządzanej
Zgodnie z opisem we wcześniejszej sekcji tego artykułu, jest brak synchronizacji z domeny zarządzanej, wróć do dzierżawy usługi Azure AD. Można wybrać opcję [Tworzenie niestandardowej jednostki organizacyjnej (OU)](create-ou.md) w Twojej domeny zarządzanej. Ponadto można utworzyć inne jednostki organizacyjne, użytkowników, grup lub kont usług w ramach tych niestandardowych jednostkach organizacyjnych. Brak obiektów utworzonych w ramach niestandardowej jednostki organizacyjne są synchronizowane z powrotem dzierżawy usługi Azure AD. Te obiekty są dostępne do użytku tylko w ramach Twojej domeny zarządzanej. Dlatego te obiekty nie są widoczne, za pomocą poleceń cmdlet programu Azure AD PowerShell, interfejsu API programu Graph usługi Azure AD lub interfejs użytkownika zarządzania usługi Azure AD.

## <a name="related-content"></a>Powiązana zawartość
* [Funkcje — Azure AD Domain Services](active-directory-ds-features.md)
* [Scenariusze wdrażania — usługi domenowe Azure AD](scenarios.md)
* [Informacje dotyczące sieci usługi Azure AD Domain Services](network-considerations.md)
* [Rozpoczynanie pracy z usługą Azure AD Domain Services](create-instance.md)
