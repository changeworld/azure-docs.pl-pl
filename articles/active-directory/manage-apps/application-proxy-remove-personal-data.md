---
title: Usuwanie danych osobowych — serwer Proxy usługi Azure Active Directory Application | Dokumentacja firmy Microsoft
description: Usuń dane osobowe z zainstalowanych na urządzeniach dla serwera Proxy usługi Azure Active Directory Application łączników.
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5f3c1883f156562cfab59cb102fb0cf18b03803
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292866"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Usuwanie danych osobowych dla serwera Proxy usługi Azure Active Directory aplikacji  

Usługa Azure Active Directory serwera Proxy aplikacji wymaga zainstalowania łączników na urządzeniach, co oznacza, że może być dane osobowe na urządzeniach. Ten artykuł zawiera instrukcje dotyczące sposobu usunięcia tego danych osobowych w celu ochrony prywatności. 


## <a name="where-is-the-personal-data"></a>Gdzie jest dane osobowe?
Możliwe jest, serwer Proxy aplikacji do zapisania danych osobowych przez następujące typy dzienników:

- Dzienniki zdarzeń łącznika
- Dzienniki zdarzeń systemu Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Usuwanie danych osobowych z dzienników zdarzeń Windows

Aby uzyskać informacje na temat konfigurowania przechowywanie danych dzienników zdarzeń Windows, zobacz [ustawienia dzienników zdarzeń](https://technet.microsoft.com/library/cc952132.aspx). Aby dowiedzieć się więcej na temat dzienników zdarzeń Windows, zobacz [przy użyciu dziennika zdarzeń Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Usuwanie danych osobowych z dzienników zdarzeń łącznika

Aby upewnić się, dzienniki serwera Proxy aplikacji nie ma danych osobistych, można:

- Usuń lub wyświetlanie danych w razie potrzeby, lub
- Wyłącz rejestrowanie

Poniższe sekcje umożliwia usuwanie danych osobowych z dzienników zdarzeń łącznika. Należy wykonać proces usuwania dla wszystkich urządzeń, na których zainstalowano łącznik.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Wyświetlanie lub eksportowanie danych specyficznych dla

Aby wyświetlić lub eksportowania określonych danych, wyszukiwanie powiązane wpisy we wszystkich dziennikach zdarzeń łącznika. Dzienniki znajdują się w lokalizacji `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Ponieważ dzienniki są plikami tekstowymi, możesz użyć [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) szukać wpisów tekstowych skojarzoną z użytkownikiem.  

Umożliwiające znalezienie danych osobowych, Wyszukaj pliki dziennika na identyfikator użytkownika. 

Aby znaleźć dane osobowe rejestrowane przez aplikację, która korzysta z ograniczonego delegowania protokołu Kerberos, wyszukaj te składniki wpisz nazwę użytkownika:

- Główna nazwa użytkownika lokalnego
- Część nazwy użytkownika głównej nazwy użytkownika
- Część nazwy użytkownika głównej nazwy użytkownika lokalnego
- Nazwa konta Menedżera (kont zabezpieczeń SAM) kont zabezpieczeń w środowisku lokalnym 


### <a name="delete-specific-data"></a>Usuń określone dane

Aby usunąć szczegółowe dane:

1. Uruchom ponownie usługę łącznika serwera Proxy w aplikacji pakietu Microsoft Azure AD, aby wygenerować nowy plik dziennika. Nowy plik dziennika można usunąć ani zmodyfikować stare pliki dziennika. 
2. Postępuj zgodnie z [widoku lub eksportowania określonych danych](#view-or-export-specific-data) proces opisany wcześniej w celu znalezienia informacji, które musi zostać usunięty. Wyszukaj wszystkie dzienniki łącznika.
3. Usuń odpowiednich plików dziennika lub selektywnie usunąć pola, które zawierają dane osobowe. Możesz także usunąć wszystkie stare pliki dziennika, jeśli użytkownik nie są już potrzebne.

### <a name="turn-off-connector-logs"></a>Wyłącz łącznik dzienników

Jedną opcję, aby upewnić się, dzienniki programu connector nie zawierają danych osobowych jest Wyłącz Generowanie dziennika. Aby zatrzymać generowanie dzienników łącznika, należy usunąć następujący wyróżniony wiersz z `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfigurowanie](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie serwera Proxy aplikacji, zobacz [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).

