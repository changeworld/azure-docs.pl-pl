---
title: Co to jest Agent programu Azure AD Connect administratora — program Azure AD Connect | Dokumentacja firmy Microsoft
description: Opisuje narzędzia używane do synchronizowania i monitorowania środowiska lokalnego w usłudze Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab3fff4294b4cda3d1554ef2761d3f4acaca35
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687252"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co to jest agent administratora programu Azure AD Connect? 
Agent programu Azure AD Connect administracji jest nowym składnikiem programu Azure Active Directory Connect zainstalowanego na serwerze usługi Azure Active Directory Connect. Służy do zbierania określonych danych ze środowiska usługi Active Directory, która pomaga pomocy technicznej inżynier ds. Aby rozwiązać problemy podczas otwierania zgłoszenia do pomocy technicznej firmy Microsoft. 

>[!NOTE]
>Agent administracyjny nie jest zainstalowane i jest domyślnie włączone.  W celu zbierania danych, która pomaga w przypadkach pomocy technicznej, należy zainstalować agenta.

Po zainstalowaniu czeka Agent Azure AD Connect administracji dla określonych żądań dla danych z usługi Azure Active Directory, pobiera żądanych danych ze środowiska synchronizacji i wysyła je do usługi Azure Active Directory, gdzie są one przedstawiane przez firmę Microsoft pomocy technicznej inżynier ds. 

Informacje, które Agent programu Azure AD Connect administracyjnej pobiera ze środowiska nie są przechowywane w jakikolwiek sposób — tylko jest wyświetlany ze specjalistą pomocy technicznej firmy Microsoft w celu udzielenia mu pomocy w wykrywaniu i rozwiązywaniu problemów w usłudze Azure Active Directory Connect Domyślnie zgłoszenie do pomocy technicznej powiązane otwarcia agenta programu Azure AD Connect administracyjną nie jest zainstalowany serwerze programu platformy Azure AD Connect. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Instalowanie agenta programu Azure AD Connect administracyjną na serwerze programu Azure AD Connect 
Agent Azure AD Connect administracji pliki binarne są umieszczane na serwerze usługi AAD Connect. Aby zainstalować agenta, wykonaj następujące czynności: 



1. Otwórz program powershell w trybie administratora 
2. Przejdź do katalogu, w którym się znajduje się dysk cd "C:\Program Files\Microsoft Azure Active Directory Connect\SetupFiles" aplikacja 
3. Uruchom aplikację AADConnectAdminAgentSetup.exe 
 
Po wyświetleniu monitu wprowadź swoje poświadczenia administratora globalnego usługi Azure AD. 

>[!NOTE]
>Jest to znany problem gdzie pojawi się monit o podanie poświadczeń wielokrotnie. Ten problem zostanie rozwiązany w następnej wersji.

Po zainstalowaniu agenta zobaczysz następujące dwa nowe programy na liście "Dodaj lub usuń programy" w Panelu sterowania na serwerze: 

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jakie dane w mojej usłudze synchronizacji jest wyświetlany jako inżynier usług firmy Microsoft? 
Podczas otwierania zgłoszenia do pomocy technicznej inżynier pomocy technicznej firmy Microsoft można wyświetlić, dla danego użytkownika, odpowiednie dane w usłudze Active Directory, obszaru łącznika usługi Active Directory na serwerze usługi Azure Active Directory Connect obszaru łącznika usługi Azure Active Directory na platformie Azure Serwer Active Directory Connect i Metaverse na serwerze usługi Azure Active Directory Connect. 

Ze specjalistą pomocy technicznej firmy Microsoft nie można zmienić żadnych danych w systemie może zobaczyć i hasła. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co zrobić, jeśli nie ma ze specjalistą pomocy technicznej firmy Microsoft na dostęp do moich danych? 
Po zainstalowaniu agenta, jeśli nie chcesz, inżynier usług firmy Microsoft oraz dostęp do danych dla pomocy technicznej, możesz wyłączyć funkcje, modyfikując plik konfiguracji usługi, zgodnie z poniższym opisem: 

1.  Otwórz **C:\Program Files\Microsoft Azure AD Connect administracji Agent\AzureADConnectAdministrationAgentService.exe.config** w Notatniku.
2.  Wyłącz **UserDataEnabled** ustawienia, jak pokazano poniżej. Jeśli **UserDataEnabled** ustawienie istnieje i jest ustawiona na wartość true, a następnie ustaw ją na false. Jeśli ustawienie nie istnieje, Dodaj ustawienie jak pokazano poniżej.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Zapisz plik konfiguracji.
4.  Uruchom ponownie usługę Agent Azure AD Connect administracji, jak pokazano poniżej

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
