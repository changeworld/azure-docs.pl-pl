---
title: Dodaj nazwy głównej usługi do roli administratora serwera usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać nazwy głównej usługi Automatyzacja do roli administratora serwera
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8840dd9a8a8e8ba1f78d461824ac0cdce32b2ac9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596774"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Dodaj nazwy głównej usługi do roli administratora serwera 

 Aby zautomatyzować nienadzorowanej zadania programu PowerShell, muszą mieć nazwy głównej usługi **administrator serwera** uprawnień na serwerze usług Analysis Services zarządzany. W tym artykule opisano, jak dodać nazwy głównej usługi do roli Administratorzy serwera na serwerze jako platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed wykonaniem tego zadania, musi mieć nazwę główną usługi zarejestrowany w usłudze Azure Active Directory.

[Tworzenie nazwy głównej - usługi portalu Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby wykonać to zadanie, musisz mieć [administrator serwera](analysis-services-server-admins.md) uprawnienia na serwerze jako platformy Azure. 

## <a name="add-service-principal-to-server-administrators-role"></a>Dodaj nazwę główną usługi do roli administratora serwera

1. W programie SSMS połączenie z serwerem Azure AS.
2. W **właściwości serwera** > **zabezpieczeń**, kliknij przycisk **Dodaj**.
3. W **Wybieranie użytkownika lub grupy**, Wyszukaj aplikację zarejestrowaną nazwę Wybierz, a następnie kliknij przycisk **Dodaj**.

    ![Wyszukaj konto główne usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator konta głównego usługi, a następnie kliknij przycisk **OK**.
    
    ![Wyszukaj konto główne usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Dla operacji serwera za pomocą poleceń cmdlet AzureRm, główna harmonogram uruchomionej usługi musi również należeć do **właściciela** roli dla zasobu w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł PowerShell programu SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzia SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


