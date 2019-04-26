---
title: 'Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów | Microsoft Docs'
description: Na tej stronie udostępniona jest kompleksowa tabela zawierająca porównanie różnych narzędzi integracji katalogów, których można użyć do integracji katalogów.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aed01ea11c1f53cb090d9c2e65ee23f521575649
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456921"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów
Narzędzia do integracji katalogów były rozbudowywane i rozwijane przez wiele lat.  Ten dokument zapewnia skonsolidowany widok tych narzędzi i ułatwia porównanie funkcji, które są w nich dostępne.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Program Azure AD Connect obejmuje składniki i funkcje wcześniej wydane jako narzędzia Dirsync i AAD Sync. Te narzędzia nie są już udostępniane oddzielnie, a wszystkie przyszłe ulepszenia będą dołączane do aktualizacji programu Azure AD Connect, dzięki czemu zawsze będzie wiadomo, gdzie można uzyskać najnowsze funkcje.
> 
> Narzędzia DirSync i Azure AD Sync są przestarzałe. Więcej informacji można znaleźć [tutaj](reference-connect-dirsync-deprecated.md).
> 
> 

Poniższy klucz ma zastosowanie do wszystkich tabel.

● = dostępna teraz  
PW = w przyszłym wydaniu  
PZ = publiczna wersja zapoznawcza  

## <a name="on-premises-to-cloud-synchronization"></a>Synchronizacja danych lokalnych z chmurą
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory (AAD Sync) — JUŻ NIEOBSŁUGIWANE | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) — JUŻ NIEOBSŁUGIWANE | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Połączenie z pojedynczym lokalnym lasem usługi AD |● |● |● |● |● |
| Połączenie z wieloma lokalnymi lasami usługi AD |● |● | |● |● |
| Połączenie z wieloma lokalnymi organizacjami Exchange |● | | | | |
| Połączenie z pojedynczym lokalnym katalogiem LDAP |●* | | |● |● | 
| Połączenie z wieloma lokalnymi katalogami LDAP |●*  | | |● |● | 
| Połączenie z lokalnymi katalogami usługi AD i lokalnymi katalogami LDAP |●* | | |● |● | 
| Połączenie z systemami niestandardowymi (np. SQL, Oracle, MySQL itp.) |PW | | |● |● |
| Synchronizowanie atrybutów zdefiniowanych przez klienta (rozszerzenia katalogów) |● | | | | |
| Połączenie z lokalnymi systemami kadr (np. SAP, Oracle eBusiness, PeopleSoft) |PW | | |● |● |
| Obsługuje reguły synchronizacji programu FIM i łączniki do aprowizowania dla systemów lokalnych | | | |● |● |

 
&#42; Obecnie są obsługiwane dwie związane z tym opcje.  Oto one: 

   1. Można użyć ogólnego łącznika LDAP i włączyć go poza usługą Azure AD Connect.  Jest to skomplikowane i wymaga partnera na potrzeby wdrażania oraz utrzymywania umowy pomocy technicznej Premium.  Ta opcja umożliwia obsługę zarówno pojedynczych, jak i wielu katalogów LDAP. 

   2. Można opracować własne rozwiązanie do przenoszenia obiektów z usługi LDAP do usługi Active Directory.  Następnie można zsynchronizować obiekty z programem Azure AD Connect.  Jako rozwiązania do przenoszenia obiektów można użyć programu MIM lub FIM. 

## <a name="cloud-to-on-premises-synchronization"></a>Synchronizacja chmury z danymi lokalnymi
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory — JUŻ NIEOBSŁUGIWANE  | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) — JUŻ NIEOBSŁUGIWANE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Zapisywanie zwrotne urządzeń |● | |● | | |
| Zapisywanie zwrotne atrybutów (dla wdrożenia hybrydowego programu Exchange) |● |● |● |● |● |
| Zapisywanie zwrotne obiektów grup |● | | | | |
| Zapisywanie zwrotne haseł (z funkcji samoobsługowego resetowania hasła i zmiany hasła) |● |● | | | |

## <a name="authentication-feature-support"></a>Obsługa funkcji uwierzytelniania
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory — JUŻ NIEOBSŁUGIWANE  | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) — JUŻ NIEOBSŁUGIWANE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Synchronizacja skrótów haseł dla jednego lokalnego lasu usługi AD |●|●|● | | |
| Synchronizacja skrótów haseł dla wielu lokalnych lasów usługi AD |●|● | | | |
| Uwierzytelnianie przekazywane dla jednego lokalnego lasu usługi AD |●| | | | |
| Logowanie jednokrotne z federacją |● |● |● |● |● |
| Bezproblemowe logowanie jednokrotne|● |||||
| Zapisywanie zwrotne haseł (z funkcji samoobsługowego resetowania hasła i zmiany hasła) |● |● | | | |

## <a name="set-up-and-installation"></a>Konfiguracja i instalacja
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory — JUŻ NIEOBSŁUGIWANE  | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) — JUŻ NIEOBSŁUGIWANE  | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Obsługuje instalację na kontrolerze domeny |● |● |● | |
| Obsługuje instalację przy użyciu programu SQL Express |● |● |● | |
| Łatwe uaktualnienie z narzędzia DirSync |● | | | |
| Lokalizacja środowiska użytkownika administratora na języki systemu Windows Server |● |● |● | |
| Lokalizacja środowiska użytkownika końcowego na języki systemu Windows Server | | | |● |
| Obsługa systemu Windows Server 2008 i Windows Server 2008 R2 |● dla synchronizacji, nie dla federacji |● |● |● |
| Obsługa systemu Windows Server 2012 i Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtrowanie i konfiguracja
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory — JUŻ NIEOBSŁUGIWANE  | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) — JUŻ NIEOBSŁUGIWANE  | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrowanie według domen i jednostek organizacyjnych |● |● |● |● |● |
| Filtrowanie według wartości atrybutów obiektów |● |● |● |● |● |
| Umożliwia synchronizowanie minimalnego zestawu atrybutów (MinSync) |● |● | | | |
| Umożliwia stosowanie różnych szablonów usług dla przepływów atrybutów |● |● | | | |
| Umożliwia usuwanie atrybutów z przepływu z usługi AD do usługi Azure AD |● |● | | | |
| Umożliwia zaawansowane dostosowywanie przepływów atrybutów |● |● | |● |● |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).

