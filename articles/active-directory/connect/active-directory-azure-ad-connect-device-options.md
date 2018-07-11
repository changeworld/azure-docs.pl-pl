---
title: 'Azure AD Connect: Opcje urządzenia | Dokumentacja firmy Microsoft'
description: W tym dokumencie szczegółowych informacji dostępnych w programie Azure AD Connect opcje urządzenia
services: active-directory
documentationcenter: ''
author: billmath
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e52f691c75d491897b06a4ebb492d87fda682e38
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917850"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opcje urządzenia

Poniższa dokumentacja zawiera informacje o różnych opcjach urządzeń dostępnych w programie Azure AD Connect. Program Azure AD Connect można użyć, aby skonfigurować następujące dwie operacje: 
* **Dołączenie do hybrydowej usługi Azure AD**: Jeśli w lokalnym środowisku zużycie AD, a także skorzystać z możliwości oferowane przez usługę Azure Active Directory, można zaimplementować urządzeń przyłączonych do usługi Azure AD hybrydowych. Są to urządzenia, które są zarówno do lokalnej usługi Active Directory i usługi Azure Active Directory.
* **Zapisywanie zwrotne urządzeń**: zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego opartego na urządzeniach z usługami AD FS (2012 R2 lub nowszy) chronione urządzeń

## <a name="configure-device-options-in-azure-ad-connect"></a>Konfiguruj opcje urządzenia w usłudze Azure AD Connect

1.  Uruchom usługę Azure AD Connect. W **dodatkowe zadania** wybierz opcję **Konfiguruj opcje urządzenia**.
    ![Konfiguruj opcje urządzenia](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Po kliknięciu przycisku Dalej, **Przegląd** zostanie wyświetlona strona, której szczegóły operacji, które mogą być wykonywane.
    ![Omówienie](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nowe Konfiguruj opcje urządzenia jest dostępna tylko w wersji 1.1.819.0 lub nowszej.

2.  Po podaniu poświadczeń dla usługi Azure AD, możesz wybrać operacji do wykonania na stronie Opcje urządzenia.
    ![Operacje dotyczące urządzenia](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Kolejne kroki

* [Konfiguruj dołączenie do hybrydowej usługi Azure AD](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Skonfiguruj / Wyłącz zapisywanie zwrotne urządzeń](./active-directory-aadconnect-feature-device-writeback.md)

