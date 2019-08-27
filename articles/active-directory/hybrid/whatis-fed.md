---
title: Co to jest federacja w usłudze Azure AD Connect? | Microsoft Docs
description: Opisuje federację w usłudze Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f9832a4307ea9519df448f192eeca70043a6d6
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035170"
---
# <a name="what-is-federation-with-azure-ad"></a>Co to jest federacja w usłudze Azure AD Connect?

Federacja to kolekcja domen, które mają ustanowioną relację zaufania. Poziom zaufania może się różnić — zwykle obejmuje uwierzytelnianie i niemal zawsze autoryzację. Typowa federacja może obejmować kilka organizacji, które mają ustanowioną relację zaufania na potrzeby dostępu współdzielonego do zestawu zasobów.

Można sfederować środowisko lokalne z usługą Azure AD i użyć tej federacji do celów uwierzytelniania i autoryzacji.  Dzięki tej metodzie logowania cały proces uwierzytelniania użytkowników odbywa się lokalnie.  Ta metoda umożliwia administratorom wdrożenie bardziej rygorystycznych poziomów kontroli dostępu. Dostępne są federacje w usługach AD FS i PingFederate.

![Tożsamość federacyjna](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Jeśli zdecydujesz się na użycie federacji z usługami Active Directory Federation Services (AD FS), możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako kopię zapasową na wypadek awarii infrastruktury usług AD FS.


## <a name="next-steps"></a>Następne kroki

- [Co to jest tożsamość hybrydowa?](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Co to są programy Azure AD Connect i Connect Health?](whatis-azure-ad-connect.md)
- [Co to jest synchronizacja skrótów haseł?](whatis-phs.md)
- [Co to jest federacja?](whatis-fed.md)
- [Co to jest logowanie jednokrotne?](how-to-connect-sso.md)
- [How federation works (Jak działa federacja)](how-to-connect-fed-whatis.md)
- [Konfigurowanie federacji z serwerem PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
