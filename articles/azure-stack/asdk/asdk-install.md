---
title: Zainstaluj zestaw Azure Stack Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób instalowania usługi Azure Stack Development Kit (ASDK).
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
ms.date: 09/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 09/10/2018
ms.openlocfilehash: 363e0868542f56df8c37639b2af7ac295be97da2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249909"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Zainstaluj zestaw Azure Stack Development Kit (ASDK)
Po [Przygotowywanie komputera hosta ASDK](asdk-prepare-host.md), ASDK można wdrożyć w obrazie CloudBuilder.vhdx wykonując następujące kroki w tym artykule.

## <a name="install-the-asdk"></a>Zainstaluj ASDK
Kroki opisane w tym artykule pokazano, jak wdrożyć ASDK przy użyciu graficznego interfejsu użytkownika (GUI) dostarczonych przez pobranie i uruchomienie **asdk installer.ps1** skrypt programu PowerShell.

> [!NOTE]
> Interfejs użytkownika Instalatora programu Azure Stack Development Kit jest skrypt open source, na podstawie usługi WCF i programu PowerShell.


1. Po uruchomieniu pomyślnie komputera hosta do obrazu CloudBuilder.vhdx, zaloguj się przy użyciu poświadczeń administratora określony, gdy użytkownik [przygotowanym komputerze-hoście development kit](asdk-prepare-host.md) ASDK instalacji. Powinna to być taka sama jak poświadczenia administratora lokalnego development kit hosta.
2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i uruchom  **&lt;litera dysku > \AzureStack_Installer\asdk-installer.ps1** skryptu (która może znajdować się na innym dysku niż C:\ na obrazie CloudBuilder.vhdx). Kliknij pozycję **Zainstaluj**.

    ![](media/asdk-install/1.PNG) 

3. W dostawcy tożsamości **typu** listy rozwijanej wybierz pozycję **w chmurze platformy Azure** lub **usług AD FS**. W obszarze **hasło administratora lokalnego** wpisz hasło administratora lokalnego (który musi być zgodna bieżące hasło administratora lokalnego skonfigurowanego) **hasło** , a następnie kliknij przycisk  **Następny**.
    - **Chmura azure**: Umożliwia skonfigurowanie usługi Azure Active Directory (Azure AD) jako dostawcy tożsamości. Aby użyć tej opcji, konieczne jest połączenie internetowe, pełna nazwa usługi Azure AD directory dzierżawy w formie *nazwa_domeny*. onmicrosoft.com lub usługi Azure AD zweryfikować niestandardowej nazwy domeny i poświadczenia administratora globalnego dla określonego katalog. Po wdrożeniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo ponownie przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.
    - **AD FS**: Domyślna usługa katalogowa sygnatura jest używany jako dostawcy tożsamości. Domyślne konto do zalogowania to azurestackadmin@azurestack.local, a hasło do użycia jest dostarczana jako część instalacji.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Aby uzyskać najlepsze wyniki nawet, jeśli chcesz użyć środowiska bez połączenia usługi Azure Stack jako dostawcy tożsamości za pomocą usług AD FS jest najlepiej zainstalować ASDK po nawiązaniu połączenia z Internetem. W ten sposób można aktywować wersję ewaluacyjną systemu Windows Server 2016, które są dołączone do rozwoju Instalacja zestawu w czasie wdrażania.
4. Wybierz kartę sieciową do użycia dla zestawu SDK, a następnie kliknij przycisk **dalej**.

    ![](media/asdk-install/3.PNG)

5. Wybierz pozycję DHCP lub konfiguracji statycznej sieci dla maszyny wirtualnej BGPNAT01.
    > [!TIP]
    > Maszyna wirtualna BGPNAT01 jest routera brzegowego, która udostępnia funkcje translatora adresów Sieciowych i sieci VPN dla usługi Azure Stack.

    - **DHCP** (ustawienie domyślne): Maszyna wirtualna pobiera konfigurację sieci IP od serwera DHCP.
    - **Statyczna**: Tej opcji należy używać tylko wtedy, gdy DHCP nie może przypisać prawidłowy adres IP dla usługi Azure Stack uzyskać dostęp do Internetu. **Należy określić statyczny adres IP o długości maska podsieci w formacie CIDR (na przykład 10.0.0.5/24)**.
    - Wpisz prawidłową **czas adres IP serwera** adresu. To wymagane pole zestawy serwer czasu, który będzie używany przez zestaw development kit. Ten parametr musi zostać podana jako adres IP serwera prawidłową godzinę. Nazwy serwerów nie są obsługiwane.

      > [!TIP]
      > Serwer czasu adres IP można znaleźć [pool.ntp.org](http://pool.ntp.org) lub wysłać polecenie ping time.windows.com. 

    - **Opcjonalnie**, ustaw następujące wartości:
        - **IDENTYFIKATOR SIECI VLAN**: Ustawia identyfikator sieci VLAN. Tej opcji należy używać tylko wtedy, gdy host i AzS-BGPNAT01, należy skonfigurować identyfikator sieci VLAN do uzyskania dostępu do sieci fizycznej (i Internetu). 
        - **Usługa przesyłania dalej DNS**: Serwer DNS jest tworzona jako część wdrożenia usługi Azure Stack. Aby zezwolić komputerom wewnątrz rozwiązania do rozpoznawania nazw poza sygnatury, podaj istniejącej infrastruktury serwera DNS. Serwer DNS w sygnatury przekazuje żądania rozpoznania nieznanej nazwy do tego serwera.

    ![](media/asdk-install/4.PNG)

6. Na **Weryfikowanie właściwości karty interfejsu sieciowego** stronie zostanie wyświetlony pasek postępu. Po zakończeniu weryfikacji kliknij przycisk **dalej**.

    ![](media/asdk-install/5.PNG)

9. Na **Podsumowanie** kliknij **Wdróż** aby rozpocząć instalację ASDK na komputerze-hoście development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > W tym miejscu możesz również skopiować Instalator poleceń programu PowerShell, które będą używane do zainstalowania deweloperski. Jest to przydatne, jeśli kiedykolwiek zajdzie potrzeba [ponownie wdrożyć ASDK na komputerze-hoście przy użyciu programu PowerShell](asdk-deploy-powershell.md).

10. Jeśli przeprowadzasz wdrożenie usługi Azure AD zostanie wyświetlony monit o podanie poświadczeń konta administratora globalnego usługi Azure AD za kilka minut, po uruchomieniu Instalatora.

    ![](media/asdk-install/7.PNG)

11. Proces wdrażania potrwa kilka godzin, podczas których komputer-host zostanie automatycznie ponownie uruchomiony jeden raz. Jeśli chcesz monitorować postęp wdrażania, zaloguj się jako azurestack\AzureStackAdmin po ponownym uruchomieniu hosta kit rozwoju. Po pomyślnym wdrożeniu Wyświetla konsoli programu PowerShell: **NALEŻY WYKONAĆ: Akcja "Wdrożenie"**. 
    > [!IMPORTANT]
    > Jeśli rejestrujesz się jako administrator lokalny po komputer jest przyłączony do domeny, nie będzie wyświetlany postęp wdrożenia. Ponownie uruchom wdrożenie, nie zamiast tego zaloguj się jako azurestack\AzureStackAdmin można sprawdzić, czy jest uruchomiona.

    ![](media/asdk-install/8.PNG)

Gratulacje, pomyślnie zainstalowanych ASDK!

Jeśli wdrożenie nie powiedzie się z jakiegoś powodu, możesz to zrobić [ponownie wdrożyć](asdk-redeploy.md) od podstaw lub użyj następujące polecenia programu PowerShell, w tym samym oknie programu PowerShell z podwyższonym poziomem uprawnień, aby ponownie uruchomić wdrożenia w ostatnim kroku pomyślnie:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Kolejne kroki
[Po wdrożeniu](asdk-post-deploy.md)
