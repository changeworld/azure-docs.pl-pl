---
title: W tym samouczku tworzenia maszyny Wirtualnej platformy Azure stosu przy użyciu szablonu | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania ASDK, aby utworzyć Maszynę wirtualną przy użyciu szablonu predfined i szablonu niestandardowego usługi GitHub.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: e772dc41ce2cb77a03b91515cae35ffc48f5dbc3
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238501"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Samouczek: tworzenie maszyny Wirtualnej przy użyciu szablonu społeczności
Jako operator stosu Azure lub użytkownika, można utworzyć maszynę Wirtualną przy użyciu [niestandardowe szablony szybkiego startu GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) zamiast wdrażanie go ręcznie z stosu Azure marketplace.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Więcej informacji na temat szablonów Szybki Start Azure stosu 
> * Utwórz maszynę Wirtualną przy użyciu szablonu niestandardowego usługi GitHub
> * Uruchom minikube i zainstalować aplikację

## <a name="learn-about-azure-stack-quickstart-templates"></a>Więcej informacji na temat szablonów Szybki Start Azure stosu
Szablony szybkiego startu usługi Azure stosu są przechowywane w [publicznego repozytorium szablony szybkiego startu AzureStack](https://github.com/Azure/AzureStack-QuickStart-Templates) w witrynie GitHub. To repozytorium zawiera szablony wdrażania usługi Azure Resource Manager, które zostały przetestowane z Microsoft Azure stosu Development Kit (ASDK). Aby ułatwić ocenę stosu Azure i korzystać ze środowiska ASDK można używać ich. 

Wraz z upływem czasu w przypadku wielu użytkowników GitHub przyczyniły się do repozytorium, co powoduje duży zbiór ponad 400 szablony wdrażania. To repozytorium jest doskonały punkt początkowy uzyskanie lepszego zrozumienia sposobów wdrażania różnego rodzaju środowisk Azure stosu. 

>[!IMPORTANT]
> Niektóre z tych szablonów są tworzone przez członków społeczności, a nie przez firmę Microsoft. Każdego szablonu ma licencji na mocy umowy licencyjnej jego właściciel, a nie przez firmę Microsoft. Firma Microsoft nie jest odpowiedzialny za te szablony i nie sprawdza ich pod kątem zabezpieczeń, zgodności czy wydajności. Społeczność szablony nie są obsługiwane w ramach usług ani programów pomocy technicznej firmy Microsoft i są udostępniane ", w jakim są" bez jakichkolwiek gwarancji.

Jeśli chcesz przekazać do usługi GitHub szablonów usługi Azure Resource Manager, należy ustawić Ciebie do [repozytorium azure-Szybki Start — szablony](https://github.com/Azure/AzureStack-QuickStart-Templates).

Aby dowiedzieć się więcej o repozytorium GitHub i sposobie przyczyniają się do niego, zobacz [plik readme dla repozytorium](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md). 


## <a name="create-a-vm-using-a-custom-github-template"></a>Utwórz maszynę Wirtualną przy użyciu szablonu niestandardowego usługi GitHub
W tym samouczku przykład [101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) stosu Azure szybkiego startu szablon zostanie użyty do wdrożenia maszyny wirtualnej systemu Ubuntu 16.04 na AzureStack systemem Minikube do zarządzania klastrem kubenetes.

Minikube to narzędzie, które można łatwo uruchomić Kubernetes lokalnie. Minikube uruchamia codziennych klaster jednowęzłowy Kubernetes wewnątrz maszyny Wirtualnej dla użytkowników, które chcą wypróbować Kubernetes lub Opracuj z nim. Obsługuje prosty, jeden węzeł klastra Kubernetes uruchomione na maszynie Wirtualnej systemu Linux. Jest to metoda najszybsze i najbardziej bezpośrednio do przodu funkcjonalnej Kubernetes klastra z systemem. Umożliwia deweloperom projektowania i testowania wdrożeń aplikacji na podstawie Kubernetes na swoich komputerach. Pod względem architektury maszyna wirtualna Minikube działa główny oraz składniki węzła agenta lokalnie:
- Węzeł główny składniki, takie jak serwera interfejsu API, harmonogram i etcd serwera są uruchamiane w ramach jednego procesu Linux o nazwie LocalKube.
- Składniki węzła agenta są uruchamiane wewnątrz docker kontenerów, dokładnie tak, jak działają w normalnym węźle agenta. Z punktu widzenia wdrożenia aplikacji nie ma żadnej różnicy, gdy aplikacja jest wdrażana na Minikube lub regularne Kubernetes klastra.

Ten szablon instalowane są następujące składniki:

- Maszyna wirtualna LTS Ubuntu 16.04
- [Docker CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Obraz maszyny Wirtualnej systemu Ubuntu (Ubuntu Server 16.04 LTS w tym przykładzie) musi już zostały dodane do stosu Azure marketplace przed rozpoczęciem tych kroków.

1.  Kliknij przycisk **+ nowy** > **niestandardowy** > **wdrażania szablonu**.

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. Kliknij przycisk **Edytuj szablon**.

   ![](media/azure-stack-create-vm-template/2.PNG) 

3.  Kliknij przycisk **szablon szybkiego startu**.

       ![](media/azure-stack-create-vm-template/3.PNG)

4. Wybierz **101-vm-linux-minikube** z dostępnych szablonów przy użyciu **wybierz szablon** listy rozwijanej, a następnie kliknij przycisk **OK**.  

   ![](media/azure-stack-create-vm-template/4.PNG)

5. Jeśli chcesz wprowadzić zmiany w szablonie JSON można to zrobić, jeśli nie, lub po zakończeniu kliknij przycisk **zapisać** aby zamknąć okno dialogowe Edytowanie szablonu.

   ![](media/azure-stack-create-vm-template/5.PNG) 

6.  Polecenie **parametry**Wypełnij lub zmodyfikuj pola dostępne w razie potrzeby, a następnie kliknij **OK**. Wybierz subskrypcję do użycia, lub wybierz istniejącą nazwę grupy zasobów, a następnie kliknij polecenie **Utwórz** do inicjowania wdrożenia szablonu.

       ![](media/azure-stack-create-vm-template/6.PNG)

7. Wybierz subskrypcję do użycia, lub wybierz istniejącą nazwę grupy zasobów, a następnie kliknij polecenie **Utwórz** do inicjowania wdrożenia szablonu.

   ![](media/azure-stack-create-vm-template/7.PNG)

8. Wdrażanie grupy zasobów może zająć kilka minut utworzyć niestandardowe na podstawie szablonu maszyny Wirtualnej. Można monitorować stan instalacji za pomocą powiadomień i przy użyciu właściwości grupy zasobów. 

   ![](media/azure-stack-create-vm-template/8.PNG)

   >[!NOTE]
   > Wirtualna zostanie uruchomiona po zakończeniu wdrożenia. 

## <a name="start-minikube-and-install-an-application"></a>Uruchom minikube i zainstalować aplikację
Teraz, że pomyślnie utworzono maszynę Wirtualną systemu Linux, należy zalogować się do uruchomienia minikube i zainstalowania aplikacji. 

1. Po zakończeniu wdrożenia, kliknij przycisk **Connect** Aby wyświetlić adres publiczny adres IP, który będzie używany do nawiązania połączenia maszyny Wirtualnej systemu Linux. 

   ![](media/azure-stack-create-vm-template/9.PNG)

2. W wierszu polecenia z podwyższonym poziomem uprawnień, uruchom **mstsc.exe** aby otworzyć usługę Podłączanie pulpitu zdalnego i połączyć maszyny Wirtualnej systemu Linux publiczny adres IP odnalezione w poprzednim kroku. Po wyświetleniu monitu zaloguj się do xRDP, należy użyć poświadczenia określone podczas tworzenia maszyny Wirtualnej.

   ![](media/azure-stack-create-vm-template/10.PNG)

3. Otwórz Terminal emulatora, a następnie wprowadź następujące polecenia, aby uruchomić minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/azure-stack-create-vm-template/11.PNG)

4. Otwórz przeglądarkę sieci web, a następnie odwiedź adres kubernetes pulpitu nawigacyjnego. Gratulacje, masz teraz pełni pracy kubernetes instalację przy użyciu minikube!

   ![](media/azure-stack-create-vm-template/12.PNG)

5. Jeśli chcesz wdrożyć przykładowej aplikacji, odwiedź stronę kubernetes w oficjalnej dokumentacji, Pomiń w sekcji "Tworzenie klastra Minikube", ponieważ został już utworzony powyżej. Wystarczy przejść do sekcji "Tworzenie aplikacji Node.js" w https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Więcej informacji na temat szablonów Szybki Start Azure stosu 
> * Utwórz maszynę Wirtualną przy użyciu szablonu niestandardowego usługi GitHub
> * Uruchom minikube i zainstalować aplikację

