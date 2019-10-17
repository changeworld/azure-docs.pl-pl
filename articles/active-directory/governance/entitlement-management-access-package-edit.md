---
title: Ukrywanie lub usuwanie pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak ukryć lub usunąć pakiet dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab6f3a86d8e88ee2d7465fd32bde7b7555f1f326
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389170"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management-preview"></a>Ukrywanie lub usuwanie pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pakiety dostępu są domyślnie odnajdywane. Oznacza to, że jeśli zasady zezwalają użytkownikowi na żądanie pakietu dostępu, automatycznie zobaczą pakiet dostępu wymieniony w portalu mojego dostępu. Ustawienie **ukrywane** można jednak zmienić tak, aby pakiet dostępu nie był wyświetlany na liście Portal użytkownika mojego dostępu.

W tym artykule opisano sposób ukrywania lub usuwania pakietu dostępu.

## <a name="change-the-hidden-setting"></a>Zmień ustawienie ukrywane

Wykonaj następujące kroki, aby zmienić ustawienie **ukryte** dla pakietu dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd kliknij przycisk **Edytuj**.

1. Ustaw ustawienie **ukryte** .

    Jeśli ustawisz wartość **nie**, pakiet dostępu zostanie wyświetlony w portalu dostępu użytkownika.

    W przypadku wybrania **opcji tak**pakiet dostępu nie będzie wyświetlany w portalu dostępu użytkownika. Jedynym sposobem wyświetlania pakietu dostępu przez użytkownika jest to, że użytkownik ma **link bezpośredniego dostępu Portal** do pakietu dostępu. Aby uzyskać więcej informacji, zobacz [udostępnianie linku do żądania pakietu dostępu](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Usuwanie pakietu dostępu

Pakiet dostępu można usunąć tylko wtedy, gdy nie ma aktywnych przypisań użytkownika. Wykonaj następujące kroki, aby usunąć pakiet dostępu.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania** i Usuń dostęp dla wszystkich użytkowników.

1. W menu po lewej stronie kliknij pozycję **Przegląd** , a następnie kliknij pozycję **Usuń**.

1. W wyświetlonym komunikacie Usuń kliknij przycisk **tak**.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie, Dodawanie i usuwanie przypisań dla pakietu dostępu](entitlement-management-access-package-assignments.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)
