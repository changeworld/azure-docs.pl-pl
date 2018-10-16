---
title: 'Azure AD Connect: Opcje urządzenia | Dokumentacja firmy Microsoft'
description: W tym dokumencie szczegółowych informacji dostępnych w programie Azure AD Connect opcje urządzenia
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c21b4b8996d7dae62c2aa1937c2876a66f6f82bd
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320564"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opcje urządzenia

Poniższa dokumentacja zawiera informacje o różnych opcjach urządzeń dostępnych w programie Azure AD Connect. Program Azure AD Connect można użyć, aby skonfigurować następujące dwie operacje: 
* **Dołączenie do hybrydowej usługi Azure AD**: Jeśli w lokalnym środowisku zużycie AD a zalet usługi Azure AD, można zaimplementować urządzeń przyłączonych do usługi Azure AD hybrydowych. Te urządzenia są połączone, zarówno w usłudze Active Directory w środowisku lokalnym i usługi Azure Active Directory.
* **Zapisywanie zwrotne urządzeń**: zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego opartego na urządzeniach z usługami AD FS (2012 R2 lub nowszy) chronione urządzeń

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfiguruj opcje urządzenia w usłudze Azure AD Connect

1.  Uruchom usługę Azure AD Connect. W **dodatkowe zadania** wybierz opcję **Konfiguruj opcje urządzenia**.  Kliknij przycisk **Dalej**.
    ![Konfiguruj opcje urządzenia](./media/how-to-connect-device-options/deviceoptions.png) 

    **Przegląd** strony wyświetli szczegóły.
    ![Omówienie](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nowe Konfiguruj opcje urządzenia jest dostępna tylko w wersji 1.1.819.0 lub nowszej.

2.  Po podaniu poświadczeń dla usługi Azure AD, możesz wybrać operacji do wykonania na stronie Opcje urządzenia.
    ![Operacje dotyczące urządzenia](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Kolejne kroki

* [Konfiguruj dołączenie do hybrydowej usługi Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Skonfiguruj / Wyłącz zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md)

