---
title: Rozwiązywanie problemów z błędami w interfejsie API raportowania usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Zapewnia rozwiązywanie problemów z błędami podczas wywoływania interfejsów API raportowania usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399280"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API raportowania usługi Azure Active Directory

W tym artykule wymieniono typowe komunikaty o błędach, na które można napotkać podczas uzyskiwania dostępu do raportów aktywności przy użyciu interfejsu API programu Microsoft Graph i kroków dotyczących ich rozwiązania.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 Błąd serwera wewnętrznego HTTP podczas uzyskiwania dostępu do punktu końcowego programu Microsoft Graph V2

Obecnie nie obsługujemy punktu końcowego programu Microsoft Graph w wersji 2 — upewnij się, że uzyskujesz dostęp do dzienników działań przy użyciu punktu końcowego programu Microsoft Graph w wersji 1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: żadna dzierżawa nie jest B2C lub dzierżawca nie ma licencji premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji premium usługi Azure Active Directory 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa jest licencjonowana z licencją usługi Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Błąd: użytkownik nie ma dozwolonych ról 

Jeśli ten komunikat o błędzie jest wyświetlany podczas próby uzyskania dostępu do dzienników inspekcji lub logowań przy użyciu interfejsu API, upewnij się, że twoje konto jest częścią roli **czytnika zabezpieczeń** lub **czytnika raportów** w dzierżawie usługi Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: aplikacja brakuje uprawnienia AAD "Odczyt danych katalogu" 

Wykonaj kroki w [wymaganiach wstępnych, aby uzyskać dostęp do interfejsu API raportowania usługi Azure Active Directory,](howto-configure-prerequisites-for-reporting-api.md) aby upewnić się, że aplikacja jest uruchomiona z odpowiednim zestawem uprawnień. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Błąd: aplikacja brakuje interfejsu API programu Microsoft Graph "Odczyt wszystkich danych dziennika inspekcji"

Wykonaj kroki w [wymaganiach wstępnych, aby uzyskać dostęp do interfejsu API raportowania usługi Azure Active Directory,](howto-configure-prerequisites-for-reporting-api.md) aby upewnić się, że aplikacja jest uruchomiona z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

[Użyj odwołania interfejsu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
API inspekcji[Użyj odwołania interfejsu API raportu aktywności logowania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
