---
title: Połącz konto Azure, aby identyfikator partnera | Dokumentacja firmy Microsoft
description: Śledź interakcje z klientami platformy Azure, tworząc konto użytkownika, którego używasz do zarządzania zasobami przez klienta identyfikator partnera.
services: billing
author: dhirajgandhi
ms.author: cwatson
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8226ff956db1b21c308d184e8d5876a59d1b646d
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391782"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Identyfikator partnera linku do kont systemu Azure

Jako partner możesz śledzić swoje oddziaływanie w powiadomieniach promujących zaangażowanie klientów przez połączenie z Partnerem w kontach używanych do zarządzania zasobami przez klienta.

Ta funkcja jest dostępna w publicznej wersji zapoznawczej.

## <a name="get-access-from-your-customer"></a>Uzyskiwanie dostępu do klienta

Aby połączyć się z Partnerem, klient musi udzielić Ci dostępu do swoich zasobów platformy Azure przy użyciu jednej z następujących opcji:

- **Użytkownik-Gość:** klienta można dodał Cię jako użytkownika-gościa i przypisać wszystkie role RBAC. Aby uzyskać więcej informacji, zobacz [dodać użytkowników-gości z innego katalogu](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Konto Directory:** klienta można utworzyć nowego użytkownika z Twojej organizacji w jego katalogu i przyznawać żadnej roli RBAC.

- **Nazwa główna usługi:** dodać aplikację lub skrypt z Twojej organizacji w jego katalogu i przyznawać żadnej roli RBAC klienta. Tożsamość aplikacji lub skryptu jest określany jako nazwy głównej usługi.

## <a name="link-partner-id"></a>Identyfikator partnera linku

Jeśli masz dostęp do zasobów przez klienta, usługa witryny Azure portal, programu PowerShell lub interfejsu wiersza polecenia umożliwia łączenie identyfikator sieci Microsoft Partner (identyfikator MPN) do Identyfikatora użytkownika lub nazwy głównej usługi. Należy połączyć Identyfikatora partnera w każdej dzierżawy klienta.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Łącze Nowy identyfikator partnera za pomocą witryny Azure portal

1. Przejdź do [Połącz z Identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure portal.

2. Zaloguj się do Portalu Azure.

3. Wprowadź identyfikator partnera firmy Microsoft Partner ten identyfikator jest [Network(MPN) partnera firmy Microsoft](https://partner.microsoft.com/) ID organizacji.

  ![Zrzut ekranu pokazujący identyfikator partnera linku](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Aby połączyć Identyfikatora partnera do innego klienta, użyj przełącznika katalogów. W obszarze Przełącz katalog wybierz katalog.

  ![Zrzut ekranu pokazujący identyfikator partnera linku](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Aby połączyć nowy identyfikator partnera przy użyciu programu PowerShell

1. Zainstaluj [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) modułu programu PowerShell.

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub usługę podmiotu zabezpieczeń, aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu programu Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Łącze Nowy identyfikator partnera. Partner ten identyfikator jest [Network(MPN) partnera firmy Microsoft](https://partner.microsoft.com/) ID organizacji.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator połączonej, partnera
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Aktualizacja Identyfikatora połączonej, partnera
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Usuń identyfikator połączonej, partnera
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Użyj interfejsu wiersza polecenia, aby połączyć nowy identyfikator partnera
1.  Zainstaluj rozszerzenie interfejsu wiersza polecenia.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Zaloguj się przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Łącze Nowy identyfikator partnera. Partner ten identyfikator jest [Network(MPN) partnera firmy Microsoft](https://partner.microsoft.com/) ID organizacji.

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

Każdy użytkownik z organizacji partnerskiej, zarządza zasobu klienta można połączyć Identyfikatora partnera na koncie.

**Po identyfikator partnera jest połączony można go zmienić?**

Tak, identyfikator partnera połączone mogą być zmienione, dodane lub usunięte.

**Co zrobić, jeśli użytkownik ma konto usługi w wielu dzierżaw klientów?**

Łącze między identyfikator partnera, a kontem odbywa się dla każdej dzierżawy klienta.  Należy połączyć Identyfikatora partnera w każdej dzierżawy klienta.

**Inne partnera lub klienta edytować lub usunąć łącze, aby identyfikator partnera?**

Łącze jest skojarzone na poziomie konta. Tylko Edytuj lub Usuń łącze do identyfikatora partnera. Innych partnerów i klientów nie można zmienić link do identyfikatora partnera. 
