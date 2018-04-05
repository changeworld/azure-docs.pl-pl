---
title: Połącz konto platformy Azure do współpracy identyfikator | Dokumentacja firmy Microsoft
description: Śledzenie korzystają z klientów platformy Azure przez łączenie z kontem użytkownika, który umożliwia zarządzanie zasobami klienta identyfikator partnera.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f729a0228c6b0c2f514ab7170299b0271ed28ad2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Identyfikator partnera łącza do konta platformy Azure 
Przez partnera można śledzić wpływu na Twoje w powiadomieniach promujących zaangażowanie klienta, łącząc Twój identyfikator partnera kont, używany do zarządzania zasobami przez klienta.

Ta funkcja jest dostępna w publicznej wersji zapoznawczej. 

## <a name="get-access-from-your-customer"></a>Uzyskanie dostępu do klienta 
Przed połączeniem Twój identyfikator partnera, klient musi uzyskania dostępu do ich zasobów platformy Azure przy użyciu jednej z następujących opcji:

- **Gość:** klienta można dodać Cię jako Gość i przypisać wszystkie role RBAC. Aby uzyskać więcej informacji, zobacz [Dodaj gości z innego katalogu](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Konta katalogu:** klienta można utworzyć nowego użytkownika z Twojej organizacji w ich katalogu i przypisać każdej roli RBAC. 

- **Nazwy głównej usługi:** klienta można dodać aplikacji lub skryptu z Twojej organizacji w ich katalogu i przypisać każdej roli RBAC. Tożsamość aplikacji lub skryptu jest znana jako nazwy głównej usługi.

## <a name="link-partner-id"></a>Identyfikator partnera łącza
Jeśli masz dostęp do zasobów przez klienta, użyć programu PowerShell lub interfejsu wiersza polecenia, aby połączyć identyfikator sieci Microsoft Partner (identyfikator MPN) do Twojej nazwy użytkownika i nazwę główną usługi. Należy połączyć identyfikator partnera dla każdego dzierżawcy klienta. 

### <a name="use-powershell-to-link-new-partner-id"></a>Połącz nowy identyfikator partnera przy użyciu programu PowerShell

1. Zainstaluj [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) modułu programu PowerShell.

2. Zaloguj się do dzierżawy przez klienta z konta użytkownika lub usługę podmiotu zabezpieczeń, aby uzyskać więcej informacji, zobacz [logowania przy użyciu programu Powershell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Login-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Połącz nowy identyfikator partnera. Identyfikator partnera [Network(MPN) partnera firmy Microsoft](https://partner.microsoft.com/) identyfikator organizacji.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator partnera połączonego
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Aktualizacja Identyfikatora partnera połączonego
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Usuń identyfikator połączonego partnera.
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Użyj interfejsu wiersza polecenia, aby połączyć nowy identyfikator partnera
1.  Zainstaluj rozszerzenie interfejsu wiersza polecenia.

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Zaloguj się do dzierżawy przez klienta z konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz [Zaloguj się za pomocą usługi Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Połącz nowy identyfikator partnera. Identyfikator partnera [Network(MPN) partnera firmy Microsoft](https://partner.microsoft.com/) identyfikator organizacji.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Pobierz identyfikator partnera połączonego
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Aktualizacja Identyfikatora partnera połączonego
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Usuń identyfikator połączonego partnera.
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Kto może połączyć identyfikator partnera?**

Każdy użytkownik z organizacji partnerskiej, który jest zarządzany przez klienta zasobu można połączyć konto identyfikator partnera.
  

**Po identyfikatorze partnera jest połączony można go zmienić?**

Tak, identyfikator partnera połączonego można można zmienić, dodane ani usunąć.

**Co zrobić, jeśli użytkownik ma konto w wielu dzierżawców klienta?**

Powiązanie identyfikator partnera i konto odbywa się dla każdego dzierżawcy klienta.  Należy połączyć z Identyfikatorem partnera w każdej dzierżawy klienta.

**Inne partnera lub klienta edytować lub usunąć link do identyfikator partnera?**

Łącze jest skojarzony na poziomie konta. Tylko Edytuj lub Usuń łącze do identyfikator partnera. Klient i drugiego partnera, nie można zmienić łącze identyfikator partnera. 


