---
title: Rozwiązywanie problemów z błędami w interfejsie API raportowania Azure Active Directory | Microsoft Docs
description: Zapewnia rozwiązanie błędów podczas wywoływania interfejsów API raportowania Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f34dcece9acb20d0db091152b24b26cb9fa2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989531"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API raportowania Azure Active Directory

W tym artykule wymieniono typowe komunikaty o błędach, które można napotkać podczas uzyskiwania dostępu do raportów aktywności przy użyciu programu MS interfejs API programu Graph i kroków dla ich rozwiązania.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Błąd wewnętrzny serwera HTTP 500 podczas uzyskiwania dostępu do punktu końcowego Microsoft Graph v2

Obecnie nie obsługujemy punktu końcowego Microsoft Graph v2 — upewnij się, że uzyskujesz dostęp do dzienników aktywności przy użyciu punktu końcowego Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Błąd: Nie można pobrać ról użytkownika z grafu usługi AD

Ten komunikat o błędzie może pojawić się podczas próby dostępu do logowania za pomocą Eksploratora grafów. Upewnij się, że logujesz się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika programu Graph Explorer, jak pokazano na poniższej ilustracji. 

![Eksplorator programu Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Błąd: Nie można przeprowadzić kontroli licencji Premium na podstawie grafu usługi AD 

Jeśli ten komunikat o błędzie jest uruchamiany podczas próby dostępu do logowania za pomocą Eksploratora grafów, wybierz pozycję **Modyfikuj uprawnienia** pod Twoim kontem na lewym pasku nawigacyjnym, a następnie wybierz kolejno pozycje **Tasks. ReadWrite** i **Directory. Read. All**. 

![Modyfikuj interfejs użytkownika uprawnień](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: Żadna dzierżawa nie jest B2C lub dzierżawa nie ma licencji Premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji Azure Active Directory Premium 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa jest licencjonowana przy użyciu licencji usługi Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Błąd: Użytkownik nie znajduje się w dozwolonych rolach 

Jeśli ten komunikat o błędzie jest wyświetlany podczas próby dostępu do dzienników inspekcji lub logowania przy użyciu interfejsu API, upewnij się, że konto należy do roli **czytelnik zabezpieczeń** lub **czytelnik raportu** w dzierżawie Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: Brak uprawnienia do odczytu danych katalogu usługi AAD w aplikacji 

Wykonaj kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Błąd: Brak uprawnienia MSGraph interfejsu API "Odczyt wszystkich danych dziennika inspekcji" w aplikacji

Wykonaj kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsu API raportowania Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) , aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

[Użyj odwołania](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
API inspekcji,[korzystając z dokumentacji dotyczącej interfejsu API raportów działań związanych](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin) z logowaniem
