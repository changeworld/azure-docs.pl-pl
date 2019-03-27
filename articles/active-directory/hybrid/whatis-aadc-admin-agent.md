---
title: Co to jest Agent programu Azure AD Connect administratora — program Azure AD Connect | Dokumentacja firmy Microsoft
description: Opisuje narzędzia używane do synchronizowania i monitorowania środowiska lokalnego w usłudze Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b99b7b03898ad72b4d9c7cc2ba9154855475ef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488405"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Co to jest Agent programu Azure AD Connect administratora? 
Agent programu Azure AD Connect administracji jest nowym składnikiem programu Azure Active Directory Connect zainstalowanego na serwerze usługi Azure Active Directory Connect. Służy do zbierania określonych danych ze środowiska usługi Active Directory, która pomaga pomocy technicznej inżynier ds. Aby rozwiązać problemy podczas otwierania zgłoszenia do pomocy technicznej firmy Microsoft.

Po zainstalowaniu czeka Agent Azure AD Connect administracji dla określonych żądań dla danych z usługi Azure Active Directory, pobiera żądanych danych ze środowiska synchronizacji, a następnie wysyła je do usługi Azure Active Directory, gdzie są one przedstawiane przez firmę Microsoft pomocy technicznej inżynier ds.

Informacje, które Agent programu Azure AD Connect administracyjnej pobiera ze środowiska nie są przechowywane w jakikolwiek sposób — tylko jest wyświetlany ze specjalistą pomocy technicznej firmy Microsoft w celu udzielenia mu pomocy w wykrywaniu i rozwiązywaniu problemów w usłudze Azure Active Directory Connect zgłoszenie do pomocy technicznej powiązane, który został otwarty

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Jak Agent programu Azure AD Connect administratora jest zainstalowany na serwerze usługi Azure AD Connect? 
Po zainstalowaniu agenta administratora, zostanie wyświetlone następujące dwa nowe programy na liście "Dodaj lub usuń programy" w Panelu sterowania na serwerze: 

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent1.png)

Nie zostaje usunięte ani cofnąć zainstalować te programy są one krytyczny element instalacji programu Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Jakie dane w mojej usłudze synchronizacji jest wyświetlany jako inżynier usług firmy Microsoft?
Podczas otwierania zgłoszenia do pomocy technicznej, inżynier pomocy technicznej firmy Microsoft można wyświetlić, dla danego użytkownika, odpowiednie dane w usłudze Active Directory, obszaru łącznika usługi Active Directory na serwerze usługi Azure Active Directory Connect obszaru łącznika usługi Azure Active Directory na platformie Azure Serwer Active Directory Connect i Metaverse na serwerze usługi Azure Active Directory Connect.

Ze specjalistą pomocy technicznej firmy Microsoft nie można zmienić żadnych danych w systemie może zobaczyć i hasła.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Co zrobić, jeśli nie ma ze specjalistą pomocy technicznej firmy Microsoft na dostęp do moich danych? 
 
Jeśli nie chcesz, inżynier usług firmy Microsoft oraz dostęp do danych dla wywołania pomocy technicznej, może to wskazywać, po otwarciu obsługi wywołań w portalu: 

  1.    Otwórz **C:\Program Files\Microsoft Azure AD Connect administracji Agent\AzureADConnectAdministrationAgentService.exe.config** w Notatniku.
  2.    Wyłącz **UserDataEnabled** ustawienia, jak pokazano poniżej. Jeśli **UserDataEnabled** ustawienie istnieje i jest ustawiona na wartość true, a następnie ustaw ją na false. Jeśli ustawienie nie istnieje, Dodaj ustawienie jak pokazano poniżej.    
  `
 <appSettings>
   <add key="TraceFilename" value="ADAdministrationAgent.log" />
   <add key="UserDataEnabled" value="false" />
  </appSettings>
  `
  3.    Zapisz plik konfiguracji.
  4.    Uruchom ponownie usługę Agent Azure AD Connect administracji, jak pokazano poniżej

![agent administracyjny](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).