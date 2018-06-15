---
title: Zainstaluj zestaw Azure stosu Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: Opisuje sposób instalowania Azure stosu Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 7b8fe61731a9412c61152bc58e55deebb611d011
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30171200"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Zainstaluj zestaw Azure stosu Development Kit (ASDK)
Po [Przygotowywanie komputera hosta ASDK](asdk-prepare-host.md), ASDK można wdrożyć w obrazie CloudBuilder.vhdx, wykonując następujące kroki w tym artykule.

## <a name="install-the-asdk"></a>Zainstaluj ASDK
Kroki opisane w tym artykule opisano sposób wdrażania ASDK przy użyciu graficznego interfejsu użytkownika (GUI) udostępniane przez pobieranie i uruchamianie **asdk installer.ps1** skrypt programu PowerShell.

> [!NOTE]
> Instalator interfejsu użytkownika dla zestawu SDK usługi Azure stosu jest powierzając jej ich konserwację Otwórz skryptu na podstawie WCF i programu PowerShell.


1. Po uruchomieniu pomyślnie komputera hosta w obrazie CloudBuilder.vhdx, logowanie przy użyciu poświadczeń administratora określone podczas możesz [przygotowane na komputerze deweloperskim zestaw hosta](asdk-prepare-host.md) ASDK instalacji. Powinna być taka sama jak poświadczenia administratora lokalnego hosta development kit.
2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i uruchom  **&lt;litera dysku > \AzureStack_Installer\asdk-installer.ps1** skryptu (która może być teraz na innym dysku niż C:\ w obrazie CloudBuilder.vhdx). Kliknij pozycję **Zainstaluj**.

    ![](media/asdk-install/1.PNG) 

3. W dostawcy tożsamości **typu** listy rozwijanej wybierz pozycję **chmury Azure** lub **usług AD FS**. W obszarze **hasło administratora lokalnego** wpisz hasło administratora lokalnego (która musi odpowiadać bieżące hasło administratora lokalnego skonfigurowany) **hasło** , a następnie kliknij przycisk  **Następny**.
    - **Chmury Azure**: umożliwia skonfigurowanie usługi Azure Active Directory (Azure AD) jako dostawcy tożsamości. Aby użyć tej opcji, należy połączenie internetowe, pełna nazwa usługi Azure AD directory dzierżawy w formie *domainname*. onmicrosoft.com lub usługi Azure AD zweryfikować niestandardowej nazwy domeny i poświadczenia administratora globalnego dla określonego katalog. 
    - **Usługi AD FS**: Usługa katalogowa sygnatury domyślny jest używany jako dostawca tożsamości. Zaloguj się przy użyciu domyślnego konta jest azurestackadmin@azurestack.local, i hasło używane jest dostarczana jako część instalacji.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Aby uzyskać najlepsze wyniki nawet jeśli ma być używany w środowisku bez połączenia stosu Azure za pomocą usług AD FS jako dostawca tożsamości, najlepiej zainstalować ASDK podczas połączenia z Internetem. W ten sposób można aktywować wersję ewaluacyjną systemu Windows Server 2016 dołączone do rozwoju Instalacja zestawu w czasie wdrażania.
4. Wybierz kartę sieciową do użycia dla zestawu SDK, a następnie kliknij przycisk **dalej**.

    ![](media/asdk-install/3.PNG)

5. Wybierz DHCP lub konfiguracji statycznej sieci dla maszyny wirtualnej BGPNAT01.
    > [!TIP]
    > Maszyna wirtualna BGPNAT01 jest router brzegowy, który zapewnia możliwości translatora adresów Sieciowych i sieci VPN Azure stosu.

    - **DHCP** (domyślnie): maszyny wirtualnej pobiera konfigurację sieci IP od serwera DHCP.
    - **Statyczne**: tej opcji należy używać tylko wtedy, gdy DHCP nie można przypisać prawidłowy adres IP dla stosu Azure na dostęp do Internetu. **Należy określić statyczny adres IP o długości maska podsieci w formacie CIDR (na przykład 10.0.0.5/24)**.
    - Wpisz w prawidłowy **czasu adres IP serwera** adres. Wymaganego pola ustawia czas server używanego przez zestaw deweloperski. Ten parametr musi być dostarczona jako adres IP serwera czas ważności. Nazwy serwerów nie są obsługiwane.

      > [!TIP]
      > Aby znaleźć adres IP serwera czasu, odwiedź stronę [pool.ntp.org](http:\\pool.ntp.org) lub polecenie ping time.windows.com. 

    - **Opcjonalnie**, ustaw następujące wartości:
        - **Identyfikator sieci VLAN**: Określa identyfikator sieci VLAN. Tej opcji należy używać tylko wtedy, gdy host i AzS BGPNAT01 należy skonfigurować identyfikator sieci VLAN umożliwiający dostęp do sieci fizycznej (i Internetu). 
        - **Usługa przesyłania dalej DNS**: serwer DNS jest tworzony jako część wdrożenia stosu Azure. Aby umożliwić komputerom wewnątrz rozwiązania do rozpoznawania nazw poza sygnatury, podaj istniejącej infrastruktury serwera DNS. Serwer DNS w sygnatury przekazuje żądania rozpoznania nieznanej nazwy do tego serwera.

    ![](media/asdk-install/4.PNG)

6. Na **Weryfikowanie właściwości karty interfejsu sieciowego** strony, zostanie wyświetlony pasek postępu. Po zakończeniu weryfikacji kliknij **dalej**.

    ![](media/asdk-install/5.PNG)

9. Na **Podsumowanie** kliknij przycisk **Wdróż** do rozpoczęcia instalacji ASDK na komputerze hosta development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > W tym miejscu można także skopiować ustawienia poleceń programu PowerShell, które będą używane do zainstalowania z zestawem deweloperskim. Jest to przydatne, jeśli kiedykolwiek zajdzie potrzeba [ponownie wdrożyć ASDK na komputerze hosta przy użyciu programu PowerShell](asdk-deploy-powershell.md).

10. Jeśli przeprowadzasz wdrożenie usługi Azure AD zostanie wyświetlony monit o wprowadzenie poświadczeń konta administratora globalnego usługi Azure AD za kilka minut po uruchomieniu Instalatora.

    ![](media/asdk-install/7.PNG)

11. Proces wdrażania potrwa kilka godzin, w tym czasie komputer zostanie automatycznie ponownie uruchomiony jeden raz. Jeśli chcesz monitorować postęp wdrażania, zaloguj się jako azurestack\AzureStackAdmin po ponownym uruchomieniu hosta zestawu programowanie. Gdy wdrożenie zakończy się powodzeniem, konsoli programu PowerShell Wyświetla: **COMPLETE: Akcja "Wdrożenie"**. 
    > [!IMPORTANT]
    > Jeśli zarejestrujesz się jako administrator lokalny po komputer jest przyłączony do domeny, nie będzie wyświetlany jest postęp wdrażania. Ponownie uruchom wdrożenie, nie zamiast tego zaloguj się jako azurestack\AzureStackAdmin można sprawdzić, czy jest uruchomiona.

    ![](media/asdk-install/8.PNG)

Gratulacje, po zainstalowaniu pomyślnie ASDK!

Jeśli wdrożenie nie powiedzie się z jakiegoś powodu, możesz [ponownie wdrożyć](asdk-redeploy.md) od podstaw lub użyj programu PowerShell następujących poleceń, z tego samego okna programu PowerShell z podwyższonym poziomem uprawnień, uruchom ponownie wdrażanie w ostatnim kroku powiodło się:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Kolejne kroki
[Konfiguracja wdrożenia POST](asdk-post-deploy.md)