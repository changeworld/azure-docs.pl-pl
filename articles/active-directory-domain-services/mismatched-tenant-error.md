---
title: Rozwiązywanie niezgodnych błędów katalogów w Azure AD Domain Services | Microsoft Docs
description: Zrozumienie i rozwiązywanie niezgodnych błędów katalogów dla istniejących Azure AD Domain Services domen zarządzanych
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
ms.openlocfilehash: 4978f7b782271daff996807172a24103bd8d9860
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617295"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Rozwiązywanie niezgodnych błędów katalogów dla istniejących Azure AD Domain Services domen zarządzanych
Masz istniejącą domenę zarządzaną Azure AD Domain Services. Po przejściu do Azure Portal i wyświetleniu domeny zarządzanej zostanie wyświetlony następujący komunikat o błędzie:

![Niezgodny błąd katalogu](./media/getting-started/mismatched-tenant-error.png)

Nie można administrować tą domeną zarządzaną, dopóki błąd nie zostanie rozwiązany.


## <a name="whats-causing-this-error"></a>Co jest przyczyną tego błędu?
Ten błąd jest spowodowany tym, że zarządzana domena i Sieć wirtualna jest włączona, należy do dwóch różnych dzierżawców usługi Azure AD. Na przykład masz domenę zarządzaną o nazwie "contoso.com" i włączono ją dla dzierżawy usługi Azure AD firmy Contoso. Jednak Sieć wirtualna platformy Azure, w której włączono domenę zarządzaną, należy do innej dzierżawy usługi Azure AD.

Nowe Azure Portal (a w odróżnieniu od rozszerzenia Azure AD Domain Services) są oparte na Azure Resource Manager. W środowisku Modern Azure Resource Manager niektóre ograniczenia są wymuszane w celu zapewnienia większego poziomu zabezpieczeń i kontroli dostępu opartej na rolach (RBAC) do zasobów. Włączenie Azure AD Domain Services dla dzierżawy usługi Azure AD jest operacją poufną, ponieważ powoduje synchronizowanie skrótów poświadczeń z domeną zarządzaną. Ta operacja wymaga, aby być administratorem dzierżawy dla katalogu. Ponadto należy mieć uprawnienia administracyjne do sieci wirtualnej, w której włączono domenę zarządzaną. Aby testy RBAC działały spójnie, domena zarządzana i Sieć wirtualna powinny należeć do tej samej dzierżawy usługi Azure AD.

Nie można włączyć domeny zarządzanej dla dzierżawy usługi Azure AD "contoso.com" w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do innej dzierżawy usługi Azure AD "fabrikam.com". 

**Prawidłowa konfiguracja**: W tym scenariuszu wdrażania jest włączona domena zarządzana Contoso dla dzierżawy usługi Azure AD firmy Contoso. Domena zarządzana jest uwidaczniana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Azure AD firmy Contoso. W związku z tym zarówno domena zarządzana, jak i Sieć wirtualna należą do tej samej dzierżawy usługi Azure AD. Ta konfiguracja jest prawidłowa i jest w pełni obsługiwana.

![Prawidłowa konfiguracja dzierżawy](./media/getting-started/valid-tenant-config.png)

**Niezgodna konfiguracja dzierżawy**: W tym scenariuszu wdrażania jest włączona domena zarządzana Contoso dla dzierżawy usługi Azure AD firmy Contoso. Jednak domena zarządzana jest uwidaczniana w sieci wirtualnej należącej do subskrypcji platformy Azure należącej do dzierżawy usługi Azure AD firmy Fabrikam. W związku z tym domena zarządzana i Sieć wirtualna należą do dwóch różnych dzierżaw usługi Azure AD. Ta konfiguracja jest niezgodną konfiguracją dzierżawy i nie jest obsługiwana. Sieć wirtualną należy przenieść do tej samej dzierżawy usługi Azure AD (czyli contoso) jako domeny zarządzanej. Szczegółowe informacje znajdują się w sekcji [rozwiązanie](#resolution) .

![Niezgodna konfiguracja dzierżawy](./media/getting-started/mismatched-tenant-config.png)

W związku z tym, gdy zarządzana domena i Sieć wirtualna jest włączona, należy do dwóch różnych dzierżawców usługi Azure AD.

W środowisku Menedżer zasobów są stosowane następujące reguły:
- Katalog usługi Azure AD może mieć wiele subskrypcji platformy Azure.
- Subskrypcja platformy Azure może mieć wiele zasobów, takich jak sieci wirtualne.
- Dla katalogu usługi Azure AD jest włączona jedna Azure AD Domain Services domena zarządzana.
- Azure AD Domain Services domeny zarządzanej można włączyć w sieci wirtualnej należącej do dowolnej subskrypcji platformy Azure w ramach tej samej dzierżawy usługi Azure AD.


## <a name="resolution"></a>Rozwiązanie
Dostępne są dwie opcje rozwiązania niezgodnego błędu katalogu. Możesz:

- Kliknij przycisk **Usuń** , aby usunąć istniejącą domenę zarządzaną. Utwórz ponownie przy użyciu [Azure Portal](https://portal.azure.com), aby zarządzana domena i Sieć wirtualna należały do katalogu usługi Azure AD. Dołącz wszystkie komputery wcześniej przyłączone do usuniętej domeny do nowo utworzonej domeny zarządzanej.

- Przenieś subskrypcję platformy Azure zawierającą sieć wirtualną do katalogu usługi Azure AD, do której należy domena zarządzana. Postępuj zgodnie z instrukcjami w artykule [przenoszenie subskrypcji platformy Azure do innego artykułu dotyczącego konta](../billing/billing-subscription-transfer.md) .


## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](tutorial-create-instance.md)
* [Przewodnik rozwiązywania problemów — Azure AD Domain Services](troubleshoot.md)
