---
title: 'Program Azure AD Connect: Synchronizowanie wystąpienia usługi | Dokumentacja firmy Microsoft'
description: Ta strona zawiera dokumentację specjalne uwagi dotyczące wystąpień usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298832"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Program Azure AD Connect: Specjalne uwagi dotyczące wystąpień
Program Azure AD Connect jest najczęściej używana z wystąpienia na całym świecie usługi Azure AD i Office 365. Ale istnieją również inne wystąpienia, a te mają różne wymagania dotyczące adresów URL i inne zagadnienia specjalne.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud (Niemcy)
[Microsoft Cloud w Niemczech](https://www.microsoft.de/cloud-deutschland) jest należących do suwerennej chmury przez niemieckiego zarządcę danych.

| Adresy URL, aby otworzyć w serwera proxy |
| --- |
| \*.microsoftonline.de |
| \*. windows.net |
| + Listy odwołania certyfikatów |

Po zalogowaniu się do dzierżawy usługi Azure AD, należy użyć konta w domenie onmicrosoft.de.

Aktualnie nie znajduje się w Microsoft Cloud w Niemczech funkcje:

* **Zapisywanie zwrotne haseł** jest dostępna w wersji zapoznawczej, za pomocą usługi Azure AD Connect w wersji 1.1.570.0 i po nim.
* Inne usługi Azure AD Premium nie są dostępne.

## <a name="microsoft-azure-government"></a>Microsoft Azure Government
[Chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/) to chmura dla instytucji rządowych USA.

Ta chmura obsługiwanego przez wcześniejszych wersjach narzędzia DirSync. Z 1.1.180 kompilacji programu Azure AD Connect chmury nowej generacji jest obsługiwana. Ta generacja używa tylko do Stanów Zjednoczonych oparte na punktach końcowych i mieć różne listy adresów URL, aby otworzyć na serwerze proxy.

| Adresy URL, aby otworzyć w serwera proxy |
| --- |
| \*. microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (wymagane w celu automatycznego wykrywania dzierżawy Azure dla instytucji rządowych) |
| \*.gov.us.microsoftonline.com |
| + Listy odwołania certyfikatów |

> [!NOTE]
> Począwszy od usługi Azure AD Connect w wersji 1.1.647.0, ustawiając wartość AzureInstance w rejestrze nie jest już wymagane, pod warunkiem że *. windows.net jest otwarty na Twoje serwery proxy. Jednak w przypadku klientów, które nie zezwalają na połączenie z Internetem z ich serwerów usługi Azure AD Connect, następująca konfiguracja ręczna może służyć.

### <a name="manual-configuration"></a>Konfiguracja ręczna

Następujące kroki ręcznej konfiguracji są używane do upewnij się, że program Azure AD Connect używa punktów końcowych synchronizacji usługi Azure Government.

1. Uruchom instalację usługi Azure AD Connect.
2. Gdy pojawi się pierwsza strona, gdzie są powinien zaakceptować umowę licencyjną, nie Kontynuuj, ale pozostaw uruchomiony Kreator instalacji.
3. Uruchom regedit i zmienić wartość klucza rejestru `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` wartość `4`.
4. Przejdź do Kreatora instalacji usługi Azure AD Connect, należy zaakceptować umowę licencyjną i kontynuować. Podczas instalacji upewnij się, że używasz **konfiguracji niestandardowej** instalacji ścieżka (i nie Instalacja ekspresowa), następnie kontynuuj instalację w zwykły sposób.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
