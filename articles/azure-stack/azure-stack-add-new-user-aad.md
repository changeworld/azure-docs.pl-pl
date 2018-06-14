---
title: Dodaj nowe konto dzierżawy Azure stosu w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Po wdrożeniu systemu Microsoft Azure stosu Development Kit, musisz utworzyć konto użytkownika co najmniej jednego dzierżawcy, aby można było eksplorować portalu dzierżawcy.
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
ms.openlocfilehash: 590426563936c66b1353f769be138759bb53f58c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2018
ms.locfileid: "29553209"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Dodaj nowe konto dzierżawy Azure stosu w usłudze Azure Active Directory
Po [wdrażanie Azure stosu Development Kit](azure-stack-run-powershell-script.md), potrzebujesz konta użytkownika dzierżawcy, aby można było eksplorować portalu dzierżawcy i przetestować oferty i planów. Możesz utworzyć konto dzierżawy przez [przy użyciu portalu Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) lub [przy użyciu programu PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Tworzenie konta dzierżawy Azure stosu przy użyciu portalu Azure
Musi mieć subskrypcję platformy Azure za pomocą portalu Azure.

1. Zaloguj się do [Azure](https://portal.azure.com).
2. Na pasku nawigacyjnym po lewej stronie Microsoft Azure, kliknij przycisk **usługi Active Directory**.
3. Na liście katalogu kliknij katalog, który ma być używany dla stosu Azure lub Utwórz nową.
4. Na tej stronie katalogu kliknij **użytkowników**.
5. Kliknij przycisk **Dodaj użytkownika**.
6. W **Dodaj użytkownika** w kreatorze **typ użytkownika** wybierz **nowy użytkownik w organizacji**.
7. W **nazwy użytkownika** wpisz nazwę użytkownika.
8. W  **@**  , wybierz odpowiedni wpis.
9. Kliknij strzałkę dalej.
10. W **profilu użytkownika** kreatora, a typ **imię**, **nazwisko**, i **Nazwa wyświetlana**.
11. W **roli** wybierz **użytkownika**.
12. Kliknij strzałkę dalej.
13. Na **Uzyskaj hasło tymczasowe** kliknij przycisk **Utwórz**.
14. Kopiuj **nowe hasło**.
15. Zaloguj się do systemu Microsoft Azure przy użyciu nowego konta. Zmień hasło po wyświetleniu monitu.
16. Zaloguj się do `https://portal.local.azurestack.external` o nowe konto, aby wyświetlić portal dzierżawcy.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Tworzenie konta dzierżawy Azure stosu przy użyciu programu PowerShell
Jeśli nie masz subskrypcji platformy Azure, nie można użyć portalu Azure, aby dodać konto użytkownika dzierżawcy. W takim przypadku należy użyć usługi Azure Active Directory modułu dla środowiska Windows PowerShell.

> [!NOTE]
> Jeśli używasz Account Microsoft (Live ID) do wdrożenia usługi Azure stosu Development Kit, nie można utworzyć konta dzierżawy za pomocą programu PowerShell usługi AAD. 
> 
> 

1. Zainstaluj [Microsoft Online Services Asystenta logowania dla specjalistów IT RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Zainstaluj [Azure Active Directory modułu dla środowiska Windows PowerShell (wersja 64-bitowa)](http://go.microsoft.com/fwlink/p/?linkid=236297) i otwórz go.
3. Uruchom następujące polecenia cmdlet:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Zaloguj się do systemu Microsoft Azure za pomocą nowego konta. Zmień hasło po wyświetleniu monitu.
2. Zaloguj się do `https://portal.local.azurestack.external` o nowe konto, aby wyświetlić portal dzierżawcy.

