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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 509dafb02d456e3f612e1de8a5bc1cf4b010a05a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113860"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Zainstaluj zestaw Azure Stack Development Kit (ASDK)
Po [Przygotowywanie komputera hosta ASDK](asdk-prepare-host.md), ASDK można wdrożyć w obrazie CloudBuilder.vhdx wykonując następujące kroki w tym artykule.

## <a name="install-the-asdk"></a>Zainstaluj ASDK
Kroki opisane w tym artykule pokazano, jak wdrożyć ASDK przy użyciu graficznego interfejsu użytkownika (GUI) dostarczonych przez pobranie i uruchomienie **asdk installer.ps1** skrypt programu PowerShell.

> [!NOTE]
> Interfejs użytkownika Instalatora programu Azure Stack Development Kit jest skrypt open source, na podstawie usługi WCF i programu PowerShell.


1. Po uruchomieniu pomyślnie komputera hosta do obrazu CloudBuilder.vhdx, zaloguj się przy użyciu poświadczeń administratora określony, gdy użytkownik [przygotowanym komputerze-hoście development kit](asdk-prepare-host.md) ASDK instalacji. Powinna to być taka sama jak poświadczenia administratora lokalnego development kit hosta.
2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i uruchom  **&lt;litera dysku > \AzureStack_Installer\asdk-installer.ps1** skrypt programu PowerShell. Należy pamiętać, że skrypt teraz mogą znajdować się na innym dysku niż C:\ na obrazie CloudBuilder.vhdx. Kliknij pozycję **Zainstaluj**.

    ![](media/asdk-install/1.PNG) 

3. W dostawcy tożsamości **typu** listy rozwijanej wybierz pozycję **Azure albo chmura**, **Azure US Government Cloud**, **usług AD FS**, lub **Chmury platformy azure**. W obszarze **hasło administratora lokalnego** wpisz hasło administratora lokalnego (który musi być zgodna bieżące hasło administratora lokalnego skonfigurowanego) **hasło** , a następnie kliknij przycisk  **Następny**.

    ![](media/asdk-install/2.PNG) 
  
   Jeśli dostawca tożsamości subskrypcji platformy Azure, konieczne jest połączenie internetowe, pełna nazwa usługi Azure AD directory dzierżawy w formie *nazwa_domeny*. onmicrosoft.com lub usługi Azure AD należy zweryfikować domenę niestandardową nazwę i globalne poświadczenia administratora dla określonego katalogu.<br><br>Po wdrożeniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo ponownie przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.<br><br>Korzystając z usług AD FS jako dostawcy tożsamości, usługa katalogowa sygnatury domyślny jest używany. Domyślne konto do zalogowania to azurestackadmin@azurestack.local, a hasło do użycia jest dostarczana jako część instalacji.

   > [!NOTE]
   > Aby uzyskać najlepsze wyniki nawet, jeśli chcesz użyć środowiska bez połączenia usługi Azure Stack jako dostawcy tożsamości za pomocą usług AD FS jest najlepiej zainstalować ASDK po nawiązaniu połączenia z Internetem. W ten sposób można aktywować wersję ewaluacyjną systemu Windows Server 2016, które są dołączone do rozwoju Instalacja zestawu w czasie wdrażania.

4. Wybierz kartę sieciową do użycia dla zestawu SDK, a następnie kliknij przycisk **dalej**.

    ![](media/asdk-install/3.PNG)

5. Na **konfiguracji sieci** Podaj prawidłową **czas adres IP serwera** adresu. To wymagane pole zestawy serwer czasu, który będzie używany przez zestaw development kit. Ten parametr musi zostać podana jako adres IP serwera prawidłową godzinę. Nazwy serwerów nie są obsługiwane.

      > [!TIP]
      > Serwer czasu adres IP można znaleźć [ntppool.org](https://www.ntppool.org/) lub wysłać polecenie ping time.windows.com. 

    **Opcjonalnie**, może zapewnić **usługi przesyłania dalej DNS** adresu IP. Serwer DNS jest tworzona jako część wdrożenia usługi Azure Stack. Aby zezwolić komputerom wewnątrz rozwiązania do rozpoznawania nazw poza sygnatury, podaj istniejącej infrastruktury serwera DNS. Serwer DNS w sygnatury przekazuje żądania rozpoznania nieznanej nazwy do tego serwera.

    ![](media/asdk-install/4.PNG)

6. Na **Weryfikowanie właściwości karty interfejsu sieciowego** stronie zostanie wyświetlony pasek postępu. Po zakończeniu weryfikacji kliknij przycisk **dalej**.

    ![](media/asdk-install/5.PNG)

7. Na **Podsumowanie** kliknij **Wdróż** aby rozpocząć instalację ASDK na komputerze-hoście development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > W tym miejscu możesz również skopiować Instalator poleceń programu PowerShell, które będą używane do zainstalowania deweloperski. Jest to przydatne, jeśli kiedykolwiek zajdzie potrzeba [ponownie wdrożyć ASDK na komputerze-hoście przy użyciu programu PowerShell](asdk-deploy-powershell.md).

8. Jeśli przeprowadzasz wdrożenie usługi Azure AD zostanie wyświetlony monit o podanie poświadczeń konta administratora globalnego usługi Azure AD za kilka minut, po uruchomieniu Instalatora.

9. Proces wdrażania potrwa kilka godzin, podczas których komputer-host zostanie automatycznie ponownie uruchomiony jeden raz. Jeśli chcesz monitorować postęp wdrażania, zaloguj się jako azurestack\AzureStackAdmin po ponownym uruchomieniu hosta kit rozwoju. Po pomyślnym wdrożeniu Wyświetla konsoli programu PowerShell: **NALEŻY WYKONAĆ: Akcja "Wdrożenie"**. 
    > [!IMPORTANT]
    > Jeśli rejestrujesz się jako administrator lokalny po komputer jest przyłączony do domeny azurestack, nie będzie wyświetlany postęp wdrożenia. Ponownie uruchom wdrożenie, nie zamiast tego zaloguj się jako azurestack\AzureStackAdmin można sprawdzić, czy jest uruchomiona.

    ![](media/asdk-install/7.PNG)

Gratulacje, pomyślnie zainstalowanych ASDK!

Jeśli wdrożenie nie powiedzie się z jakiegoś powodu, możesz to zrobić [ponownie wdrożyć](asdk-redeploy.md) od podstaw lub użyj następujące polecenia programu PowerShell, w tym samym oknie programu PowerShell z podwyższonym poziomem uprawnień, aby ponownie uruchomić wdrożenia w ostatnim kroku pomyślnie:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Kolejne kroki
[Po wdrożeniu](asdk-post-deploy.md)
