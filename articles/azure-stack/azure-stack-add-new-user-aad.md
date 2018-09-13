---
title: Dodaj nowe konto dzierżawy usługi Azure Stack w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Po wdrożeniu systemu Microsoft Azure Stack Development Kit, należy utworzyć konta użytkownika co najmniej jednej dzierżawy, dzięki czemu możesz zapoznać się z portalu dzierżawcy.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c312658750c1e9ef024a837ccc16e5cd5be8a5ef
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35940552"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Dodaj nowe konto dzierżawy usługi Azure Stack w usłudze Azure Active Directory
Po [wdrażania usługi Azure Stack Development Kit](azure-stack-run-powershell-script.md), musisz mieć konto użytkownika dzierżawy, możesz eksplorować portalu dzierżawcy i przetestować oferty i plany. Można utworzyć konta dzierżawy przez [przy użyciu witryny Azure portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) lub [przy użyciu programu PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Utwórz konto dzierżawy usługi Azure Stack przy użyciu witryny Azure portal
Musisz mieć subskrypcję platformy Azure za pomocą witryny Azure portal.

1. Zaloguj się do [Azure](https://portal.azure.com).
2. Na pasku nawigacyjnym po lewej stronie Microsoft Azure, kliknij **usługi Active Directory**.
3. Na liście katalog kliknij katalog, który chcesz użyć dla usługi Azure Stack lub utworzyć nową.
4. Na tej stronie katalogu kliknij **użytkowników**.
5. Kliknij pozycję **Dodaj użytkownika**.
6. W **Dodaj użytkownika** kreatora **typ użytkownika** wybierz **nowy użytkownik w organizacji**.
7. W **nazwa_użytkownika** wpisz nazwę użytkownika.
8. W **@** , wybierz odpowiedni wpis.
9. Kliknij strzałkę dalej.
10. W **profilu użytkownika** strony kreatora, a typ **imię**, **nazwisko**, i **nazwę wyświetlaną**.
11. W **roli** wybierz **użytkownika**.
12. Kliknij strzałkę dalej.
13. Na **Uzyskaj hasło tymczasowe** kliknij **Utwórz**.
14. Kopiuj **nowe hasło**.
15. Zaloguj się do systemu Microsoft Azure przy użyciu nowego konta. Zmień hasło po wyświetleniu monitu.
16. Zaloguj się do `https://portal.local.azurestack.external` przy użyciu nowego konta, aby wyświetlić portalu dzierżawcy.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Utwórz konto dzierżawy usługi Azure Stack przy użyciu programu PowerShell
Jeśli nie masz subskrypcji platformy Azure, nie można użyć witryny Azure portal, aby dodać konto użytkownika dzierżawy. W takim przypadku można zamiast tego użyj usługi Azure Active Directory modułu dla Windows PowerShell.

> [!NOTE]
> Jeśli używasz Account firmy Microsoft (Live ID) do wdrożenia usługi Azure Stack Development Kit, nie można utworzyć konta dzierżawy za pomocą programu PowerShell usługi AAD. 
> 
> 

1. Zainstaluj [Microsoft Online Services Sign-In Assistant for IT Professionals RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Zainstaluj [Azure Active Directory Module for Windows PowerShell (wersja 64-bitowa)](http://go.microsoft.com/fwlink/p/?linkid=236297) i otwórz go.
3. Uruchom następujące polecenia cmdlet:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Zaloguj się do systemu Microsoft Azure przy użyciu nowego konta. Zmień hasło po wyświetleniu monitu.
2. Zaloguj się do `https://portal.local.azurestack.external` przy użyciu nowego konta, aby wyświetlić portalu dzierżawcy.

