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
ms.topic: article
ms.date: 06/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: be20f50b68ab3715e2b7a98db208dcd81a995f1b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463885"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Program Azure AD Connect: Specjalne uwagi dotyczące wystąpień
Program Azure AD Connect jest najczęściej używana z wystąpienia na całym świecie usługi Azure AD i Office 365. Ale istnieją również inne wystąpienia, a te mają różne wymagania dotyczące adresów URL i inne zagadnienia specjalne.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud (Niemcy)
[Microsoft Cloud w Niemczech](https://www.microsoft.de/cloud-deutschland) jest należących do suwerennej chmury przez niemieckiego zarządcę danych.

| Adresy URL, aby otworzyć w serwera proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listy odwołania certyfikatów |

Po zalogowaniu się do dzierżawy usługi Azure AD, należy użyć konta w domenie onmicrosoft.de.

Aktualnie nie znajduje się w Microsoft Cloud w Niemczech funkcje:

* **Zapisywanie zwrotne haseł** jest dostępna w wersji zapoznawczej, za pomocą usługi Azure AD Connect w wersji 1.1.570.0 i po nim.
* Inne usługi Azure AD Premium nie są dostępne.

## <a name="microsoft-azure-government-cloud"></a>Chmura platformy Microsoft Azure Government
[Chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/) to chmura dla instytucji rządowych USA.

Ta chmura obsługiwanego przez wcześniejszych wersjach narzędzia DirSync. Z 1.1.180 kompilacji programu Azure AD Connect chmury nowej generacji jest obsługiwana. Ta generacja używa tylko do Stanów Zjednoczonych oparte na punktach końcowych i mieć różne listy adresów URL, aby otworzyć na serwerze proxy.

| Adresy URL, aby otworzyć w serwera proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (wymagane do automatycznego wykrywania dzierżawy dla instytucji rządowych usługi Azure AD) |
| \*.gov.us.microsoftonline.com |
| + Listy odwołania certyfikatów |

> [!NOTE]
> Począwszy od AAD Connect wersji 1.1.647.0, ustawiając wartość AzureInstance w rejestrze nie jest już wymagany pod warunkiem, że *. windows.net jest otwarty na Twoje serwery proxy.

Aktualnie nie znajduje się w chmurze Microsoft Azure dla instytucji rządowych funkcje:

* **Zapisywanie zwrotne haseł** jest dostępna w wersji zapoznawczej, za pomocą usługi Azure AD Connect w wersji 1.1.570.0 i po nim.
* Inne usługi Azure AD Premium nie są dostępne.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
