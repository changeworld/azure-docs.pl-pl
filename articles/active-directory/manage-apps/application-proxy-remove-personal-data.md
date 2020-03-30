---
title: Usuwanie danych osobowych — serwer proxy aplikacji usługi Azure Active Directory
description: Usuwanie danych osobowych z łączników zainstalowanych na urządzeniach dla serwera proxy aplikacji usługi Azure Active Directory.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe97956d99bd5c677e499b532ef85a1bb4d324ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275408"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Usuwanie danych osobowych dla serwera proxy aplikacji usługi Azure Active Directory

Usługa Azure Active Directory Application Proxy wymaga zainstalowania łączników na urządzeniach, co oznacza, że mogą istnieć dane osobowe na urządzeniach. Z tego artykułu dowiesz się, jak usunąć te dane osobowe, aby poprawić prywatność.

## <a name="where-is-the-personal-data"></a>Gdzie są dane osobowe?

Serwer proxy aplikacji może zapisywać dane osobowe w następujących typach dzienników:

- Dzienniki zdarzeń łącznika
- Dzienniki zdarzeń systemu Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Usuwanie danych osobowych z dzienników zdarzeń systemu Windows

Aby uzyskać informacje dotyczące konfigurowania przechowywania danych dla dzienników zdarzeń systemu Windows, zobacz [Ustawienia dzienników zdarzeń](https://technet.microsoft.com/library/cc952132.aspx). Aby dowiedzieć się więcej o dziennikach zdarzeń systemu Windows, zobacz [Korzystanie z dziennika zdarzeń systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Usuwanie danych osobowych z dzienników zdarzeń łącznika

Aby upewnić się, że dzienniki serwera proxy aplikacji nie mają danych osobowych, można:

- Usuwanie lub wyświetlanie danych w razie potrzeby lub
- Wyłącz rejestrowanie

Poniższe sekcje można usunąć dane osobowe z dzienników zdarzeń łącznika. Proces usuwania wszystkich urządzeń, na których jest zainstalowane złącze, należy zakończyć proces usuwania.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Wyświetlanie lub eksportowanie określonych danych

Aby wyświetlić lub wyeksportować określone dane, wyszukaj powiązane wpisy w każdym z dzienników zdarzeń łącznika. Dzienniki znajdują się `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`w .

Ponieważ dzienniki są plikami tekstowymi, można użyć [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) do wyszukiwania wpisów tekstowych związanych z użytkownikiem.  

Aby znaleźć dane osobowe, wyszukaj pliki dziennika userid.

Aby znaleźć dane osobowe zarejestrowane przez aplikację korzystającą z ograniczonego delegowania protokołu Kerberos, wyszukaj następujące składniki typu nazwy użytkownika:

- Lokalna nazwa główna użytkownika
- Nazwa użytkownika w nazwie głównej użytkownika
- Nazwa użytkownika w lokalnej nazwie głównej użytkownika
- Nazwa konta lokalnego menedżera kont zabezpieczeń (SAM)

### <a name="delete-specific-data"></a>Usuwanie określonych danych

Aby usunąć określone dane:

1. Uruchom ponownie usługę Łącznik proxy aplikacji usługi Microsoft Azure AD, aby wygenerować nowy plik dziennika. Nowy plik dziennika umożliwia usunięcie lub zmodyfikowanie starych plików dziennika. 
1. Postępuj zgodnie z opisanej wcześniej [strony procesem Wyświetlania lub eksportowania określonych danych,](#view-or-export-specific-data) aby znaleźć informacje, które należy usunąć. Przeszukaj wszystkie dzienniki łączników.
1. Usuń odpowiednie pliki dziennika lub wybiórczo usuń pola zawierające dane osobowe. Możesz również usunąć wszystkie stare pliki dziennika, jeśli nie są już potrzebne.

### <a name="turn-off-connector-logs"></a>Wyłączanie dzienników złączy

Jedną z opcji, aby upewnić się, że dzienniki łącznika nie zawierają danych osobowych jest wyłączenie generowania dziennika. Aby zatrzymać generowanie dzienników łączników, usuń `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`z pliku .

![Pokazuje fragment kodu z wyróżnionym kodem do usunięcia](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem serwera proxy aplikacji, zobacz [Jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).