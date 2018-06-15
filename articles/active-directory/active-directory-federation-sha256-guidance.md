---
title: Zmień algorytm wyznaczania wartości skrótu podpisu dla zaufania jednostki uzależnionej usługi Office 365 | Dokumentacja firmy Microsoft
description: Ta strona zawiera wskazówki dotyczące zmieniania algorytm SHA relacja zaufania federacji z usługą Office 365
keywords: SHA1, SHA256, usługi O365, federacyjnych, aadconnect, usług AD FS, usługi ad fs, sha zmian relacji zaufania federacji, zaufanie jednostki uzależnionej
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: ec7eee36888d825d65335db590731103aabbf5c2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26598965"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Zmień algorytm wyznaczania wartości skrótu podpisu dla usługi Office 365 zaufanie jednostki uzależnionej
## <a name="overview"></a>Omówienie
Active Directory Federation Services (AD FS) podpisuje Microsoft Azure Active Directory w celu zapewnienia nie może zostać zmieniony z jego tokenów. Podpis może bazować na SHA1 lub SHA256. Azure Active Directory obsługuje teraz tokeny podpisane przy użyciu algorytmu skrótu SHA256 i zaleca się ustawienie algorytm podpisywania tokenu SHA256 najwyższego poziomu zabezpieczeń. W tym artykule opisano kroki niezbędne do Ustaw poziom algorytm podpisywania tokenu do bardziej bezpieczne SHA256.

>[!NOTE]
>Firma Microsoft zaleca użycie SHA256 jako algorytmu podpisywania tokenów jest bezpieczniejsza niż SHA1, ale wciąż obsługiwaną opcją SHA1.

## <a name="change-the-token-signing-algorithm"></a>Zmień algorytm podpisywania tokenu
Po ustawieniu algorytm podpisu z jednego z dwóch poniższych procesów usług AD FS podpisuje tokeny dla usługi Office 365 z SHA256 zaufanie jednostki uzależnionej. Nie trzeba wprowadzać żadnych zmian konfiguracji dodatkowe, a ta zmiana nie ma wpływu na możliwość uzyskania dostępu do usługi Office 365 lub innych aplikacji usługi Azure AD.

### <a name="ad-fs-management-console"></a>Konsoli zarządzania usług AD FS
1. Otwórz konsolę zarządzania usług AD FS na podstawowym serwerze usług AD FS.
2. Rozwiń węzeł usług AD FS, a następnie kliknij przycisk **zaufania jednostek uzależnionych**.
3. Kliknij prawym przyciskiem myszy programu Office 365/Azure zaufania jednostki uzależnionej, a następnie wybierz **właściwości**.
4. Wybierz **zaawansowane** i wybierz skrótu secure hash algorithm SHA256.
5. Kliknij przycisk **OK**.

![Algorytm podpisywania SHA256--programu MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Polecenia cmdlet programu AD FS programu PowerShell
1. Na dowolnym serwerze usług AD FS Otwórz program PowerShell w obszarze uprawnienia administratora.
2. Wartość skrótu secure hash algorithm przy użyciu **Set-AdfsRelyingPartyTrust** polecenia cmdlet.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Zobacz temat
* [Napraw zaufania usługi Office 365 z programem Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

