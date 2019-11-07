---
title: Dodaj nazwę główną usługi do roli administratora Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak dodać nazwę główną usługi Automation do roli administratora serwera Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0a3a86283c8ec9876fbec049a2a1a110eb1a80f3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573615"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Dodawanie jednostki usługi do roli administratora serwera 

 Aby zautomatyzować nienadzorowane zadania programu PowerShell, jednostka usługi musi mieć uprawnienia **administratora serwera** na zarządzanym serwerze Analysis Services. W tym artykule opisano sposób dodawania nazwy głównej usługi do roli Administratorzy serwera na platformie Azure jako serwer.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed ukończeniem tego zadania musisz mieć nazwę główną usługi zarejestrowaną w Azure Active Directory.

[Tworzenie jednostki usługi —  Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)  
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby wykonać to zadanie, musisz mieć uprawnienia [administratora serwera](analysis-services-server-admins.md) na platformie Azure jako serwer. 

## <a name="add-service-principal-to-server-administrators-role"></a>Dodawanie nazwy głównej usługi do roli Administratorzy serwera

1. W programie SSMS Połącz się z platformą Azure jako serwerem.
2. W obszarze **Właściwości serwera** > **zabezpieczenia**kliknij przycisk **Dodaj**.
3. W obszarze **Wybierz użytkownika lub grupę**Wyszukaj zarejestrowaną aplikację według nazwy, wybierz pozycję, a następnie kliknij przycisk **Dodaj**.

    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator konta nazwy głównej usługi, a następnie kliknij przycisk **OK**.
    
    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> W przypadku operacji serwera przy użyciu poleceń cmdlet Azure PowerShell, jednostka usługi z uruchomionym harmonogramem musi również należeć do roli **właściciela** zasobu w [Access Control opartej na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł SQL Server PowerShell](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzie SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


