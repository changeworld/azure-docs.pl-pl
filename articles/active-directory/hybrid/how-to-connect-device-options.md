---
title: 'Usługa Azure AD Connect: opcje urządzenia | Dokumenty firmy Microsoft'
description: Ten dokument zawiera szczegółowe informacje o opcjach urządzenia dostępnych w usłudze Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ddcdb67ef079cfa23902a1dcb03b0ec61077fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109543"
---
# <a name="azure-ad-connect-device-options"></a>Usługa Azure AD Connect: opcje urządzenia

Poniższa dokumentacja zawiera informacje o różnych opcjach urządzeń dostępnych w usłudze Azure AD Connect. Za pomocą usługi Azure AD Connect można skonfigurować następujące dwie operacje: 
* **Hybrydowe sprzężenie usługi Azure AD:** Jeśli twoje środowisko ma lokalną usługę AD i chcesz korzystać z usługi Azure AD, możesz zaimplementować hybrydowe urządzenia przyłączone do usługi Azure AD. Te urządzenia są przyłączane zarówno do lokalnej usługi Active Directory, jak i usługi Azure Active Directory.
* **Zapisywanie zwrotne urządzenia:** Zapisywanie zwrotne urządzenia służy do włączania dostępu warunkowego na podstawie urządzeń do urządzeń chronionych usługami AD FS (2012 R2 lub nowszych)

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfigurowanie opcji urządzeń w usłudze Azure AD Connect

1.  Uruchom usługę Azure AD Connect. Na stronie **Zadania dodatkowe** wybierz pozycję **Konfiguruj opcje urządzenia**.  Kliknij przycisk **alej**.
    ![Konfigurowanie opcji urządzenia](./media/how-to-connect-device-options/deviceoptions.png) 

    Na stronie **Przegląd** są wyświetlane szczegóły.
    ![Omówienie](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nowe opcje konfigurowania urządzenia są dostępne tylko w wersji 1.1.819.0 i nowszej.

2.  Po podaniu poświadczeń dla usługi Azure AD można wybrać operację, która ma zostać wykonana na stronie Opcje urządzenia.
    ![Operacje na urządzeniach](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie hybrydowego sprzężenia usługi AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Konfigurowanie / wyłączanie zapisywania zwrotnego urządzenia](how-to-connect-device-writeback.md)

