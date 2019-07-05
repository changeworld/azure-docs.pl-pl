---
title: Rozwiązywanie błędów niezgodny katalog dla istniejących domen zarządzanych usług domenowych Azure AD | Dokumentacja firmy Microsoft
description: Omówienie i rozwiązywanie błędów niezgodny katalog dla istniejących domen zarządzanych usług domenowych Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1ab6a535c9ffebcb423e7a5cb7f158224c004bd1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472893"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Rozwiązywanie błędów niezgodny katalog dla istniejących domen zarządzanych usług domenowych Azure AD
Masz istniejące domeny zarządzanej usług domenowych Azure AD. Gdy przejdź do witryny Azure portal i wyświetlić domeny zarządzanej, zobaczysz następujący komunikat o błędzie:

![Błąd niezgodny katalog](./media/getting-started/mismatched-tenant-error.png)

Nie można administrować tej domeny zarządzanej, do momentu usunięcia błędu.


## <a name="whats-causing-this-error"></a>Co jest przyczyną tego błędu?
Przyczyną tego błędu, jeśli Twoja domena zarządzana i sieć wirtualna jest włączona w należą do dwóch różnych dzierżaw usługi Azure AD. Na przykład masz domeny zarządzanej, o nazwie "contoso.com" i został włączony dla dzierżawy usługi Azure AD firmy Contoso. Jednak sieci wirtualnej platformy Azure, w którym domena zarządzana została włączona należy do firmy Fabrikam - innej dzierżawy usługi Azure AD.

Nowej witryny Azure portal (i w szczególności rozszerzenia usług domenowych Azure AD) bazuje na usłudze Resource Manager. Nowoczesne środowiska usługi Azure Resource Manager pewne ograniczenia obowiązują do dostarczania lepszych zabezpieczeń i w przypadku dostępu na podstawie ról kontroli (RBAC) do zasobów. Włączanie usług domenowych Azure AD dla dzierżawy usługi Azure AD jest operacją poufne, ponieważ sprawia, że poświadczenie skróty do synchronizacji z domeną zarządzaną. Ta operacja wymaga, aby użytkownik był administratorem dzierżawy dla katalogu. Ponadto musi mieć uprawnienia administracyjne, za pośrednictwem sieci wirtualnej, w którym należy włączyć domeny zarządzanej. Dla kontroli RBAC, które mają działać spójnie domena zarządzana i sieć wirtualna powinny należeć do tej samej dzierżawie usługi Azure AD.

Krótko mówiąc nie można włączyć domeny zarządzanej dla dzierżawy usługi Azure AD "contoso.com" w sieci wirtualnej należących do subskrypcji platformy Azure należących do innej dzierżawy usługi Azure AD "fabrikam.com". 

**Nieprawidłowa konfiguracja**: W tym scenariuszu wdrażania domena zarządzana Contoso jest włączona dla dzierżawy firmy Contoso w usłudze Azure AD. Domena zarządzana jest udostępniany w sieci wirtualnej należących do subskrypcji platformy Azure należących do dzierżawy firmy Contoso w usłudze Azure AD. W związku z tym zarówno domeny zarządzanej, jak i sieci wirtualnej należy do tej samej dzierżawie usługi Azure AD. Ta konfiguracja jest prawidłowa i w pełni obsługiwana.

![Konfiguracja prawidłową dzierżawę](./media/getting-started/valid-tenant-config.png)

**Konfiguracji dzierżawy niedopasowanych**: W tym scenariuszu wdrażania domena zarządzana Contoso jest włączona dla dzierżawy firmy Contoso w usłudze Azure AD. Jednakże domena zarządzana jest udostępniany w sieci wirtualnej, który należy do subskrypcji platformy Azure należących do dzierżawy usługi Azure AD firmy Fabrikam. W związku z tym, domena zarządzana i sieć wirtualna należy do dwóch różnych dzierżaw usługi Azure AD. Ta konfiguracja jest Konfiguracja dzierżawy niezgodne i nie jest obsługiwane. Sieć wirtualna należy przenieść z tą samą dzierżawą usługi Azure AD (czyli Contoso) jako domeny zarządzanej. Zobacz [rozpoznawania](#resolution) sekcji, aby uzyskać szczegółowe informacje.

![Konfiguracja dzierżawy niezgodne](./media/getting-started/mismatched-tenant-config.png)

W związku z tym, gdy domena zarządzana i sieć wirtualna jest włączona w należą do dwóch różnych dzierżaw usługi Azure AD, zostanie wyświetlony ten błąd.

W środowisku usługi Resource Manager obowiązują następujące reguły:
- Katalog usługi Azure AD mogą mieć wiele subskrypcji platformy Azure.
- Subskrypcja platformy Azure może mieć wiele zasobów, takich jak sieci wirtualne.
- Pojedynczej domeny zarządzanej usług domenowych Azure AD jest włączona dla katalogu usługi Azure AD.
- Domeny zarządzanej usług domenowych Azure AD można włączyć w sieci wirtualnej należące do dowolnej subskrypcji platformy Azure w ramach tej samej dzierżawie usługi Azure AD.


## <a name="resolution"></a>Rozwiązanie
Dostępne są dwie opcje, aby naprawić błąd niezgodny katalog. Użytkownik może:

- Kliknij przycisk **Usuń** domeny zarządzanej przez przycisk, aby usunąć istniejącą. Utwórz ponownie przy użyciu [witryny Azure portal](https://portal.azure.com), dzięki czemu domena zarządzana i jest on dostępny w sieci wirtualnej należy do katalogu usługi Azure AD. Dołącz do wszystkich maszyn, które wcześniej przyłączone do domeny usunięto do nowo utworzonego domeny zarządzanej.

- Przenoszenie subskrypcji platformy Azure zawierająca sieć wirtualną do katalogu usługi Azure AD, do której należy Twojej domeny zarządzanej. Postępuj zgodnie z instrukcjami w [przenieść własność subskrypcji platformy Azure do innego konta](../billing/billing-subscription-transfer.md) artykułu.


## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Rozwiązywanie problemów z przewodnik — usługi domenowe Azure AD](troubleshoot.md)
