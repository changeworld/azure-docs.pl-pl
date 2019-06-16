---
title: Dodawanie jednostki usługi do roli administratora serwera usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać nazwy głównej usługi automation do roli administratora serwera
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 701be795ca217c4a2dc5a7dbaa3a3717d16c85bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61024628"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Dodawanie jednostki usługi do roli administratora serwera 

 Aby zautomatyzować zadania instalacji nienadzorowanej programu PowerShell, musi mieć nazwę główną usługi **administrator serwera** uprawnień na serwerze usług Analysis Services są zarządzane. Ten artykuł zawiera opis sposobu dodawania jednostki usługi do roli Administratorzy serwera na serwerze Azure AS.

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed wykonaniem tego zadania, musi mieć nazwę główną usługi zarejestrowane w usłudze Azure Active Directory.

[Tworzenie jednostki usługi — witryna Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby wykonać to zadanie, konieczne jest posiadanie [administrator serwera](analysis-services-server-admins.md) uprawnień serwera usług Azure AS. 

## <a name="add-service-principal-to-server-administrators-role"></a>Dodaj nazwę główną usługi do roli Administratorzy serwera

1. W programie SSMS nawiązać połączenia z serwerem usług AS. platformy Azure.
2. W **właściwości serwera** > **zabezpieczeń**, kliknij przycisk **Dodaj**.
3. W **zaznacz użytkownika lub grupę**, Wyszukaj aplikację zarejestrowane przy użyciu nazwy, wybierz i kliknij przycisk **Dodaj**.

    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator podmiotu zabezpieczeń konta usługi, a następnie kliknij przycisk **OK**.
    
    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> W przypadku operacji serwera przy użyciu poleceń cmdlet programu Azure PowerShell jednostki uruchomione narzędzie harmonogramu usługi musi również należeć do **właściciela** roli dla zasobu w [based kontroli dostępu (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł PowerShell programu SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Pobieranie programu SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


