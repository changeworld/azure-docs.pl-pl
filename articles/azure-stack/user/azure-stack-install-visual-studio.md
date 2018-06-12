---
title: Zainstaluj program Visual Studio i połącz się stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się na krokach wymaganych do instalacji programu Visual Studio i połącz się stosu Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295034"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Zainstaluj program Visual Studio i połącz się stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można użyć programu Visual Studio Aby zapisać i wdrożyć usługi Azure Resource Manager [szablony](azure-stack-arm-templates.md) stos Azure. Kroki opisane w tym artykule umożliwia przeprowadzenie instalacji programu Visual Studio na [stosu Azure](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), lub komputerze zewnętrznego, jeśli planujesz stosu Azure za pośrednictwem [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="install-visual-studio"></a>Instalacja programu Visual Studio

1. Pobierz i uruchom [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Otwórz **Instalatora platformy sieci Web firmy Microsoft**.

3. Wyszukaj **Visual Studio Community 2015 z zestawu SDK platformy Microsoft Azure - 2.9.6**. Kliknij przycisk **dodać**, i **zainstalować**.

4. Odinstaluj **Microsoft Azure PowerShell** który jest instalowany jako część zestawu SDK platformy Azure.

    ![Zrzut ekranu WebPI kroki instalacji](./media/azure-stack-install-visual-studio/image1.png) 

5. [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)

6. Po zakończeniu instalacji uruchom ponownie system operacyjny.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Połącz stos Azure z usługą Azure AD

1. Uruchom program Visual Studio.

2. Z **widoku** menu, wybierz opcję **Eksplorator chmury**.

3. W okienku, wybierz **Dodaj konto** i zaloguj się przy użyciu poświadczeń usługi Azure Active Directory (Azure AD).  

    ![Zrzut ekranu Eksplorator chmury raz zalogowany i podłączony do stosu Azure](./media/azure-stack-install-visual-studio/image2.png)

Po zalogowaniu, możesz [wdrażanie szablonów](azure-stack-deploy-template-visual-studio.md) lub przeglądanie dostępnych typów zasobów i grup zasobów, aby tworzyć własne szablony.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Połącz Azure stos z usługami AD FS

1. Uruchom program Visual Studio.

2. Z **narzędzia**, wybierz pozycję **opcje**.

3. Rozwiń węzeł **środowiska** w **okienka nawigacji** i wybierz **kont**.

4. Wybierz **Dodaj**i wprowadź punktu końcowego użytkownika usługi Azure Resource Manager.  
  Azure stosu Development kit, adres URL jest: `https://management.local.azurestack/external`.  
  Stos Azure zintegrowanych systemów adres URL jest: `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Wybierz pozycję **Dodaj**.  

    Program Visual Studio wywołuje Menedżera zasobów Azure i umożliwia odnalezienie punktów końcowych, łącznie z punktu końcowego uwierzytelniania dla usług federacyjnych Azure Directory (AD FS).

    ![Zrzut ekranu Eksplorator chmury raz zalogowany i podłączony do stosu Azure](./media/azure-stack-install-visual-studio/image6.png)

6. Wybierz **Eksplorator chmury** z **widoku** menu.
7. Wybierz **Dodaj konto** i zaloguj się przy użyciu poświadczeń usług AD FS.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    Eksplorator chmury kwerendy dostępnych subskrypcji. Można wybrać jedną subskrypcję dostępne do zarządzania.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. Przeglądanie z istniejących zasobów, grupy zasobów lub wdrażanie szablonów.

## <a name="next-steps"></a>Kolejne kroki

 - Przeczytaj więcej na temat [współistnienie](https://msdn.microsoft.com/library/ms246609.aspx) z innymi wersjami programu Visual Studio.
 - [Tworzenie szablonów dla stosu Azure](azure-stack-develop-templates.md)