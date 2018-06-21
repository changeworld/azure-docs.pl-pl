---
title: 'Azure AD Connect: Wystąpienie usługi synchronizacji | Dokumentacja firmy Microsoft'
description: Ta strona dokumentów uwagi dotyczące wystąpienia usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
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
ms.openlocfilehash: 351f3e34f5050d922655a633ec89f51d191b946e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295617"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Uwagi dotyczące wystąpienia
Azure AD Connect jest najczęściej używana z wystąpieniem na całym świecie usługi Azure AD i Office 365. Istnieją także inne wystąpienia, ale te mają różne wymagania dotyczące adresów URL i inne uwagi.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud (Niemcy)
[Niemcy Microsoft Cloud](http://www.microsoft.de/cloud-deutschland) to chmura suwerennych przez osobę zaufaną niemieckim danych.

| Adresy URL, aby otworzyć w serwera proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + Listy odwołania certyfikatów |

Po zalogowaniu się do dzierżawy usługi Azure AD, należy użyć konta w domenie onmicrosoft.de.

Aktualnie nie znajduje się w Niemczech Microsoft Cloud funkcje:

* **Zapisywanie zwrotne haseł** jest dostępna w wersji zapoznawczej wersji Azure AD Connect 1.1.570.0 i po.
* Inne usługi Azure AD Premium nie są dostępne.

## <a name="microsoft-azure-government-cloud"></a>Chmury Microsoft Azure dla instytucji rządowych
[Chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/) to chmura dla instytucji rządowych Stanów Zjednoczonych.

Ta chmura obsługiwanego przez wcześniejszych wersjach narzędzia DirSync. Z 1.1.180 kompilacji programu Azure AD Connect generacji chmury jest obsługiwana. Tej generacji używa USA — tylko do oparte na punktach końcowych i mieć różne listy adresów URL, aby otworzyć w serwera proxy.

| Adresy URL, aby otworzyć w serwera proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (wymagane do automatycznego wykrywania dzierżawy dla instytucji rządowych usługi Azure AD) |
| \*.gov.us.microsoftonline.com |
| + Listy odwołania certyfikatów |

> [!NOTE]
> Począwszy od AAD Connect wersji 1.1.647.0 ustawienie wartości AzureInstance w rejestrze nie jest już wymagane, pod warunkiem że *. windows.net jest otwarty na Twoje serwery proxy.

Aktualnie nie znajduje się w chmurze Microsoft Azure dla instytucji rządowych funkcje:

* **Zapisywanie zwrotne haseł** jest dostępna w wersji zapoznawczej wersji Azure AD Connect 1.1.570.0 i po.
* Inne usługi Azure AD Premium nie są dostępne.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
