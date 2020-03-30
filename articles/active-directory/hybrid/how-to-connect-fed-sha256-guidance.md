---
title: Zmienianie algorytmu mieszania podpisu dla zaufania jednostki uzależniającej usługi Office 365 — Azure
description: Ta strona zawiera wskazówki dotyczące zmiany algorytmu SHA dla zaufania federacji za pomocą usługi Office 365
keywords: SHA1,SHA256,O365,federation,aadconnect,adfs,ad fs,change sha,federation trust,relying party trust
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897337"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Zmienianie algorytmu mieszania podpisu dla zaufania jednostki uzależniającej usługi Office 365
## <a name="overview"></a>Omówienie
Usługi federacyjne Active Directory (AD FS) podpisują swoje tokeny w usłudze Microsoft Azure Active Directory, aby upewnić się, że nie można ich manipulować. Ten podpis może być oparty na SHA1 lub SHA256. Usługa Azure Active Directory obsługuje teraz tokeny podpisane za pomocą algorytmu SHA256 i zaleca się ustawienie algorytmu podpisywania tokenów na SHA256 dla najwyższego poziomu zabezpieczeń. W tym artykule opisano kroki potrzebne do skonfigurowania algorytmu podpisywania tokenu na bezpieczniejszym poziomie SHA256.

>[!NOTE]
>Firma Microsoft zaleca użycie SHA256 jako algorytm do podpisywania tokenów, ponieważ jest bezpieczniejszy niż SHA1, ale SHA1 nadal pozostaje obsługiwaną opcją.

## <a name="change-the-token-signing-algorithm"></a>Zmienianie algorytmu podpisywania tokenów
Po ustawieniu algorytmu podpisu z jednym z dwóch procesów poniżej, ad fs podpisuje tokeny dla usługi Office 365 zaufania jednostki uzależniającej z SHA256. Nie musisz wprowadzać żadnych dodatkowych zmian konfiguracji, a ta zmiana nie ma wpływu na możliwość uzyskania dostępu do usługi Office 365 lub innych aplikacji usługi Azure AD.

### <a name="ad-fs-management-console"></a>Konsola zarządzania usługami AD FS
1. Otwórz konsolę zarządzania usługami AD FS na podstawowym serwerze usług AD FS.
2. Rozwiń węzeł usług AD FS i kliknij pozycję **Zaufania jednostki uzależnień .**
3. Kliknij prawym przyciskiem myszy zaufanie jednostki uzależniającej usługi Office 365/Azure i wybierz polecenie **Właściwości**.
4. Wybierz kartę **Zaawansowane** i wybierz algorytm bezpiecznego mieszania SHA256.
5. Kliknij przycisk **OK**.

![Algorytm podpisywania SHA256 - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell usług AD FS
1. Na dowolnym serwerze usług AD FS otwórz program PowerShell w obszarze uprawnień administratora.
2. Ustaw algorytm bezpiecznego mieszania przy użyciu polecenia cmdlet **Set-AdfsRelyingPartyTrust.**
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Przeczytaj także:
* [Naprawianie zaufania usługi Office 365 za pomocą usługi Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

