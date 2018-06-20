---
title: Rozwiązywanie błędów niedopasowanych directory dla istniejących domen zarządzanych usług domenowych Azure AD | Dokumentacja firmy Microsoft
description: Zrozumienia i rozwiązania błędów niedopasowanych directory dla istniejących domen zarządzanych usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: maheshu
ms.openlocfilehash: 7e7786ac36485a792e9c77b10925f01790f95ab1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218201"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Rozwiązywanie błędów niedopasowanych directory dla istniejących domen zarządzanych usług domenowych Azure AD
Masz istniejące domeny zarządzanej usług domenowych Azure AD. Gdy przejdź do portalu Azure i wyświetlić domeny zarządzanej, zobaczysz komunikat o błędzie:

![Błąd niedopasowanych katalogu](.\media\getting-started\mismatched-tenant-error.png)

Nie można administrować tej domeny zarządzanej, do momentu rozwiązania błędu.


## <a name="whats-causing-this-error"></a>Co to jest przyczyną tego błędu?
Ten błąd występuje, gdy domeny zarządzanej i jest włączona w sieci wirtualnej należy do dwóch różnych dzierżaw usługi Azure AD. Na przykład masz domeny zarządzanej o nazwie "contoso.com" i został włączony dla dzierżawy usługi Azure AD firmy Contoso. Jednak sieci wirtualnej platformy Azure, w którym włączono domeny zarządzanej należy do firmy Fabrikam - innej dzierżawy usługi Azure AD.

Nowego portalu Azure (i w szczególności rozszerzenia usług domenowych Azure AD) jest oparty na usłudze Azure Resource Manager. Nowoczesne środowisko usługi Azure Resource Manager wymusza pewne ograniczenia do dostarczania większe bezpieczeństwo i dla dostępu na podstawie ról (RBAC) należy kontrolować do zasobów. Włączanie usług domenowych Azure AD dla dzierżawy usługi Azure AD jest poufnych operacji, ponieważ powoduje skrótów poświadczeń do synchronizacji z domeny zarządzanej. Ta operacja wymaga, aby użytkownik był administratorem dzierżawy dla katalogu. Ponadto musi mieć uprawnienia administracyjne za pośrednictwem sieci wirtualnej, w którym można włączyć domeny zarządzanej. Kontroli RBAC działać spójnie domeny zarządzanej i siecią wirtualną powinny należeć do tej samej dzierżawy usługi Azure AD.

Innymi słowy nie można włączyć domeny zarządzanej dla dzierżawy usługi Azure AD "contoso.com" w sieci wirtualnej należących do subskrypcji platformy Azure należących do innej dzierżawy usługi Azure AD "fabrikam.com". 

**Nieprawidłowa konfiguracja**: W tym scenariuszu wdrażania domeny zarządzanej Contoso jest włączona dla dzierżawy usługi Azure AD w firmie Contoso. Domeny zarządzanej jest widoczna w sieci wirtualnej należących do subskrypcji platformy Azure należących do dzierżawy usługi Azure AD w firmie Contoso. W związku z tym zarówno domeny zarządzanej, jak i sieć wirtualna należy do tej samej dzierżawy usługi Azure AD. Ta konfiguracja jest nieprawidłowy i w pełni obsługiwane.

![Nieprawidłowa dzierżawy konfiguracji](./media/getting-started/valid-tenant-config.png)

**Niedopasowane dzierżawy konfiguracji**: W tym scenariuszu wdrażania domeny zarządzanej Contoso jest włączona dla dzierżawy usługi Azure AD w firmie Contoso. Jednak domeny zarządzanej jest widoczne w sieci wirtualnej, który należy do subskrypcji platformy Azure należących do dzierżawy usługi Azure AD w firmie Fabrikam. W związku z tym domeny zarządzanej i sieć wirtualna należy do dwóch różnych dzierżaw usługi Azure AD. Ta konfiguracja jest Konfiguracja dzierżawy niezgodne i nie jest obsługiwana. Sieć wirtualna muszą zostać przeniesione do tej samej dzierżawy usługi Azure AD (to znaczy Contoso) jako domeny zarządzanej. Zobacz [rozpoznawania](#resolution) sekcji, aby uzyskać szczegółowe informacje.

![Niedopasowane dzierżawy konfiguracji](./media/getting-started/mismatched-tenant-config.png)

W związku z tym, kiedy domeny zarządzanej i sieci wirtualnej jest włączona w należą do dwóch różnych dzierżaw usługi Azure AD, zostanie wyświetlony ten błąd.

W środowisku usługi Resource Manager obowiązują następujące reguły:
- Katalog usługi Azure AD mogą mieć wiele subskrypcji Azure.
- Subskrypcja platformy Azure mogą mieć wiele zasobów, takich jak sieci wirtualnych.
- Jednej domeny zarządzanej usług domenowych Azure AD jest włączone dla katalogu usługi Azure AD.
- Domeny zarządzanej usług domenowych Azure AD można włączyć w sieci wirtualnej należących do żadnej subskrypcji platformy Azure w ramach tej samej dzierżawy usługi Azure AD.


## <a name="resolution"></a>Rozwiązanie
Dostępne są dwie opcje, aby rozwiązać problem niedopasowanych katalogu. Użytkownik może:

- Kliknij przycisk **usunąć** przycisk, aby usunąć istniejącą zarządzane domeny. Utwórz ponownie przy użyciu [portalu Azure](https://portal.azure.com), dzięki czemu domeny zarządzanej i jest on dostępny w sieci wirtualnej należy do katalogu usługi Azure AD. Dołącz wszystkie komputery, które wcześniej przyłączone do domeny usunięto do nowo utworzonej domeny zarządzanej.

- Przenoszenie subskrypcji platformy Azure zawierającą sieci wirtualnej do katalogu usługi Azure AD, do którego należy domeny zarządzanej. Postępuj zgodnie z instrukcjami [przeniesienia własności subskrypcji platformy Azure na inne konto](../billing/billing-subscription-transfer.md) artykułu.


## <a name="related-content"></a>Zawartość pokrewna
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Rozwiązywanie problemów z guide - usługi domenowe Azure AD](active-directory-ds-troubleshooting.md)
