---
title: Dodaj zasadę usługi do roli administratora serwera usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać zasadę usługi Automatyzacja do roli administratora serwera
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9c6417e069bbed38b1f6e9317636a10834ce7197
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Dodaj zasadę usługi do roli administratora serwera 

 Aby zautomatyzować nienadzorowanej zadania programu PowerShell, musi mieć zasady usługi **administrator serwera** uprawnień na serwerze usług Analysis Services zarządzany. W tym artykule opisano sposób dodawania zasady usługi do roli Administratorzy serwera na serwerze jako platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed wykonaniem tego zadania, musi mieć zasady usługi zarejestrowany w usłudze Azure Active Directory.

[Utwórz zasadę usługi - portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — program PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby wykonać to zadanie, musisz mieć [administrator serwera](analysis-services-server-admins.md) uprawnienia na serwerze jako platformy Azure. 

## <a name="add-service-principle-to-server-administrators-role"></a>Dodaj zasadę usługi do roli administratora serwera

1. W programie SSMS połączenie z serwerem Azure AS.
2. W **właściwości serwera** > **zabezpieczeń**, kliknij przycisk **Dodaj**.
3. W **Wybieranie użytkownika lub grupy**, Wyszukaj aplikację zarejestrowaną nazwę Wybierz, a następnie kliknij przycisk **Dodaj**.

    ![Wyszukaj konta zasady usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator konta usługi zasady, a następnie kliknij przycisk **OK**.
    
    ![Wyszukaj konta zasady usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Dla operacji serwera za pomocą poleceń cmdlet AzureRm, zasady usługi harmonogramu uruchamiania musi również należeć do **właściciela** roli dla zasobu w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł PowerShell programu SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzia SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


