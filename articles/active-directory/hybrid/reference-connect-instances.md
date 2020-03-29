---
title: 'Usługa Azure AD Connect: wystąpienia usługi synchronizacji | Dokumenty firmy Microsoft'
description: Ta strona zawiera specjalne uwagi dotyczące wystąpień usługi Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298832"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Usługa Azure AD Connect: uwagi specjalne dotyczące wystąpień
Usługa Azure AD Connect jest najczęściej używana z ogólnoświatowym wystąpieniem usługi Azure AD i usługi Office 365. Ale istnieją również inne wystąpienia i mają one różne wymagania dotyczące adresów URL i innych specjalnych zagadnień.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Niemcy
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) to suwerenna chmura obsługiwana przez niemieckiego zarządcę danych.

| Adresy URL do otwarcia na serwerze proxy |
| --- |
| \*microsoftonline.de . |
| \*.windows.net |
| +Listy odwołania certyfikatów |

Po zalogowaniu się do dzierżawy usługi Azure AD należy użyć konta w domenie onmicrosoft.de.

Funkcje obecnie nie obecne w Microsoft Cloud Germany:

* **Zapisywanie hasła** jest dostępne w wersji zapoznawczej w usłudze Azure AD Connect w wersji 1.1.570.0 i później.
* Inne usługi Azure AD Premium są niedostępne.

## <a name="microsoft-azure-government"></a>Platforma Microsoft Azure dla instytucji rządowych
[Chmura Microsoft Azure Dla Instytucji Rządowych](https://azure.microsoft.com/features/gov/) to chmura dla instytucji rządowych w Stanach Zjednoczonych.

Ta chmura została obsługiwana przez wcześniejsze wersje programu DirSync. Z kompilacji 1.1.180 usługi Azure AD Connect jest obsługiwana następna generacja chmury. Ta generacja używa punktów końcowych opartych tylko na stanach AMERYKAŃSKICH i ma inną listę adresów URL do otwarcia na serwerze proxy.

| Adresy URL do otwarcia na serwerze proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (wymagane do automatycznego wykrywania dzierżawy platformy Azure dla instytucji rządowych) |
| \*gov.us.microsoftonline.com .gov.us.microsoftonline.com |
| +Listy odwołania certyfikatów |

> [!NOTE]
> Od wersji 1.1.647.0 usługi Azure AD Connect ustawienie wartości AzureInstance w rejestrze nie jest już wymagane, pod warunkiem że *.windows.net jest otwarty na serwerach proxy. Jednak dla klientów, którzy nie zezwalają na łączność z Internetem z ich serwerów Usługi Azure AD Connect, można użyć następującej konfiguracji ręcznej.

### <a name="manual-configuration"></a>Konfiguracja ręczna

Następujące kroki konfiguracji ręcznej są używane w celu zapewnienia, że usługa Azure AD Connect używa punktów końcowych synchronizacji platformy Azure dla instytucji rządowych.

1. Uruchom instalację usługi Azure AD Connect.
2. Gdy zobaczysz pierwszą stronę, na której powinieneś zaakceptować umowy Licencyjnej, nie kontynuuj, ale pozostaw uruchomiony kreator instalacji.
3. Uruchom regedit i zmień `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` klucz `4`rejestru na wartość .
4. Wróć do kreatora instalacji usługi Azure AD Connect, zaakceptuj umowy Licencyjnej II i kontynuuj. Podczas instalacji upewnij się, że używasz niestandardowej ścieżki **instalacji konfiguracji** (a nie instalacji ekspresowej), a następnie kontynuuj instalację w zwykły sposób.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
