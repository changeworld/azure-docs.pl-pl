---
title: Zmień algorytm wyznaczania wartości skrótu podpisu dla usługi Office 365 jednostki uzależnionej relacja zaufania - Azure
description: Ta strona zawiera wskazówki dotyczące zmieniania algorytm SHA relacjami zaufania federacji z usługą Office 365
keywords: Algorytm SHA1, SHA256, usługi O365, Federacji aadconnect, usług AD FS, usługi ad fs, sha zmiany relacjami zaufania federacji, zaufanie jednostki uzależnionej
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b6c81343d52f016dc779f9e08176a0d909684c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138625"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Zmień algorytm wyznaczania wartości skrótu podpisu dla usługi Office 365, zaufanie jednostki uzależnionej
## <a name="overview"></a>Omówienie
Active Directory Federation Services (AD FS) podpisuje jego tokenów do usługi Microsoft Azure Active Directory do upewnij się, że ich nie dało się. Ta sygnatura może bazować na SHA1 lub SHA256. Usługa Azure Active Directory obsługuje teraz tokeny podpisane przy użyciu algorytm SHA256 i firma Microsoft zaleca ustawienie algorytmu podpisywania tokenu SHA256 na najwyższy poziom zabezpieczeń. W tym artykule opisano czynności, aby ustawić algorytm podpisywania tokenu bezpieczniejszy algorytm SHA256 poziom.

>[!NOTE]
>Firma Microsoft zaleca użycie SHA256 algorytmu podpisywania tokenów jest bezpieczniejszy niż SHA1, ale ciągle obsługiwaną opcją SHA1.

## <a name="change-the-token-signing-algorithm"></a>Zmień algorytm podpisywania tokenu
Po ustawieniu algorytm podpisu przy użyciu jednego z dwóch procesy poniżej usług AD FS podpisuje tokeny dla usługi Office 365 za pomocą SHA256 zaufanie jednostki uzależnionej. Nie musisz wprowadzać żadnych zmian dodatkowej konfiguracji, a ta zmiana nie ma wpływu na możliwość dostępu do usługi Office 365 lub innych aplikacji usługi Azure AD.

### <a name="ad-fs-management-console"></a>Konsoli zarządzania usług AD FS
1. Otwórz konsolę zarządzania usług AD FS na serwerze podstawowym usług AD FS.
2. Rozwiń węzeł usługi AD FS, a następnie kliknij przycisk **zaufania jednostek uzależnionych**.
3. Kliknij prawym przyciskiem myszy usługi Office 365/Azure zaufania jednostki uzależnionej, a następnie wybierz pozycję **właściwości**.
4. Wybierz **zaawansowane** kartę, a następnie wybierz pozycję skrótu secure hash algorithm SHA256.
5. Kliknij przycisk **OK**.

![Algorytm podpisywania SHA256 — programu MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Polecenia cmdlet programu AD FS programu PowerShell
1. Na dowolnym serwerze usług AD FS Otwórz program PowerShell w ramach uprawnień administratora.
2. Ustaw skrótu secure hash algorithm, używając **zestaw AdfsRelyingPartyTrust —** polecenia cmdlet.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Przeczytaj również
* [Naprawa relacji zaufania usługi Office 365 w programie Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

