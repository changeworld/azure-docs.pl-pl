---
title: Dodaj nowe konto dzierżawy usługi Azure Stack w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Po wdrożeniu systemu Microsoft Azure Stack Development Kit, należy utworzyć konta użytkownika co najmniej jednej dzierżawy, dzięki czemu możesz zapoznać się z portalu dzierżawcy.
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 5c07288bbfbf70be62723f835192cf09d92166ab
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163227"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Dodaj nowe konto dzierżawy usługi Azure Stack w usłudze Azure Active Directory

Po [wdrażania usługi Azure Stack Development Kit](azure-stack-run-powershell-script.md), musisz mieć konto użytkownika dzierżawy, możesz eksplorować portalu dzierżawcy i przetestować oferty i plany. Można utworzyć konta dzierżawy przez [przy użyciu witryny Azure portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) lub [przy użyciu programu PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Utwórz konto dzierżawy usługi Azure Stack przy użyciu witryny Azure portal

Musisz mieć subskrypcję platformy Azure za pomocą witryny Azure portal.

1. Zaloguj się do [Azure](https://portal.azure.com).
2. Na pasku nawigacyjnym po lewej stronie wybierz **usługi Active Directory** i przejdź do katalogu, którego chcesz użyć dla usługi Azure Stack lub utworzyć nową.
3. Wybierz **usługi Azure Active Directory** > **użytkowników** > **nowego użytkownika**.

    ![Użytkownicy — Wszyscy użytkownicy strony za pomocą nowego użytkownika z wyróżnioną pozycją](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Na **użytkownika** strony, wprowadź wymagane informacje.

    ![Dodawanie nowego użytkownika, strona użytkownika o informacje o użytkowniku](media/azure-stack-add-new-user-aad/new-user-user.png)

    - **Nazwa (wymagane).** Imię i nazwisko nowego użytkownika. Na przykład Joanna Parker.
    - **Nazwa użytkownika (wymagane).** Nazwa użytkownika nowego użytkownika. Na przykład mary@contoso.com.
        Składowa domeny nazwa użytkownika, należy użyć albo nazwa wstępnej domyślnej domeny, <_NazwaDomeny_>. onmicrosoft.com lub niestandardową nazwę domeny, taką jak contoso.com. Aby uzyskać więcej informacji na temat tworzenia niestandardowej nazwy domeny, zobacz [sposób dodawania niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md).
    - **Profil.** Opcjonalnie można dodać więcej informacji o użytkowniku. Można również dodać informacje o użytkowniku w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania informacji o użytkowniku, zobacz [jak dodać lub zmienić informacje o profilu użytkownika](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).
    - **Rola katalogu.**  Wybierz **użytkownika**.

5. Sprawdź **Pokaż hasło** i skopiuj hasło wygenerowane automatycznie w **hasło** pole. To hasło będzie potrzebne dla początkowego procesu logowania.

6. Wybierz pozycję **Utwórz**.

    Użytkownik zostanie utworzony i dodany do dzierżawy usługi Azure AD.

7. Zaloguj się do portalu Microsoft Azure przy użyciu nowego konta. Zmień hasło po wyświetleniu monitu.
8. Zaloguj się do `https://portal.local.azurestack.external` przy użyciu nowego konta, aby wyświetlić portalu dzierżawcy.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Tworzenie konta użytkownika usługi Azure Stack przy użyciu programu PowerShell

Jeśli nie masz subskrypcji platformy Azure, nie można użyć witryny Azure portal, aby dodać konto użytkownika dzierżawy. W takim przypadku można zamiast tego użyj usługi Azure Active Directory modułu dla Windows PowerShell.

> [!NOTE]
> Jeśli używasz Account firmy Microsoft (Live ID) do wdrożenia usługi Azure Stack Development Kit, nie można utworzyć konta dzierżawy za pomocą programu PowerShell usługi AAD. 

1. Zainstaluj [Microsoft Online Services Sign-In Assistant for IT Professionals RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).
2. Zainstaluj [Azure Active Directory Module for Windows PowerShell (wersja 64-bitowa)](https://go.microsoft.com/fwlink/p/?linkid=236297) i otwórz go.
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

## <a name="next-steps"></a>Kolejne kroki

[Dodaj użytkowników usługi Azure Stack w usługach AD FS](azure-stack-add-users-adfs.md)