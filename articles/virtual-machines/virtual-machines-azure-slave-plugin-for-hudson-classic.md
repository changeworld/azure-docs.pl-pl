---
title: Jak używać wtyczki podrzędnej platformy Azure z funkcją ciągłej integracji Hudson | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać wtyczki podrzędnej platformy Azure z funkcją ciągłej integracji Hudson.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: ef24e356c9ac8424fc519a3b16af5d37a20e706f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444218"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Jak używać wtyczki podrzędnej platformy Azure z funkcją ciągłej integracji Hudson
Podrzędnej wtyczki platformy Azure dla rozwiązania Hudson umożliwia aprowizowanie węzłów podrzędnych na platformie Azure, w przypadku uruchamiania rozproszonych kompilacji.

## <a name="install-the-azure-slave-plug-in"></a>Instalowanie wtyczki podrzędnej platformy Azure
1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **Zarządzanie Hudson**.
2. W **Zarządzanie Hudson** kliknij na **Zarządzaj wtyczkami**.
3. Kliknij przycisk **dostępne** kartę.
4. Kliknij przycisk **wyszukiwania** i typ **Azure** ograniczenie listy do odpowiednich dodatków plug-in.
   
    Jeśli postanowisz przewijać listę dostępnych wtyczek zawiera element podrzędny platformy Azure wtyczki w obszarze **klastra zarządzania i dystrybucji kompilacji** sekcji **innych** kartę.
5. Zaznacz pole wyboru **wtyczka podrzędna Azure**.
6. Kliknij pozycję **Zainstaluj**.
7. Uruchom ponownie Hudson.

Teraz, gdy ta wtyczka jest zainstalowana, kolejne kroki będzie, aby skonfigurować wtyczkę do profilu subskrypcji platformy Azure i utworzyć szablon, który będzie używany podczas tworzenia maszyny Wirtualnej dla węzła podrzędnego.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurowanie wtyczki podrzędnej platformy Azure przy użyciu profilu subskrypcji
Profil subskrypcji również określane jako ustawienia, publikowania jest plik XML, który zawiera bezpiecznych poświadczeń i niektóre dodatkowe informacje, które będą potrzebne do pracy z platformą Azure w środowisku programistycznym. Aby skonfigurować wtyczki podrzędnej platformy Azure, potrzebne są:

* Identyfikator subskrypcji
* Certyfikat zarządzania do subskrypcji

Są one dostępne w Twojej [Profil subskrypcji]. Poniżej przedstawiono przykład profilu subskrypcji.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Po utworzeniu profilu subskrypcji, wykonaj następujące kroki w celu skonfigurowania wtyczki podrzędnej platformy Azure.

1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **Zarządzanie Hudson**.
2. Kliknij przycisk **skonfigurować System**.
3. Przewiń w dół strony Aby znaleźć **chmury** sekcji.
4. Kliknij przycisk **Dodaj nową chmurę > Microsoft Azure**.
   
    ![Dodaj nową chmurę][add new cloud]
   
    Spowoduje to wyświetlenie pola wymagających wprowadź szczegóły subskrypcji.
   
    ![Konfigurowanie profilu][configure profile]
5. Skopiuj subskrypcji identyfikator i certyfikat zarządzania z Twojej subskrypcji i wklej je w odpowiednich polach.
   
    Podczas kopiowania subskrypcji identyfikator i certyfikat zarządzania, **nie** z uwzględnieniem cudzysłowów, które należy umieścić wartości.
6. Kliknij pozycję **konfiguracji Sprawdź, czy**.
7. Kiedy konfiguracja nie zostanie pomyślnie zweryfikowana, kliknij przycisk **Zapisz**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Konfigurowanie szablonu maszyny wirtualnej dla podrzędnego Azure wtyczki
Szablon maszyny wirtualnej definiuje parametry, których wtyczka zostaną użyte do utworzenia węzła podrzędnego na platformie Azure. W poniższych krokach będziemy utworzona szablonu dla maszyny Wirtualnej systemu Ubuntu.

1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **Zarządzanie Hudson**.
2. Kliknij pozycję **skonfigurować System**.
3. Przewiń w dół strony Aby znaleźć **chmury** sekcji.
4. W ramach **chmury** sekcji, Znajdź **Dodaj szablon maszyny wirtualnej platformy Azure** i kliknij przycisk **Dodaj** przycisku.
   
    ![Dodaj szablon maszyny wirtualnej][add vm template]
5. Określ nazwę usługi w chmurze w **nazwa** pola. Jeśli nazwa, które określisz odnosi się do istniejącej usługi w chmurze, maszyny Wirtualnej zostaną zainicjowane w tej usłudze. W przeciwnym razie platforma Azure utworzy nową.
6. W **opis** wprowadź tekst, który opisuje szablon tworzysz. Te informacje jest tylko na potrzeby dokumentacji i nie jest używany podczas aprowizacji maszyny Wirtualnej.
7. W **etykiety** wprowadź **linux**. Ta etykieta jest używany do identyfikowania szablon, który tworzysz i są następnie używane podczas tworzenia zadania Hudson k odkazu szablonu.
8. Wybierz region, w którym zostanie utworzona maszyna wirtualna.
9. Wybierz odpowiedni rozmiar maszyny Wirtualnej.
10. Określ konto magazynu, w którym zostanie utworzona maszyna wirtualna. Upewnij się, że jest w tym samym regionie co usługa w chmurze, który ma być używany. Jeśli chcesz, aby nowego magazynu, które ma zostać utworzony, to pole może pozostać puste.
11. Czas przechowywania określa liczbę minut, zanim Hudson usuwa w przypadku bezczynności. Pozostaw to domyślna wartość 60.
12. W **użycia**, wybierz odpowiedniego warunku, jeśli będzie używany ten węzeł podrzędny. Teraz wybierz **korzystanie z tego węzła możliwie**.
    
     W tym momencie formularz powinien wyglądać nieco mniej więcej tak:
    
     ![Konfiguracja szablonu][template config]
13. W **rodziny obrazu lub identyfikatora** należy określić, jakie obrazu systemu zostanie zainstalowany na maszynie Wirtualnej. Możesz wybrać z listy rodzin obrazu lub określ niestandardowy obraz.
    
     Jeśli chcesz wybrać z listy rodzin obrazu, wprowadź pierwszy znak (z uwzględnieniem wielkości liter) nazwę rodziny obrazu. Na przykład wpisanie **U** spowoduje to wyświetlenie listy rodzin Ubuntu Server. Po wybraniu z listy Jenkins użyje najnowszej wersji tego obrazu systemu z tej rodziny, który po aprowizacji maszyny Wirtualnej.
    
     ![Listy rodziny systemów operacyjnych][OS family list]
    
     Jeśli masz niestandardowy obraz, którego chcesz użyć, wprowadź nazwę tego obrazu niestandardowego. Nazwy obrazów niestandardowych nie są wyświetlane na liście, więc trzeba upewnić się, że jej nazwa jest wpisana poprawnie.    
    
     W tym samouczku wpisz **U** do wyświetlania listy obrazów systemu Ubuntu i wybierz **Ubuntu Server 14.04 LTS**.
14. Aby uzyskać **metoda uruchomienia**, wybierz opcję **SSH**.
15. Skopiuj poniższy skrypt i Wklej w **skryptu inicjowania** pola.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     **Skryptu inicjowania** zostaną wykonane po utworzeniu maszyny Wirtualnej. W tym przykładzie skrypt instaluje Java, programu git i ant.
16. W **Username** i **hasło** wprowadź preferowany wartości dla konta administratora, który zostanie utworzony na maszynie Wirtualnej.
17. Kliknij pozycję **Sprawdź szablon** do sprawdzenia, jeśli zostanie określone parametry są prawidłowe.
18. Kliknij przycisk **Zapisz**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Tworzenie zadania Hudson, które jest uruchamiane na węzeł podrzędny na platformie Azure
W tej sekcji zostanie utworzona zadań Hudson, która zostanie uruchomiona na węzeł podrzędny na platformie Azure.

1. Na pulpicie nawigacyjnym rozwiązania Hudson kliknij **nowe zadanie**.
2. Wprowadź nazwę zadania, które tworzysz.
3. Typ zadania **kompilacji zadanie oprogramowania stylu**.
4. Kliknij przycisk **OK**.
5. Na stronie konfiguracji zadania wybierz **ograniczanie, gdzie można uruchomić tego projektu**.
6. Wybierz **węzła i etykiety menu** i wybierz **linux** (określonej etykiety podczas tworzenia szablonu maszyny wirtualnej w poprzedniej sekcji).
7. W **kompilacji** kliknij **Dodaj krok kompilacji** i wybierz **wykonaj powłokę**.
8. Edytuj następujące skrypt, zastępując **{Twoja nazwa konta usługi github}**, **{Nazwa projektu}**, i **{Twój katalog projektu}** odpowiednie wartości i Wklej edytowany skrypt w obszarze tekst, który pojawia się.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Kliknij przycisk **Zapisz**.
10. Na pulpicie nawigacyjnym rozwiązania Hudson znaleźć zadania został utworzony i kliknij pozycję **zaplanować kompilacji** ikony.

Hudson zostanie następnie utworzyć węzeł podrzędny przy użyciu szablonu utworzonego w poprzedniej sekcji i wykonywanie skryptu, który określono w kroku kompilacji dla tego zadania.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o używaniu platformy Azure z językiem Java, zobacz [Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Profil subskrypcji]: https://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

