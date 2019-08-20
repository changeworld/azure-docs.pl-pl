---
title: 'Azure Active Directory Domain Services: Synchronizacja w domenach zarządzanych | Microsoft Docs'
description: Opis synchronizacji w Azure Active Directory Domain Services domenie zarządzanej
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: 1c52ac967d241f31d96988fa5ead8b4e049f6f4c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617098"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronizacja w Azure AD Domain Servicesej domenie zarządzanej
Na poniższym diagramie przedstawiono sposób działania synchronizacji w Azure AD Domain Services domenach zarządzanych.

![Synchronizacja w Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronizacja z katalogu lokalnego do dzierżawy usługi Azure AD
Azure AD Connect Sync służy do synchronizowania kont użytkowników, członkostw w grupach i skrótów poświadczeń z dzierżawą usługi Azure AD. Są synchronizowane atrybuty kont użytkowników, takich jak UPN i Lokalny identyfikator SID. Jeśli używasz Azure AD Domain Services, starsze skróty poświadczeń wymagane do uwierzytelniania NTLM i uwierzytelnianie Kerberos są również synchronizowane z dzierżawą usługi Azure AD.

W przypadku skonfigurowania funkcji zapisu zwrotne zmiany w katalogu usługi Azure AD są synchronizowane z powrotem do Active Directory lokalnego. Jeśli na przykład zmienisz hasło przy użyciu samoobsługowego zarządzania hasłami w usłudze Azure AD, zmienione hasło zostanie zaktualizowane w lokalnej domenie usługi AD.

> [!NOTE]
> Zawsze używaj najnowszej wersji Azure AD Connect, aby mieć pewność, że masz poprawki dla wszystkich znanych usterek.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronizacja z dzierżawy usługi Azure AD do domeny zarządzanej
Konta użytkowników, członkostwa w grupach i skróty poświadczeń są synchronizowane z dzierżawy usługi Azure AD do domeny zarządzanej Azure AD Domain Services. Ten proces synchronizacji jest automatyczny. Nie trzeba konfigurować, monitorować ani zarządzać tym procesem synchronizacji. Synchronizacja początkowa może potrwać od kilku godzin do kilku dni, w zależności od liczby obiektów w katalogu usługi Azure AD. Po zakończeniu synchronizacji początkowej trwa około 20-30 minut na zaktualizowanie zmian wprowadzonych w usłudze Azure AD w domenie zarządzanej. Ten interwał synchronizacji dotyczy zmian haseł lub zmian atrybutów wprowadzonych w usłudze Azure AD.

Proces synchronizacji jest również jednokierunkowe/jednokierunkowe. Twoja domena zarządzana jest w dużym stopniu tylko do odczytu, z wyjątkiem wszelkich utworzonych niestandardowych jednostek organizacyjnych. W związku z tym nie można wprowadzać zmian w atrybutach użytkownika, hasłach użytkowników ani członkostwie w grupach w domenie zarządzanej. W związku z tym nie istnieje wsteczna Synchronizacja zmian z domeny zarządzanej z powrotem do dzierżawy usługi Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizacja z środowiska lokalnego z obsługą kilku lasów
Wiele organizacji ma dość skomplikowaną lokalną infrastrukturę tożsamości składającą się z wielu lasów kont. Azure AD Connect obsługuje synchronizowanie użytkowników, grup i skrótów poświadczeń z środowisk z wieloma lasami do dzierżawy usługi Azure AD.

W przeciwieństwie do dzierżawy usługi Azure AD jest dużo prostszej i płaskiej przestrzeni nazw. Aby umożliwić użytkownikom niezawodne uzyskiwanie dostępu do aplikacji zabezpieczonych za pomocą usługi Azure AD, Rozwiąż konflikty nazw UPN między kontami użytkowników w różnych lasach. Domena zarządzana Azure AD Domain Services ma zbliżone podobieństwo do dzierżawy usługi Azure AD. Zobaczysz płaską strukturę jednostki organizacyjnej w domenie zarządzanej. Wszystkie konta użytkowników i grupy są przechowywane w ramach kontenera "AADDC users", mimo że są synchronizowane z różnych domen lub lasów lokalnych. Można skonfigurować hierarchiczną strukturę ORGANIZACYJNą w środowisku lokalnym. Twoja domena zarządzana nadal ma prostą strukturę płaskiej jednostki organizacyjnej.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Wykluczenia — co nie jest synchronizowane z domeną zarządzaną
Następujące obiekty lub atrybuty nie są zsynchronizowane z dzierżawą usługi Azure AD lub domeną zarządzaną:

* **Wykluczone atrybuty:** Użytkownik może zdecydować się na wykluczenie niektórych atrybutów z synchronizacji do dzierżawy usługi Azure AD z domeny lokalnej przy użyciu Azure AD Connect. Te wykluczone atrybuty nie są dostępne w domenie zarządzanej.
* **Zasady grupy:** Zasady grupy skonfigurowane w domenie lokalnej nie są synchronizowane z domeną zarządzaną.
* **Udział Sysvol:** Podobnie zawartość udziału SYSVOL w domenie lokalnej nie jest synchronizowana z domeną zarządzaną.
* **Obiekty komputera:** Obiekty komputerów w przypadku komputerów przyłączonych do domeny lokalnej nie są synchronizowane z domeną zarządzaną. Te komputery nie mają relacji zaufania z domeną zarządzaną i należą do domeny lokalnej. W domenie zarządzanej znajdują się obiekty komputerów tylko dla komputerów, które zostały dołączone do domeny zarządzanej jawnie.
* **Atrybuty SidHistory dla użytkowników i grup:** Identyfikatory SID użytkownika podstawowego i grupy podstawowej z domeny lokalnej są synchronizowane z domeną zarządzaną. Jednak istniejące atrybuty historii SID dla użytkowników i grup nie są synchronizowane z domeny lokalnej do domeny zarządzanej.
* **Struktury jednostek organizacyjnych (OU):** Jednostki organizacyjne zdefiniowane w domenie lokalnej nie są synchronizowane z domeną zarządzaną. W domenie zarządzanej są dwie wbudowane jednostki organizacyjne. Domyślnie domena zarządzana ma płaską strukturę jednostki organizacyjnej. Można jednak [utworzyć niestandardową jednostkę organizacyjną w domenie zarządzanej](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Jak określone atrybuty są synchronizowane z domeną zarządzaną
W poniższej tabeli wymieniono niektóre typowe atrybuty i opisano sposób ich synchronizacji z domeną zarządzaną.

| Atrybut w domenie zarządzanej | Source | Uwagi |
|:--- |:--- |:--- |
| UPN |Atrybut nazwy UPN użytkownika w dzierżawie usługi Azure AD |Atrybut nazwy UPN z dzierżawy usługi Azure AD jest synchronizowany zgodnie z domeną zarządzaną. W związku z tym najbardziej niezawodnym sposobem logowania się do domeny zarządzanej jest użycie nazwy UPN. |
| SAMAccountName |Atrybut mailNickname użytkownika w dzierżawie usługi Azure AD lub wygenerowany automatycznie |Atrybut SAMAccountName jest źródłem z atrybutu mailNickname w dzierżawie usługi Azure AD. Jeśli wiele kont użytkowników ma ten sam atrybut mailNickname, nazwa SAMAccountName jest generowana automatycznie. Jeśli długość prefiksu mailNickname lub nazwy UPN użytkownika jest dłuższa niż 20 znaków, nazwa SAMAccountName jest generowana automatycznie, aby spełnić limit 20 znaków dla atrybutów SAMAccountName. |
| Hasła |Hasło użytkownika z dzierżawy usługi Azure AD |Skróty poświadczeń wymagane do uwierzytelniania NTLM lub Kerberos (nazywane również poświadczeniami uzupełniającymi) są synchronizowane z dzierżawy usługi Azure AD. Jeśli dzierżawa usługi Azure AD jest zsynchronizowaną dzierżawą, te poświadczenia pochodzą z domeny lokalnej. |
| Podstawowy identyfikator SID użytkownika/grupy |Generowany automatycznie |Podstawowy identyfikator SID kont użytkowników/grup jest generowany automatycznie w domenie zarządzanej. Ten atrybut nie jest zgodny z identyfikatorem SID podstawowego użytkownika/grupy obiektu w lokalnej domenie usługi AD. Ta niezgodność wynika z faktu, że domena zarządzana ma inną przestrzeń nazw identyfikatora SID niż domena lokalna. |
| Historia identyfikatora SID dla użytkowników i grup |Lokalny podstawowy użytkownik i identyfikator SID grupy |Atrybut SidHistory dla użytkowników i grup w domenie zarządzanej jest ustawiony jako zgodny z identyfikatorem SID odpowiedniego użytkownika podstawowego lub grupy w domenie lokalnej. Ta funkcja pomaga uprościć i przełączać aplikacje lokalne do domeny zarządzanej, ponieważ nie ma potrzeby ponownego tworzenia listy ACL zasobów. |

> [!NOTE]
> **Zaloguj się do domeny zarządzanej przy użyciu formatu UPN:** Atrybut SAMAccountName może być automatycznie generowany dla niektórych kont użytkowników w domenie zarządzanej. Jeśli wielu użytkowników ma ten sam atrybut mailNickname lub użytkownicy mają zbyt długie prefiksy UPN, można automatycznie wygenerować SAMAccountName dla tych użytkowników. W związku z tym format SAMAccountName (na przykład "CONTOSO\dee") nie zawsze jest niezawodny, aby zalogować się do domeny. Automatycznie generowane SAMAccountName użytkownika może różnić się od prefiksu UPN. Użyj formatu nazwy UPN (na przykład "dee@contoso.com"), aby niezawodnie zalogować się do domeny zarządzanej.

### <a name="attribute-mapping-for-user-accounts"></a>Mapowanie atrybutów dla kont użytkowników
W poniższej tabeli przedstawiono, w jaki sposób konkretne atrybuty obiektów użytkowników w dzierżawie usługi Azure AD są synchronizowane z odpowiednimi atrybutami w domenie zarządzanej.

| Atrybut użytkownika w dzierżawie usługi Azure AD | Atrybut użytkownika w domenie zarządzanej |
|:--- |:--- |
| accountEnabled |kontroli konta użytkownika (ustawia lub czyści bit ACCOUNT_DISABLED) |
| city |l |
| trzeciego |co |
| Dział |Dział |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Stanowisko |tytuł |
| poczta |poczta |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (mogą być czasami generowane automatycznie) |
| Telefon komórkowy |Telefon komórkowy |
| obiektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Skopiowano element SIDHistory |
| passwordPolicies |kontroli konta użytkownika (ustawia lub czyści bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |St |
| Adres |Adres |
| nazwisko |numery seryjne |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapowanie atrybutów dla grup
W poniższej tabeli przedstawiono, w jaki sposób konkretne atrybuty obiektów grupy w dzierżawie usługi Azure AD są synchronizowane z odpowiednimi atrybutami w domenie zarządzanej.

| Grupuj atrybut w dzierżawie usługi Azure AD | Grupuj atrybut w domenie zarządzanej |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (mogą być czasami generowane automatycznie) |
| poczta |poczta |
| mailNickname |msDS-AzureADMailNickname |
| obiektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Skopiowano element SIDHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronizacja skrótów haseł i zagadnienia dotyczące zabezpieczeń
Po włączeniu Azure AD Domain Services katalog usługi Azure AD generuje i przechowuje skróty haseł w formatach zgodnych z protokołem NTLM & Kerberos. 

W przypadku istniejących kont użytkowników w chmurze, ponieważ usługa Azure AD nigdy nie przechowuje haseł w postaci zwykłego tekstu, te skróty nie mogą być generowane automatycznie. W związku z tym firma Microsoft wymaga [od użytkowników chmury resetowania/zmiany haseł](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) w celu wygenerowania i zapisania skrótów haseł w usłudze Azure AD. W przypadku wszystkich kont użytkowników w chmurze utworzonych w usłudze Azure AD po włączeniu Azure AD Domain Services skróty haseł są generowane i przechowywane w formacie zgodnym z NTLM i Kerberos. 

W przypadku kont użytkowników synchronizowanych z lokalnej usługi AD przy użyciu synchronizacji Azure AD Connect należy [skonfigurować Azure AD Connect do synchronizowania skrótów haseł w formatach zgodnych z protokołem NTLM i Kerberos](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Skróty haseł zgodne z NTLM i Kerberos są zawsze przechowywane w sposób zaszyfrowany w usłudze Azure AD. Te skróty są szyfrowane w taki sposób, że tylko Azure AD Domain Services ma dostęp do kluczy odszyfrowywania. Żadna inna usługa lub składnik w usłudze Azure AD nie ma dostępu do kluczy odszyfrowywania. Klucze szyfrowania są unikatowe dla dzierżawy usługi Azure AD. Azure AD Domain Services synchronizuje skróty haseł z kontrolerami domeny dla domeny zarządzanej. Te skróty haseł są przechowywane i zabezpieczane na tych kontrolerach domeny, podobnie jak w przypadku przechowywania i zabezpieczania haseł na kontrolerach domeny usługi AD systemu Windows Server. Dyski dla tych zarządzanych kontrolerów domeny są szyfrowane w stanie spoczynku.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Obiekty, które nie są zsynchronizowane z dzierżawą usługi Azure AD z domeny zarządzanej
Zgodnie z opisem w poprzedniej sekcji tego artykułu nie istnieje żadna synchronizacja z domeny zarządzanej z powrotem do dzierżawy usługi Azure AD. Można [utworzyć niestandardową jednostkę organizacyjną (OU)](create-ou.md) w domenie zarządzanej. Dodatkowo można utworzyć inne jednostki organizacyjne, użytkowników, grupy lub konta usług w ramach tych niestandardowych jednostek organizacyjnych. Żaden z obiektów utworzonych w ramach niestandardowych jednostek organizacyjnych nie jest zsynchronizowany z dzierżawą usługi Azure AD. Te obiekty są dostępne do użycia tylko w ramach domeny zarządzanej. W związku z tym te obiekty nie są widoczne przy użyciu poleceń cmdlet programu PowerShell usługi Azure AD, usługi Azure AD interfejs API programu Graph lub przy użyciu interfejsu użytkownika zarządzania usługą Azure AD.

## <a name="related-content"></a>Powiązana zawartość
* [Scenariusze wdrażania — Azure AD Domain Services](scenarios.md)
* [Zagadnienia dotyczące sieci Azure AD Domain Services](network-considerations.md)
* [Wprowadzenie do Azure AD Domain Services](tutorial-create-instance.md)
