---
title: Rozwiązywanie problemów z błędami interfejsu API raportowania usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Umożliwia rozwiązanie problemu dotyczącego błędów podczas wywoływania usługi Azure Active Directory interfejsy API raportowania usługi.
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
ms.openlocfilehash: 8b517204fb650020bdebf8172186f30fff58f722
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60285021"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Rozwiązywanie problemów z błędami interfejsu API raportowania usługi Azure Active Directory

W tym artykule wymieniono typowe komunikaty o błędach, które mogą występować podczas uzyskiwania dostępu do raportów aktywności przy użyciu interfejsu API programu Graph MS i kroki do ich rozwiązania.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP wewnętrzny błąd serwera podczas uzyskiwania dostępu do punktu końcowego programu Microsoft Graph w wersji 2

Obecnie obsługujemy punktu końcowego v2 programu Microsoft Graph — upewnij się, że dostęp do dzienników aktywności, przy użyciu punktu końcowego v1 programu Microsoft Graph.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Błąd: Nie można pobrać ról użytkownika z programu Graph usługi AD

Może wystąpić ten komunikat o błędzie podczas próby dostępu do logowania za pomocą Eksploratora programu Graph. Upewnij się, że użytkownik jest zalogowany na koncie przy użyciu zarówno przycisków logowania w Interfejsie użytkownika programu Graph Explorer, jak pokazano na poniższej ilustracji. 

![Eksplorator programu Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Błąd: Nie można wykonać sprawdzenia licencji premium z programu Graph usługi AD 

Jeśli napotkasz ten komunikat o błędzie podczas próby uzyskania dostępu do logowania za pomocą Eksploratora programu Graph, wybierz **zmodyfikować uprawnienia** poniżej swoje konto w lewym okienku nawigacji, a następnie wybierz **Tasks.ReadWrite** i **Directory.Read.All**. 

![Modyfikowanie uprawnień interfejsu użytkownika](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: Dzierżawa jest B2C ani dzierżawa nie ma licencji premium

Uzyskiwanie dostępu do raportów logowania wymaga usługi Azure Active Directory — wersja premium 1 (P1) licencji. Jeśli widzisz ten komunikat o błędzie podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawy ma licencję z licencją Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Błąd: Użytkownik nie jest dozwolone role 

Jeśli widzisz ten komunikat o błędzie podczas próby uzyskania dostępu do dzienników inspekcji lub logowania za pomocą interfejsu API upewnij się, że Twoje konto jest częścią **Czytelnik zabezpieczeń** lub **czytelnika raportów** roli w usłudze Azure Active Directory Dzierżawca. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: Brak uprawnień Czytaj dane katalogu usługi AAD aplikacji 

Wykonaj poniższe kroki w [wymagania wstępne dotyczące dostępu do usługi Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) aby upewnić się, aplikacja jest uruchomiona przy użyciu odpowiedniego zestawu uprawnień. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Błąd: Brak uprawnień "Odczytywać wszystkie dane dziennika inspekcji" interfejsu API w żądaniach programu MSGraph aplikacji

Wykonaj poniższe kroki w [wymagania wstępne dotyczące dostępu do usługi Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md) aby upewnić się, aplikacja jest uruchomiona przy użyciu odpowiedniego zestawu uprawnień. 

## <a name="next-steps"></a>Następne kroki

[Użyj inspekcji, dokumentacja interfejsu API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[użycia odwołania raportu interfejsu API logowań](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
