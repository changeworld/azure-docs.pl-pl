---
title: Przesyłanie zadań HPC Pack klastra na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ustawić na komputerze lokalnym, aby przesyłać zadania do klastra pakietu HPC Pack na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: ce8e2457c1d575e890174de3b9cf7faf6e16a7cb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258820"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Przesyłanie zadań HPC z lokalnego komputera do klastra pakietu HPC Pack wdrożonego na platformie Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Konfigurowanie lokalnych komputera klienckiego do przesyłania zadań do [pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klastra na platformie Azure. W tym artykule przedstawiono sposób konfigurowania komputera lokalnego przy użyciu narzędzi klienckich, aby przesłać zadanie przy użyciu protokołu HTTPS w klastrze na platformie Azure. W ten sposób kilku użytkowników klastra można przesłać zadania do klastra pakietu HPC Pack oparte na chmurze, ale bez bezpośredniego połączenia z węzłem głównym maszyny Wirtualnej lub uzyskiwania dostępu do subskrypcji platformy Azure.

![Przesyłanie zadania do klastra na platformie Azure][jobsubmit]

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzeł główny HPC Pack wdrożony w Maszynie wirtualnej platformy Azure** — zalecane jest użycie zautomatyzowanych narzędzi takich jak [szablonu szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) wdrażanie węzła głównego i klastra. Konieczne jest nazwa DNS węzła głównego i poświadczenia administratora klastra wykonanie czynności opisanych w tym artykule.
* **Komputer kliencki** — wymagają Windows lub Windows Server komputera klienckiego, który można uruchomić narzędzia klienta pakietu HPC Pack (zobacz [wymagania systemowe](https://technet.microsoft.com/library/dn535781.aspx)). Jeśli chcesz użyć interfejsu API REST lub portalu sieci web pakietu HPC Pack do przesyłania zadań, można użyć dowolnego komputera klienckiego wybranych przez użytkownika.
* **Nośnik instalacyjny pakietu HPC Pack** — Aby zainstalować narzędzi pakietu HPC Pack klienta, pakiet instalacyjny bezpłatne do najnowszej wersji pakietu HPC Pack jest niedostępna z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Upewnij się, czy możesz pobrać tę samą wersję pakietu HPC Pack jest zainstalowany w węźle głównym maszyny Wirtualnej.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Krok 1: Instalowanie i konfigurowanie składników sieci web w węźle głównym
Aby włączyć interfejs REST przesyłać zadania do klastra za pośrednictwem protokołu HTTPS, upewnij się, że składniki sieci web pakietu HPC Pack są skonfigurowane na węzeł główny HPC Pack. Jeśli nie są już zainstalowane, należy najpierw zainstalować składniki sieci web, uruchamiając plik instalacyjny HpcWebComponents.msi. Następnie należy skonfigurować składniki, uruchamiając skrypt środowiska PowerShell klastra HPC **HPCWebComponents.ps1 zestaw**.

Szczegółowe procedury zobacz [zainstalować składniki sieci Web firmy Microsoft HPC Pack](https://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Niektóre szablony szybkiego startu platformy Azure, w przypadku klastrów HPC Pack Instalowanie i konfigurowanie składników sieci web w automatycznie.
> 
> 

**Aby zainstalować składniki sieci web**

1. Łączenie z węzłem głównym maszyny Wirtualnej, przy użyciu poświadczeń administratora klastra.
1. Z folderu instalacji pakietu HPC należy uruchomić HpcWebComponents.msi w węźle głównym.
1. Postępuj zgodnie z instrukcjami w kreatorze, aby zainstalować składniki sieci web

**Aby skonfigurować składniki sieci web**

1. W węźle głównym uruchamiania środowiska PowerShell klastra HPC, jako administrator.
1. Zmień katalog na lokalizację skryptu konfiguracji, wpisz następujące polecenie:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
1. Aby skonfigurować interfejs REST i uruchomić usługę sieci Web HPC, wpisz następujące polecenie:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
1. Gdy zostanie wyświetlony monit o wybranie certyfikatu, należy wybrać certyfikat, który odnosi się do publicznej nazwy DNS węzła głównego. Na przykład, jeśli wdrożono węzła głównego maszyny Wirtualnej przy użyciu klasycznego modelu wdrażania, nazwa certyfikatu wygląda CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Jeśli używasz modelu wdrażania usługi Resource Manager, nazwa certyfikatu wygląda CN =&lt;*HeadNodeDnsName*&gt;.&lt; *region*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Możesz wybrać ten certyfikat później podczas przesyłania zadań do węzła głównego z komputera lokalnego. Brak zaznaczenia lub skonfigurować certyfikat, który odpowiada nazwie komputera węzła głównego w domenie usługi Active Directory (na przykład CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
1. Aby skonfigurować portalu sieci web do przesłania zadania, wpisz następujące polecenie:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
1. Po ukończeniu działania skryptu, należy zatrzymać i ponownie uruchomić usługa harmonogramu zadań HPC, wpisując następujące polecenia:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Krok 2: Zainstaluj narzędzia klienta pakietu HPC Pack na komputerze lokalnym
Jeśli chcesz zainstalować narzędzia klienta pakietu HPC Pack na komputerze, należy pobrać plików instalacyjnych pakietu HPC Pack (Instalacja pełna) z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Po rozpoczęciu instalacji wybierz opcję instalacji **narzędzi klienta pakietu HPC Pack**.

Aby użyć narzędzia klienckie pakietu HPC Pack do przesyłania zadań do węzła głównego maszyny Wirtualnej, również należy wyeksportować certyfikat z węzłem głównym i zainstaluj go na komputerze klienckim. Certyfikat musi być w. CER format.

**Aby wyeksportować certyfikat z węzła głównego**

1. W węźle głównym Dodaj przystawkę Certyfikaty do konsoli Microsoft Management Console dla konta komputera lokalnego. Aby uzyskać instrukcje dotyczące dodawania przystawki, zobacz [Dodawanie przystawki Certyfikaty do programu MMC](https://technet.microsoft.com/library/cc754431.aspx).
1. W drzewie konsoli rozwiń **certyfikaty — komputer lokalny** > **osobistych**, a następnie kliknij przycisk **certyfikaty**.
1. Znajdź certyfikat, który został skonfigurowany dla składników sieci web pakietu HPC Pack w [krok 1: Instalowanie i konfigurowanie składników sieci web w węźle głównym](#step-1-install-and-configure-the-web-components-on-the-head-node) (na przykład CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
1. Kliknij prawym przyciskiem myszy certyfikat, a następnie kliknij przycisk **wszystkie zadania** > **wyeksportować**.
1. W Kreatorze eksportu certyfikatów kliknij **dalej**i upewnij się, że **nie eksportuj klucza prywatnego** jest zaznaczone.
1. Wykonaj pozostałe kroki kreatora Aby wyeksportować certyfikat w certyfikat x.509 szyfrowany binarnie algorytmem DER (. Format CER).

**Aby zaimportować certyfikat na komputerze klienckim**

1. Skopiuj certyfikat, który został wyeksportowany z węzła głównego do folderu na komputerze klienckim.
1. Na komputerze klienckim, aby uruchomić certmgr.msc.
1. Rozwiń węzeł w Menedżerze certyfikatów **Certyfikaty — bieżący użytkownik** > **zaufane główne urzędy certyfikacji**, kliknij prawym przyciskiem myszy **certyfikaty**, a następnie Kliknij przycisk **wszystkie zadania** > **importu**.
1. Kreatora importu certyfikatów kliknij **dalej** i postępuj zgodnie z instrukcjami, aby zaimportować certyfikat, który został wyeksportowany z węzłem głównym w magazynie zaufanych głównych urzędów certyfikacji.

> [!TIP]
> Może pojawić się ostrzeżenie, ponieważ urzędu certyfikacji na węzeł główny nie jest rozpoznawane przez komputer kliencki. Do celów testowych możesz zignorować to ostrzeżenie i wykonać importu certyfikatów.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Krok 3: Przebieg testu zadań w klastrze
Aby sprawdzić konfigurację, spróbuj uruchomionych zadań w klastrze na platformie Azure z komputera lokalnego. Na przykład można użyć narzędzia z graficznym interfejsem użytkownika HPC Pack lub wiersza poleceń do przesyłania zadań do klastra. Portal sieci web umożliwia również przesyłać zadania.

**Aby uruchamiać polecenia przesyłania zadań na komputerze klienckim**

1. Na komputerze klienckim, w którym są zainstalowane narzędzia klienta pakietu HPC Pack należy uruchomić wiersz polecenia.
1. Wpisz polecenie próbki. Na przykład aby wyświetlić listę wszystkich zadań w klastrze, wpisz polecenie podobne do jednej z poniższych pozycji w zależności od tego, pełną nazwę DNS węzła głównego:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    lub
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Użyj pełnej nazwy DNS węzła głównego, a nie adresu IP w adresie URL harmonogramu. Jeśli określisz adres IP, pojawia się błąd podobny do "certyfikat serwera musi mieć prawidłowy łańcuch zaufania lub umieszczone w magazynie zaufany główny urząd certyfikacji".
   > 
   > 
1. Po wyświetleniu monitu wpisz nazwę użytkownika (w postaci &lt;nazwa_domeny&gt;\\&lt;UserName&gt;) i hasło administratora klastra HPC lub innemu użytkownikowi klastra, który został skonfigurowany. Użytkownik chce przechowywać poświadczenia lokalnie dla kolejnych operacjach zadania.
   
    Zostanie wyświetlona lista zadań.

**Aby użyć Menedżer zadań klastra HPC na komputerze klienckim**

1. Jeśli nie została wcześniej przechowywane poświadczenia domeny użytkownika klastra podczas przesyłania zadania, można dodać poświadczeń w Menedżerze poświadczeń.
   
    a. W Panelu sterowania na komputerze klienckim należy uruchomić Menedżera poświadczeń.
   
    b. Kliknij przycisk **poświadczenia Windows** > **Dodaj poświadczenie ogólny**.
   
    c. Określ adres internetowy (na przykład https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler lub https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;.cloudapp.azure.com/HpcScheduler) i nazwę użytkownika (&lt;nazwa_domeny&gt;\\&lt;UserName&gt;) i hasło administratora klastra lub innego Użytkownik klastra, który został skonfigurowany.
1. Na komputerze klienckim, aby uruchomić Menedżer zadań klastra HPC.
1. W **wybierz węzeł główny** okno dialogowe, wpisz adres URL z węzłem głównym na platformie Azure (na przykład https://&lt;HeadNodeDnsName&gt;. cloudapp.net lub https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;. cloudapp.azure.com).
   
    Menedżer zadań klastra HPC otwiera i wyświetla listę zadań w węźle głównym.

**Aby korzystać z portalu sieci web działające w węźle głównym**

1. Uruchom przeglądarkę sieci web na komputerze klienckim, a następnie wprowadź jedną z następujących adresów, w zależności od pełną nazwę DNS węzła głównego:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    lub
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
1. W oknie dialogowym Zabezpieczenia wpisz poświadczenia domeny administratora klastra HPC. (Możesz również dodać innym użytkownikom klastra w różne role. Zobacz [Zarządzanie użytkownikami klastra](https://technet.microsoft.com/library/ff919335.aspx).)
   
    W portalu sieci web zostanie otwarty widok listy zadań.
1. Aby przesłać zadanie próbki, która zwraca ciąg "Hello World" z klastra, kliknij przycisk **nowe zadanie** w obszarze nawigacji po lewej stronie.
1. Na **nowe zadanie** w obszarze **ze stron przesyłania**, kliknij przycisk **HelloWorld**. Zostanie wyświetlona strona przesyłania zadania.
1. Kliknij przycisk **przesłać**. Po wyświetleniu monitu podaj poświadczenia domeny administratora klastra HPC. Zadanie zostanie przesłany i identyfikator zadania jest wyświetlany w **Moje zadania** strony.
1. Aby wyświetlić wyniki zadania przesłane, kliknij przycisk Identyfikator zadania, a następnie kliknij **zadania widoku** do wyświetlania danych wyjściowych polecenia (w obszarze **dane wyjściowe**).

## <a name="next-steps"></a>Kolejne kroki
* Możesz również przesłać zadań w klastrze platformy Azure za pomocą [interfejsu API REST pakietu HPC](https://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Jeśli chcesz przesłać zadań klastra z klientów systemu Linux, zobacz przykład języka Python w [HPC Pack 2012 R2 SDK czy kod przykładowy](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
