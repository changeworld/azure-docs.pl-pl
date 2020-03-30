---
title: Przykłady programu PowerShell dla serwera proxy aplikacji usługi Azure AD
description: Użyj tych przykładów programu PowerShell dla serwera proxy aplikacji usługi Azure AD, aby uzyskać informacje o aplikacjach i łącznikach serwera proxy aplikacji w katalogu, przypisać użytkowników i grupy do aplikacji i uzyskać informacje o certyfikacie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481266"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Przykłady programu Azure AD PowerShell dla serwera proxy aplikacji usługi Azure AD

Poniższa tabela zawiera łącza do przykładów skryptów programu PowerShell dla serwera proxy aplikacji usługi Azure AD. Te przykłady wymagają [modułu AzureAD V2 PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) lub [wersjiór preview modułu AzureAD V2 dla modułu Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), chyba że zaznaczono inaczej.


Aby uzyskać więcej informacji na temat poleceń cmdlet używanych w tych przykładach, zobacz [Zarządzanie aplikacjami serwera proxy aplikacji](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) i zarządzanie [łącznikami serwera proxy aplikacji](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management).

| | |
|---|---|
|**Aplikacje proxy aplikacji**||
| [Lista podstawowych informacji dla wszystkich aplikacji proxy aplikacji](scripts/powershell-get-all-app-proxy-apps-basic.md) | Wyświetla listę podstawowych informacji (AppId, DisplayName, ObjId) dotyczących wszystkich aplikacji proxy aplikacji w katalogu . |
| [Lista rozszerzonych informacji dla wszystkich aplikacji proxy aplikacji](scripts/powershell-get-all-app-proxy-apps-extended.md) | Wyświetla listę rozszerzonych informacji (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType) dotyczących wszystkich aplikacji proxy aplikacji w katalogu.  |
| [Lista wszystkich aplikacji proxy aplikacji według grupy łączników](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | Wyświetla informacje o wszystkich aplikacjach proxy aplikacji w katalogu i grupach łączników, do których są przypisane aplikacje. |
| [Pobierz wszystkie aplikacje proxy aplikacji z zasadami okresu istnienia tokenu](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | Wyświetla listę wszystkich aplikacji proxy aplikacji w katalogu z zasadami okresu istnienia tokenu i jego szczegółami. Ten przykład wymaga [wersji zapoznawczej modułu AzureAD V2 PowerShell for Graph.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) |
|**Grupy łączników**||
| [Pobierz wszystkie grupy łączników i łączniki w katalogu](scripts/powershell-get-all-connectors.md) | Wyświetla listę wszystkich grup łączników i łączników w katalogu. |
| [Przenoszenie wszystkich aplikacji przypisanych do grupy łączników do innej grupy łączników](scripts/powershell-move-all-apps-to-connector-group.md) | Przenosi wszystkie aplikacje aktualnie przypisane do grupy łączników do innej grupy łączników. |
|**Przypisani użytkownicy i grupa**||
| [Wyświetlanie użytkowników i grup przypisanych do aplikacji proxy aplikacji](scripts/powershell-display-users-group-of-app.md) | Wyświetla listę użytkowników i grup przypisanych do określonej aplikacji serwera proxy aplikacji. |
| [Przypisywanie użytkownika do aplikacji](scripts/powershell-assign-user-to-app.md) | Przypisuje określonego użytkownika do aplikacji. |
| [Przypisywanie grupy do aplikacji](scripts/powershell-assign-group-to-app.md) | Przypisuje określoną grupę do aplikacji. |
|**Konfiguracja zewnętrznego adresu URL**||
| [Pobierz wszystkie aplikacje proxy aplikacji przy użyciu domen domyślnych (msappproxy.net)](scripts/powershell-get-all-default-domain-apps.md)  | Wyświetla listę wszystkich aplikacji proxy aplikacji przy użyciu domen domyślnych (msappproxy.net). |
| [Pobierz wszystkie aplikacje proxy aplikacji przy użyciu publikowania symboli wieloznacznych](scripts/powershell-get-all-wildcard-apps.md) | Wyświetla listę wszystkich aplikacji proxy aplikacji przy użyciu publikowania symboli wieloznacznych. |
|**Konfiguracja domeny niestandardowej**||
| [Pobierz wszystkie aplikacje proxy aplikacji przy użyciu domen niestandardowych i informacji o certyfikatach](scripts/powershell-get-all-custom-domains-and-certs.md) | Wyświetla listę wszystkich aplikacji proxy aplikacji, które używają domen niestandardowych i informacji o certyfikatach skojarzonych z domenami niestandardowymi. |
| [Pobierz wszystkie aplikacje usługi Azure AD Proxy opublikowane bez przekazywania certyfikatu](scripts/powershell-get-all-custom-domain-no-cert.md) | Wyświetla listę wszystkich aplikacji proxy aplikacji, które używają domen niestandardowych, ale nie mają prawidłowego certyfikatu TLS/SSL przekazanego. |
| [Pobierz wszystkie aplikacje usługi Azure AD Proxy opublikowane przy identycznym certyfikacie](scripts/powershell-get-custom-domain-identical-cert.md) | Wyświetla listę wszystkich aplikacji usługi Azure AD Proxy opublikowanych przy identycznym certyfikacie. |
| [Pobierz wszystkie aplikacje usługi Azure AD Proxy opublikowane przy identycznym certyfikacie i zastąp je](scripts/powershell-get-custom-domain-replace-cert.md) | W przypadku aplikacji usługi Azure AD Proxy, które są publikowane przy zastosowaniu identycznego certyfikatu, można zastąpić certyfikat zbiorczo. |
