---
title: Link konta platformy Azure, z Identyfikatorem partnera | Dokumentacja firmy Microsoft
description: Śledź interakcje z klientami platformy Azure, tworząc konto użytkownika, którego używasz do zarządzania zasobami przez klienta identyfikator partnera.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6bf61e2afd96e3923938ac4f815d34ae08f7c618
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371295"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Połącz z Identyfikatorem partnera do kont systemu Azure

Jako partner możesz śledzić swoje oddziaływanie w powiadomieniach promujących zaangażowanie klientów. Możesz połączyć Twój identyfikator partnera kont, które są używane do zarządzania zasobami klienta.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Uzyskiwanie dostępu do klienta

Aby połączyć się z Partnerem, klient musi udzielić Ci dostępu do swoich zasobów platformy Azure przy użyciu jednej z następujących opcji:

- **Użytkownik-Gość**: Klienta można dodał Cię jako użytkownika-gościa i przypisywania ról (RBAC) kontroli dostępu opartej na rolach. Aby uzyskać więcej informacji, zobacz [dodać użytkowników-gości z innego katalogu](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Konto Directory**: Klienta można utworzyć konto użytkownika dla Ciebie w ich własnych katalogu i przyznawać żadnej roli RBAC.

- **Nazwa główna usługi**: Klienta można dodać aplikację lub skrypt z Twojej organizacji w jego katalogu i przyznawać żadnej roli RBAC. Tożsamość aplikacji lub skryptu jest określany jako nazwy głównej usługi.

## <a name="link-to-a-partner-id"></a>Łączenie z identyfikatorem partnera

Jeśli masz dostęp do zasobów przez klienta, użyć witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator sieci Microsoft Partner (identyfikator MPN) do Identyfikatora użytkownika lub nazwy głównej usługi. Połącz z Identyfikatorem partnera w każdej dzierżawy klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użyj witryny Azure portal, aby połączyć nowy identyfikator partnera

1. Przejdź do [Połącz z Identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure portal.

2. Zaloguj się do Portalu Azure.

3. Wprowadź identyfikator partnera firmy Microsoft Partner ten identyfikator jest [sieci Microsoft Partner Network](https://partner.microsoft.com/) identyfikator dla Twojej organizacji.

   ![Zrzut ekranu przedstawiający Link do Identyfikatora partnera](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Aby połączyć Identyfikatora partnera do innego klienta, należy przełączyć katalog. W obszarze **Przełącz katalog**, wybierz swój katalog.

   ![Zrzut ekranu pokazujący Przełącz katalog](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Aby połączyć nowy identyfikator partnera przy użyciu programu PowerShell

1. Zainstaluj [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) modułu programu PowerShell.

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Link do nowego identyfikatora partnera. Partner ten identyfikator jest [sieci Microsoft Partner Network](https://partner.microsoft.com/) identyfikator dla Twojej organizacji.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator połączonej, partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizacja Identyfikatora połączonej, partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Usuń identyfikator połączonej, partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Użyj wiersza polecenia platformy Azure, aby połączyć nowy identyfikator partnera
1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Link do nowego identyfikatora partnera. Partner ten identyfikator jest [sieci Microsoft Partner Network](https://partner.microsoft.com/) identyfikator dla Twojej organizacji.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator połączonej, partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualizacja Identyfikatora połączonej, partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Usuń identyfikator połączonej, partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Kolejne kroki

Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion) odbierać aktualizacje lub wysłać opinię.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Kto może połączyć Identyfikatora partnera?**

Każdy użytkownik z organizacji partnerskiej, użytkowników, którzy zarządzają zasobami platformy Azure klienta można połączyć Identyfikatora partnera na koncie.

**Identyfikator partnera można zmienić po prowadzi?**

Tak. Identyfikator partnera połączonego można można zmienić, dodać ani usunąć.

**Co zrobić, jeśli użytkownik ma konto w więcej niż jedną dzierżawę klienta?**

Łącze między identyfikator partnera, a kontem odbywa się dla każdej dzierżawy klienta. Połącz z Identyfikatorem partnera w każdej dzierżawy klienta.

**Innych partnerów lub klientów edytować lub usunąć łącze, aby identyfikator partnera?**

Łącze jest skojarzone na poziomie konta użytkownika. Tylko Edytuj lub Usuń łącze do identyfikatora partnera. Klienta i innymi partnerami, nie można zmienić link do identyfikatora partnera.


**Identyfikator MPN, z którym należy użyć, jeśli Moja firma ma wiele?**

Można użyć dowolnego prawidłowego Identyfikatora MPN, z wyjątkiem wirtualny organization(v-org) identyfikator MPN. Większość partnerów wybrać identyfikator MPN dla lokalizacji geograficznej, w którym opiera się klienta lub usługi są dostarczane.

**Gdzie można znaleźć ich przychód raportowanie dla połączonych Partnerem?**

Możesz znaleźć ich przychód raportowanie na [Insights Mój pulpit nawigacyjny](https://partner.microsoft.com/membership/reports/myinsights). Należy wybrać łącze administrator partnera jako typ skojarzenia partnera.

**Dlaczego nie widzę mojego klienta w raportach**

Nie widzisz klienta w raportach z następujących przyczyn

1. Konto połączonego użytkownika nie ma [dostępu na podstawie roli](https://docs.microsoft.com/azure/role-based-access-control/overview) dowolnego klienta subskrypcji platformy Azure lub zasobów.

2. Subskrypcja platformy Azure, w której użytkownik ma [dostępu na podstawie roli](https://docs.microsoft.com/azure/role-based-access-control/overview) dostęp nie ma każde miesięczne użycie.

**Połączenie z partnerem, identyfikator działa z usługą Azure Stack?**

Tak, można połączyć z Partnerem usługi Azure Stack.
