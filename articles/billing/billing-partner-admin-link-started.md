---
title: Połącz konto platformy Azure z IDENTYFIKATORem partnera | Microsoft Docs
description: Śledź zaangażowanie z klientami platformy Azure, łącząc identyfikator partnera z kontem użytkownika używanym do zarządzania zasobami klienta.
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
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706400"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Łączenie identyfikatora partnera z kontami platformy Azure

Partnerzy firmy Microsoft oferują usługi, które pomagają klientom osiągnąć cele biznesowe i służbowe za pomocą produktów firmy Microsoft. Podczas działania w imieniu klienta zarządzającego, konfigurowaniem i obsługą usług platformy Azure, użytkownicy będą musieli uzyskać dostęp do środowiska klienta. Przy użyciu linku administratora partnera partnerzy mogą skojarzyć identyfikator sieci partnera z poświadczeniami używanymi do dostarczania usług.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Uzyskaj dostęp od klienta

Przed połączeniem identyfikatora partnera Klient musi zapewnić dostęp do swoich zasobów platformy Azure, korzystając z jednej z następujących opcji:

- **Użytkownik-Gość**: Klient może dodać Cię jako użytkownika-gościa i przypisać role kontroli dostępu opartej na rolach (RBAC). Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników-Gości z innego katalogu](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Konto katalogu**: Klient może utworzyć konto użytkownika w swoim własnym katalogu i przypisać dowolną rolę RBAC.

- Nazwa **główna usługi**: Klient może dodać aplikację lub skrypt z organizacji w swoim katalogu i przypisać dowolną rolę RBAC. Tożsamość aplikacji lub skryptu jest znana jako nazwa główna usługi.

## <a name="link-to-a-partner-id"></a>Łączenie z identyfikatorem partnera

Jeśli masz dostęp do zasobów klienta, użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator Microsoft Partner Network (MPN ID) z IDENTYFIKATORem użytkownika lub jednostką usługi. Połącz identyfikator partnera w każdej dzierżawie klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użyj Azure Portal, aby połączyć się z nowym IDENTYFIKATORem partnera

1. Przejdź do [linku do identyfikatora partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w Azure Portal.

2. Zaloguj się do Portalu Azure.

3. Wprowadź identyfikator partnera firmy Microsoft. Identyfikator partnera to identyfikator [Microsoft Partner Network](https://partner.microsoft.com/) organizacji.

   ![Zrzut ekranu przedstawiający link do identyfikatora partnera](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Aby połączyć identyfikator partnera dla innego klienta, przełącz katalog. W obszarze **Przełącz katalog**wybierz swój katalog.

   ![Zrzut ekranu przedstawiający katalog przełączników](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Używanie programu PowerShell do łączenia z nowym IDENTYFIKATORem partnera

1. Zainstaluj moduł programu PowerShell [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Logowanie przy użyciu programu PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Połącz się z nowym IDENTYFIKATORem partnera. Identyfikator partnera to identyfikator [Microsoft Partner Network](https://partner.microsoft.com/) organizacji.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator połączonego partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Zaktualizuj identyfikator połączonego partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Usuń połączony identyfikator partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Używanie interfejsu wiersza polecenia platformy Azure do łączenia z nowym IDENTYFIKATORem partnera
1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Połącz się z nowym IDENTYFIKATORem partnera. Identyfikator partnera to identyfikator [Microsoft Partner Network](https://partner.microsoft.com/) organizacji.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator połączonego partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Zaktualizuj identyfikator połączonego partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Usuń połączony identyfikator partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Następne kroki

Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion) , aby otrzymywać aktualizacje lub wysłać opinię.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Kto może połączyć identyfikator partnera?**

Każdy użytkownik z organizacji partnerskiej, który zarządza zasobami platformy Azure klienta, może połączyć identyfikator partnera z kontem.

**Czy identyfikator partnera można zmienić po jego połączeniu?**

Tak. Identyfikator połączonego partnera można zmienić, dodać lub usunąć.

**Co zrobić, jeśli użytkownik ma konto w więcej niż jednej dzierżawie klienta?**

Łącze między IDENTYFIKATORem partnera i kontem jest wykonywane dla każdej dzierżawy klienta. Połącz identyfikator partnera w każdej dzierżawie klienta.

**Czy inni partnerzy lub klienci mogą edytować lub usunąć link do identyfikatora partnera?**

Łącze jest skojarzone na poziomie konta użytkownika. Tylko ty można edytować lub usunąć łącze do identyfikatora partnera. Klient i inni partnerzy nie mogą zmienić linku do identyfikatora partnera.


**Którego identyfikatora MPN należy użyć, jeśli moja firma ma wiele?**

Do łączenia się z IDENTYFIKATORem partnera należy używać kont lokalizacji partnera i skojarzonych identyfikatorów MPN.  Dowiedz się więcej o [kontach partnerów](https://docs.microsoft.com/partner-center/account-structure)

**Gdzie można znaleźć wpływ na raportowanie przychodów dla połączonego identyfikatora partnera?**

Raportowanie wydajności produktu w chmurze jest dostępne dla partnerów w centrum partnerskim na [pulpicie nawigacyjnym my Insights](https://partner.microsoft.com/membership/reports/myinsights). Należy wybrać łącze administratora partnera jako typ powiązania partnera.

**Dlaczego nie widzę mojego klienta w raportach?**

Klient nie jest widoczny w raportach z następujących powodów

1. Połączone konto użytkownika nie ma [dostępu opartego na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) w żadnej subskrypcji lub zasobie klienta platformy Azure.

2. Subskrypcja platformy Azure, w której użytkownik ma dostęp [oparty na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) , nie ma żadnego użycia.

**Czy identyfikator partnera linku współdziała z Azure Stack?**

Tak, możesz połączyć identyfikator partnera, aby uzyskać Azure Stack.
