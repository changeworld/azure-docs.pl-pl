---
title: Usuwanie danych osobowych - serwer Proxy usługi Azure Active Directory aplikacji | Dokumentacja firmy Microsoft
description: Usuń dane osobowe z łączników zainstalowane na urządzeniach dla serwera Proxy usługi Azure Active Directory aplikacji.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 94cf0464aca3c46e0c6425b0fb3e24fcd767f95c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655054"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Usuwanie danych osobowych dla serwera Proxy usługi Azure Active Directory aplikacji  

Azure Active Directory serwera Proxy aplikacji wymaga zainstalowania łączników na urządzeniach, co oznacza, że może być danych osobowych na urządzeniach. Ten artykuł zawiera kroki dotyczące sposobu usunięcia tego danych osobowych w celu zwiększenia ochrony prywatności. 


## <a name="where-is-the-personal-data"></a>W przypadku danych osobowych?
Jest to możliwe, że serwer Proxy aplikacji do zapisania danych osobowych do następujących typów dziennika:

- Dzienniki zdarzeń łącznika
- Dzienniki zdarzeń systemu Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Usuń dane osobowe z dzienników zdarzeń systemu Windows

Aby uzyskać informacje na temat konfigurowania przechowywania danych dzienników zdarzeń systemu Windows, temacie [ustawienia dzienników zdarzeń](https://technet.microsoft.com/library/cc952132.aspx). Aby dowiedzieć się więcej na temat dzienniki zdarzeń systemu Windows, temacie [dziennika zdarzeń systemu Windows przy użyciu](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Usuń dane osobowe z dzienników zdarzeń łącznika

Aby zapewnić dzienniki serwera Proxy aplikacji nie danych osobowych, można:

- Usuwanie i wyświetlanie danych w razie potrzeby lub
- Wyłącz rejestrowanie

Poniższe sekcje umożliwiają Usuń dane osobowe z dzienników zdarzeń łącznika. Należy przeprowadzić proces usuwania wszystkich urządzeń, na których jest zainstalowany łącznik.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Widok lub eksportowania określonych danych

Aby wyświetlić lub eksportowania określonych danych, wyszukiwanie powiązane wpisy zarejestrowane w każdym z dzienników zdarzeń łącznika. Dzienniki znajdują się w folderze `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Ponieważ dzienniki są pliki tekstowe, można użyć [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) do wyszukiwania tekstu wpisy związane z użytkownikiem.  

Aby znaleźć dane osobowe, Wyszukaj pliki dziennika na UserID. 

Aby znaleźć dane osobowe rejestrowane przez aplikację, która wykorzystuje ograniczone delegowanie protokołu Kerberos, wyszukaj te składniki wpisz nazwę użytkownika:

- Główna nazwa użytkownika lokalnego
- Część nazwy użytkownika głównej nazwy użytkownika
- Część nazwy użytkownika głównej nazwy użytkownika lokalnego
- Nazwa konta Menedżera (kont zabezpieczeń SAM) kont zabezpieczeń lokalnych 


### <a name="delete-specific-data"></a>Usuwanie określonych danych

Aby usunąć szczegółowe dane:

1. Uruchom ponownie usługę łącznika serwera Proxy w aplikacji pakietu Microsoft Azure AD, aby wygenerować nowy plik dziennika. Nowy plik dziennika umożliwia usunięcia lub zmodyfikowania stare pliki dziennika. 
2. Postępuj zgodnie z [widoku lub eksportowania określonych danych](#view-or-export-specific-data) proces opisany wcześniej można znaleźć informacje, które musi zostać usunięty. Wyszukaj wszystkie dzienniki łącznika.
3. Usuń odpowiednich plików dziennika lub selektywnie usunąć pola, które zawierają dane osobowe. Możesz także usunąć wszystkie stare pliki dziennika, jeśli użytkownik nie są już potrzebne.

### <a name="turn-off-connector-logs"></a>Wyłącz łącznik dzienników

Wyłącz Generowanie dziennika jest jedną opcję, aby zapewnić dzienniki łącznika nie zawierają danych osobowych. Aby zatrzymać generowanie dzienników łącznika, należy usunąć następujący wyróżniony wiersz z `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfigurowanie](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Kolejne kroki

Omówienie serwera proxy aplikacji, zobacz [jak zapewnić bezpieczny zdalny dostęp do aplikacji lokalnych](application-proxy.md).

