---
title: 'Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów | Microsoft Docs'
description: Na tej stronie udostępniona jest kompleksowa tabela zawierająca porównanie różnych narzędzi integracji katalogów, których można użyć do integracji katalogów.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: billmath
ms.openlocfilehash: 5d189af9b08f2b6e9ea194c15bfba683afc75a54
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Tożsamość hybrydowa: porównanie narzędzi do integracji katalogów
Narzędzia do integracji katalogów były rozbudowywane i rozwijane przez wiele lat.  Ten dokument zapewnia skonsolidowany widok tych narzędzi i ułatwia porównanie funkcji, które są w nich dostępne.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> Program Azure AD Connect obejmuje składniki i funkcje wcześniej wydane jako narzędzia Dirsync i AAD Sync. Te narzędzia nie są już udostępniane oddzielnie, a wszystkie przyszłe ulepszenia będą dołączane do aktualizacji programu Azure AD Connect, dzięki czemu zawsze będzie wiadomo, gdzie można uzyskać najnowsze funkcje.
> 
> Narzędzia DirSync i Azure AD Sync są przestarzałe. Więcej informacji można znaleźć [tutaj](active-directory-aadconnect-dirsync-deprecated.md).
> 
> 

Poniższy klucz ma zastosowanie do wszystkich tabel.

● = dostępna teraz  
PW = w przyszłym wydaniu  
PZ = publiczna wersja zapoznawcza  

## <a name="on-premises-to-cloud-synchronization"></a>Synchronizacja danych lokalnych z chmurą
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory (AAD Sync) | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Połączenie z pojedynczym lokalnym lasem usługi AD |● |● |● |● |● |
| Połączenie z wieloma lokalnymi lasami usługi AD |● |● | |● |● |
| Połączenie z wieloma lokalnymi organizacjami Exchange |● | | | | |
| Połączenie z pojedynczym lokalnym katalogiem LDAP | | | |● |● |
| Połączenie z wieloma lokalnymi katalogami LDAP |  | | |● |● |
| Połączenie z lokalnymi katalogami usługi AD i lokalnymi katalogami LDAP | | | |● |● |
| Połączenie z systemami niestandardowymi (np. SQL, Oracle, MySQL itp.) |PW | | |● |● |
| Synchronizowanie atrybutów zdefiniowanych przez klienta (rozszerzenia katalogów) |● | | | | |
| Połączenie z lokalnymi systemami kadr (np. SAP, Oracle eBusiness, PeopleSoft) |PW | | |● |● |
| Obsługuje reguły synchronizacji programu FIM i łączniki do aprowizowania dla systemów lokalnych | | | |● |● |


## <a name="cloud-to-on-premises-synchronization"></a>Synchronizacja chmury z danymi lokalnymi
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Zapisywanie zwrotne urządzeń |● | |● | | |
| Zapisywanie zwrotne atrybutów (dla wdrożenia hybrydowego programu Exchange) |● |● |● |● |● |
| Zapisywanie zwrotne obiektów grup |● | | | | |
| Zapisywanie zwrotne haseł (z funkcji samoobsługowego resetowania hasła i zmiany hasła) |● |● | | | |

## <a name="authentication-feature-support"></a>Obsługa funkcji uwierzytelniania
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Synchronizacja haseł dla pojedynczego lokalnego lasu usługi AD |● |● |● | | |
| Synchronizacja haseł dla wielu lokalnych lasów usługi AD |● |● | | | |
| Logowanie jednokrotne z federacją |● |● |● |● |● |
| Zapisywanie zwrotne haseł (z funkcji samoobsługowego resetowania hasła i zmiany hasła) |● |● | | | |

## <a name="set-up-and-installation"></a>Konfiguracja i instalacja
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Obsługuje instalację na kontrolerze domeny |● |● |● | |
| Obsługuje instalację przy użyciu programu SQL Express |● |● |● | |
| Łatwe uaktualnienie z narzędzia DirSync |● | | | |
| Lokalizacja środowiska użytkownika administratora na języki systemu Windows Server |● |● |● | |
| Lokalizacja środowiska użytkownika końcowego na języki systemu Windows Server | | | |● |
| Obsługa systemu Windows Server 2008 i Windows Server 2008 R2 |● dla synchronizacji, nie dla federacji |● |● |● |
| Obsługa systemu Windows Server 2012 i Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtrowanie i konfiguracja
| Cecha | Azure Active Directory Connect | Usługi synchronizacji usługi Azure Active Directory | Narzędzie do synchronizacji z usługą Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrowanie według domen i jednostek organizacyjnych |● |● |● |● |● |
| Filtrowanie według wartości atrybutów obiektów |● |● |● |● |● |
| Umożliwia synchronizowanie minimalnego zestawu atrybutów (MinSync) |● |● | | | |
| Umożliwia stosowanie różnych szablonów usług dla przepływów atrybutów |● |● | | | |
| Umożliwia usuwanie atrybutów z przepływu z usługi AD do usługi Azure AD |● |● | | | |
| Umożliwia zaawansowane dostosowywanie przepływów atrybutów |● |● | |● |● |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

