---
title: Udostępnianie łącza w celu żądania pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak udostępnić łącze, aby zażądać pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
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
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968766"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Udostępnij łącze, aby zażądać pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Większość użytkowników w katalogu może zalogować się do portalu Mój dostęp i automatycznie wyświetlić listę pakietów dostępu, których mogą zażądać. Jednak dla użytkowników zewnętrznych partnerów biznesowych, którzy nie są jeszcze w katalogu, należy wysłać im łącze, którego mogą użyć do żądania pakietu dostępu. 

Tak długo, jak katalog dla pakietu dostępu jest [włączony dla użytkowników zewnętrznych](entitlement-management-catalog-create.md) i masz zasady dla katalogu użytkownika zewnętrznego, użytkownik [zewnętrzny](entitlement-management-access-package-request-policy.md)może użyć łącza Portal Mój dostęp, aby zażądać pakietu dostępu.

## <a name="share-link-to-request-an-access-package"></a>Udostępnij łącze, aby zażądać pakietu dostępu

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Na stronie Przegląd skopiuj **łącze Mój portal programu Access**.

    ![Omówienie pakietu dostępu — łącze z portalem Mój dostęp](./media/entitlement-management-shared/my-access-portal-link.png)

    Ważne jest, aby skopiować całe łącze portalu Mój dostęp podczas wysyłania go do wewnętrznego partnera handlowego. Gwarantuje to, że partner uzyska dostęp do portalu katalogu, aby złożyć wniosek. Łącze rozpoczyna `myaccess`się od , zawiera wskazówkę katalogu, a kończy się identyfikatorem pakietu dostępu.  (Dla instytucji rządowych stanów Zjednoczonych domeną `myaccess.microsoft.us`w łączu portalu Mój dostęp będzie domena .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Wyślij wiadomość e-mail lub wyślij link do zewnętrznego partnera biznesowego. Mogą udostępnić łącze swoim użytkownikom, aby zażądać pakietu dostępu.

## <a name="next-steps"></a>Następne kroki

- [Żądanie dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Zatwierdzanie lub odmawianie żądań dostępu](entitlement-management-request-approve.md)