---
title: Tworzenie aplikacji kontenera systemu Windows w usłudze Service Fabric na platformie Azure | Microsoft Docs
description: W tym przewodniku Szybki start utworzysz pierwszą aplikację kontenera systemu Windows w usłudze Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 081b2be82b15c36566e8eb9fe4af0037804d0e7e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951199"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Szybki start: wdrażanie kontenerów systemu Windows do usługi Service Fabric

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych oraz niezawodnych mikrousług i kontenerów, a także zarządzanie nimi.

Uruchomienie istniejącej aplikacji w kontenerze systemu Windows w klastrze usługi Service Fabric nie wymaga dokonywania żadnych zmian w aplikacji. W tym przewodniku Szybki start przedstawiono sposób wdrażania wstępnie skompilowanego obrazu kontenera platformy Docker w aplikacji usługi Service Fabric. Po zakończeniu będziesz mieć uruchomiony kontener systemu Windows Server 2016 Nano Server i usług IIS. W tym przewodniku Szybki start opisano wdrażanie kontenera dla systemu Windows. Aby wdrożyć kontener dla systemu Linux, zapoznaj się z [tym przewodnikiem Szybki start](service-fabric-quickstart-containers-linux.md).

![Domyślna strona internetowa usług IIS][iis-default]

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Pakowanie kontenera obrazu platformy Docker
* Konfigurowanie komunikacji
* Kompilowanie i tworzenie pakietu aplikacji usługi Service Fabric
* Wdrażanie aplikacji kontenera na platformie Azure

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure (można utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Komputer dewelopera z następującym oprogramowaniem:
  * Program Visual Studio 2015 lub Visual Studio 2017.
  * [Zestaw SDK usługi Service Fabric oraz narzędzia](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Pakowanie kontenera obrazu platformy Docker przy użyciu programu Visual Studio

Zestaw SDK usługi Service Fabric oraz narzędzia udostępniają szablon usługi ułatwiający wdrażanie kontenera w klastrze usługi Service Fabric.

Uruchom program Visual Studio jako administrator.  Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.

Wybierz pozycję **Aplikacja usługi Service Fabric**, nadaj jej nazwę „MyFirstContainer” i kliknij przycisk **OK**.

Wybierz pozycję **Kontener** z szablonów **Hostowane kontenery i aplikacje**.

W polu **Nazwa obrazu** wprowadź wartość „microsoft/iis:nanoserver”, [podstawowy obraz serwera Nano Server systemu Windows Server i usług IIS](https://hub.docker.com/r/microsoft/iis/).

Skonfiguruj mapowanie portów kontenera typu „port do hosta” w taki sposób, aby żądania przychodzące do usługi na porcie 80 były mapowane na port 80 w kontenerze.  Ustaw wartość „80” dla pozycji **Port kontenera** i **Port hosta**.  

Nazwij usługę „MyContainerService”, a następnie kliknij przycisk **OK**.

![Okno dialogowe nowej usługi][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Określanie kompilacji systemu operacyjnego dla obrazu kontenera
Kontenery utworzone za pomocą określonej wersji systemu Windows Server mogą nie działać na hoście z uruchomioną inną wersją tego systemu. Na przykład kontenery utworzone przy użyciu systemu Windows Server w wersji 1709 nie działają na hostach z systemem Windows Server w wersji 2016. Aby dowiedzieć się więcej, zobacz temat [Windows Server container OS and host OS compatibility](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility) (Zgodność między systemami operacyjnymi Windows Server kontenera i hosta). 

W przypadku środowiska uruchomieniowego usługi Service Fabric w wersji 6.1 i nowszych można określić wiele obrazów systemu operacyjnego dla kontenera i oznaczyć każdy z nich wersją kompilacji systemu operacyjnego, na którym powinien zostać wdrożony. Ułatwia to zapewnienie, że aplikację będzie można uruchomić na hostach z różnymi wersjami systemu operacyjnego Windows. Aby dowiedzieć się więcej, zobacz temat [Określanie obrazów kontenera dla kompilacji systemu operacyjnego](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

Firma Microsoft publikuje różne obrazy dla wersji oprogramowania IIS opartych na różnych wersjach systemu Windows Serwer. Aby się upewnić, że usługa Service Fabric wdraża kontener zgodny z wersją systemu Windows Server uruchomionego na węzłach klastra, na których wdraża aplikację, dodaj następujące wiersze do pliku *ApplicationManifest.xml*. Wersja kompilacji dla systemu Windows Server 2016 to 14393, a dla systemu Windows Server w wersji 1709 — 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

Manifest usługi nadal określa tylko jeden obraz dla nanoserwera, `microsoft/iis:nanoserver`. 

## <a name="create-a-cluster"></a>Tworzenie klastra

Aby wdrożyć aplikację w klastrze na platformie Azure, możesz dołączyć do klastra testowego. Klastry testowe to bezpłatne, ograniczone czasowo klastry usługi Service Fabric hostowane na platformie Azure i uruchamiane przez zespół usługi Service Fabric, w których każdy może wdrażać aplikacje i poznawać platformę.  Klaster używa jednego certyfikatu z podpisem własnym w przypadku zabezpieczeń między węzłami, jak i zabezpieczeń między klientem i węzłem. Klastry testowe obsługują kontenery. Jeśli zdecydujesz się skonfigurować własny klaster i używać go, musi on działać w jednostce SKU obsługującej kontenery (np. Windows Server Datacenter 2016 z kontenerami).

Zaloguj się i [dołącz do klastra z systemem Windows](http://aka.ms/tryservicefabric). Pobierz certyfikat PFX na komputer, klikając link **PFX**. Kliknij link **Jak nawiązać połączenie z zabezpieczonym klastrem testowym?** i skopiuj hasło certyfikatu. Certyfikat, hasło certyfikatu oraz wartość pola **Punkt końcowy połączenia** będą używane w kolejnych krokach.

![Plik PFX i punkt końcowy połączenia](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Liczba klastrów testowych dostępnych przez godzinę jest ograniczona. Jeśli wystąpi błąd podczas próby tworzenia konta umożliwiającego korzystanie z klastra testowego, możesz poczekać, a następnie spróbować ponownie. Możesz też wykonać kroki opisane w samouczku [Wdrażanie aplikacji .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application), aby utworzyć klaster usługi Service Fabric w subskrypcji platformy Azure i wdrożyć w nim aplikację. Klaster utworzony za pomocą programu Visual Studio obsługuje kontenery. Po wdrożeniu i zweryfikowaniu aplikacji w klastrze możesz przejść od razu do sekcji [Kompletny przykład aplikacji i manifestów usługi Service Fabric](#complete-example-service-fabric-application-and-service-manifests) w tym przewodniku Szybki start.
>

Na komputerze z systemem Windows zainstaluj plik PFX w magazynie certyfikatów *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

Zapamiętaj odcisk palca na potrzeby następnego kroku.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Wdrażanie aplikacji na platformie Azure przy użyciu programu Visual Studio

Kiedy aplikacja jest gotowa, można wdrożyć ją w klastrze bezpośrednio z programu Visual Studio.

Kliknij prawym przyciskiem myszy pozycję **MyFirstContainer** w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**. Zostanie wyświetlone okno dialogowe Publikowanie.

Skopiuj **punkt końcowy połączenia** ze strony klastra testowego do pola **Punkt końcowy połączenia**. Na przykład `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Kliknij pozycję **Zaawansowane parametry połączenia**, a następnie zweryfikuj informacje dotyczące parametru połączenia.  Wartości *FindValue* i *ServerCertThumbprint* muszą być zgodne z odciskiem palca certyfikatu zainstalowanego w poprzednim kroku.

![Okno dialogowe Publikowanie](./media/service-fabric-quickstart-containers/publish-app.png)

Kliknij przycisk **Opublikuj**.

Każda aplikacja w klastrze musi mieć unikatową nazwę.  Klastry testowe są jednak publicznym, udostępnionym środowiskiem i może wystąpić konflikt z istniejącą aplikacją.  Jeśli występuje konflikt nazw, zmień nazwę projektu programu Visual Studio i wdróż ponownie.

Otwórz przeglądarkę i przejdź do **punktu końcowego połączenia** określonego na stronie klastra testowego. Adres URL można opcjonalnie poprzedzić identyfikatorem schematu `http://` i dołączyć port `:80`. Na przykład http://zwin7fh14scd.westus.cloudapp.azure.com:80. Powinna zostać wyświetlona domyślna strona internetowa usługi IIS: ![domyślna strona internetowa usług IIS][iis-default]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* Pakowanie kontenera obrazu platformy Docker
* Konfigurowanie komunikacji
* Kompilowanie i tworzenie pakietu aplikacji usługi Service Fabric
* Wdrażanie aplikacji kontenera na platformie Azure

Aby dowiedzieć się więcej na temat pracy z kontenerami systemu Windows w usłudze Service Fabric, przejdź do samouczka dotyczącego aplikacji kontenerów systemu Windows.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji kontenerów systemu Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
