---
title: 'Usługa Azure AD Connect: funkcje w wersji zapoznawczej | Dokumenty firmy Microsoft'
description: W tym temacie opisano bardziej szczegółowe funkcje, które są w wersji zapoznawczej w usłudze Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261283"
---
# <a name="more-details-about-features-in-preview"></a>Więcej szczegółów na temat funkcji w wersji zapoznawczej
W tym temacie opisano sposób używania funkcji aktualnie w wersji zapoznawczej.

## <a name="group-writeback"></a>Zapisywanie zwrotne grup
Opcja zapisywania grup w funkcjach opcjonalnych umożliwia zapisywanie **grup usługi Office 365** do lasu z zainstalowaną programem Exchange. Jest to grupa, która jest zawsze opanowana w chmurze. Jeśli masz program Exchange lokalnie, możesz zapisywać te grupy do lokalnych grup, aby użytkownicy z lokalną skrzynką pocztową programu Exchange mogli wysyłać i odbierać wiadomości e-mail z tych grup.

Więcej informacji na temat grup usługi Office 365 i sposobu ich używania można znaleźć [tutaj](https://aka.ms/O365g).

Grupa usługi Office 365 jest reprezentowana jako grupa dystrybucyjna w lokalnych usługach AD DS. Lokalny serwer exchange musi znajdować się w zbiorczej aktualizacji 8 programu Exchange 2013 (wydanej w marcu 2015 r.) lub programie Exchange 2016, aby rozpoznać ten nowy typ grupy.

**Notatki podczas podglądu**

* Atrybut książki adresowej nie jest obecnie wypełniany w wersji zapoznawczej. Bez tego atrybutu grupa nie jest widoczna w gal. Najprostszym sposobem wypełniania tego atrybutu jest użycie polecenia cmdlet `update-recipient`programu Exchange PowerShell .
* Tylko lasy ze schematem programu Exchange są prawidłowymi obiektami docelowymi dla grup. Jeśli program Exchange nie został wykryty, nie można włączyć stornia grupy.
* Obecnie obsługiwane są tylko wdrożenia organizacji programu Exchange z jednym lasem. Jeśli masz więcej niż jedną organizację programu Exchange lokalnie, potrzebujesz lokalnego rozwiązania GALSync, aby te grupy były wyświetlane w innych lasach.
* Funkcja stornia grupowego nie obsługuje grup zabezpieczeń ani grup dystrybucyjnych.

> [!NOTE]
> Subskrypcja usługi Azure AD Premium jest wymagana do zapisywania grup.
> 
>

## <a name="user-writeback"></a>Odpisy użytkownika
> [!IMPORTANT]
> Funkcja podglądu stornia zwrotnego użytkownika została usunięta w aktualizacji z sierpnia 2015 r. do usługi Azure AD Connect. Jeśli włączono go, należy wyłączyć tę funkcję.
>
>

## <a name="next-steps"></a>Następne kroki
Kontynuuj [niestandardową instalację usługi Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
