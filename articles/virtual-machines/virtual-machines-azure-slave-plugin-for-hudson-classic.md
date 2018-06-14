---
title: Jak używać wtyczki podrzędnej Azure z Hudson ciągłej integracji | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania wtyczki podrzędnej Azure z Hudson ciągłej integracji.
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
ms.openlocfilehash: c11b59f8ea432075b147a391de4b7bd3331e639e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2018
ms.locfileid: "27704804"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Jak używać wtyczki podrzędnej Azure z Hudson ciągłej integracji
Wtyczki dla Hudson podrzędnej Azure umożliwia udostępnianie węzłów podrzędnych na platformie Azure, podczas uruchamiania rozproszonych kompilacji.

## <a name="install-the-azure-slave-plug-in"></a>Zainstaluj wtyczkę Azure podrzędnej
1. Na pulpicie nawigacyjnym Hudson kliknij **Zarządzanie Hudson**.
2. W **Zarządzanie Hudson** kliknij pozycję **Zarządzanie wtyczkami**.
3. Kliknij przycisk **dostępne** kartę.
4. Kliknij przycisk **wyszukiwania** i typ **Azure** do ograniczania listy do odpowiednich dodatków plug-in.
   
    Jeśli wybierzesz opcję przewiń listę dostępnych wtyczek, można znaleźć Azure podrzędnej wtyczki w obszarze **klastra zarządzania i dystrybucji kompilacji** sekcji **innym** kartę.
5. Zaznacz pole wyboru **wtyczki podrzędna Azure**.
6. Kliknij pozycję **Zainstaluj**.
7. Uruchom ponownie Hudson.

Teraz, gdy ta wtyczka jest zainstalowana, kolejne kroki będzie, aby skonfigurować wtyczkę do profilu subskrypcji platformy Azure i utworzyć szablon, który będzie używany podczas tworzenia maszyny Wirtualnej dla węzeł podrzędny.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurowanie wtyczki podrzędnej Azure przy użyciu profilu subskrypcji
Profil subskrypcji, również określone ustawienia, publikowania jest plik XML, który zawiera bezpiecznych poświadczeń i dodatkowe informacje, które będą potrzebne do pracy z platformą Azure w środowisku projektowania. Aby skonfigurować wtyczkę Azure podrzędnej, potrzebne są:

* Identyfikator subskrypcji
* Certyfikat zarządzania dla subskrypcji

Są one dostępne w Twojej [profilu subskrypcji]. Poniżej znajduje się przykład profilu subskrypcji.

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

Po utworzeniu profilu subskrypcji, wykonaj następujące kroki, aby skonfigurować wtyczkę Azure podrzędnej.

1. Na pulpicie nawigacyjnym Hudson kliknij **Zarządzanie Hudson**.
2. Kliknij przycisk **skonfigurować System**.
3. Przewiń w dół strony, aby znaleźć **chmury** sekcji.
4. Kliknij przycisk **Dodaj nowe chmury > Microsoft Azure**.
   
    ![Dodaj nowy chmury][add new cloud]
   
    Wyświetli pola, w których należy wprowadzić szczegóły subskrypcji.
   
    ![Konfigurowanie profilu][configure profile]
5. Skopiuj certyfikat zarządzania i identyfikator subskrypcji z Twojej subskrypcji i wklej je w odpowiednich polach.
   
    Podczas kopiowania certyfikatu zarządzania i identyfikator subskrypcji, **nie** zawierają oferty, które należy ująć wartości.
6. Polecenie **konfiguracji Sprawdź**.
7. Kiedy konfiguracja nie zostanie pomyślnie zweryfikowana, kliknij przycisk **zapisać**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Konfigurowanie szablonu maszyny wirtualnej dla podrzędnej Azure wtyczki
Szablon maszyny wirtualnej definiuje parametry, które wtyczka będzie używać do tworzenia węzeł podrzędny na platformie Azure. W poniższych krokach możemy utworzyć szablon dla maszyny Wirtualnej systemu Ubuntu.

1. Na pulpicie nawigacyjnym Hudson kliknij **Zarządzanie Hudson**.
2. Polecenie **skonfigurować System**.
3. Przewiń w dół strony, aby znaleźć **chmury** sekcji.
4. W ramach **chmury** sekcji, Znajdź **dodać szablon maszyny wirtualnej Azure** i kliknij przycisk **Dodaj** przycisku.
   
    ![Dodaj szablon maszyny wirtualnej][add vm template]
5. Podaj nazwę usługi chmury w **nazwa** pola. Jeśli przez Ciebie nazwą odwołuje się do istniejącej usługi w chmurze, maszyna wirtualna zostanie zainicjowana w tej usłudze. W przeciwnym razie Azure utworzy nowy.
6. W **opis** wprowadź tekst, który zawiera opis tworzenia szablonu. Te informacje są tylko do celów dokumentacji i nie jest używany w inicjowania obsługi maszyny Wirtualnej.
7. W **etykiety** wprowadź **linux**. Etykieta służy do identyfikowania tworzonego szablonu i są następnie używane do odwołania szablonu podczas tworzenia zadania Hudson.
8. Wybierz region, w którym zostanie utworzona maszyna wirtualna.
9. Wybierz odpowiedni rozmiar maszyny Wirtualnej.
10. Określ konto magazynu, w którym zostanie utworzona maszyna wirtualna. Upewnij się, że jest w tym samym regionie co usługa w chmurze, który ma być używany. Jeśli chcesz nowego magazynu należy utworzyć to pole może pozostać puste.
11. Czas przechowywania określa liczbę minut, zanim Hudson usuwa bezczynności podrzędna. Pozostaw to domyślna wartość 60.
12. W **użycia**, wybierz odpowiedni warunek, jeśli będzie używany ten węzeł podrzędny. Teraz, wybierz **korzystać z tego węzła możliwie**.
    
     W tym momencie formularza będzie wyglądać nieco podobny do poniższego:
    
     ![Konfiguracja szablonu][template config]
13. W **rodziny obrazu lub identyfikator** do określania, jakie obrazu systemu zostanie zainstalowana na maszynie Wirtualnej. Możesz wybrać z listy obrazów rodzin lub określ niestandardowy obraz.
    
     Jeśli chcesz wybrać z listy rodzin obrazu, wprowadź pierwszego znaku (z uwzględnieniem wielkości liter) nazwę rodziny obrazu. Na przykład wpisanie **U** pojawi się lista rodzin Ubuntu Server. Po wybraniu na liście Wpięć użyje najnowszej wersji tego obrazu systemu z tej rodziny podczas inicjowania obsługi administracyjnej maszyny Wirtualnej.
    
     ![Listy rodziny systemów operacyjnych][OS family list]
    
     Jeśli masz niestandardowego obrazu, który chcesz użyć, wprowadź nazwę niestandardowego obrazu. Nazwy niestandardowego obrazu nie są wyświetlane na liście, dlatego należy upewnić się, że jej nazwa jest wpisana poprawnie.    
    
     W tym samouczku, wpisz **U** przywołać listę obrazów Ubuntu i wybierz **Ubuntu Server 14.04 LTS**.
14. Dla **uruchamiania metody**, wybierz pozycję **SSH**.
15. Skopiuj poniższy skrypt i Wklej w **skryptu Init** pola.
    
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
    
     **Skryptu Init** zostaną wykonane po utworzeniu maszyny Wirtualnej. W tym przykładzie skrypt instaluje Java, usługi git i ant.
16. W **Username** i **hasło** pól, wprowadź wartości preferowanych dla konta administratora, który zostanie utworzony na maszynie Wirtualnej.
17. Polecenie **Sprawdź szablon** do sprawdzenia, jeśli zostanie określone parametry są prawidłowe.
18. Kliknij przycisk **Zapisz**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Utwórz zadanie Hudson uruchamianego na węzeł podrzędny na platformie Azure
W tej sekcji trzeba utworzyć zadania Hudson, które zostanie uruchomione w węźle podrzędna na platformie Azure.

1. Na pulpicie nawigacyjnym Hudson kliknij **nowe zadanie**.
2. Wprowadź nazwę dla tworzonego zadania.
3. Wybierz typ zadania **kompilacji zadanie oprogramowania wolne stylu**.
4. Kliknij przycisk **OK**.
5. Na stronie Konfiguracja zadania wybierz **Ogranicz, w którym można uruchomić tego projektu**.
6. Wybierz **węzła i etykiety menu** i wybierz **linux** (możemy określona etykieta w podczas tworzenia szablonu maszyny wirtualnej w poprzedniej sekcji).
7. W **kompilacji** kliknij **kroku kompilacji Dodaj** i wybierz **wykonywania powłoki**.
8. Edytuj poniższy skrypt, zastępując **{nazwa konta usługi github}**, **{nazwę projektu}**, i **{katalogu projektu}** z odpowiednie wartości, a następnie wklej edytowanych skrypt w obszarze tekst, który pojawia się.
   
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
10. Na pulpicie nawigacyjnym Hudson Znajdź właśnie utworzony zadanie i kliknij na **zaplanować kompilacji** ikony.

Hudson będą utworzyć szablon utworzony w poprzedniej sekcji węzeł podrzędny i uruchom skrypt określone w kroku kompilacji dla tego zadania.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o używaniu platformy Azure z językiem Java, zobacz [Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[profilu subskrypcji]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

