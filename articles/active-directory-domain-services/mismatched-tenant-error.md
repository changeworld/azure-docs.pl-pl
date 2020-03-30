---
title: Naprawianie niedopasowanych błędów katalogów w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, co oznacza niezgodny błąd katalogu i jak go rozwiązać w usługach domenowych usługi Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613383"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Rozwiązywanie niedopasowanych błędów katalogów dla istniejących domen zarządzanych usług domenowych usługi AD Azure

Jeśli domena zarządzana usługami domenowymi usługi Active Directory platformy Azure (Usługi Azure AD DS) pokazuje błąd niedopasowania dzierżawy, nie można administrować domeną zarządzaną, dopóki nie zostanie rozwiązany. Ten błąd występuje, jeśli podstawowa sieć wirtualna platformy Azure jest przenoszona do innego katalogu usługi Azure AD.

W tym artykule wyjaśniono, dlaczego występuje błąd i jak go rozwiązać.

## <a name="what-causes-this-error"></a>Co powoduje ten błąd?

Niezgodny błąd katalogu występuje, gdy domena zarządzana usługą Azure AD DS i sieć wirtualna należą do dwóch różnych dzierżaw usługi Azure AD. Na przykład może mieć domenę zarządzaną usług Ad DS platformy Azure o nazwie *aaddscontoso.com,* która jest uruchamiana w dzierżawie usługi Azure AD firmy Contoso. Jednak sieć wirtualna platformy Azure dla domeny zarządzanej jest częścią dzierżawy usługi Fabrikam Azure AD.

Platforma Azure używa kontroli dostępu opartej na rolach (RBAC) w celu ograniczenia dostępu do zasobów. Po włączeniu usług Azure AD DS w dzierżawie usługi Azure AD skróty poświadczeń są synchronizowane z domeną zarządzaną. Ta operacja wymaga, aby być administratorem dzierżawy dla katalogu usługi Azure AD i dostęp do poświadczeń musi być kontrolowany. Aby wdrożyć zasoby w sieci wirtualnej platformy Azure i kontrolować ruch, musisz mieć uprawnienia administracyjne w sieci wirtualnej, w której można wdrożyć usługi Azure AD DS.

Aby funkcja RBAC działała spójnie i bezpiecznie korzystała ze wszystkich zasobów używanych przez usługę Azure AD DS, domena zarządzana i sieć wirtualna muszą należeć do tej samej dzierżawy usługi Azure AD.

W środowisku Menedżera zasobów obowiązują następujące reguły:

- Katalog usługi Azure AD może mieć wiele subskrypcji platformy Azure.
- Subskrypcja platformy Azure może mieć wiele zasobów, takich jak sieci wirtualne.
- Jedna domena zarządzana usługą AD Azure jest włączona dla katalogu usługi Azure AD.
- Domena zarządzana usługami domenowymi usługi Azure AD można włączyć w sieci wirtualnej należącej do dowolnej subskrypcji platformy Azure w ramach tej samej dzierżawy usługi Azure AD.

### <a name="valid-configuration"></a>Prawidłowa konfiguracja

W poniższym scenariuszu wdrażania przykładowej domeny zarządzanej usług Microsoft usługi Contoso Azure AD DS jest włączona w dzierżawie usługi Ad Contoso. Domena zarządzana jest wdrażana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Contoso Azure AD. Zarówno domena zarządzana, jak i sieć wirtualna należą do tej samej dzierżawy usługi Azure AD. W tym przykładzie konfiguracja jest prawidłowa i w pełni obsługiwana.

![Prawidłowa konfiguracja dzierżawy usług Azure AD DS z domeną zarządzałą i siecią wirtualną należącą do tej samej dzierżawy usługi Azure AD](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Niedopasowana konfiguracja dzierżawy

W tym przykładowym scenariuszu wdrażania domena zarządzana usługą Azure AD DS firmy Contoso jest włączona w dzierżawie usługi Ad Contoso. Jednak domena zarządzana jest wdrażana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Fabrikam Azure AD. Domena zarządzana i sieć wirtualna należą do dwóch różnych dzierżaw usługi Azure AD. W tym przykładzie konfiguracji jest niedopasowane dzierżawy i nie jest obsługiwany. Sieć wirtualna musi zostać przeniesiona do tej samej dzierżawy usługi Azure AD co domena zarządzana.

![Niedopasowana konfiguracja dzierżawy](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Rozwiązywanie niedopasowanego błędu dzierżawy

Następujące dwie opcje rozwiązują niezgodny błąd katalogu:

* [Usuń domenę zarządzana usługą Azure AD DS](delete-aadds.md) z istniejącego katalogu usługi Azure AD. [Utwórz zastępczą domenę zarządzaną usługą Azure AD DS](tutorial-create-instance.md) w tym samym katalogu usługi Azure AD, co sieć wirtualna, której chcesz użyć. Gdy wszystko będzie gotowe, dołącz do wszystkich maszyn wcześniej przyłączonych do usuniętej domeny do odtworzonej domeny zarządzanej.
* [Przenieś subskrypcję platformy Azure](../cost-management-billing/manage/billing-subscription-transfer.md) zawierającą sieć wirtualną do tego samego katalogu usługi Azure AD, co domena zarządzana usługą Azure AD DS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów z usługą Azure AD DS, zobacz [przewodnik po rozwiązywaniu problemów](troubleshoot.md).
