---
title: 'Program Azure AD Connect: Funkcje w wersji zapoznawczej | Dokumentacja firmy Microsoft'
description: W tym temacie opisano więcej szczegółów funkcji, które są dostępne w programie Azure AD Connect w wersji zapoznawczej.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347808"
---
# <a name="more-details-about-features-in-preview"></a>Więcej szczegółów na temat funkcji w wersji zapoznawczej
W tym temacie opisano sposób użycia funkcji, obecnie w wersji zapoznawczej.

## <a name="group-writeback"></a>Zapisywanie zwrotne grup
Opcja dla zapisu zwrotnego grup w funkcje opcjonalne służy do zapisywania zwrotnego **grup usługi Office 365** do lasu za pomocą programu Exchange jest zainstalowana. Jest to grupa, która zawsze format zarządzany w chmurze. Jeśli masz dla lokalnego programu Exchange, możesz napisać ponownie te grupy do serwera lokalnego, wysyłanie i odbieranie wiadomości e-mail z tych grup użytkowników z skrzynki pocztowej programu Exchange w środowisku lokalnym.

Więcej informacji na temat grup usługi Office 365 i sposób ich użycia można znaleźć [tutaj](https://aka.ms/O365g).

Grupy usługi Office 365 jest reprezentowany jako grupę dystrybucyjną w lokalnych usługach AD DS. W środowisku lokalnym serwerem Exchange musi być na aktualizację zbiorczą programu Exchange 2013 8 (wydanej w marcu 2015) lub Exchange 2016, rozpoznawał ten nowy typ grupy.

**Informacje o wersji zapoznawczej**

* Atrybut książki adresu jest obecnie pusta w wersji zapoznawczej. Bez tego atrybutu grupy nie jest widoczna w globalnej. Najprostszym sposobem wypełnienia tego atrybutu jest użycie polecenia cmdlet programu PowerShell programu Exchange `update-recipient`.
* Tylko lasy ze schematem Exchange są prawidłowe obiekty docelowe dla grup. Jeśli Exchange nie zostało wykryte, zapisywanie zwrotne grup nie jest możliwość włączenia.
* Obecnie obsługiwane są tylko jednym lasem wdrożeń organizacji programu Exchange. Jeśli masz więcej niż jedną organizację dla lokalnego programu Exchange, należy to rozwiązanie GALSync lokalnych dla tych grup, które będą wyświetlane na innych lasów usługi.
* Nie obsługuje funkcji zapisywania zwrotnego grup, grup zabezpieczeń lub grup dystrybucji.

> [!NOTE]
> Subskrypcja usługi Azure AD Premium jest wymagana dla zapisu zwrotnego grup.
> 
>

## <a name="user-writeback"></a>Zapis zwrotny użytkowników
> [!IMPORTANT]
> Funkcja w wersji zapoznawczej funkcji zapisywania zwrotnego użytkownika zostało usunięte w aktualizacji z sierpnia 2015 do programu Azure AD Connect. Jeśli została włączona, należy wyłączyć tę funkcję.
>
>

## <a name="next-steps"></a>Kolejne kroki
Kontynuuj swoje [instalację niestandardową programu Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
