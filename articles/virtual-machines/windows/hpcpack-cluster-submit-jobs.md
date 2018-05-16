---
title: Przesyłanie zadań HPC Pack klastra na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować na komputerze lokalnym do przesyłania zadań do klastra HPC Pack na platformie Azure
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
ms.openlocfilehash: 025ff3dea365ab75af55f107da1fb7331861eb06
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Przesyłanie zadań HPC z lokalnego komputera do klastra pakietu HPC Pack wdrożonego na platformie Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Konfigurowanie komputera klienckiego lokalnymi umożliwiają przesyłanie zadań do [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) klastra na platformie Azure. W tym artykule przedstawiono sposób konfigurowania lokalnego komputera z narzędziami klienckimi można przesłać zadania przy użyciu protokołu HTTPS do klastra w systemie Azure. W ten sposób wielu użytkowników klastra można przesłać zadania do klastra HPC Pack oparte na chmurze, ale bez połączenie bezpośrednio z węzłem głównym maszyny Wirtualnej lub uzyskiwanie dostępu do subskrypcji platformy Azure.

![Przesyłanie zadań do klastra w systemie Azure][jobsubmit]

## <a name="prerequisites"></a>Wymagania wstępne
* **Węzłem głównym HPC Pack wdrożony w maszynie Wirtualnej platformy Azure** -zalecane jest użycie automatycznych narzędzi takich jak [szablonie Szybki Start Azure](https://azure.microsoft.com/documentation/templates/) wdrażanie węzła głównego i klastra. Należy nazwy DNS węzła głównego i poświadczenia administratora klastra, aby wykonać kroki opisane w tym artykule.
* **Komputer kliencki** -wymagają systemu Windows lub Windows Server komputera klienckiego, który można uruchomić narzędzia klienta HPC Pack (zobacz [wymagania systemowe](https://technet.microsoft.com/library/dn535781.aspx)). Jeśli chcesz użyć portalu internetowego HPC Pack lub interfejsu API REST do przesyłania zadań, można użyć dowolnego komputera klienckiego wybranych przez użytkownika.
* **Nośnik instalacyjny HPC Pack** — Aby zainstalować HPC Pack narzędzi klienta, pakiet instalacyjny wolnego do najnowszej wersji HPC Pack jest niedostępna z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Upewnij się, pobrania tej samej wersji pakietu HPC, który jest zainstalowany w węźle głównym maszyny Wirtualnej.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Krok 1: Instalowanie i konfigurowanie składników sieci web na węzła głównego
Aby włączyć interfejs REST umożliwiają przesyłanie zadań do klastra przy użyciu protokołu HTTPS, upewnij się, że składniki sieci web HPC Pack są skonfigurowane w węźle głównym HPC Pack. Jeśli nie są już zainstalowane, należy najpierw zainstalować składniki sieci web, uruchamiając plik instalacyjny HpcWebComponents.msi. Następnie należy skonfigurować składniki przez uruchomienie skryptu środowiska PowerShell klastra HPC **HPCWebComponents.ps1 zestawu**.

Aby uzyskać szczegółowe procedury, zobacz [zainstalować składniki sieci Web programu Microsoft HPC Pack](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Niektóre szablony Szybki Start Azure w przypadku klastrów HPC Pack zainstaluje i skonfiguruje składniki sieci web automatycznie.
> 
> 

**Aby zainstalować składniki sieci web**

1. Połączenie z węzłem głównym maszyny Wirtualnej przy użyciu poświadczeń administratora klastra.
2. Uruchom z folderu instalacji pakietu HPC, HpcWebComponents.msi w węźle głównym.
3. Postępuj zgodnie z instrukcjami kreatora, aby zainstalować składniki sieci web

**Aby skonfigurować składniki sieci web**

1. W węźle głównym Uruchom program HPC PowerShell jako administrator.
2. Aby zmienić katalog do lokalizacji pliku skryptu konfiguracji, wpisz następujące polecenie:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Aby skonfigurować interfejs REST i uruchomić usługę sieci Web HPC, wpisz następujące polecenie:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Gdy zostanie wyświetlony monit o wybranie certyfikatu, należy wybrać certyfikat, który odpowiada publicznej nazwy DNS węzła głównego. Na przykład w przypadku wdrożenia maszyny Wirtualnej przy użyciu klasycznego modelu wdrażania węzłem głównym, nazwa certyfikatu wygląda CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Jeśli używasz modelu wdrażania usługi Resource Manager, nazwa certyfikatu wygląda CN =&lt;*HeadNodeDnsName*&gt;.&lt; *region*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Należy wybrać ten certyfikat później podczas przesyłania zadań do węzła głównego z komputera lokalnego. Brak zaznaczenia lub skonfigurować certyfikat, który odpowiada nazwie komputera węzła głównego w domenie usługi Active Directory (na przykład, CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Aby skonfigurować dla przesyłanie zadań do portalu sieci web, wpisz następujące polecenie:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Po ukończeniu działania skryptu, zatrzymać i ponownie uruchomić usługa harmonogramu zadań HPC, wpisując następujące polecenia:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Krok 2: Zainstaluj narzędzia klienta HPC Pack na komputerze lokalnym
Jeśli chcesz zainstalować narzędzia klienta HPC Pack na komputerze, należy pobrać pliki instalacji HPC Pack (Instalacja pełna) z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Po rozpoczęciu instalacji wybierz opcję instalacji dla **narzędzi klienta HPC Pack**.

Aby użyć narzędzia klienta HPC Pack umożliwiają przesyłanie zadań do węzła głównego maszyny Wirtualnej, należy również eksportowania certyfikatu z węzła głównego i zainstaluj go na komputerze klienckim. Certyfikat musi być w. CER format.

**Aby wyeksportować certyfikat z węzła głównego**

1. W węźle głównym Dodaj przystawkę Certyfikaty do programu Microsoft Management Console dla konta komputera lokalnego. Aby uzyskać instrukcje dotyczące dodawania przystawki, zobacz [Dodawanie przystawki Certyfikaty do programu MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. W drzewie konsoli rozwiń węzeł **certyfikaty — komputer lokalny** > **osobistych**, a następnie kliknij przycisk **certyfikaty**.
3. Zlokalizować certyfikatu, który skonfigurowany dla składników sieci web HPC Pack w [krok 1: Instalowanie i konfigurowanie składników sieci web w węźle głównym](#step-1:-install-and-configure-the-web-components-on-the-head-node) (na przykład, CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
4. Kliknij prawym przyciskiem myszy certyfikat, a następnie kliknij przycisk **wszystkie zadania** > **wyeksportować**.
5. W Kreatorze eksportu certyfikatów kliknij **dalej**i upewnij się, że **nie eksportuj klucza prywatnego** jest zaznaczone.
6. Wykonaj pozostałe kroki kreatora, aby wyeksportować certyfikat w certyfikat x.509 szyfrowany binarnie algorytmem DER (. Format CER).

**Aby zaimportować certyfikat na komputerze klienckim**

1. Skopiuj certyfikat, który został wyeksportowany z węzła głównego do folderu na komputerze klienckim.
2. Uruchom certmgr.msc na komputerze klienckim.
3. Rozwiń węzeł w Menedżerze certyfikatów **Certyfikaty — bieżący użytkownik** > **zaufane główne urzędy certyfikacji**, kliknij prawym przyciskiem myszy **certyfikaty**, a następnie Kliknij przycisk **wszystkie zadania** > **importu**.
4. Kreatora importu certyfikatów kliknij **dalej** i wykonaj kroki, aby zaimportować certyfikat, który został wyeksportowany z węzłem głównym w magazynie zaufanych głównych urzędów certyfikacji.

> [!TIP]
> Może pojawić się ostrzeżenie, ponieważ urzędu certyfikacji w węźle głównym nie jest rozpoznawane przez komputer kliencki. Do celów testowych, możesz zignorować to ostrzeżenie i ukończenie importu certyfikatów.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Krok 3: Uruchamianie testu zadań w klastrze
Aby sprawdzić konfigurację, spróbuj uruchomionych zadań w klastrze na platformie Azure z komputera lokalnego. Na przykład można użyć narzędzia HPC Pack GUI lub poleceń wiersza polecenia do przesyłania zadań do klastra. Portal sieci web służy także do przesyłania zadań.

**Aby uruchomić polecenia przesyłania zadań na komputerze klienckim**

1. Na komputerze klienckim, w którym są zainstalowane narzędzia klienta HPC Pack należy uruchomić wiersz polecenia.
2. Wpisz polecenie przykładowe. Na przykład aby wyświetlić listę wszystkich zadań w klastrze, wpisz polecenie podobne do jednej z następujących czynności, w zależności od węzła głównego pełną nazwę DNS:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    lub
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Pełna nazwa DNS węzła głównego, a nie adres IP, należy użyć w adresie URL harmonogramu. Jeśli określony adres IP, pojawia się błąd podobny do "certyfikat serwera musi mieć prawidłowy łańcuch zaufania lub umieszczone w magazynie zaufanych certyfikatów głównych."
   > 
   > 
3. Po wyświetleniu monitu wpisz nazwę użytkownika (w postaci &lt;DomainName&gt;\\&lt;UserName&gt;) i hasło administratora klastrów HPC lub innego użytkownika klastra skonfigurowanego. Można zapisać poświadczenia lokalnie dla kolejnych operacjach zadania.
   
    Zostanie wyświetlona lista zadań.

**Na komputerze klienckim za pomocą Menedżera zadań HPC**

1. Jeśli wcześniej nie przechowują poświadczeń domeny użytkownika, klastra, podczas przesyłania zadania, można dodać poświadczeń w Menedżerze poświadczeń.
   
    a. W Panelu sterowania na komputerze klienckim otwórz Menedżera poświadczeń.
   
    b. Kliknij przycisk **poświadczeń systemu Windows** > **Dodaj poświadczenie ogólnego**.
   
    c. Określ adres internetowy (na przykład https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler lub https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;.cloudapp.azure.com/HpcScheduler) oraz nazwy użytkownika (&lt;DomainName&gt;\\&lt;UserName&gt;) i hasło administratora klastra lub innego użytkownika klastra, który został skonfigurowany.
2. Na komputerze klienckim, aby uruchomić Menedżer zadań klastra HPC.
3. W **wybierz węzeł Head** okna dialogowego, wpisz adres URL węzła głównego na platformie Azure (na przykład https://&lt;HeadNodeDnsName&gt;. cloudapp.net lub https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;. cloudapp.azure.com).
   
    Menedżer zadań klastra HPC otwiera i zawiera listę zadań z węzła głównego.

**Aby korzystać z portalu sieci web uruchomiona w węźle głównym**

1. Uruchom przeglądarkę sieci web na komputerze klienckim i wprowadź jedno z następujących adresów, w zależności od węzła głównego pełną nazwę DNS:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    lub
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. W oknie dialogowym Zabezpieczenia wpisz poświadczenia domeny administratora klastra HPC. (Można również dodać innych klastra użytkowników w różnych ról. Zobacz [Zarządzanie użytkownikami klastra](https://technet.microsoft.com/library/ff919335.aspx).)
   
    Portal sieci web zostanie otwarty widok listy zadań.
3. Aby przesłać zadanie próbki, która zwraca ciąg "Hello World" z klastra, kliknij przycisk **nowe zadanie** w obszarze nawigacji po lewej stronie.
4. Na **nowe zadanie** w obszarze **ze stron przesyłania**, kliknij przycisk **HelloWorld**. Zostanie wyświetlona strona przesyłania zadania.
5. Kliknij przycisk **przesłać**. Po wyświetleniu monitu podaj poświadczenia domeny administratora klastra HPC. Zadanie, a identyfikator zadania są wyświetlane na **Moje zadania** strony.
6. Aby wyświetlić wyniki zadania, który zostanie przesłany, kliknij Identyfikatora zadania, a następnie kliknij **zadania widoku** do wyświetlania danych wyjściowych polecenia (w obszarze **dane wyjściowe**).

## <a name="next-steps"></a>Kolejne kroki
* Istnieje także możliwość przesyłania zadań do klastra platformy Azure z [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Jeśli chcesz przesłać zadań klastra z klientów systemu Linux, zobacz próbki Python w [HPC Pack 2012 R2 SDK oraz przykładowy kod](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
