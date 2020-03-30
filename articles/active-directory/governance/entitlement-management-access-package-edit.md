---
title: Ukrywanie lub usuwanie pakietu dostępu w zarządzaniu uprawnieniami — usługa Azure AD
description: Dowiedz się, jak ukryć lub usunąć pakiet dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
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
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261998"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Ukrywanie lub usuwanie pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Pakiety dostępu są domyślnie wykrywalne. Oznacza to, że jeśli zasady zezwalają użytkownikowi na żądanie pakietu dostępu, automatycznie zobaczą pakiet dostępu wymieniony w portalu Mój dostęp. Można jednak zmienić ustawienie **Ukryte,** tak aby pakiet dostępu nie był wymieniony w portalu Mój dostęp użytkownika.

W tym artykule opisano sposób ukrywania lub usuwania pakietu dostępu.

## <a name="change-the-hidden-setting"></a>Zmienianie ustawienia Ukryte

Wykonaj następujące kroki, aby zmienić **ukryte** ustawienie dla pakietu dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd kliknij pozycję **Edytuj**.

1. Ustaw ustawienie **Ukryte.**

    Jeśli ustawiona jest liczba **nie,** pakiet dostępu zostanie wyświetlony w portalu Mój dostęp użytkownika.

    Jeśli ustawiona jest opcja **Tak,** pakiet dostępu nie zostanie wyświetlony w portalu Mój dostęp użytkownika. Jedynym sposobem, w jaki użytkownik może wyświetlić pakiet dostępu, jest to, że ma **bezpośrednie łącze portalu Mój dostęp** do pakietu dostępu. Aby uzyskać więcej informacji, zobacz [Udostępnianie łącza, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Usuwanie pakietu dostępu

Pakiet dostępu można usunąć tylko wtedy, gdy nie ma aktywnych przypisań użytkowników. Wykonaj następujące kroki, aby usunąć pakiet dostępu.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **Przydziały** i usuń dostęp dla wszystkich użytkowników.

1. W menu po lewej stronie kliknij pozycję **Przegląd,** a następnie kliknij pozycję **Usuń**.

1. W wyświetlonym komunikacie o usunięciu kliknij przycisk **Tak**.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie, dodawanie i usuwanie przypisań dla pakietu dostępu](entitlement-management-access-package-assignments.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)
