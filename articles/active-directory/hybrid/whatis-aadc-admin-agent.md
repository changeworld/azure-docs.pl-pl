---
title: Co to jest agent administracyjny usługi Azure AD Connect — Usługa Azure AD Connect | Dokumenty firmy Microsoft
description: Opisuje narzędzia używane do synchronizowania i monitorowania środowiska lokalnego w usłudze Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f68635820125161ed4f5777e27a20de9e6fbe8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049392"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co to jest agent administratora programu Azure AD Connect? 
Agent administracyjny usługi Azure AD Connect to nowy składnik usługi Azure Active Directory Connect, który można zainstalować na serwerze usługi Azure Active Directory Connect. Służy do zbierania określonych danych ze środowiska usługi Active Directory, który pomaga inżynierowi pomocy technicznej firmy Microsoft w rozwiązywaniu problemów podczas otwierania przypadku pomocy technicznej. 

>[!NOTE]
>Agent administracyjny nie jest zainstalowany i domyślnie włączony.  Należy zainstalować agenta w celu zbierania danych, aby pomóc w przypadkach pomocy technicznej.

Po zainstalowaniu agent administracyjny usługi Azure AD Connect czeka na określone żądania danych z usługi Azure Active Directory, pobiera żądane dane ze środowiska synchronizacji i wysyła je do usługi Azure Active Directory, gdzie są prezentowane do pomocy technicznej firmy Microsoft Inżynier. 

Informacje pobierane przez agenta administracyjnego usługi Azure AD Connect ze środowiska nie są w żaden sposób przechowywane — są wyświetlane tylko inżynierowi pomocy technicznej firmy Microsoft, aby pomóc im w badaniu i rozwiązywaniu problemów z usługą Azure Active Directory Connect przypadku pomocy technicznej pokrewnego, który został otwarty Agent administracyjny usługi Azure AD Connect nie jest zainstalowany na serwerze Azure AD Connect Server domyślnie. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalowanie agenta administracyjnego usługi Azure AD Connect na serwerze usługi Azure AD Connect 

Wymagania wstępne:
1.    Usługa Azure AD Connect jest zainstalowana na serwerze
2.    Usługa Azure AD Connect Health jest zainstalowana na serwerze

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent0.png)

Pliki binarne agenta administracyjnego usługi Azure AD Connect są umieszczane na serwerze AAD Connect. Aby zainstalować agenta, wykonaj następujące czynności:

1.    Otwieranie programu PowerShell w trybie administracyjnym
2.    Przejdź do katalogu, w którym znajduje się aplikacja cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    Uruchom pozycję Konfiguruj programSminAgent.ps1

Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego usługi Azure AD. Powinny to być te same poświadczenia wprowadzone podczas instalacji usługi Azure AD Connect.

Po zainstalowaniu agenta na liście "Dodaj/usuń programy" w Panelu sterowania serwera zobaczysz następujące dwa nowe programy: 

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jakie dane w mojej usłudze synchronizacji są wyświetlane inżynierowi serwisowi firmy Microsoft? 
Po otwarciu przypadku pomocy technicznej inżynier pomocy technicznej firmy Microsoft może zobaczyć odpowiednie dane w usłudze Active Directory, obszar łącznika usługi Active Directory na serwerze Azure Active Directory Connect, obszar łącznika usługi Azure Active Directory na platformie Azure Serwer Active Directory Connect i metaverse na serwerze Azure Active Directory Connect. 

Inżynier pomocy technicznej firmy Microsoft nie może zmienić żadnych danych w systemie i nie widzi żadnych haseł. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co zrobić, jeśli nie chcę, aby inżynier pomocy technicznej firmy Microsoft miał dostęp do moich danych? 
Po zainstalowaniu agenta, Jeśli nie chcesz, aby inżynier serwisu firmy Microsoft, aby uzyskać dostęp do danych dla wywołania pomocy technicznej, można wyłączyć funkcjonalność, modyfikując plik konfiguracji usługi, jak opisano poniżej: 

1.    Otwórz **program C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** w notatniku.
2.    Wyłącz ustawienie **UserDataEnabled,** jak pokazano poniżej. Jeśli **userDataEnabled** ustawienie istnieje i jest ustawiona na true, a następnie ustawić go na false. Jeśli ustawienie nie istnieje, dodaj ustawienie, jak pokazano poniżej.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.    Zapisz plik konfiguracyjny.
4.    Uruchom ponownie usługę agenta administracyjnego usługi Azure AD Connect, jak pokazano poniżej

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
