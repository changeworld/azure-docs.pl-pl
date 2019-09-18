---
title: Łączenie konta platformy Azure z identyfikatorem partnera | Microsoft Docs
description: Śledź kontakty z klientami platformy Azure, łącząc identyfikator partnera z kontem użytkownika, którego używasz do zarządzania zasobami klienta.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68706400"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Łączenie identyfikatora partnera z kontami platformy Azure

Partnerzy firmy Microsoft oferują usługi, które pomagają klientom osiągać cele biznesowe i ogólne przy użyciu produktów firmy Microsoft. Podejmując działania w imieniu klienta podczas konfigurowania i obsługiwania usług platformy Azure oraz zarządzania nimi, użytkownicy partnera będą musieli uzyskać dostęp do środowiska klienta. Korzystając z łącza administratora partnera, partnerzy mogą powiązać identyfikator sieci partnera z poświadczeniami używanymi do dostarczania usług.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Uzyskanie dostępu od klienta

Przed połączeniem identyfikatora partnera klient musi zapewnić dostęp do swoich zasobów platformy Azure, korzystając z jednego z następujących rozwiązań:

- **Użytkownik-gość**: Klient może dodać Cię jako użytkownika-gościa i przypisać role kontroli dostępu na podstawie ról (RBAC). Aby uzyskać więcej informacji, zobacz temat [Add guest users from another directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (Dodawanie użytkowników-gości z innego katalogu).

- **Konto katalogu**: Klient może utworzyć dla Ciebie konto użytkownika w swoim własnym katalogu i przypisać dowolną rolę RBAC.

- **Jednostka usługi**: Klient może dodać aplikację lub skrypt z organizacji w swoim katalogu i przypisać dowolną rolę RBAC. Tożsamość aplikacji lub skryptu jest znana jako nazwa główna usługi.

## <a name="link-to-a-partner-id"></a>Łączenie z identyfikatorem partnera

Jeśli masz dostęp do zasobów klienta, użyj witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator MPN (Microsoft Partner Network) z identyfikatorem użytkownika lub nazwą główną usługi. Połącz identyfikator partnera w każdej dzierżawie klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użycie witryny Azure Portal do łączenia z nowym identyfikatorem partnera

1. Przejdź do sekcji [Łączenie z identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure Portal.

2. Zaloguj się do Portalu Azure.

3. Wprowadź identyfikator partnera firmy Microsoft. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

   ![Zrzut ekranu przedstawiający sekcję Łączenie z identyfikatorem partnera](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Aby połączyć identyfikator partnera innego klienta, przełącz katalog. W obszarze **Przełącz katalog** wybierz swój katalog.

   ![Zrzut ekranu przedstawiający obszar Przełącz katalog](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Używanie programu PowerShell do łączenia z nowym identyfikatorem partnera

1. Zainstaluj moduł [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) programu PowerShell.

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się w programie Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Pobieranie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizowanie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Usuwanie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure do połączenia z nowym identyfikatorem partnera
1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Zaloguj się do dzierżawy klienta, używając konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Pobieranie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualizowanie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Usuwanie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Następne kroki

Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion), aby otrzymywać aktualizacje lub wysłać opinię.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Kto może połączyć identyfikator partnera?**

Identyfikator partnera z kontem może połączyć każdy użytkownik z organizacji partnerskiej, który zarządza zasobami platformy Azure klienta.

**Czy identyfikator partnera można zmienić po jego połączeniu?**

Tak. Połączony identyfikator partnera można zmienić, dodać lub usunąć.

**Co należy zrobić, jeśli użytkownik ma konto w więcej niż jednej dzierżawie klienta?**

Połączenie między identyfikatorem partnera i kontem jest wykonywane dla każdej dzierżawy klienta. Połącz identyfikator partnera w każdej dzierżawie klienta.

**Czy inni partnerzy lub klienci mogą edytować lub usunąć hiperlink do identyfikatora partnera?**

Hiperlink jest skojarzony na poziomie konta użytkownika. Tylko Ty możesz edytować lub usunąć hiperlink do identyfikatora partnera. Klient i inni partnerzy nie mogą zmienić hiperlinku do identyfikatora partnera.


**Którego identyfikatora MPN należy użyć, jeśli moja firma ma ich wiele?**

Do połączenia identyfikatora partnera należy użyć kont Partner Location Account i powiązanych identyfikatorów MPN.  Dowiedz się więcej na temat [kont partnerów](https://docs.microsoft.com/partner-center/account-structure).

**Gdzie można znaleźć uwzględnione raportowanie przychodów dla połączonego identyfikatora partnera?**

Raportowanie wydajności produktu w chmurze jest dostępne dla partnerów na [pulpicie nawigacyjnym Moje usługi Insights](https://partner.microsoft.com/membership/reports/myinsights) w Centrum partnerskim. Jako typ powiązania partnera należy wybrać hiperlink administratora partnera.

**Dlaczego nie widzę mojego klienta w raportach?**

Klient może być niewidoczny w raportach z następujących powodów:

1. Połączone konto użytkownika nie ma [dostępu opartego na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview) w żadnej subskrypcji ani zasobie klienta platformy Azure.

2. Subskrypcja platformy Azure, w której użytkownik ma [dostęp oparty na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview), nie ma żadnego użycia.

**Czy identyfikator partnera hiperlinku współdziała z usługą Azure Stack?**

Tak. Identyfikator partnera można połączyć z usługą Azure Stack.
