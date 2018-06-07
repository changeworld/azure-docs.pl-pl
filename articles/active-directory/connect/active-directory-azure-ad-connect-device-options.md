---
title: 'Azure AD Connect: Opcje urządzenia | Dokumentacja firmy Microsoft'
description: Ten dokument zawiera szczegóły urządzenia opcje dostępne w programie Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
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
ms.author: anandy
ms.openlocfilehash: 0eb3a33ee030dcda6a811a771578c9e976e36619
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593286"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: Opcje urządzenia

Poniższa dokumentacja zawiera informacje o różnych opcjach urządzenia dostępne w programie Azure AD Connect. Azure AD Connect umożliwia konfigurowanie dwie następujące czynności: 
* **Hybrydowe usługi Azure AD join**: Jeśli środowiska pod kątem lokalnej wpływ AD a również skorzystać z możliwości oferowane przez usługi Azure Active Directory, można zaimplementować hybrydowe usługi Azure AD przyłączone do urządzeń. Są to urządzenia, które są zarówno do lokalnej usługi Active Directory i Azure Active Directory.
* **Zapisywanie zwrotne urządzeń**: zapisywanie zwrotne urządzeń służy do włączania dostępu warunkowego, w oparciu o urządzenia do usług AD FS (2012 R2 lub nowszej) chronione urządzeń

## <a name="configure-device-options-in-azure-ad-connect"></a>Skonfiguruj opcje urządzenia w programie Azure AD Connect

1.  Uruchom usługi Azure AD Connect. W **dodatkowe zadania** wybierz pozycję **skonfigurować opcje urządzenia**.
    ![Skonfiguruj opcje urządzenia](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Po kliknięciu przycisku Dalej, **omówienie** zostanie wyświetlona strona, której szczegóły operacji, które mogą być wykonywane.
    ![Omówienie](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Nowe opcje konfiguracji urządzenia jest dostępna tylko w wersji 1.1.819.0 i nowszych.

2.  Po podaniu poświadczenia dla usługi Azure AD, możliwe jest wybranie operacji do wykonania na stronie Opcje urządzenia.
    ![Operacje urządzenia](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Kolejne kroki

* [Skonfiguruj hybrydowych usługi Azure AD join](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Skonfiguruj / wyłączyć zapisywanie zwrotne urządzeń](./active-directory-aadconnect-feature-device-writeback.md)

